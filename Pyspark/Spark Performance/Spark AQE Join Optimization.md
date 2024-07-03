Let's assume we have two large tables and we are joining these two tables using the following query.

```SQL
SELECT *
FROM large_tbl_1
JOIN large_tbl_2 ON large_tbl_1.key = large_tbl_2.key
WHERE large_tbl_2.value = 'some value'
```

```python
df1.join(df2, df1.key == df2.key, "inner")
	.filter("value=='some value'")
```

Both of the tables are large tables, so we are expecting a sort-merge join to take place. We ran your job and checked the execution plan.

![[Spark AQE Join Optimization 1.png]]

We are reading two tables, and we have two stages of reading those tables and it accounts to two stages. Both these stages send data to exchange. And everything after the exchange is part of the third stage.

The third stage collects the data from the exchange, performs the sorting operation on the data, and finally joins them. That's what happens in a sort-merge join operation.

Lets assume both of the tables are large enough, but we are also applying a filter condition on the large_tbl_2. The large table_2 is a 100GB table, but after filtering we selected only 7 MB of data from the large table_2.

large_table_1 is 100 GB, and we are selecting all rows from this table whereas large_tbl_2 is also 100 GB, but we are selecting only 7 MB from that table. With this information we will not use sort-merge join but want to use broadcast hash join here because one of the tables is small enough.

Spark will not apply Broadcast hash join if Broadcast Join Threshold is broken. But the value is 10MB, and that's the default value. We are selecting 7 MB from a large table, and that's well below the broadcast threshold. But Spark is not applying the broadcast join.
`spark.sql.autoBroadcastJoinThreshold = 1OMB`

The Spark execution plan is created before the spark starts the job execution. Spark doesn't know the size of the table, so it applied a sort-merge join. 

Spark will not apply broadcast if we do not have a column histogram for the filter column. It cannot apply broadcast join if the statistics are outdated. So one solution is to analyze the Spark tables and keep your table and column statistics up to date.

Another solution is to enable AQE. We already learned that the AQE computes statistics on the shuffle data. So AQE will compute the statistics on the shuffle data and use that information to do the following things.

We enabled AQE and executed the same query to check the new execution plan. We still have three stages. Stage one and Stage two are scanning the tables and sending data to exchange. The Adaptive Query will compute statistics on the exchange data. The statistics tell that the data size of large_tbl_2 is small enough to apply broadcast join. So the AQE will dynamically change the execution plan and apply broadcast hash join.

Unfortunately, we still have the shuffle, but we saved the sort operation. We couldn't save the shuffle operation, and you still see the exchange in the query plan. AQE cannot avoid shuffle because the AQE computes the statistics during the shuffle. The AQE will dynamically change the plan and apply broadcast hash join to save the expensive sort operation.

![[Spark AQE Join Optimization 2.png]]

The shuffle operation is already complete. We already distributed data from stage one and two to stage three exchange. But if we apply broadcast join now, are we going to broadcast the table once again?

So AQE also gives you another configuration, `spark.sql.adaptive.localShuffeReader.enabled = true`. The default value for this configuration is true.

![[Spark AQE Join Optimization 3.png]]

Both the plans are almost the same, but we see this Custom Shuffle Reader or local shuffle reader. This Custom shuffle reader is specifically designed to further optimize the AQE broadcast join by reducing the network traffic.

- Broadcast shuffle join is the most performant join strategy
- We can apply broadcast join if one side of the join can fit well in memory
- One table must me shorter than `spark.sql.autoBroadcastJoinThreshold`
- Estimating the table size is problematic in following scenarios
	- We applied a highly selective filter on the table
	- Join table is generated at runtime after a series of complex operations

- Spark AQE can help
	- AQE computes the table size at shuffle time
	- Replan the join strategy at runtime converting sort-merge join to a broadcast hash join