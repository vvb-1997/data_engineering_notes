The purpose of the staging area is just to extract the data from the sources. We don't want to spend a lot of time on the data sources so we just quickly want to have read access to the data there and then quickly pull the data and extract it and place it in our staging area where it's now safely available in our environment in nice tables. And from there, we can then go ahead and define all of the transformations with our ETL tool and load it into our core layer. This again is the access layer for the end users and the applications and that's why this layer is oftentimes perceived as our data warehouse.

![[Staging layer.png]]

## Purpose of staging layer

- Short time on the source/operational systems
- Quickly extract
- Move the data into relational database
- Start transformations from there

## Staging Layer examples

### Initial ETL process

The data is first available in our source system. So let's assume we have some small sales table and this is now the first step. We just extract the data from the source systems. Very quickly, we just need to quickly read the data from the source systems and then we can extract it quickly into our staging area. And from there, we can now of course as mentioned apply our transformations and load it into the data warehouse. So in our case, we've just merged this data with some additional tables so we have now some additional columns. So it's just very small transformations in our case.

![[Staging layer Initial ETL run.png]]
### Temporary Staging layer

And now what happens if in the next day there are some additional data now present in source systems. So what we are now doing is we are cleaning up, we are truncating the staging layer. So it's basically empty and it's just a temporary layer because after every cycle or after every round of our ETL, we always truncate this layer. And now what we need to do is we need to have a look at what is now new data in the data sources.

And to identify what is new, need to have a delta logic in place. That means we need to find a column that is our delta column that is telling us if this is new data. So for example, it can be an ID column. We remember the last number that we have imported was five. So now in the next run, we will just load everything that is larger than five so we will load all of these rows. So of course if we use an ID column as a delta column, we need to make sure that it's really a strictly ascending number otherwise we will load the wrong data. So therefore, oftentimes in practice, a date column is the delta column.

So we will in our case load everything that is after the 2nd of January. So we will load all of that data. And then again, we have our logic for the transformations defined and we can apply the same logic and append now this data in our data warehouse. So this is how it's usually working. One downside of this approach is that these transformations can sometimes be problematic and cause some mistakes, the data might change and therefore there can be some cases where we need to roll back and start again from the data that is also from previous days, for example.

![[Staging layer Temporary.png]]

### Persistent Staging layer

if we need to roll everything back, of course we could go back to our source systems. But as we remember, we don't want to spend a lot of time there and this can be a bit more complicated. So therefore, it can be in some cases good to have a persistent layer where we are never truncating the data. So we can always easily roll back in time. The persistent layer is much more rare than the temporary layer.

![[Staging layer persistent.png]]
## Summarize staging layer 

- Staging Layer is the landing zone for extracted data 
- As little "touching" (transformation) as possible
- Data in tables and on a separate database
- We don't charge the source systems
- Temporary or Persistent Staging Layers