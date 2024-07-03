The term RDD stands for the Resilient Distributed Dataset. An RDD is a dataset. That means they are nothing but a data structure to hold your data records. They are similar to DataFrames because DataFrames are also built on top of the RDDs.

However, unlike DataFrames, RDD records are just language-native objects, and they do not have a row/column structure and a schema. So in simple words, RDD is just like a Scala, Java, or a Python collection. You can create an RDD reading your data from a file. However, RDD is internally broken down into partitions to form a distributed collection.

Same as DataFrames, they are partitioned and spread across the executor cores so they can be processed in parallel. RDDs are resilient. That means they are fault-tolerant. RDDs are fault-tolerant because they also store information about how they are created.

Let's assume an RDD partition is assigned to an executor core for processing it. In some time, the executor fails or crashes. That's a fault, and you could lose your RDD partition. However, the driver will notice the failure and assign the same RDD partition to another executor core. The new executor core will reload the RDD partition and start the processing. And this can be done easily because each RDD partition comes with the information about how to create it and how to process it. And that's why we call them resilient. That means an RDD partition can be recreated and reprocessed anywhere in the cluster.

RDDs are similar to DataFrames, but they lack a row/column structure and the schema. Now let's create an example to see how things work. The first thing is to learn to create an RDD.
The RDD APIs were based on `SparkContext`. We can create a `SparkContext` and pass on your `SparkConf`. Now all the RDD APIs are available through the `SparkContext`.

Create the SparkSession, by using the `SparkSession` builder, set the `configs`, and `getOrCreate()`. We need the `SparkContext` for using RDD APIs. `SparkSession` is a higher-level object which was created in the newer versions of Spark as an improvement over the `SparkContext`. However, The `SparkSession` still holds the `SparkContext` and uses it internally.

```python
conf = SparkConf() \
	.setMaster("local[3]") \
	.setAppName("HelloRDD")

spark = SparkSession.builder.config(conf=conf).getOrCreate()
sc = spark.sparkContext
```

For reading a data file, start with the `SparkContext` and read a text file. This method returns an RDD, and each record in the RDD is a line of text from the data file. We are reading a text file here. However, the `SparkContext` comes with a bunch of methods to read a binary file, sequence file, Hadoop file, and object file.

So basically, the data reader APIs in RDD were raw and fundamental. They didn't allow you to work with commonly used files such as CSV, JSON, parquet, and Avro. However, the RDD APIs were extensible to read any kind of file format. But nothing was available out of the box. The DataFrame API comes with a DataFrame Reader interface and out of the box support for a bunch of file formats.

```python
file_name = 'sample_without_header.csv'
linesRDD = sc.textFile(os.path.join('data',file_name))
partitionedRDD = linesRDD.repartition(2)
```

RDDs implemented the notion of Transformations and Actions. The idea of Transformations and Actions are the same as DataFrame for the RDDs. However, RDDs offered only basic transformations such as `map()`, `reduce()`, `filter()`, `foreach()` etc.

Most of the RDD transformations were designed to accept a lambda function and simply apply your custom code to the RDD. So basically, the RDD API leaves all the responsibility in the developer's hand. You need to take care of giving a structure to your data, implement your operations, create an optimized data structure, compress your objects, and a lot of other things.

So the developers are expected to reinvent the wheel. The DataFrame APIs improved the situation by offering some commonly used SQL like operations such as `select()`, `where()`, `groupBy()`, etc.

We don't have a schema or a row/column structure. So the first thing is to give a structure to my data record using `map()` transformation. The `map()` transformation takes a lambda function and calls it in a loop for each line. So in each iteration, we will get a line, and I want to process the line.

The processing is simple. The first thing is to remove the double-quotes. If you look at the data file, I have these double quotes around all the values. And this is a common thing in most of the CSV files.  However, I am reading and processing these lines a plain text, so these double quotes are a problem for me.

Once double quotes are removed, the line is splitted using the comma delimiter. The result is a list of strings. The input was a line of text, and the output is a List of text. We get a new RDD of List where I separated each column i.e. a row-column structure.

```python
colsRDD = partitionedRDD.map(lambda line: line.replace('"', '').split(","))
```

We want to give a name and a data type to each column. Create a `namedtuple`. You can also create a class and use it to define a schema. However, it is more convenient and quick to use a `namedtuple`. Let me name it as `SurveyRecord`. I am creating only four fields. We have a schema now, and use it to give a structure to my RDD.

Now let's process the `colsRDD` using the `map()` method to process each row. So, create a `SurveyRecord` object taking only four columns. The result is another RDD of `SurveyRecord`. So we managed to attach a schema to my RDD and also implemented the select() operation because we selected only four columns.

```python
SurveyRecord = namedtuple("SurveyRecord", ["Age", "Gender", "Country", "State"])
selectRDD = colsRDD.map(lambda cols: SurveyRecord(int(cols[1]), cols[2], cols[3], cols[4]))
```

So, we are filtering all the records where the age is less than forty and group this record by country and count it. So, the first step is to create a key-value pair. The country becomes the key, and the value is a hardcoded value one which generates key/Value RDD.

```python
filteredRDD = selectRDD.filter(lambda r: r.Age < 40)
kvRDD = filteredRDD.map(lambda r: (r.Country, 1))
```

The next step is to use the `reduceByKey()` method and sum up the hardcoded value one. The result is another key/Value RDD where the key is the country, and the value is the sum of all the values for the country. And that's the count. You can collect the result and push it to your log file.

```python
countRDD = kvRDD.reduceByKey(lambda v1, v2: v1 + v2)

colsList = countRDD.collect()
for x in colsList:
	logger.info(x)
```

However, the `groupBy` implementation on an RDD is not so obvious as we needed to hand-code everything, including regular operations such as grouping and aggregating. The Spark Engine had no clue about the data structure inside the RDD. Neither Spark could look inside your lambda functions. And these two things limited Spark to create an optimized execution plan.

```python
import os
from collections import namedtuple

from pyspark import SparkConf
from pyspark.sql import SparkSession

from lib.logger import Log4J

SurveyRecord = namedtuple("SurveyRecord", ["Age", "Gender", "Country", "State"])

if __name__ == "__main__":
    conf = SparkConf() \
		.setMaster("local[3]") \
		.setAppName("HelloRDD")

    spark = SparkSession.builder.config(conf=conf).getOrCreate()
    sc = spark.sparkContext
    logger = Log4J(spark)
  
    file_name = 'sample_without_header.csv'
    linesRDD = sc.textFile(os.path.join('data',file_name))
    partitionedRDD = linesRDD.repartition(2)
  
    colsRDD = partitionedRDD.map(lambda line: line.replace('"', '').split(","))
    selectRDD = colsRDD.map(lambda cols: SurveyRecord(int(cols[1]), cols[2], cols[3], cols[4]))
    filteredRDD = selectRDD.filter(lambda r: r.Age < 40)
    kvRDD = filteredRDD.map(lambda r: (r.Country, 1))
    countRDD = kvRDD.reduceByKey(lambda v1, v2: v1 + v2)

    colsList = countRDD.collect()
    for x in colsList:
        logger.info(x)
```