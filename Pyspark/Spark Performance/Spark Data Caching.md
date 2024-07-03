Spark memory pool is broken down into two sub-pools.
- Storage memory
	- Spark use the storage pool for caching data frames.
- Executor memory
	- Spark will use the Executor pool to perform dataframe computations.

We cache a DataFrame in the executor storage pool using two methods.
- `cache()` method
- `persist()` method

Both of these methods will cache the dataframe in the executor storage pool. At a high level, they are the same because they do the same thing - cache the dataframe. The `cache()` method does not take any argument and it will cache the dataframe using the default MEMORY_AND_DISK storage level. However, the `persist()` method takes an optional argument for the storage level and it allows to configure the storage level.

So the signature for the persist method is like this.
```python
persist([StorageLevel(useDisk,
					useMemory,
					useOffHeap,
					deserialized,
					replication=1)])

persist([StorageLevel.storageLevel])
```
# Storage level

Here is a simple Spark DataFrame code. We are creating a dataframe of one million rows and repartitioning it to 10 uniform partitions.

```python
df = spark.range(1, 1000000).toDF("id") \
	.repartition(10) \
	.withColumn("square", expr("id * id")) \
	.cache()

df.take(10)
```

We are calling the `cache()` method to cache the dataframe in Spark storage memory. Finally, we are executing the `take(10)` action. The `cache()` method is a lazy transformation. So Spark will not do anything until we execute an action. That is why we are executing the `take(10)` action. The `take(10)` action will load one partition to Spark memory and give me ten records.

We have one partition in the cache. The row shows the details of the cached partition. We created a dataframe of 10 partitions. Then we cached it. But the spark is showing only one partition in the cache. The `take(10)` action will bring only one partition in the memory and return one record from the loaded partition.

Since we brought only one partition to memory, the spark will cache only one partition. Spark would cache the whole partition. We are taking only ten records from the loaded partition, but Spark will cache the entire partition because Spark will always cache the entire partition.

If we have a memory to cache 7.5 partitions, Spark will cache only seven partitions because the 8th partition does not fit in the memory. Spark will never cache a portion of the partition. It will either cache the entire partition or nothing but never cache a portion of the partition.

![[Spark Data Caching 1.png]]

The default storage level for the `cache()` method is Memory Deserialized 1 X replicated. We can also see the size of the cached partition here (~1.565 MB)

Now let me change the code by replacing `take(10)` method and with the `count()` action. The `count()` action will bring all the partitions in the memory, compute the count and return it. 

```python
df = spark.range(1, 1000000).toDF("id") \
	.repartition(10) \
	.withColumn("square", expr("id * id")) \
	.cache()

df.count()
```

Now we see all the ten partitions are cached because `count()` action forced all the partitions to be loaded in the executor memory. We have enough memory to cache all ten partitions, so Spark is caching all the ten partitions. The storage level of all the cached blocks is still the same as Memory Deserialized 1 X replicated.

![[Spark Data Caching 2.png]]

Spark `cache()` as well as `persist()` methods are lazy transformations. So they do not cache anything until an action is executed. The `cache()` and `persist()` methods are smart enough to cache only what you access. So if we are accessing only 3 or 5 partitions, Spark will cache only those partitions. Spark will never cache a portion of a partition. It will always cache the whole partition if and only if the partition fits in the storage.

Now let us change the code and use `persist()` method.

```python
df = spark.range(1, 1000000).toDF("id") \
	.repartition(10) \
	.withColumn("square", expr("id * id")) \
	.persist(StorageLevel(False,
						True,
						False,
						True,
						1))

df.count()
```

The `cache()` method and the `persist()` method work in the same way. The `persist()` method allows us to customize the storage level, but the rest is the same as the `cache()` method. In this example, we configured the storage level to the same as `cache()` storage level. So we don't see any difference.

But now, lets change the storage level and try to understand the storage characteristics. Here is the format for the `StorageLevel` method. In the current example, we are setting the following values.

![[Spark Data Caching 3.png]]

When `useMemory` flag is True we are saying use memory to cache the Spark DataFrame partitions. We can set `useDisk` flag to True and now we are extending the cache to the disk. If the partition does not fit into the Spark memory, Spark will cache it in the local disk of the executor.

We can also extend it to off-heap memory for caching dataframe partitions by setting `useOffHeap` flag to True. If we haven't added off-heap memory for your Spark application, setting this parameter should not have any effect.

Spark always stores data on a disk in a serialized format. But when data comes to Spark memory, it must be deserialized into Java objects. That's mandatory for Spark to work correctly. But when we cache data in memory, we can cache it in serialized format or deserialized format.

The deserialized format takes a little extra space, and the serialized format is compact. So we can save some memory using the serialized format. But when Spark wants to use that data, it must deserialize it. So we will spend some extra CPU overhead.

We can cache the data in a serialized format and save some memory but incur CPU costs when Spark access the cached data. Alternatively, we can cache it deserialized and save your CPU overhead. The recommendation is to keep it deserialized and save CPU. And this configuration only applies to memory. Disk-based cache is always serialized.

Lets change the code and set some different storage levels, and try it again.

```python
df = spark.range(1, 1000000).toDF("id") \
	.repartition(10) \
	.withColumn("square", expr("id * id")) \
	.persist(StorageLevel(True,
						True,
						False,
						False,
						1))

df.count()
```

This time, we are setting the storage level to cache data in memory, if we do not have enough memory, then cache it in the disk. We don't want to cache anything in off-heap memory because we haven't added off-heap memory for the application. We are also willing to try to cache data in serialized format.

The last one is the replication factor. We are setting it to one because we do not want to cache multiple copies of data. If we set this value to 3, Spark will cache three copies of dataframe partitions. All those three copies will be cached on three different executors, but caching multiple copies is wastage of memory.

![[Spark Data Caching 4.png]]

When we stored it as deserialized, it took more memory space, but the serialized storage takes a little lesser.

The persist method allows to customize cache storage level and cache it. 
- We can cache the data in memory only, or we can cache it in the disk only. 
- We can also configure it to store both in memory and disk. 
- If we have some off-heap memory, we can also expand cache memory to off-heap storage.

So assume we enabled all three storage levels, then Spark will cache your data in memory that's the first preference. If we need more storage, Spark will use off-heap memory. We still want to cache more data, Spark will use the local disk.

We can keep cache in serialized or in deserialized formats, but this configuration is only applicable for memory. Disk and off-heap do not support deserialized formats. They always store data in serialized formats.

The last option is to increase the replication factor of your cached blocks. Increasing replication can give better data locality to Spark task scheduler and make your application run faster. However, it will cost a lot of memory.

We used `StorageLevel()` function to demo the persist method. But we can also use some predefined constants. In this example, we are using a predefined storage level constant. The constant makes the code more readable, but we can also use the `StorageLevel`() function.

```python
df = spark.range(1, 1000000).toDF("id") \
	.repartition(10) \
	.withColumn("square", expr("id * id")) \
	.persist(StorageLevel = StorageLevel.MEMORY_AND_DISK)

df.count()
```

Here is an indicative list of available constants.
- DISK_ONLY
- MEMORY_ONLY
- MEMORY_AND_DISK
- MEMORY_ONLY_SER
- MEMORY_AND_ DISK_ SER
- OFF_HEAP
- MEMORY_ONLY_2
- MEMORY_ONLY_3
The numbers 2 and 3 in these constants represent the replication factor.

We can remove the date from the cache that we previously cached using the `unpersist()` method. We do not have any method called `uncache()` but `unpersist()` will do the job.

When we want to access large DataFrame multiple times across Spark actions, we should consider caching the data frames. But make sure we know the memory requirements and configure the memory accordingly. Do not cache data frames when significant portions of them don't fit in the memory. If we are not reusing the data frames frequently or too small, do not cache them.