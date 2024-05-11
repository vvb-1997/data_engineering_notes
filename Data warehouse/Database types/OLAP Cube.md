This is an alternative method to further increase the performance of our data marts, and actually it's a quite established and mature technology. So in a traditional data warehouse, data is stored usually in a relational database where we have the data organized into tables that can have certain relations with each other. But now in a cube, the data is not organized in tables with relations but in a non-relational way and into dimensions.

And in this multidimensional data set (MOLAP), the data is not organized into tables with columns and rows, but into so-called arrays. The main reason why we want to use those cubes is for analytical purposes whenever we need to have a fast query performance. And that's why they are used exclusively in the data marts. And then if we have those cubes created with some technology, some software, we can use those cubes in different BI solutions.

Properties of OLAP Cube
- Traditional DWH based on relational DBMS (ROLAP)
- Arrays instead of tables
- Data is organized non relational in Cube (MOLAP) Cube = Multidimensional dataset
- Main reason to use: Fast query performance
- Works well with many BI solutions

So a cube, if we want to see that visually, is organizing the data. For example, we want to analyze the sales data into multiple dimensions. And be aware that we can have more than just three dimensions. So in our case, we have products, time and customers, and we want to analyze the sales. So this is what we want to measure across those multiple dimensions. And now we can use those arrays and those cells to slice and dice our data. For example, we can use the intersection from a certain customer in a certain month or on a certain time to get the amount of sales from that. So we want to measure the sales and we can now slice and dice the data to get a specific data point calculated. And this data is now, this is where the benefit from these cubes is coming from is pre-calculated. So the values in those cells is already calculated so that when we want to see the data, visualize it, and use it in our tools, it's already calculated and already available. And now this technology is different from SQL and usually it use the MDX language. So that's the multidimensional expression that was developed by Microsoft, and it's now the most commonly used query language to query data from cubes.

![[OLAP Cube.png]]

Recommendation for using OLAP Cube

- Built for a specific use case (as data marts in general)
- More efficient & less complex with separate data marts
- Good for interactive queries with hierarchies
- Optional after star schema is built in relational DB

Alternatives for  OLAP Cube
- Less important today with advancement of hardware
- Alternatives:
	- Tabular models (SSAS)
	- ROLAP
	- columnar storage