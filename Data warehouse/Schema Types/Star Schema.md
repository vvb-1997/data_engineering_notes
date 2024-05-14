The Star Schema is basically the most important schema in our data warehouse, especially in our data marts. So we've already seen that we arrange, and structure our data in facts and dimensions. So in our case, if we have a look at this example we have the sales table, which contains all of our important facts, and we can create relationships.

![[Star Schema example.png]]

Using the foreign key and the primary key to create those connections or joins to our dimension tables, in our case, the product table. And these are then, for example, looking like this. So we have our fact table with our foreign key that enable a relationship or a join to a primary key of our dimension table. And in this case, we have a one-to-many relationship usually between the fact and the dimension table. So we have every single value in the column that we use for the connection. So it's the product ID. 

![[Star Schema example PK & FK.png]]

Since we have only one level of hierarchy, so we have only one connection, and then from here, there's not any other connection starting. And that's why we can have some data redundancy because in this case, the category column is basically another level of hierarchy. And in here we have, for example, garlic and banana and they are both in the category fruits and vegetables. And therefore this value is repeated. And basically this is a data redundancy because values are occurring twice, which would theoretically, not be necessary. So we can see later on in the Snowflake Schema, an alternative that is reducing this data redundancy. And this reducing of data redundancy is called normalization.

Normalization is not ideal to get data out, and have a good read performance on our operations as well as a good usability. And therefore we can accept this data redundancy because it is in our case, better for what we want to do with that data. So again, normalization is a mathematical technique that is reducing the data redundancy, and with that we have lower storage costs.

Properties of Normalization:
- Technique to avoid redundancy
- Minimizes storage
- Performance (write / update)
- Many tables
- Many joins necessary

Properties of Denormalization:
- There is data redundancy!
- Optimized to get data out
- Query performance (read)
- User experience

# One Fact Table and multiple Dimension table

![[Star Schema 1 FT & N DT.png]]
# Multiple Fact Table and multiple Dimension table

![[Star Schema N FT & M DT.png]]
# Summary of Star Schema
- Most common schema in Data Mart
- Simplest form (vs. snowflake schema)
- Work best for specific need (simple set of queries vs complex)
- Usability + Performance for specific (read) use case
