Let's assume you have two larger tables and we are joining these two tables using the following query.

```SQL
SELECT *
FROM large_tbl_1
	JOIN large_tbl_2
	ON large_tbl_1.key = large_tbl_2.key
```

```python
df1.join(df2, df1.key == df2.key, "inner")
	.filter("value=='some value'")
```

Both of the tables are large tables, so we are expecting a sort-merge join to take place. We ran the job and checked the execution plan.

We are reading table one and table two. These two tables should join, so we have a shuffle operation for both tables. And that's why we see these two exchanges here. The first exchange partitions the data by the join key for the first table. And the second exchange partitions the data by the join key for the second table.

![[Spark AQE Data Skew 1.png]]

Let assume that the first table had two partitions. Each color represents data for one unique join key. So we read these two initial partitions, and then we shuffled them. The primary purpose of the shuffle is to repartition the data by the key. The second table also goes through the same process.

So partitions for the first table are coming from the left side, and the second table comes from the right side. Spark appropriately partitioned the data by the key. Now, all we need to do is sort each partition by the key and merge the records from both sides. We have four partitions to join so we will need four tasks.

![[Spark AQE Data Skew 2.png]]

Each task will pickup one color, sort the data, and merge it to complete the join operation. The green color partition on the left side is quite large as it is skewed. We need more memory to sort and merge the green partition. We planned 4 GB RAM for each task, and that should be sufficient for the other tasks. But the task working with the green color cannot manage the sort/merge operation with 4 GB RAM.

We could increase spark memory, but that's not a good solution. We have two reasons.
- Memory wastage.
	- We can increase the memory, assuming that I will have a skewed partition but we are not sure if all joins will result in a skewed partition.
	- We might have 10 or 15 join operations in the Spark application. All other join operations work perfectly fine with 4 GB task memory.
	- However, we have one skewed join, and one task needs extra memory. Increasing memory for one specific join or task is not possible. So we will end up increasing memory for the entire application, and that's wastage.
- Not a permanent solution.
	- Data keeps changing. We might have a skewed partition today, which required 6 GB to complete the sort/merge operation.
	- But we do not know what happens a week later. We got new data, and the skew is now more significant and now need 8 GB to pass through that skew.
	- We cannot let application fail every week or month, investigate the logs, and identify that we need more memory because data is now more skewed.

Spark AQE offers an excellent solution for this problem. We can enable skew optimization using the following configurations.
- `spark.sql.adaptive.enabled = true`
	- Enables the Spark AQE feature.
- `spark.sql.adaptive.skewJoin.enabled = true`
	- Enables the skew-join optimization.

Once we enable AQE and skew-join optimization, for the four shuffle partitions that we have, we will need four tasks to perform this sort/merge join operation. However, the green partition is skewed. So the green task will struggle to finish and take longer to complete. In the worst case, it might fail due to a memory crunch. But the other three tasks will complete quickly and normally because they are small enough.

However, we enabled AQE and skew-join optimization. So Spark AQE will detect this skewed partition and it will split the skewed partition on the left side into two or more partitions.

In our example, splitting into two is sufficient so let's assume Spark splitting it into two partitions. Spark will also duplicate the matching right-side partition. Now we have five partitions instead of four partitions. So we will need five tasks. The data partitions for all five tasks are almost the same, so they will consume uniform resources and finish simultaneously.

![[Spark AQE Data Skew 3.png]]

We also have two more configurations to customize the Skew Join optimization. These two configurations are used to define the skew. Spark AQE assumes that the partition is skewed and starts splitting when both thresholds are broken.
- `spark.sql.adaptive.skewJoin.skewedPartitionFactor`
	- The default value of `skewedPartitionFactor` is five. So a partition is considered skewed if its size is larger than five times the median partition size.
- `spark.sql.adaptive.skewJoin.skewedPartitionThresholdlnBytes`
	- Default value of `skewedPartitionThresholdInBytes` is 256MB. So a partition is considered skewed if its size in bytes is larger than this threshold.

