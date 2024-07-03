Dynamic Partition Pruning is a new feature available in Spark 3.0 and above and this feature is enabled by default. However, if we want to disable it, you can use the following configuration.
`spark.sql.optimizer.dynamicPartitionPruning.enabled` The default value for this configuration is true in Spark 3.0.

Let's assume we have two tables. The first table is an orders table, and it stores all your orders. This table is huge because it stores thousands of orders every day and millions of orders a year. So we decided to partition it on `order_date`.

![[Spark Dynamic Partition Pruning 1.png]]

We partition orders table on the order date as we are querying this table on the order date.
We are reading the orders data set. Then applying a filter on `order_date` and finally computing total sales for 3rd Feb 2021.

```Python
order_df = spark.read.parquet("orders")
summary_df = order_df
	.where("order_date=='2021-02-03'") \
	.selectExpr("sum(unit_price * qty) as total_sales")
```

The physical plan for this query clearly shows that Spark performed six steps to complete this query. The first step is to scan or read the parquet file. But Spark is doing a smart thing here. Spark applied Partition Filters on the `order_date` column, and it is reading only one partition. The plan also shows a number of files read one.

![[Spark Dynamic Partition Pruning 2.png]]

In a typical case, Spark should read all the partitions of the parquet data set and then apply the filter. But data set was partitioned on the `order_date` column. So Spark decided to read only one partition that belongs to 3rd Feb 2021.

Spark query optimization uses below two features
- Predicate Pushdown
	- The predicate pushdown means Spark will push down the where clause filters down in the steps and apply them as early as possible.
	- Spark will not try a typical sequence to read the data first, then filter, and finally, calculate the sums.
	- Spark will push the filter condition down to the scan step and apply the where clause when reading the data itself.
- Partition Pruning
	- Predicate pushdown doesn't help much unless your data is partitioned on the filter columns.
	- It can simply leave all other partitions, and that feature is known as Partition Pruning.

These two features will optimize the query and reduce the read volume of your data. If we are reading less amount of data, queries run faster.
# Dynamic Partition Pruning

Let's assume we have dates table. This table is known as the date dimension in the data warehousing world. Orders table is a fact table, and the dates table is a dimension table. This kind of table structure is common in data warehouses.

![[Spark Dynamic Partition Pruning 3.png]]

Now let's assume we are running a query to calculate the sum of sales for February 2021.

```SQL
SELECT year, month, sum(unit_price * qty) as total_sales
FROM orders JOIN dates ON order_date == full date
GROUP BY year, month
WHERE year='2021' AND month='02'
```

```python
join_expr = order_df.order_date == date_df.full_date
order_df
	.join(date_df, join_expr, "inner") \
	.filter("year==2021 and month==2") \
	.groupBy("year", "month") \
	.agg(f.sum(f.expr("unit_price * qty"))
	.alias("total_sales"))
```

We execute this code on an older version of Spark where dynamic partition pruning is not available or, we can disable the dynamic partition pruning feature and try this SQL.

This is a typical sort/merge join execution plan. We are reading the parquet files and also reading the other data set. Both are going for a shuffle operation, and hence we can see these two exchanges. Finally, Spark will sort, then merge and finally aggregate it.

The parquet files are partitioned on the order date column, but that partitioning does not benefit us. If we see the scan parquet step in the diagram, we can see the number of files read four. The physical plan shows the partition filter step, but it is not pruning any partitions because we do not have any filter condition on the order date.

We want to read all the partitions for February 2021, but Spark is reading other month partitions also. The best case is to read only February 2021 partitions and leave all other partitions. However, Spark is not applying partition pruning here. Because the filter conditions are on the dates table. They are not on the orders tables.

![[Spark Dynamic Partition Pruning 4.png]]

To improve it we must do two things.
- Enable Dynamic Partition Pruning feature.
- Apply broadcast on the dimension table

The Dynamic partition pruning is enabled by default in Spark 3 and above. But we must apply a broadcast to the dimension table. So we modified my code, and it now looks like this.

```python
join_expr = order_df.order_date == date_df.full_date
order_df
	.join(f.broadcast(date_df), join_expr, "inner") \
	.filter("year==2021 and month==2") \
	.groupBy("year", "month") \
	.agg(f.sum(f.expr("unit_price * qty"))
	.alias("total_sales"))
```

Now we have applied `broadcast()` to the `date_df`. We ran this code once again, and here is the new execution plan.

The `date_df` is going for a broadcast exchange. But then, Spark creates a subquery from the broadcasted `dates_df` and sending it as an input to the parquet scan. We are now reading only two files. Spark applied partition pruning, and now it is reading only February 2021 partitions.

![[Spark Dynamic Partition Pruning 5.png]]

If we look at the physical plan and check out the scan parquet details, we will see a dynamic pruning expression. Spark Dynamic partition pruning can take a filter condition from the dimension table and inject it into the fact table as a subquery. Once a subquery is injected into your fact table, Spark can apply partition pruning on the fact table.

![[Spark Dynamic Partition Pruning 6.png]]

Using this feature is not straightforward as we must understand the following things.
- We must have a fact and dimension-like setup i.e. one large table and another small table. 
- The large table or the fact table must be partitioned so Spark can try partition pruning. 
- We must broadcast the smaller table or the dimension table. If it is smaller than 10 MB, then Spark should automatically broadcast it. However, we should make sure that your dimension table is broadcasted.

If we meet all three conditions, Spark will most likely apply dynamic partition pruning to optimize the queries.