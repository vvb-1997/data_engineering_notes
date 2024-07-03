# Spark Data Source API

Apache Spark offers a standardized API interface to work with Data Sources. These APIs have a well-defined format. Spark allows to read data using the `DataFrameReader` API. Access the `DataFrameReader` through the SparkSession via the read method.

```python
#General Structure
DataFrameReader
	.format(...)
	.option("key" , "value")
	.schema(...)
	.load()	
```

```python
#Example
spark.read
	.format("csv")
	.option("header", "true")
	.option("path", "/data/mycsvfiles/")
	.option("mode" , "FAILFAST")
	.schema(mySchema)
	.load()
```

Get the `DataFrameReader` using the `spark.read` method where `spark` is the SparkSession variable. We can specify four main things on `DataFrameReader` object.
- Format of the data source
	- Spark `DataFrameReader` reader supports several built-in formats such as CSV, JSON, Parquet, ORC, and JDBC.
	- The `DataFrameReader` API is extended by the community to support hundreds of external data sources. And they offer a separate format for different sources such as Cassandra, MongoDB, XML, HBase, Redshift, and many more.
	- Use the Spark `DataFrameReader` API to read data from any data source, which is supported by Spark or by the community developers. And all of those are going to use the same set of API and the structure.
- Header Options
	- Every data source has a specific set of options to determine how the `DataFrameReader` is going to read the data. The header option in this example is specific to CSV format.
	- For other formats, we must look into the documentation of the data source.
- Path and read mode options
	- Reading data from a source file, especially the semi-structured data sources such as CSV, JSON, and XML, may encounter a corrupt or malformed record.
	- Read modes specify what will happen when Spark comes across a malformed record.  Spark allows three read modes. `PERMISSIVE`, `DROPMALFORMED` and `FAILFAST`. 
	- The `PERMISSIVE` Mode is the default option. This Mode sets all the fields to null when it encounters a corrupted record and places the corrupted records in a string column called `_corrupt_record`.
	- The `DROPMALFORMED` is going to remove the malformed record. That means, you are ignoring the malformed records and only loading the well-formed records. 
	- Finally, the `FAILFAST` raises an exception and terminates immediately upon encountering a malformed record.
- Schema
	- The schema is optional for two reasons. `DataFrameReader` allows you to infer the schema in many cases. So, if you are inferring the schema, then you may not provide an explicit schema.
	- Some data sources such as Parquet and AVRO comes with a well-defined schema inside the data source itself. So, in those cases, you do not need to specify a schema.

Once you are done setting the format, all necessary options, Mode, and schema, you can call the `load()` method to read the data and create a DataFrame. This is a standard structure.

However, like any other tool, `DataFrameReader` also comes with some shortcuts and variations. Instead of using the load method, we used the `csv()` method. However, it is recommend avoiding shortcuts and follow the standard style. Following the standard is going to add to your code maintainability.

```python
#Shortcut example
spark.read
	.option("header", "true")
	.option("mode" , "FAILFAST")
	.schema(mySchema)
	.csv("/data/mycsvfiles/")
```
# Read Data from CSV, JSON, Parquet

```python
from pyspark.sql import SparkSession

spark = SparkSession \
	.builder \
	.master("local[3]") \
	.appName("SparkSchemaDemo") \
	.getOrCreate()
```
## Read CSV File

```python
# Read CSV File with inferSchema
flightTimeCsvDF = spark.read \
	.format("csv") \
	.option("header", "true") \
	.option("inferSchema", "true") \
	.load("data/flight*.csv")

flightTimeCsvDF.show(5)
logger.info(f"CSV Schema: {flightTimeCsvDF.schema.simpleString()}")
```

```python
# Read CSV File with Schema defined
from pyspark.sql.types import StructType, StructField, DateType, StringType, IntegerType

flightSchemaStruct = StructType([
	StructField("FL_DATE", DateType()),
	StructField("OP_CARRIER", StringType()),
	StructField("OP_CARRIER_FL_NUM", IntegerType()),
	StructField("ORIGIN", StringType()),
	StructField("ORIGIN_CITY_NAME", StringType()),
	StructField("DEST", StringType()),
	StructField("DEST_CITY_NAME", StringType()),
	StructField("CRS_DEP_TIME", IntegerType()),
	StructField("DEP_TIME", IntegerType()),
	StructField("WHEELS_ON", IntegerType()),
	StructField("TAXI_IN", IntegerType()),
	StructField("CRS_ARR_TIME", IntegerType()),
	StructField("ARR_TIME", IntegerType()),
	StructField("CANCELLED", IntegerType()),
	StructField("DISTANCE", IntegerType())
])

flightTimeCsvDF = spark.read \
	.format("csv") \
	.option("header", "true") \
	.schema(flightSchemaStruct) \
	.option("mode", "FAILFAST") \
	.option("dateFormat", "M/d/y") \
	.load("data/flight*.csv")
```
## Read JSON File

```python
# Read JSON File with inferSchema
flightTimeJsonDF = spark.read \
	.format("json") \
	.option("inferSchema", "true") \
	.option("dateFormat", "M/d/y") \
	.load("data/flight*.json")

flightTimeJsonDF.show(5)
logger.info("JSON Schema:" + flightTimeJsonDF.schema.simpleString())
```

```python
# Read JSON File with Schema defined
flightSchemaDDL = """FL_DATE DATE, OP_CARRIER STRING, OP_CARRIER_FL_NUM INT, ORIGIN STRING,ORIGIN_CITY_NAME STRING, DEST STRING, DEST_CITY_NAME STRING, CRS_DEP_TIME INT, DEP_TIME INT, WHEELS_ON INT, TAXI_IN INT, CRS_ARR_TIME INT, ARR_TIME INT, CANCELLED INT, DISTANCE INT"""

flightTimeJsonDF = spark.read \
	.format("json") \
	.schema(flightSchemaDDL) \
	.option("dateFormat", "M/d/y") \
	.load("data/flight*.json")
```
## Read Parquet File

```python
# Read parquet file
flightTimeParquetDF = spark.read \
	.format("parquet") \
	.load("data/flight*.parquet")

flightTimeParquetDF.show(5)
logger.info("Parquet Schema:" + flightTimeParquetDF.schema.simpleString())
```
# DataFrame Schema

DataFrame schema is all about setting the column name and appropriate data types. However, there are Spark supported data types. Apache Spark comes with its own data types. However, you can find the full list in the `org.apache.spark.sql.types` package.

![[Spark Data Source 1.png]]

If you are using Scala, they simply correspond to their equivalent Scala Types. In Python, they correspond to equivalent Python types. However, we define Spark DataFrame schema using the Spark Types.

Spark maintains its own types because Spark is like a compiler. It complies with the high-level API code into low-level RDD operations. And during this compilation process, it generated different execution plans and also performed a bunch of optimizations. This all is not at all possible for the Spark engine without maintaining its own type of information.

And this approach is not new. Every SQL database would have a set of SQL data types.  Similarly, Spark also works on Spark data types.

Spark allows you to define Schema in two ways.
- Programmatically
- Using DDL String
## Programmatic method.

A Spark DataFrame Schema is a `StructType` which is made up of a list of `StructField`. The `StructField` takes two mandatory arguments. The first one is a column name, and the second one is the data type. You must be using one of the Spark Data Types.

The `StructType` represents a DataFrame row structure, and the `StructField` is a column definition. Now we can use this Schema to load your CSF file to a DataFrame and comment out the `inferSchema` option.

```python
flightSchemaStruct = StructType([
	StructField("FL_DATE", DateType()),
	StructField("OP_CARRIER", StringType()),
	StructField("OP_CARRIER_FL_NUM", IntegerType()),
	StructField("ORIGIN", StringType()),
	StructField("ORIGIN_CITY_NAME", StringType()),
	StructField("DEST", StringType()),
	StructField("DEST_CITY_NAME", StringType()),
	StructField("CRS_DEP_TIME", IntegerType()),
	StructField("DEP_TIME", IntegerType()),
	StructField("WHEELS_ON", IntegerType()),
	StructField("TAXI_IN", IntegerType()),
	StructField("CRS_ARR_TIME", IntegerType()),
	StructField("ARR_TIME", IntegerType()),
	StructField("CANCELLED", IntegerType()),
	StructField("DISTANCE", IntegerType())
])
```
## DDL string

The Schema DDL is straightforward. All you need is the column name and data type separated by a comma. And you can use this Schema in the same way as a Programmatically defined schema.

```python
flightSchemaDDL = """FL_DATE DATE, OP_CARRIER STRING, OP_CARRIER_FL_NUM INT, ORIGIN STRING, ORIGIN_CITY_NAME STRING, DEST STRING, DEST_CITY_NAME STRING, CRS_DEP_TIME INT, DEP_TIME INT, WHEELS_ON INT, TAXI_IN INT, CRS_ARR_TIME INT, ARR_TIME INT, CANCELLED INT, DISTANCE INT"""
```
# Reference

[CSV Files - Spark 3.5.1 Documentation (apache.org)](https://spark.apache.org/docs/3.5.1/sql-data-sources-csv.html)
[Data Types — PySpark master documentation (apache.org)](https://spark.apache.org/docs/latest/api/python/reference/pyspark.sql/data_types.html)
