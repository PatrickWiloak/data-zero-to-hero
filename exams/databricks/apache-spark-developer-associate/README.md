---
last-updated: 2026-11-07
applies-to: Databricks exam page and exam guide (published 2026-10-08), read 2026-11-07
---

# Databricks Certified Associate Developer for Apache Spark

## Exam Overview

The Databricks Certified Associate Developer for Apache Spark exam tests whether you understand Spark's architecture and can use the DataFrame API in Python to do basic data manipulation: select, rename, filter, aggregate, join, handle missing data, read and write with schemas, use Spark SQL, write UDFs, tune and troubleshoot applications, run Structured Streaming queries, connect with Spark Connect and use the pandas API on Spark.

**Exam Details** (from the **[📖 exam page](https://www.databricks.com/learn/certification/apache-spark-developer-associate)** and **[📖 exam guide](https://www.databricks.com/sites/default/files/2026-10/databricks-certified-associate-developer-apache-spark-exam-guide-august-2026.pdf)**, read 2026-11-07):
- **Exam Name:** Databricks Certified Associate Developer for Apache Spark (no separate code)
- **Duration:** 90 minutes
- **Number of Questions:** 45 scored multiple-choice questions, plus up to 10 unscored
- **Passing Score:** Not published
- **Cost:** $200 USD plus taxes
- **Delivery:** Online proctored or test center; no test aids
- **Languages:** English; code is Python
- **Validity:** 2 years; retakes after 14 days at full price
- **Prerequisites:** None; 6+ months of hands-on Spark recommended

## Exam Sections

| # | Section | Weight | About | Notes |
|---|---|---|---|---|
| 1 | Apache Spark Architecture and Components | 20% | 9 | [01](notes/01-spark-architecture-and-components.md) |
| 2 | Using Spark SQL | 20% | 9 | [02](notes/02-using-spark-sql.md) |
| 3 | Developing Apache Spark DataFrame/Dataset API Applications | 30% | 13-14 | [03](notes/03-dataframe-api-applications.md) |
| 4 | Troubleshooting and Tuning Apache Spark DataFrame API Applications | 10% | 4-5 | [04](notes/04-troubleshooting-and-tuning.md) |
| 5 | Structured Streaming | 10% | 4-5 | [05](notes/05-structured-streaming.md) |
| 6 | Using Spark Connect to Deploy Applications | 5% | 2 | [06](notes/06-spark-connect-and-deployment.md) |
| 7 | Using Pandas API on Apache Spark | 5% | 2 | [07](notes/07-pandas-api-on-spark.md) |

Question counts apply the weights to 45 scored items; Databricks does not publish per-section counts.

### What each section covers

1. **Architecture** - advantages and challenges of Spark; driver, executors, cluster manager; DataFrames, SparkSession, caching and GC; the job, stage and task hierarchy; partitions and shuffles; lazy evaluation; the Spark modules.
2. **Spark SQL** - reading and writing files and JDBC, save modes, partitioning, SQL on files, persistent tables, temporary views.
3. **DataFrame API** - columns and rows, deduplication, aggregation, dates, joins and unions, schemas, UDFs, broadcast variables and accumulators.
4. **Troubleshooting and tuning** - repartition and coalesce, skew, shuffles, Adaptive Query Execution, driver and executor logs.
5. **Structured Streaming** - the model, output modes and sinks, windows, watermarks, streaming deduplication.
6. **Spark Connect** - the client-server architecture; client, cluster and local modes.
7. **Pandas API on Spark** - advantages; pandas UDFs.

## Study Approach

### Phase 1: Concepts and SQL (Weeks 1-2)
1. Section 1: draw driver, executors and the job-stage-task chain. Run an action and read the Spark UI.
2. Section 2: write, read, partition and overwrite Parquet locally; try each save mode.

### Phase 2: The DataFrame API (Weeks 3-4)
1. Section 3 is 30%. Write every example yourself; change one thing and predict the output before running it.

### Phase 3: Tuning, streaming and the rest (Week 5)
1. Sections 4-7: read the AQE settings table; run a rate-source streaming query; write a pandas UDF.

### Phase 4: Readiness (Week 6)
1. Practice questions, then the "How the exam tests this section" endings of the notes you miss.

## Local setup

You can practise on your laptop: `pip install pyspark`, Java installed, and `SparkSession.builder.master("local[2]").getOrCreate()`. Notes: set `PYSPARK_PYTHON` to your interpreter if the worker and driver Python versions differ, and keep pandas below 3 with PySpark 4.3.0. Databricks Free Edition is the other free option.

## Study Resources

- **[📖 Exam page](https://www.databricks.com/learn/certification/apache-spark-developer-associate)** - format, fee, registration
- **[📖 Exam guide PDF](https://www.databricks.com/sites/default/files/2026-10/databricks-certified-associate-developer-apache-spark-exam-guide-august-2026.pdf)** - objectives and sample questions
- **[📖 Apache Spark documentation](https://spark.apache.org/docs/latest/)** - the reference for every section
- Courses named in the guide (plain text, names change): Apache Spark Programming with Databricks (instructor-led); Introduction to Apache Spark, Developing Applications with Apache Spark, Stream Processing and Analysis with Apache Spark, Monitoring and Optimizing Apache Spark Workloads on Databricks (self-paced, Databricks Academy)

## Tips for Success

1. **Predict before you run.** Most questions show code and ask what happens.
2. **Know the traps:** union by position, accumulators in transformations, explode on empty arrays, collect on big data.
3. **Know the numbers:** 200 shuffle partitions, 10 MB broadcast, 128 MB input partition, 4040 UI port.
4. **Skew is fixed by partitioning and joins, not by more hardware.**

## File Index

| File | Description |
|------|-------------|
| [fact-sheet.md](fact-sheet.md) | Exam facts and key facts per section, with documentation links |
| [strategy.md](strategy.md) | Study phases and exam tactics |
| [practice-plan.md](practice-plan.md) | Six-week schedule with checkboxes |
| [scenarios.md](scenarios.md) | Exam-style scenarios with solutions |
| [notes/01-spark-architecture-and-components.md](notes/01-spark-architecture-and-components.md) | Driver, executors, hierarchy, lazy evaluation, partitions, caching |
| [notes/02-using-spark-sql.md](notes/02-using-spark-sql.md) | Sources, save modes, partitioning, views, tables |
| [notes/03-dataframe-api-applications.md](notes/03-dataframe-api-applications.md) | Columns, joins, unions, dates, UDFs, shared variables |
| [notes/04-troubleshooting-and-tuning.md](notes/04-troubleshooting-and-tuning.md) | Partitioning, skew, AQE, logs |
| [notes/05-structured-streaming.md](notes/05-structured-streaming.md) | Model, modes, windows, watermarks, dedup |
| [notes/06-spark-connect-and-deployment.md](notes/06-spark-connect-and-deployment.md) | Spark Connect and deployment modes |
| [notes/07-pandas-api-on-spark.md](notes/07-pandas-api-on-spark.md) | pandas API on Spark and pandas UDFs |
| [Practice questions](../../../resources/practice-questions/databricks-apache-spark-developer-associate.md) | 24 questions tagged by section |
