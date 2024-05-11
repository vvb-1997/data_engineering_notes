A relational database is basically just a database. That means we store data in tables. They are also called relations in relational databases. And that means that we have just our data structured into columns and rows. And we use SQL usually. This is the language that is used to query the data. That means that we want to see and return this data in our interface or in our application.

Specific about these relational databases is that we can use keys and put these tables into relations. Usually in a table, we have a primary key. The purpose of this primary key is to uniquely identify every single row in our table. And for that we use the column that is our primary key. And this column then, therefore, needs to contain only unique values. We cannot have any duplicates, and we cannot have any null values.

And the purpose of those foreign keys is to reference another table. So they contain the values from a primary key of another table. So for example, in this case, it can be the customer table. And with these values, we can now reference rows in other tables. Because we remember, this primary key is uniquely identifying every single row. And therefore, if we know that this is a two in this first row, we can go to this table and we know exactly this row here is meant.

![[Relational Database PK and FK.png]]

Traditionally we used the data in operational systems. So we just queried, usually, data from one table. And we were just editing single values, updating single values, entering values. And now with this possibility of putting tables into relations, we can now get a lot more context and combine multiple tables. And this was then enabling and advancing a lot more analysis. So this was the advancement of OLAP. And now this was also closely connected, this rise of relational databases, with the rise of data warehouses. Because now we can organize our data in multiple tables. We can bring them into relation, and now really analyze our data. And we later see how we model these tables, these different tables. So we do this using so-called star schemas.

Relational database management system (RDMS)
- Oracle
- Microsoft SQL Server
- PostgreSQL
- MySQL
- Amazon Relational Database Service (RDS)
- Azure SQL databases
- (Snowflake)