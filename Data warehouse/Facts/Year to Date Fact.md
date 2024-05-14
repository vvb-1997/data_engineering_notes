So these are specific calculations that are actually very problematic, and therefore, how we can deal with those year-to-date calculations in our data warehouse. So this is something that is very commonly requested by the business users because they want to have those year-to-date or month-to-date calculations in their BI tool because they want to see it in their reporting. And therefore, we are tempted to just implement this by calculating those year-to-date or month-to-date facts and physically store them in columns in our data warehouse. And this can be true also for the month-to-date calculation, quarter-to-date, fiscal-year-to-date and all of the different variations of basically, for example, just the revenue. So just the underlying value.

Properties of Year to Date Fact
- Often requested by business users 
- Tempted to store them in columns
- Month to Date, Quarter to Date, Fiscal Year to Date etc.
- Better store the underlying values in defined grain (!)
- Instead calculate all the to Date variations in BI tool