Spark DataFrame is an immutable data structure. That means it cannot be changed. However, you can give instructions to your driver about what do you want to do. And let the driver decide how to achieve it with the help of the executors. These instructions to the driver are called transformations.

And these transformations could be as simple as some SQL like operations such as `select()`, `filter()`, and `groupBy()`.

For Example: So we start with the DataFrame and apply a filter using the `where()` method with a simple condition where Age is less than 40.

You can add another transformation by selecting only four columns. You can chain more. Let me add a `groupBy()`. All this goes into the Same SparkSession object. In fact, we are using a builder pattern to create our SparkSession step by step.

```python
survery_df = spark.read \
	.option("header", "true") \
	.option("inferSchema", "true") \
	.csv(data_file)

survey_df.where("Age < 40") \
	.select("Age", "Gender", "Country", "state") \
	.groupBy("Country")
```

We can break it into smaller steps using intermediate variables. For example, you can hold your raw data frame in `surveyRawDF`. Then you can keep the filtered records in a separate intermediate variable `filteredSurveyDF`. Similarly, you can hold the `groupedSurveyDF` in a different variable.

```python
surveyRawDF = spark.read \
	.option("header", "true") \
	.option("inferSchema", "true") \
	.csv(data_file)

filteredSurveyDF = surveyRawDF.where("Age < 40") \
	.select("Age", "Gender", "Country", "state")

groupedSurveyDF = filteredSurveyDF.groupBy("Country")
```

No matter you chain these transformations or break them in smaller steps, all of these transformations are going to your SparkSession. And each intermediate step is another DataFrame that you can think of as an outcome of the applied transformation. Every transformation creates a graph of the operations. So this simple graph might look like this. 

![[Spark T&A 1.png]]

Spark data processing is all about creating a DAG of operations. So all that we are going to do in Spark programming is to create a DAG of activities and these operations are of two types.
- Transformations
- Actions.
# Transformation
Transformations are used to transform one DataFrame into another DataFrame without modifying the original DataFrame. For example, the `where()` clause transformation. It works on the input DataFrame and produces an output DataFrame. The initial input DataFrame remains immutable. And that's how the Spark DataFrame is an immutable data structure.

Spark Transformation is further classified into two types.
- Narrow Dependency Transformation
- Wide Dependency Transformations
## Narrow Dependency Transformation

A transformation that can be performed on a single partition and still produce an overall valid result is a Narrow Dependency. For example, the `where()` clause transformation is a narrow dependency.

Let's assume you have two partitions of a data frame. If you apply a `where()` clause transformation on this data frame, Spark executor should be able to perform this filtering on each partition. They do not depend on any other partition. And we still get a valid overall result on combining these filtered partitions. Such transformations are not dependent on other things, and it can be easily accomplished by each executor on their partitions.

![[Spark Transform 1.png]]
## Wide Dependency Transformations

A transformation that requires data from other partitions to produce correct results. For example a `groupBy()` transformation. We want to apply a `groupBy` transformation and then count the number of records in each group. The Spark executor can perform a `groupBy` on each individual partition and create a new DataFrame partition. 

We want to count the rows in each group. However, we do not get an overall correct result on combining these two outputs. So, the `groupBy` brakes the independence of a partition. Hence, Any aggregation after the `groupBy` will produce incorrect results.

![[Spark Transform 2.png]]

We can fix the issue by using the repartitioning of the grouped data. Combine all the partitions and then create some new partitions to make sure that all the records of the same group are collected into the same partition Here is the result of the repartitioning.

This operation of combining and repartitions of data is caused by the wide dependency transformation and known as shuffle and sort operation. Now you can apply the count() aggregation on these new partitions. The count() aggregation remains a narrow dependency because combining the outcome of the count() operation, and you still have a valid result.

![[Spark Transform 3.png]]

Lazy evaluation is a functional programming technique. Look at these transformations. A typical programmer is going to look at these transformations as individual statements that are executed and evaluated line by line. So you might think of it as read the data, then perform this transformation, then move to the next operation and execute it, and move on line by line.

```python
spark = SparkSession \
	.builder \
	.config(conf=conf) \
	.getOrCreate()

survey_df = load_survey_df(spark, sys.argv[1])
filtered_df = survey_df.where("Age < 40")
selected_df = filtered_df.select("Age", "Gender", "Country", "state")
grouped_df = selected_df.groupBy("Country")
count_df = grouped_df.count()
count_df.show()
```

However Spark programs are not going to behave the same because we are using a builder pattern to create a DAG of transformations. All of this goes to the Spark Driver. The driver is going to look at these operations, rearrange them to optimize certain activities, and finally create an execution plan which will be executed by the executors.

So these statements are not executed as individual operations, but they are converted into an optimized execution plan which is terminated and triggered by an Action. But what is an Action? Read, Write, Collect, and Show.

All of these are transformations because we are transforming one DataFrame into another DataFrame. Once we are done, we want to see the result. And you cannot see the result until we execute and finish all of this.

![[Spark T&A 2.png]]

So, we need to take action, compute the result, and show it to the console. And that's why show() is an Action. Similarly, all of these are actions.
- Read
- Write
- Collect
- Show

You want to write the result to a data file Collect the result from the executors to the driver Read a data file to infer the column names and schema. So, Spark Actions will terminate the Transformation DAG and trigger the execution. That is why we say that the transformations are lazy but actions are evaluated immediately. Anything which takes one DataFrame and converts into another DataFrame is a Transformation. But Operations that require you to read, write, collect to show the data is an Action.