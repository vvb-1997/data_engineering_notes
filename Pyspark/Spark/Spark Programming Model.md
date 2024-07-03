# Configure Spark Log

Spark project is using Log4J, and we also want to use the same for our Spark applications. We want to use log4j for logging messages from our application. We avoid python logger because collecting python log files is not integrated with the Spark. Spark is designed to work with the log4j, and most of the cluster managers are also comfortable managing log4j files.

You can still use python logger to send log messages to the Console. However, if you want to collect your python logs to a central location, then you must configure remote log Handlers and use them in your PySpark programs. Setting and using Python remote log handlers could be an unnecessary complexity. Log4J can be used with PySpark by configuring Log4J in three-step process. 
- Create a Log4J configuration file.
- Configure Spark JVM to pickup the Log4J configuration file.
- Create a Python Class to get Spark's Log4J instance and use it in PySpark.
## Create a Log4J configuration file

```properties
# Set everything to be logged to the console
log4j.rootCategory=WARN, console

# define console appender
log4j.appender.console=org.apache.log4j.ConsoleAppender
log4j.appender.console.target=System.out
log4j.appender.console.layout=org.apache.log4j.PatternLayout
log4j.appender.console.layout.ConversionPattern=%d{yy/MM/dd HH:mm:ss} %p %c{1}: %m%n

#application log
log4j.logger.vinay.bhat.spark.examples=INFO, console, file
log4j.additivity.vinay.bhat.spark.examples=false
  
#define rolling file appender
log4j.appender.file=org.apache.log4j.RollingFileAppender
log4j.appender.file.File=${spark.yarn.app.container.log.dir}/${logfile.name}.log
#log4j.appender.file.File=app-logs/hello-spark.log
#define following in Java System
# -Dlog4j.configuration=file:log4j.properties
# -Dlogfile.name=hello-spark
# -Dspark.yarn.app.container.log.dir=app-logs

log4j.appender.file.ImmediateFlush=true
log4j.appender.file.Append=false
log4j.appender.file.MaxFileSize=500MB
log4j.appender.file.MaxBackupIndex=2
log4j.appender.file.layout=org.apache.log4j.PatternLayout
log4j.appender.file.layout.conversionPattern=%d{yy/MM/dd HH:mm:ss} %p %c{1}: %m%n
  
# Recommendations from Spark template
log4j.logger.org.apache.spark.repl.Main=WARN
log4j.logger.org.spark_project.jetty=WARN
log4j.logger.org.spark_project.jetty.util.component.AbstractLifeCycle=ERROR
log4j.logger.org.apache.spark.repl.SparkIMain$exprTyper=INFO
log4j.logger.org.apache.spark.repl.SparkILoop$SparkILoopInterpreter=INFO
log4j.logger.org.apache.parquet=ERROR
log4j.logger.parquet=ERROR
log4j.logger.org.apache.hadoop.hive.metastore.RetryingHMSHandler=FATAL
log4j.logger.org.apache.hadoop.hive.ql.exec.FunctionRegistry=ERROR
```

Log4J has got three components.
- Logger 
	- The Logger is the set of APIs which we are going to use from our application.
- Configurations 
	- Configurations are defined in the log4j properties file, and they are loaded by the loggers at runtime.
- Appenders
	- Appenders are the output destinations, such as Console and a log file. These Appenders are also configured in the log4j properties file.

Log4j configurations are defined in the hierarchy. And the topmost hierarchy is the root category. So, I have defined the root category at the top. For any hierarchy or category, we define two things. The first thing is the log level, and the second thing is the list of Appenders.

Log4J supports multiple log levels, such as DEBUG, INFO, WARN, and ERROR. Here the root category is set as WARN. So, at the topmost level, we only want to see the warnings and errors and we want these messages to go to the Console. So, appender is set as Console.

```properties
log4j.rootCategory=WARN, console
```

The next thing is to define the console appender. All these configurations are standard, and they remain the same in most of the projects. So, these two sections together will set the root level log4J configuration, and they will stop all the log messages sent by the Spark and other packages except warning and errors. So, you will get a clean and minimal log output.

```properties
log4j.appender.console=org.apache.log4j.ConsoleAppender
log4j.appender.console.target=System.out
log4j.appender.console.layout=org.apache.log4j.PatternLayout
log4j.appender.console.layout.ConversionPattern=%d{yy/MM/dd HH:mm:ss} %p %c{1}: %m%n
```

However, for the behavior of our application, we are defining the second log level specific to my application naming this level as `vinay.bhat.spark.examples`. This is the name used when using the Logger in my application. So, the application level log will be set to INFO. 

```properties
log4j.logger.vinay.bhat.spark.examples=INFO, console, file
log4j.additivity.vinay.bhat.spark.examples=false
```

Logs are send to console and file appenders. We wanted to send the root level logs to the Console only. But application-level logs to go to the console and log file both. As console appender are already defined we only setting the file appender here.

```properties
log4j.appender.file=org.apache.log4j.RollingFileAppender
log4j.appender.file.File=${spark.yarn.app.container.log.dir}/${logfile.name}.log
```

All these log4j configurations are standard, and it should work well for most of your projects. We have only two places to tweak.

The first one is the application logger name. (`vinay.bhat.spark.examples`)
The second one is the log file location and the log file name. (`${spark.yarn.app.container.log.dir}/${logfile.name}.log`)

The first variable is to identify the log file directory location (`${spark.yarn.app.container.log.dir}`). 
And the second variable is to identify the log file name (`${logfile.name}.log`). 

We already learned that the Spark follows a master-slave architecture, and your application is going to run as one driver and multiple executor processes. And all these executors are individual JVMs running on different machines in your cluster.

Driver is running on one machine, and all your executors are running at different computers. But you do not have any control over it. All of that is managed by the cluster manager. The driver and executers all creating application log files on their local machines. So your log file is also distributed. How are you going to collect it? The cluster manager will manage the log.

Now let's assume we are using YARN. So, YARN is going to collect all these log files and keep it at one predefined location so you can read them and investigate them quickly. But for YARN to find these log files, you should make sure that your application JVM is creating logs at a well defined fixed location on each machine. 

If we want YARN manager to collect your application logs, then you must ensure to create log files in `'spark.yarn.app.container.log.dirctory'` This is a variable that YARN is going to use for log collection. And this configuration should be defined appropriately by your cluster admin. If application is creating log files at some other location, YARN is not going to collect it. Or, if your cluster admin forgot to define a value for this variable, YARN is not going to manage the logs.

![[Spark Programming Model 1.png]]

The next variable is the log file name. We are using a variable name for the log file name because we want to control the log file name and do not wish log4j to use a default and random name.

The rest of the settings below this level are some recommendations by the Spark Log4J configuration template.

```properties
log4j.logger.org.apache.spark.repl.Main=WARN
log4j.logger.org.spark_project.jetty=WARN
log4j.logger.org.spark_project.jetty.util.component.AbstractLifeCycle=ERROR
log4j.logger.org.apache.spark.repl.SparkIMain$exprTyper=INFO
log4j.logger.org.apache.spark.repl.SparkILoop$SparkILoopInterpreter=INFO
log4j.logger.org.apache.parquet=ERROR
log4j.logger.parquet=ERROR
log4j.logger.org.apache.hadoop.hive.metastore.RetryingHMSHandler=FATAL
log4j.logger.org.apache.hadoop.hive.ql.exec.FunctionRegistry=ERROR
```

## Configure Spark JVM to pickup the Log4J config file.

Spark has a complex mechanism to read configuration settings. However, at a high level, every Spark application would look for a SPARK_HOME environment variable. If you have a SPARK_HOME configured, then Spark will look into the `conf` directory of your SPARK_HOME.

We will be using `spark-defaults.conf` file to configure the value of our log4j variables. Spark is a JVM based application written in Scala, and it runs in a Java Virtual Machine. So, add `Spark.driver.extraJavaOptions` configuration to pass on some variables to the Spark JVM.

```properties
spark.driver.extraJavaOptions -Dlog4j.configuration=file:log4j.properties -Dspark.yarn.app.container.log.dir=app-logs -Dlogfile.name=hello-spark
```

The first variable tells the log4j configuration file location and name. Log4j configurations are defined in the log4j.properties file in the current directory. And that is why we added the log4j properties file in the project root directory. `-Dlog4j.configuration=file:log4j.properties`

The second variable sets the log file directory location. `-Dspark.yarn.app.container.log.dir=app-logs`

And the third one is the log file name. `-Dlogfile.name=hello-spark`

We are setting these variables in the JVM, but all these three variables will reach to the log4j Logger. That's how the log4j is designed.

## Create a Python Class to get Spark's Log4J instance

```python
class Log4J:

    def __init__(self, spark):
        root_class = "vinay.bhat.spark.examples"
        conf = spark.sparkContext.getConf()
        app_name = conf.get("spark.app.name")
        log4j = spark._jvm.org.apache.log4j
        self.logger = log4j.LogManager.getLogger(root_class + "." + app_name)

    def warn(self, message):
        self.logger.warn(message)
  
    def info(self, message):
        self.logger.info(message)

    def error(self, message):
        self.logger.error(message)

    def debug(self, message):
        self.logger.debug(message)
```

# Initialize Spark Session

Every Spark program will start a driver. And then, the driver is going to start the executors for doing most of the work. So, the first thing in a Spark program is to create a SparkSession object, which is your Spark Driver.

![[Spark Session 1.png]]

SparkSession is your driver. However, when you are writing a spark program, then we must create a SparkSession because it is not already provided. Creating a SparkSession is straightforward. You will declare a variable and use `SparkSession.builder().getOrCreate()`. It returns SparkSession object, and we often name it as the spark.

SparkSession is a Singleton object, so each Spark application can have one and only one active spark session. And that makes a perfect sense because the SparkSession is your driver, and you cannot have more than one driver in a spark application.

Spark is a highly configurable system, and hence your SparkSession is also highly configurable. The builder method returns a Builder object, which allows you to configure your SparkSession and finally use the `getOrCreate()`. Some configurations, such as `AppName`, and `master` are the most commonly used configs by almost every application. So, we have direct methods to specify them.

```python
spark = SparkSession.builder \
        .appName("Hello Spark") \
        .master("local[3]") \
        .getOrCreate()
```

However, we also have some overloaded `config()` methods for all other configurations. The cluster manager config is defined as a spark master. We will use local multithreaded JVM with three threads. So we created a Spark session, and that means we created our spark driver. We will be using this driver to do some data processing, and once we are done with it, we should stop the driver. That's how a typical spark program is structured.

# Configuring Spark Session

Configuring Spark Session
- Environment variables (mainly used by the cluster admins.)
- SPARK_HOME\\conf\\spark-defauIts.conf (ignored by developers)
- spark-submit command-line options `
	- `bin\spark-subrit --master loca1[3] --conf "spark.app.naæ=Hello Spark" --conf spark.eventLog.enabled=false HelloSpark.py`
- SparkConf Object
	- Create a SparkConf object. Now, you can use the `set()` method to set the application name config. You can set as many configs as you want. 
	- However, you must know the actual config property name string. For example, the spark application name config string is `"spark.app.name"` Similarly, `"spark.master"` is the string config name for the spark master.  
	- Once you have your SparkConf ready with all the desired configs, you can pass it to the session builder.

```python
from pyspark import SparkConf

conf = SparkConf()
conf.set("spark.app.name", "Hello Spark")
conf.set("spark.master", "local[3]")

spark = SparkSession.builder \
	.config(conf=conf) \
	.getOrCreate()
```
## Spark Session Config precedence

![[Spark Session Config 1.png]]

When to use which method? Should I use spark-defaults, or the command line or the spark conf in my application? Leave the environment variables and spark-defaults for your cluster admins. Do not make your application dependent on these two methods of setting configurations. So you should be using either command line or spark conf. 

Spark properties can be grouped into two categories.
- The first category is for deployment-related configs such as `spark.driver.memory`, and `spark.executor.instances`. This kind of config is going to depend on which cluster manager and deploy mode you choose. So, we often set them through the spark-submit command line and avoid setting them from the application.
- The other category of configs is to control the Spark Application runtime behavior such as `spark.task.maxFailures`. So, we often set them through `SparkConf`.

Look at the Spark submit help. Wherever you see specially named flags, such as --master, --driver-memory, --num-executors, etc. You should be using them with the spark-submit, and rest all configs are the right candidate for `SparkConf`.

## Dynamic Spark Session Config

All these configurations are hardcoded in my application. In some cases, you may want to avoid hard-coding specific configs and keep it outside your application code. or example, hardcoding the master is a big problem.

While developing and debugging my application, we want to use the local master. However, once  application is ready, we want to deploy it in a cluster environment. And we don't know yet if I am going to use it on YARN or on a Kubernetes cluster. So, I do not want to hard-code this config. How do you deal with this requirement?

We have a couple of approaches to handle it. The first approach is to keep these configurations in a separate file and load it from the file at run-time. Create a Python config file named `spark.conf`. Now, create a section and place configurations in this file. The configs are key-value pair separated by the '=' symbol.

```ini
[SPARK_APP_CONFIGS]
spark.app.name = HelloSpark
spark.master = local[3]
spark.sql.shuffle.partitions = 2
```

Create a function which will load the configurations from the `spark.conf` file and return a `SparkConf` object. We will create a new `SparkConf` object. Read the configs from the file. Loop through the configs and set it to the `SparkConf`. Finally, return the `SparkConf` object. 

```python
import configparser
from pyspark import SparkConf

def get_spark_app_config():
    spark_conf = SparkConf()
    config = configparser.ConfigParser()
    config.read("spark.conf")
  
    for (key, val) in config.items("SPARK_APP_CONFIGS"):
        spark_conf.set(key, val)

    return spark_conf
```

Now, we need to come back to the main and use it in the spark builder config method. We fixed the hardcoding problem. Now you can add more and more configs in this `spark.conf` file, and your application will load it at runtime and use them to configure your SparkSession.

```python
conf = get_spark_app_config()

# Create a SparkSession
spark = SparkSession.builder \
	.config(conf=conf) \
	.getOrCreate()
```

# Spark Data Processing

Typical data processing is a three-step process.
- Read the data
- Processes it according to your business requirement
- Write the outcome of your processing
## Read Data

We start with the Spark Session and call the read() method, which returns a `DataFrameReader` object. And the `DataFrameReader` is your gateway to read the data in Apache Spark. It allows you to read the data from a variety of sources, and you can see all these functions here. You can read a CSV, JDBC, JSON, ORC, Parquet, and Text File and many more

However, we want to read a CSV file, so let's use the `csv()` method. All you need to do is to pass the file path. Spark will load the data into a Spark DataFrame. CSV data files are plain text data files. They offer a lot of flexibility, and hence they are complex to read correctly.

```python
spark.read.csv(data_file)
```

Look at my data file. The first row is a header row, and the rest of the rows are data rows. The `DataFrameReader` doesn't know these details. So, we must tell this information to the `DataFrameReader` so it can read the file correctly using the `DataFrameReader` `option()` method. The option method takes a key-value pair, and you can get a list of all available CSV options from Spark Documentation. 

[CSV Files - Spark 3.5.1 Documentation (apache.org)](https://spark.apache.org/docs/3.5.1/sql-data-sources-csv.html)

```csv
"Timestamp","Age","Gender","Country","state","self_employed","family_history","treatment","work_interfere","no_employees","remote_work","tech_company","benefits","care_options","wellness_program","seek_help","anonymity","leave","mental_health_consequence","phys_health_consequence","coworkers","supervisor","mental_health_interview","phys_health_interview","mental_vs_physical","obs_consequence","comments"
2014-08-27 11:29:31,37,"Female","United States","IL",NA,"No","Yes","Often","6-25","No","Yes","Yes","Not sure","No","Yes","Yes","Somewhat easy","No","No","Some of them","Yes","No","Maybe","Yes","No",NA
2014-08-27 11:29:37,44,"M","United States","IN",NA,"No","No","Rarely","More than 1000","No","No","Don't know","No","Don't know","Don't know","Don't know","Don't know","Maybe","No","No","No","No","No","Don't know","No",NA
2014-08-27 11:29:44,32,"Male","Canada",NA,NA,"No","No","Rarely","6-25","No","Yes","No","No","No","No","Don't know","Somewhat difficult","No","No","Yes","Yes","Yes","Yes","No","No",NA
```

My data file comes with a header row, so I want to set the header option. The default value is false, and I am going to set it to true. Now the `DataFrameReader` will skip the first row but use it to infer the column names and create a Spark DataFrame.

```python
spark.read \
	.option("header", "true") \
	.csv(data_file)
```

Spark DataFrame is a two-dimensional table-like data structure that is inspired by Pandas DataFrame. They are a distributed table with named columns and well-defined schema. That means each column has a specific data type such as integer, float, string, timestamp, etc.

DataFrame is similar to a database table. And most of the operations that you are going to perform on these DataFrames  are also similar to database table operation using rows and columns.

A DataFrame must have two things. 
- Column Names
- Schema - data types for each column.

So, the `DataFrameReader` is going to use the header row and infer the column names.
For the schema we can use the `inferSchema` option. This option will allow the `DataFrameReader` to read a portion of the file and make an intelligent guess about the data types for all the columns. This option works well in some cases, but it fails to infer the correct data types in most of the cases.

```python
spark.read \
	.option("inferSchema", "true") \
	.csv(data_file)
```

This code is going to read a CSV data file, use the first row to infer the column names, make an intelligent guess for the column data types, and finally return a DataFrame.

```python
spark.read \
	.option("header", "true") \
	.option("inferSchema", "true") \
	.csv(data_file)
```