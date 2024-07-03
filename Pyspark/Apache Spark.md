Apache Spark is a distributed data processing framework, and this diagram represents the Spark Ecosystem. So, the Spark ecosystem is designed in two layers. The bottom layer is the Spark Core Layer. Then we have the next layer, a set of DSLs, libraries, and APIs. These two things together make the Spark ecosystem.

![[Spark 1.png]]

The Spark core layer itself has got two parts. A distributed Computing Engine And a set of core APIs And this whole thing runs on a cluster of computers to offer you distributed data processing. However, Spark does not manage the cluster. It only gives you a data processing framework. So, you are going to need a **cluster manager**. People use different names for Cluster Manager, such as Resource Manager or the Container Orchestrator. However, they all offer similar services to Spark, and Spark is designed to work with both. Spark was initially based on Hadoop MR, and it was adopted in the Hadoop platform. So, the Hadoop YARN resource manager is the most commonly used cluster manager for Spark. However, you have other options, such as Mesos and Spark Standalone Cluster Manager. The newer versions of Spark are also compatible with Kubernetes as a Cluster Orchestrator.

Similarly, Spark also doesn't come with an in-built storage system. And it allows you to process the data, which is stored in a variety of storage systems. The most popular and commonly used storage systems are HDFS, Amazon S3, Azure Data Lase Storage, Google Cloud Storage, and the Cassandra file system.

![[Spark 2.png]]

Apache Spark does not offer Cluster Management and Storage Management. All you can do with Apache Spark is to run your data processing workload. And that part is managed by the Spark Compute Engine. So the compute engine is responsible for example, breaking your data processing work into smaller tasks, scheduling those tasks on the cluster for parallel execution, providing data to these tasks, managing and monitoring those tasks, provide you fault tolerance when a job fails.

And to do all these, the core engine is also responsible for interacting with the cluster manager and the data storage manager. So the Spark compute engine is the core that runs and manages your data processing work and provides with a seamless experience. All the user needs to do is submit data processing jobs to Spark, and the Spark core will take care of everything else.

# Spark Core

This layer is the programming interface layer that offers you the core APIs in four major languages.
- Scala
- Java
- Python,
- R programming language.

These are the APIs that we used to write data processing logic during the initial days of Apache Spark. However, these APIs were based on resilient distributed datasets (RDD) and are tricky to learn and use for their day-to-day data processing work. These APIs are the most complicated way to work with Apache Spark. They also lack some performance optimization features. And the Spark creators are now recommending avoiding them as much as possible. However, these APIs can offer you the highest level of flexibility to solve some complex data processing problems.

The topmost layer is the prime area of interest for most Spark developers and data scientists. This layer is again a set of libraries, packages, APIs, and DSL (Domain Specific Language). These are developed by the Spark community over and above the Core APIs. Internally, all of those will be using Spark Core APIs, and ultimately things will go to the Spark Compute Engine. The topmost API layer is grouped into four categories to support four different data processing requirements. However, this is just a logical grouping, and there is no rigid boundary. Things are going to overlap in most of the real-life projects.

-  Spark SQL and Spark DataFrame/Dataset APIs
	- Spark SQL allows you to use SQL queries to process your data. So that part is quite simple for those who already know SQL. 
	- Spark DataFrame/Dataset will allow you to use functional programming techniques to solve your data crunching problems. 
	- These APIs are available in Java, Scala, and Python. Both of these together can help you resolve most of the structured and semi structured data crunching problems.

-  Spark Streaming libraries
	- Spark Streaming libraries allow to process a continuous and unbounded stream of data

- Mllib (Machine Learning)
	- Mllib are a set of libraries specifically designed to meet your machine learning, deep learning, and AI requirements.
	
- Graph Processing libraries
	- Graph Processing libraries allow you to implement Graph Processing Algorithms using Apache Spark.
# Benefits of Spark 
- Abstraction
	- Spark will abstract away that you are coding to execute your program on a cluster of computers. 
	- In the best-case scenario, you will work with tables and use SQL queries. You will feel like working with a database. In the worst-case scenario, you will be working with RDDs or data frames. You will feel like working with Java/Scala collections or a python data frame.
	- Everything else, all the complexities of distributed storage, computation, and parallel programming, is abstracted away by the Spark core.
- Unified Platform
	- Spark combines the capability of SQL queries, Batch Processing, Stream Processing, Structured and semi structured data handling, Graph processing, machine learning, and deep learning. 
	- All of this in a single framework using your favorite programming language.
	- You can mix and match them to solve many sophisticated requirements.
- Ease of Use
	- Comparing it with old Hadoop and MapReduce code, Spark code is much shorter, simpler, and easy to read and understand. You also have a growing and evolving ecosystem to offer ready-to-use libraries, algorithms, and integration with a wide variety of other systems.

And these three things make Spark a super popular distributed computing platform for data processing.

# Databricks

Benefits of Databricks over Apache Spark

- Spark on the Cloud Platform
	- Databricks brings Apache Spark to the Cloud Platform. In fact, Databricks product is known as Databricks Cloud. And the Databricks Cloud is only available on the Cloud platforms. But they are available on AWS, Azure, and Google Cloud. So Databricks brings Apache Spark on all the major cloud platforms.
- Spark Cluster Management
	- Databricks offers you an easy way to configure, launch, and shut down the clusters in the cloud environment.
- Notebooks and Workspace
	- These workspace and Notebooks are as good as IDE for spark development. You can use the notebooks to develop, share and collaborate with your colleagues for Spark development. You can also integrate Databricks Notebooks with configuration management tools such as Git.
- Administration Controls
	- Administration Controls to manage access for your workspace, clusters, and storage and implement security controls.
- Optimized Spark
	- Databricks Spark runtime is 5x faster than the standard Apache Spark runtime.
- Databases/Tables and Catalog
	- Databricks Spark runtime is 5x faster than the standard Apache Spark runtime. Databricks also offers you an integrated Hive meta-store to store metadata, allowing you to create Databases, Tables, and Views using Spark SQL.
- Databricks SQL Analytics
	- Databricks also offers you an advanced SQL query engine called **Photon**, which allows you to gain data warehouse grade performance of your SQL queries and dashboards on top of the data lake infrastructure.
- Delta Lake Integration
	- Seamless Delta Lake integration that offers ACID transactions and Data consistency features to your Application workload and Spark SQL.
- ML Flow
	- ML Flow which allows us to manage the machine learning life cycle, including experimentation, deployment, model registry, etc.
- Industry vertical accelerators
	- Databricks is also offering some industry-specific solution accelerators for rapid adoption and solving common industry-specific problems.

Spark DataFrames

# Reference 

[GitHub - LearningJournal/Spark-Programming-In-Python: Apache Spark 3 - Spark Programming in Python for Beginners](https://github.com/LearningJournal/Spark-Programming-In-Python)