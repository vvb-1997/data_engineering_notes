We are going to create Avro output. Spark AVRO doesn't come bundled with Apache Spark, so if you want to work with Avro data, then you must include an additional Scala package in your project.

`spark-defaults.conf` file. Let me open.

We used this file to configure some Java System variables. Right? You can use the same file to add some Scala packages. So, this configuration tells that we want some jars to be included in our Spark application dependencies. And this is the jar that we need. It is Spark Avro, for Scala 2.11 and Spark 2.4.5.

```config
spark.jars.packages org.apache.spark:spark-avro_2.11:2.4.5
```

The code will read parquet file and then repartition the dataframe into 5 partitions with equal number of rows. The repartition worked and we got five equal partitions which results in five output files.

```python
spark = SparkSession \
	.builder \
	.master("local[3]") \
	.appName("SparkSchemaDemo") \
	.getOrCreate()

logger = Log4j(spark)

flightTimeParquetDF = spark.read \
	.format("parquet") \
	.load("dataSource/flight*.parquet")

logger.info("Num Partitions before: " + str(flightTimeParquetDF.rdd.getNumPartitions()))
flightTimeParquetDF.groupBy(spark_partition_id()).count().show()

partitionedDF = flightTimeParquetDF.repartition(5)
logger.info("Num Partitions after: " + str(partitionedDF.rdd.getNumPartitions()))
partitionedDF.groupBy(spark_partition_id()).count().show()

partitionedDF.write \
	.format("avro") \
	.mode("overwrite") \
	.option("path", "dataSink/avro/") \
	.save()
```

However, partitioning the data to equal chunks may not make a good sense in most of the cases. We do want to partition our data because we will be working with massive volumes. And partitioning our data might get us two types of direct benefits. Parallel Processing and Partition Elimination for certain read operations. The random but equal partitions will give you parallel processing benefits. However, they do not help in partition elimination. So, you might want to partition your data for specific columns. And that is where you can use the `partitionBy()` method.

Partition the output based on two columns. The first column is the `OP_CARRIER`, and the second column is the `ORIGIN`. The `DataFrameWriter` will partition this DataFrame using these two columns.

Well, repartitioning on a given column requires shuffling all the records. The dataset had ten different flight carriers and we get one directory for each carrier. Now, if we are reading this dataset and want to filter it only for one carrier such as HP. Then your Spark SQL engine can optimize this read and filter operation, ignoring other directories and only reading the HP directory.

Now let's go inside one of these directories. So we have a second level of directories. And this is caused by the second column in the `partitionBy()` method. So, The carrier AA has got these many origins and each origin becomes a subdirectory. If you go inside these directories, then you will see the data file.

Data is partitioned by the unique combination of your partition columns. And these partitions are written down in a separate directory. Each data file in these directories will hold data only for the given combination. For example, this data file contains 1400 plus records, and all these records are for carrier=AA and origin=BOS.

In fact, those two columns are not even recorded in the data file. Because that would be redundant information. If you look at the directory name, these are named as carrier=AA, and origin=BOS and these two columns are removed from the data file.

We played with a small data source of nearly 400K records. After partitioning them on these two columns, I got files that could be of reasonable size, and I am not expecting a too large file here. The largest data file is roughly 4 MB. And this one is created for OP_CAREER=DL and ORIGIN=ATL. 

`maxRecordsPerFile` can be used to control the file size Let me apply that option to limit the file for 10K records only. So, we have one data file with OP_CAREER=DL and ORIGIN=ATL and it contains approximately 19K records. With this new `maxRecordsPerFile` option, we are expecting this file to split. We can see two data files. 

```python
spark = SparkSession \
	.builder \
	.master("local[3]") \
	.appName("SparkSchemaDemo") \
	.getOrCreate()

logger = Log4j(spark)

flightTimeParquetDF = spark.read \
	.format("parquet") \
	.load("dataSource/flight*.parquet")

logger.info("Num Partitions: " + str(flightTimeParquetDF.rdd.getNumPartitions()))
flightTimeParquetDF.groupBy(spark_partition_id()).count().show()

flightTimeParquetDF.write \
	.format("json") \
	.mode("overwrite") \
	.option("path", "dataSink/json/") \
	.partitionBy("OP_CARRIER", "ORIGIN") \
	.option("maxRecordsPerFile", 10000) \
	.save()
```