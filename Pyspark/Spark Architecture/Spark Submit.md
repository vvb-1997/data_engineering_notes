We have many ways to submit a Spark application to the cluster. However, the most commonly used method is the spark-submit command-line tool

The spark-submit is a command-line tool that allows us to submit the Spark application to the cluster.

Here is a general structure of the spark-submit command. This is a minimalist example with the most commonly used options. We can use the spark-submit command and pass some configuration options using -- in front of the option name. The second last argument is the application jar or a PySpark script.

```bash
spark-submit --class <main-class> —master <master-url> —deploy-mode <deploy-mode> <application-jar> [application-args]
```

Below is a list of the most commonly used options.

![[Spark Submit 1.png]]

- `class`
	The class option is only valid for Java or Scala, and it is not required when we submit a PySpark application. This class option tells the driving class name where we defined the main() method for java and Scala.
- `master`
	The master option is to tell the cluster manager. If we are using YARN, the value of master is yarn. But if we want to run application on the local machine, you should set the master to local. We can also use local\[3] to run spark locally with three threads.
- `deploy-mode`
	The deploy mode takes one of the following two configurations. client and cluster. Remember we have only two deploy modes. The local is a master configuration, and it is not a valid deploy mode.
- `conf`
	The `conf` option allows to set additional spark configurations. For example, we can set `spark.executor.memoryOverhead` = 0.20 using the --conf. The default value for `spark.executor.memoryOverhea`d is 0.10.
# Resource allocation options.

Spark application runs as one driver and one or more executors. 
## Driver resources
We can use the `driver-cores` option to ask for CPU cores for the driver container. Similarly, we can ask RAM for the driver container using the `driver-memory` option.

## Executors resources
The `num-executors` is to set the required number of executors. And the `executor-cores` and `executor-memory` are for the CPU and memory of the executor containers.
# PySpark and Scala Spark Submit

We will submit and run it in my Hadoop YARN cluster, so the master is yarn. We want this application to run in cluster mode, so the deploy-mode is the cluster. We are requesting YARN RM to allocate a driver container with 2 CPU cores and 8 GB memory.

We also want the driver to ask YARN RM for four executor containers and start four executors, hence we defined num-executors as four. Then we wanted to get 2 CPU cores and 16 GB of memory for each executor container.
## PySpark Spark Submit
```bash
spark-submit —master yarn —deploy-mode cluster —driver-cores 2 --driver-memory 8G —num-executors 4 —executor-cores 4 —executor-memory 16G hello-spark.py
```

## Scala Spark Submit
```bash
spark-submit —class guru.learningjournal.HelloSpark —master yarn —deploy-mode cluster —driver-cores 2 —driver-memory 8G
—num-executors 4 —executor-cores 4 —executor-memory 16G hello-spark.jar
```
