Spark started with the goal of simplifying and improving the Hadoop Map/Reduce programming model. To achieve this goal, Spark came up with the idea of RDD - Resilient Distributed Dataset. The notion of RDD was revolutionary, and it created the core foundation of Apache Spark.

Spark added a higher level of APIs such as Dataset APIs and DataFrame APIs. To simplify our life, they also came up with the a SQL layer and a catalyst optimizer.

![[Spark Structured API 1.png]]

## RDD APIs
The RDD is at the core. You can use RDD APIs to develop your applications. However, it is the most challenging tool to learn and use. They offer you some additional flexibility, but they lack all the optimization brought to you by the catalyst optimizer. However, the Spark community is not recommending to use RDD APIs.
[[Spark RDD APIs]]
## Catalyst Optimizer.

So we write code using Spark SQL, DataFrame APIs, and Dataset APIs. This code is then submitted to Spark for execution. However, the code passes through the Catalyst Optimizer, which decides how it should be executed and lays out an execution plan. 
## Spark SQL, DataFrame & Dataset APIs,

[[Spark SQL]]
Spark SQL is the most convenient option to use. So, you should prefer using it wherever applicable. The Spark SQL is excellent, and it can do a lot of the work for you. However, a single SQL expression or even a SQL script with a series of SQLs may not be helpful in many scenarios. You do not get some necessary facilities like debugging, implementing application logs, unit testing, and other obvious capabilities that a programming language brings on the table.

Spark SQL is useful to catch some low hanging fruits. However, a sophisticated data pipeline is going to push you to use DataFrame APIs and rely more on your favorite programming language. And that's where the DataFrame APIs are your obvious choice.

The Dataset APIs are the language-native APIs in Scala and Java. These APIs are strongly typed objects in your JVM based language such as Scala. And they are not at all available in dynamically typed languages such as Python.