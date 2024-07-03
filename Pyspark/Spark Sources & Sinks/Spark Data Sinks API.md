# Spark Data Sinks API

The `DataFrameWriter` is a standardized API to work with a variety of internal and external data sources.

```python
#General Structure
DataFrameWriter
	.format(...)
	.option(...)
	.partitionBy(...)
	.bucketBy(...))
	.sortBy(...)
	.save()
```

```python
#Example
DataFrame.write
	.format("parquet")
	.mode(saveMode)
	.option("path", "/data/flights/")
	.save()
```

Get the `DataFrameWriter` using the write method on any of your DataFrame. Once you have the `DataFrameWriter`, you can specify four main things.
- Output Format
	- Spark supports a bunch of internal file formats such as CSV, JSON, Parquet, AVRO, ORC, etc. However, Parquet is the default file format. `DataFrameWriter` assumes Parquet when you do not specify the format.
	- These are the internal Spark file-formats bundled with your Spark. However, you also have a bunch of formats offered by the community developers and third-party vendors.
	- Some of the popular ones are JDBC, Cassandra, MongoDB, Kafka, and Delta Lake.
- Target options. 
	- Every data sink has a specific set of options to determine how the `DataFrameWriter` is going to write the data. However, at a minimum, you must specify the target of your DataFrame.
	- For a file-based data sink, such as Parquet, it should be a directory location.
- Save mode.
	- Save modes specifies what will happen when Spark existing finds data at the specified location. We have four valid modes. `append`, `overwrite`, `errorIfExists`, `ignore`
	- The append mode will create new files without touching the existing data at the given location.
	- The `overwrite` will remove the existing data files and create new files
	- `errorIfExists` will throw an error when you already have some data at the specified location
	- The `ignore` will write the data if and only if the target directory is empty and do nothing if some files already exist at the location.
- Layout of output data.
	- Control the following items. 
		- Number of files and file size
		- Organizing your output in partitions and buckets
		- Storing sorted data
	- DataFrames are partitioned. So, when DataFrame are written to the file system, we get one output file per partition. That's the default behavior, and it is perfectly fine because each partition is written by an executor core in parallel.
	- We can tweak the default behavior in bunch of ways. The simplest option is to repartition the DataFrame before you write it.
	- We have three different ways to repartition your data to write it to the sink.
		- The first option is to do the Simple Repartitioning using `DataFrame.repartition()` transformation. This transformation will repartition your data, and you can control the number of output files. However, this could be a blind repartitioning and is not going to help you in most of the situations.
		- The second option is to use a `partitionBy()` method. The `partitionBy()` method will repartition your data based on a key column. You can use a single column key such as country code, or you can use a composite column key such as country code plus state code. Key-based partitioning is a powerful method to break your data logically. It also helps to improve your Spark SQL performance using the partition pruning technique.
		- The third option is to partition data into a fixed number of predefined buckets. And this is known as bucketing. You can use the `bucketBy()` method. However, the `bucketBy()` is only available on Spark managed tables.

We have two more suitable options `sortBy()` and `maxRecordsPerFile`. The `sortBy()` method is commonly used with the `bucketBy()`. This option allows to create sorted buckets.

`MaxRecordsPerFile` option allows to limit the number of records per file. This option can be used with or without `partitionBy()`. and it helps to control the file size based on the number of records and protect from creating huge and inefficient files.