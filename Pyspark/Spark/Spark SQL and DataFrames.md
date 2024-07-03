# Database working

Spark is a data processing platform. What other data processing platform do you know? Databases. Databases are the most popular and most widely used data processing platforms. They offer two things at a high level.
- Tables
- SQL

A database table allows you to load the data in the table. The data in a table is internally stored as a .dbf file. And these "dbf" files are stored on the disk. But you don't care about the "dbf" file and the storage layer. You only care about the table. You always look at the table and query the table. But what is a table? A table contains two things. Table Schema and Table Data Table schema is a list of column names and data types. The schema information is stored in a database data dictionary or a metadata store. This is how a table is organized.

![[Spark DF 1.png]]

We have three layers to form a table. Storage layer stores the table data in a file, Metadata layer stores the table schema and other important information The Logical layer presents you with a database table, and you can execute SQL queries on the logical table.

![[Spark DF 2.png]]

When you submit a SQL query to your database SQL Engine, the database will refer to the metadata store for parsing your SQL queries and the database will throw a syntax error or an analysis error if you are using a column name in your SQL that does not exist in the metadata store.

Here is an example of SQL. This SQL will return an analysis exception because the column XYZ does not exist in the table. So the schema is essential for the database table and for the SQL expressions to work correctly. The table data is stored in the dbf files behind the table. So if your SQL query is correct and passes all the schema validation, you will see query results. The database will read data from the "dbf" file, process it according to your SQL query, and show you the results.

![[Spark DF 3.png]]

# Apache Spark

Apache Spark offers you two ways of data processing.
- Spark Database and SQL
- Spark DataFrame and DataFrame API

The first approach is precisely the same as a typical database. So you will create table and load data into the table. Spark table data is internally stored in the data files. But these files are not dbf files. Spark gives you the flexibility to choose the file format and supports many file formats such as the following.
- CSV
- JSON
- Parquet
- AVRO
- XML
- and many more.

Spark supports structured, semi-structured, and unstructured data. The DBF file format and database engine were designed to support structured data. But Spark is designed to support a variety of file formats, and hence you can store and process a variety of data formats.

The spark storage layer also supports distributed storage such as HDFS and Cloud storage such as Amazon S3 and Azure ADLS. So you are not limited to disk storage capacity. You can use distributed storage and store large data files. 

Spark also has a metadata store for storing table schema information. So that part is similar to the databases. Then Spark also comes with an SQL query engine and supports standard SQL syntax for processing and querying data from Spark tables. However, Spark goes beyond the Tables and SQL to offer Spark Dataframe and Dataframe API. 

![[Spark DF 4.png]]

So what is a Spark Dataframe? It is the same as the table without a metadata store. Spark Dataframe is structurally the same as the table. However, it does not store any schema information in the metadata store. Instead, we have a runtime metadata catalog to store the dataframe schema information. The catalog is similar to the metadata store, but Spark will create it at the runtime to store schema information in the catalog. This catalog is only valid until your application is running. Spark will delete this catalog when your Spark application terminates.

![[Spark DF 5.png]]

The benefits to not store Dataframe schema in the metadata store and create runtime metadata catalog are
- Spark Dataframe is a runtime object.
	- You can create a Spark Data frame at runtime and keep it in memory until your program terminates. Once your program terminates, your dataframe is gone. It is an in-memory object.
	- Spark tables are permanent. Once created, you will have a table forever. You can drop a table and remove it. But it remains in the system until you drop the table. 
	- However, Spark Dataframe is a runtime and temporary object which lives in Spark memory and goes away when the application terminates. So the metadata is also stored in the temporary metadata catalog.
- Spark Dataframe supports schema-on-read.
	- Spark Dataframe is designed to support the idea of schema-on-read. Dataframe does not have a fixed and predefined schema stored in the metadata store. Instead, schema is defined when the data is read from a file and load it into the Dataframe. 
	- So the difference is straightforward. We define a schema for the table when creating a table. Then we load data into the table. The data must comply with the table schema, or you will get an error. 
	- However, Dataframe is different. We load the data into a Dataframe and tell the schema when loading the data. And Spark will read the file, apply the schema at the time of reading, create the Dataframe using the schema and load the data.
	- So a Dataframe is always loaded with some data, whereas a Table can be empty.

| Spark Table                                                               | Spark DataFrame                                                                                                                          |
| ------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------- |
| Tables store schema information in metadata store                         | Data Frame stores schema information in runtime Catalog                                                                                  |
| Table and metadata are persistent objects and visible across applications | Data Frame and Catalog are runtime objects and live only during the application runtime. Data Frame is visible to your application only. |
| We create tables with a predefined table schema<br>                       | Data Frame supports schema-on-read<br>                                                                                                   |
| Table supports SQL Expressions and does not support API                   | Data Frame offers APIs and does not support SQL expressions                                                                              |
A Spark table and a Dataframe are convertible objects. So you can convert a table into Dataframe and save a Dataframe as a Spark table.

# Create Spark DataFrame using read

```python
fire_df = spark.read.format('csv') \
	.option("header", "true") \
    .option("inferSchema", "true") \
    .load("/databricks-datasets/learning-spark-v2/sf-fire/sf-fire-calls.csv")
```

`spark.read` and then four lines to create a Dataframe. The code will return a dataframe, and is assigned the dataframe to the fire_df variable.

The spark here is a Spark Session object. Spark session is your entry point for the Spark programming APIs. Every Spark program starts with the Spark Session because Spark APIs are available to you via the Spark Session object.  Spark Session object has many methods such as  Builder, Catalog, conf, and many more items.

So spark is a SparkSession object, and all these are either attributes or methods of the spark session object. We want to use the read attribute. The read is an attribute of the spark session object, and that's why we do not use parenthesis after the read attribute.

The read attribute gives you a DataframeReader object. So the first line of code `spark.read` gives you a DataframeReader. The rest of the code, such as format, option, and load, are the methods of the DataframeReader. So what are we doing in the format method?

`format('csv')` tells the DataframeReader that the data file is a CSV file.
`option("header", "true")` tells that the CSV file comes with a header row.
`option("inferSchema", "true")` DataframeReader should infer schema from the file itself.
`load("/databricks-datasets/learning-spark-v2/sf-fire/sf-fire-calls.csv")` load method so the DataframeReader can load the file from the given location.

We get a DataframeReader from the `spark.read`. The rest of the code sets some configuration for the DataframeReader and loads the data. The DataframeReader will use the format and options to load the file, create a Dataframe and return it.

The code is using a builder pattern. So in this code, we use DataframeReader to build a Dataframe step by step. Each method call here represents one step of the DataframeReader.

# Create Global View using DataFrame

```python
fire_df.createGlobalTempView("fire_service_calls_view")
```

So Dataframe is similar to the database table and could be converted from Dataframe into a table. Converting dataframe and creating a global-temporary view for the dataframe. The view name is fire_service_calls_view.

SQL expression can be executed on this view. The global_temp is a hidden database, and all global temporary tables are created in this global_temp database. So you must use global_temp for accessing a global-temporary view.

![[Spark Create DF 1.png]]

# Spark Tables and SQL

Create a database for the project and keep all the project tables inside the newly created database.

```SQL
DROP TABLE IF EXISTS demo_db.fire_service_calls_tbl;
DROP DATABASE IF EXISTS demo_db;
```

```bash
%fs 
rm dbfs:/user/hive/warehouse/demo_db.db/fire_service_calls_tbl -r
```

```SQL
CREATE DATABASE IF NOT EXISTS demo_db
```

```SQL
CREATE TABLE IF NOT EXISTS demo_db.fire_service_calls_tbl(
  CallNumber integer,
  UnitID string,
  IncidentNumber integer,
  CallType string,
  CallDate string,
  WatchDate string,
  CallFinalDisposition string,
  AvailableDtTm string,
  Address string,
  City string,
  Zipcode integer,
  Battalion string,
  StationArea string,
  Box string,
  OrigPriority string,
  Priority string,
  FinalPriority integer,
  ALSUnit boolean,
  CallTypeGroup string,
  NumAlarms integer,
  UnitType string,
  UnitSequenceInCallDispatch integer,
  FirePreventionDistrict string,
  SupervisorDistrict string,
  Neighborhood string,
  Location string,
  RowID string,
  Delay float
) USING parquet
```

The first thing is to truncate the table, so we clean the garbage record that we just inserted. Spark SQL doesn't offer to delete statements. You cannot delete data from a Spark Table using Spark SQL.

Inserting records into the `demo_db.fire_service_calls_tbl` from `global_temp.fire_service_calls_view`. We created this global temporary table earlier

```SQL
INSERT INTO demo_db.fire_service_calls_tbl
SELECT * FROM global_temp.fire_service_calls_view
```

![[Spark SQL 1.png]]

```bash
%fs
ls dbfs:/user/hive/warehouse/demo_db.db/fire_service_calls_tbl
```

![[Spark SQL 2.png]]

You can see Alter, create and drop statements here. You will only see insert statements in the DML statements. So we do not have Delete and Update DML statements in Spark SQL. You have a comprehensive Select expression supported for data retrieval. You can also use these auxiliary statements to describe and show.

[SQL Syntax - Spark 3.5.1 Documentation (apache.org)](https://spark.apache.org/docs/latest/sql-ref-syntax.html)

# Spark DataFrames

Dataframe offers you a long list of methods. However, we can classify them into three logical groups. 
- Actions
	- Actions are DataFrame operations that **kick off a Spark Job** execution and **return to the Spark Driver**.
	- Actions trigger a Spark Job and return to the Spark driver.
	- Spark is a distributed processing system. So it implements a driver/executor architecture. So every Spark program runs as one driver and one or more executors.
- Transformations
	- Spark DataFrame transformation **produces a newly transformed dataframe**.
	- Transformations are different than actions. So they do not kick off a Spark Job, and they do not return to the driver.
	- They simply create a new DataFrame and return it.
- Functions/Methods
	- These are DataFrame methods or functions which are not categorized into Actions or Transformations.
	- So everything other than the Actions and Transformations is a method or function.
## Actions
Spark offers 12 actions as on date.
- collect
- count
- describe
- first
- foreach
- foreachPartition
- head
- show
- summary
- tail
- take
- toLocallterator
## Transformations
Spark offers 38 transformations.
- agg
- alias
- coalesce
- colRegex
- crossJoin
- crosstab
- cube
- distinct
- drop
- drop_duplicates
- dropDuplicates
- dropna
- exceptAll
- filter
- groupby
- intintersect
- intersectAll
- join
- limit
- orderBy
- randomSplit
- repartition
- repartitionByRange
- rollup
- sample
- sampleBy
- select
- selectExpr
- sort
- sortWithinPartitions
- subtract
- transform
- union
- unionAll
- unionByName
- where
- withColumn
- withColumnRenamed

## Functions/Methods
Spark offers 20 utility functions or methods
- approxQuantile
- cache
- checkpoint
- createGlobalTempView
- createOrReplaceGlobalTempView
- createOrReplaceTempView
- createTempView
- explain
- hint
- inputFiles
- isLocal
- localCheckpoint
- persist
- printSchema
- registerTempTable
- toDF
- toJSON
- unpersist
- writeTo
- withWatermark
## Reference
[pyspark.sql.DataFrame — PySpark master documentation (apache.org)](https://spark.apache.org/docs/latest/api/python/reference/pyspark.sql/api/pyspark.sql.DataFrame.html)

# Spark DataFrame Transformations

```python
fire_df = spark.read.format('csv') \
	.option("header", "true") \
    .option("inferSchema", "true") \
    .load("/databricks-datasets/learning-spark-v2/sf-fire/sf-fire-calls.csv")
```

![[Pyspark/images/Spark DF Transform 1.png]]

This dataframe has got two problems.
- Column Names are not standardized
	- Space in a column name is not a common thing. We have spaces in many column names for this dataframe. For example:- There is blank space between call and number  for `Call Number` column name.
- Date fields are of string type
## Standardizing Column Names

Spark Dataframe offers `withColumnRenamed()` transformation to rename a column. We can use the `withColumnRenamed()` transformation to rename the column and remove the space. This method takes two arguments. Old column name and New column name The old column name is with space, and the new column name is without space.

This operation will return a new dataframe transforming the old dataframe. And I will hold the new dataframe in a new variable `remaned_fire_df`. So the `remaned_fire_df` contains all the columns, but the call number column is renamed.

```python
renamed_fire_df = raw_fire_df \
    .withColumnRenamed("Call Number", "CallNumber")
```

![[Spark DF Transform 2.png]]

 We can chain the dataframe transformations one after the other to do many things at once. Now this `remaned_fire_df` contains all old columns, but two of the column names are renamed.

```python
renamed_fire_df = raw_fire_df \
    .withColumnRenamed("Call Number", "CallNumber") \
    .withColumnRenamed("Unit ID", "UnitID") \
    .withColumnRenamed("Incident Number", "IncidentNumber") \
    .withColumnRenamed("Call Date", "CallDate") \
    .withColumnRenamed("Watch Date", "WatchDate") \
    .withColumnRenamed("Call Final Disposition", "CallFinalDisposition") \
    .withColumnRenamed("Available DtTm", "AvailableDtTm") \
    .withColumnRenamed("Zipcode of Incident", "Zipcode") \
    .withColumnRenamed("Station Area", "StationArea") \
    .withColumnRenamed("Final Priority", "FinalPriority") \
    .withColumnRenamed("ALS Unit", "ALSUnit") \
    .withColumnRenamed("Call Type Group", "CallTypeGroup") \
    .withColumnRenamed("Unit sequence in call dispatch", "UnitSequenceInCallDispatch") \
    .withColumnRenamed("Fire Prevention District", "FirePreventionDistrict") \
    .withColumnRenamed("Supervisor District", "SupervisorDistrict")
```

![[Spark DF Transform 3.png]]

Spark Dataframe programming is all about doing two things.
- Read raw data into a dataframe.
- Transform raw dataframe and create a new transformed Dataframe

We performed the first step here to create `raw_fire_df` dataframe. Then we performed the second step here to create `remaned_fire_df`. We can create a chain of Spark transformation methods one after the other.  Spark transformation returns a new dataframe after transforming the old dataframe. 

Spark dataframe is immutable. I mean, you cannot change or modify an existing dataframe. Instead, we transform the existing dataframe and create a new dataframe. So in this code, `raw_fire_df` is the old dataframe. We created a new `remaned_fire_df` dataframe after transformation. The old `raw_fire_df` remains unchanged. You can display the `raw_fire_df`, and you will still see the same old column names with spaces. Spark dataframe is immutable. We can only create a new dataframe from an existing dataframe.
## Convert Data type for Date Fields

You can see `callDate`, `watchDate`, and `AvailableDtTm` fields. The `callDate` and `watchDate` represent a date. And the `AvailableDtTm` represents a timestamp.

The data type information for columns can be fetched using the `printSchema()` utility method.

```python
renamed_fire_df.printSchema()
```

![[Spark DF Transform 4.png]]

Every time date columns are used, we must remember to convert them to date. If that is missed even in one place, there is a bug in your code. Sometimes, it may throw an error, and sometimes you will not see any error. But the result will be wrong. So it is recommend fixing the date column data type once for all.

The `withColumn()` is the most commonly used column transformation method. It can transform one column into another column. The `withColumn` method takes two arguments. We want to convert the `CallDate` to a Date. So we are using the `to_date()` function. The `CallDate` column in the `remaned_fire_df` is represented using `MM/dd/yyyy` string. So the format for the `to_date()` function is `MM/dd/yyyy`.

```python
from pyspark.sql.functions import to_date
fire_df = renamed_fire_df \
    .withColumn("CallDate", to_date("CallDate", "MM/dd/yyyy")) \
    .withColumn("WatchDate", to_date("WatchDate", "MM/dd/yyyy"))
```

The `AvailableDtTm` is a timestamp, converted it to the timestamp field.

```python
from pyspark.sql.functions import to_timestamp
fire_df = renamed_fire_df \
    .withColumn("AvailableDtTm", to_timestamp("AvailableDtTm", "MM/dd/yyyy hh:mm:ss a"))
```

The `Delay` column represents call response delay in minutes. We have six digits in this field. But I wanted to fix the values up to two digits i.e. rounding the `Delay` column for two digits.

```python
from pyspark.sql.functions import round
fire_df = renamed_fire_df \
    .withColumn("Delay", round("Delay", 2))
```

Final Code

```python
from pyspark.sql.functions import to_date, to_timestamp, round
fire_df = renamed_fire_df \
    .withColumn("CallDate", to_date("CallDate", "MM/dd/yyyy")) \
    .withColumn("WatchDate", to_date("WatchDate", "MM/dd/yyyy")) \
    .withColumn("AvailableDtTm", to_timestamp("AvailableDtTm", "MM/dd/yyyy hh:mm:ss a")) \
    .withColumn("Delay", round("Delay", 2))
```

```python
fire_df.printSchema()
```

![[Spark DF Transform 5.png]]

```python
display(fire_df.select('callNumber', 'CallDate', 'WatchDate', 'AvailableDtTm', 'Delay'))
```

![[Spark DF Transform 6.png]]

## Cache DataFrame

However, before I start analysis, lets cache the dataframe. The cache() method is a utility method. It will cache the dataframe into memory. Why do I want to cache this dataframe? It makes sense to cache the dataframe in memory and speed up my analysis execution.

```python
fire_df.cache()
```

The first step is to create a view of your dataframe. The second step is to run SQL queries on the view. The second step will give you a dataframe. And you can take that dataframe for further analysis or simply display it. This is one easy way to use Spark for those who are more comfortable using SQL queries.

```python
fire_df.createOrReplaceTempView( "fire_service_calls_view" )
q1_sql_df = spark.sql("""
    select count(distinct CallType) as distinct_call_type_count
    from fire_service_calls_view
    where CallType is not null
    """)

display(q1_sql_df)
```

![[Spark DF Transform 7.png]]

- `fire_df.where("CallType is not null")` Filter the records and take only those where `CallType` is not null
- `.select("CallType")` Select the `CallType` column
- `.distinct()` Take only distinct call types
- `print(q1_df.count())` Show the count

```python
q1_df = fire_df.where("CallType is not null") \
    .select("CallType") \
    .distinct()

print(q1_df.count())
```

![[Spark DF Transform 8.png]]
### DataFrame Transformations
- The `where()` method takes a filter condition and applies it to the dataframe. The condition string is the same as the SQL condition string.
- The `select()` method takes the column name you want to select and gives you only the selected column.
- The `distinct()` method removes all duplicate rows from the given dataframe.
They are transformations because they return a dataframe, and we can chain one or more transformations together.
### DataFrame Action
- The `count()` method is an action.
- Actions are DataFrame operations that kick off a Spark Job execution and return to the Spark Driver. So an Action doesn't return a dataframe. It returns the result to the driver. 
- The count() method is an action, and it returns the count.
### DataFrame Group By and Count
- A transformation always returns a DataFrame or its variant. So count() is an action. Now look for `groupBy()` method. 
- Groups the DataFrame using the specified columns to run aggregation on them.
- The `groupBy()` returns a `GroupedData` object. It does not return a Dataframe. Instead, it returns the `GroupedData` object. 
- The `GroupedData` is a variant of a Dataframe Object. So `groupBy()` is also a transformation.
- The `count()` method in `GroupedData` counts the number of records for each group and is a transformation.
- There are two count() methods in Spark. `DataFrame.count()` and `GroupedData.count()` If you are using a count before the `groupBy()`, it will be an action, and you cannot chain any other transformation after the `count()` Action. However, if you use a `count()` method after the `groupBy()`, it will be a transformation, and you can chain further transformations.