#Data_warehouse
# Why use data warehouse

## Operational data keeping
The data is used to **Turn the wheel** and answers below questions
- Receive orders
- React to complaints
- Fill up stock
- etc..
It is also referred as **Online Transactional Processing** (OLTP)

OLTP stands for Online Transaction Processing. OLTP has the work to administer day-to-day transactions in any organization. The main goal of OLTP is data processing not data analysis.
### OLTP Examples
An example considered for OLTP System is ATM Center a person who authenticates first will receive the amount first and the condition is that the amount to be withdrawn must be present in the ATM. The uses of the OLTP System are described below.
- ATM center is an OLTP application.
- OLTP handles the ACID properties during data transactions via the application.
- It’s also used for Online banking, Online airline ticket booking, sending a text message, add a book to the shopping cart.

![[OLTP.png]]

### Requirements
- One record at a time
- Data input
- No long history
## Analytical decision making
The data is used to **Evaluate performance** and **Decision making** and answer below questions
- What's the best category?
- How many sales compared to last month?
- What can be improved?
- etc..
It is also referred as **Online Analytical Processing** (OLAP)

OLAP stands for Online Analytical Processing. OLAP systems have the capability to analyze database information of multiple systems at the current time. The primary goal of OLAP Service is data analysis and not data processing.
### OLAP Examples
Any type of Data Warehouse System is an OLAP system. The uses of the OLAP System are described below.
- Spotify analyzed songs by users to come up with a personalized homepage of their songs and playlist.
- Netflix movie recommendation system.

![[OLAP.png]]
### Benefits of OLAP Services
- OLAP services help in keeping consistency and calculation.
- We can store planning, analysis, and budgeting for business analytics within one platform.
- OLAP services help in handling large volumes of data, which helps in enterprise-level business applications.
- OLAP services help in applying security restrictions for data protection.
- OLAP services provide a multidimensional view of data, which helps in applying operations on data in various ways.
### Drawbacks of OLAP Services
- OLAP Services requires professionals to handle the data because of its complex modeling procedure.
- OLAP services are expensive to implement and maintain in cases when datasets are large.
- We can perform an analysis of data only after extraction and transformation of data in the case of OLAP which delays the system.
- OLAP services are not efficient for decision-making, as it is updated on a periodic basis.

### Requirement
- Thousands of records at a time
- Fast query performance
- Historical context
## Difference between OLAP and OLTP

| Category            | OLAP (Online Analytical Processing)                                                                                           | OLTP (Online Transaction Processing)                                                                                                                  |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------- |
| Definition          | It is well-known as an online database query management system.                                                               | It is well-known as an online database modifying system.                                                                                              |
| Data source         | Consists of historical data from various Databases.                                                                           | Consists of only operational current data.                                                                                                            |
| Method used         | It makes use of a data warehouse.                                                                                             | It makes use of a standard [database management system (DBMS).](https://www.geeksforgeeks.org/introduction-of-dbms-database-management-system-set-1/) |
| Application         | It is subject-oriented. Used for [Data Mining](https://www.geeksforgeeks.org/data-mining/), Analytics, Decisions making, etc. | It is application-oriented. Used for business tasks.                                                                                                  |
| Normalized          | In an OLAP database, tables are not normalized.                                                                               | In an OLTP database, tables are [normalized (3NF)](https://www.geeksforgeeks.org/third-normal-form-3nf/).                                             |
| Usage of data       | The data is used in planning, problem-solving, and decision-making.                                                           | The data is used to perform day-to-day fundamental operations.                                                                                        |
| Task                | It provides a multi-dimensional view of different business tasks.                                                             | It reveals a snapshot of present business tasks.                                                                                                      |
| Purpose             | It serves the purpose to extract information for analysis and decision-making.                                                | It serves the purpose to Insert, Update, and Delete information from the database.                                                                    |
| Volume of data      | A large amount of data is stored typically in TB, PB                                                                          | The size of the data is relatively small as the historical data is archived in MB, and GB.                                                            |
| Queries             | Relatively slow as the amount of data involved is large. Queries may take hours.                                              | Very Fast as the queries operate on 5% of the data.                                                                                                   |
| Update              | The OLAP database is not often updated. As a result, data integrity is unaffected.                                            | The data integrity constraint must be maintained in an OLTP database.                                                                                 |
| Backup and Recovery | It only needs backup from time to time as compared to OLTP.                                                                   | The backup and recovery process is maintained rigorously                                                                                              |
| Processing time     | The processing of complex queries can take a lengthy time.                                                                    | It is comparatively fast in processing because of simple and straightforward queries.                                                                 |
| Types of users      | This data is generally managed by CEO, MD, and GM.                                                                            | This data is managed by clerksForex and managers.                                                                                                     |
| Operations          | Only read and rarely write operations.                                                                                        | Both read and write operations.                                                                                                                       |
| Updates             | With lengthy, scheduled batch operations, data is refreshed on a regular basis.                                               | The user initiates data updates, which are brief and quick.                                                                                           |
| Nature of audience  | The process is focused on the customer.                                                                                       | The process is focused on the market.                                                                                                                 |
| Database Design     | Design with a focus on the subject.                                                                                           | Design that is focused on the application.                                                                                                            |
| Productivity        | Improves the efficiency of business analysts.                                                                                 | Enhances the user’s productivity.                                                                                                                     |

# What is data warehouse

A database used and optimized for analytical purposes. Data warehouse must be ==User friendly==, ==Fast query performance== and ==Enabling data analysis==

![[ETL.png]]

![[ETL Detailed.png]]
## Goals of a data warehouse
- Centralized and consistent location for data
- User friendly (easy to understand)
- Must load data consistently and repeatedly (ETL)
- Data must be accessible fast (query performance)
- Reporting and data visualization built on top

# What is Business Intelligence

Business Intelligence is made up of different strategies, procedures, and technologies and infrastructures, for example, different tools that we use to create meaningful insights with data analysis. This means that we need to gather data, we need to manage and store data, so that we can create meaningful insights with reporting, data visualizations, but also some more complex tasks, such as data mining or predictive analytics.

- Data gathering
- Data storing
- Reporting 
- Data visualization 
- Data mining 
- Predictive analytics

So what we are doing in Business Intelligence is we want to use and find raw data and transform this data in such a way that we can turn it into meaningful insights. So for example, as mentioned,
we want to do data visualizations, reporting, and those meaningful insights we want to use to understand our company better and make better decisions in the future.

And now, how does a data warehouse fit in here? Well, the data warehouse is basically a very important component of Business Intelligence. Because we use this data warehouse as our data storage, but not only that, it's specifically for a centralized location of the structured data and the transformed data that we then can use to do our data visualizations, our reporting, and therefore it is one of the most important components of Business Intelligence.

But you might also have heard of something that is called a Data Lake. So what is the difference between a data lake and a data warehouse? Because a data lake is also used to store data, and nowadays this is a word that's used so commonly, and therefore we also now quickly want to have a look at a data lake, and what the difference is to a data warehouse.
## Data Warehouse and Data Lake

Data lake & data warehouse are BOTH used as centralized data storage.

|              | Data Lake       | Data Warehouse                |
| :----------: | --------------- | ----------------------------- |
|     Data     | Raw             | Processed                     |
| Technologies | Big Data        | Database                      |
|  Structure   | Unstructured    | Structured                    |
|    Usage     | Not defined yet | Specific and ready to be used |
|    Users     | Data Scientists | Business Users and IT         |


# Data Warehouse Architecture
[[Data Warehouse Architecture]]
# Dimensional Modeling
[[Dimensional Modeling]]
# Schema types
- [[Star Schema]] (Mainly used in Data Marts and Core)
- [[Snowflake Schema]] (Can be used in Core)
# Facts
## Properties of Fact table
- [[Additivity of facts]]
- [[Nulls in Fact]]
- [[Year to Date Fact]]
## Types of Fact table
- [[Transaction Fact Table]]
- [[Periodic Snapshot Fact Table]]
- [[Accumulation Snapshot Fact Table]]

|       **Type**        | **Transactional**                   | **Periodic Snapshot**                            | **Accumulating Snapshot**          |
| :-------------------: | :---------------------------------- | :----------------------------------------------- | :--------------------------------- |
|       **Grain**       | 1 row = 1 transaction               | 1 row = 1 defined period (plus other dimensions) | 1 row = lifetime of process/event  |
|  **Date Dimensions**  | 1 Transaction date                  | Snapshot date (end of period)                    | Multiple snapshot dates            |
| **No. Of dimensions** | High                                | Lower                                            | Very High                          |
|       **Facts**       | Measures of transactions            | Cumulative measures of<br>transactions in period | Measures of process in<br>lifespan |
|       **Size**        | Largest (most detailed grain)       | Middle (less detailed grain)                     | Lowest (highest aggregation)       |
|    **Performance**    | Can be improved with<br>aggregation | Better (less detailed)                           | Good performance                   |
## Special Fact tables
- [[Factless Fact Table]]
- [[Natural vs. Surrogate key]]
## Steps to create a fact table
1. **Identify business process for analysis** 
	Example: Sales, Order processing
2. **Declare the grain**
	Example: Example: Transaction, Order, Order lines, Daily, Daily + location
3. **Identify dimensions that are relevant** (Soul for analysis)
	What, when, where, how and why
	Example: Time, locations, products, customers, etc.
4. **Identify facts for measurement**
	Defined by the grain & not by specific use case

![[Steps to create FT example.png]]
# Dimension

## Introduction
Dimension table always has a Primary Key (PK). Primary key is natural key which is coming straight out of the source system. But this is not the best way of a primary key. We should rather replace those primary keys and use surrogate keys.
![[Dimension example 1.png]]

Surrogate key is usually a integer number that is just increasing one by one.
![[Dimension example 2.png]]

And then also the question is do we need to keep those original natural keys? Yes, we can, but oftentimes it is also not necessary. But what we should do is we want to have usually a lookup table. So this is just giving the reference of our key so our created surrogate key and the natural key. So this is done very easily. We can just query in SQL the distinct values of this product ID, the natural key and then just populate a sequence next to that. That is very easy in SQL or also the other ETL tools.
![[Dimension example 3.png]]
## How can the new surrogate key mapped to fact table

Initial Fact table will contain the natural key (Order_line_ID) as showed below
![[Dimension fact table example.png]]

The product_dim table with the natural and surrogate key.
![[Dimension example 3.png]]

Using the below SQL query the surrogate key can be mapped to Fact Table.
![[Dimension SQL example.png]]

Resulting in the below output which contains all the data from fact table and the new surrogate key from product dimension table
![[Dimension fact table example 2.png]]

## Properties of Dimension table
- Always has a Primary Key (PK)
- Relatively few rows / many columns with descriptive attributes
- Group & Filter ("slice & dice")

![[Dimension example 4.png]]

![[Dimension example 5.png]]

## Dimension Types
- [[Date Dimension]]
- [[Nulls in Dimension]]
- [[Hierarchies in dimensions]]
- [[Conformed dimensions]]
- [[Degenerate dimension]]
- [[Junk dimensions]]
- [[Role playing dimension]]
## Slowing Changing Dimension
Till now we have pretended dimensions never change, but surprise indeed they are rather static but usually they do change in the real world. 
How to  handle SCDs
- Be proactive: Ask about potential changes
- Business users + IT
- Strategy for each changing attribute

Kimball introduced SCD in 1995 and distinguished between different types (1, 2, 3, …)
- [[Type 0 Retain Original]]
- [[Type 1 Overwrite]]
- [[Type 2 New row]]
- [[Administrate Type 2 SCD]]
- [[Mixing Type 1 + 2]]
- [[Type 3 Additional Attributes]]

# ETL 

Three steps for ETL
- [[Extract]]
- [[Load]]
- [[Transform]]

# ETL Tools

| **Enterprise**                                                   | **Open<br>source**                                       | **Cloud<br>native**                                              | **Custom**         |
| ---------------------------------------------------------------- | -------------------------------------------------------- | ---------------------------------------------------------------- | ------------------ |
| Commercial                                                       | Source code                                              | Cloud technology                                                 | Own development    |
| Most mature                                                      | Often free                                               | Data already in cloud?                                           | Customized         |
| Graphical interface                                              | Graphical interface                                      | Efficiency                                                       | Internal resources |
| Architecural needs                                               | No guaranteed Support                                    | No Flexibility                                                   | Own Maintenance    |
| Support                                                          | No ease of use                                           |                                                                  | Own Training       |
| Ex: Alteryx, Informatica, Oracle Data Integrator, Microsoft SSIS | Ex: Talend Open Studio, Pentaho Data Integration, Hadoop | Ex: Azure Data Factory, AWS Glue, Google Cloud Data Flow, Stitch |                    |
# ELT
[[ELT]]

# ETL vs ELT

| ETL                                      | ELT                                    |
| ---------------------------------------- | -------------------------------------- |
| More stable with defined transformations | Requires high performance DB           |
| More generic use cases                   | More flexible                          |
| Security                                 | Transformations can be changed quickly |
| Batch processed                          | Real time                              |
| Reporting                                | Data Science, ML                       |
| Generic use cases                        | Real time requirements                 |
| Easy to use                              | Big data                               |


# Data Warehouse use cases

- Basis for reporting
	- Performance
	- Strategic decisions
	- Integrated
- Enables business users to analyze data
	- Accessible
	- Data Quality
	- Easy to use
- Predictive Analytics
	- Continuous Training of Machine Learning Models
- Use Big Data
	- Aggregate & Filter

# On-Premises vs Cloud

| On-Premises                                      | Cloud                                                    |
| ------------------------------------------------ | -------------------------------------------------------- |
| Own local hardware                               | Software as a service                                    |
| Own Storage and Compute layer                    | Pay for what you use                                     |
| Own Software layer                               | Managed service                                          |
| Physical data center                             | Optimized for scalable analytics                         |
| Benefits: Full control                           | Benefits: Fully managed, Scalable, Time to market        |
| Benefits: Data governance & compliance           | Benefits: Cost efficient, Managed security, Availability |
| Problems: Full responsibility, High costs        | Problems: Regulations                                    |
| Problems: More internal resources, Less flexible | Problems: Different providers?                           |

# References

([Difference between OLAP and OLTP in DBMS - GeeksforGeeks](https://www.geeksforgeeks.org/difference-between-olap-and-oltp-in-dbms/))