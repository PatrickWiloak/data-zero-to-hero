---
last-updated: 2026-11-07
applies-to: Databricks exam page and exam guide (published 2026-10-08), read 2026-11-07
---

# Databricks Associate Developer for Apache Spark - Fact Sheet

## Exam Overview

**Exam Name:** Databricks Certified Associate Developer for Apache Spark
**Exam Code:** None published (no separate code)
**Duration:** 90 minutes
**Questions:** 45 scored multiple-choice questions, plus up to 10 unscored items
**Passing Score:** Not published; set statistically and may change
**Cost:** $200 USD plus applicable taxes (exam page and guide, read 2026-11-07)
**Delivery:** Online proctored or test center; no test aids
**Languages:** English; all code is in Python
**Valid For:** 2 years; recertify by passing the current exam
**Retakes:** Wait 14 days between attempts; the full fee applies each time
**Prerequisites:** None; related training recommended; 6+ months of hands-on Spark work recommended

Registration: through the exam delivery platform at webassessor.com/databricks (per the exam page). The guide's update table shows "Exam Published October 8, 2026" and "Exam Guide Updated October 8, 2026". The exam page's navigation also calls this certification "Apache Spark Developer Associate".

**[📖 Official exam page](https://www.databricks.com/learn/certification/apache-spark-developer-associate)** - format, fee, weights, registration
**[📖 Exam guide PDF (August 2026)](https://www.databricks.com/sites/default/files/2026-10/databricks-certified-associate-developer-apache-spark-exam-guide-august-2026.pdf)** - every objective and sample questions
**[📖 Apache Spark documentation](https://spark.apache.org/docs/latest/)** - the authoritative reference for the engine
**[📖 Databricks documentation](https://docs.databricks.com)** - platform documentation linked from the guide

## Sections and Weights

| # | Section | Weight |
|---|---|---|
| 1 | Apache Spark Architecture and Components | 20% |
| 2 | Using Spark SQL | 20% |
| 3 | Developing Apache Spark DataFrame/Dataset API Applications | 30% |
| 4 | Troubleshooting and Tuning Apache Spark DataFrame API Applications | 10% |
| 5 | Structured Streaming | 10% |
| 6 | Using Spark Connect to Deploy Applications | 5% |
| 7 | Using Pandas API on Apache Spark | 5% |

## Target Audience

- Developers and data engineers who write Spark applications in Python
- Data scientists and analysts moving from pandas to distributed processing
- Anyone preparing for Databricks data engineering exams who wants Spark fundamentals first

## Section 1: Architecture and Components (20%)

**[📖 Cluster mode overview](https://spark.apache.org/docs/latest/cluster-overview.html)** - driver, executors, cluster manager
**[📖 RDD programming guide](https://spark.apache.org/docs/latest/rdd-programming-guide.html)** - lazy evaluation, persistence, shuffles
**[📖 Tuning Spark](https://spark.apache.org/docs/latest/tuning.html)** - memory and GC

**Key Facts:**
- Driver plans and sends tasks; executors run tasks and cache data; cluster manager allocates resources (Standalone, YARN, Kubernetes)
- Action creates a job; shuffle boundaries split stages; one task per partition
- Transformations are lazy; actions trigger execution; wide transformations shuffle
- `repartition` shuffles to n balanced partitions; `coalesce` merges without a full shuffle
- `cache()` for RDDs is MEMORY_ONLY; DataFrame cache reported memory and disk in a local run
- Modules: Spark SQL and DataFrames, pandas API on Spark, MLlib, Structured Streaming, GraphX

## Section 2: Using Spark SQL (20%)

**[📖 Generic load/save functions](https://spark.apache.org/docs/latest/sql-data-sources-load-save-functions.html)** - save modes, partitioning, tables

**Key Facts:**
- Default source is Parquet; save modes: error (default), append, overwrite, ignore; not atomic
- `partitionBy` for low cardinality; `bucketBy` and `sortBy` need `saveAsTable`
- SQL on files: ``SELECT * FROM parquet.`/path` ``
- `createOrReplaceTempView` is session scoped; global temp views use `global_temp`
- External table (with `path`) keeps data on drop

## Section 3: DataFrame API (30%)

**[📖 Performance tuning](https://spark.apache.org/docs/latest/sql-performance-tuning.html)** - broadcast threshold and hints
**[📖 Spark SQL data types](https://spark.apache.org/docs/latest/sql-ref-datatypes.html)** - schemas

**Key Facts:**
- `explode` drops empty arrays; `explode_outer` keeps them as null
- `union` is by position and keeps duplicates; `unionByName` is by name
- Joins: inner, left, right, full, left_semi, left_anti, crossJoin; list of columns for multiple keys
- `F.broadcast` hint; auto-broadcast threshold 10 MB
- `from_unixtime` renders in the session time zone
- Accumulators: exactly once in actions, may repeat in transformations, zero until an action
- Python UDFs are row at a time; prefer built-ins

## Section 4: Troubleshooting and Tuning (10%)

**[📖 Performance tuning: AQE](https://spark.apache.org/docs/latest/sql-performance-tuning.html)** - AQE features

**Key Facts:**
- `spark.sql.shuffle.partitions` 200; `spark.sql.files.maxPartitionBytes` 128 MB
- AQE on by default since 3.2: coalesce partitions (64 MB advisory), switch to broadcast, split skewed partitions (5x median and 256 MB)
- Skew fixes: AQE, salting, broadcast; not more executors
- Executor logs for OOM and GC; driver for collect problems; Spark UI on port 4040

## Section 5: Structured Streaming (10%)

**[📖 Structured Streaming basics](https://spark.apache.org/docs/latest/streaming/getting-started.html)** - model and guarantees
**[📖 Window operations and watermarking](https://spark.apache.org/docs/latest/streaming/apis-on-dataframes-and-datasets.html)** - windows and dedup

**Key Facts:**
- Output modes: append, update, complete (complete keeps all state)
- Exactly-once: replayable sources, checkpoint, idempotent sinks
- Watermark before the aggregation on the same event-time column; append or update mode
- Dedup without watermark keeps unbounded state; `dropDuplicatesWithinWatermark` needs a watermark
- `distinct` and `limit` are not supported on streams

## Section 6: Spark Connect (5%)

**[📖 Spark Connect overview](https://spark.apache.org/docs/latest/spark-connect-overview.html)** - architecture
**[📖 Submitting applications](https://spark.apache.org/docs/latest/submitting-applications.html)** - deploy modes

**Key Facts:**
- Thin client, unresolved logical plans, gRPC, Arrow results; default port 15002; no RDD or SparkContext
- Client mode: driver with the submitter; cluster mode: driver on a worker; local: one JVM

## Section 7: Pandas API on Spark (5%)

**[📖 Pandas UDFs](https://spark.apache.org/docs/latest/api/python/tutorial/sql/arrow_pandas.html)** - forms and caveats

**Key Facts:**
- `import pyspark.pandas as ps` runs pandas-style code distributed
- Pandas UDF: Series in, Series out, same length; Arrow batches (10,000 rows default)
- Forms: Series to Series, Iterator, Series to scalar, grouped map with `applyInPandas`

## Exam Tips

1. **The DataFrame API is 30%.** Be able to read code and predict output, including nulls, unions and joins.
2. **Know lazy versus eager** and narrow versus wide cold.
3. **Do not say "add executors"** for skew or a slow single task.
4. **Streaming:** modes, watermark placement, exactly-once ingredients.
5. **Time:** about 2 minutes per question.

## Quick Reference

```python
df.filter(F.col("amount") > 0).groupBy("customer").agg(F.sum("amount").alias("total"))
c.join(F.broadcast(p), ["id"], "left")
df.write.mode("overwrite").partitionBy("customer").parquet(path)
ev.withWatermark("timestamp", "10 minutes").groupBy(F.window("timestamp", "5 minutes")).count()
```
