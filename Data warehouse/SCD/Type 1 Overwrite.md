Many attributes in dimension are subject to changes, and we also want that these changes are reflected in our dimensions so that the users can really see the updated and real new values, and that brings us to the first type, the overwrite, slowly changing dimensions. we just update the old attributes and basically just overwrite them with the new values. In that case, we have only in our dimension table the current state reflected.

For example, if we have a product table and some of the names of the products are changing, for example, oatmeal biscuits is changing to delicious oatmeal biscuits, in that case, we just update this value, and also, we have then only this updated table. Of course, this can be also the case, if, for example, the category changes, then we can also just update the value and we have then this new updated table, and you see that this is very simple to implement because we just update the values in the table, so in the dimension tables, and nothing else is necessary apart from these very simple updates. So also, the fact tables don't need to have any additional updates.

![[Dimension SCD Type 1 example 1.png]]



Problems with type 1:
- History is lost!
	- For example, if we change the category to biscuits, we can now only see how the data is grouped with these new and updated category descriptions. So in that case, we have probably less values, or less amount of sales, for the category sweets because now, it's just this product is in the category biscuits
- Insignificant changes
	-  if the changes are not so significant, so for example, if we have delicious, just an updated product name, it's probably not a significant change and this will not affect all of our analysis, and therefore, in that case, it is not a big deal, and in that case, we can really ignore that
- Might affect / break existing queries
	- For example, if we make a grouping, so in SQL, we can create a calculated column, so we can use case column. So when we have a certain condition and that is depending on one exact name, in that case, we have to be a little bit careful that now, this updated value is not breaking any existing queries.

![[Dimension SCD Type 1 example 2.png]]