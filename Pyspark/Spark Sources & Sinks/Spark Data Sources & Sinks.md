# Spark Data Sources

Spark is used for processing large volumes of data. However, any processing engine, including Spark, must-read data from some data source. And that's what we mean by Spark Data Sources. These data sources can be further categorized into two groups. 
- External Data Sources
- Internal Data Sources

![[Spark Data Sources 1.png]]
## External Data Sources

Data can be stored in some source systems such as Oracle or SQL Server Databases. It might be stored at some application servers, such as your application logs. However, all these systems are external to your Data Lake and not present in the Data Lake conceptual diagram. So, we categorize such data sources as external data sources. The list of possible external data sources is too long. However, here are some notable systems. 

- JDBC Data Sources
	- Oracle, SQL Server, PostgreSQL
- No SQL Data Systems
	- Cassandra, MongoDB
- Cloud Data Warehouses
	- Snowflake, Redshift
- Stream Integrators
	- Kafka, Kinesis

The data cannot be processed from these systems unless it is read and create a Spark DataFrame or a Dataset. There are two approaches. The first approach is to bring your data to the Data Lake and store them in your Lake's distributed storage. The most commonly used approach is to use a suitable data integration tool. Best used for batch processing requirements.

![[Spark Data Sources 2.png]]

The second approach is to use Spark Data Source API to directly connect with these external systems. And Spark allows you to do it for a variety of source systems. Best used for stream processing requirements. 

Using a data integration tool to bring data to the distributed storage, and then we start processing it. Below are the reasons to prefer this two-step approach
- Modularity
	-  Bringing data correctly and efficiently to your lake is a complex goal in itself. We want to decouple the ingestion from the processing to improve manageability. 
- Load Balance
	- Your source system would have been designed for some specific purpose. And the capacity of your source system would have been planned accordingly.
- Security
	- Now, if you want to connect your Spark workload to these systems, then you must have to replan your source system capacity and the security aspects of those systems.  
- Flexibility
	- We want to use the right tool for the right purpose. Spark is an excellent tool for data processing. However, It isn't designed to handle the complexities of Data Ingestion. So, most of the well designed real-life projects are not going to directly connect to the external systems even though we can do it.
## Internal Data Sources

Internal data source is your distributed storage. It could be HDFS or cloud-based storage. However, at the end of the day, data is stored in these systems as a data file. The mechanics of reading data from HDFS or from cloud storage is the same.

![[Spark Data Sources 3.png]]

However, the difference lies in the data file format. Here are some commonly used file formats.
- CSV
- JSON
- Parquet
- AVRO
- Plain Text

Other than these file formats, we also have two more options. These two are also backed by data files. However, they also include some additional metadata that is stored outside the data file. So we do not categorize them as file sources.
- Spark SQL Tables
- Delta Lake

# Data Sinks

Data sinks are the final destination of the processed data. So we load the data from an internal or an external source. Then you will be handling it using the Spark APIs. Once your processing is complete, you want to save the outcome to an internal or an external system. And these systems could be a data file in your data lake storage, or it could be an external system such as a JDBC database, or a NoSQL database.

Working with the data source is all about reading the data, and working with the sink is about writing the data. Same as sources, Spark allows you to write the data in a variety of file formats, SQL tables, and delta-lake. Spark also allows you to directly write the data to a bunch of external sources such as JDBC databases, Cassandra, and MongoDB.

![[Spark Data Sources 4.png]]

However, it is not recommend to directly writing data to the external systems for the same reasons as we do not directly read from these external systems.
