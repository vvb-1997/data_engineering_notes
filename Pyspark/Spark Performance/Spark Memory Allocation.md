When we submit a spark application in a YARN cluster, the YARN RM will allocate an application master (AM) container and start the driver JVM in the container.  The driver will start with some memory allocation which was requested. 

We can ask for the driver memory using two configurations.
- `spark.driver.memory`
- `spark.driver.memoryOverhead`

So let's assume you asked for the `spark.driver.memory` = 1GB and the default value of `spark.driver.memoryOverhead` = 0.10. The YARN RM will allocate 1 GB of memory for the driver JVM.  And it will also allocate 10% of the requested memory or 384 MB, whatever is higher for container overhead.

As the 10% of 1 GB is 100 MB, but 100 MB is less than the 384 MB. So the YARN RM will allocate 384 MB for overhead. So the total memory for the container comes to 1 GB + 384 MB.  The overhead memory is used by the container process or any other non JVM process within the container.  The Spark driver uses all the JVM heap but nothing from the overhead.

![[Spark Memory Allocation 1.png]]

Now the driver is started with 1 GB of JVM heap. So the driver will again request for the executor containers from the YARN. The YARN RM will allocate a bunch of executor containers. The total memory allocated to the executor container is the sum of the following.
- Heap Memory
	- JVM Heap is the `spark.executor.memory`.
- Overhead Memory
	- Overhead memory configuration is the `spark.executor.memoryOverhead`.
- Off Heap Memory 
	- Off Heap memory comes from `spark.memory.offHeap.size`.
- PySpark Memory
	- PySpark memory comes from the `spark.executor.pyspark.memory`.

So the driver will look at all these configurations to calculate the memory requirement and sum it up. Now let's assume you asked for `spark.executor.memory` = 8 GB. The default value of `spark.executor.memoryOverhead` = 10%. Let's assume the other two configurations are not set, and the default value is zero.

We asked `spark.executor.memory` =  8 GB, so we will get 8 GB for JVM. Then we asked for `spark.executor.memoryOverhead` = 10%, so we will get 800 MB extra for the overhead. The total container memory comes to 8800 MB. So the driver will ask for 8.8 GB containers to the YARN RM. 

![[Spark Memory Allocation 2.png]]

The container should run on a worker node in the YARN cluster but what if the worker node is a 6 GB machine. YARN cannot allocate an 8 GB container on a 6 GB machine because there is not enough physical memory.

Hence before we ask for the driver or executor memory, we should check with your cluster admin for the maximum allowed value. If we are using YARN RM, we should look for the following configurations.
- `yarn.scheduler.maximum-allocation-mb` 
- `yarn.nodemanager.resource.memory-mb`

For example, let's assume we are using AWS c4.large instance type to set up your Spark cluster using AWS EMR.  The c4.large instance comes with 3.75 GB RAM and we launched an EMR cluster using c4.large instances. The EMR cluster will start with the default value of `yarn.scheduler.maximum-allocation-mb`  = 1792. That means we cannot ask for a container of higher than 1.792 GB.  That's your physical memory limit for each container. 

The total physical memory for a driver container comes from the following two configurations. `spark.driver.memory` and `spark.driver.memoryOverhead`. Once allocated, it becomes physical memory limit for the spark driver. 

For example, if we asked for a 4 GB `spark.driver.memory`, we will get 4 GB JVM heap and 400 MB off JVM Overhead memory. Now we have three limits. If any of these limits are violated, we will see an OOM exception.
- Spark driver JVM cannot use more than 4 GB. 
- Non-JVM workload in the container cannot use more than 400 MB.
- Container cannot use more than 4.4 GB of memory in total. 

The total physical memory for an executor container comes from the following four configurations.
- `spark.executor.memoryOverhead`
- `spark.executor.memory`
- `spark.memory.offHeap.size`
- `spark.executor.pyspark.memory`

The default value for the third and fourth configurations are not defined. So we can consider them zero. Now let's assume we asked for `spark.executor.memory` = 8 GB, so we will get 8 GB for the Spark executor JVM. We will also get 800 MB for overhead.  The total physical memory of your container is 8.8 GB.  Now we have three limits. We will see an OOM exception when any of these limits are crossed.
- Executor JVM cannot use more than 8 GB of memory.
- Non JVM processes cannot use more than 800 MB.
- Container has a maximum physical limit of 8.8 GB.

Physical memory limit at the worker node is defined in `yarn.scheduler.maximum-allocation-mb` for the maximum limit. We cannot get more than the maximum allocation mb.

As PySpark is not a JVM process, we will not get anything from those 8 GBs. All we have is 800 MB of overhead memory.  Some 300 to 400 MB of this is constantly consumed by the container processes and other internal processes.  So the PySpark will get approximately 400 MB.

If the PySpark consumes more than what can be accommodated in the overhead, we will see an OOM error.

In YARN perspective, we have a container, and the container has got some memory. This total memory is broken into two parts.
- Heap memory( driver/executor memory)
	- The heap memory goes to the JVM. We call it driver memory when we are running a driver in this container. Similarly, we call it executor memory when the container is running an executor. 
- Overhead memory (OS Memory)
	- The overhead memory is used for a bunch of things such as for network buffers. 
	- So we will be using overhead memory as your shuffle exchange or reading partition data from remote storage etc.

Both the memory portions are critical for your Spark application. And more than often, lack of enough overhead memory will cost us an OOM exception. Because the overhead memory is often overlooked, but it is used as your shuffle exchange or network read buffer.

![[Spark Memory Allocation 3.png]]