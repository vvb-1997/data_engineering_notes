RDDs, DataFrames, and Spark SQL are internally powered by something known as the Spark SQL engine. The Spark SQL engine is a powerful compiler that optimizes the code and also generates efficient Java Bytecode.

![[Spark SQL Engine 1.png]]

The overall effort of the Spark SQL engine can be broken down into four phases.
- Analysis.
	- In this phase, the Spark SQL engine will read your code and generate an Abstract Syntax Tree for your SQL or the DataFrame queries. 
	- In this phase, code is analyzed, and the column names, table, or view names, SQL functions are resolved. 
	- There might be a runtime error shown as an analysis error at this stage when your names don't resolve.
- Logical optimization.
	- In this phase, the SQL engine will apply rule-based optimization and construct a set of multiple execution plans. 
	- Then the catalyst optimizer will use cost-based optimization to assign a cost to each plan. The logical optimization includes standard SQL optimization techniques such as predicate pushdown, projection pruning, boolean expression simplification, and constant folding.
- Physical planning
	- The SQL engine picks the most effective logical plan and generates a physical plan. The physical plan is nothing but a set of RDD operations, which determines how the plan is going to execute on the Spark cluster.
- Whole Stage Code Generation
	- This phase involves generating efficient Java bytecode to run on each machine. This phase was introduced in Spark 2.0 as part of the Project Tungsten.
	- The Project Tungsten was initiated to apply ideas from modern compilers and MPP databases and make Spark run more efficiently.