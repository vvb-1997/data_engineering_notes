Dimension that is referenced multiple times by a fact. For example Date Dimension.

So in this case we have one fact table that also contains order dates for specific products. And then also when we started with the production of those products. And we can see now that we have two date foreign keys and now of course we have one date dimension. And now it wouldn't make sense to duplicate this dimension physically in our database and have now one dimension that is for the order date and one dimension that is for the production date. And therefore we can use that same table.

![[Dimension Role playing example 1.png]]

So these are now the two roles that this date dimension is playing. And now with that, we can analyze both the orders received so based on the date dimension of the month, but now if we want to use the other role, so the other relationship or the other join with the other column, then we can also display the products based on the date of the production. So when the production has started.

![[Dimension Role playing example 2.png]]

- For analysis in SQL you can create additional view for each role
- No duplicated data but still we it appears like a separate dimension