However, we can execute SQL expression only on a table or view. Spark allows you to register your DataFrame as a View. We start with the DataFrame and `createOrReplaceTempView`. Supply the view name.

Now we have a view, and you can execute your SQL expressions against this view. We start with the `SparkSession` and use the `sql` method. The `sql` method takes your Spark SQL expression and returns a DataFrame.

All that we did in the earlier examples using multiple lines is done using a single SQL expression. If you know SQL, this approach could be easy to learn and the fastest method to meet your requirements. And the best part, Spark SQL, is as performant as the DataFrames. So, using SQL, you are not going to pay any additional performance cost.

```python
import os
from pyspark.sql import SparkSession
from lib.logger import Log4J

if __name__ == "__main__":
    spark = SparkSession \
        .builder \
        .master("local[3]") \
        .appName("HelloSparkSQL") \
        .getOrCreate()

    logger = Log4J(spark)

    file_name = 'sample.csv'
    surveyDF = spark.read \
        .option("header", "true") \
        .option("inferSchema", "true") \
        .csv(os.path.join('data',file_name))

    surveyDF.createOrReplaceTempView("survey_tbl")
    countDF = spark.sql("select Country, count(1) as count from survey_tbl where Age<40 group by Country")

    countDF.show()
```
