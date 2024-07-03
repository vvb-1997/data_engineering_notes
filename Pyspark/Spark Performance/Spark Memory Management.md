We started the application using `spark.executor.memory` as 8 GB and `spark.executor.cores` equal to 4. Hence we got 8 GB memory and 4 CPU cores along with default 10% overhead memory. Right?

The JVM heap memory is further broken down into three parts.
- Reserved Memory
- Spark Memory Pool
- User Memory Pool

So let's assume we got 8 GB for the JVM heap. This 8 GB is divided into three parts. Spark will reserve 300 MB for itself. That's fixed, and the Spark engine itself uses it. The next part is the Spark executor memory pool. This one is controlled by the `spark.memory.fraction` configuration, and the default value is 60%. So for our example, the spark memory pool translates to 4620 MB.

We got 8 GB or 8000 MB.  Three hundred is gone for Reserved memory hence we are left with 7700 MB. Now you take 60% of this, and we will get 4620 MB for the Spark memory pool. We still have 3080 MB left, and that one is gone for user memory. 

We can change this 60-40 ratio between Spark memory and User memory using the `spark.memory.fraction` configuration. The default value is 60%.

![[Spark Memory Management 1.png]]

The reserved pool is gone for the Spark engine itself we cannot use it.
# User Memory Pool

The User Memory pool is used for non-dataframe operations. The User defined operations are
- User-defined data structures such as hash maps, Spark would use the user memory pool.
- Spark internal metadata 
- User-defined functions (UDF) are stored in the user memory.
- All the RDD information and the RDD operations are performed in user memory.
	- But if we are using DataFrame operations, they do not use the user memory even if the dataframe is internally translated and compiled into RDD.
	- We will be using user memory only if you apply RDD operations directly in your code.

The Spark memory pool is where all the data frames and dataframe operations live. We can increase it from 60% to 70% or even more if we are not using UDFs, custom data structures, and RDD operations. But we cannot make it zero or reduce it too much because we will need it for metadata and other internal things.
# Spark Memory Pool

The Spark Memory pool is the main executor memory pool which we will use for data frame operations and caching. So for this example, we started with 8 GB, but we are left with a 4620 MB Spark memory pool. This memory pool is further broken down into two sub-pools.
- Storage memory 
- Executor memory

The default break up is 50% each, but we can change it using the `spark.memory.storageFraction` configuration.  So Spark will reserve 50% of the memory pool for the storage pool, and the remaining 50% comes to the executor pool. For our example, we got a 2310 MB storage pool and another 2310 MB executor pool.

We use the storage pool for caching data frames and the Executor pool is to perform dataframe computations. So if we are joining two data frames, Spark will need to buffer some data for performing the joins. That buffer is created in the executor pool.

Similarly, if we are aggregating or performing some calculation, the required buffer memory comes from the executor pool. So executor pool is short-lived. We will use it for execution  and free it immediately as soon as your execution is complete.

The storage pool is used to cache the data frames. So if we are using dataframe cache operation, you will be caching the data in the storage pool. So the storage pool is long-term. We will cache the dataframe and keep it there as long as the executor is running or we want to un-cache it. 

![[Spark Memory Management 2.png]]

For this example, we asked for the 4 CPU cores. So the executor will have four slots, and we can run four parallel tasks in these slots. These slots are threads within the same JVM. We do not have multiple processes or JVMs here. We have a single JVM, and all the slots are simply threads in the same JVM.

![[Spark Memory Management 3.png]]

So we have one executor JVM, 2310 MB storage pool, another 2310 MB executor pool, and four threads to share these two memory pools.

![[Spark Memory Management 4.png]]

Each task will get 2310/4 MB executor memory That's static memory management and Spark used to assign task memory using this static method before spark 1.6. But now, they changed it and implemented a unified memory manager. The unified memory manager tries to implement fair allocation amongst the active tasks.

Let's assume I have only two active tasks and we have four slots. So the unified memory manager can allocate all the available execution memory amongst the two active tasks. There is nothing reserved for any task. The task will demand the execution memory, and the unified memory manager will allocate it from the pool.

![[Spark Memory Management 5.png]]

If the executor memory pool is fully consumed, the memory manager can also allocate executor memory from the storage memory pool as long as we have some free space. We start with a 50-50 boundary between executor memory and storage memory but we can change it using `spark.memory.storageFraction`.

![[Spark Memory Management 6.png]]

But this boundary is flexible, and we can borrow space as long as we have free space. So the memory manager can borrow some memory from the other side as long as the other side is free.

Let's assume we cached some data frames and entirely consumed the storage memory pool and want to cache some more data. So the memory manager will give me some more memory from the executor memory pool. The executor memory was free, so we consumed it.

![[Spark Memory Management 7.png]]

Now executor is performing some join operation, and it needs memory. So the executor will start consuming memory as long as there is free space. 

![[Spark Memory Management 8.png]]

But in the end, the memory manager will evict the cached data frames  and spill them to the disk to make more space for the executor. We borrowed it from the executor pool and now the executor needs it.  So the memory manager will evict it.

![[Spark Memory Management 9.png]]

However, you reached the boundary, and the executor needs more memory. This boundary is rigid, and the memory manager cannot evict any changed data blocks. If it was free, the memory manager should have given it to the executor. But it is not free, and we already occupied it. Right?

However, the executor needs more memory. The executor will try to spill few things to the disk and make some more room for itself. But there are things that we cannot spill to disk, then we are hitting the OOM exception.

The Spark memory management is too complex. But we should understand the structure so you can manage things more efficiently and we have the following configurations to control it.
- `spark.executor.memoryOverhead`
	- It will give you 10% extra of whatever you are asking for the JVM, and it is outside the JVM and reserved for overhead.
- `spark.executor.memory`
	- JVM memory comes from `spark.executor.memory`. This configuration allows to ask for the executor JVM memory.  Whatever we get, the spark engine will reserve 300 MB from your allocation.
- `spark.memory.fraction`
	- We can configure `spark.memory.fraction` to tell how much we want to use for DataFrame operations. The default value is 60%, but we can increase it.
	- The leftover is kept aside for non-dataframe operations, and we call it user memory.
- `spark.memory.storageFraction`
	- The `spark.memory.storageFraction` allows to set a hard boundary that the memory manager cannot evict. If we are not dependent on cached data, we can reduce this hard limit.
- `spark.executor.cores`
	- The executor core is critical because this configuration defines the maximum number of concurrent threads.
	- If we have too many threads, we will have too many tasks competing for memory and if we have a single thread, we might not be able to use the memory efficiently.
	- In general, Spark recommends two or more cores per executor, but we should not go beyond five cores. More than five cores cause excessive memory management overhead and contention, so they recommend stopping at five cores.

Spark also allows the following three configurations for off-heap memory outside the JVM.
- `spark.memory.offHeap.enabled`
- `spark.memory.offHeap.size`
- `spark.executor.pyspark.memory`
## `spark.memory.offHeap.enabled`
Spark operations and data caching are performed in the JVM heap and they perform best when using the on-heap memory. However, the JVM heap is subject to garbage collection. So if we are allocating a huge amount of heap memory to your executor, we might see excessive garbage collection delays.

However, Spark 3.x was optimized to perform some operations in the off-heap memory. Using off-heap memory gives us the flexibility of managing memory by user and avoid GC delays. Spark can take some advantage of this idea and use off-heap memory. So if we need an excessive amount of memory for your Spark application, it might help to take some off-heap memory.

For large memory requirements, mixing some on-heap and off-heap might help us to reduce GC delays. By default, the off-heap memory feature is disabled. We can enable it by setting `spark.memory.offHeap.enabled = true`. 
## `spark.memory.offHeap.size`
We can set your off-heap memory requirement using `spark.memory.offHeap.size`. Spark will use the off-heap memory to extend the size of spark executor memory and storage memory. The off-heap memory is some extra space. So if needed, Spark will use it to buffer spark dataframe operations and cache the data frames. So adding off-heap is an indirect method of increasing the executor and storage memory pools.

![[Spark Memory Management 10.png]]
## `spark.executor.pyspark.memory`

Scala is a JVM language, and hence Spark is also a JVM application. But if we are using PySpark, the application may need a Python worker. These Python workers cannot use JVM heap memory.  So they use off-heap overhead memory.

We can set that extra memory requirement for the Python workers using `spark.executor.pyspark.memory` there is no default value for this configuration because most of the PySpark applications do not use external Python libraries, and they do not need a Python worker. So Spark does not set a default value for this configuration parameter. But if we need some extra memory for your Python workers, we can set the requirement using the `spark.executor.pyspark.memory`.