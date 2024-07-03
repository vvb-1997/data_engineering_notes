Apache Spark is not only a set of APIs and a processing engine. It is a database in itself. So we can create a database in Spark. 
# Spark Tables

Once we have a database, we can create database tables and views. These tables and views reside inside your database. The table has got two parts.
- Table Data
	- The table data still resides as data files in your distributed storage.
	- By default the file type is parquet file, however, we can control the file type.
- Table Metadata
	- The metadata is stored in a meta-store called catalog. The meta-store holds the information about the table and its data, such as schema, table name, database name, column names, partitions, the physical location where the actual data resides. 
	- All of this is stored in a central meta-store called catalog. By default, Spark comes with an in-memory catalog which is maintained per spark session. 
	- However, this information goes away when the session ends so in order to have a persistent and durable meta-store, spark reuses the Apache Hive meta-store.

![[Spark DB & Tables 1.png]]

Spark allows to create two types of tables
- Managed Tables
	- For managed tables, Spark manages both the metadata and the data.
	- Once managed table is created, Spark is going to create and store metadata about the table in the meta-store.
	- Spark is going to write the data inside a predefined directory location. This directory is known as the `spark.sql.warehouse.dir`. So the `spark.sql.warehouse.dir` is the base location where all managed tables are stored.
	- Once managed table are dropped, Spark is going to delete the metadata and the data as well.
	- Managed tables offers some additional features such as bucketing and sorting. All the future improvements in Spark SQL will also target managed tables.
	
![[Spark Managed Table 1.png]]

- Unmanaged Tables (External Tables)
	- They are similar to Managed tables in terms of metadata but there is difference in terms of data storage location.
	- Once unmanaged table is created, the Spark will only create metadata for your table and store it in the meta-store.
	- User has to specify the data directory location while creating unmanaged table. These tables provides the flexibility to store data at any preferred location.
	- These tables are used when we want to use Spark SQL statements on existing dataset/file. Spark will create metadata and store it. This will allow to run Spark SQL statements on this data.
	- If we drop an unmanaged table, then Spark is going to remove the metadata and do not touch the data files.
	- Unmanaged tables are external tables, and Spark does not have enough control over them. They are designed for reusing the existing data in Spark SQL, and it should be used in those scenarios only.

![[Spark Unmanaged Table 1.png]]
# Spark Managed Tables

Creating managed tables is going to need persistent Metastore. For that we need to enable hive support for Spark using `enableHiveSupport()` method as this is needed to allow the connectivity to a persistent Hive Metastore.

```python
spark = SparkSession \
	.builder \
	.master("local[3]") \
	.appName("SparkSQLTableDemo") \
	.enableHiveSupport() \
	.getOrCreate()
```

```python
flightTimeParquetDF = spark.read \
	.format("parquet") \
	.load("dataSource/")
```

Lets create a managed table by saving existing DataFrame to the Spark Table. But why do we want to save my DataFrame as a Managed Table and not as a parquet file.
- However, if we want to reaccess the data, then we must use the DataFrame Reader API and read it as a DataFrame.
- However, Spark is a database also. So if we create a managed table in a Spark database, then the data is available to a whole lot of other SQL compliant tools.
- Spark database tables can be accessed using SQL expressions over JDBC/ODBC connectors. So, we can use other third-party tools such as Tableau, Talend, PowerBI, and a whole bunch of other tools.
- However, plain data files such as Parquet, Avro, JSON, CSV, etc. are not accessible through JDBC/ODBC interface.

Writing a DataFrame is as simple as using the `DataFrame.write()` method. Set the save mode. And finally, `saveAsTable()`. The `saveAsTable()` method takes the table name, and it creates a managed table in the current spark database.

Apache Spark comes with one default database and the database name itself is the `default`. So, the `saveAsTable()` is going to create `flight_data_tbl` in the default database. However, we wanted to create this table in the `AIRLINE_DB`. There are two options. Prefix the database name with your table name. The second option is to access the catalog and set the current database for this session using `spark.catalog.setCurrentDatabase()` method

```python
spark.sql("CREATE DATABASE IF NOT EXISTS AIRLINE_DB")
spark.catalog.setCurrentDatabase("AIRLINE_DB")

flightTimeParquetDF.write \
	.mode("overwrite") \
	.saveAsTable("flight_data_tbl")
```

To get a list of all the tables in `AIRLINE_DB`, we can use `spark.catalog.listTables()` method 

```python
spark.catalog.listTables("AIRLINE_DB")
```

However, If we wanted to create a partitioned table, we can add a `partitionBy()` method while saving dataframe to table. We have overwritten this plain table with a partitioned table with these 200+ partitions. And this happens because we have 200+ different origins.

![[Spark Managed Table 2.png]]

```python
flightTimeParquetDF.write \
	.mode("overwrite") \
	.partitionBy("ORIGIN" ,"OP_CARRIER") \
	.saveAsTable("flight_data_tbl")
```

For a large dataset, these 200+ partitions are still fine. We should not be partitioning data for a column that has got too many unique values. Instead, we can use the `bucketBy()`. The `bucketBy()` allows you to restrict the number of partitions. So, these five partitions are now called buckets. 

![[Spark Managed Table 3.png]]

```python
flightTimeParquetDF.write \
	.mode("overwrite") \
	.bucketBy(5, "ORIGIN" ,"OP_CARRIER") \
	.saveAsTable("flight_data_tbl")
```

We get five data files, these are five partitions or buckets. The bucketing does not require a lengthy directory structure. It is as simple as data files.

Spark will read one record, look at the key column values. In this case, it is the OP_CARRIER+ORIGIN. Now compute the hash value. Let's say you got some hash number. Divide it by five and take the remainder. You are going to get something between 0 to 4. If the remainder is 0, place the record in the first file. If you got 1, put it in the second file and so on.

Well, this is the simplest form of the hashing. There are other variations for hashing, but the basic principle is the same. As an end result, each unique key combination is going to produce the same hash value, and hence it is going to land in the same file.

![[Spark Managed Table 4.png]]

Sometimes, these buckets can improve your join operations significantly. However, if these records are sorted, they could be much more ready to use for certain operations. So the `bucketBy()` can also have a `sortBy()` companion.

```python
flightTimeParquetDF.write \
	.mode("overwrite") \
	.bucketBy(5, "ORIGIN" ,"OP_CARRIER") \
	.sortBy("ORIGIN" ,"OP_CARRIER")
	.saveAsTable("flight_data_tbl")
```

