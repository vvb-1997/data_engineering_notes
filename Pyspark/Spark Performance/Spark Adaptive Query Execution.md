Spark Adaptive Query Execution or AQE is a new feature released in Apache Spark 3.0. It offers three capabilities.
- Dynamically coalescing shuffle partitions
- Dynamically switching join strategies
- Dynamically optimizing skew joins

Let's assume we are running a super simple group-by query in Spark SQL or Dataframe expression.

```SQL
SELECT tower_location,
	sum(call_duration) as duration_served
FROM call_records
GROUP BY tower_location;
```

```python
df.groupBy("tower_location")
	.agg(sum("call_duration").alias("duration_served"))
```

The table `call_records` stores information about the cell phone calls made by different users. Below is a simplified table structure. So we record `call_id`, then the duration of the call in minutes, and we also record which cell tower served the call.

![[Spark  AQE 1.png]]

Spark SQL is trying to get the sum of call duration by the `tower_location`. For these four sample records, we should get the result as shown here.

![[Spark  AQE 2.png]]

Spark will take the code, create an execution plan for the query, and execute it on the cluster. The spark job that triggers this query should have a two-stage plan.

Stage zero reads the data from the source table and fills it to the output exchange. The second stage will read the data from the output exchange and brings it to the input exchange. And this process is known as shuffle/sort.

We need shuffle/sort in our execution plan because we are doing a `groupBy` operation and `groupBy` is a wide-dependency transformation. The stage is dependent on stage zero, so stage one cannot start unless stage zero is complete.

Lets assume that the input data source has got only two partitions. So the stage zero exchange should have two partitions and we have data for five towers. So each partition in this exchange might have some data for tower a, tower b, tower c, and so on.

Stage zero will read data from the source table and make it available to the exchange for the second stage to read. We have only two partitions in my input source, so the exchange shows only two partitions.

Now the shuffle/sort operation will read these partitions, sort them by the `tower_location` and bring them to the input exchange of stage one. 

Let's assume I configured `spark.sql.shuffle.partitions = 10`. So the shuffle/sort will create ten partitions in the exchange. Even if we have only five unique values, the shuffle/sort will create ten partitions. Five of them will have data, and the remaining five will be blank partitions.

We need ten tasks because we have ten partitions in the exchange. Five partitions are empty, but Spark will still trigger ten tasks. The empty partition task will do nothing and finish in milliseconds. But Spark scheduler needs to spend time scheduling and monitoring these useless tasks. The overhead is small, but we do have some unnecessary overhead here. We improved the situation by reducing the shuffle partitions to 10 from the default value of 200.

![[Spark  AQE 3.png]]

We cannot keep changing the shuffle partitions for every query. Even if we want to do that, we do not know how many unique values the SQL will fetch. The number of unique keys is dynamic and depends on the dataset.

Some partitions are big, and others are small, they are not proportionate. So a task working on partition one will take a long time while the task doing the partition-2 will finish very quickly. The stage is not complete until all the tasks of the stage are complete. Three tasks processing partitions 2,3 and 4 will finish quickly, but we still need to wait for partition-1 and partition -4. And that's a wastage of CPU resources. The situation becomes worse when one partition becomes excessively long.

![[Spark  AQE 4.png]]

In shuffle partitions problem, the query resulted in five disproportionate shuffle partitions. We made an intelligent guess and decided to reduce my shuffle partitions to ten. However, the situation is still not very good. 

Spark 3.0 offers Adaptive Query Execution to solve this problem. We must enable it, and the AQE will take care of setting the number of your shuffle partitions.

The input data is already loaded in stage zero exchange. Stage zero is already done, and data has come to the exchange. Now Spark will start the shuffle/sort. So it can compute the statistics on this data and find out some details such as the following.

![[Spark  AQE 5.png]]

And this is called dynamically computing the statistics on your data during the shuffle/sort. Such dynamic statistics are accurate and most up-to-date. When Spark knows enough information about the data, it will dynamically adjust the number of shuffle partitions for the next stage.

For example, in our case, Spark might dynamically set the shuffle partitions to four. And the result of that setting looks like this. We have four shuffle partitions for stage one. Those five empty partitions are gone. Spark also merged two small partitions to create one larger partition. So instead of having five disproportionate partitions, we have four partitions. And these four are a little more proportionate. Due to this we only need four task to run for those partitions.

Task 3 working on partition-3 will finish quickly, but the other three tasks will take almost equal time. So we saved one CPU slot, and we also eliminated the useless empty tasks.

![[Spark  AQE 6.png]]

- Shuffle operation is critical for Spark performance
- We can tune it using `spark.sql.shuffle.partitions`
- Tuning `spark.sql.shuffle.partitions` is complex for following reasons
	- Small number of partitions cause
		- Large partition size
		- Task needs large amount of memory
		- May cause OOM exception
	- Large number of partitions cause
		- Small/tiny partition size
		- Many network fetch causing inefficient network 1/0
		- More burden for Spark Task Scheduler

- To solve this problem, we can set a relatively large number of shuffle partitions at the beginning and enable Adaptive query execution. The AQE feature of Apache Spark will compute shuffle file statistics at runtime and perform two things.
	- Determine and set the best shuffle partition number
	- Combine or coalesce the small partitions

We can enable AQE using the following configuration.
- `spark.sql.adaptive.enabled`
	- The default value of this configuration is false.
- `spark.sql.adaptive.coalescePartitions.initialPartitionNum`
	- The first configuration sets the initial number of shuffle partitions.
	- Dynamically determining the best number and setting that value is applied later, AQE starts with this value.
	- This configuration works as the max number of shuffle partitions.
	- The Spark AQE cannot set a number larger than this. This configuration doesn't have a default value. So if you do not set this configuration, Spark will set it equal to `spark.sql.shuffle.partitions`.
- `spark.sql.adaptive.coalescePartitions.minPartitionNum`
	- This configuration defines the minimum number of shuffle partitions after coalescing or combining multiple partitions.
	- We do not have a default value for this configuration also. So if you do not set this configuration, Spark will set it equals to `spark.default.parallelism`.
- `spark.sql.adaptive.advisoryPartitionSizelnBytes`
	- The default value for this configuration is 64 MB, and it works as an advisory size of the shuffle partition during adaptive optimization.
	- So this configuration takes effect when Spark coalesces small shuffle partitions or splits skewed shuffle partition.
	- The AQE will use this number for determining the size of the partitions and combine them accordingly.
- `spark.sql.adaptive.coalescepartitions.enabled`
	- If we set this value to false, Spark AQE will not combine or coalesce smaller partitions.

The first one is a kind of master configuration to enable or disable the AQE feature. If we disable it, the other four configurations do not take any effect. But if we enabled AQE, we can tune it further using the other four configurations.


