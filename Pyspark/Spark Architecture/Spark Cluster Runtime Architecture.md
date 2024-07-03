Apache Spark is a distributed computing platform. However, every Spark application is a distributed application in itself. A Distributed application runs on a cluster. We can run it on local machine for development and unit testing but ultimately Spark application runs on a production cluster.

We have two most commonly used cluster technologies for Spark. Hadoop YARN cluster and  Kubernetes Cluster, there are few more such as Mesos and Spark Standalone cluster but these two covers more than 90% market share.
# Cluster

A cluster is a pool of physical computers working together but viewed as a single machine.

For example, we have a cluster of 10 machines. Each machine in this example cluster comes with 16 CPU cores and 64 GB RAM. They are networked, and we created a cluster of all these ten machines using the Hadoop YARN cluster manager.

![[Spark Cluster Arch 1.png]]

The entire pool is termed as a cluster, and individual machines are known as worker nodes. We have ten workers, each with 16 CPU cores and 64 GB RAM. So the total CPU capacity is 160 CPU cores, and the RAM capacity is 640 GB.

We want to run a Spark application on this cluster by using the spark-submit command and submit spark application to the cluster. The request will go to the YARN resource manager. The YARN RM will create one Application Master container on a worker node and start the application's main() method in the container.

![[Spark Cluster Arch 2.png]]

A container is an isolated virtual runtime environment. It comes with some CPU and memory allocation. For example, let's assume YARN RM gave 4 CPU Cores and 16 GB memory to this container and started it on a worker node. The worker node has got 16 CPU cores and 64 GB of memory. But YARN RM took 4 CPU cores and 16 GB memory and gave it to the container. The application's main() method will run in the container, and it can use 4 CPU cores and 16 GB memory

The container is running the main() method of the application and we have two possibilities here as Spark comes in two commonly used flavors.
- PySpark Application
- Scala Application

So let's assume the application is a PySpark application but Spark is written in Scala, and it runs in the Java virtual machine as Scala is a JVM language, and it always runs in the JVM. The Spark developers wanted to bring this to Python developers. So they created a Java wrapper on top of the Scala code. And then, they created a Python wrapper on top of the Java wrappers and this Python wrapper is known as PySpark.

![[Spark Cluster Arch 3.png]]

The python code in the main() method is designed to start a Java main() method internally. So PySpark application will start a JVM application. Once we have a JVM application, the PySpark wrapper will call the Java Wrapper using the Py4J connection.

Py4J allows a Python application to call a Java application. And that's how PySpark works. It will always start a JVM application and call Spark APIs in the JVM. The actual Spark application is always a Scala application running in the JVM. But PySpark is calling Java Wrapper using Py4J, and the Java Wrapper runs Scala code in the JVM.

The PySpark main method is the PySpark Driver and the JVM application here is the Application Driver. The Spark application driver is the main method of the application. If we wrote a PySpark application, we would have a PySpark driver and an application driver. But if we wrote it in Scala, we won't have a PySpark driver, but we will always have an application Driver.

![[Spark Cluster Arch 5.png]]

Spark application is a distributed application in itself as it distributes the work to others. So the driver does not perform any data processing work. Instead, it will create some executors and get the work done from them.

After starting, the driver will go back to the YARN RM and ask for some more containers. The RM will create some more containers on worker nodes and give them to the driver.

![[Spark Cluster Arch 6.png]]

So let's assume we got four new containers. And let's assume each container comes with 4 CPU Cores and 16 GB of memory. Now the driver will start spark executor in these containers. Each container will run one Spark executor, and the Spark executor is a JVM application.

The driver is a JVM application, and the executor is also a JVM application. These executors are responsible for doing all the data processing work. The driver will assign work to the executors, monitor them, and manage the overall application, but the executors do all the data processing.

![[Spark Cluster Arch 7.png]]

We have a container that runs the driver. This container is also known as Application Master or AM Container. The AM container runs a Spark driver. If we submitted the PySpark code, we would have a PySpark driver, and we will also have a JVM driver. These two will communicate using Py4J. If we started a Scala or a Java application, we would have a JVM driver only.

The driver will start first, and then it will request the Cluster RM for more containers. On receiving new containers, the driver will start executors in these new containers. We call them executor containers.

The AM container, as well as the executor containers, will run on the worker nodes. Your worker node may have a physical CPU and Memory. But your driver and executor can use the CPU and Memory given to the container. They cannot use extra CPU or Memory from the Workers.

If we are using Spark Dataframe API in Scala or Java, the runtime architecture will have one JVM driver and one or more JVM executors.

![[Spark Cluster Arch 8.png]]

If we are using PySpark Dataframe APIs, the runtime architecture will have one PySpark driver, one JVM driver, and one or more JVM executors.

![[Spark Cluster Arch 9.png]]

But if we are also using some additional Python libraries that are not part of the PySpark even if we are creating UDFs in Python, then the runtime architecture will have a Python worker at each executor.

![[Spark Cluster Arch 10.png]]

Python worker is a Python runtime environment and we need them only if you are using some python specific code or libraries. PySpark is a wrapper on Java code. So as long as we are using only PySpark, we do not need a Python runtime environment. All the PySpark code is translated into Java code, and it runs in the JVM. 

But if we are using some Python libraries which doesn't have a Java wrapper, we will need a Python runtime environment to run them. So the executors will create a Python runtime environment so they can execute the Python code.