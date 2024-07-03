Joins are all about bringing together two data frames. Here is an example. These two data frames are termed as a left data frame and the right data frame. Correct?

![[Spark DF Joins 1.png]]

We combine these left and right data frames using two things.
- The join condition or the join expression.
	- `join_expr = order_df.prod_id == product_df.prod_id`
- Join type
	- `joinType = "inner"`
`order_df.join(product_renamed_df, join_expr, "inner")`

We always start with the left data frame and pass-in the right data frame as the first argument to the join method. The join method takes two more arguments, Join Expression and Join Type. We can define a variable and pass it here or we can type in the expressions directly. It works both ways.

```python
orders_list = [("01", "02", 350, 1),
			   ("01", "04", 580, 1),
			   ("01", "07", 320, 2),
			   ("02", "03", 450, 1),
			   ("02", "06", 220, 1),
			   ("03", "01", 195, 1),
			   ("04", "09", 270, 3),
			   ("04", "08", 410, 2),
			   ("05", "02", 350, 1)]

order_df = spark.createDataFrame(orders_list).toDF("order_id", "prod_id", "unit_price", "qty")

product_list = [("01", "Scroll Mouse", 250, 20),
				("02", "Optical Mouse", 350, 20),
				("03", "Wireless Mouse", 450, 50),
				("04", "Wireless Keyboard", 580, 50),
				("05", "Standard Keyboard", 360, 10),
				("06", "16 GB Flash Storage", 240, 100),
				("07", "32 GB Flash Storage", 320, 50),
				("08", "64 GB Flash Storage", 430, 25)]

product_df = spark.createDataFrame(product_list).toDF("prod_id", "prod_name", "list_price", "qty")
```

The inner join is the default join type. So we can skip the third argument if you want to perform an inner join. Spark is going to take one row from your left data frame and evaluate the join expression for all the rows in the right data frame to find a match. Then it goes to the second row and repeats the same. And so on. 

![[Spark DF Joins 2.png]]

The next step is to combine these matching records from the left and the right data frames to create a new data frame. That's where the join type comes into the play. The default inner join type will take only those records which are matched with at least one record on the other side.

```python
join_expr = order_df.prod_id == product_df.prod_id
order_df.join(product_df, join_expr, "inner").show()
```

![[Spark DF Joins 3.png]]

However, We are only interested in 4 columns. Order id, product name, unit price, and the order quantity. However one is the order quantity, and the other one is the product reorder threshold quantity. But unfortunately, both have the same column name. So, when we ask for the quantity, the spark gets confused and throws this error.

This ambiguity didn't happen when we selected all the columns. In fact, the product id is also coming twice because it was present in both the left and right data frames. Why do we have this select * working perfectly fine, but Spark complaining about ambiguity when we explicitly refer a column name.

Every Dataframe column has a unique ID in the catalog, and the Spark engine always works using those internal ids. These ids are not shown to us, and we are expected to work with the column names. However, the spark engine will translate these column names to ids during the analysis phase. When we refer the column name in my expression, then the Spark engine will internally translate the column names to the column id.

But when we are using select * , it takes all the column ids and shows them. Translation from column names to column id is not needed.

We have two approaches to avoid such ambiguity. The first approach is to rename ambiguous columns, even before joining these two data frames. Use `withColumnRenamed()` transformation to rename the product quantity to the `reorder_quantity`.

```python
product_renamed_df = product_df.withColumnRenamed("qty", "reorder_qty")

order_df.join(product_renamed_df, join_expr, "inner") \
	.select("order_id", "prod_name", "unit_price", "qty") \
	.show()
```

![[Spark DF Joins 4.png]]

We have another approach. Drop the ambiguous column after the join. We still have one ambiguous column `product_id`. So if we am selecting `prod_id` here, we are still going to get the ambiguity exception. We want to drop one of those after joining the data frames.

```python
product_renamed_df = product_df.withColumnRenamed("qty", "reorder_qty")

order_df.join(product_renamed_df, join_expr, "inner") \
	.drop(product_renamed_df.prod_id) \
	.select("order_id", "prod_id", "prod_name", "unit_price", "list_price", "qty") \
	.show()
```

![[Spark DF Joins 5.png]]