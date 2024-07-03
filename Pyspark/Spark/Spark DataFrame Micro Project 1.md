
Data Set: Fire Department Calls for Service
URL: https://data.sfgov.org/Public-Safety/Fire-Department-Calls-for-Service/nuek-vuh3
Databricks: /databricks-datasets/learning-spark-v2/sf-fire/sf-fire-calls.csv

What are the requirements
1. Load the given data file and create a Spark data frame.
2. Use the Spark data frame to answer the following questions.
	1. How many distinct types of calls were made to the fire department?
	2. What are distinct types of calls made to the fire department?
	3. Find out all responses or delayed times greater than 5 mins?
	4. What were the most common call types?
	5. What zip codes accounted for the most common calls?
	6. What San Francisco neighborhoods are in the zip codes 94102 and 94103
	7. What was the sum of all calls, average, min, and max of the call response times?
	8. How many distinct years of data are in the CSV file?
	9. What week of the year in 2018 had the most fire calls?
	10. What neighborhoods in San Francisco had the worst response time in 2018?

What will you do?
1. How to load data into Spark
2. How to query data in Spark

Two Approaches
1. Using spark SQL
2. Using Spark data frame API

# Spark SQL

[Databricks Spark Notebook](https://community.cloud.databricks.com/?o=7951261193684502#notebook/452335050402042/command/4475405779686777)
## Load Data using Spark API

```python
fire_df = spark.read.format('csv') \
	.option("header", "true") \
    .option("inferSchema", "true") \
    .load("/databricks-datasets/learning-spark-v2/sf-fire/sf-fire-calls.csv")
```
## Create Global Temporary View

```python
fire_df.createGlobalTempView("fire_service_calls_view")
```
## Run SQL code to fetch data from View

```SQL
SELECT * FROM global_temp.fire_service_calls_view
```
## Describe the View

```SQL
DESCRIBE TABLE global_temp.fire_service_calls_view
```
# Create Spark Table

[Databricks Notebook](https://community.cloud.databricks.com/?o=7951261193684502#notebook/4475405779686779/command/4475405779686781)

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

```SQL
INSERT INTO demo_db.fire_service_calls_tbl
SELECT * FROM global_temp.fire_service_calls_view
```

# Spark SQL Solution

[03_spark_sql_demo - Databricks Community Edition](https://community.cloud.databricks.com/?o=7951261193684502#notebook/4475405779686792/command/4475405779686793)
## How many distinct types of calls were made to the fire department?

```SQL
SELECT COUNT(DISTINCT CallType) AS distinct_call_type_count
FROM demo_db.fire_service_calls_tbl
WHERE CallType IS NOT NULL
```

![[Spark MP1 1.png]]
## What are distinct types of calls made to the fire department?

```SQL
SELECT DISTINCT CallType AS distinct_call_types
FROM demo_db.fire_service_calls_tbl
WHERE CallType IS NOT NULL
```

![[Spark MP1 2.png]]
## Find out all responses or delayed times greater than 5 mins?

```SQL
SELECT CallNumber, Delay
FROM demo_db.fire_service_calls_tbl
WHERE Delay > 5
```

![[Spark MP1 3.png]]
## What were the most common call types?

```SQL
SELECT CallType AS most_common_call_type, COUNT(*) AS call_type_count
FROM demo_db.fire_service_calls_tbl
WHERE CallType IS NOT NULL
GROUP BY CallType
ORDER BY call_type_count DESC
```

![[Spark MP1 4.png]]
## What zip codes accounted for the most common calls?

```SQL
SELECT Zipcode AS most_common_zip_code, COUNT(*) AS zip_code_count
FROM demo_db.fire_service_calls_tbl
WHERE CallType IS NOT NULL
GROUP BY Zipcode
ORDER BY zip_code_count DESC
```

![[Spark MP1 5.png]]
## What San Francisco neighborhoods are in the zip codes 94102 and 94103

```SQL
SELECT DISTINCT Neighborhood
FROM demo_db.fire_service_calls_tbl
WHERE Zipcode IN (94102, 94103)
```

![[Spark MP1 6.png]]
## What was the sum of all calls, average, min, and max of the call response times?

```SQL
SELECT
  sum(NumAlarms) AS total_alerts,
  avg(Delay) AS avg_response,
  min(Delay) AS min_response,
  max(Delay) AS max_response
FROM demo_db.fire_service_calls_tbl
```

![[Spark MP1 7.png]]
## How many distinct years of data are in the CSV file?

```SQL
SELECT DISTINCT year(to_date(CallDate, "yyyy-MM-dd")) AS distinct_years
FROM demo_db.fire_service_calls_tbl
ORDER BY distinct_years
```

![[Spark MP1 8.png]]
## What week of the year in 2018 had the most fire calls?

```SQL
SELECT 
	weekofyear(to_date(CallDate, "yyyy-MM-dd")) AS week_year,
	COUNT(*) AS most_fire_call
FROM demo_db.fire_service_calls_tbl
WHERE year(to_date(CallDate, "yyyy-MM-dd")) = 2018
GROUP BY week_year
ORDER BY most_fire_call DESC
```

![[Spark MP1 9.png]]
## What neighborhoods in San Francisco had the worst response time in 2018?

```SQL
SELECT Neighborhood, Delay
FROM demo_db.fire_service_calls_tbl
WHERE year(to_date(CallDate, "yyyy-MM-dd")) = 2018
ORDER BY Delay DESC
```

![[Spark MP1 10.png]]

# Spark DataFrame Solution

## Load Data using Spark API

[05_working_with_dataframes - Databricks Community Edition](https://community.cloud.databricks.com/?o=7951261193684502#notebook/4301427077688906)

```python
fire_df.cache()
```
## How many distinct types of calls were made to the fire department?

```SQL
SELECT COUNT(DISTINCT CallType) AS distinct_call_type_count
FROM demo_db.fire_service_calls_tbl
WHERE CallType IS NOT NULL
```

```python
q1_df = fire_df.where("CallType is not null") \
    .select("CallType") \
    .distinct()

print(q1_df.count())
```

![[Spark DF Transform 8.png]]
## What are distinct types of calls made to the fire department?

```SQL
SELECT DISTINCT CallType AS distinct_call_types
FROM demo_db.fire_service_calls_tbl
WHERE CallType IS NOT NULL
```

```python
from pyspark.sql.functions import expr
q2_df = fire_df.where("CallType is not null") \
    .select(expr("CallType AS distinct_call_types")) \
    .distinct()

display(q2_df)
```

![[Spark DF Transform 9.png]]
## Find out all responses or delayed times greater than 5 mins?

```SQL
SELECT CallNumber, Delay
FROM demo_db.fire_service_calls_tbl
WHERE Delay > 5
```

```python
q3_df = fire_df.where("Delay > 5") \
  .select("CallNumber", "Delay")

display(q3_df)
```

![[Spark DF Transform 10.png]]
## What were the most common call types?

```SQL
SELECT CallType AS most_common_call_type, COUNT(*) AS call_type_count
FROM demo_db.fire_service_calls_tbl
WHERE CallType IS NOT NULL
GROUP BY CallType
ORDER BY call_type_count DESC
```

Filter the records where Call Type is not null → `where("CallType is not null")`
Select Call Type → `select("CallType")`
Group them by Call Type → `groupBy("CallType")`
Count the grouped Data Frame → `count()`
Sort it by count in descending order → `orderBy("count", ascending=False)`
Show the results → `show()`

```python
fire_df.where("CallType is not null") \
    .select("CallType") \
    .groupBy("CallType") \
    .count() \
    .orderBy("count", ascending=False) \
    .show(truncate=False)
```

![[Spark DF Transform 11.png]]
## What zip codes accounted for the most common calls?

```SQL
SELECT Zipcode AS most_common_zip_code, COUNT(*) AS zip_code_count
FROM demo_db.fire_service_calls_tbl
WHERE CallType IS NOT NULL
GROUP BY Zipcode
ORDER BY zip_code_count DESC
```

```python
fire_df.where("CallType is not null") \
  .select(expr("Zipcode AS most_common_zip_code")) \
  .groupBy("most_common_zip_code") \
  .count().withColumnRenamed("count","zip_code_count") \
  .orderBy("zip_code_count", ascending=False) \
  .show(truncate=False)
```

![[Spark DF Transform 12.png]]
## What San Francisco neighborhoods are in the zip codes 94102 and 94103

```SQL
SELECT DISTINCT Neighborhood
FROM demo_db.fire_service_calls_tbl
WHERE Zipcode IN (94102, 94103)
```

```python
fire_df.where("Zipcode IN (94102, 94103)") \
    .select("Neighborhood") \
    .distinct() \
    .show(truncate=False)
```

![[Spark DF Transform 13.png]]
## What was the sum of all calls, average, min, and max of the call response times?

```SQL
SELECT
  sum(NumAlarms) AS total_alerts,
  avg(Delay) AS avg_response,
  min(Delay) AS min_response,
  max(Delay) AS max_response
FROM demo_db.fire_service_calls_tbl
```

```python
from pyspark.sql.functions import sum, avg, min, max

fire_df.select(
        sum("NumAlarms").alias("total_alerts"),
        avg("Delay").alias("avg_response"),
        min("Delay").alias("min_response"),
        max("Delay").alias("max_response"),
    ).show(truncate=False)
```

![[Spark DF Transform 14.png]]
## How many distinct years of data are in the CSV file?

```SQL
SELECT DISTINCT year(to_date(CallDate, "yyyy-MM-dd")) AS distinct_years
FROM demo_db.fire_service_calls_tbl
ORDER BY distinct_years
```

```python
from pyspark.sql.functions import year

fire_df.select(year("CallDate").alias("distinct_years")) \
    .distinct() \
    .orderBy("distinct_years") \
    .show(truncate=False)
```

![[Spark DF Transform 15.png]]
## What week of the year in 2018 had the most fire calls?

```SQL
SELECT 
	weekofyear(to_date(CallDate, "yyyy-MM-dd")) AS week_year,
	COUNT(*) AS most_fire_call
FROM demo_db.fire_service_calls_tbl
WHERE year(to_date(CallDate, "yyyy-MM-dd")) = 2018
GROUP BY week_year
ORDER BY most_fire_call DESC
```

```python
from pyspark.sql.functions import weekofyear

fire_df.where(year("CallDate") == 2018) \
    .select(weekofyear("CallDate").alias("week_year")) \
    .groupBy("week_year") \
    .count().withColumnRenamed("count", "most_fire_call") \
    .orderBy("most_fire_call", ascending=False) \
    .show(truncate=False)
```

![[Spark DF Transform 16.png]]
## What neighborhoods in San Francisco had the worst response time in 2018?

```SQL
SELECT Neighborhood, Delay
FROM demo_db.fire_service_calls_tbl
WHERE year(to_date(CallDate, "yyyy-MM-dd")) = 2018
ORDER BY Delay DESC
```

```python
fire_df.where(year("CallDate") == 2018) \
    .select("Neighborhood", "Delay") \
    .orderBy("Delay", ascending = False) \
    .show(truncate=False)
```

![[Spark DF Transform 17.png]]

