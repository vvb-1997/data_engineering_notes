Usually, if we have nulls, this is not a problem at all because all of the aggregations that we are doing in SQL are also in our BI tools, such as Power BI, Tableau, and so on. These null values can be dealt with very easily.

In SQL if we want to take the average of the incoming amount in our account, so for example, we have $50,the next day we have 100, so there were $50 incoming and then the next day, there was no change, so nothing was incoming and therefore, we have a null value in here. But now if we want to aggregate that table, so all of the average, minimum, sums, we can easily get those numbers. So this is something that SQL and all of the other tools usually can deal with very easily. So therefore, this is usually nothing that we need to take special care about.

![[Fact Null example.png]]

But now oftentimes, the only thing that we need to be a little bit careful is that if we, for example, want to have a look at the average, we see the average incoming amount is indeed 50 US dollars because when there were some transfers, the average of these transfers were $50. But the meaning can be a little bit misleading because on average, we did not get 50 US dollars per day, for example. 

![[Fact Null example 2.png]]

And therefore, in some cases, it can make sense to replace the null values with real zeros if the meaning should be there was zero but also null values can occur. But then if they are real null values, this is completely fine because all of the aggregations can still be done and make sense.

![[Fact Null example 3.png]]

The only thing that we need to be careful with nulls is in the foreign keys. So if we have a fact table and then there is, for example, no portfolio ID associated, we should not use values here at all because this can create some conflicts and lead to some missing data and some problems if we want to connect this to some dimension tables. 

![[Fact Null example 4.png]]

In this case, if we have null values because there is no portfolio type available, then we should even create dummy values. So for example, 999 or also any other number, for example, minus one or something that is quickly indicating that this is a special value. And then we can also add this value of 999 into our dimension table. So for example, it could be outdated accounts. Sometimes we have no date associated. And then we can also just include some dummy value and include some dummy date, for example, 1st of January 1900. And then we have all of the data and we can easily still connect those tables with the dimension and make sense of the data and don't lose any data.

![[Fact Null example 5.png]]

