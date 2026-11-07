---
last-updated: 2026-11-07
---

# Databricks Certified Associate Developer for Apache Spark - Practice Questions

24 scenario-based questions for the Apache Spark Developer Associate exam, tagged with the seven sections of the guide published 2026-10-08. Weights: DataFrame API 30%, Architecture 20%, Spark SQL 20%, Troubleshooting and tuning 10%, Structured Streaming 10%, Spark Connect 5%, Pandas API 5%. All code is Python.

> **Cert page:** [exams/databricks/apache-spark-developer-associate/](../../exams/databricks/apache-spark-developer-associate/)

> Original scenario questions for study. They contain no vendor exam content.

---

### Question 1 - Section 1: Spark Architecture and Components
**Scenario:** A notebook cell defines df2 = df.filter('amount > 100').select('id') and finishes instantly without reading any data. Why?

A. The optimizer decided the filter condition always evaluates to false for the table
B. The cluster manager rejected the job because no executor was free to run it
C. Spark cached the result on the driver during the earlier cell, so nothing was read
D. filter and select are lazy transformations, so no job runs until an action is called

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Transformations only build a plan; an action such as count or write triggers a job. Nothing in the story suggests caching, a rejected job or a constant-false filter.

**Key Concept:** exams/databricks/apache-spark-developer-associate/notes/01-spark-architecture-and-components.md
</details>

---

### Question 2 - Section 1: Spark Architecture and Components
**Scenario:** Which statement correctly describes the relationship between jobs, stages and tasks?

A. An action starts a job, shuffles split it into stages, stages run tasks per partition
B. A task is made of several jobs, and a job is made of several stages in any order
C. Each transformation creates a job, and each job runs exactly one task on a single executor
D. A stage is created for each executor, and tasks are created for each column in the data

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** One action triggers a job; wide operations end stages; each stage runs one task per partition. Transformations do not create jobs by themselves, stages are not per executor, and tasks are the smallest unit, not a container of jobs.

**Key Concept:** exams/databricks/apache-spark-developer-associate/notes/01-spark-architecture-and-components.md
</details>

---

### Question 3 - Section 1: Spark Architecture and Components
**Scenario:** A data engineer calls collect() on a 300 GB DataFrame and the application fails with an out-of-memory error on the node that launched it. Which component ran out of memory?

A. The cluster manager, because it stores every executor's results
B. The executors, because collect() caches partitions on the worker disks
C. The driver, because collect() returns all rows to the driver process
D. The worker nodes, because collect() duplicates data in the shuffle service

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** collect() brings all results into the driver's memory, so a very large result exhausts it. The cluster manager only allocates resources, and executors hold partitions rather than the final collected result.

**Key Concept:** exams/databricks/apache-spark-developer-associate/notes/01-spark-architecture-and-components.md
</details>

---

### Question 4 - Section 1: Spark Architecture and Components
**Scenario:** A DataFrame will be used by four separate actions in a job, and recomputing it is expensive. What is the most appropriate step?

A. Increase spark.sql.shuffle.partitions so that each action runs faster
B. Call cache() or persist() on it before the first action and unpersist() when finished
C. Call repartition(1) so that all four actions read from a single partition
D. Convert it to an RDD of Python objects and call collect() on it once

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Caching keeps the computed partitions for reuse, and lost partitions are recomputed from lineage if needed. A single partition removes parallelism, collecting everything to Python does not scale, and the shuffle partition count does not avoid recomputation.

**Key Concept:** exams/databricks/apache-spark-developer-associate/notes/01-spark-architecture-and-components.md
</details>

---

### Question 5 - Section 1: Spark Architecture and Components
**Scenario:** Which operation is a wide transformation that normally requires a shuffle?

A. withColumn('x', col('a') + 1)
B. select('id', 'amount')
C. filter(col('amount') > 0)
D. groupBy('customer').count()

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** A grouped aggregation needs all rows with the same key together, so it shuffles. Filter, withColumn and select compute each output partition from a single input partition.

**Key Concept:** exams/databricks/apache-spark-developer-associate/notes/01-spark-architecture-and-components.md
</details>

---

### Question 6 - Section 2: Using Spark SQL
**Scenario:** A job must reload a daily Parquet output folder so that reruns replace the previous output rather than add rows or fail. Which write is appropriate?

A. df.write.parquet(path) using the default save mode
B. df.write.mode('append').parquet(path)
C. df.write.mode('ignore').parquet(path)
D. df.write.mode('overwrite').parquet(path)

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Overwrite replaces existing data. Append would duplicate rows on a rerun, ignore would silently keep stale data, and the default error mode fails when the path exists.

**Key Concept:** exams/databricks/apache-spark-developer-associate/notes/02-using-spark-sql.md
</details>

---

### Question 7 - Section 2: Using Spark SQL
**Scenario:** Which write call is valid only for persistent tables created with saveAsTable?

A. mode('overwrite').format('csv')
B. bucketBy(42, 'name').sortBy('age')
C. partitionBy('country', 'region')
D. option('compression', 'snappy')

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** bucketBy and sortBy work only with saveAsTable. partitionBy works with both save and saveAsTable, and mode, format and option calls apply to any write.

**Key Concept:** exams/databricks/apache-spark-developer-associate/notes/02-using-spark-sql.md
</details>

---

### Question 8 - Section 2: Using Spark SQL
**Scenario:** An analyst wants to query a DataFrame with SQL within the current Spark session only. What should they use?

A. df.cache() and then query the cache name
B. df.createGlobalTempView('t') and query it as t in another application
C. df.write.saveAsTable('t') with an explicit path option
D. df.createOrReplaceTempView('t') then spark.sql('select ... from t')

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** A temporary view is session-scoped and queryable with SQL. saveAsTable persists the data, a global view needs the global_temp prefix and is shared across sessions of one application, and cache does not create a SQL name.

**Key Concept:** exams/databricks/apache-spark-developer-associate/notes/02-using-spark-sql.md
</details>

---

### Question 9 - Section 2: Using Spark SQL
**Scenario:** Which statement about partitionBy('country') on write is true?

A. It writes one directory per country value, enabling directory skipping on reads
B. It splits data into a fixed number of buckets chosen by hashing the country column
C. It sorts rows inside each file by country so that range queries are faster
D. It sets the number of in-memory partitions used by later shuffles in the job

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** partitionBy lays out one directory per value, enabling partition pruning on reads. Bucketing uses a fixed number of buckets, shuffle partition counts are a separate setting, and partitionBy does not sort within files.

**Key Concept:** exams/databricks/apache-spark-developer-associate/notes/02-using-spark-sql.md
</details>

---

### Question 10 - Section 2: Using Spark SQL
**Scenario:** How do you run SQL directly on a Parquet folder without creating a DataFrame first?

A. spark.read.sql('/data/orders')
B. spark.sql("select * from /data/orders format parquet")
C. spark.sql("select * from parquet.`/data/orders`")
D. spark.table('/data/orders')

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Spark SQL accepts a format-prefixed path in backticks. The other forms are not valid ways to query files directly.

**Key Concept:** exams/databricks/apache-spark-developer-associate/notes/02-using-spark-sql.md
</details>

---

### Question 11 - Section 3: DataFrame/Dataset API Applications
**Scenario:** A table has an array column tags. Some rows have an empty array. A report needs one row per tag but must keep rows with no tags, showing null. Which function is right?

A. explode_outer('tags')
B. explode('tags')
C. split('tags', ',')
D. flatten('tags')

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** explode_outer keeps rows whose array is empty or null and emits null. explode drops those rows, split converts a string to an array, and flatten merges nested arrays.

**Key Concept:** exams/databricks/apache-spark-developer-associate/notes/03-dataframe-api-applications.md
</details>

---

### Question 12 - Section 3: DataFrame/Dataset API Applications
**Scenario:** Two DataFrames have the same columns in a different order: a(id, amount) and b(amount, id). The engineer wants rows stacked by column name. Which call is correct?

A. a.crossJoin(b)
B. a.unionByName(b)
C. a.join(b, 'id', 'union')
D. a.union(b)

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** unionByName matches columns by name, whereas union matches by position and would put amounts under id. The other calls do not stack rows by name.

**Key Concept:** exams/databricks/apache-spark-developer-associate/notes/03-dataframe-api-applications.md
</details>

---

### Question 13 - Section 3: DataFrame/Dataset API Applications
**Scenario:** A small dimension table is joined to a very large fact table, and Spark is shuffling the large side. What is the most direct improvement?

A. Call coalesce(1) on the large fact table before joining
B. Replace the join with a cross join and filter afterwards
C. Wrap the small table with F.broadcast() in the join
D. Cache the large fact table with DISK_ONLY storage

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** A broadcast join ships the small table to every executor and avoids shuffling the large side. coalesce(1) removes parallelism, a cross join explodes the row count, and caching does not remove the shuffle.

**Key Concept:** exams/databricks/apache-spark-developer-associate/notes/03-dataframe-api-applications.md
</details>

---

### Question 14 - Section 3: DataFrame/Dataset API Applications
**Scenario:** An engineer adds to an accumulator inside a map transformation and prints its value before running any action. What is printed?

A. An error, because accumulators can only be created inside actions
B. The number of rows, because map updates the accumulator as it is defined
C. Zero, because transformations are lazy and the update has not run yet
D. Null, because accumulators cannot be read on the driver

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Lazy transformations do not execute until an action runs, so the accumulator is still at its initial value. It is readable on the driver and can be created any time.

**Key Concept:** exams/databricks/apache-spark-developer-associate/notes/03-dataframe-api-applications.md
</details>

---

### Question 15 - Section 3: DataFrame/Dataset API Applications
**Scenario:** Which choice is the best way to apply logic to a column when a built-in Spark function already exists for it?

A. Write a Python UDF, because UDFs are always faster than built-in functions
B. Use the built-in function, because Spark can optimize it and it avoids row-by-row Python
C. Collect the column to the driver and loop over it with a Python for statement
D. Convert the DataFrame to RDDs and apply a lambda function to each element

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Built-ins are optimized by Catalyst and run in the JVM. Python UDFs run row by row in Python workers and are generally slower, and collecting or RDD lambdas are less efficient still.

**Key Concept:** exams/databricks/apache-spark-developer-associate/notes/03-dataframe-api-applications.md
</details>

---

### Question 16 - Section 3: DataFrame/Dataset API Applications
**Scenario:** A column holds Unix epoch seconds. Which expression returns the calendar year as an integer?

A. F.year(F.to_date(F.from_unixtime('ts')))
B. F.to_date('ts', 'yyyy')
C. F.date_format('ts', 'yyyy').cast('date')
D. F.from_unixtime('ts').year

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** from_unixtime converts seconds to a string, to_date converts it to a date and year extracts the integer year. Attribute access on a column does not exist, and the other forms parse or format incorrectly.

**Key Concept:** exams/databricks/apache-spark-developer-associate/notes/03-dataframe-api-applications.md
</details>

---

### Question 17 - Section 3: DataFrame/Dataset API Applications
**Scenario:** Which statement about dropDuplicates(['id']) is correct?

A. It keeps one row for each distinct id and removes the other rows
B. It removes every row whose id appears more than once, keeping none of them
C. It sorts the DataFrame by id and removes null ids
D. It converts id to a primary key that later writes will enforce

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** dropDuplicates with a subset keeps one row per distinct combination of those columns. It does not remove all copies, sort or enforce constraints.

**Key Concept:** exams/databricks/apache-spark-developer-associate/notes/03-dataframe-api-applications.md
</details>

---

### Question 18 - Section 4: Troubleshooting and Tuning
**Scenario:** Most tasks in a stage finish in seconds, but one task processes far more data and runs for several minutes. What is the likely cause?

A. Data skew, where one key holds a large share of the rows
B. The driver is using client deploy mode instead of cluster mode
C. The DataFrame was cached with a serialized storage level
D. Too many executors, so tasks wait for each other to finish

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** A single oversized task is the signature of skew. Extra executors do not cause one slow task, and deploy mode and cache level do not produce an uneven partition.

**Key Concept:** exams/databricks/apache-spark-developer-associate/notes/04-troubleshooting-and-tuning.md
</details>

---

### Question 19 - Section 4: Troubleshooting and Tuning
**Scenario:** Which two Adaptive Query Execution features reduce problems after a shuffle?

A. Converting all aggregations to RDD operations and removing the optimizer
B. Disabling broadcast joins and increasing the number of shuffle partitions
C. Coalescing small shuffle partitions and splitting skewed join partitions
D. Forcing every join to a sort-merge join and caching its output

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** AQE merges small post-shuffle partitions toward an advisory size and splits skewed partitions in sort-merge joins. The other choices describe changes AQE does not make.

**Key Concept:** exams/databricks/apache-spark-developer-associate/notes/04-troubleshooting-and-tuning.md
</details>

---

### Question 20 - Section 5: Structured Streaming
**Scenario:** A streaming aggregation counts events in 10-minute event-time windows and must drop state for windows that are far behind. What is required?

A. A complete output mode so that all state is kept and can be reconciled
B. A trigger of availableNow so that old windows are dropped when the query ends
C. withWatermark on the event-time column, in append or update output mode
D. A call to dropDuplicates() without columns after the aggregation completes

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** A watermark on the aggregated event-time column, placed before the aggregation, with append or update mode, lets Spark finalize windows and clean state. Complete mode keeps all state, and the other options do not bound state.

**Key Concept:** exams/databricks/apache-spark-developer-associate/notes/05-structured-streaming.md
</details>

---

### Question 21 - Section 5: Structured Streaming
**Scenario:** Which combination gives a Structured Streaming query end-to-end exactly-once semantics after a failure?

A. Caching the input stream and restarting with a new checkpoint directory
B. Replayable sources, a checkpoint of progress and idempotent sinks
C. A fast network, local disks on every executor and a large driver heap
D. Update output mode, a one-second trigger and several partitions per source

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Exactly-once relies on replayable offsets, a checkpoint recording what was processed and sinks that tolerate replays. Output mode, triggers, hardware or a new checkpoint do not provide that guarantee.

**Key Concept:** exams/databricks/apache-spark-developer-associate/notes/05-structured-streaming.md
</details>

---

### Question 22 - Section 5: Structured Streaming
**Scenario:** A streaming DataFrame has duplicate events with the same guid and the same event time. The team wants to remove duplicates but keep state bounded. What should they do?

A. Switch to complete output mode so that duplicates are overwritten each trigger
B. Call dropDuplicates on guid only, with no watermark, and keep unlimited state
C. Call distinct() on the streaming DataFrame before writing to the sink
D. Set a watermark on the event-time column and dropDuplicates on guid plus event time

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** With a watermark and the event-time column in the key, old state can be dropped. Without a watermark state grows forever, and distinct() is not supported on streaming DataFrames.

**Key Concept:** exams/databricks/apache-spark-developer-associate/notes/05-structured-streaming.md
</details>

---

### Question 23 - Section 6: Spark Connect and Deployment
**Scenario:** Which description of Spark Connect is correct?

A. A thin client sends unresolved logical plans over gRPC to a Spark server
B. The client runs inside the Spark driver JVM and exposes RDD and SparkContext APIs to remote users
C. The client downloads all data to its own memory and runs the optimizer locally
D. The server executes Python lambdas by connecting directly to each executor from the client

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Spark Connect decouples client and server using unresolved logical plans over gRPC with Arrow-encoded results. RDDs and SparkContext are not supported, and the optimizer runs on the server.

**Key Concept:** exams/databricks/apache-spark-developer-associate/notes/06-spark-connect-and-deployment.md
</details>

---

### Question 24 - Section 7: Pandas API on Spark
**Scenario:** A team needs a vectorized function that doubles a numeric column using pandas, with Spark moving data in Arrow batches. Which definition is right?

A. df.toPandas() and then a Python for loop that doubles each value
B. @pandas_udf('long') def f(s: pd.Series) -> pd.Series: return s * 2
C. df.rdd.map(lambda r: r.x * 2), applied to each element of the RDD
D. @udf('long') def f(s): return s * 2, applied one row at a time

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** A pandas UDF with Series type hints is vectorized and uses Arrow batches. A plain udf works row by row, RDD lambdas bypass the optimizer, and toPandas collects everything to the driver.

**Key Concept:** exams/databricks/apache-spark-developer-associate/notes/07-pandas-api-on-spark.md
</details>

---
