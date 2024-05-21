For example, with this previous approach, we could not use and identify the current value. So for example, the current product name and this is one reason why we should also include a so-called effective date and an expiration date. And this is then just referring to the period in which those values are now valid. So for example, for row number three, we have that this value was only valid until end of May, 2022. And after that we have this new value and from that moment on we should use this value. And with that we can now identify the current value and also track all of the history. So from which moment on which value was indeed the correct value that should be used.
![[Dimension SCD admin Type 2 example 1.png]]

We should always use a value for the expiration date. You might think that we could also use nulls but it's a better approach actually to use just instead of nulls, a value that is very far in the future. So for example, 100 or 200 years in the future and this just enables more possibilities. So for example, in SQL, we oftentimes want to filter data using the between function, for example. And this would not work with the null and therefore we should just instead always just use a date that's very far in the future.

And note also that this approach with the slowly changing dimension type 2, makes it necessary to use a surrogate key. So use this primary key for our product key that we have artificially created. So we can see that the natural key is not unique anymore, but our primary key always needs to be unique and therefore we for sure need to have this surrogate key.

How can we make sure to use the correct foreign key also in our fact table? And the approach, we just want to break it down very simply. So we should first, as we have learned, add the new row in our dimension. So this is the first step. We can just use that and add also the effective date, the expiration date. And then what we basically need to do is we need a look up in the dimension. So what we do is we use now, in order to find the correct foreign key, we need to use of course as previously also the natural key. But now on top of that, we need to find out additionally what is now the current value. So for example, if the ETL runs today at the 1st of June, in let's say 2023, then we can see okay, the current date is in between the effective date and expiration date only for row number four and therefore this is the current value and this is the current correct foreign key that should be used in the fact table. So we just need to make a quick look up after the row has been added in the dimension. And then we can use the correct foreign key also in our fact table.
![[Dimension SCD admin Type 2 example 2.png]]

And one additional thing that we can do in order to make the filtering a little bit easier is to add a column that is just a flag basically indicating if this is the current value or not. And this is just an additional filtering option that is suggested by also Kimball, for example and we can combine that now with the other columns. And this is now just a little bit easier for business users to, for example, if they want to just see the current values. So this can be a flag that is just also helping with the filtering. 
![[Dimension SCD admin Type 2 example 3.png]]