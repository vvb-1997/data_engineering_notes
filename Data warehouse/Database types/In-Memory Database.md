In-memory databases are highly optimized for query performance, and this is why the common use case is always when we need to have high query performance. This is usually the case if we want to use our database for analytical purposes, or anything that requires high query volume. So that's why these in-memory databases are commonly used for data marts, which we remember is the access layer for our users or our applications. They are independent from the fact if the data is structured in a relational way or in a non-relational way.

Properties of In-Memory database
- Highly optimized for query performance
- Usually used for data marts
- Good for Analytics / High query volume
- Relational and non relational

Traditional databases usually store the data on hard disks or solid drive disks, and then when the data is queried, and the data needs to be processed, we load the data in memory, and there's a response time in between that, and this is actually where most of the time that we need to wait for our query to be returned is coming from. And therefore, this is, of course, not optimal if we require a high query performance.

![[In-Memory DB traditional dbs.png]]

Hence in-memory databases are built without disk, but all of the data is stored in memory. That means we eliminate the response time coming from the disk. And since most of the time that we need to use for our query to be returned is coming from this response time from disk to be loaded in memory, we have a much, much better query performance.

So there are also different technology, different algorithms, and different methods used, so for example, there's a columnar storage. This is usually in-memory database is the case that the data is scanned through not by rows but by columns, so we have a columnar storage, and also, we can sometimes use parallel query plans. That means that larger queries that take up a lot of time can be broken down into multiple parts, and then be processed parallel by different threads.

![[In-Memory DB.png]]

First challenge that comes with this in-memory databases is related to the durability of our data. This is a key component and requirement that we have with databases, that we want to be sure that data is still available after a long time, because this data is important for us, and this needs to be durable, but the problem with this in-memory storage is that when the database is disconnected from power, or if it is reset, the information is completely lost. So this is why we need to add additional technologies to get this durability also in memory databases. So for example, we can create snapshots or images that are basically just representing a specific state of our in-memory database so that we can roll back to these snapshots or images in case the data is lost, or of course, also, this in-memory database needs to be for updates or something, needs to be restarted sometimes, and therefore, before this is done, the data is stored into these images on disks, and with it we can also get durable databases, but still, this comes with a high price tag, because this is a technology that is, of course, it's getting cheaper with new hardware developments, but still, the amount of data that we need is increasing even with a faster pace than the price of these technologies, and this hardware is going down, so this is still a very relevant factor. And also, be aware that traditional databases are also getting optimized, and it's also being worked, and we also have a lot of progress in reducing this usage of the disk, and with that getting better query performance.

Challenges of using In-Memory Database
- Durability: Lose all information when device loses power or is reset
- Durability added through snapshots / images
- Cost-factor
- Traditional DBs also trying reduce usage of disc

In-memory databases for our data marts, we should be very specific with the use case, and only load the relevant data for this specific use case into our data mart. And this is why data marts can make sense, because this in-memory databases are, as we've learned, not super cheap, and therefore, we only want to pull the really relevant data for a specific use case.

Products using In-Memory Database
- SAP HANA
- MS SQL Server In Memory Tables
- Oracle In Memory
- Amazon MemoryDB