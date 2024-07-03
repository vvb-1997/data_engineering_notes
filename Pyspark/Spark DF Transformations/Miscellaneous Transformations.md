# Quick method to create Dataframe

Lets create a list with some sample data elements by creating five sample records, each having four fields. The first field is the name, then we have a day, month, and year. Some of the year fields are four-digit year, and others are only two digits. We have a duplicate record also in this sample data set.

```python
data_list = [("Ravi", "28", "1", "2002"),
			 ("Abdul", "23", "5", "81"),  # 1981
			 ("John", "12", "12", "6"),  # 2006
			 ("Rosy", "7", "8", "63"),  # 1963
			 ("Abdul", "23", "5", "81")]  # 1981
```

We will use the `createDataFrame()` method with schema by using `toDF()` method and give a list of column names.

```python
raw_df = spark.createDataFrame(data_list).toDF("name", "day", "month", "year")
raw_df.printSchema()
```

```out
root
|-- name: string (nullable = true)
|-- day: string (nullable = true)
|-- month: string (nullable = true)
|-- year: string (nullable = true)
```
# Adding monotonically increasing id

We want to add one more field in this data frame to create a unique identifier for each record. We can do it using a built-in function `monotonically_increasing_id`. The function generates a monotonically increasing integer number, which is guaranteed to be unique across all partitions.

However, it does not provides a consecutive sequence number. It could be any integer number, but we won't get a duplicate.

Let's add an id field. But before that, let us repartition this data frame and make three partitions. While working on a local machine, we always make sure that we have more than one partition so we can sense a realistic behavior.

```python
data_list = [("Ravi", "28", "1", "2002"),
			 ("Abdul", "23", "5", "81"),  # 1981
			 ("John", "12", "12", "6"),  # 2006
			 ("Rosy", "7", "8", "63"),  # 1963
			 ("Abdul", "23", "5", "81")]  # 1981

raw_df = spark.createDataFrame(data_list).toDF("name", "day", "month", "year").repartition(3)
```

We will create a new data frame and use the `withColumn()` transformation. We can use this method to add a new column in the data frame and transform the column id. Since column id does not exist in the data frame, it will be added. And the value of the column will come from the `monotonically_increasing_id` function.

```python
from pyspark.sql.functions import col, monotonically_increasing_id, when, expr

df1 = raw_df.withColumn("id", monotonically_increasing_id())
df1.show()
```

![[Spark DF Misc Transform 1.png]]
# Using Case When Then transformation

The `Case When Then` is a popular construct in programming languages. It is commonly known as switch case, and we prefer to use it to avoid lengthy if-else statements. It is trendy among SQL developers as well.

We want to use it to fix the year digit problem. We have these two-digit years, and want to make it four-digit. Let's assume that the year between 0 to 20 has an intention to be a post-2000 year. And anything between 21 to 99 is in the previous century.

Lets add one one more `withColumn()` to work with the year field and create an expression using a multi-line string. It is as simple as the case when year < 21 then take year + 2000, when year < 100, then take year + 1900 else take the year without any change. Close the case with the end.

```python
df2 = df1.withColumn("year", expr("""
         case when year < 21 then year + 2000
         when year < 100 then year + 1900
         else year
         end"""))
df2.show()
```

![[Spark DF Misc Transform 2.png]]

The year becomes decimal. This is caused by the incorrect data type and the automatic type promotion. The year field in the data frame is a string. However, we performed an arithmetic operation in the year field. So, the Spark SQL engine automatically promoted it to decimal. And after completing the arithmetic operation, it is again demoted back to string because the data frame schema is for a string field. However, in all this, we still keep the decimal part, and that's how you see a decimal value in the result.
# Casting your columns

Casting the data frame columns is a common requirement. We have a bunch of ways to do it. However, we are particularly interested in two commonly used approaches.
## Cast all the individual columns

The first option is to make sure that the Spark doesn't automatically promote or demote your field types. Instead, we can write code to push it to an appropriate type.

We can cast the year to an int. Now the Spark SQL engine does not need to promote it and cause problems for us. This is the recommended approach for casting your fields.

```python
df3 = df1.withColumn("year", expr("""
         case when year < 21 then cast(year as int) + 2000
         when year < 100 then cast(year as int) + 1900
         else year
         end"""))
df3.show()
```

![[Spark DF Misc Transform 3.png]]
## Cast the resultant column

The `withColumn()` is going to give me a modified year column. we can use the column functions here and cast it to `IntegerType` and this casting will truncate the decimal part. However, since we are converting the column itself so my data frame schema will also change.

```python
df4 = df1.withColumn("year", expr("""
         case when year < 21 then year + 2000
         when year < 100 then year + 1900
         else year
         end""").cast(IntegerType()))
df4.show()
df4.printSchema()
```

![[Spark DF Misc Transform 4.png]]

Lets assume that the initial data frame had an incorrect schema. Now we want to fix the types for the day, month, and year. So, we are going to cast all the three fields. Once we have the appropriate types, we can do all these arithmetic correctly.

```python
df5 = df1.withColumn("day", col("day").cast(IntegerType())) \
         .withColumn("month", col("month").cast(IntegerType())) \
         .withColumn("year", col("year").cast(IntegerType())) 

df6 = df5.withColumn("year", expr("""
          case when year < 21 then year + 2000
          when year < 100 then year + 1900
          else year
          end"""))
df6.show()
```

![[Spark DF Misc Transform 5.png]]

Incorrect types might give you some unexpected results. So, make sure that understand your data frame types and the kind of operations that are allowed. The explicit casing is always a good option to avoid unexpected behavior.

Below is the code for case when then using dataframe API.

```python
df7 = df5.withColumn("year", \
                    when(col("year") < 21, col("year") + 2000) \
                    .when(col("year") < 100, col("year") + 1900) \
                    .otherwise(col("year")))
df7.show()
```
# Adding columns to Dataframe

We want to add one more column combining the day, month, and year by using the `withColumn()`. Create a new field for the date of birth. Create an expression to `concat` all the three fields and a field separator. However, this is going to give us a string. We can use the `to_date()` function to convert string to date

We can use it inside your SQL expression or use it on the column. Both options are valid and work in the same way. So we have the `dob` field.

```python
df8 = df7.withColumn("dob", expr("to_date(concat(day,'/',month,'/',year), 'd/M/y')"))
df8.show()
```

![[Spark DF Misc Transform 6.png]]
# Dropping Columns

However, the three fields, day, month and year are now useless. We can drop them from the data frame

```python
df9 = df7.withColumn("dob", to_date(expr("concat(day,'/',month,'/',year)"), 'd/M/y')) \
         .drop("day", "month", "year")
df9 = df9.show()
```
# Dropping duplicate rows

Drop duplicates using name and `dob` fields.

```python
df9 = df7.withColumn("dob", to_date(expr("concat(day,'/',month,'/',year)"), 'd/M/y')) \
         .drop("day", "month", "year") \
         .dropDuplicates(["name", "dob"]) \
df9.show()
```
# Sorting Dataframe

We can sort the dataframe by `dob` in descending order 

```python
df9 = df7.withColumn("dob", to_date(expr("concat(day,'/',month,'/',year)"), 'd/M/y')) \
         .drop("day", "month", "year") \
         .dropDuplicates(["name", "dob"]) \
         .sort(expr("dob desc"))
df9.show()
```

![[Spark DF Misc Transform 7.png]]