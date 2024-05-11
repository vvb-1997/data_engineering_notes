The core layer is serving as our access layer. But sometimes if we are in a very large company and our Data Warehouse, it's built for many, many different use cases. And we can use different tools with this Data Warehouse. And all of the user groups, all of the departments, all of the regions, they are all using the same Data Warehouse. And this can be sometimes a little bit complicated then, and therefore sometimes there's an additional layer added on top of this core layer. And these are the Data Marts.

So, this additional layer of the Data Marts is actually nothing but a subset of our Data Warehouse, meaning our core layer. And the data in this Data Marts is modeled in a dimensional way. So we have fact tables in the middle and dimension tables around these fact tables.

If core layer is serving us our access layer, which is possible, sometimes we don't need the Data Marts, then also in this core layer the data can be modeled into dimensions and facts. But also what is possible that since these Data Marts are really built for a specific use case, this is what makes them a Data Mart. We can also sometimes further aggregate the data so that it serves, really, our use case.

![[Data mart layer.png]]

## What is Data mart

- Subset of a DWH
- Dimensional Model
- Can be further aggregated

## Reasons of using data mart

- Usability + Acceptance for not technical users
- Performance by using in-memory databases or data cubes

### Use cases for data mart

- **Tools**  
	We can have data visualization with Power BI, and then it's very good if the data is in in-memory databases. So this is good for the performance and what gives very good results for Power BI. But for other tools, for example, we want to do some predictive analytics with some other tools, then we don't need to have the data stored in a in-memory database. And, therefore, we can have another Data Mart, maybe also with different data and a different database type.
- **Departments**
	Different departments with different use cases. So we have the sales team, and we have the finance team, we have the marketing team, and all of that is stored in our core, but it's not relevant for all of the use cases and all of the departments.
- **Regions**
	Another use case can be that we have just different Data Marts for different regions.
- **Use-cases**
	Overall, we can just summarize that a Data Mart is built for different use cases, whether it be different tools, different departments, or different regions.