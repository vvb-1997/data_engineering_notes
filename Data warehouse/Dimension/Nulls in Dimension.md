
Nulls must be avoided in FKs in fact tables as Nulls in FKs break referential integrity! and they don't appear in Join between fact and dimension table.

![[Dimension Null example.png]]

To address NULL values we can add a row in dimension table with negative or large value
![[Dimension Null example 2.png]]
![[Dimension NULL example 3.png]]

For example, no promo or if we are talking about the date dimension, we should also just use one dummy value. Of course, this needs to be with the same data type as the column and therefore, if it's a date column as in the date table, then it can be, for example, 1st of January 1900. So it is in the same data type as this entire column here.

![[Dimension NULL example 4.png]]
# Guidelines for NULL in dimension
- Replace nulls with descriptive values
- More understandable for business users
- Values appear in aggregations in BI tools
