---
last-updated: 2026-11-07
applies-to: Databricks Data Analyst Associate exam guide (August 2026 edition, read 2026-11-07)
difficulty: intermediate
reading-time: 25 min
---

# Section 5: Analyzing Queries

Weighted **15%**, about seven questions. Six objectives: Photon; finding poorly performing queries (Query Insights, query profile); using Delta Lake to audit history and compare results; query history and caching; Predictive Optimization and automatic Liquid Clustering; and fixing a query to get the desired result.

## Table of Contents

1. [Photon](#photon)
2. [Finding slow queries](#finding-slow-queries)
3. [Auditing with Delta history](#auditing-with-delta-history)
4. [Caching](#caching)
5. [Predictive Optimization and Liquid Clustering](#predictive-optimization-and-liquid-clustering)
6. [Fixing a query](#fixing-a-query)
7. [How the exam tests this section](#how-the-exam-tests-this-section)
8. [Key terms](#key-terms)

---

## Photon

**Photon** is Databricks' native vectorized query engine, written in C++ and processing data in columnar batches. Spark's optimizer still plans the query, and existing SQL and DataFrame code runs unchanged. It speeds up SQL, DataFrame, ETL and stateless streaming work, helps most on long-running queries over large data, and does little for queries that finish in under two seconds. Photon is the default engine for all SQL warehouses, and Databricks says it is enabled on serverless compute and serverless Lakeflow pipelines. Limits: UDFs, RDD and Dataset APIs and stateful streaming are not supported, and an unsupported operation simply falls back to regular Spark for that portion, with correct results.

**[📖 Photon](https://docs.databricks.com/aws/en/compute/photon)** - what it is, supported workloads and limitations

## Finding slow queries

Start at **Query History**. You can filter by user, date range, compute, duration, status, statement type and query tags, and open a query to see its status (Queued, Running, Finished, Failed, Cancelled), the full statement text, a breakdown of wall-clock time into scheduling, optimization, file pruning and execution, aggregated task time, and a list of the longest operators. From there, **See query profile** opens the detailed execution plan, where you look for the operator that dominates time or reads far more data than it returns. Query history is visible to the query owner and to users with at least CAN VIEW on the warehouse; `system.query.history` offers account-wide records, readable by admins by default.

**[📖 Query history](https://docs.databricks.com/aws/en/sql/user/queries/query-history)** - filters, details, query profile and the system table

Habits that make queries slow, and their cures: reading more columns or rows than needed (select fewer columns, filter early), joining huge tables before filtering, missing data skipping because the table layout does not match the filter columns (clustering, below), and repeating the same heavy aggregation (use a materialized view).

## Auditing with Delta history

Because every write is a version, `DESCRIBE HISTORY` is an audit log: operation, user, timestamp and often metrics. Use `VERSION AS OF` to re-run a query on yesterday's data and compare it with today's, which tells you whether a number changed because the data changed. Retention limits from the previous section apply (history 30 days; files for time travel 7 days by default).

```sql
-- Syntax per docs; not run.
SELECT 'then' AS snapshot, COUNT(*) FROM main.silver.orders VERSION AS OF 120
UNION ALL
SELECT 'now',  COUNT(*) FROM main.silver.orders;
```

## Caching

There are three layers. The **UI cache** is per user and keeps the latest results for up to 7 days, cleared when the tables change. The **result cache** stores results of SQL warehouse queries for 24 hours; a local in-memory part clears when the cluster stops, and serverless warehouses add a remote part shared across the workspace's warehouses and surviving restarts. The **disk cache** keeps copies of data files on the nodes' local SSDs. Caches are invalidated when the underlying data changes, so you never read stale results from them. A query that returns instantly the second time is probably the result cache, which is why benchmark runs set `use_cached_result = false`.

**[📖 Query caching](https://docs.databricks.com/aws/en/sql/user/queries/query-caching)** - UI cache, result cache and disk cache

## Predictive Optimization and Liquid Clustering

**Predictive Optimization** runs table maintenance for you on Unity Catalog **managed** tables: `OPTIMIZE` (file layout and incremental clustering), `VACUUM` (delete unreferenced files), `ANALYZE` (statistics) and index refresh. External tables are not covered. It is on by default and can be controlled at account, catalog, schema or table level.

**Liquid Clustering** replaces partitioning and Z-order. `CLUSTER BY (col)` tells Delta how to lay data out so queries filtering on those columns skip files. Unlike partitions, you can change the keys later without rewriting existing data. **`CLUSTER BY AUTO`** lets Databricks choose keys from the table's query history; it requires predictive optimization and Unity Catalog managed tables, and keys change only when predicted savings exceed the cost. The decision rule: analyze which columns appear in filters and joins (the query history and profile show this), and cluster on those; if patterns shift, use AUTO.

```sql
-- Syntax per docs; not run.
ALTER TABLE main.silver.orders CLUSTER BY (customer_id, order_date);
ALTER TABLE main.silver.orders CLUSTER BY AUTO;
```

**[📖 Predictive optimization](https://docs.databricks.com/aws/en/optimizations/predictive-optimization)** - operations, supported tables, automatic liquid clustering
**[📖 Liquid clustering](https://docs.databricks.com/aws/en/delta/clustering)** - CLUSTER BY, AUTO and comparison with partitioning

## Fixing a query

"Fix a query to achieve the desired result" is a reading skill. Compare the stated goal with the SQL and look for the usual defects: an aggregate without `GROUP BY`; a `WHERE` on an aggregate (should be `HAVING`); an inner join that drops rows you wanted (should be left); a missing join condition that multiplies rows; `= NULL` instead of `IS NULL`; `UNION` removing rows you meant to keep (use `UNION ALL`).

## How the exam tests this section

- **Where to look:** Query History for the list, query profile for the plan.
- **Caching:** repeated identical queries return fast from the result cache; data changes invalidate it.
- **Maintenance:** managed tables get Predictive Optimization; external tables do not.
- **Clustering:** frequently filtered columns become cluster keys; AUTO when patterns change.
- **Audit/compare:** Delta history and time travel.

## Key terms

- **Photon** - Databricks' native vectorized C++ query engine that accelerates SQL and DataFrame workloads.
- **Query history** - the list of previous query runs with filters, status, timing details and a link to the query profile.
- **Query profile** - the detailed execution plan of a query, used to find slow operators.
- **Result cache** - a 24-hour cache of SQL warehouse query results, invalidated when the underlying tables change.
- **Disk cache** - copies of data files stored on the local SSDs of compute nodes to speed repeat reads.
- **Predictive optimization** - automatic OPTIMIZE, VACUUM and ANALYZE for Unity Catalog managed tables.
- **Liquid clustering** - a flexible data layout, set with CLUSTER BY, that replaces partitioning and Z-order.
- **CLUSTER BY AUTO** - a setting where Databricks selects and adapts clustering keys from query history.
- **OPTIMIZE** - a command that compacts small files and applies liquid clustering.
