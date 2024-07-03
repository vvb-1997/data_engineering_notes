The inner join is going to take only those records that are matched on both sides. Everything else is left behind. So, the inner join makes sense in only those requirements  when you know that your data will match on both sides, and you purposefully want to skip the missing records.

However, in this example, a missing record doesn't make much sense. We sold three units of something called product id 9. We don't know the name of the item, but we made a transaction and we cannot lose it from the final result.

![[Spark DF Outer Joins 1.png]]

The outer join will take up all the records even if they do not have a matching pair. Change the join type and take all the columns so we can see the full result of the join. We are going to sort the output by `order_id`. 

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

join_expr = order_df.prod_id == product_df.prod_id
product_renamed_df = product_df.withColumnRenamed("qty", "reorder_qty")

order_df.join(product_renamed_df, join_expr, "outer") \
	.drop(product_renamed_df.prod_id) \
	.select("*") \
	.sort("order_id") \
	.show()
```

![[Spark DF Outer Joins 2.png]]

We got three records of order id 1. They were expected because we have a matching record on the right side. Similarly, these three rows are also coming because we had a match on the right side.

However, we are also getting the product id 9. This item doesn't have a matching record on the right side. But you still get this thing here because we used outer join. And since we do not have a match on the right side, so we do not know anything about these column values. Hence, these values are null.

So, we got the record from the right side even if the left side is missing. Similarly, we also got one record from the right side even though non of the left side orders referred to the standard keyboard.

The outer join will ensure that we get each record from both sides at least once, even if there is no match on the other side. This record had no match on the right side. And the first one had no match on the left side. But both of these will show up in the join result. And this join is known as full outer join.

We wanted to get a sales report and we haven't sold a single unit of a standard keyboard. So, this record doesn't make any sense in my report.

![[Spark DF Outer Joins 3.png]]

There comes a variation of the outer join. Spark offers three types of outer joins.
- Outer join, or full outer join.
	- The full outer join will bring all the records from both sides, even if we do not have a match.
- Left join, or left outer join.
	- The left outer join will bring all the records from the left side even if we do not have a matching pair on the right side.
- Right join, or right outer join.
	- The right join will bring all the records from the right side even if we do not have a matching record on the left side.

If we want all the records from the left, use the left join. If we want all the records from the right, and do not care about the left side, use the right join. What if we want the records from both sides? Use the full outer join.  So, let's change this join to left join.

```python
order_df.join(product_renamed_df, join_expr, "left") \
	.drop(product_renamed_df.prod_id) \
	.select("order_id", "prod_id", "prod_name", "unit_price", "list_price", "qty") \
	.sort("order_id") \
	.show()
```

However, we still see a small problem. We don't like these nulls in the report. These values are missing on the right side and so we are getting these nulls. But still, can we get some meaningful value here.

![[Spark DF Outer Joins 4.png]]

One option could be to use the product id. If we do not know the name, we can show the product id here. Similarly, if we do not know the list price, we should consider showing the unit price.

We already learned to use built-in functions and column transformations. We can use the `coalesce()` function to transform these two columns. The `coalesce()` function will take the first non-null value from the given list of columns. We take the product name, but if that one is null, we show the product id. Similarly, we fix the list price also.

```python
order_df.join(product_renamed_df, join_expr, "left") \
	.drop(product_renamed_df.prod_id) \
	.select("order_id", "prod_id", "prod_name", "unit_price", "list_price", "qty") \
	.withColumn("prod_name", expr("coalesce(prod_name, prod_id)")) \
	.withColumn("list_price", expr("coalesce(list_price, unit_price)")) \
	.sort("order_id") \
	.show()
```

![[Spark DF Outer Joins 5.png]]