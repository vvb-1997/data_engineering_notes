In Spark, we read the data and create one of the two things.
- DataFrames
- Database Tables

Both of these are the same but two different interfaces. The DataFrame is the programmatic interface for the data, and the database table is the SQL interface of the data.

Similarly for transformations, we have two approaches. A programmatic approach that we apply on DataFrames and SQL expressions that we can use on the Database Tables.

![[Spark DF Transform 1 1.png]]

What are different types of Transformations
- Combining one or more DataFrames using operations such as Join and Union
- Aggregating and summarizing DataFrames using operations such as grouping, windowing, and rollups
- Applying functions and built-in transformations on the DataFrame such as filtering, sorting, splitting, sampling, and finding unique.
- Using and implementing built-in functions, column-level functions, and user-defined functions 
- Referencing Rows/Columns and creating custom expressions
# DataFrame Row Transformation

Spark DataFrame is a Dataset \[Row\]. And each row in a DataFrame is a single record represented by an object of type Row.

![[Spark DF Row Transform 1.png]]

Most of the time, we do not directly work with the entire row. However, there are three specific scenarios when we might have to work with the Row object.
- Manually creating Rows and DataFrame.
- Collecting DataFrame rows to the driver.
- Work with an individual row in Spark Transformations.
## Manually creating Rows and DataFrame.

The function `to_date_df` takes a DataFrame, date format string, and a field name. Then it simply returns a new DataFrame converting the type of the field using a given date format.

```python
def to_date_df(df, fmt, fld):
    return df.withColumn(fld, to_date(fld, fmt))
```

The data type of the `EventDate` is a string, which is converted to a `DateType` in the output DataFrame.

![[Spark DF Row Transform 2.png]]

For testing the function we can create a One approach is to create a data file with some sample data. Then you can load it here to create your DataFrame and use that DataFrame to test the function. But this approach has following problems.
- A large project might need hundreds of small sample data files to test the functions. This could be difficult to manage over some time.
- The build pipeline might run slow due to loading hundreds of sample files and increased I/O.

Hence we can create a DataFrame on the fly. Lets create the DataFrame schema and list of row objects. However the list is not distributed. It is a single list with four records. So, we are going to convert it to an RDD of two parallel partitions and use this RDD and the Schema to create a DataFrame.

```python
from pyspark.sql import *
from pyspark.sql.functions import *
from pyspark.sql.types import *

spark = SparkSession \
	.builder \
	.master("local[3]") \
	.appName("RowDemo") \
	.getOrCreate()

my_schema = StructType([
	StructField("ID", StringType()),
	StructField("EventDate", StringType())])

my_rows = [Row("123", "04/05/2020"), Row("124", "4/5/2020"), Row("125", "04/5/2020"), Row("126", "4/05/2020")]
my_rdd = spark.sparkContext.parallelize(my_rows, 2)
my_df = spark.createDataFrame(my_rdd, my_schema)
```

Now lets test the function. Before DataFrame shows a `String` field and after DataFrame shows a `DataType` field.

```python
my_df.printSchema()
my_df.show()
new_df = to_date_df(my_df, "M/d/y", "EventDate")
new_df.printSchema()
new_df.show()
```

The above test was manual verification. We can convert it to a automated Unit test. Let's create a new Python file which will contain test cases by creating a `TestCase` using the Python unit test framework .We can create a class and inherit the `TestCase`.

```python
from datetime import date
from unittest import TestCase

from pyspark.sql import *
from pyspark.sql.types import *

from RowDemo import to_date_df
```
## Collecting DataFrame rows to the driver.

For unit testing the code, we will be setting up the Spark Session and DataFrame by creating a class method `setUpClass`. We also need a schema and a list of rows. Then we create an RDD and convert it to a DataFrame.

```python
class RowDemoTestCase(TestCase):

    @classmethod
    def setUpClass(cls) -> None:
        cls.spark = SparkSession.builder \
            .master("local[3]") \
            .appName("RowDemoTest") \
            .getOrCreate()

        my_schema = StructType([
            StructField("ID", StringType()),
            StructField("EventDate", StringType())])

        my_rows = [Row("123", "04/05/2020"), Row("124", "4/5/2020"), Row("125", "04/5/2020"), Row("126", "4/05/2020")]
        my_rdd = cls.spark.sparkContext.parallelize(my_rows, 2)
        cls.my_df = cls.spark.createDataFrame(my_rdd, my_schema)
```

Now we have a Spark Session, and a DataFrame that we can use in all the test cases. we will create two test cases, In the first test case we will be validating the data type. And in the second test case, I am going to validate the data itself.

Let me create the first test case. We are going to call the function which in turn return a DataFrame. However, the actual data of a DataFrame is sitting at the executor The DataFrame is just a reference to the real data and hence we cannot assert the DataFrame. If we want to validate the data, then we must bring it to the driver.

We can collect the rows using `collect()` method will return List \[Row\] to your driver. Once the data is at the driver, we can assert it. Then we can iterate over the rows. Each iteration will give a Row, and we are going to assert the Row by validating it for an `isInstanceOf[Date]`. We are expecting the function to return a DataFrame where the `EventDate` is converted to the `DateType`.

```python
def test_data_type(self):
	rows = to_date_df(self.my_df, "M/d/y", "EventDate").collect()
	for row in rows:
		self.assertIsInstance(row["EventDate"], date)
```

For second test case, we are going to call the function and collect it as a List of Row. We will again loop through the List and assert each record for data value

```python
def test_date_value(self):
	rows = to_date_df(self.my_df, "M/d/y", "EventDate").collect()
	for row in rows:
		self.assertEqual(row["EventDate"], date(2020, 4, 5))
```

## Work with an individual row in Spark Transformations.

Spark DataFrame offers a bunch of Transformations functions and these methods can be used when the DataFrame has got a schema. 

![[Spark DF Row Transform 3.png]]

When the DataFrame doesn't have a column structure, we will take an extra step to create a columnar structure and then use the transformation functions. In those cases, we may have to start working with the row only and then transform it into a columnar structure.

The data file is an apache web server log file. Each line in this file is one log entry. We do have some patterns in the log output. However, this file is not even a semi-structured data file. It is just a log dump. 

```log
83.149.9.216 - - [17/May/2015:10:05:03 +0000] "GET /presentations/logstash-monitorama-2013/images/kibana-search.png HTTP/1.1" 200 203023 "http://semicomplete.com/presentations/logstash-monitorama-2013/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/32.0.1700.77 Safari/537.36"
83.149.9.216 - - [17/May/2015:10:05:43 +0000] "GET /presentations/logstash-monitorama-2013/images/kibana-dashboard3.png HTTP/1.1" 200 171717 "http://semicomplete.com/presentations/logstash-monitorama-2013/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/32.0.1700.77 Safari/537.36"
83.149.9.216 - - [17/May/2015:10:05:47 +0000] "GET /presentations/logstash-monitorama-2013/plugin/highlight/highlight.js HTTP/1.1" 200 26185 "http://semicomplete.com/presentations/logstash-monitorama-2013/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/32.0.1700.77 Safari/537.36"
83.149.9.216 - - [17/May/2015:10:05:12 +0000] "GET /presentations/logstash-monitorama-2013/plugin/zoom-js/zoom.js HTTP/1.1" 200 7697 "http://semicomplete.com/presentations/logstash-monitorama-2013/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/32.0.1700.77 Safari/537.36"
```

So if we read this file in a DataFrame, all we are going to get is a Row of strings. We cannot have columns because the data file is an unstructured data file. We can parse this data file and extract some fields. the text file gives us a DataFrame \[String\].

```python
file_df = spark.read.text("data/apache_logs.txt")
file_df.printSchema()
```

```out
root
|-- value: string (nullable = true)
```

The DataFrame Row has got only one string field named value and no other columns and schema. We cannot use many of the higher level transformations such as aggregation and grouping.

We need to find a way to extract some well-defined fields from the data by using regular expressions. Every entry in this log follows a standard apache log file format. It comes with the following information.
1. IP
2. client
3. user
4. datetime
5. cmd
6. request
7. protocol
8. status
9. bytes
10. referrer
11. userAgent

We can extract all these fields using the below regular expression. This regex is going to match against eleven elements in a given string. And we will be using this regex to extract the fields.

```python
log_reg = r'^(\S+) (\S+) (\S+) \[([\w:/]+\s[+\-]\d{4})\] "(\S+) (\S+) (\S+)" (\d{3}) (\S+) "(\S+)" "([^"]*)'
```

We start with the DataFrame and use the `select()` method with the `regexp_extract()` function. This function takes three arguments. 
- The first argument is the field name or the source string.
- The second argument is the regular expression.
- The third is the group number for which we are interested.

```python
logs_df = file_df.select(regexp_extract('value', log_reg, 1).alias('ip'),
						 regexp_extract('value', log_reg, 4).alias('date'),
						 regexp_extract('value', log_reg, 6).alias('request'),
						 regexp_extract('value', log_reg, 10).alias('referrer'))
```

We created a new DataFrame. This new DataFrame has got four fields.

```out
root
|-- ip: string (nullable = true)
|-- date: string (nullable = true)
|-- request: string (nullable = true)
|-- referrer: string (nullable = true)
```

Now it is quite simple to perform some kind of analysis on this dataframe. We can easily group by the referrer, take a count, and show it.

```python
logs_df.groupBy("referrer") \
	.count() \
	.show(100, truncate=False)
```

So this count doesn't make a good sense as there are three URLs are the same referrer. However, they are aggregating as three different websites.

```text
http://www.semicomplete.com/resume.xml
http://www.semicomplete.com/projects/pmbackup/
http://www.semicomplete.com/blog/text.html
```

We will transform this referrer column taking only the website's home URL. 
```python
logs_df.withColumn("referrer", substring_index("referrer", "/", 3)) \
	groupBy("referrer") \
	.count() \
	.show(100, truncate=False)
```

There are 4K plus hits that are not coming from any referrer. If you want, you can filter them out.
```python
logs_df \
	.where("trim(referrer) != '-' ") \
	.withColumn("referrer", substring_index("referrer", "/", 3)) \
	.groupBy("referrer") \
	.count() \
	.show(100, truncate=False)
```

Dataframe APIs can be used to operate on unstructured data as well. However, before using most of the DataFrame transformations, we may have to give an appropriate schema to the unstructured data frame.
# DataFrame Column Transformation

Spark DataFrame columns are objects of type Column. However, they do not make any sense outside the context of a DataFrame, and we cannot manipulate then independently. Columns are always used within a Spark Transformation. There are two ways to refer to columns in a DataFrame Transformation.
- Column String
	- Spark offers a bunch of DataFrame transformations that are designed to accept column strings. Can be used in `select()`, `drop()`, `orderBy()`, `groupBy()`, `cube()`, `rollup()` and few more.
	- Example: `airlinesDF.select("Origin", "Dest", "Distance").show(10)`
- Column Object
	- The most common method is to use the `column()` or the `col()` function.
	- Three different methods to reference a column 
	 `airlinesDF.select(column("Origin"), col("Dest"), airlinesDF.Distance).show(10)`

![[Spark DF Column Transform 1.png]]

Spark DataFrame offers multiple ways to create column expressions. However, you can again group them into two types.
- String Expressions or SQL Expressions
- Column Object Expressions
## String Expressions or SQL Expressions

When we want to combine multiple fields and create a single field for the date of the flight we can easily create an expression in SQL. However, this is an expression string and not a plain column name string and will give an error because the select method accepts column strings or column objects they do not take expressions.

```python
airlinesDF.select("Origin", "Dest", "Distance", "to_date(concat(Year,Month,DayofMonth),'yyyyMMdd') as FlightDate").show(10)
```

We can use the `expr()` function to convert an expression to a column object. Now, `expr()` function is going to parse the expression and return a column object.

```python
airlinesDF.select("Origin", "Dest", "Distance", expr("to_date(concat(Year,Month,DayofMonth),'yyyyMMdd') as FlightDate")).show(10)
```
## Column Object Expressions

The second method is to use column objects and build your expression. The only difference is to avoid using strings and apply column objects and functions.

Remove the `expr()` function and we have a `to_date()`, and the `concat()` functions that we can directly use. However, these functions do not understand the column names they take name strings. The `concat()` function is going to combine these three columns and return a new column. The new column goes to the `to_date()` function which returns a Date column. We can apply the `alias()` function to rename it. 

```python
airlinesDF.select("Origin", "Dest", "Distance", to_date(concat("Year","Month","DayofMonth"),"yyyyMMdd").alias("FlightDate")).show(10)
```

[pyspark.sql.Column — PySpark master documentation (apache.org)](https://spark.apache.org/docs/3.5.1/api/python/reference/pyspark.sql/api/pyspark.sql.Column.html)
