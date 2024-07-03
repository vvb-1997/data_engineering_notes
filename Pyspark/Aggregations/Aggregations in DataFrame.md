Aggregations can be classified into three broad categories.
- Simple Aggregations 
- Grouping Aggregations
- Windowing Aggregations and summarization.
# Simple Aggregations

All aggregations in Spark are implemented via built-in functions, many functions listed under the aggregate functions. These functions include `avg()`, `count()`, `max()`, `min()`, `sum()` and many other things. These aggregate functions are used for simple and grouping aggregations.

You will also find another category for window functions and are used for windowing aggregates. `lead()`, `lag()`, `rank()`, `dense_rank()`, `cume_dist()`

The data file contains the list of invoice line items. So, we have the invoice number, stock code, and stock item description. We also have some other things like Quantity, Date, UnitPrice, CustomerID, and Country. Invoice numbers are repeating, so all these items belong to the same invoice.

Read the data file to a DataFrame and also importing all functions here because aggregation is all about using aggregate and windowing functions.

```python
from pyspark.sql import SparkSession
from pyspark.sql import functions as f

spark = SparkSession \
	.builder \
	.appName("Agg Demo") \
	.master("local[2]") \
	.getOrCreate()

invoice_df = spark.read \
	.format("csv") \
	.option("header", "true") \
	.option("inferSchema", "true") \
	.load("data/invoices.csv")
```

The simplest form of aggregation is to summarize the complete Data Frame, and it is going to give you a single row in the result. For example, we can count the number of records in the DataFrame, and it will return you a single row with the count of records.

Use the `select()` method and apply the `count()` function and we can also give an alias to the summary column. We can also add one more summary column for the `sum()` of the quantity. We can compute the average price and count the unique values of an invoice number using  The `countDistinct()` function which will give you the number of unique invoices in this DataFrame.

```python
invoice_df.select(f.count("*").alias("Count *"),
				  f.sum("Quantity").alias("TotalQuantity"),
				  f.avg("UnitPrice").alias("AvgPrice"),
				  f.countDistinct("InvoiceNo").alias("CountDistinct")
				  ).show()
```

The aggregate functions can also be used them in SQL like string expressions using the `selectExpr()` and can pass in the SQL like expressions.

```python
invoice_df.selectExpr(
	"count(1) as `count 1`",
	"count(StockCode) as `count field`",
	"sum(Quantity) as TotalQuantity",
	"avg(UnitPrice) as AvgPrice"
).show()
```

There is no difference between count() and count(1) because in Spark both work in the same way and give the same answer. However, counting a column field has a difference. The count() and count(1) will count all the rows even if you have null values in all the columns. However, counting a field such as `StockCode` will not count the null values.

The simple aggregation will always give you a one-line summary. There will be scenarios where instead of summarizing them for the whole DataFrame, we want to group them into the country and invoice number. And then apply aggregates.

Spark SQL is an excellent and easy method to run your grouping aggregations.

```python
invoice_df.createOrReplaceTempView("sales")
summary_sql = spark.sql("""
	  SELECT Country, InvoiceNo,
			sum(Quantity) as TotalQuantity,
			round(sum(Quantity*UnitPrice),2) as InvoiceValue
	  FROM sales
	  GROUP BY Country, InvoiceNo""")

summary_sql.show()
```

However, we can do the same using Dataframe expressions also by using the `groupBy()` to group the records. Use the `agg()` transformation as it is specifically designed to take a list of aggregation functions. So the first aggregation is the sum(Quantity) with an alias.

The next aggregation is a little complex. We start with the innermost expression but this is not a single column name. It is an expression. So, we wrap it around the `expr()`. Then, we can take a `sum()`, then `round()` it. And finally, give an `alias()`.

```python
summary_df = invoice_df \
	.groupBy("Country", "InvoiceNo") \
	.agg(f.sum("Quantity").alias("TotalQuantity"),
		 f.round(f.sum(f.expr("Quantity * UnitPrice")), 2).alias("InvoiceValue"),
		 f.expr("round(sum(Quantity * UnitPrice),2) as InvoiceValueExpr")
		 )

summary_df.show()
```
# Grouping Aggregations

We want to group the initial raw DataFrame by two columns `Country` and `Week Number`. We already have a country field in the source DataFrame. However, we do not have the week number field. We can extract the week number from the `InvoiceDate`.

We have a built-in function called `weekofyear()`, which will give us the week number. However, the `weekofyear()` expects a Date field, and `InvoiceDate` is a string in our data frame. So, the first thing is to convert the `InvoiceDate` to a proper date field.

We start with the invoice data frame, and we will be using the `withColumn()` transformation to convert the `InvoiceDate`by applying the `to_date()` function.

The sample data is a little big for aggregating on a single computer, so let us filter it for 2010 only. Now we are ready to group it using `groupBy()` for country and then week number.

Aggregate the data using `agg()` method. The first aggregation is as simple as using the `countDistinct()` for `InvoiceNo` and an alias. Right?

We can build your expression here in the `agg()` method or, we can define this aggregation as a Python variable and use the `val` in the `agg()` method.

Define a variable and initializing it with the expression. Now, we can use this variable in the `agg()`. This approach can make the code more readable. We can define the rest of the aggregations in the similar manner.

```python
invoice_df = spark.read \
	.format("csv") \
	.option("header", "true") \
	.option("inferSchema", "true") \
	.load("data/invoices.csv")

NumInvoices = f.countDistinct("InvoiceNo").alias("NumInvoices")
TotalQuantity = f.sum("Quantity").alias("TotalQuantity")
InvoiceValue = f.expr("round(sum(Quantity * UnitPrice),2) as InvoiceValue")

exSummary_df = invoice_df \
	.withColumn("InvoiceDate", f.to_date(f.col("InvoiceDate"), "dd-MM-yyyy H.mm")) \
	.where("year(InvoiceDate) == 2010") \
	.withColumn("WeekNumber", f.weekofyear(f.col("InvoiceDate"))) \
	.groupBy("Country", "WeekNumber") \
	.agg(NumInvoices, TotalQuantity, InvoiceValue)
```

We want to save this data frame as a parquet file. Set the mode to overwrite and save it to the output directory. However, we want to coalesce() this data frame before saving.

```python
exSummary_df.coalesce(1) \
	.write \
	.format("parquet") \
	.mode("overwrite") \
	.save("output")
```

Lets sort the data using `sort()` and show it.

```python
exSummary_df.sort("Country", "WeekNumber").show()
```
# Windowing Aggregations

We want to compute week by week running total for each country, So the outcome should look like this.

![[Spark Agg 1.png]]

Let's assume we are starting with week 48. So the running total of week 48 is the same as the invoice Value. But for week 49, it is the sum of these two. Similarly, for week 50, it will be the sum of these three. And it goes on in the same way for each country. However, the whole logic restarts when you get a new country.

We cannot achieve this requirement using simple grouping and aggregation. These kinds of aggregates are called window aggregates.

We want to compute running totals for each country. The running total should restart for each country. So the first thing is to make sure that we break this Dataframe by the country. It is like partitioning your data frame by country. Now the next critical thing is to make sure that we order each partition by the week number because we want to compute week by week totals. So the running total for week 50 should be the sum of the week 48+49+50 in the same sequence. So, the order of week number is another critical thing. Now the last thing is to see how to compute the running total. And we can calculate the totals using a sliding window of records.

So, the running-total window starts with one record. We take the total and extend the window to two records. Take the sum once again. Then extend the window to three records. Take the total once again. And this goes on for the entire partition.

This windowing can help you compute a whole new class of aggregations. And using them is a three-step process.
- Identify your partitioning columns. 
	- In our example, it was the country.
- Identify your ordering requirement. 
	- We wanted it to order by week number.
- Define the window start and end.
	- The window starts at the first record and includes everything till the current record.

Let's define our window by creating a `running_total_window`. And we can set it using the `Window` Object. We need three things to define here.
- Partition 
- Ordering
- Window start/end. Right?

Let's define the partition. So we want to `partitionBy` the country. And order by the week number. Finally, we want to consider rows between `unboundedPreceding` and `currentRow`.

```python
from pyspark.sql import SparkSession, Window

running_total_window = Window.partitionBy("Country") \
	.orderBy("WeekNumber") \
	.rowsBetween(Window.unboundedPreceding, Window.currentRow)
```

Computing aggregate over this window is straight. We start with the Dataframe and use the `withColumn()` to add a new column for running total. And the value of the running total is the sum(`InvoiceValue`) over the `running_total_window`.

```python
summary_df.withColumn("RunningTotal",
		  f.sum("InvoiceValue").over(running_total_window)) \
	.show()
```

We used the sum for aggregating over the window. However, we can use any of the available aggregation functions, such as `avg()` or `mean()`. We can also use one of the analytical functions such as `dense_rank()`, `lead()`, and `lag()`, etc.