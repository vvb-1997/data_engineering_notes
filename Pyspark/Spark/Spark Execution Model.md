# Spark Execution types

How to execute Spark Programs? There are two methods to run spark programs.
- Interactive Clients
	- Command-line Spark Shell and web-based Notebooks.
	- Offers an easy method to run your spark programs. Allows to run your Spark code line-by-line and get the output back on your console. 
	- Most of the people will use them for learning or during the development phase. 
	- Interactive clients are best suitable for exploration purposes.
- Submit a Job
	- spark-submit, Databricks Notebook, Rest API.
	- You may want to develop a stream processing application or a batch processing job, Spark supports both.
	- In both the scenarios, a stream processing Job or a periodic batch job, you must package your application and submit it to the Spark cluster for execution. And that's the second method for executing your programs on a Spark Cluster. 
	- For a production use case, you will be using this technique. 
	- Apache Spark comes with a spark-submit utility to allow you to submit your Spark jobs to the cluster.

# Spark distributed processing model

Spark applies a master-slave architecture to every application. when an application is submitted to the Spark, it is going to create a master process for your application. This master process is then going to create a bunch of slaves to distribute the work and complete your job.

You can think of these as runtime containers with some dedicated CPU and memory. In Spark terminology, the master is a driver, and the slaves are the executors. 

The cluster itself might have a master-node and a bunch of slave nodes. But those things are part of the cluster and are managed by the cluster manager.

The Spark engine is going to ask for a container from the underlying cluster manager to start the driver process. Once started, the driver is again going to ask for some more containers to start the executor process. And this happens for each application

![[Spark DPM 1.png]]

Suppose you are using the spark-submit utility, and submitted an application A1. Now Spark engine will request the cluster manager to give a container and start a driver process for A1. Once started, the driver will ask for some more containers from the cluster manager and start slave executors. Now your driver and executors are responsible for running your application code and doing the job that you wanted. 

Let's assume you submitted one more application, A2. Now the A2 is going to repeat the same. That means the spark engine will request a container and start the A2 driver. Then the A2 driver is going to ask some more containers and start the A2 executors. A1 and A2 are two independent applications. Both of them are following a master-salve architecture and have their own dedicated driver and executors.

Every spark application applies a master-slave architecture and runs independently on the cluster. And that is how Spark is a distributed computing platform.

![[Spark DPM 2.png]]

# Spark Execution Modes

How Spark runs your application on a local machine when we do not have a cluster and a cluster manager.

And the next question is about how Spark runs our application when we use interactive tools. Because in that case, we do not submit the application to the Spark cluster.

## Spark on Local machine

You can execute a Spark application on your local machine without even having a real cluster. We can configure your application to run on a variety of clusters, and the Spark engine is compatible with the following cluster managers.
- local \[n\]
- YARN
- Kubernetes
- Mesos
- Standalone

So here is a configuration that tells about the target cluster manager. 

```
[SPARK_APP_CONFIGS]
spark.app.name = HelloSpark
spark.master = local[3]
spark.sql.shuffle.partitions = 2
```

`local[3]` Spark runs locally as a multi-threaded application. It starts three threads, and that's what the three means. You can have 2, 3, 5, or whatever number you want.

If you simply say local and do not give any number, then it becomes a single-threaded application. So, when you configure your application to run with a single local thread, then you will have a driver only and no executors. And in that case, your driver is forced to do everything itself. Nothing happens in parallel. 

However, when you run your application with three local threads, then your application will have one dedicated thread for the driver and two executor threads. And this local cluster manager is designed for running, testing, and debugging your spark application locally. This technique is nothing but a simulation of distributed client-server architecture using multiple threads so we can test our application locally.

## Spark run with an interactive tool

Spark gives you a choice to run your application in one of the following modes.
- Client
	- The Client mode is designed for interactive clients. such as spark-shell and the notebooks.
	- In this mode, the Spark driver process runs locally at your client machine. However, the driver still connects to the cluster manager and starts all the executors on the cluster. 
	- And this is a powerful feature for submitting interactive queries and receiving results back to your client. And that is how your spark-shell and the Notebooks are working. 
	- However, if you are quitting your client or you log off from your client machine, then your driver dies, and hence the executors will also die in the absence of the driver. 
	- So, client mode is suitable for interactive work but not for long-running jobs.
- Cluster
	- The cluster mode is designed to submit your application to the cluster and let it run. 
	- In this mode, everything runs on the cluster - driver, as well as the executors. 
	- Once you submit your application to run in cluster mode, you can log off from the client machine, and driver is not impacted because it is running on the cluster. 
	- So, the cluster mode is meant for submitting long-running jobs to the cluster.

# Spark Execution Model

| Cluster Manager | Execution Modes | Execution Tools |
| --------------- | --------------- | --------------- |
| local \[n\]     | Client          | IDE, Notebook   |
| YARN            | Client          | Notebook, Shell |
| YARN            | Cluster         | Spark Submit    |
