In transactional fact table, we can have a lot of indicators or flags that are actually dimensions but they don't really fit into a given dimension. And therefore, we could create a so-called junk dimension.

In transactional table there are some sales transactions and we have a few flags or indicators. So for example, what is the payment type? Is it incoming or an outbound payment? And also is it associated with a bonus or not? And in this case, we have now a few options of how we could deal
with these dimensional attributes in our fact table.

![[Dimension Junk dimensions example 1.png]]

Possible solutions
- **Eliminate them if they are not relevant.**
	- There might be some business users that say this is very relevant for me and I still want to use these indicators. 
- **Leave them as they are in the fact**
	- What happens if these are very long text values that are really bulky? And then also it could be that this table size if it's a very long fact table, is just increasing dramatically.
- **One Flag -> One dimension (Create new dimension** 
	- The problem with that would be that if we have already a very wide fact table, this could just again increase the size and the width of this fact table, which is not so good for the performance and not so good for the user friendliness.

And therefore, we can go also with an alternative. And that is to create a so-called junk dimension. A junk dimension is basically a separate dimension that contains various flags or indicators. So this could be yes, no or anything else with a very low cardinality. So the options of values are not so many. So for example, there are only two, three or four different values. And then we refer to this as a junk dimension.

So we can understand this junk dimension like a box in our household where we just put in some items that don't fit anywhere else. So we don't want to create a separate storage location for these various items and therefore we put them all just in one box. So we have here all of the items that don't really fit somewhere else. And this is how we can also think of a junk dimension.

**Note** We call it ==junk dimension== usually only internally. While talking to business users we can refer to as ==transactional indicator dimension==  or ==transactional flag dimension==.

Let's assume we have the following fact table. We see we have these three indicators and now what we could do is just replace them all with a foreign key. So in this case, one, seven and 12. And of course, we now need to create all of the possible combinations and have also this flag, primary key. So in that case, we can create the reference from the fact to the dimension. And then we have all of these possible combinations in this dimensional table. And now of course, this can be used very easily for the users. So we can use the payment type to group the data by the amount, we can use the other attributes such as the bonus and so on. And this is our goal with our dimensional modeling. So this works now very well, but now we have to be a little bit careful because sometimes, there can be quite a lot of combinations. 

![[Dimension Junk dimensions example 2.png]]

Assume for example, that we have in this case, three different columns. And in the first one, there are three potential values. In the second one, only two, and in the last one, also only two. In that case, we have a total number of combinations which is 12. So of course then, our dimensional table should have now 12 rows to reference all of these different combinations. But sometimes if we have a lot of value, so a lot of indicators, this can increase exponentially. So we can end up with a lot of combinations. For example, if we have nine indicators, with four dimensions, we have over 260,000 combinations.

![[Dimension Junk dimensions example 3.png]]

Possible Solution to exponential combinations in junk dimension
- Extract only available combinations of fact table
- Create two or more junk dimensions
	- For Example for one table with 9 indicators with 4 combinations -> 4^9 = 262144
	- But by splitting the indications into two tables with 4 and 5 indicators, the total size decreases to 4^5 = 1024.