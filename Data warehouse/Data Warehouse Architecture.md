Data warehouse consists of multiple layers and have our data sources and use an ETL process to load the data from these data sources with some transformations into data warehouse.

And to do so and understand these different layers, we want to have a closer look, also, at the ETL process. So, in the beginning of this ETL process, there are source data which is processed by using ETL tool to extract these different formats and these different files into first layer. And this is the so-called "staging layer". This layer is there just to extract the tables as they are into this first layer and we want to place it into tables. So, they are now all in tables without any data transformation.

So, for example, let's say we have employee tables in different departments and they are all in different formats, like CSV files, some are in databases, and so on. And we want to extract them all into tables, into our staging area. And it can be, now, that they are in different tables.

![[DW architecture staging.png]]

But if the data structure is pretty much the same, then we can also already do a little bit of transformation. That is to append these data roles and combine them already into one  department's table or into one employee's table. So, this is what we want to have anyways later on. So, you see that there can be some small transformation steps, such as appending and combining the tables into one employee table, for example.

![[DW Architecture Staging combine.png]]

But sometimes, also, we need to do a little bit more work. So, for example, we see that in this case, the table headers are differently named. So, we have "position", "position_level". Some data is abbreviated in one table and written out in the other table. And also, we see the ID is again starting in the different department, again, with one. And these are all steps that we now need to resolve by using some data transformations to integrate the data. And of course, also, we want to model the data as we want to have it. So, oftentimes, we want to restructure that. 

![[DW Architecture Staging seperate.png]]

And while copying the data from Staging to this Core layer, which is sometimes considered as the data warehouse itself, because it is just perceived as that from the end users, we do these transformations while we copy the data from staging to this core layer. So, this is then the layer that is usually used from the end users or the applications. So, from here, we can now pull the final data from our data warehouse and, for example, create reports, do data mining, or predictive analytics.

![[Data warehouse/images/DW Architecture core.png]]

For large data warehouse that consists of many, many tables and we have many, many different use cases, we can include and build data marts on top of this core layer. So, this data mart just takes the relevant tables from the data warehouse that are relevant for one specific use case and this increases the user friendliness because we are not overwhelmed with so many tables. But also, it can help with the query performance because now we have only the tables that we need and only this single user group is querying from the data mart. And not everyone is using up the performance of our data warehouse. And also, we can use specialized databases, such as in-memory databases or cubes, to increase the performance. So, this can be, then, specialized to increase the performance even further. But this data mart is not always necessary. 

![[DW Architecture Data mart.png]]

And another layer that is also very optional is a cleansing area. Sometimes this is included, also, if the data is very raw and we need to do a lot of data cleaning, then we can also have a dedicated cleansing area to clean the data. 

![[DW Architecture Cleaning.png]]

But now, with all of these layers, sometimes there is a little bit of a confusion, what is now actually our data warehouse? And as I mentioned, the core layer is oftentimes the layer that is perceived as the data warehouse from the end users, because it is the single point of truth. It is what is visible in the end, and therefore, oftentimes, we call this core layer our data warehouse. But actually, the data warehouse consists of all of these layers, and therefore, all of these layers can be also called our data warehouse, even though the core or the data marts are perceived as the data warehouse because they are the final access layers.

![[DW Architecture layers.png]]

# Layers of Data Warehouse

- [[Staging Layer]]
- [[Data Mart]]

![[Data Warehouse layers.png]]

Staging
- Landing zone
- Minimal transformation
- "Stage" the data in tables
Core
- Always there
- Business Logic & Single Point of Truth
- Can be sometimes the access layer
Mart
- Access Layer
- Specific to one use case
- Optimized for performance
# Database types

- [[Relational Database]]
- [[In-Memory Database]]
- [[OLAP Cube]]
- [[Operational Data Storage (ODS)]]

