So a Snowflake schema is the more general concept because it allows for multiple levels. So basically a star schema is a Snowflake schema with only one level in the hierarchy. But even though this is generally or theoretically true, a Star schema is much more common actually. In Star schema we have some data redundancy , which again, we accept for the usability, and also usually better read performance. 

![[Snowflake Schema example.png]]

If we want to use a Snowflake schema, these redundancies, which is written out as text, can be reduced by just keeping the ID. So the number which takes much less disk space, so much less storage. Then all of that written out letters, and then all of the related information to the category we only need to have stored once, which again creates a second level of a hierarchy. And this now resembles not a Star anymore, but it looks more like a Snowflake. And again, this is where the name of this schema is coming from. And in contrast to the Star schema, the Snowflake schema is now also more normalized.

![[Snowflake Schema example normalized.png]]

# Advantages and Disadvantages of Snowflake Schema

| Advantage                                                                     | Disadvantage                       |
| ----------------------------------------------------------------------------- | ---------------------------------- |
| Less space (storage cost)                                                     | More complex                       |
| No (less) redundant data (easier to maintain/<br>less risk of corrupted data) | More joins (more complex SQL       |
| Solves write slow downs                                                       | Less performance Data Marts/ Cubes |
