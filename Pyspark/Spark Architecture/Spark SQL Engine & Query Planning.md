Apache Spark gives us two prominent interfaces to work with data.
- Spark SQL
	- Spark SQL is compliant with ANSI SQL:2003 standard. So we can think of it as standard SQL.
- Dataframe API
	 DataFrames are function APIs, and they allow to implement functional programming techniques to process the data.

Other than these two, we also have Dataset API available only for Scala and Java. The Dataframe API internally uses Dataset APIs, but these Dataset APIs are not available in PySpark. If we are using Scala or Java, we can directly use Dataset APIs. But Spark recommends using Dataframe APIs and avoid using Dataset. as we can do almost everything using DataFrames

We can write spark code using Dataframe APIs, or can write SQL expressions. Spark looks at your code in terms of jobs. So if we have a Dataframe API code, Spark will take action and all its preceding transformations to consider it a single Job. Similarly, if we write a SQL expression, Spark considers one SQL expression as one Job.

Spark code is nothing but a sequence of Spark Jobs and each Spark Job represents a logical query plan. This first logical plan is a user-created logical query plan. Now this plan goes to Spark SQL engine for Dataframe APIs or SQL. For Spark, both are nothing but a Spark Job represented as a logical plan.
# Analysis phase

The Spark SQL Engine will process the logical plan in four stages. The Analysis stage will parse the code for errors and incorrect names. For example, let's assume the code represents the following SQL. Spark doesn't know if the column is a valid column name and the data type of this column. So the Spark SQL engine will look into the Catalog to resolve the column name and its data type.

For example, assume we have a SQL like this. Then Spark SQL engine might also apply an implicit type casting to the `product_qty` to perform and validate the addition operation.

```SQL
SELECT product_qty + 5 FROM sales;
```

The Analysis phase will parse your code and create a fully resolved logical plan. We might see an Analysis exception if the column names do not resolve or have some incorrect typecasting, or used an invalid function name, etc. If the code passed the Analysis phase, that means we have a valid code, and we are not using something that Spark doesn't know or cannot resolve.

# Logical optimization.

The logical optimization phase applies standard rule-based optimizations to the logical plan. Here are some examples of logical optimization to create an optimized logical plan.
- Constant folding
- Predicate pushdown
- Partition pruning
- Null propagation
- Boolean expression simplification

Then this optimized logical plan goes into the next phase of physical planning.
# Physical planning

Spark SQL takes a logical plan and generates one or more physical plans in the physical planning phase. Physical planning applies cost-based optimization. So the engine will create multiple plans, calculate each plan's cost, and finally select the plan with the least cost.

At this stage, they mostly use different join algorithms to create more than one physical plan. For example, they might create one plan using broadcast join and another using Sort merge, and one more using shuffle hash join. Then they apply a cost to each plan and choose the best one.
# Code generation

So the best physical plan goes into code generation, and the engine will generate Java byte code for the RDD operations in the physical plan. And that's why Spark is also said to act as a compiler because it uses state-of-the-art compiler technology for code generation to speed up execution.

![[Spark SQL Engine & QE 1.png]]