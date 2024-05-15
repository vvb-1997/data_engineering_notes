Conformed dimension is a dimension that is shared by multiple fact tables / stars. Used to compare facts across different fact tables.

So let's imagine we have a sales report, we have a star schema, so a data mart around a sales table, and in our case, we now want to also analyze the costs. So we have a second fact, and now, we want to compare, of course, the sales with the costs, and now, what we could do is use a so-called conformed dimension, so a dimension that shares the same attributes. So for example, this is something that can be the date or the time, which is the most typical example for a conformed dimension. And now, with that connection, so this conformed dimension, we can now create a report or analysis that is comparing the sales and the fact, so we call this a ==drill across==.

![[Dimension Conformed dimension 1.png]]

![[Dimension Conformed dimension 1.1.png]]

 Both in the sales table, and in the cost fact, we have the region included, and these are now both attributes that are shared by both of these facts, and now, we can in one analysis, in one report, use the sales and compare it also with the cost using this shared attribute of the region.

![[Dimension Conformed dimension 2.png]]

![[Dimension Conformed dimension 2.1.png]]

To compare multiple fact tables, what we need to have if we have a conformed dimension, we need to have the identical attributes, or at least a subset of attributes used for both of these facts. So this could be, in our case, if we have the sales fact, that we of course have a date foreign key, and then also in the cost fact, we need to also have this date foreign key included, and note that it is not necessary to have the same granularity.

![[Dimension Conformed dimension 3.png]]

The cost fact is one row and one date, so therefore, every foreign key, so every value here is also unique. So one row is equal to one day, and this is not necessarily the case in the sales fact table. So in that case, here, there can be duplicate values, and there can be a different granularity, and this makes now this conform dimension so powerful, because now even though we need to have separate fact tables, we can still compare those values with these conformed dimensions.

![[Dimension Conformed dimension 4.png]]

Another option would be that we can also have, in this case, only the monthly costs, but in the sales table, we have maybe just transactional, or also, we have just a periodical sales fact, so every day is one row, but in this case, we can still have different foreign keys.

![[Dimension Conformed dimension 5.png]]

So for example, we can have just the foreign key, in this case, that is using the month and the year to connect to the dimension, and in the other case, we can have the daily key. So this is using now different foreign keys, but still, it is possible to use this same dimension, and then only, of course, in the cost fact, we need to be careful to use only the correct dimension attributes. So this is possible to use also different foreign keys, but also, for example, if we have a monthly value, another alternative would be to just use the first of the month and still use this foreign key. This is also something that is possible, but I would personally prefer that if we have the other foreign key also created in our dimension, that we then use that one of the month.

![[Dimension Conformed dimension 6.png]]