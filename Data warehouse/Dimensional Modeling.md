Dimensional modeling is referring to methods of organizing our data in a specific way, and this is typically used in a data warehouse. Because in a data warehouse, we have usually specific requirements that are also related to usability, and performance because we want to use the data for reporting and OLAP use cases.

In a dimensional model, data is organized in either facts or dimensions. So a fact can be something that is usually measured, so something like a profit that can be aggregated. And then dimensions on the other side, give additional context to those measurements. So for example, it can be something like a month, a time period, or also a product category. And then with this dimension, we can give additional context. So we can turn the fact, the measurement, with the context into meaningful insight. So for example, we can analyze the profit by year or the profit by category. And then with this additional context to our measurement, we get the meaningful insight from our data. 

So you see that this keyword, by, is usually indicating that the year or the category is a dimension. And now this fact is usually in the middle, and we have multiple dimensions clustered around this fact. So we can use all of these different dimensions to analyze our data, and the measurements in our fact table. And because of this visual appearance of a star, we also call this method or this alignment a Star Schema.

![[Data Modelling Star Schema.png]]

Properties of Data Modelling
- Unique technique of structuring data
- Commonly used in DWH
- Optimized for faster data retrieval
- Oriented around performance & usability
- Designed Reporting / OLAP

Benefits of Data Modelling
- Goal: Fast data retrieval
- Oriented around performance & usability

So let's assume we have this table. In our case, it consists out of seven columns, and we want to aggregate. So we want to calculate the total profit in this table. And now, how a database works is that a table is scanned through in rows because the data is stored in rows.

![[Data Modelling example.png]]

The performance of those wider tables is not as efficient as with the more narrow tables. So of course, we have query optimizers in place, but still we have, with wide tables, not such a good query performance. And also, if data is arranged in that way, there can be a lot of duplicate data. So for example, we have the name for the same customer being repeated over and over again. And also, we can have the same product maybe sold thousands of times, and therefore, the product name will be repeated over, and over again.

![[Data Modelling example same name.png]]

And this all results in not such a nice query performance. And for that reason, we can exclude things like the name of the customer into a separate customer dimension. So we only keep the customer ID, and all of the related information we put into a separate customer dimension table. 

![[Data Modelling example remove name.png]]

And the same thing, we can also do with product and category. So we don't need to keep that information, but we can just use and keep the foreign key of this product and then exclude all of this information into a separate product table. 

![[Data Modelling example products name.png]]

We can have the primary key in the dimension table and the related foreign key that is then referring to this primary key of our product table. And now, with that, we have a better performance, but also a better usability because now the data is structured into logical units. So we have all of the product-related information put into a product table, and all of the date information can also be put into a date dimension. 

![[Data Modelling example PK & FK.png]]

So for example, if in our case, we use a date ID that just is a number consisting out of the year, the month and the day, then we can put all of the date-related information into that separate date dimension. And like this, we can now easily find all of that date-related information in our day table. And we don't have to search in hundreds of columns of one fact table. And this is making things much easier for the users. So now, we can just take the profit and slice and dice it, for example, by the month or by the weekday. 

![[Data Modelling example data dim.png]]

And this is where the benefits of a high performance, and a high usability in a dimensional model are coming from. So we do dimensional modeling for the goal of fast data retrieval that comes from high query performance and high usability, because of the way the data is structured into dimensions and fact tables. And therefore, this is the preferred technique for data warehousing because in a data warehouse, we want to use that data for OLAP use cases and reporting. And this is where we need to have high performance, and high usability.

# Facts

In a star schema, which is the most common schema in our data warehouse, when we model our data in a dimensional way, we have the fact table in the middle and then we have the dimensions clustered around this fact. And an example of that could be that we have a fact table, which could be, in our case, a sales table. And then we have different dimension tables clustered around this fact. So the fact is in the center of our star schema and it contains the important measurement of our company. So for example, it can be the sales and we can measure the units sold or the profit that we have made. So this fact table is the foundation of our data warehouse because it contains the key measurements in our company. For example, sales, profit, units sold and so on. And these facts are then what we want to aggregate usually and analyze by the dimensions.

Properties of Fact
- **Aggregable (numerical values)**
	If we have the units, we add them all up, it makes sense. This is the total amount of sold units
- **Measurable vs. descriptive**
- **Event or transactional data**
	So we have one sale, and this sale is basically like a transaction or we have another event.
- **Date/time in a fact table**
	So a sale can also be like an event that is happening at a specific time or a specific date. And therefore, oftentimes in a fact table, we also have some kind of date or time column included.

![[Facts.png]]

So therefore, a fact table consists out of our primary key, which is uniquely identifying every single row in our fact table. Then we have usually multiple foreign keys that are the reference to our dimensions. And then, of course, we have the facts themselves. So these facts can be, for example, sales, profit, budget or any other things that are of key interests and can be measured in our company.

Fact table is defined by the so-called grain. And the grain means the most atomic level of a fact. So for example, if we have the following fact table, we see that we have the profit for every single region and every single day. So for every single date and every single region, we have one row defined. So we have the profit in one row for one specific region at one specific date. And this is then the most atomic level and this is then the grain of our fact table.

![[Fact grain example.png]]

==A fact table is containing the facts, which are the key measurements in our company, and they are in the middle of our star schema.==

# Dimension

Dimension in a star schema are clustered around the facts. And the purpose of them is to categorize the facts so we get meaningful context for our measurements. Otherwise, if we just have the total amount of units sold we don't have really some meaningful insights and therefore the character of those dimensions is more supportive and descriptive. So we can't measure it, but we use it to describe for example, the product name or the product category. And this is supporting the facts and help us to analyze and basically filter group and label our data. So that is what we also commonly refer to as slicing and dicing the data.

Purposes of Dimension
- Categorizes facts
- Supportive & descriptive
- Filtering, Grouping & Labeling

Characteristics of Dimension
- **Non Aggregable**
	For example, if we have the years, so the year in a day table can be of course or is numerical but it doesn't make sense to aggregate or add up the years to get a total amount of year.
- **Measurable vs. descriptive**
- **(More) static**
	For example, product name, product category, this is something that is usually not changing or there's not coming anything new, there's nothing happening. Of course, we can have changes in dimensions too but in general this data is more static.

Properties of Dimension
- Dimension table: PK, Dimension, (FK)
- **Examples**: People, products, places, time

![[Data warehouse/images/Dimension example.png]]