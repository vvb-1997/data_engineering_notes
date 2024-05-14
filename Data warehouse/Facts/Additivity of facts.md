# Fully Additive fact
This is the most common type, and it can be just normally added up across all of our dimensions. And the result, the total, of course still is meaningful and makes sense and this is the most flexible and most useful fact because it can just be added up across all of the dimensions and has the most analytical value.

Example: We have a sales table of five different sales and we have the fact of the units sold. So for every of the sales, for every row we see for example, sale number three, we have two units sold and now it makes sense to add up all of these values. In total, we have sold 10 units.  This is a number that makes sense that is meaningful and we have gained that number by just adding up all of these values. So across all of the dimensions we can add up these values and the number makes sense.

![[Fact fully additive example.png]]

we can group by the category and get meaningful numbers that make sense. And also we can just group by, for example the name and the numbers to make sense. And also this is the case, if we want to see this across the dates, we can add the values up across all of the categories across all of the product names. 

![[Fact full additive example 2.png]]

And then for every single day we get the number of units sold. And of course, the number is meaningful and make sense.

![[Fact fully additive example 3.png]]
# Semi Additive fact
Semi Additive fact can only be added up across a few dimensions. So it's again, less flexible. Used carefully & less flexible. Averaging might be an alternative. Example: Balance

Example:  we have account balances. In this case, we have one row where we have in the first day an account balance of $50 and now we add $50 to that account. So in the next day, the account balance is now $100. And then again, in the next day, there is no change. We have no money coming in and no money going out and therefore the value is still 100. But now of course, it doesn't make sense to add up all of these values and get a total balance because the number in the end we have on our account is just $100 on the portfolio type one, of course.

![[Fact Semi additive example.png]]

But now what we can do is we can indeed add the values up across the portfolio types. So for example, for day number one, so we don't add up across the dates, but only across the portfolio type. So we have $50 plus in the same day portfolio type two, $120. So 50 plus 120 is 170. And then in the second day we have in total $270. So this does make sense if we want to add up those values across the portfolio types but now we cannot add them up across the dates. So if we want to add up the total amount of balance this doesn't really make sense because in fact, in the end we only have $100 in USD cash and in the last day we have only $60 in portfolio type two. So the stocks, and therefore this is not possible to add up these values across the date because the numbers simply don't make any sense. And the date is also the typical example of a dimension that the values in a semi-additive fact cannot be added up across.

![[Fact Semi additive example 2.png]]

So this is a very typical example here, but an alternative could be to just take the average. Again, this could be across the date, and in this case the average could make sense. So we can have a daily average and then get these numbers that we see. So this could be something that is possible for the semi-additive facts

![[Fact Semi additive example 3.png]]
# Non Additive fact
Non Additive fact can not be added up whatsoever across any dimension. Examples: Price, Percentages, Ratios

A non-additive fact is something that we have seen in here where we have the price of a unit and now that we can have multiple units sold, and then we if we want to have the total revenue would have to multiply the unit sold with the price per unit. And of course, the revenue is fully additive but the price itself is not additive because we cannot add up all of the prices and then get something like a total price of a category, for example. This doesn't make any sense, and therefore this is completely non-additive because we cannot add those values up in any way and get a meaningful value. And therefore, in this case, we have a non-additive fact. And even the average is very tricky and we have to still consider the number of units sold to get a weighted average.

![[Fact Non additive example.png]]

Because they have limited analytical value, some even argue to include them in a fact table. A better method would be to store the underlying value. So for example, if we have some ratio we can store both the numerator and the denominator and then calculate our resulting values in Power BI tools. So for example, in Power BI, we can make our own calculations with those underlying values. And with that we have the best analytical value from these non-additive facts. So those non-ad facts have the lowest analytical value and we have to be most careful with them. And therefore it is good to know about these three different types of facts in terms of the additivity.