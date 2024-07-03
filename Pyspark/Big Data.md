# Understanding Big Data

Technologies may evolve, but the requirement for processing more and more data at a faster speed will keep becoming more critical. But how do we create data processing applications? We have used RDBMS technology for many decades. Some popular RDBMS systems are Oracle, SQL Server, PostgreSQL, MySQL, Teradata, and Exadata. They all come with different capabilities, but the core technology is the same. 

These RDBMS systems offered us three main features to help us develop Data Processing applications. 
- SQL - An easy Data Query Language
- Scripting Languages such as PL/SQL and Transact SQL
- Interface for other programming languages such as JDBC and ODBC

So we used SQL for querying data and PL/SQL for doing things that we couldn't do using SQL. They also offered interfaces such as ODBC/JDBC so we could interact with data using the  programming languages. Things were perfect. We could create data processing applications using these technologies. 

But then, the data itself started to evolve. In the beginning, we saw data in rows and columns. Here is an example. COBOL stored and accessed this data in plain text files such as CSV files. RDBMS such as Oracle stored this data in an advanced file format like DBF files. We do not care about the file format used by RDBMS, but we see data as a table of rows and columns. But with time, we developed some new data formats such as JSON and XML.

RDBMS was not ready to store and process these JSON and XML data files. They needed us to define a row-column structure and then store it in RDBMS. However, many RDBMS added this capability over some time and started to allow storing and processing JSON and XML data files. But by that time, we developed some new formats such as txt, pdf, doc, jpg, png, gif, mp3, mp4, and many more. RDBMS technology was not designed to handle such an expansion of file formats.
## Categories of Data
There are three categories of data. 
- **Structured**
	- The structured data comes in a standardized format such as row-column table format.
- **Semi-Structured**
	- The semi-structured data does not obey the tabular structure. However, it has another well-defined key/value structures such as JSON or XML. 
	- They do not have a tabular structure. But they have some key/value structures.
- **Un-Structured** 
	- There are no definite pattern or structure. The unstructured data comes in text files, pdf and other documents, images, and video files. With the growth of the internet, social media, mobile apps, etc., businesses are now collecting more semi-structured and unstructured data.
## Big Data Problem
Modern data processing application requires handling the following problems. 
- **Variety**
	- They must be able to handle all three varieties of data i.e. methods to store and process structured, semi-structured, and unstructured data.
- **Volume**
	- The overall data volume is relatively high these days. Companies collect terabytes and petabytes of data in a short period. So we need capabilities to handle large volumes of data.
- **Velocity**
	- Modern data is being generated at a very high speed. I mean, it takes very little time for an organization to collect petabytes of volume. And they do not have weeks and months to process these volumes. We want to process it faster in hours and minutes. So the velocity of collecting data is very high, and the requirement of processing velocity is also high.
These three problems combined are also known as the **Big Data problem**.

## Big Data Platform Requirements
- Store High Volumes of data arriving at a higher velocity 
- Accommodate structured, semi-structured, and unstructured data variety 
- Process high volumes of a variety of data at a higher velocity

## Approaches to solving Big Data Problem
- Monolithic Approach
	- The monolithic approach designs one large and robust system that handles all the requirements. Teradata and Exadata are examples. These two systems mainly support only structured data.
	- A monolithic system uses one massive system with a vast capacity of the following resources. CPU Capacity RAM Capacity and Disk Storage
- Distributed Approach
	- In a distributed approach, we take many smaller systems and bring them together to solve a bigger problem.
	- In distributed system uses a cluster of computers connected to work as a single system. The combined capacity of the cluster may be equal to or even higher than the single monolithic system.

## Monolithic VS Distributed
- **Scalability**
	- Scalability is the ability of the system to increase or decrease performance in response to the demand.
	- Let's say we have ten TB of data stored in both systems. And we have 100 concurrent users reading and writing data to the system. Assume both the systems are working perfectly fine. But over some time, the data volume and the concurrent users are increased. Both the systems have limited capacity.  I mean, the CPU, Memory, and disk size are fixed. As the data volume and concurrent users increase, these systems will reach their maximum capacity. What happens when they reach their maximum capacity? Can we increase the capacity? If yes, the system is scalable. If not, it is not scalable. 
	- Both the approaches are scalable but scaling a monolithic system is complex. You may have to call the vendor and request them to increase the capacity. They will take some time, bring some more chips, open the system and increase the tower's height. This approach is known as **vertical scalability**. 
	- In vertical scalability, you will increase the height of a single system adding more resources to one single system. On the other side, the distributed system is also scalable. 
	- But scaling a distributed system is as simple as adding a few more computers to the network. And this approach is known as **horizontal scalability**.
	- In horizontal scalability, you will add new members to the cluster and increase the length of the network. Horizontal scalability takes less time because you can easily buy some new systems. Vertical scalability requires coordination with the system vendor and help from them. So it may take a longer time.
- **Fault tolerance and high availability**
	- What happens if a CPU burns out, a network card fails, or the system's motherboard fails. The monolithic system will stop working. So the monolithic system may not tolerate a hardware failure. If a hardware component of a monolithic system fails, it may stop working, and your application will not remain available for your users. 
	- What happens if a computer fails in the cluster? Other computers remain working. Right? So a distributed system can tolerate many failures. A system failure in a cluster will only reduce the capacity, but the overall system remains working.
- **Cost-effectiveness**
	- The distributed architecture uses a cluster of computers. You can start with a small cluster and keep your initial investment as low as needed. You can add some more machines at a later stage as your requirement grows. You can even use an average quality machine available at a reasonable price. You can use a cloud environment to get machines at rental prices and create your cluster. These options make a distributed approach more cost-effective and economical. 
	- However, monolithic systems are expensive. Scaling them takes a lot of time, so you may have to start with a large machine even if you need a smaller one.

# Hadoop
Engineers evaluated the monolithic and distributed approaches to design a new data processing system and handle big data requirements. And the evaluations indicate that distributed system could be a better choice. And that's where a new system called Hadoop came into existence. 

Hadoop was a revolutionary Big Data processing platform that grabbed immense attention and massive adoption. So what is Hadoop? Hadoop came up as a new data processing platform to solve Big Data problems. 

The Hadoop platform was designed and developed in layers.
- Distributed cluster formation or Cluster Operating System (YARN)
- Data storage and retrieval on the distributed cluster or Distributed Storage (HDFS)
- Distributed data processing using Java programming language (Map-Reduce Framework)

So Hadoop was developed as an operating system of a distributed cluster. And that was the most critical capability. Let's try to understand. We can create data processing software, install it on one computer and run it there. The computer comes with an Operating system. Right? The operating system allows you to use the computer resources such as CPU, Memory, and Disk to run your applications. If you do not have an operating system on the computer, you cannot run any software on it. 

Similarly, when you create a cluster of computers, you need a cluster operating system. The cluster operating system will allow you to use the cluster resources such as CPU, Memory, and disk. But cluster operating system has more complex work to do. Because the CPU, Memory, and Disk on a cluster setup are spread across the computers. We are not using a single computer where everything is available on a single machine. A Cluster is a group of computers. And the cluster operating system makes it work like a single large computer. And that was the first capability of the Hadoop platform. It allowed us to use a cluster of computers as a single large computer. 

Hadoop also allowed us to store data and retrieve it back. The data was internally distributed on the hard disks on the cluster. However, Hadoop offered us a distributed storage system and allowed us to save and read the data file as we do it on a single machine.

The next critical feature of Hadoop was to allow us to write a data processing application in Java  and run it on the cluster. So Hadoop offered us a new distributed data processing framework known as Map-Reduce framework for processing large volumes of data. Hadoop platform was designed to use the cluster of computers as a single large machine. You can read/write and program data processing applications  and run them on the Hadoop cluster without realizing that you are working on a cluster. Hadoop simplified distributed computing for developers. It saved the developer community from learning the complexities of distributed computing and parallel processing. We develop a data processing application on Hadoop the same way we develop it for a monolithic system. But the program runs on a distributed computing platform and uses parallel processing. And that's why Hadoop grabbed immense attention and popularity. 

The Hadoop platform was designed and developed in layers. The core layers offered a cluster computing operating system, distributed storage, and Map-Reduce Framework. However, the Hadoop community developed many other tools over and above the Hadoop Core platform. Some of the most popular tools are listed here. Hive Database, HBase Database, Pig Scripting Language Sqoop data ingestion tools and Oozee workflow tool

## Hadoop VS Database

|                        | Database                       | Hadoop                                             |
| ---------------------- | ------------------------------ | -------------------------------------------------- |
| Data Storage           | Handles upto Terabytes of Data | Handles Petabytes of Data                          |
| Data Types             | Only Structed Data             | Structured, semi structured and Un-structured Data |
| Query language         | SQL                            | Hive SQL                                           |
| Scripting language     | Supports scripting language    | Apache Pig Scripting.                              |
| JDBC/ODBC connectivity | Supports                       | Supports                                           |
