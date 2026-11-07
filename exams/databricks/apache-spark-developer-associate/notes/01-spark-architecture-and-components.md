---
last-updated: 2026-11-07
applies-to: Databricks Associate Developer for Apache Spark exam guide (published 2026-10-08, read 2026-11-07)
difficulty: intermediate
reading-time: 35 min
---

# Section 1: Apache Spark Architecture and Components

Weighted **20%**, about nine questions. Seven objectives: advantages and challenges of Spark; the roles of cluster, driver, workers and executors; DataFrame and Dataset concepts, the SparkSession lifecycle, caching, storage levels and garbage collection; the execution hierarchy; partitioning, shuffles and partitions; actions, transformations and lazy evaluation; and the Spark modules.

All code on the exam is Python. Examples here were run on PySpark 4.3.0 in local mode (Python 3.12, Java 25) on 2026-11-07 unless marked "not run".

## Table of Contents

1. [What Spark is, and what is hard about it](#what-spark-is-and-what-is-hard-about-it)
2. [The cast: driver, cluster manager, executors](#the-cast-driver-cluster-manager-executors)
3. [The execution hierarchy](#the-execution-hierarchy)
4. [DataFrames, the SparkSession and lazy evaluation](#dataframes-the-sparksession-and-lazy-evaluation)
5. [Partitions and shuffles](#partitions-and-shuffles)
6. [Caching, storage levels and garbage collection](#caching-storage-levels-and-garbage-collection)
7. [The Spark modules](#the-spark-modules)
8. [How the exam tests this section](#how-the-exam-tests-this-section)
9. [Key terms](#key-terms)

---

## What Spark is, and what is hard about it

Apache Spark is a unified analytics engine for large-scale data processing. "Unified" means one engine and one set of APIs for batch SQL, DataFrame transformations, streaming and machine learning, instead of a separate system for each. The big idea underneath is that your data is split into **partitions**, and the same operation runs on many partitions at once across many machines.

**[📖 Spark overview](https://spark.apache.org/docs/latest/index.html)** - Spark is a unified engine with SQL and DataFrames, a pandas API, MLlib, Structured Streaming and GraphX.

The advantages follow from that design: it scales out by adding machines, keeps intermediate data in memory where it can, recovers from failures by recomputing lost partitions, and offers high-level APIs in Python, Scala, Java and SQL that Spark's optimizer plans for you. The challenges are the mirror image: distribution has a price. Moving data between machines (a **shuffle**) is slow and is the main cost to manage; uneven data (**skew**) leaves one task running long while the rest wait; memory is finite, so big partitions spill to disk or crash executors; and a small job on a big cluster wastes more time on overhead than it saves. Many exam answers boil down to "do less shuffling" or "make the partitions even".

## The cast: driver, cluster manager, executors

Think of a restaurant. The **driver** is the head chef who reads the order, plans the dishes and hands out tasks; the **executors** are the cooks who do the work and keep ingredients at hand; the **cluster manager** is the kitchen manager who decides how many cooks you get.

| Component | Role |
|---|---|
| **Driver program** | Runs your `main()`, creates the SparkContext/SparkSession, builds the plan and sends **tasks** to executors. If the driver dies, the application dies. |
| **Cluster manager** | Allocates resources across applications. Spark supports Standalone, Hadoop YARN and Kubernetes. |
| **Worker node** | Any machine that can run application code. |
| **Executor** | A process on a worker that runs tasks in threads and stores data in memory or on disk for your application. Executors live as long as the application. |
| **Core / slot** | One task runs at a time per core allocated to an executor, so total cores bounds parallelism. |

**[📖 Cluster mode overview](https://spark.apache.org/docs/latest/cluster-overview.html)** - driver, cluster manager, executors, tasks and the execution flow

Each application gets its own executors, so applications are isolated; they share data only through external storage. Executors must be able to reach the driver for the whole run, so the driver should be near the workers. Two practical consequences matter for the exam: `collect()` pulls all results into **driver** memory (a classic out-of-memory cause), while heavy per-row work runs on **executors**.

## The execution hierarchy

When you call an **action**, Spark creates a **job**. Spark splits the job into **stages** at every shuffle boundary, and each stage runs many **tasks**, one per partition. So: application, then jobs, then stages, then tasks.

- **Job** - the whole computation triggered by one action such as `count`, `collect` or `write`.
- **Stage** - a set of tasks that can run without moving data between executors. A wide transformation (join, groupBy) ends a stage.
- **Task** - the smallest unit: one operation on one partition, run by one executor core.

The Spark UI (typically on port 4040 of the driver) shows jobs, stages, tasks, executors and storage. When a question gives a symptom, such as one task much slower than its peers, the answer lives at the stage or task level.

## DataFrames, the SparkSession and lazy evaluation

A **DataFrame** is a distributed table with named, typed columns, and a **Dataset** is the typed variant (in Scala and Java; in Python you use DataFrames). Both describe a computation, optimized by the Catalyst optimizer, rather than holding all data in your Python process. The **SparkSession** is the entry point: you create it once (`SparkSession.builder...getOrCreate()`), use it to read data and run SQL, and stop it with `spark.stop()` when the application ends. In notebooks on Databricks the session already exists as `spark`.

Transformations are **lazy**; actions are **eager**. Transformations (`select`, `filter`, `withColumn`, `join`, `groupBy`) only add steps to a plan. Actions (`count`, `collect`, `show`, `take`, `write`) force Spark to run it. Laziness lets Spark see the whole chain and optimize it, for example pruning columns and pushing filters down before reading data.

**[📖 RDD programming guide](https://spark.apache.org/docs/latest/rdd-programming-guide.html)** - transformations are lazy and only computed when an action needs a result

```python
# Ran on PySpark 4.3.0 locally.
x = df.filter(F.col("id") > 1).select("id")   # transformation: returns a DataFrame, nothing runs yet
print(type(x).__name__)                        # DataFrame
print(x.count())                               # action: runs the job, prints 3 for the sample data
```

Transformations are also classified by how they move data. A **narrow** transformation (`filter`, `select`, `withColumn`) computes each output partition from one input partition, with no shuffle. A **wide** transformation (`groupBy`, `join`, `distinct`, `orderBy`, `repartition`) needs data from many input partitions, so it shuffles and starts a new stage.

## Partitions and shuffles

A **partition** is a chunk of the dataset that one task processes. Too few partitions leave cores idle; too many make tasks tiny and add overhead. The tuning guide suggests 2-3 tasks per CPU core as a starting point for parallelism.

A **shuffle** is the all-to-all redistribution of data across partitions needed when values for a key must be together. It involves disk I/O, serialization and network I/O, so it is costly. Operations that trigger shuffles include `repartition`, key-based aggregations, sorts and joins. Settings to know: `spark.sql.shuffle.partitions` defaults to 200 (the number of partitions after a shuffle in joins and aggregations), and `spark.sql.files.maxPartitionBytes` defaults to 128 MB (how much file data goes into one input partition).

**[📖 Performance tuning](https://spark.apache.org/docs/latest/sql-performance-tuning.html)** - partition settings, join strategy and AQE defaults

`repartition(n)` shuffles to exactly n partitions (it can increase or decrease and balances data); `coalesce(n)` only merges existing partitions to reduce the count and avoids a full shuffle, so use it to shrink output files after a filter.

```python
# Ran on PySpark 4.3.0 locally.
print(o.rdd.getNumPartitions(), o.repartition(8).rdd.getNumPartitions(), o.repartition(8).coalesce(2).rdd.getNumPartitions())
# prints: 2 8 2
```

## Caching, storage levels and garbage collection

If a DataFrame is used by several actions, Spark recomputes it each time unless you cache it. `df.cache()` marks it to be kept after the first action computes it; `df.persist(level)` chooses the storage level; `df.unpersist()` frees it. Lost cached partitions are recomputed from their lineage, so caching is an optimization, not a durability guarantee.

Storage levels trade memory, CPU and disk. In the RDD API `cache()` means `MEMORY_ONLY`; for DataFrames, I observed locally that `df.cache()` reported "Disk Memory Deserialized 1x Replicated", i.e. memory and disk. Levels you should recognise:

| Level | Meaning |
|---|---|
| `MEMORY_ONLY` | Deserialized objects in memory; partitions that do not fit are recomputed |
| `MEMORY_AND_DISK` | Spill partitions that do not fit to disk |
| `DISK_ONLY` | Partitions only on disk |
| `*_2` | Same, replicated on two nodes |
| `MEMORY_ONLY_SER` | Serialized in memory (Java/Scala); in Python data is always pickled so the serialized levels do not matter |

**Garbage collection** matters because executors are JVMs. Java objects are bloated (often 2-5 times the raw data size), and GC cost grows with the number of objects. When GC time is high, the tuning guide's first suggestion is serialized caching and fewer, larger objects; it also covers resizing the young generation and lowering `spark.memory.fraction`. Execution and storage share a unified memory region (`spark.memory.fraction` is 0.6 of heap minus 300 MiB) and `spark.memory.storageFraction` (0.5) protects cached blocks from eviction. Since Spark 4.0.0, JDK 17 and G1GC are the defaults.

**[📖 Tuning Spark](https://spark.apache.org/docs/latest/tuning.html)** - memory management, GC tuning, parallelism and broadcasting

## The Spark modules

| Module | Purpose |
|---|---|
| **Spark Core** | The engine and the lower-level RDD API |
| **Spark SQL and DataFrames** | Structured data, SQL and the optimizer |
| **Pandas API on Spark** | pandas-style code distributed on Spark |
| **Structured Streaming** | Incremental stream processing on DataFrames |
| **MLlib** | Machine learning |
| **GraphX** | Graph processing |

## How the exam tests this section

- **Who does what:** driver plans and collects; executors run tasks and cache data; the cluster manager allocates resources.
- **Hierarchy:** action creates job; shuffle boundary creates stages; partition creates tasks.
- **Lazy evaluation:** "nothing happened when I ran the cell" means no action yet.
- **Narrow versus wide**, and which causes a shuffle.
- **repartition versus coalesce:** shuffle and balance versus merge without shuffle.
- **Caching:** use when reused; `unpersist` when done; cached data is recomputable.

## Key terms

- **Driver** - the process that runs your program, builds the execution plan and sends tasks to executors.
- **Executor** - a process on a worker node that runs tasks and stores cached data for one application.
- **Cluster manager** - the service that allocates cluster resources to Spark applications, such as YARN, Kubernetes or Standalone.
- **Job** - the parallel computation triggered by a single action.
- **Stage** - a set of tasks that can run without a shuffle, bounded by shuffle boundaries.
- **Task** - the smallest unit of work, one operation on one partition.
- **Partition** - a chunk of a dataset that a single task processes.
- **Shuffle** - the costly redistribution of data across partitions needed by wide operations like joins and aggregations.
- **Lazy evaluation** - the behaviour where transformations only build a plan and nothing runs until an action is called.
- **Transformation** - an operation that defines a new dataset from an existing one, such as filter or select, without running it.
- **Action** - an operation that triggers execution and returns a result or writes output, such as count or collect.
- **SparkSession** - the unified entry point for reading data, running SQL and configuring a Spark application.
- **Narrow transformation** - a transformation where each output partition depends on one input partition, so no shuffle is needed.
- **Wide transformation** - a transformation that needs data from many input partitions, causing a shuffle.
- **Storage level** - the setting that decides whether cached data lives in memory, on disk or both.
