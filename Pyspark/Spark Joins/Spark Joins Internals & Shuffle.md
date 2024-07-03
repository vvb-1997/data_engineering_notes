Spark joins are one of the most common causes for slowing down your application. Spark implements two approaches to join your data frames.
- Shuffle Join
- Broadcast Join
# Shuffle Join

The shuffle join is the most commonly used join type. And the internal implementation goes back to the notion of the Hadoop MapReduce implementation.

Let's assume we have two data frames. Both of these are partitioned, and considering three partitions for each. Now we want to join them using a key. All these partitions are distributed across different executors in the cluster. They may or may not reside on the same system.

![[Spark Join Internals 1.png]]

Let me assume that we have three executors, and these partitions are equally assigned to these three executors. So, each executor has got 2 partitions, one from each data frame. Each data frame is made up of three partitions, which are evenly distributed among the three executors.

![[Spark Join Internals 2.png]]

So each executor owns two partitions - one from each data frame. Because matching keys of two records are on two different executors. We cannot join them unless we have both the records on the same executor.

![[Spark Join Internals 3.png]]

The join is performed in two stages. In the first stage, each executor will map these records using the join key and send it to the exchange. This is the `map()` phase of the MapReduce implementation, so let's call these exchanges as the map exchange

In this first stage, all records are identified by the key, and they are made available in the map exchange to be picked up by the Spark framework. The map exchange is like a record buffer at the executor.

Now the Spark framework will pick these records and send them to the reduce-exchange. A reduce-exchange is going to collect all the records for the same key. All the records for the key-value 01 will go to the same exchange. These exchanges are known as shuffle partitions.

![[Spark Join Internals 4.png]]

And, it is up to us to decide the number of shuffle partitions? And the best case scenario for us is to have three shuffle partitions where each partition is handled by one executor. So, all three partitions can be processed in parallel.

Now let's assume we have 30 unique join keys. So, in this case, each reduce-exchange should get 10 keys. Key-value 01 to 10 should go to first exchange. Similarly, key-value 11 to 20 will go to second exchange, and so on.

![[Spark Join Internals 5.png]]

And all this transfer of data from a map exchange to the reduce-exchange is what we call a shuffle operation. This shuffle operation can choke your cluster network and slow down your join performance. The shuffle is the main reason why spark joins could be slow and non-performant. Tuning your join operation is all about optimizing the shuffle operation. 

![[Spark Join Internals 6.png]]

When shuffle is complete, and we got your records at the reduce-exchange, then each exchange is self-sufficient to join the records. Because all the records for key-value 01 from both data frames are now present at this exchange. Now it is all about combining those records and create a new data frame partition. And there comes the second stage of applying a sort-merge join algorithm and finish the job.

![[Spark Join Internals 7.png]]

We have data sets d1 and d2 and it has three data files in both of these data sets in order to make sure we get three partitions when we read them. Create a spark session with 3 three parallel threads to do the work. Also we are reading these two datasets in two different data frames.

```python
spark = SparkSession \
	.builder \
	.appName("Shuffle Join Demo") \
	.master("local[3]") \
	.getOrCreate()

logger = Log4j(spark)

flight_time_df1 = spark.read.json("data/d1/")
flight_time_df2 = spark.read.json("data/d2/")
```

Lets also set the shuffle partition configuration. This config will ensure that we get three partitions after the shuffle, which means having three reduce exchanges. 

```python
spark.conf.set("spark.sql.shuffle.partitions", 3)
```

Now let's define the join condition which will be inner and then perform the join operation.  But join is a transformation. So, nothing is going to actually happen until we take action. So, let me add a collect action.

```python
join_expr = flight_time_df1.id == flight_time_df2.id
join_df = flight_time_df1.join(flight_time_df2, join_expr, "inner")

join_df.collect()
```

We have three jobs. The first job is to read the first data frame. Reading a data frame is a single-stage and straightforward operation. So, we have a single stage for this job. We had three files, or you can say three partitions for the data frame, so the read happened in three parallel tasks. The same happened for the second data frame.

![[Spark Join Internals 8.png]]

The second job is join operation. The join operation is accomplished in three stages. There are two stages to create a map exchange for the two data frames. Data moves from the map exchange to the reduce-exchange. Rest all is a simple sort-merge join. 

![[Spark Join Internals 9.png]]

So the first two-stage were doing shuffle write, and all this happened in three parallel tasks because both the data frames had three partitions. The final stage was doing a shuffle read. That thing also happened in three parallel tasks because we configured the shuffle partitions to 3.

![[Spark Join Internals 10.png]]

# Optimizing Shuffle Join

Joining two DataFrames can bring the following two scenarios.

- Joining a large data frame with another large data frame.
	- When we say large, we mean large enough to not to fit into the memory of a single executor.  
- Join a large Dataframe with a small or tiny data frame.
	- We consider it small when the Dataframe can fit into the memory of a single executor.

However, if your Dataframe is more than a few GBs, it should be considered large and must be broken down into multiple partitions to achieve parallel processing.

When both of your DataFrames are large, we should watch out for the following things in the join operation.
## Don't code like a novice

We already learned that shuffle join will send all of the data from map exchange to the reduce-exchange. So, filtering out unnecessary data before you perform a join will obviously cut down the amount of data sent for shuffle operation. Sometimes such filtering is obvious, but more often, these things are not apparent unless we know enough about your datasets.

Let's consider the following datasets. The first one is the global online sales, and the second one is only for us warehouse event dataset. We want to perform a left join by `order_id`, and it looks obvious to join them without any prior filtering. However, we know that orders from European cities are not going to match US cities' orders, and they will be filtered out in the Join operation.

![[Spark Join 1.png]]

It makes a perfect sense to filter for the US orders from the `global_sales` even before we perform a join operation. Join the us warehouse event with the global sales and filter for the United States only. The point is straight. Look for all the possible opportunities to reduce the Dataframe size. Smaller the Dataframe will result in smaller the shuffle and faster join. 

![[Spark Join 2.png]]
## Shuffle partitions and Parallelism

Number of shuffle partitions and the number of executors are going to determine the degree of parallelism.

The maximum possible parallelism for the join operation depends on three factors. 
- Number of executors.
	- If we can run your job with 500 executors, then that's your maximum limit.
- Number of shuffle partitions.
	- If we have 500 executors, but we configured to have 400 shuffle partitions, then our maximum is limited to 400. Because we have only 400 partitions to process in parallel.
- Number of unique join keys.
	- If we have only 200 unique keys, then you can have only 200 shuffle partitions. Even if we define 400 shuffle partitions, only 200 of those will have data, and others will remain blank.

Consider the following DataFrames. We are joining these two DataFrames on `product_id` and we have 200 unique products. Hence we can have a maximum of 200 reduce exchanges where each unique key goes to its dedicated exchange as  one reduce exchange should get records for only one unique key and each exchange is equal to one shuffle partition.

![[Spark Join 3.png]]

We can take full advantage of 200 partitions by running join operation on a 200 node Spark cluster. So, each node is going to process only one partition, and everything gets processed in parallel. If we have a 100 node cluster, then each node might have to process two partitions taking double the time in the ideal case.

Hence if we have 200 unique keys, then we can have a maximum of 200 parallel tasks even if we run the job on a 500 node cluster, we will be running only 200 parallel tasks. If we want to scale Join operation and take advantage of a larger cluster, we should increase the number of shuffle partitions.

If the number of the unique key is limiting scalability, try increasing the join key's cardinality. Changing join key to increase the cardinality or applying some trick to increase the cardinality of your current join key may not always be possible. However, we should be looking for opportunities to improve the parallelism if you have a large cluster.
## Key Distribution

The distribution of data across the keys. For example, we are joining the sales and product Dataframe and consider that we have 200 products. So, all sales transactions are joined with 200 products resulting in 200 shuffle partitions.

However, we may have some fast-moving products, some are slow-moving items, and others may be very slow-moving products. So, a fast-moving product should have a lot of transactions, and all of those will land into one partition because they all belong to the same product key. If we compare the size of your fastest-moving product partition vs. your slowest moving product partition, the difference could be significant.

![[Spark Join 4.png]]

The Spark task, which is sorting and joining the larger partition, may take a lot of time, whereas the smaller partition will get joined very quickly. However, join operation is not complete until all the partitions are joined.

Therefore watch out for the time taken by individual tasks and the amount of data processed by the join task. If some tasks are taking significantly longer than other tasks, we have an opportunity to fix join key or apply some hack to break the larger partition into more than one partitions.   

Shuffle joins could become severely problematic for the following principal reasons.
- Huge Volumes — Filter/Aggregate
- Parallelism — Shuffles/Executors/Keys
- Shuffle Distribution — Key Skews
# Broadcast Join

A large Dataframe to a large Dataframe join is always going to go for a shuffle join. We do not have any other option. However, a large Dataframe join with a small dataframe can take advantage of the broadcast join, which often works faster than the shuffle join.

Sales dataframe is huge having millions of records in this dataframe. These records are partitioned, and we have 100 partitions. These partitions are distributed across 100 executors. 

![[Spark Join 5.png]]

Product dataframe is just 200 records, and it is worth 2 MBs. We have a single partition, and it is stored at one executor.

![[Spark Join 6.png]]

Lets join these two DataFrames using a shuffle join. So, we can expect 200 unique keys for this join. We have enough executors, so shuffle is going to create 200 shuffle partitions. And all the data from these 100 executors will be shuffled to these 200 shuffle partitions.

![[Spark Join 7.png]]

We are sending all those millions of records over the network for a shuffle operation. Instead of sending this data from these 100 executors, we can dispatch the product table to all those executors. This dataframe is small, just 2 MB. Right?

So, even if we send it to all 100 executors, we send 2x100=200 MB of data over the network, which is a lot smaller than the sales table. And these executors can smoothly perform a partitioned join because they have a list of all the products.

![[Spark Join 8.png]]

We are broadcasting the smaller table to all the executors and hence is called as broadcast join. The broadcast join is an easy method to eliminate the need for a shuffle. However, this approach works only when one of your dataframe is small, it doesn't mean a few MBs it could be 1GB or 2GB.

Make sure that the driver and executors have enough memory to accommodate your dataframe. In most of the cases, Spark will automatically use the broadcast join when one of your dataframe is smaller, and it can be broadcasted.

However, we know the DataFrames a lot better than Spark and we can apply a hint for using the broadcast join. We can easily apply the broadcast join for the example because these DataFrames would easily fit in the driver and executor memory.

```python
from pyspark.sql.functions import broadcast

spark = SparkSession \
	.builder \
	.appName("Shuffle Join Demo") \
	.master("local[3]") \
	.getOrCreate()

logger = Log4j(spark)

flight_time_df1 = spark.read.json("data/d1/")
flight_time_df2 = spark.read.json("data/d2/")

spark.conf.set("spark.sql.shuffle.partitions", 3)

join_expr = flight_time_df1.id == flight_time_df2.id
join_df = flight_time_df1.join(broadcast(flight_time_df2), join_expr, "inner")

join_df.collect()
```

So, we will apply the broadcast function to the right side dataframe. We are giving a hint to spark that this dataframe is small enough, and it should consider broadcasting this table.

![[Spark Join 9.png]]