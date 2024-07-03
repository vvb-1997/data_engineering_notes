The spark-submit allows to submit the spark application to the cluster and we can submit the application to run in one of the two modes.
- Cluster Mode
	- `spark-submit —master yarn —deploy-mode cluster`
- Client Mode
	- `spark-submit —master yarn —deploy-mode client`
# Cluster mode architecture

In the cluster mode, the spark-submit will reach the YARN RM requesting him to start the driver in an AM container. YARN will start the driver in the AM container on a worker node in the cluster. Then the driver will again request YARN to start some executor containers. So the YARN will start executor containers and handover them to the driver.

In the cluster mode, driver is running in the AM container on a worker node in the cluster and executors are also running in the executor containers on some worker nodes in the cluster.

![[Spark Client & Cluster Mode 1.png]]
# Client mode architecture

The spark-submit doesn't go to the YARN resource manager for starting an AM container. Instead, the spark-submit command will start the driver JVM directly on the client machine. So, in this case, the spark driver is a JVM application running on the client machine. This is the same machine where we executed the spark-submit command.

Now the driver will reach out to the YARN resource manager requesting executor containers. The YARN RM will start executor containers and handover them to the driver. The driver will start executors in those containers to do the job.

![[Spark Client & Cluster Mode 2.png]]
# Modes and Uses

So the cluster mode and client mode have got only one difference. In cluster mode, the driver runs in the cluster. In client mode, the driver runs in your client machine. All the executors will be running on the cluster in both cases.

We will almost always submit the application in cluster mode. It is unlikely that we submit the spark application in client mode.

We have two clear advantages of running application in cluster mode. 
1. The cluster mode allows to submit the application and log off from the client machine because the driver and executors are running on the cluster. They have nothing active on the client's machine. So even if we log off from your client machine, the driver and executor will continue running in the cluster.
2. Application runs faster in cluster mode because the driver is closer to the executors. The driver and executor communicate heavily, and if they are closer, you don't get impacted by network latency.

We have client mode for the following reasons.
1. The client mode is designed for interactive workloads. For example, spark-shell, pyspark, spark-sql runs the code in client mode. 
2. Similarly, Spark notebooks are also using the client mode for two reasons
	 1. The first reason is the interactive mode. Spark shell and notebooks gives an interactive method to work with Spark. They will run the code and show us the results hence they use client mode and keep the driver running on the local machine. When a driver is local, they can easily communicate with the driver, get the results and show it back to us.
	 2. The second reason is to make an exit easy. What happens if you log off from the client machine or stop the shell and quit. The driver dies because it was running locally. As soon as the driver dies, the YARN RM knows that the driver is dead, and the executors assigned to the driver are now orphans. So the RM will terminate the executor containers to free up the resources.