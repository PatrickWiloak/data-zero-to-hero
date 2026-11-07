---
last-updated: 2026-11-07
applies-to: Databricks exam guide (published 2026-10-08), read 2026-11-07
---

# Databricks Associate Developer for Apache Spark - Study Strategy

Weights: DataFrame API 30%, Architecture 20%, Spark SQL 20%, Troubleshooting 10%, Streaming 10%, Spark Connect 5%, Pandas API 5%. This is a code-reading exam: hands-on time beats reading time.

## Study Approach

### Phase 1: Concepts and SQL (Weeks 1-2)
- Install PySpark locally and run a job. Open the Spark UI (port 4040 while the application runs) and find the job, stages and tasks for a `groupBy`.
- Write and read Parquet with each save mode. Partition by a column and list the directories.

### Phase 2: DataFrame API (Weeks 3-4)
- For each method in note 03, write a two-row example, predict the output, run it. Include a null, an empty array and a duplicate row.
- Build one end-to-end script: read, clean, join, aggregate, write.

### Phase 3: Tuning, streaming and the rest (Week 5)
- Print `df.explain()` for a join with and without `F.broadcast`. Compare partition counts with `repartition` and `coalesce`.
- Run a streaming query on the `rate` source with a watermark and a window.
- Write a pandas UDF and a grouped map.

### Phase 4: Readiness (Week 6)
- Practice bank, timed, then note endings for misses.

## Exam Tactics

1. **Execute the code in your head** on three rows, one of them null.
2. **Classify each call** as transformation or action; as narrow or wide.
3. **Eliminate by principle:** collecting to the driver, row-by-row UDFs and "add more executors" are rarely right.
4. **Check the output mode** against the query shape in streaming questions.
5. **Time:** 2 minutes per question; flag and return.

## Common Traps

- `union` matches by position, not name.
- Accumulators updated in a transformation stay at zero until an action, and may double count on retries.
- `explode` removes rows with empty arrays.
- `collect()` and `toPandas()` pull everything to the driver.
- `coalesce` cannot increase partitions usefully and may leave them uneven.
- Streaming dedup without a watermark keeps unbounded state.
- A watermark after the aggregation, or on a different column, does not bound state.
