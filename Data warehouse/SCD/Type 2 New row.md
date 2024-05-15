Problems with Type 1 SCD:
- No history of dimensions!
- Only current state is reflected

Example: Change in Dimension Table
![[Data warehouse/images/Dimension SCD Type 2 example 1.png]]

Affects aggregation in fact table. So in our case, we want, if we have made the change, after sale number four, we want that line number three is associated now to Biscuits. But all of the out oatmeal biscuits that have been sold before the change should still be now also in the previous category, because at that moment in the history, we didn't make the change, and therefore we still want this to be associated to the Sweets. So this is how we would like to have it. But now with this type 1, we only would have all of the values, now all of the historical sales, also this oatmeal biscuits associated to this new category. And therefore this is not respecting the history.
![[Dimension SCD Type 2 example 2.png]]

The problem that this slowly changing dimension type 2 is solving. Because now with that type, we can perfectly partition and segment our history. So previous values can still be associated to a previous category or a previous dimension value. And then from a moment in time, we can associate this to a new value. And this is what we want to have in our data warehouse, because you remember that history is very important in our data warehouse, and we want to maintain also the history. So this is now what we can do with this type 2 slowly changing dimension.
![[Dimension SCD Type 2 example 3.png]]

With type 1, just overwritten the values, but now what we want to do, whenever there's a change, we don't want to touch the previous value in our dimension, but we want to add an additional row. Then this additional row, of course, has an additional primary key in our dimension, and then this just contains the updated value.
![[Dimension SCD Type 2 example 4.png]]

How does this reflect in the fact table? Well, it's also very simple. In the fact table, from now on, we just use that new foreign key from this dimension. So from now on, we use for this product oatmeal biscuits only the foreign key 4. And with that, we don't need to make any changes in the fact table. So we don't need to make any updates, just use from that moment on the new foreign key. And with that, we get now the correct results, and we completely perfectly respect the history in our data. So this is what we usually want to have if we are expecting common changes in our dimension values.
![[Dimension SCD Type 2 example 5.png]]

![[Dimension SCD Type 2 example 6.png]]

Now one question might arise, what can we now do in order to calculate the number of products? Because now in our dimension table, we see that there are now different products. So for oatmeal biscuit, we have now two products. But keep in mind that we still have, in this situation, we still have the natural key. So in this case, the Product_ID. So what we could do is just count the distinct values of the Product_ID. And with that, we would still get the correct number of products. So this is just highlighting that with that approach, we have the highest analytical values and we still have all of the possibilities. But this is now still not perfect, and you might still have some objections. So for example, how can we, with this approach, find out what is the list of all of the current products? So we cannot distinguish now between those two which is now the current product, and therefore we need to also implement a few strategies to administrate this type 2 slowly changing dimension.
![[Dimension SCD Type 2 example 7.png]]