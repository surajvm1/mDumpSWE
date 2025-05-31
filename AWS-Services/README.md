
# AWS_Services


#### EC2 Spot Instances

A Spot Instance is an instance that uses spare EC2 capacity that is available for less than the On-Demand price. Because Spot Instances enable you to request unused EC2 instances at steep discounts, you can lower your Amazon EC2 costs significantly. The hourly price for a Spot Instance is called a Spot price. The Spot price of each instance type in each Availability Zone is set by Amazon EC2, and is adjusted gradually based on the long-term supply of and demand for Spot Instances. Your Spot Instance runs whenever capacity is available. [Spot instances _al](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-spot-instances.html)

[EC2 burstable instances _al](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/burstable-performance-instances.html), [Burstable credits _al](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/burstable-credits-baseline-concepts.html): 
- Many general purpose workloads are on average not busy, and do not require a high level of sustained CPU performance. 
- These low-to-moderate CPU utilization workloads lead to wastage of CPU cycles and, as a result, you pay for more than you use. To overcome this, you can leverage the low-cost burstable general purpose instances, which are the T instances. Simply put: Burstable instances are designed for scenarios where an instance is typically idle, or has low CPU utilization with occasional spikes in usage. 
- The EC2 burstable instances consist of T4g, T3a and T3 instance types, and the previous generation T2 instance types.

#### Redshift

Redshift is a clustered data warehouse and each cluster can house multiple databases. As expected, each database contains multiple objects like tables, views, stored procedures, etc. It is logical to expect that the data tables are stored across multiple nodes.

A node is a compute unit with dedicated CPUs, memory and disk. Redshift has two types of nodes: **Leader** and **Compute**. The Leader node manages data distribution and query execution across Compute nodes. Data is stored on Compute nodes only. 
Slice is logical partition for disk storage. Each node has multiple slices which allow parallel access and processing across slices on each node. The number of slices per node depends on the node instance types. [Redshift architecture _al](https://towardsdatascience.com/amazon-redshift-architecture-b674513eb996)

In Redshift, Vacuum is a resource-intensive operation. Re-sorts rows and reclaims space in either a specified table or all tables in the current database. Users can access tables while they are being vacuumed. You can perform queries and write operations while a table is being vacuumed, but when data manipulation language (DML) commands and a vacuum run concurrently, both might take longer. If you run UPDATE and DELETE statements during a vacuum, system performance might be reduced. VACUUM DELETE temporarily blocks update and delete operations. [Redshift Vacuum performance _al](https://repost.aws/knowledge-center/redshift-vacuum-performance). 

According to Redshift Documentation, You can only add one column in each ALTER TABLE statement. Only way to add multiple columns is executing multiple ALTER TABLE statements.

In Redshift, max limit to a varchar/jsonvarchar datatype is 65535 bytes. In PostgreSQL, where we have this data type called **Text** which can store unlimited char values. 

Note that, 
- **'Tag'** is a reserved word in Redshift, if you like to use reserved words as column names or aliases you need to use delimited identifiers (double quotes). Hence to create column in a table with name tag, instead of using this: alter table schema.tablename add column tag varchar(100), you have to use this: alter table schema.tablename add column "tag" varchar(100)
- A table's **distkey** is the column on which it's distributed to each node. Rows with the same value in this column are guaranteed to be on the same node. 
  - DISTKEY in Redshift determines how rows are distributed across compute slices (partitions).
  - The general rule is to set the DISTKEY to the column most commonly JOINed.
  - With high-cardinality keys (e.g., user_id, order_id), the data gets more evenly distributed across slices/nodes, which helps avoid skew and improves parallelism.
  - Low cardinality key causes data skew. 
  - DISTKEY in Amazon Redshift and partitioning in Apache Spark serve different purposes and behave quite differently, even though they both relate to data distribution.
    - Redshift focuses on storage layout and long-term performance for joins/queries.
    - Spark focuses on in-memory distributed processing and task execution optimization.
- A table's **sortkey** is the column by which it's sorted within each node.

Note: To disable using cache results in Redshift query use: SET enable_result_cache_for_session TO OFF;

#### Redshift Spectrum 

Using Amazon Redshift Spectrum, you can efficiently query and retrieve structured and semistructured data from files in Amazon S3 without having to load the data into Amazon Redshift tables. Redshift Spectrum queries employ massive parallelism to run very fast against large datasets. Much of the processing occurs in the Redshift Spectrum layer, and most of the data remains in Amazon S3. Multiple clusters can concurrently query the same dataset in Amazon S3 without the need to make copies of the data for each cluster.

Redshift Spectrum resides on dedicated Amazon Redshift servers that are independent of your cluster. Based on the demands of your queries, Redshift Spectrum can potentially use thousands of instances to take advantage of massively parallel processing. 
You create Redshift Spectrum tables by defining the structure for your files and registering them as tables in an external data catalog. The external data catalog can be AWS Glue, the data catalog that comes with Amazon Athena, or your own Apache Hive metastore. You can create and manage external tables either from Amazon Redshift using data definition language (DDL) commands or using any other tool that connects to the external data catalog. Changes to the external data catalog are immediately available to any of your Amazon Redshift clusters.

Spectrum (Redshift) tables access data from S3 and uses almost infinite resources to read data. Cost of querying depends on size of data being read. Hence they are costly. [Few more details on Spectrum _al](https://stackoverflow.com/questions/73994206/when-to-use-redshift-spectrum-for-your-redshift-data-warehouse)

#### AWS Glue Catalogue

It is a centralized metadata repository for all your data assets across various data sources. It provides a unified interface to store and query information about data formats, schemas, and sources. An AWS Glue crawler automatically discovers and extracts metadata from a data store, and then it updates the AWS Glue Data Catalog accordingly.

#### AWS RDS and Aurora

- Amazon Aurora (Aurora) is a fully managed relational database engine that's compatible with MySQL and PostgreSQL. 
- Amazon Relational Database Service (Amazon RDS) is a web service that makes it easier to set up, operate, and scale a relational database in the AWS Cloud [Amazon RDS Docs _al](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/Welcome.html). 

While RDS provides good performance for most workloads, Aurora generally offers better performance due to its architecture optimizations. But not always true. [Amazon RDS Aurora vs RDS MySQL vs MySQL on EC2 _al](https://stackoverflow.com/questions/46401830/amazon-rds-aurora-vs-rds-mysql-vs-mysql-on-ec2), [Optimize write performance for AWS Aurora instance _al](https://stackoverflow.com/questions/46383763/optimize-write-performance-for-aws-aurora-instance/46384196). 

If you have secondary indexes and have high write traffic, Aurora is not suitable. Though it may change in future. AWS RDS is the managed database solution which provides support for multiple database options Amazon Aurora, PostgreSQL, MySQL, MariaDB, Oracle, and Microsoft SQL Server. When you consider Amazon Aurora in RDS, it differs from the rest of the engines because, its new and fully implemented by Amazon from ground up. 

You choose Aurora MySQL or Aurora PostgreSQL as the DB engine option when setting up new database servers through Amazon RDS. Aurora takes advantage of the familiar Amazon Relational Database Service (Amazon RDS) features for management and administration. [PostgreSQL vs. Aurora PostgreSQL _al](https://www.linkedin.com/pulse/postgresql-vs-aurora-choosing-right-database-your-aws-barry-o-connell-mgare/) 

- ExportTaskAlreadyExistsFault error comes in RDS when you trigger export of snapshot data of an RDS instance (having db's, tables) multiple times. 

#### AWS ECS Task, Task Definition & Service

- ECS task definition is: The task definition is a text file, in JSON format, that describes one or more containers, that form your application. It can be thought of as a blueprint for your application. The Task definition allows you to specify which Docker image to use, which ports to expose, how much CPU and memory to allot, how to collect logs, and define environment variables. 
- A Task is created when you run a Task directly, which launches container(s) (defined in the task definition) until they are stopped or exit on their own, at which point they are not replaced automatically. Running Tasks directly is ideal for short-running jobs, perhaps as an example of things that were accomplished via CRON.
- A Service is used to guarantee that you always have some number of Tasks running at all times. If a Task's container exits due to an error, or the underlying EC2 instance fails and is replaced, the ECS Service will replace the failed Task. This is why we create clusters so that the service has plenty of resources in terms of CPU, memory and network ports to use. To us it doesn't really matter which instance Tasks run on so long as they run. A Service configuration references a Task definition. A Service is responsible for creating Tasks.
- [Memory reservations, soft & hard limit in ECS _al](https://aws.amazon.com/blogs/containers/how-amazon-ecs-manages-cpu-and-memory-resources/)

[ECS task and task definition _al](https://stackoverflow.com/questions/42960678/what-is-the-difference-between-a-task-and-a-service-in-aws-ecs)
[ECS task and task definition _vl](https://www.youtube.com/watch?v=5uJUmGWjRZY)

#### AWS ECR

Amazon Elastic Container Registry (Amazon ECR) is an AWS managed container image registry service that is secure, scalable, and reliable. Amazon ECR supports private repositories with resource-based permissions using AWS IAM. [Docs _al](https://docs.aws.amazon.com/AmazonECR/latest/userguide/what-is-ecr.html)

#### Misc infos: 

- With **Amazon S3 Select**, you can use structured query language (SQL) statements to filter the contents of an Amazon S3 object and retrieve only the subset of data that you need.
- Boto3 makes it easy to integrate your Python application, library, or script with AWS services including Amazon S3, Amazon EC2, Amazon DynamoDB, and more. [boto3 client vs resource vs session _al](https://stackoverflow.com/questions/42809096/difference-in-boto3-between-resource-client-and-session)
  - boto3.client interface provides a low-level, service-specific API. It exposes methods and parameters that closely mirror the AWS service APIs.
  - boto3.resource interface provides a higher-level, object-oriented API. It abstracts away many of the low-level details and provides a more Pythonic and object-oriented way to work with AWS services.
  - Session stores configuration information (primarily credentials and selected region) & allows you to create service clients and resources

----------------------------------------------------------------------





















