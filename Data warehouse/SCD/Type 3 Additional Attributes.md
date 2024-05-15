- Type 1 Static
- Type 2 Default strategy to maintain reflect history
- Type 3 In between: Switching back & forth between versions

And this is also now different from the previous implementations because now instead of adding an additional role we want to add an additional column. And this additional column is just now containing the previous value. So this is basically now the historical value. And in the normal categorical value we just have the current state. So with those two columns we can now reflect the two different states. And the great thing is that now we cannot only use that category in our analysis. So this is the current state but we can switch also to the previous state and aggregate the data with this previous state. And this is now something that is used usually if we have some significant major changes that are usually planned and are happening all at one time. A typical example is the restructuring in an organization. So for example, we've decided in the company that all of the categories are restructured or maybe also the hierarchies in our organization have changed the regions are now different and then we can implement the strategy. And this is now great because with that we can now switch back and forth between the previous historic state and the current state.
![[Dimension SCD Type 3 example 1.png]]

Properties of Type 3:
- Instead of adding a row we add a column
- Typically used for significant changes at a time (e.g. restructurings in organizations)
- Enables switching between historic / current view

Limitations
- Not suitable for frequent or unpredictable changes -> better Type 2
- Minor changes -> better Type 1