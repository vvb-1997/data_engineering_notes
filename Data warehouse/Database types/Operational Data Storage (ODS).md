ODS is very similar to a data warehouse because we also have different operational systems. And this can be sometimes where all of the important data is stored. But we want to integrate now these different operational systems, the data of these systems into one single database. And we also can use an ETL. And then the database in which we have integrated all of this data is our operational data storage. 

And the key difference between ODS and Data warehouse is that an ODS is used for operational decision making. And this makes the process a little bit different and also the requirements of an ODS different because it's not used for analytical or strategic decision making but for very quick operational decision making.

Property of ODS
- No need for long history
- Needs to be very current or real time

![[ODS.png]]

Assume you are working in a financial service company, and in this financial service company, your customers, they can do different things. They can invest in ETFs, they can invest in stocks, in cryptocurrencies, they can have an account balance. And we need to find out from those different systems. So maybe we have one system for crypto, one for stock trading, and we need to combine the overall balance the overall amount that they have invested from those different systems. And this needs to be reflected immediately so we can make our operational decisions.

So, for example, we need to decide can we give this customer a credit or not? And therefore we need to see the data as it is in these operational systems in almost real time so that we can make accurate decisions. And also we don't need to have a long history but only how does the data look currently in those source systems. And therefore we have usually in this ETL or this real time data feed and update logic in place. So we don't append the data usually and keep the history but we just update or replace the data.

Yes, we can have both ODS and Data Warehouse. So we can have an ODS that uses a separate ETL with separate requirements that is transforming and integrating the data and then is used for operational decision making. And then parallel to that, we can have another ETL that maybe goes once per day or even once per hour. And then this data warehouse that is created out of that is used for the analytical decisions that are more long-term or more strategic. 

![[ODS Parallel.png]]

And this is a sequential integration. So in this case, we can still maintain our operational data storage, but we just built our ETL for the data warehouse on top of our operational data storage because we've already done the heavy work of integrating the data and now we can just use this operational data storage as kind of our source, our staging layer, if you will, for our data warehouse. And with that, we just save a lot of work and effort to integrate the data because we can just use the ODS as our data source.

![[ODS Sequential.png]]

ODS is getting less and less relevant. This is partly because of better performance of the hardware. So we can load just the data much quicker nowadays. And this is just making the ODS less important. And also there are nowadays other technologies that fulfill these requirements for real-time data or very fast updated data. So therefore it is more and more uncommon to have an ODS in place in a company.
