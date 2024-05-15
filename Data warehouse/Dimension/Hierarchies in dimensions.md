In our source data, the data is used for transactional processing and this is why the data is oftentimes also normalized. That means that if we have, for example, this product table, we have the associated category also and then there is a separate table. This of course saves disk space and is also good for the right performance and therefore it is just a good fit for transactional data processing.

![[Dimension Hierarchies.png]]

In data warehouse for analytical data processing, we are looking for high performance to read the data, so to get the data out of the warehouse, and also we want to have a high usability. And if we would have such a case where everything is denormalized, we have many foreign keys in our table, and this can really make the fact table very wide. And this is something that we should avoid. If we do that for all of the different dimensions and all of the hierarchies we get something that is called a snowflake schema. Bad for usability & performance!

![[Dimension Hierarchies  example 2.png]]

we should denormalize the data. For example, we could just create a join between Category_id and then the category table. So we could write the data in our data warehouse just in one table and like this, have flattened the dimension. This is what we should do in our dimensions in dealing with these hierarchies. 

![[Dimension Hierarchies  example 3.png]]

We can combine attributes of different levels of hierarchies together in one column. So this combination can be then more user friendly if the user wants to also have the combination of, for example, year and quarter, and then this can be used directly out-of-the-box. And especially this can be helpful if we have duplicate values, or let's say Nashville, a city, that can occur in the state and not only in one state, but the same city also in another state. In that case, it can also make sense to have some pre-calculated column that is just combining the city and the state. Especially in those cases where one attribute can occur in different levels of the hierarchies multiple times. In that case, it can especially make sense for the user friendliness to just create an out-of-the-box combination of those two columns. 

![[Dimension Hierarchies example 4.png]]