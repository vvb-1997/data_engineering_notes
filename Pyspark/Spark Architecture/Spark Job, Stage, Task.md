# Spark Data Frame API Categories

- Transformations
	- Used for transforming data
	- Further classification
		- Narrow dependency
			- Performed in parallel on data partitions
			- Example: `select()`, `filter()`, `withColumn()`, `drop()`
		- Wide dependency
			- Performed after grouping data from multiple partitions
			- Example: `groupBy()`, `join()`, `cube()`, `rollup()`, and `agg()`
- Actions
	- Used to trigger some work (Job)
	- Example: `read()`, `write()`, `collect()`, `take()`, and `count()`
	- All spark actions trigger one or more Spark jobs.

For the example we are reading a CSV file and also inferring the schema by creating an initial data frame and named it `surveyRawDF`. Then we am repartitioning the `surveyRawDF` and creating a new Dataframe named `partitionedSurveyDF`. We repartitioned it to make sure so that we have two internal data partitions. `repartition()` is a wide dependency transformation.

```python
surveyRawDF = spark.read
	.option("header", "true")
	.option("inferSchema", "true")
	.csv(args(0))

partitionedSurveyDF = surveyRawDF.repartition( numPartitions = 2)

countDF = partitionedSurveyDF.where(conditionExpr = "Age < 40")
	.select( "Age","Gender", "Country", "state")
	.groupBy("Country")
	.count()

 print(countDF.collect().mkString("->"))
```

Then we are taking a dataframe of two partitions and applying some transformations. The first transformation is `where()` method, and that one is a narrow transformation. Then we have a `select()` method, and this is also a narrow transformation. The third one is a `groupBy()`, and it is a wide dependency transformation. Then we have a `count()` method, and this is a narrow transformation. Then in the last line, we are applying the `collect()` method on the `countDF` and that's an action.

This code snippet has got two spark code blocks. Start from the first line and look for action. Wherever we find action, the first block ends there, and the following line starts a new block.

In this example, the first line itself is an action as `read()` method is an action. So the first block starts at the first line, and it also ends at the first line.

The following line starts a new code block. Then we will keep looking for the action, but we find it only at the last line. So the second block starts from the second line and ends at the last line.

![[Spark Job, Stage, Task 1.png]]

Spark will run each code block as one spark job. Each action creates a spark job and that's why we keep looking for actions to separate spark code blocks. Spark Jobs is as simple as each spark action runs as a spark job that contains all the transformations of its code block.

So in this example, for the second code block that terminates with a spark action. The application driver will take this block, compile it and create a Spark job. This job must be performed by the executors because the driver doesn't perform any data processing job. The driver must break this job into smaller tasks and assign them to the executors.

The job start with `surveyDF` and repartition it to create `partitionedSurveyDF`. Then it must apply the four transformations to create the `countDF`. So this is what we call the logical query plan of your spark job.

![[Spark Job, Stage, Task 2.png]]

Spark driver will crate a logical query plan for each spark job. Once we have the logical plan, the driver will start breaking this plan into stages. The driver will look at this plan to find out the wide dependency transformations. 

We have two wide dependencies in this plan. The first one is the `repartition()` method, and the second one is the `groupBy()` method. So the driver will break this plan after each wide dependency. The logical plan is now broken down into three stages. Each stage can have one or more narrow transformations, and the last operation of the stage is a wide dependency transformation.

![[Spark Job, Stage, Task 3.png]]

Spark cannot run these stages in parallel. We should finish the first stage, and then only we can start the next stage because the output of the first stage is an input for the next stage.

We are reading `surveyDF` and repartitioning it to create `partitionedSurveyDF`. We have 1 partitions in the `surveyDF` and 2 partitions in the `partitionedSurveyDF` as we repartitioned it and create two partitions. The final output of the stage must be stored in an exchange buffer called as write exchange.

Now the output of one stage becomes the input of the next stage hence the next stage starts with the exchange buffer called as read exchange. Spark is a distributed system and the Write exchange and the read exchange may be on two different worker nodes.

It may be on the same worker, but it can be on two different works. So we must consider a copy of data partitions from the Write exchange to the read exchange. And this copy operation is popularly known as the Shuffle/Sort operation. Shuffle/sort will move data from the Write exchange to the read exchange.

The stage ends with a wide dependency transformation, and hence it requires a shuffle/sort of the data. The Shuffle/Sort is an expensive operation in the Spark cluster. It requires a write exchange buffer and a read exchange buffer. The data from the write exchange buffer is sent to the read exchange buffer over the network.

For the second stage, the read exchange has got two partitions and will have two input partitions here. And now we can run these transformations in parallel on those two partitions. Spark can execute the same plan in parallel on two partitions because we have two partitions. And this is what we call a task.

So we can have two parallel tasks in stage two. Stage two also ends with a wide dependency transformation. So we again need a shuffle/sort operation here. 

So the Spark driver starts with a logical query plan of a job and converts it to a runtime execution plan. And this is how the runtime execution plan looks for the example spark job.

![[Spark Job, Stage, Task 4.png]]

-  Job
	- Spark creates one job for each action. This job may contain a series of multiple transformations. The Spark engine will optimize those transformations and create a logical plan for the job.
-  Stage
	- Then spark will break the logical plan at the end of every wide dependency and create two or more stages. If we do not have any wide dependency, the plan will be a single-stage plan. But if we have N wide-dependencies, the plan should have N+1 stages. 
- Shuffle/Sort
	- Data from one stage to another stage is shared using the shuffle/sort operation. 
- Task
	- Now each stage may be executed as one or more parallel tasks. The task is the most critical concept for a Spark job. A task is the smallest unit of work in a Spark job. The Spark driver assigns these tasks to the executors and asks them to do the work.
	- The number of tasks in the stage is equal to the number of input partitions. In our example, the first stage starts with one single partition. So it can have a maximum of one parallel task. We made sure two partitions in the second stage. So we can have two parallel tasks in stage two. If I create 100 partitions for stage two, I can have 100 parallel tasks for stage two.

The executor needs the following things to perform the task.
- The task Code
- Data Partition

So the driver is responsible for assigning a task to the executor. The executor will ask for the code or API to be executed for the task. It will also ask for the data frame partition on which to execute the given code. The application driver facilitates both these things to the executor, and the executor performs the task.

In spark cluster we have a driver and four executors. Each executor will have one JVM process. But we assigned 4 CPU cores to each executor. So, my Executor-JVM can create four parallel threads. and that's the slot capacity of my executor.

So each executor can have four parallel threads, and we call them executor slots. The drive knows how many slots do we have at each executor. And he is going to assign tasks to fit in the executor slots.

![[Spark Job, Stage, Task 5.png]]

So for this configuration, let's assume the driver has a job stage to finish. And we have ten input partitions for the stage. So we can have ten parallel tasks for the same. Now the driver will assign those ten tasks in these slots. All the green slots are filled. But we have some extra capacity that we are wasting because we do not have enough tasks for this stage.

![[Spark Job, Stage, Task 6.png]]

Once the stage is complete, all slots will now be free. Now the driver should start the next stage. And we have 32 tasks for the next stage. But we have 16 slots only. The driver will schedule 16 tasks in the available slots. The remaining 16 will wait for slots to become available again. That's how these tasks are assigned and run by the executor.

![[Spark Job, Stage, Task 7.png]]

The `collect()` action requires each task to send data back to the driver. So the tasks of the last stage will send the result back to the driver over the network. The driver will collect data from all the tasks and present it to you. 

In the example, we wanted to flush the result into the log so the driver will do the same. If we had an action to write the result in a data file, in that case, all the tasks will write a data file partition and send the partition details to the driver. The driver considers the job done when all the tasks are successful. If any task fails, the driver might want to retry it. So it can restart the task at a different executor. If all retries also fail, then the driver returns an exception and marks the job failed.