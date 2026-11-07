---
last-updated: 2026-11-07
applies-to: Databricks exam guide (published 2026-10-08), read 2026-11-07
---

# Databricks Associate Developer for Apache Spark - Study Plan

## 6-Week Study Schedule

About 1 hour a day. Run everything locally with PySpark (`local[2]`) or in **[📖 Databricks Free Edition](https://www.databricks.com/learn/free-edition)**.

### Week 1: Architecture (Section 1)
- [ ] Read `notes/01-spark-architecture-and-components.md`
- [ ] Draw driver, cluster manager and executors from memory
- [ ] Run a groupBy and find its job, stages and tasks in the Spark UI
- [ ] Compare `getNumPartitions` before and after `repartition` and `coalesce`

### Week 2: Spark SQL (Section 2)
- [ ] Read `notes/02-using-spark-sql.md`
- [ ] Write Parquet with each save mode and confirm the row counts
- [ ] Write with `partitionBy` and list the directories
- [ ] Query a Parquet folder with SQL and a DataFrame through a temp view

### Weeks 3-4: DataFrame API (Section 3)
- [ ] Read `notes/03-dataframe-api-applications.md`
- [ ] Columns: withColumn, rename, drop, split, explode and explode_outer on a table with an empty array
- [ ] Aggregation and dates: summary, approx_count_distinct, from_unixtime to a year
- [ ] Joins: inner, left, cross, broadcast; union versus unionByName
- [ ] A Python UDF, a broadcast variable and an accumulator (check it before and after an action)

### Week 5: Tuning, streaming, Connect, pandas (Sections 4-7)
- [ ] Read notes 04 to 07
- [ ] Inspect AQE settings with `spark.conf.get` and read an `explain()` plan
- [ ] Run a rate-source streaming query with a watermark and window; list the checkpoint folder
- [ ] Write a pandas UDF and an `applyInPandas` function

### Week 6: Readiness
- [ ] Practice question bank, timed
- [ ] Reread the endings of the two weakest notes
- [ ] Check the exam guide for changes two weeks before the exam
