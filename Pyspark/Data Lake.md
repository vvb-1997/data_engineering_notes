What was there before the HDFS and Map/Reduce came into existence? Data Warehouses Like Teradata and Exadata. How do we use these Data Warehouses? We created pipelines to collect data from many OLTP systems and brought them into the Data Warehouse. Then we processed all that data to extract business insights and used it to make the correct business decision. This is precisely the same as what Hadoop promised to offer. Hadoop also offered to collect data and process it to extract business insights. So the advent of HDFS and MR started challenging these Data Warehouses in three critical respects.

|      Data Warehouse      |                     Data Lake                      |
| :----------------------: | :------------------------------------------------: |
|   ![[Data Lake 1.png]]   |                ![[Data Lake 2.png]]                |
|     Vertical Scaling     |                 Horizontal Scaling                 |
| Large Capital Investment |             Low Capital Investment<br>             |
|  Terabytes to Exabytes   |             Exabytes to Petabytes<br>              |
|   Only Structured Data   | Structured, Semi Structured and Un-Structured Data |

Data Warehouses were challenged, and we needed a new name for the Hadoop approach. This is precisely when James Dixon, CTO at Pentaho, coined a new term - The Data Lake.

So How did Data lake work? Here is how Data Lake was initially imagined. Same as Data warehouses, we collected data from different data sources and stored them in HDFS. Then we used Map Reduce and Spark to process this data and prepare new data models for generating reports and business insights. Spark took over the Map/Reduce and other tools over a period of time, so it is safe to consider that we used Spark to process and prepare data for reporting. This processed data was also stored in Data Lake storage for business intelligence and reporting. Most of the popular BI and reporting tools offered a connector to access data from the data lake. Data Lake also allowed us to collect and process huge volumes of semi-structured and unstructured data. That's what we needed for Machine Learning and AI, so Data Lake also empowered ML/AI workloads. So this is how the Data Lake was imagined and applied in the initial days of Data Lake evolution.

![[Data Lake 3.png]]

The Data Lake technology missed two supercritical features that Data warehouses offered.
- Transaction and Consistency
- Reporting Performance

We started to integrate relational databases and data warehouses for reporting and BI purposes. So basically, we collected data in Data lake storage, proceeded it using Apache Spark, and stored the result in a Data Warehouse. Finally, we connected the BI and Reporting with the Data Warehouse. Machine Learning and AI still work on the Data Lake, but BI and reporting again shifted to Data warehouses. So Data Lake married Data warehouse, and that's the current Data Lake architecture for many organizations.

![[Data Lake 4.png]]

Hadoop, as a platform, has lost the excitement and hype. Cloud infrastructure became more economical and started seeing wider adoption. And with these developments, the notion of Data Lake also matured as a platform with four key capabilities.
- Data Collection and Ingestion
	- The notion of the data lake recommends that you bring data into the lake in a raw format.
	- That means you should ingest the data into the data lake and preserve an unmodified immutable copy of the data.
	- The ingest block of the data lake is all about identifying, implementing, and managing the right tools to bring data from the source systems to the data lake. We do not have one ingestion tool that solves the purpose in all use cases. And hence, many vendors are competing for a place in this box.
	- Examples:- HVR, ARS Glue, Kafka, Azure Data Factory, Informatica, Talend.
- Data Storage and Management 
	- On-premise HDFS
	- Cloud Object Stores such as Amazon S3, Azure Blob, Azure Data Lake Storage, or Google Cloud Storage.
	- Cloud storage is leading because they offer scalable and high availability access at an extremely low cost in almost no time to procure.
- Data Processing and Transformation
	- This is the place where all the computation is going to happen. For example Initial data quality check, Transforming and preparing your data. Correlating, Aggregating, Analyzing, and extracting some business insights. Applying some machine learning model. All those actions are going to happen in this layer.
	- The Apache Spark falls in this place.
- Data Access and Retrieval
	- Consumption requirements will be coming in all possible formats. You might have data analysts and data scientists looking to access the data from the lake. You might have some applications and dashboards. Those guys are also interested in aggregated results and insights. Some of them would ask you to allow JDBC/ODBC connections. Others might ask you for the REST Interface. Experimenting data scientists would be interested in accessing files.
	- Every data lake consumer comes with different requirements and expectations, complicating the consumption layer.
	- And hence, we have many products competing to find a place in this box as well. Data warehouses and relational databases are the most prominent in this layer.

![[Data Lake 5.png]]

However, the data lake platform is much more complicated as a whole. There are bunch of other additional capabilities to complete your Data Lake implementation. The most critical ones are listed here. 
- Security and Access Control 
- Scheduling and Workflow Management 
- Data Catalog and Metadata 
- Data Lifecycle and Governance
- Operations and Monitoring
