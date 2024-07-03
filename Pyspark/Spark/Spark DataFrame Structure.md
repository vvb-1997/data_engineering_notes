
DataFrame is a distributed data structure which helps Spark to implement distributed processing. SparkSession offers `read()` method to read data from a data file such as a CSV file. In a real-life scenario, that CSV file is going to be stored in a distributed storage such as HDFS or cloud storage such as Amazon S3.

All these distributed storage systems are designed to partition your data file and store those partitions across the distributed storage nodes. So let's assume that you have these 10 node storage cluster. The file might be broken into 100 smaller partitions, and those partitions are stored on these ten nodes. The data file is stored as smaller partitions, and each storage node may have one or more partitions of your file.

While reading file using a Spark `DataFrameReader`, Since the data is already partitioned, the `DataFrameReader` is also going to read them as a bunch of in-memory partitions. The DataFrame are a bunch of smaller DataFrames, each logically representing a partition.

The driver that we want to read this data file. And the driver is going to reach out to the cluster manager and the storage manager to get the details about the data file partitions. So at runtime, your driver knows how to read the data file and how many partitions are there. Hence, it can create a logical in-memory data structure, which we see as a DataFrame. However, nothing is loaded in the memory yet. Its all a logical structure with enough information to actually load it. The DataFrame as a bunch of smaller DataFrames, each logically representing a partition.  

![[Spark DF Structure 1.png]]

For Spark executors, we already learned that you can configure your SparkSession to start N number of executors and can also decide on how much memory and CPU do you want to allocate to your executors. All those configurations are available to the driver.

Let's assume that you configured to start five executors, each with 10 GB of memory and 5 CPU cores. Now the driver is again going to reach out to the cluster manager and ask for the containers. Once those containers are allocated, the driver is going to start the executors within those containers. We already know that each executor is nothing but a JVM process with some assigned CPU cores and memory. 

The driver is ready to distribute the work to these executors by assigning some DataFrame partitions to each JVM core. And these executor cores will load their respective partitions in the memory. This is the distributed DataFrame setup where each executor core is assigned its own data partition to work on.

![[Spark DF Structure 2.png]]

Spark will also try to minimize the network bandwidth for loading data from the physical storage to the JVM memory using internal Spark optimization. While assigning partitions to these executors, Spark will try its best to allocate the partitions which are closest to the executors in the network. However, such data locality is not always possible. And Spark, as well as the cluster manager, is going to work together to achieve the best possible localization.