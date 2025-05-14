
# Spark

Apache Spark is a data processing framework that can quickly perform processing tasks on very large data sets, and can also distribute data processing tasks across multiple computers, either on its own or in tandem with other distributed computing tools. 

- At the heart of Apache Spark is the concept of the **Resilient Distributed Dataset (RDD)**, a programming abstraction that represents an immutable collection of objects that can be split across a computing cluster. Operations on the RDDs can also be split across the cluster and executed in a parallel batch process, leading to fast and scalable parallel processing. 
- Apache Spark turns the user’s data processing commands into a **Directed Acyclic Graph, or DAG**. The DAG is Apache Spark’s scheduling layer; it determines what tasks are executed on what nodes and in what sequence. 
- Spark runs in a distributed fashion by combining a **Driver** core process that splits a Spark application into tasks and distributes them among many **Executor** processes that do the work. These executors can be scaled up and down as required for the application’s needs. 

Concept of application, job, stage and task in Spark:
- **Application** - A user program built on Spark using its APIs. It consists of a driver program and executors on the cluster.
- **Job** - A parallel computation consisting of multiple tasks that gets spawned in response to a Spark action (e.g., save(), collect()). During interactive sessions with Spark shells, the driver converts your Spark application into one or more Spark jobs. It then transforms each job into a DAG. This, in essence, is Spark’s execution plan, where each node within a DAG could be a single or multiple Spark stages.
  - A job in Spark refers to a sequence of transformations on data. Whenever an action like count(), first(), collect(), and save() is called on RDD (Resilient Distributed Datasets), a job is created. A job could be thought of as the total work that your Spark application needs to perform, broken down into a series of steps. 
  - Consider a scenario where you’re executing a Spark program, and you call the action count() to get the number of elements. This will create a Spark job. If further in your program, you call collect(), another job will be created. So, a Spark application could have multiple jobs, depending upon the number of actions. [Job, Stage, Task in Spark _al](https://medium.com/@diehardankush/what-are-job-stage-and-task-in-apache-spark-2fc0d326c15f)
- **Stage** - Each job gets divided into smaller sets of tasks called stages that depend on each other. As part of the DAG nodes, stages are created based on what operations can be performed serially or in parallel. Not all Spark operations can happen in a single stage, so they may be divided into multiple stages. Often stages are delineated on the operator’s computation boundaries, where they dictate data transfer among Spark executors.
  - A stage in Spark represents a sequence of transformations that can be executed in a single pass, i.e., without any shuffling of data. When a job is divided, it is split into stages. Each stage comprises tasks, and all the tasks within a stage perform the same computation.
  - The boundary between two stages is drawn when transformations cause data shuffling across partitions. Transformations in Spark are categorized into two types: narrow and wide. Narrow transformations, like map(), filter(), and union(), can be done within a single partition. But for wide transformations like groupByKey(), reduceByKey(), or join(), data from all partitions may need to be combined, thus necessitating shuffling and marking the start of a new stage.
- **Task** - A single unit of work or execution that will be sent to a Spark executor. Each stage is comprised of Spark tasks (a unit of execution), which are then federated across each Spark executor; each task maps to a single core and works on a single partition of data. As such, an executor with 16 cores can have 16 or more tasks working on 16 or more partitions in parallel, making the execution of Spark’s tasks exceedingly parallel. 
  - A task in Spark is the smallest unit of work that can be scheduled. Each stage is divided into tasks. A task is a unit of execution that runs on a single machine. When a stage comprises transformations on an RDD, those transformations are packaged into a task to be executed on a single executor.
  - For example, if you have a Spark job that is divided into two stages and you’re running it on a cluster with two executors, each stage could be divided into two tasks. Each executor would then run a task in parallel, performing the transformations defined in that task on its subset of the data.
  - In summary, a Spark job is split into multiple stages at the points where data shuffling is needed, and each stage is split into tasks that run the same code on different data partitions.
- Example: [Number of spark jobs and stages _al](https://stackoverflow.com/questions/75930351/number-of-spark-jobs-and-stages)
  ```
  Example 1: 
  Consider a spark job, which reads two csv files with header=True (no infer_schema). Then joins these 2 dataframes, performs group by and finally write as csv.
  Steps:
    Read csv file1 with header=True (no infer_schema)
    Read csv file2 with header=True (no infer_schema)
    Join file1 and file2 (not a broadcast)
    Perform group by
    write grouped data as results in csv format
  So, 
  The number of jobs corresponds to the number of actions
  No of jobs = No of actions. The number of stages aligns with the count of wide transformations.
  No of Stage = No of wide transformation. In this scenario, you will have three jobs reflecting the three actions, and two stages due to the inclusion of wide transformations, such as group by and join operations.
  Tasks depends on the number of data partitions. 
  
  Example 2: 
  Let’s take an example where we read a CSV file, perform some transformations on the data, and then run an action to demonstrate the concepts of job, stage, and task in Spark with Scala.
  Code: 
  import org.apache.spark.sql.SparkSession
  val spark = SparkSession.builder.appName("Spark Job Stage Task Example").getOrCreate() // Create a Spark Session
  val data = spark.read.option("header", "true").csv("path/to/your/file.csv") // Read a CSV file - this is a transformation and doesn't trigger a job
  val transformedData = data.withColumn("new_column", data("existing_column") * 2) // Perform a transformation to create a new DataFrame with an added column. This also doesn't trigger a job, as it's a transformation (not an action)
  val result = transformedData.count() // Now, call an action - this triggers a Spark job
  println(result)
  spark.stop()
  In the above code:
    A Job is triggered when we call the action count(). This is where Spark schedules tasks to be run.
    Stages are created based on transformations. In this example, we have two transformations (read.csv and withColumn). However, these two transformations belong to the same stage since there's no data shuffling between them.
    Tasks are the smallest unit of work, sent to one executor. The number of tasks depends on the number of data partitions. Each task performs transformations on a chunk of data.
  ```

Spark processes queries by distributing data over multiple nodes and calculating the values separately on every node. However, occasionally, the nodes need to exchange the data. After all, that’s the purpose of Spark - processing data that doesn’t fit on a single machine. [Application, Job, Stage in Spark _al](https://stackoverflow.com/questions/42263270/what-is-the-concept-of-application-job-stage-and-task-in-spark)

The Catalyst Optimizer is a component of Spark SQL that performs optimization on a query through 4 stages: analysis, logical optimization, physical planning, code generation. Catalyst Optimizer is a rule based engine that takes the Logical Plan and rewrites it as an optimized Physical Plan. The Physical Plan is developed BEFORE a query is executed.
To view the Catalyst Optimizier in action, use df.explain(True) to view the Logical and Physical Execution plans of a query.
In Spark 3.0, Adaptive Query Execution (AQE) was introduced. One difference between AQE and Catalyst Optimizer is that AQE modifies the Physical Plan based on Runtime Statistics, so AQE can tune your queries further on the flight. So you may think that AQE is complimentary to Catalyst Optimizer.
For example, during runtime, based on the new information that is previously not available during planning, AQE can decide to change your join strategy to Broadcast Hash Join from Sort Merge Join to reduce data shuffle. Or AQE can coalesce your partitions to optimal size during shuffling stage, or help improve Skew Join.
This option is not turned on by default in Spark, you can enable by setting spark config: spark.conf.set(spark.sql.adaptive.enabled, True) , and it’s recommended to turn this on. However, If you run Spark on later version of Databricks Runtime, AQE is enabled by default. [Tune spark _al](https://anhcodes.dev/blog/tune-spark/)

When Spark reads a file from HDFS (File-based RDD), S3, or any distributed file system, it follows the file system's block size (typically 128 MB). Each block is mapped to a single partition. Example: If you have a 1 GB file in HDFS with a block size of 128 MB, you will have 8 partitions by default. If you want more partitions, you can increase the minPartitions parameter when reading the file.
When you use spark for parallel processing (In-memory RDD), it creates partitions that are equal to the number of CPU cores in the machine; Say: When you create an RDD using sc.parallelize(), Spark uses the number of available CPU cores on the driver or cluster to determine the number of partitions.
- Data of each partition resides in a single machine. Spark/Pyspark creates a task for each partition. Spark shuffle operations move the data from one partition to other partitions. Partitioning is an expensive operation as it creates a data shuffle (Data could move between the nodes). Partition is a logical division of the data , this idea is derived from Map Reduce (split). Logical data is specifically derived to process the data. Small chunks of data also it can support scalability and speed up the process. Input data, intermediate data, and output data everything is partitioned RDD. Spark uses the map-reduce API to do the partition the data. Do not partition by columns having high cardinality. For example, don’t use your partition key such as roll no, employee id etc , Instead your state code, country code etc. Partition data by specific columns that will be mostly used during filter and group by operations. [Spark Partition _al](https://statusneo.com/everything-you-need-to-understand-data-partitioning-in-spark/)

**Shuffling** is the process of exchanging data between partitions as seen earlier. As a result, data rows can move between worker nodes when their source partition and the target partition reside on a different machine. Spark doesn’t move data between nodes randomly. Shuffling is a time-consuming operation, so it happens only when there is no other option. Spark nodes read chunks of the data (data partitions), but they don’t send the data between each other unless they need to. When do they do it? When you explicitly request data repartitioning (using the repartition functions), when you join DataFrames or group data by a column value. When we join the data in Spark, it needs to put the data in both DataFrames in buckets. Those buckets are calculated by hashing the partitioning key (the column(s) we use for joining) and splitting the data into a predefined number of buckets. We can control the number of buckets using the spark.sql.shuffle.partitions parameter. The same hashing and partitioning happen in both datasets we join. The corresponding partitions from both datasets are transferred to the same worker node. The goal is to have the data locally on the same worker before we start joining the values. Spark partitions the data also when we run a grouping operation and calculate an aggregate. This time we have only one dataset, but we still need the data that belongs to a single group on a single worker node. Otherwise, we couldn’t calculate the aggregations. Of course, some aggregations, like calculating the number of elements in a group or a sum of the parts, don’t require moving data to a single node. If we calculate the sum on every node separately and then move the results to a single node, we can calculate the final sum. 

Problem with Shuffling: What if one worker node receives more data than any other worker? You will have to wait for that worker to finish processing while others do nothing. What’s even worse, the worker may fail, and the cluster will need to repeat a part of the computation. You can avoid such problems by enabling **speculative execution** - use the spark.speculation parameter. With this feature enabled, the idle workers calculate a copy of long-running tasks, and the cluster uses the results produced by the worker who finished sooner. When does one worker receive more data than others?: It happens when one value dominates the partitioning key (for example, the null). All rows with the same partitioning key value must be processed by the same worker node (in the case of partitioning). So if we have 70% of null values in the partitioning key, one node will get at least 70% of the rows. When this happens, we have two options: First, we can isolate the dominating value by filtering it out from the DataFrame. After filtering, we can calculate the aggregate using the remaining rows. After that, we can calculate the aggregate of the filtered out value separately. The second solution is to create a **surrogate partitioning key** by combining multiple columns or generating an artificial partitioning value. Such a key should help us uniformly distribute the work across all worker nodes. [Spark Shuffling _al](https://mikulskibartosz.name/shuffling-in-apache-spark)

In Spark partitions: 
If partition count: 
- Too few partitions You will not utilize all of the cores available in the cluster.
- Too many partitions There will be excessive overhead in managing many small tasks (remember Driver -> Job -> Stage -> Task). 

(Or)

If partition size:
- Too small - slow read time downstream, large task creation overhead, driver OOM 
- Too large - long computation time, slow write times, executor/worker OOM [Spark basics partitions _vl](https://www.youtube.com/watch?v=hvF7tY2-L3U)

Between the two the first one is far more impactful on performance. Scheduling too many small tasks has a relatively small impact at this point for partition counts below 1000. If you have on the order of tens of thousands of partitions then spark gets very slow. Once the user has submitted his job into the cluster, each partition is sent to a specific executor for further processing. Only one partition is processed by one executor at a time, so the size and number of partitions transferred to the executor are directly proportional to the time it takes to complete them. Thus the more partitions the more work is distributed to executors, with a smaller number of partitions the work will be done in larger pieces (and often faster).

As a note: Apache Spark supports two types of partitioning “hash partitioning” and “range partitioning”. [Partition types _vl](https://www.youtube.com/watch?v=BvyOJuik8FA&ab_channel=DataSavvy). The Apache Spark documentation recommends using 2-3 tasks per CPU core in the cluster. Therefore, if you have eight worker nodes and each node has four CPU cores, you can configure the number of partitions to a value between 64 and 96. By default, Spark creates one partition for each block of the file (blocks being 128MB by default in HDFS), but you can also ask for a higher number of partitions by passing a larger value.
RDD's as we know are collection of various data items that are so huge in size, that they cannot fit into a single node and have to be partitioned across various nodes. 

Spark automatically partitions RDDs and distributes the partitions across different nodes. A partition in spark is an atomic chunk of data (logical division of data) stored on a node in the cluster. Partitions are basic units of parallelism in Apache Spark.

Ways to manage partitions:
- **Repartition** operation: Under repartitioning meant the operation to reduce or increase the number of partitions in which the data in the cluster will be split. This process involves a full shuffle. Consequently, it is clear that repartitioning is an expensive process. In a typical scenario, most of the data should be serialized, moved, and deserialized.
- **Coalesce** operation: This operation reduces the number of partitions. It avoids full shuffle, instead of creating new partitions, it shuffles the data using default Hash Partitioner, and adjusts into existing partitions, this means it can only decrease the number of partitions. The executor can safely leave data on a minimum number of partitions, moving data only from redundant nodes. Therefore, it is better to use coalesce than repartition if you need to reduce the number of partitions. 
However, you should understand that you can drastically reduce the parallelism of your data processing — coalesce is often pushed up further in the chain of transformation and can lead to fewer nodes for your processing than you would like. 
(As a note: To avoid this, you can pass shuffle = true. There are three stages at the physical level where the number of partitions is important. They are input, shuffle, and output. [Spark partition tuning _al](https://luminousmen.com/post/spark-tips-partition-tuning))
So, it would go something like this:

```
Node 1 = 1,2,3
Node 2 = 4,5,6
Node 3 = 7,8,9
Node 4 = 10,11,12
Then coalesce down to 2 partitions:
Node 1 = 1,2,3 + (10,11,12)
Node 3 = 7,8,9 + (4,5,6)
Notice that Node 1 and Node 3 did not require its original data to move.
```

Note that coalesce results in partitions with different amounts of data (sometimes partitions that have much different sizes) and repartition results in roughly equal sized partitions. Coalesce may run faster than repartition, but unequal sized partitions are generally slower to work with than equal sized partitions. You'll usually need to repartition datasets after filtering a large data set.
    - `outputData.coalesce(1).write.parquet(outputPath)` will write with 1 worker. So, even though you give 10 CPU core, it will write with 1 worker (single partition). Problem will be if your file very big (10 gb or more). But can be used if you have small file (100 mb)
- **partitionBy operation**: So instead of coalesce and repartition functions, it will change the folder structure of the underlying data. Example, in a CSV data say we can partition the data by ‘country’ column, as it has low cardinality and more likely to be used as a filter condition. However, partitionBy has no control on how many part files will be created under each folder. In some use-case, you may want to create a single file per partitioned folders, for that you need to use repartition along with partitionBy as following:

```
scala>  DF.repartition($”country”).write.mode(“overwrite”).partitionBy(“country”).option(“header”,true).csv(“/Users/d0d02t4/Desktop/data/partition_by_test.csv”)
```

Useful links:
[Data partitioning spark _al](https://medium.com/@dipayandev/everything-you-need-to-understand-data-partitioning-in-spark-487d4be63b9c), [Spark repartition vs coalesce _al](https://stackoverflow.com/questions/31610971/spark-repartition-vs-coalesce)

Now, when you have a small dataset, then to run spark jobs for testing on them, in short you could use: [Spark Jobs on Small Test Datasets _al](https://luminousmen.com/post/how-to-speed-up-spark-jobs-on-small-test-datasets)

```
.master("local[1]")
.config("spark.sql.shuffle.partitions", 1)
.config("spark.default.parallelism", <number_of_cores>)
.config("spark.rdd.compress", "false")
.config("spark.shuffle.compress", "false")
.config("spark.dynamicAllocation.enabled", "false")
.config("spark.executor.cores", 1)
.config("spark.executor.instances", 1)
.config("spark.ui.enabled", "false")
.config("spark.ui.showConsoleProgress", "false")
``` 

- **Broadcast Join** is an optimization technique in the PySpark SQL engine that is used to join two DataFrames. This technique is ideal for joining a large DataFrame with a smaller one. With broadcast join, PySpark broadcast the smaller DataFrame to all executors and the executor keeps this DataFrame in memory and the larger DataFrame is split and distributed across all executors so that PySpark can perform a join without shuffling any data from the larger DataFrame as the data required for join colocated on every executor. [Normal vs Broadcast join _al](https://www.linkedin.com/pulse/apache-spark-101-shuffle-join-vs-broadcast-joins-shanoj-kumar-v-g779c/)

- **Salting** is a technique used in Apache Spark to evenly distribute data across partitions. It involves adding a random or unique identifier (called a "salt") to each record before performing operations like grouping or joining. This helps avoid data skew and improves parallelism in data processing.

- How to handle skewed data in Spark: Data skew occurs when some partitions have significantly more data than others, leading to performance bottlenecks. Strategies to handle skewed data include - Salting, Repartitioning, Broadcast Variables.

- Spark uses two engines to optimize and run the queries - Catalyst and Tungsten, in that order. Catalyst basically generates an optimized physical query plan from the logical query plan by applying a series of transformations like predicate pushdown, column pruning, and constant folding on the logical plan. This optimized query plan is then used by Tungsten to generate optimized code, that resembles hand written code, by making use of Whole-stage Codegen functionality introduced in Spark 2.0. This functionality has improved Spark's efficiency by a huge margin from Spark 1.6, which used the traditional Volcano Iterator Model. [Spark engine _al](https://www.linkedin.com/pulse/catalyst-tungsten-apache-sparks-speeding-engine-deepak-rajak/)

- In **PySpark** (PySpark is the Python API for Apache Spark. It enables you to perform real-time, large-scale data processing in a distributed environment using Python), Python and JVM codes live in separate OS processes. 
  - PySpark uses Py4J, which is a framework that facilitates interoperation between the two languages, to exchange data between the Python and the JVM processes. When you launch a PySpark job, it starts as a Python process, which then spawns a JVM instance and runs some PySpark specific code in it. It then instantiates a Spark session in that JVM, which becomes the driver program that Spark sees. That driver program connects to the Spark master or spawns an in-proc one, depending on how the session is configured.
  - When you create RDDs or Dataframes, those are stored in the memory of the Spark cluster just as RDDs and Dataframes created by Scala or Java applications. Transformations and actions on them work just as they do in JVM, with one notable difference: anything, which involves passing the data through Python code, runs outside the JVM. So, if you create a Dataframe, and do something like: `df.select("foo", "bar").where(df["foo"] > 100).count()`, this runs entirely in the JVM as there is no Python code that the data must pass through. 
  - On the other side, if you do: `a = t.reduce(add)`, since the add operator is a Python one, the RDD gets serialised, then sent to one or more Python processes where the reduction is performed, then the result is serialised again and returned to the JVM, and finally transferred over to the Python driver process for the final reduction.

[Pyspark code run in jvm or python subprocess _al](https://stackoverflow.com/questions/61816236/does-pyspark-code-run-in-jvm-or-python-subprocess), 
[Pyspark internals _al](https://cwiki.apache.org/confluence/display/SPARK/PySpark+Internals)

- The `createOrReplaceTempView()` is used to create a temporary view/table from the Spark DataFrame or Dataset objects. It is part of Spark 2.0. In Spark 1.0 `registerTempTable()` was used. 
- Why spark faster than hadoop?: 
  - Spark is faster than Hadoop MapReduce due to in-memory data processing, reducing the need for disk I/O and improving performance for iterative algorithms.
  - Spark efficiently manages memory and can spill data to disk if the memory is not sufficient, allowing it to handle larger datasets than Hadoop MapReduce.
  - Spark uses its Catalyst Query Planner, which optimizes query execution plans, leading to better performance and more efficient resource utilization.
- Spark does lazy evaluation. 
  - For transformations, Spark adds them to a DAG of computation and only when driver requests some data, does this DAG actually gets executed. One advantage of this is that Spark can make many optimization decisions after it had a chance to look at the DAG in entirety. This would not be possible if it executed everything as soon as it got it. For example -- if you executed every transformation eagerly, what does that mean? Well, it means you will have to materialize that many intermediate datasets in memory. This is evidently not efficient -- for one, it will increase your GC costs. (Because you're really not interested in those intermediate results as such. Those are just convenient abstractions for you while writing the program.) So, what you do instead is -- you tell Spark what is the eventual answer you're interested and it figures out best way to get there. [spark lazy evaluation _al](https://stackoverflow.com/questions/38027877/spark-transformation-why-is-it-lazy-and-what-is-the-advantage)
  - In other words: Lazy evaluation allows Spark to optimize the entire data processing workflow before executing it, combining operations to minimize data shuffling and reduces the number of passes through the data, improving performance.
  - [Lazy vs Eager Evaluation _al](https://stackoverflow.com/questions/75680491/what-is-the-trade-off-between-lazy-and-strict-eager-evaluation): 
    - Lazy: It is an evaluation strategy which holds the evaluation of an expression until its value is needed. It avoids repeated evaluation. 
    - Eager: Eager Evaluation is a programming concept where an expression is evaluated as soon as it is defined. This approach is in contrast to Lazy Evaluation, where the calculation is deferred until it is needed.
- [Spark RDD vs Dataframe vs Dataset _al](https://sparkbyexamples.com/spark/spark-rdd-vs-dataframe-vs-dataset/):

| Context             | RDDs                                                                                                                  | Dataframes                                                                                                                                   | Dataset                                                                                                                        |
|---------------------|-----------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------|
| Interoperability    | Can be easily converted to DataFrames and vice versa using the `toDF()` and `rdd()` methods.                        | Can be easily converted to RDDs and Datasets using the `rdd()` and `as[]` methods respectively.                                             | Can be easily converted to DataFrames using the `toDF()` method, and to RDDs using the `rdd()` method.                         |
| Type safety         | Not type-safe                                                                                                        | DataFrames are not type-safe. When trying to access a column that doesn't exist, Dataframe APIs do not support compile-time error detection. | Datasets are type-safe. Datasets provide compile-time type checking, helping to catch errors early in the development process. |
| Performance         | Low-level API with more control over the data, but lower-level optimizations compared to DataFrames and Datasets.    | Optimized for performance, with high-level API, Catalyst optimizer, and code generation.                                                     | Datasets are faster than DataFrames because they use JVM bytecode generation, leveraging the JVM’s optimization capabilities.   |
| Memory Management  | Provide full control over memory management, as they can be cached in memory or disk as per the user’s choice.       | Have more optimized memory management, with a Spark SQL optimizer that helps to reduce memory usage.                                           | Support most available data types.                                                                                           |
| Serialization      | Uses Java serialization when distributing data within the cluster or writing data to disk.                          | DataFrames use a generic encoder that can handle any object type.                                                                           | Datasets are serialized using specialized encoders optimized for performance.                                                  |
| APIs                | Provide a low-level API requiring more code to perform transformations and actions on data.                        | Provide a high-level API making it easier to perform transformations and actions on data.                                                   | Datasets provide a richer set of APIs, supporting both functional and object-oriented programming paradigms.                 |
| Schema enforcement | Do not have an explicit schema, and are often used for unstructured data.                                             | DataFrames enforce schema at runtime. Have an explicit schema describing the data and its types.                                           | Datasets enforce schema at compile time, catching errors in data types or structures earlier in the development cycle.       |
| Programming Language Support | RDD APIs are available in Java, Scala, Python, and R languages.                                                  | Available in Java, Python, Scala, and R languages.                                                                                            | Only available in Scala and Java.                                                                                            |
| Optimization       | No inbuilt optimization engine is available in RDD.                                                                  | It uses a Catalyst optimizer for optimization.                                                                                              | It includes the concept of a Dataframe Catalyst optimizer for optimizing query plans.                                            |
| Data types         | Suitable for structured and semi-structured data processing with a higher level of abstraction.                     | DataFrames support most available data types.                                                                                               | Datasets support all data types supported by DataFrames and also support user-defined types.                                  |
| Use Cases          | Suitable for low-level data processing and batch jobs that require fine-grained control over data.                   | Suitable for structured and semi-structured data processing with a higher level of abstraction.                                               | Suitable for high-performance batch and stream processing with strong typing and functional programming.                      |

- Can we write files with specific name in Pyspark than the usual way it writes with names like: `part-000...parquet`?
  - Because you are using spark, your data is spread across multiple nodes, computing in parallel and sent in part to your directory. One of the reasons to use spark is that the data cannot be stored locally. So this is how the data is output. The larger your file the larger more "part" files should come through.
  - Pyspark stores the files in smaller chunks and processes across worker nodes. You can compute data in chunks and can write it as a single file using coalesce() but do not have the luxury to write with specific name, as with coalesce, it sits on 1 worker and writes. You can use dbutils, or other ways to rename file. 
  - A way which I (Suraj) figured out to do it in S3 reading online articles: 

```
Pseudocode: 

After writing a single file with coalesce() function pyspark with name part-000.json. I will rename it as 0.json. 

def get_dbutils(spark):
    try:
        from pyspark.dbutils import DBUtils
        dbutils = DBUtils(spark)
    except ImportError:
        import IPython
        dbutils = IPython.get_ipython().user_ns["dbutils"]
    return dbutils

def some_fun_process():
## some process - to generate a single part-000 file with coalesce() spark ##
## now renaming it 

    dbutils = get_dbutils(spark)
        try:
            files = dbutils.fs.ls(target_path)
        except Exception as err:
            _log.info(f'Error when listing files in S3 using dbutils: {err}')
            raise Exception('Files cannot be listed. Ensure there are files written in dir to rename them accordingly')
        _log.info(f'Copying file in same location with updated name') 
        for file in files:
            if file.name.endswith('json'):
                file_name = file.name
        dbutils.fs.cp(target_path + f'/{file_name}', target_path + '/0.json')
        _log.info(f'Deleting redundant files') 
        files_to_remove = [file.path for file in files if file.name.endswith(".json") and file.name != "0.json"] # deleting all files ending with .json name except 0.json file which is renamed/updated file
        for file_path in files_to_remove: # Remove all json files except '0.json'
            dbutils.fs.rm(file_path)
```

- Spark session: 
  - You should always close your SparkSession when you are done with its use (even if the final outcome were just to follow a good practice of giving back what you've been given). Closing a SparkSession may trigger freeing cluster resources that could be given to some other application. SparkSession is a session and as such maintains some resources that consume JVM memory. You can have as many SparkSessions as you want (see SparkSession.newSession to create a session afresh) but you don't want them to use memory they should not if you don't use one and hence close the one you no longer need.
  - You can have as many SparkSessions as needed. You can have one and only one SparkContext on a single JVM, but the number of SparkSessions is pretty much unbounded.
  - [Spark session close _al](https://stackoverflow.com/questions/44058122/what-happens-if-sparksession-is-not-closed), [Total spark sessions _al](https://stackoverflow.com/questions/47723761/how-many-sparksessions-can-a-single-application-have)

```
To check if there is an active spark session: 
spark = SparkSession.builder.appName("ProcessEvent").getOrCreate()
spark.stop()
if (spark.getActiveSession()):
    print('yes')
else:
    print('no')
```

- Spark UDFs are executed at workers, so the print statements inside them won't show up in the output (which is from the driver). The best way to handle issues with UDFs is to change the return type of the UDF to a struct or a list and pass the error information along with the returned output. Eg, sample code: [UDF print code eg _al](https://stackoverflow.com/questions/54252682/pyspark-udf-print-row-being-analyzed)

```
import pyspark.sql.functions as F
def myF(input):
  myF.lineNumber += 1
  if (somethingBad):
    res += 'Error in line {}'.format(myF.lineNumber)
  return res
myF.lineNumber = 0
myF_udf =  F.udf(myF, StringType())
```

- Narrow vs Wide Transformations in Pyspark: 
  - Narrow transformations:
    - Data computation happen on a single partition meaning there will not be any data movement between partitions to execute narrow transformations. 
    - Eg: map(), filter()
  - Wide Transformations: These are the operations that require shuffling data across partitions. This means that the data needs to be moved between executor or worker nodes.
    - Eg: groupBy, groupByKey(), reduceByKey(), aggregate(), aggregateByKey(), distinct(), join(), repartition()

- In BigData, writing optimized sql query is an art. [sql_queries optimize _al](https://blog.dataengineer.io/p/how-to-save-millions-by-optimizing), [reddit sql optimize1 _al](https://www.reddit.com/r/dataengineering/comments/13dlgrr/are_sql_query_optimization_skills_important_and/), [reddit sql optimize2 _al](https://www.reddit.com/r/SQL/comments/1cjinf5/how_to_optimize_sql_queries/), [reddit sql optimize3 _al](https://www.reddit.com/r/dataengineering/comments/1axd7cy/what_are_your_top_sql_query_optimization_tips/), [optimize sql _al](https://medium.com/@alejandro.ibapen/top-20-sql-query-optimisation-tricks-for-data-analysis-3d31642d9917)
  - Shuffling is caused when you try to aggregate or join datasets in distributed environments like Spark or BigQuery.
  - In terms of worstness ~ ORDER BY (takes the worst parts of shuffling and forces all the data to pass through one machine) > JOIN > GROUP BY
    - Eliminating shuffle for GROUP BY or JOIN by bucketing your data.
  - Using a broadcast join is also a good option.
  - Normalize database tables
  - Use UNION ALL instead of UNION (UNION removes duplicate rows, UNION ALL does not. There is a performance hit when using UNION instead of UNION ALL, since the database server must do additional work to remove the duplicate rows, but usually you do not want the duplicates especially when developing reports)
  - Instead of using IN or NOT IN, prefer EXISTS or NOT EXISTS. These subqueries can be more efficient, especially when dealing with extensive subquery results.
  - Regularly maintain your database to ensure optimal performance: Rebuild indexes periodically to maintain their efficiency; Use VACUUM and ANALYZE commands to manage table data and update query planner statistics; 
  - Common Table Expressions (CTEs) provide a way to create temporary result sets within a query. They can enhance the readability and maintainability of complex queries. While not always an optimisation technique, CTEs can help organise your query for better performance analysis and troubleshooting. CTE is a named temporary result set that can be referenced within a SELECT, INSERT, UPDATE, or DELETE statement. It is defined using the WITH keyword and is useful for recursive queries, subquery replacement, and code reusability.
  - For applications making frequent database connections, use connection pooling to minimise the overhead of establishing and closing connections.
  - Partitioning can significantly improve query performance by limiting the amount of data that needs to be scanned.
  - Using SELECT DISTINCT to remove duplicates from query results can be resource-intensive. If possible, design your data model to prevent duplicates, or use GROUP BY when you genuinely need to aggregate data.
  - Selecting only necessary columns: When writing a query, avoid using the wildcard (*) and select only the columns you actually need. This reduces the amount of data that needs to be processed and returned, resulting in faster query execution.
  - Using appropriate indexes: Indexes can significantly improve query performance. Be mindful of creating and using indexes on columns that are frequently used in WHERE clauses, JOIN conditions, or ORDER BY clauses.
  - Limiting the result set: Use LIMIT or TOP clauses to restrict the number of rows returned by your query. This can help reduce the amount of data processed and speed up the query execution.
  - Avoiding correlated subqueries: Correlated subqueries can be resource-intensive and slow down query execution. Where possible, use JOINs or derived tables to avoid correlated subqueries. Ensure that subqueries are well-optimized by using appropriate indexes, limiting the number of returned rows, and avoiding unnecessary subqueries when possible.
  - Filtering data early: Apply filters and conditions as early as possible in the query. This reduces the amount of data that needs to be processed in subsequent steps, resulting in faster query execution. Eg: Filtering the data and then joining rather than joining first later filtering maybe a better approach. 
  - Using the appropriate JOIN type: Different JOIN types (INNER, OUTER, LEFT, RIGHT) can have a significant impact on query performance. Choose the appropriate JOIN type based on the data and relationships between tables.
  - Minimizing the use of functions in predicates: Using functions in WHERE clauses or JOIN conditions can slow down query execution. If possible, pre-calculate function results or use other methods to minimize their usage in these clauses.
  - Avoiding excessive nesting: Deeply nested subqueries or derived tables can be difficult to read and maintain, and can also impact query performance. Look for opportunities to simplify your query by using JOINs, temporary tables, or other techniques.
  - Utilizing query execution plans: Analyze and understand the query execution plan to identify potential bottlenecks and areas for improvement. This can help you optimize your query and achieve better performance.
  - Testing and monitoring: Regularly test and monitor your queries to ensure they are performing optimally. Identify slow-running queries and make necessary adjustments to maintain the performance of your database system.

- [Difference between "predicate pushdown" and "projection pushdown" in Spark _al](https://stackoverflow.com/questions/58235076/what-is-the-difference-between-predicate-pushdown-and-projection-pushdown): Predicate refers to the where/filter clause which affects the amount of rows returned. Projection refers to the selected columns. For example: If your filters pass only 5% of the rows, only 5% of the table will be passed from the storage to Spark instead of the full table; If your projection selects only 3 columns out of 10, then less columns will be passed from the storage to Spark and if your storage is columnar (e.g. Parquet, not Avro) and the non selected columns are not a part of the filter, then these columns won't even have to be read.
- [Spark writes at least 1 file per partition _al](https://stackoverflow.com/questions/77266953/number-of-files-generated-by-spark).
- [Pyspark is faster than pandas on a single node machine _al](https://www.databricks.com/blog/2018/05/03/benchmarking-apache-spark-on-a-single-node-machine.html). Also its better to use pyspark for large datasets as it offers more functionality than pandas. 
- Running multi-thread code in Pyspark: 
  - As soon as we call the function executing code via multi-threading then multiple tasks will be submitted in parallel to spark driver which would later relay down the work to spark executors provided the driver and executor have enough cores. So, if a driver has 64 cores, then the most optimal count of tasks it can submit to executors/workers is 64, similarly for workers doing the work.
  - Question comes: What about GIL in Python, with our current scenario, only 1 thread would pass the GIL lock and driver would get only 1 thread; So 1 task which the driver will then pass on to the workers; It's conflicting to see we won't really achieve parallelism in this case; But that's not the case for all scenarios:
    - The presence of the GIL in Python impacts the ThreadPoolExecutor. The ThreadPoolExecutor maintains a fixed-sized pool of worker threads that supports concurrent tasks, but the presence of the GIL means that most tasks will not run in parallel. You may recall that concurrency is a general term that suggests an order independence between tasks, e.g. they can be completed at any time or at the same time. Parallel might be considered a subset of concurrency and explicitly suggests that tasks are executed simultaneously. The GIL means that worker threads cannot run in parallel, in most cases. Specifically, in cases where the target task functions are CPU-bound tasks. These are tasks that are limited by the speed of the CPU in the system, such as working no data in memory or calculating something. Nevertheless, worker threads can run in parallel in some special circumstances, one of which is when an IO task is being performed. These are tasks that involve reading or writing from an external resource. 
    - Examples include: Reading or writing a file from the hard drive; Reading or writing to standard output, input, or error (stdin, stdout, stderr); Printing a document; Downloading or uploading a file; Querying a server; Querying a database; Taking a photo or recording a video; And so much more.
    - When a Python thread executes a blocking IO task, it will release the GIL and allow another Python thread to execute. This still means that only one Python thread can execute Python bytecodes at any one time. But it also means that we will achieve seemingly parallel execution of tasks if tasks perform blocking IO operations.
    - Hence in our case, if say we fire spark sql queries to a db using multi-threading, since it would be more of a blocking i/o call, we would be able to achieve/observe multi-threading in pyspark.
  - [Useful ref 1 _al](https://medium.com/analytics-vidhya/horizontal-parallelism-with-pyspark-d05390aa1df5), [Useful ref 2 _al](https://superfastpython.com/threadpoolexecutor-vs-gil/)

- Distribution of Executors, Cores and Memory for a Spark Application:
  - An executor is a Spark process responsible for executing tasks on a specific node in the cluster. Each executor is assigned a fixed number of cores and a certain amount of memory. The number of executors determines the level of parallelism at which Spark can process data.
  - Generally, Having more executors allows for better parallelism and resource utilization; Each executor can work on a subset of data independently, which can lead to increased processing speed; However, it’s important to strike a balance between the number of executors and the available cluster resources. If the number of executors is too high, it can lead to excessive memory usage and increased overhead due to task scheduling; Inefficient executor allocation can result in the underutilization of cluster resources.
  - The number of cores refers to the total number of processing units available on the machines in your Spark cluster. It represents the parallelism level at which Spark can execute tasks. Each core can handle one concurrent task. Increasing the number of cores allows; Spark to execute more tasks simultaneously, which can improve the overall throughput of your application. However, adding too many cores can also introduce overhead due to task scheduling and inter-node communication, especially if the cluster resources are limited.
  - Configuring the number of cores and executors in Apache Spark depends on several factors, including: The characteristics of your workload; The available cluster resources, and Specific requirements of your application. While there is no one-size-fits-all approach, here are some general guidelines to help you configure these parameters effectively (after that an example for better understanding):
    - The number of executors should be equal to the number of cores on each node in the cluster. If there are more cores than nodes, then the number of executors should be equal to the number of nodes.
    - The amount of memory allocated to each executor should be based on the size of the data that will be processed by that executor. It is important to leave some memory available for the operating system and other processes. A good starting point is to allocate 1GB of memory per executor.
    - The number of partitions used for shuffle operations should be equal to the number of executors.
  - Let’s try to understand how to decide on the Spark number of executors and cores to be configured in a cluster. For our better understanding Let’s say you have a Spark cluster with 16 nodes, each having 8 cores and 32 GB of memory and your dataset size is relatively large, around 1 TB, and you’re running complex computations on it.
    - For the above cluster configuration we have:
      - Available Resources:
        - Total cores in the cluster = 16 nodes * 8 cores per node = 128 cores
        - Total memory in the cluster = 16 nodes * 32 GB per node = 512 GB
      - Workload Characteristics: 
        - Large dataset size and complex computations suggest that you need a high level of parallelism to efficiently process the data. 
        - Let’s assume that you want to allocate 80% of the available resources to Spark.
    - Tiny Executor Configuration:
      - One way of configuring Spark Executor and its core is setting minimal configuration for the executors and incrementing it based on the application performance.
      - Executor Memory and Cores per Executor: Considering having 1 core per executor: 
        - Number of executors per node=8,
        - Executor-memory=32/8=4GB
      - Calculating the Number of Executors: To calculate the number of executors, divide the available memory by the executor memory:
        - Total memory available for Spark = 80% of 512 GB = 410 GB
        - Number of executors = Total memory available for Spark / Executor memory = 410 GB / 4 GB ≈ 102 executors
        - Number of executors per node = Total Number of Executors/ Number of Nodes = 102/16 ≈ 6 Executors/Node
      - So, in this example, you would configure Spark with 102 executors, each executor having 1 core and 4 GB of memory.
      - Pros: 
        - Resource Efficiency: Tiny executors consume less memory and fewer CPU cores compared to larger configurations.
        - Increased Task Isolation: With tiny executors, each task runs in a more isolated environment. This isolation can prevent interference between tasks, reducing the chances of resource contention and improving the stability of your Spark application.
        - Task Granularity: Tiny executor configurations can be beneficial if your workload consists of a large number of small tasks. With smaller executors, Spark can allocate resources more precisely, ensuring that each task receives sufficient resources without excessive overprovisioning.
      - Cons: 
        - Increased Overhead: Using tiny executors can introduce higher overhead due to the increased number of executor processes and task scheduling.
        - Limited Parallelism: Tiny executors have fewer cores, limiting the level of parallelism in your Spark application.
        - Potential Bottlenecks: In a tiny executor configuration, if a single task takes longer to execute than others, it can become a bottleneck for the entire application.
        - Memory Overhead: Although tiny executors consume less memory individually, the overhead of multiple executor processes can add up. This can lead to increased memory usage for managing the executor processes, potentially reducing the available memory for actual data processing.
    - Fat Executor Configuration:
      - The other way of configuring Spark Executor and its core is setting the maximum utility configuration i.e. having only one Executor per node and optimizing it based on the application performance.
      - Executor Memory and Cores per Executor: Considering having 8 cores per executor,
        - Number of executors per node= number of cores for a node/ number of cores for an executor = 8/8 = 1,
        - Executor-memory=32/1= 32GB
      - Calculating the Number of Executors: To calculate the number of executors, divide the available memory by the executor memory:
        - Total memory available for Spark = 80% of 512 GB = 410 GB
        - Number of executors = Total memory available for Spark / Executor memory = 410 GB / 32 GB ≈ 12 executors
        - Number of executors per node = Total Number of Executors/ Number of Nodes = 12/16 ≈ 1 Executors/Node
      - So, in this example, you would configure Spark with 16 executors, each executor having 8 core and 32 GB of memory.
      - Pros: 
        - Increased Parallelism: Fat executor configurations allocate more CPU cores and memory to each executor, resulting in improved processing speed and throughput.
        - Reduced Overhead: With fewer executor processes to manage, a fat executor configuration can reduce the overhead of task scheduling, inter-node communication, and executor coordination. This can lead to improved overall performance and resource utilization.
        - Enhanced Data Locality: Larger executor memory sizes can accommodate more data partitions in memory, reducing the need for data shuffling across the cluster.
        - Improved Performance for Complex Tasks:. By allocating more resources to each executor, you can efficiently handle complex computations and large-scale data processing.
      - Cons: 
        - Resource Overallocation: Using fat executors can result in overallocation of resources, especially if the cluster does not have sufficient memory or CPU cores.
        - Reduced Task Isolation: With larger executor configurations, tasks have fewer executor processes to run on. This can increase the chances of resource contention and interference between tasks, potentially impacting the stability and performance of your Spark application.
        - Longer Startup Times: Fat executor configurations require more resources and may have longer startup times compared to smaller configurations.
        - Difficulty in Resource Sharing: Fat executors may not be efficient when sharing resources with other applications or services running on the same cluster. It can limit the flexibility of resource allocation and hinder the ability to run multiple applications concurrently.
    - Balanced Executor Configuration: 
      - Spark founder Databricks after several trail and error testing the spark Executor and cores configuration, they recommends to have 2-5 cores per executor as the best initial efficient configuration for running the application smoothly.
      - Executor Memory and Cores per Executor: Considering having 3 cores per executor, Leaving 1 core per node for daemon processes
        - Number of executors per node= (number of cores for a node – core for daemon process)/ number of cores for an executor = 7/3 ≈ 2,
        - Executor-memory=Total memory per node/ number executors per node = 32/2= 16GB
      - Calculating the Number of Executors: To calculate the number of executors, divide the available memory by the executor memory:
        - Total memory available for Spark = 80% of 512 GB = 410 GB
        - Number of executors = Total memory available for Spark / Executor memory = 410 GB / 16 GB ≈ 32 executors
        - Number of executors per node = Total Number of Executors/ Number of Nodes = 32/16 = 2 Executors/Node
      - So, in this example, you would configure Spark with 32 executors, each executor having 3 core and 16 GB of memory.
      - Pros: 
        - Optimal Resource Utilization: A balanced executor configuration aims to evenly distribute resources across the cluster. This allows for efficient utilization of both CPU cores and memory, maximizing the overall performance of your Spark application.
        - Reasonable Parallelism: By allocating a moderate number of cores and memory to each executor, a balanced configuration strikes a balance between parallelism and resource efficiency. It can provide a good compromise between the high parallelism of small executors and the resource consumption of large executors.
        - Flexibility for Multiple Workloads: A balanced configuration allows for accommodating a variety of workloads. It can handle both small and large datasets, as well as diverse computational requirements, making it suitable for environments where multiple applications or different stages of data processing coexist.
        - Reduced Overhead: Compared to larger executor configurations, a balanced configuration typically involves fewer executor processes. This can reduce the overhead of task scheduling, inter-node communication, and executor coordination, leading to improved performance and lower resource consumption.
      - Cons: 
        - Limited Scaling: A balanced executor configuration may not scale as effectively as configurations with a higher number of cores or executors. In scenarios where the workload or dataset size significantly increases, a balanced configuration may reach its limit, potentially leading to longer processing times or resource contention.
        - Trade-off in Task Isolation: While a balanced configuration can provide a reasonable level of task isolation, it may not offer the same level of isolation as smaller executor configurations. In cases where tasks have distinct resource requirements or strict isolation requirements, a balanced configuration may not be the most suitable choice.
        - Task Granularity: In situations where the workload consists of a large number of small tasks, a balanced executor configuration may not offer the same level of fine-grained task allocation as smaller executor configurations. This can lead to suboptimal resource allocation and potentially impact performance.
        - Complexity in Resource Management: Maintaining a balanced executor configuration across a dynamic cluster can be challenging. As the cluster size and resource availability change, it may require frequent adjustments to ensure the configuration remains balanced, which can add complexity to cluster management.
  - Resources: [spark-tune-executor _al](https://sparkbyexamples.com/spark/spark-tune-executor-number-cores-and-memory/), [Spark Executor Core & Memory Explained _vl](https://www.youtube.com/watch?v=PP7r_L-HB50), [Distribution of Executors, Cores and Memory for a Spark Application _al](https://spoddutur.github.io/spark-notes/distribution_of_executors_cores_and_memory_for_spark_application.html)

- PySpark Window function performs statistical operations such as rank, row number, etc. on a group, frame, or collection of rows and returns results for each row individually.
- If you do a union in pyspark: Spark will simply append the dataframes, it will not append by using columns names. If you are using union then you should make sure the columns in the dataframe appear in same order because the appending appears to be happening in the order they appear.

```
df_schema = StructType([StructField('a', StringType(), True),])
result_schema = StructType([StructField('a', FloatType(), True),])
spark = SparkSession.builder.getOrCreate()
data = [('hello_world')]
df = spark.createDataFrame(data, df_schema)
result = spark.createDataFrame([], result_schema)
result = result.union(df)
## above code works even though the schema field is different
```

- [Difference between Hive internal tables and external tables? _al](https://stackoverflow.com/questions/17038414/difference-between-hive-internal-tables-and-external-tables):
  - Hive has a relational database on the master node it uses to keep track of state. For instance, when you CREATE TABLE FOO(foo string) LOCATION 'hdfs://tmp/';, this table schema is stored in the database.
  - If you have a partitioned table, the partitions are stored in the database(this allows hive to use lists of partitions without going to the file-system and finding them, etc). These sorts of things are the 'metadata'. When you drop an internal table, it drops the data, and it also drops the metadata.
  - When you drop an external table (create it like CREATE EXTERNAL TABLE FOO(foo string) LOCATION 'hdfs://tmp/';), it only drops the meta data. That means hive is ignorant of that data now. It does not touch the data itself.
- Caching in Spark: 
  - By default, data in a DataFrame is only present in Spark cluster while bing processed during a query, it won’t be persisted on a cluster afterwards. However, you can explicitly request Spark to persist DataFrame on the cluster by invoking df.cache. Cache can store as many partitions of the dataframe as the cluster memory allows.
  - Note that cache is another type of persist: df.cache is df.persist(StorageLevel.MEMORY_AND_DISK). This stores partitions in memory and spills excess to disk.
  - Cache should be used with care because caching consumes cluster resources that could otherwise be used for other executions, and it can prevent Spark from performing query optimization. You should only used cache in below situations:
    - DataFrames frequently used during Exploratory Data Analysis, iterative machine learning training in a Spark session
    - DataFrames accessed commonly for doing frequent transformations during ETL or building data pipelines
    - Don’t use when data is too big to fit in memory, or only need infrequent transformation
  - When you use cache() or persist(), the DataFrame is not fully cached until you invoke an action that goes through every record (e.g., count()). If you use an action like take(1)w, only one partition will be cached because Catalyst realizes that you do not need to compute all the partitions just to retrieve one record.
  - Don’t forget to cleanup with df.unpersist to evict the dataframe from cache when you no longer need it.
- In Apache Spark, the default memory configuration divides the JVM heap space, with 60% (0.6) allocated for unified memory (execution and storage) and 40% (0.4) reserved for user data structures and internal metadata. There are nitty gritties to it. 
- Here's a typical Spark code example that demonstrates several common operations. I'll explain how it gets broken down into jobs, stages, and tasks:

```
// Scala Spark example
val spark = SparkSession.builder()
  .appName("SparkJobExample")
  .master("local[*]")
  .getOrCreate()
// Read data from a CSV file
val inputDF = spark.read.option("header", "true").csv("input.csv")
// Transformation 1: Filter records
val filteredDF = inputDF.filter($"age" > 25)
// Transformation 2: Select specific columns
val selectedDF = filteredDF.select("name", "age", "salary")
// Transformation 3: Add a new column
val enrichedDF = selectedDF.withColumn("salary_category", 
  when($"salary" > 50000, "high").otherwise("low"))
// Action 1: Count total records (triggers Job 1)
val recordCount = enrichedDF.count()
println(s"Total records: $recordCount")
// Transformation 4: Group by and aggregate
val aggregatedDF = enrichedDF.groupBy("salary_category")
  .agg(
    avg("salary").as("avg_salary"),
    count("*").as("count")
  )
// Action 2: Show results (triggers Job 2)
aggregatedDF.show()
// Action 3: Write results to file (triggers Job 3)
aggregatedDF.write.mode("overwrite").parquet("output")
spark.stop()
```

  - Execution Breakdown
    - Jobs: This code will create 3 jobs because it has 3 actions: In Spark, a job is created whenever an action is called.
      - enrichedDF.count() - First job
      - aggregatedDF.show() - Second job
      - aggregatedDF.write.mode("overwrite").parquet("output") - Third job
    - Stages: The number of stages depends on the shuffle boundaries (like groupBy()/groupByKey()/reduceByKey()/join()/repartition()/coalesce()).
      - Job 1 (count): 1 stage (no shuffling needed for count operation on the transformed data)
      - Job 2 (show): 2 stages: Stage 1: All operations up to groupBy, Stage 2: The groupBy aggregation (requires a shuffle)
      - Job 3 (write to parquet): 2 stages: Stage 1: All operations up to groupBy, Stage 2: The groupBy aggregation and write operation
      - Total: 5 stages
    - Tasks: The number of tasks in each stage depends on: The number of partitions in your data, The parallelism in your cluster. Assuming the default behavior with, for example, 4 partitions:
      - Job 1, Stage 1: 4 tasks
      - Job 2, Stage 1: 4 tasks, Stage 2: 4 tasks (or possibly fewer, depending on the number of distinct salary categories)
      - Job 3, Stage 1: 4 tasks, Stage 2: 4 tasks (or possibly fewer)
      - Total: Approximately 16-20 tasks

- 
- 

----------------------------------------------------------------------





