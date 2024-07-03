Shuffle join is almost unavoidable in case of a large to large dataset joins. However, in some scenarios, we can prepare yourself in advance and avoid the shuffle at the time of joining by bucketing the dataset and avoiding shuffle altogether at the join time.

We want to do the join on large datasets without the shuffle. We are assuming both of these datasets are large, and none could fit into the memory of a single executor. So, we cannot apply for the broadcast join.

We can plan the join in advance and layout the dataset so that the join can be done quickly. The goal is to avoid shuffle and that's where Spark bucketing could be handy.

So, if we have two datasets that we already know we are going to join in the future, then it is advisable to bucket both of the datasets using join key. Bucketing the dataset may also require a shuffle. However, that shuffle is needed only once when we create the bucket.

Once we have a bucket, we can join these datasets without a shuffle, and you can do it as many times as you need. So, bucketing is helpful to prepone the shuffling activity and do it only once and avoid the shuffle at the time of joining.

The first step is to bucket your datasets. We want to bucket the dataset and prepare it for the future joins. Start with the first dataframe and coalesce it to a single partition and then write it to get the `DataFrameWriter`. Then we use the `bucketBy()`. The first argument is the number of buckets. We are going to create three buckets because we will run it using three threads, and we want three partitions or buckets to achieve the maximum possible parallelism on three-node cluster.

Finally, we save it as a table also make sure that the database already exists. Lets add the save mode to overwrite. Now we have two tables that are created using buckets on your join key. These are now ready for joining and performing some analysis.

```python
spark = SparkSession \
	.builder \
	.appName("Bucket Join Demo") \
	.master("local[3]") \
	.enableHiveSupport() \
	.getOrCreate()

df1 = spark.read.json("data/d1/")
df2 = spark.read.json("data/d2/")

spark.sql("CREATE DATABASE IF NOT EXISTS MY_DB")
spark.sql("USE MY_DB")

df1.coalesce(1).write \
	.bucketBy(3, "id") \
	.mode("overwrite") \
	.saveAsTable("MY_DB.flight_data1")

df2.coalesce(1).write \
	.bucketBy(3, "id") \
	.mode("overwrite") \
	.saveAsTable("MY_DB.flight_data2")
```

Now we have a requirement for joining these two datasets. So, we are going to read both the datasets. However, these are database tables and so we will be using the `table()` method. Then we define the join expression. And finally, we can join them.

However, we are reading a managed table and these tables are small. So, Spark might automatically pick up a broadcast join. To avoid Spark to apply a broadcast join we can set the `spark.sql.autoBroadcastJoinThreshold` to -1. This setting will disable the broadcast join.

```python
df3 = spark.read.table("MY_DB.flight_data1")
df4 = spark.read.table("MY_DB.flight_data2")

spark.conf.set("spark.sql.autoBroadcastJoinThreshold", -1)
join_expr = df3.id == df4.id
join_df = df3.join(df4, join_expr, "inner")

join_df.collect()
```

A plain sort-merge join without a shuffle. However, we need to plan it ahead. Creating buckets is a design-time decision. If we understand the data and what and how we are going to use it, we can make smart design decisions and run your workload a lot faster.

![[Spark Join Bucket 1.png]]