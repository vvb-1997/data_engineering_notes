- 1 row = measurement of 1 event / transaction 
- Taken place at a specific time
- One transaction defines the lowest grain

The transaction is the definition, basically, of our grain. So our grain means we have one transaction that makes one row and therefore the transaction. So one transaction defines the grain.

For example, we have a sales transaction. And in this sales transaction we see that one row is just one transaction. And in this transaction we can have different measurements. So for example, we have units. How many units have been sold in this single transaction? And then we have also foreign keys. So we have, what was the product? What was the time of the transaction? And also of course the date and other dimensions. And the same we can also apply for the other example of the calls where we have one call is basically like one event. And then we can have different measurements associated. So for example, how long was the call and other measurements of that event? And of course again, we have different foreign keys.

![[Fact Transaction FT.png]]

Characteristics of Transaction fact table:
- Most common and very flexible
- Typically additive
- Tend to have a lot of dimensions associated
- Can be enormous in size
