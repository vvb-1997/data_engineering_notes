Hadoop is a distributed data processing platform that offers three core capabilities. The YARN stands for Yet Another Resource Manager. The name for the YARN doesn't correlate with Hadoop. However, YARN is the Hadoop cluster resource manager. We also call it Hadoop Cluster operating system, but the Hadoop creators preferred to name it a cluster resource manager.

An Operating system allows multiple programs to be in memory and run simultaneously. And this is achieved by sharing resources such as CPU, Memory, and disk I/O. The YARN does the same. It allows multiple applications to run on the Hadoop Cluster and share resources amongst the  applications.
# Hadoop YARN 

Hadoop YARN has three main components
- Resource Manager
- Node Manager
- Application Master

For example, we want to make a Hadoop cluster using these five computers. So we will install Hadoop on all these computers and configure them to make a Hadoop cluster. Hadoop uses a master-slave architecture. So one of these machines will become the master, and the remaining will act as the worker node. Configuring Hadoop will also install a YARN resource manager service on the master. It also installs a node manager service on all the worker nodes. So this diagram represents the YARN configuration on a Hadoop cluster. We have a five-node cluster. One node acts as a master and runs the YARN **resource manager** (RM) service. The other four nodes act as a Worker and run a **node manager** (NM) service. The node manager will regularly send the node status report to the resource manager. Now you want to run a data processing application on this cluster.
![[Hadoop YARN 1.png]]

For running an application on Hadoop, you must submit the application to the YARN resource manager. We have many ways to submit an application to YARN. Let's assume you submitted a Java application to the YARN using a command line submit tool. Now the resource manager should run this application on the cluster. So, the resource manager will request one of the node managers to start a resource container and run an AM (application master) in the container. Application starts running inside the Application Master container.

![[Hadoop YARN 2.png]]

But what is a container? It is a set of resources that includes memory and CPU. For example, a YARN container may be a set of 2 CPU cores and 4 GB of Memory. So wherever we refer to YARN Container, you should think of a box of some CPU and Memory. 

Now let's assume we submit another application. The resource manager will again repeat the same process. It will ask another node manager to allocate a new AM container and start this new application inside the AM container. Each application on YARN runs inside a different AM container. If you have ten applications running in parallel, you will see 10 AM containers on your Hadoop cluster.

![[Hadoop YARN 3.png]]

# HDFS

The HDFS stands for Hadoop Distributed File system, and it allows you to save and retrieve data files in the Hadoop Cluster. 

The HDFS has the following components.
- Name Node
- Data Node

Let's assume we have five computers shown here. We already installed Hadoop on these computers and created a Hadoop cluster. We also learned that Hadoop implements a master/worker architecture. So one node is designated as the master, and the other nodes act as workers. Hadoop will install the Name Node service on the master. And each worker node runs a data node service. The name node with the data node service forms the HDFS. The primary purpose of the HDFS is to allow us to save files on this Hadoop cluster and read them whenever required. 

Let's assume you want to copy a large data file on your Hadoop cluster. So you will initiate the file copy command. The file copy request will go to the name node. The name node will redirect the file Copy command to one or more data nodes. Let's assume you get redirection for three data nodes. The file copy command will split the file into smaller parts and write those parts on the three data nodes. The file copy command will break your file into smaller parts. Each part is known as a block. The typical block size is 128 MB. Now the file copy command will write some blocks to data node 1, some other blocks will go to data node 2, and the remaining ones will go and sit at data node 3. So your date file data will break into blocks, and Hadoop will distribute these blocks on the data nodes. 

![[Hadoop HDFS 1.png]]

The name node facilitates this process and keeps track of all the file metadata. File metadata stored at Name Node
- File name
- Directory Location
- File Size
- File blocks, block ID, block sequence, block location

The file is broken and spared on the data nodes. And all the information required to reassemble the file is kept at the name node. Now let's come back to the read operation. You will initiate a read operation when you want to read a data file The request goes to the name node. The name node owns all the information for reassembling the file from the blocks stored at the data node. So the name node will redirect the read operation to the target data nodes. The read API will receive data blocks from the data node and reassemble the file using the metadata provided by the name node.

# Map Reduce 

Map-reduce is a programming model and a framework.
- Programming Model
- Programming Framework
## Programming Model

A programming model is a technique or a way of solving problems. The M/R framework is a set of APIs and services that allow you to apply the map-reduce programming model. Hadoop taught us the map-reduce programming modal and also offered a Map-Reduce programming framework to implement it. Understanding the map-reduce model is more critical than the M/R framework. because the Hadoop Map Reduce framework is now outdated and not used anywhere. However, the Map-Reduce model is critical for you to understand because we still use it in many places.

Let me give you a problem. You have a CSV file. The file size is 20 TB. I asked you to count the number of lines in the file. How will you do that? 

A simple solution. Open the file, and you will get a file handle. Then loop through each line in the file. Every time you read a line, you can increment a counter. You will come out of the loop on reaching the end of the file. Now you can close the file and then print the count.

```python
open file as f_hd
	for each t_line in f_hd.get_line()
		n_count = n_count + 1
close f_hd
print n_count
```

But we have a small problem. The file size is 20 TB. Can you find a machine to store a 20 TB file and run your line count program on this file? It is hard to find such machines. Even if you find a high-end server machine, your program will take hours or days to count the lines. It takes 3-4 hours to read one TB of data from a disk. 

So we have two problems here.
- Storage capacity problem
- Processing time problem

And this problem becomes more complex if we grow the size in petabytes. We also have a processing time problem. Hadoop offered to solution to both problems.

You can use the Hadoop cluster to store the file. Let's assume you have a 21 node Hadoop cluster. One node becomes the master, and the other 20 nodes are the workers.

HDFS runs a name node in the master and a data node on the other workers. YARN runs a Resource Manager on the master, and Node Manager runs on the workers. So we have those services running on the cluster.

Now let's talk about the resources. Each node comes with
- Four hard disks of 2 TB --> 8TB per node --> 160 TB Cluster Storage.
- Four dual-core CPUs --> 160 CPU Cluster Cores.
- 64 GB of memory --> 1280 GB of Cluster Memory.

![[Hadoop MR 1.png]]

HDFS will break the file into small 128 MB blocks and spread them across the cluster. So some data nodes will sore data blocks, and altogether they can easily store your 20 TB file. Storage problem is taken care of by the Hadoop cluster. If you need more storage, you can increase the cluster size and add more computers.

![[Hadoop MR 2.png]]

What about the processing time problem? That's where the map-reduce model comes into play. Let me write the line count logic once again. Now I have broken my logic into two parts. The first part is known as the Map function The second part is known as the Reduce function. The old logic was to open the file and count the lines. 

The map function opens the file block and counts the lines. So the old logic opens the file and counts the lines. But the new logic opens the file block and counts the line.  The map function on all the data nodes in parallel. This map() function will open each block on the data node and count the lines. End of the execution, I will have the number of lines in the blocks at the given data node.

![[Hadoop MR 3.png]]

MR is counting lines on 14 data nodes in parallel. Everything runs at the same time. And I will get the line counts in 1/14th of the time compared to doing it on a single machine. However, there will have 14 line counts. Each count represents the number of lines on their respective data node. Now, I will start a Reduce function at one node. 

```python
def map(file_block):
	open file_block as fb_hd
	for each t_line in fb_hd.get_line()
		n_count = n_count + 1
	close fb_hd
	return n_count
```

![[Hadoop MR 4.png]]

All the data node will send their counts to the reduce function. The reduce function will receive 14 line counts in an array. So it will look through the array and sum up all the line counts. That's what is the logic of the reduce function. The reduce function will loop through the list of counts and sum it up. The sum is the number of lines in the file.

```python
def reduce(list_counts):
	for each cnt in list_counts
		total_count = total_count + cnt
	print total_count
```

So map-reduce is a programming model to break your application logic into two parts. Map part where you will do the parallel calculations or processing and the reduce part where you can do the summations or other types of aggregations and consolidations.

But can we do some other type of complex data processing work using this map-reduce approach? The answer is a big yes. Hadoop offered Hive database as an additional component. Hive allows you to write SQL expressions. However, the Hive SQL engine translates all your SQL expressions into Map Reduce programs. We use high-level solutions such as Hive SQL, Spark SQL, Spark Scripting, etc., and develop data processing applications. We do not go down to the Map-reduce level.

## Programming Framework

It is a single program, but we wrote two functions. Now lets submit this program to the Hadoop cluster i.e. to the YARN Resource Manager. The YARN resource manager is responsible for running my program. So the YARN resource manager will request one node manager to start a YARN resource container and run my program in the container. This first container is known as Application Master Container.

![[Hadoop MR 5.png]]

So let's assume Hadoop started my AM container on a worker node. This container has got 1 CPU core and 4 GB memory. YARN container is a bundle of CPU and Memory to run an application. The map-reduce program is running in the container. 

We have a map and a Reduce function and are written using Hadoop Map Reduce Framework API. The program will trigger the Hadoop Map-Reduce Framework. And the M/R framework will take control of the program. We have only wrote two functions, but the M/R framework will take control and start running in the AM container.

The M/R framework will again request the resource manager to start 14 more containers and run the map function in those containers. The resource manager will pass on the request to node managers, and they will start 14 new containers. So now one new container is running on 14 workers. And these new containers will run my map function.

![[Hadoop MR 6.png]]

We wanted to run our map function on all the data nodes. The Hadoop Map Reduce framework does that for us. We only write a map() function and implement the logic inside it. But when we run our program, the Hadoop M/R framework will take control and work with the YARN resource manager to execute the map function on all the data nodes. The M/R framework will not stop there. It will wait for all the map functions to be complete. Then it will collect the block counts from all the map methods, pack those values into an array, and then start the reduce method on a single node. 

The M/R framework will start the map methods and wait for completion. Once completed, it will collect the output from all the map methods. Then, it goes back to the resource manager and requests a new container to run the reduce function. The RM will coordinate with the node manager to give one more container and start the reduce function. The Reduce function needs the output of all the map functions. The M/R framework will facilitate the output of map functions to the Reduce function. The reduce function will sum up the counts and print them.

Map Reduce Model Implement logic in two functions
1. Map Function
	- Reads data block
	- Applies logic at block level
	- Map output is sent to Reduce
2. Reduce Function
	- Receives Map output
	- Consolidates the results

Hadoop M/R framework implement the map-reduce model.
- YARN manages resource allocation
- HDFS manages data blocks

# Hive

Hadoop grabbed immense attention and popularity among organizations and professionals. Since the development of Hadoop, there have been many other solutions developed over the Hadoop platform and open-sourced by various organizations. Some of the most widely adopted systems were Pig, Hive, and Hbase. Apache Hive was one of the most popular components of Hadoop.

Hive capabilities
1. Create
	-  Databases
	-  Tables
	-  Views
2. Run SQL Queries

Map Reduce Programming model was complex to use. Application developers struggled to solve data processing problems using Map Reduce. Hive came to the rescue. It allowed us to create databases and tables using DDL Statement. Then they also allowed us to use SQL queries on the table. The majority of the development workforce was familiar with the RDBMS, and they already knew SQL. So using SQL was easy to adopt. Hive SQL engine internally translated SQL queries into M/R programs. But application developers were saved from writing Map Reduce code in Java.

Hadoop as a platform and Hive as a Hadoop database became very popular. But we still had some other problems which needed improvements.

1. Performance
	- Map Reduce was slow. Map/Reduce implemented parallel processing. So it was faster and more efficient than a single computer.
	- However, Hive SQL queries performed slower than the RDBMS SQL queries.
2. Ease of development
	- Hadoop offered only Map/Reduce model. But writing Map/Reduce programs was a black art. Only a few highly-skilled engineers were writing M/R code. 
	- The industry needed to simplify so an average engineer could develop data processing applications on the Hadoop platform.
3. Language support
	- Hadoop M/R was only available in Java. Industry wanted to support some other languages.
4. Storage
	- Setting up storage for the Hadoop cluster required adding computers. If you want more storage, add more computers to the Hadoop cluster. 
	- It was a cheaper option in the beginning. But when Cloud platforms started to offer storage at throw-away prices, adding computers for creating Hadoop storage started to look expensive. 
	- The business wanted to use cloud storage instead of HDFS storage because Cloud storage was a cheaper option.
5. Resource Management
	- YARN worked as a resource manager for Hadoop. And it offered a YARN container for managing resources. 
	- But at the time, the industry developed some other container management solutions such as Mesos, Docker, and Kubernetes. 
	- YARN forced us to install and create a Hadoop cluster and then manage containers over and above the Hadoop cluster. Experts wanted to try other lightweight container management options.
# Spark
 
And that's where **Apache Spark** came into existence as an improvement over the Hadoop Map/Reduce. Apache Spark was invented at UC Berkeley to simplify and improve the ideas of the MapReduce Framework. However, it soon gained popularity and took a direction for solving all the problems listed earlier.

1. Performance
	- 10 to 100 times faster than Hadoop
2. Ease of development
	- Spark SQL
	- High performance SQL Engine
	- Composable Function API
3. Language support
	- Java, Scala, Python and R
4. Storage
	- HDFS Storage
	- Cloud Storage (Amazon S3, Azure Blob storage, etc.)
5. Resource Management
	- YARN, Mesos, Kubernetes

Runs in two setups
1. With Hadoop (Data Lake)
2. Without Hadoop (Lakehouse)

So in today's world, Spark exists on two kinds of platforms. We use the Hadoop platform as the Data Lake platform, and the primary developer technology on Hadoop Data Lake is now Apache Spark. Map/Reduce Framework is gone away forever, and Hive is also losing its place for Spark SQL. The Cloud platforms are more popular these days. So the idea of Hadoop Data Lake is now advanced and modernized with a new name of Lakehouse on the cloud platforms. The driving force behind Cloud Lakehouse is the Databricks Spark platform. So Spark is again the primary developer technology for Lakehouse.