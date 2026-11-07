---
last-updated: 2026-11-07
applies-to: Databricks Associate Developer for Apache Spark exam guide (published 2026-10-08, read 2026-11-07)
difficulty: intermediate
reading-time: 25 min
---

# Section 4: Troubleshooting and Tuning Apache Spark DataFrame API Applications

Weighted **10%**, about four or five questions. Three objectives: performance tuning (partitioning, repartitioning, coalescing, data skew, reducing shuffling); Adaptive Query Execution; and logging and monitoring with driver and executor logs to diagnose out-of-memory errors and underutilized clusters.

## Table of Contents

1. [Partitioning for performance](#partitioning-for-performance)
2. [Skew and shuffles](#skew-and-shuffles)
3. [Adaptive Query Execution](#adaptive-query-execution)
4. [Logs, the Spark UI and common failures](#logs-the-spark-ui-and-common-failures)
5. [How the exam tests this section](#how-the-exam-tests-this-section)
6. [Key terms](#key-terms)

---

## Partitioning for performance

The goal is enough, evenly sized partitions: about 2-3 tasks per core is the tuning guide's starting point. Too few partitions leave cores idle and risk out-of-memory in big tasks; too many create scheduling overhead and tiny files.

| Tool | What it does | Use when |
|---|---|---|
| `repartition(n)` or `repartition(n, "col")` | Full shuffle to n balanced partitions, optionally hashed by column | Increasing parallelism, fixing uneven partitions, co-locating keys |
| `coalesce(n)` | Merges existing partitions without a full shuffle | Reducing the count, for example fewer output files after a filter |
| `spark.sql.shuffle.partitions` | Partitions after a shuffle in joins and aggregations (default 200) | Too many tiny tasks on small data, or too few on huge data |
| `spark.sql.files.maxPartitionBytes` | Bytes per input partition when reading files (default 128 MB) | Controlling read parallelism |

`coalesce` can only reduce the count and may leave partitions uneven; `repartition` balances at the cost of a shuffle.

**[📖 Performance tuning](https://spark.apache.org/docs/latest/sql-performance-tuning.html)** - partition settings, coalesce and repartition hints

## Skew and shuffles

**Skew** means a few keys hold most of the rows, so a few tasks do most of the work. In the Spark UI stage page you see one task far longer than the median and far more shuffle read. Remedies, roughly in order: let AQE split skewed partitions; filter or handle hot keys (for example null keys) separately; broadcast the small side of a join so the large side never shuffles; or add a random "salt" to the key and aggregate twice. More executors do not fix skew, because one task is still one task.

**Reducing shuffles:** filter and select early so less data moves; broadcast small tables; use built-in aggregations that pre-aggregate on the map side (`reduceByKey` does, `groupByKey` does not); avoid unnecessary `distinct`, `orderBy` and `repartition`; and cache a result that several actions reuse instead of recomputing the whole lineage.

## Adaptive Query Execution

**AQE** re-optimizes a query plan at runtime using the statistics from completed shuffle stages. It has been on by default since Spark 3.2.0 (`spark.sql.adaptive.enabled`, confirmed true in my local run). Its three main features:

1. **Coalescing shuffle partitions** - merges small post-shuffle partitions toward an advisory size (64 MB by default), so you can set `spark.sql.shuffle.partitions` high and let AQE shrink it.
2. **Switching join strategy** - converts a sort-merge join to a broadcast hash join when one side turns out small at runtime.
3. **Optimizing skew joins** - splits skewed partitions in sort-merge joins; a partition counts as skewed when it exceeds both 5 times the median size and 256 MB by default.

**[📖 Performance tuning: AQE](https://spark.apache.org/docs/latest/sql-performance-tuning.html)** - AQE features, defaults and settings

You enable or inspect AQE with `spark.conf.set("spark.sql.adaptive.enabled", "true")` and read plans with `df.explain()`, where an `AdaptiveSparkPlan` node (visible in my broadcast-join run, `isFinalPlan=false` before execution) indicates AQE is active.

## Logs, the Spark UI and common failures

Where to look:

- **Driver logs** - application start-up, plan, errors thrown in your code, results of `collect`.
- **Executor logs** (`stderr`, `stdout`) - task failures, Python worker errors, out-of-memory kills, GC output. GC logging flags, if you add them, write on the worker nodes, not the driver.
- **Spark UI** - Jobs, Stages, Storage, Executors and SQL tabs. The stage page shows task-time distribution (skew) and spill; the Executors tab shows GC time and failed tasks.

Typical diagnoses:

| Symptom | Likely cause | Fix |
|---|---|---|
| Driver OutOfMemoryError | `collect()`, `toPandas()` or a big broadcast on the driver | Aggregate first, `limit`, write out instead |
| Executor OutOfMemoryError or container killed | Oversized or skewed partitions, too few partitions | More partitions, fix skew, raise executor memory |
| One task much slower than the rest | Data skew | AQE skew handling, salting, broadcast |
| Cluster mostly idle, few tasks | Too few partitions or a single large file | `repartition`, more input splits |
| High GC time | Many objects, memory pressure | Fewer objects, serialized caching, tune memory fraction |
| Job slow and shuffle read/write huge | Unnecessary wide operations | Filter earlier, broadcast, reduce shuffles |

**[📖 Tuning Spark](https://spark.apache.org/docs/latest/tuning.html)** - memory, GC, parallelism and task size guidance
**[📖 Cluster overview](https://spark.apache.org/docs/latest/cluster-overview.html)** - each driver serves a web UI, typically on port 4040

## How the exam tests this section

- **Pick the partition tool:** reduce files cheaply is coalesce; balance data is repartition.
- **Name the skew fix:** AQE, salting, broadcast; not "add executors".
- **What AQE does:** coalesce partitions, switch join strategy, split skewed partitions.
- **Match the log to the problem:** executor logs for OOM and GC; driver for collect-related OOM.

## Key terms

- **Data skew** - an uneven distribution of data across partitions, leaving a few tasks with most of the work.
- **repartition** - a method that reshuffles data into a specified number of balanced partitions.
- **coalesce** - a method that merges existing partitions to reduce their number without a full shuffle.
- **Adaptive Query Execution** - a runtime feature that re-optimizes the query plan using statistics from completed shuffle stages.
- **Salting** - adding a random value to a skewed key so its rows spread across more partitions.
- **Spill** - writing intermediate data to disk when it does not fit in memory during a shuffle or aggregation.
- **Executor logs** - the stdout and stderr records of each executor, used to diagnose task failures and memory errors.
- **Spark UI** - the web interface showing jobs, stages, tasks, storage and executors for a running application.
