---
last-updated: 2026-10-22
applies-to: Databricks Data Engineer Professional exam guide of 2026-10-09
difficulty: advanced
reading-time: 25 min
---

# Section 6: Cost & Performance Optimisation

The exam page weights this at 13%; the 2026-10-09 exam guide PDF at 15%. It is the second-largest section. The objectives are about the modern, mostly automatic way to keep Delta tables fast and cheap: managed tables with Predictive Optimization, Liquid Clustering (including `CLUSTER BY AUTO`), deletion vectors, the disk cache, change data feed, and reading the query profile. Older techniques (partitioning, Z-ordering) appear as the comparison.

Source: the **[📖 exam page](https://www.databricks.com/learn/certification/data-engineer-professional)** and its linked exam guide.

## Table of Contents

1. [Managed tables and Predictive Optimization](#managed-tables-and-predictive-optimization)
2. [Liquid Clustering and CLUSTER BY AUTO](#liquid-clustering-and-cluster-by-auto)
3. [Liquid Clustering vs partitioning and Z-order](#liquid-clustering-vs-partitioning-and-z-order)
4. [Deletion vectors](#deletion-vectors)
5. [Choosing the optimisation for an access pattern](#choosing-the-optimisation-for-an-access-pattern)
6. [The disk cache](#the-disk-cache)
7. [Change data feed for incremental downstream work](#change-data-feed-for-incremental-downstream-work)
8. [Reading the query profile](#reading-the-query-profile)
9. [How the exam tests this section](#how-the-exam-tests-this-section)

---

## Managed tables and Predictive Optimization

A Delta table needs regular maintenance: `OPTIMIZE` to compact small files and cluster data, `VACUUM` to delete files no longer referenced, and `ANALYZE` to keep statistics fresh for the optimizer. On old platforms someone wrote a nightly job for each table, picked the schedule, and paid for it whether or not the table changed.

**Unity Catalog managed tables** hand this to Databricks. Unity Catalog owns the storage location and the lifecycle, so the platform can safely maintain the table. **Predictive Optimization** then runs `OPTIMIZE`, `VACUUM` and `ANALYZE` automatically, when it predicts the work will pay off (from the table's write and read patterns), on serverless compute billed to the account. It is enabled at the account, catalog or schema level and is on by default for new accounts.

What that removes: maintenance jobs, the decision of when to run them, and the risk of forgetting `VACUUM` (storage cost) or `OPTIMIZE` (slow queries). What it requires: managed tables (not external) in Unity Catalog.

Managed tables also get other automatic features: automatic liquid clustering, faster metadata reads and caching. "Reduce operational and maintenance overhead" in a question almost always means managed tables plus Predictive Optimization.

**[📖 Predictive optimization](https://docs.databricks.com/aws/en/optimizations/predictive-optimization)** - what it runs, enabling, monitoring via system tables
**[📖 Unity Catalog managed tables](https://docs.databricks.com/aws/en/tables/managed)** - benefits over external tables

---

## Liquid Clustering and CLUSTER BY AUTO

**Liquid Clustering** physically groups rows with similar values of the **clustering keys** into the same files, so a filter on those keys reads few files (data skipping). Unlike partitioning, it does not create a directory per value, and unlike Z-order, it is incremental: `OPTIMIZE` only reclusters new or unclustered data, and you can **change the keys** later without rewriting the table.

```sql
CREATE TABLE sales (order_id BIGINT, customer_id BIGINT, order_date DATE, amount DECIMAL(12,2))
CLUSTER BY (customer_id, order_date);

ALTER TABLE sales CLUSTER BY (order_date);      -- change keys; applies to new clustering
OPTIMIZE sales;                                 -- clusters incrementally
OPTIMIZE sales FULL;                            -- recluster everything, e.g. after a key change
```

Choose up to four keys, the columns most often used in filters (and join keys). High cardinality is fine; it is a problem for partitioning, not for clustering.

**`CLUSTER BY AUTO`** goes one step further: Databricks chooses and changes the keys for you, based on the table's actual query history, and only when the predicted benefit outweighs the cost of reclustering. It requires a Unity Catalog managed table with Predictive Optimization enabled.

```sql
CREATE TABLE events (...) CLUSTER BY AUTO;
ALTER TABLE events CLUSTER BY AUTO;
```

Pick explicit keys when you know the access pattern and it is stable. Pick `AUTO` when access patterns are unknown, varied, or change over time, or when the team does not want to own the decision.

**[📖 Use liquid clustering for tables](https://docs.databricks.com/aws/en/tables/clustering)** - keys, `OPTIMIZE`, automatic liquid clustering

---

## Liquid Clustering vs partitioning and Z-order

| | Partitioning | Z-order | Liquid Clustering |
|---|---|---|---|
| How | One directory per value of the partition column | Rewrites files sorted on a space-filling curve during `OPTIMIZE ... ZORDER BY` | Clusters files on keys, incrementally |
| Change the scheme later | Full rewrite | Change the `ZORDER BY` list, but each run rewrites partitions fully | `ALTER TABLE ... CLUSTER BY`, no rewrite needed |
| High-cardinality column | Disaster: millions of tiny files | Fine | Fine |
| Small tables (under about 1 TB) | Usually harmful: too many small files | Overhead with little gain | Fine |
| Concurrent writes | Partition-level isolation helps | - | Row-level concurrency with deletion vectors |
| Status | Still supported; use only when the reason is clear | Superseded | Recommended default for new tables |

When is partitioning still right? Very large tables (many terabytes) where a low-cardinality column (date) matches both the query filter and the data lifecycle, so you can delete or overwrite whole partitions, and each partition holds at least around 1 GB. Even then, many teams now cluster instead. Z-order cannot be combined with Liquid Clustering on the same table.

**[📖 When to partition tables](https://docs.databricks.com/aws/en/tables/partitions)** - the "do not partition small tables" guidance
**[📖 Data skipping](https://docs.databricks.com/aws/en/tables/data-skipping)** - file statistics, Z-order, how skipping works

---

## Deletion vectors

Without deletion vectors, deleting or updating a single row means rewriting the **whole Parquet file** that contains it. A `MERGE` touching 1,000 rows spread across 1,000 files rewrites 1,000 files. That is **write amplification**.

With **deletion vectors** enabled, Delta instead writes a small sidecar file that marks which rows in a data file are deleted. An update marks the old row deleted and writes the new version to a new file. Readers combine the data file with its deletion vector and skip the marked rows. The physical rewrite happens later, during `OPTIMIZE` (or `REORG TABLE ... APPLY (PURGE)`), batched and in the background.

Effects:

- **Much faster `DELETE`, `UPDATE` and `MERGE`** on large tables with scattered changes.
- **Row-level concurrency**: two writers touching different rows of the same file can both succeed.
- **Slightly more read work** until compaction, which Predictive Optimization handles.
- **Compliance catch**: a deleted row still physically exists in the old file until it is purged and vacuumed (see [Section 7](07-security-compliance.md#purging-data-for-retention-and-right-to-erasure)).

Deletion vectors are enabled by default for new tables on current runtimes; otherwise set `delta.enableDeletionVectors = true`.

**[📖 Deletion vectors](https://docs.databricks.com/aws/en/tables/features/deletion-vectors)** - how they work, enabling, purge

---

## Choosing the optimisation for an access pattern

| Access pattern in the question | Technique |
|---|---|
| Frequent small `MERGE`, `UPDATE` or `DELETE` scattered across a big table | Deletion vectors |
| Queries filter on a few known columns | Liquid Clustering on those columns |
| Query patterns unknown or changing; no one owns tuning | `CLUSTER BY AUTO` (managed table + Predictive Optimization) |
| Many small files from streaming writes | Optimized writes and auto compaction, or Predictive Optimization |
| Repeated reads of the same data by many queries | Disk cache (automatic on suitable compute) |
| Downstream consumers need only what changed | Change data feed |
| Stale statistics causing bad join choices | `ANALYZE TABLE ... COMPUTE STATISTICS` or Predictive Optimization |

---

## The disk cache

The **disk cache** (formerly called the Delta cache) keeps copies of remote Parquet and Delta data files on the **local SSDs** of the compute nodes, in a fast decompressed format. The second time a query reads the same file, it reads from local disk instead of cloud storage.

- It is automatic on SQL warehouses and serverless, and on classic node types with SSDs (it can be toggled with `spark.databricks.io.cache.enabled`).
- It is consistent: if the underlying file changes, Delta's file names change too, so stale data is never served.
- It helps **repeated reads of the same data**: dashboards, iterative analysis, several queries on the same hot tables. It does not help a one-off full scan.

It differs from the Spark cache (`df.cache()` / `CACHE TABLE`), which stores a computed result in memory, is not shared across clusters, and can go stale. On Databricks the disk cache is usually the better choice; `df.cache()` is for a DataFrame reused several times within one job.

**[📖 Optimize performance with caching](https://docs.databricks.com/aws/en/optimizations/disk-cache)** - disk cache vs Spark cache, configuration

---

## Change data feed for incremental downstream work

**Change data feed (CDF)** records row-level changes made to a Delta table: which rows were inserted, updated (with before and after images) or deleted, at which version. Downstream jobs read only the changes since their last run instead of re-reading and comparing the whole table. That is the cost and performance angle.

```sql
ALTER TABLE silver.customers SET TBLPROPERTIES (delta.enableChangeDataFeed = true);

-- Batch: changes between versions (or timestamps)
SELECT * FROM table_changes('silver.customers', 120, 125);
```

```python
# Streaming: process each change once, checkpointed
changes = (spark.readStream
           .option("readChangeFeed", "true")
           .table("silver.customers"))
```

Each row carries `_change_type` (`insert`, `update_preimage`, `update_postimage`, `delete`), `_commit_version` and `_commit_timestamp`. CDF only records changes made **after** it is enabled, and its files are removed by `VACUUM` along with other old files, so a consumer that falls behind the retention period must do a full refresh.

Typical uses: propagate silver changes to gold with `MERGE`, feed an external system, or audit what changed. Do not confuse it with AUTO CDC: CDF **produces** a change feed from a Delta table; AUTO CDC **consumes** a change feed.

**[📖 Use Delta Lake change data feed](https://docs.databricks.com/aws/en/tables/features/change-data-feed)** - enabling, reading, retention
**[📖 table_changes function](https://docs.databricks.com/aws/en/sql/language-manual/functions/table_changes)** - SQL batch reads of the feed

---

## Reading the query profile

The **query profile** is a visual plan of an executed query (from the SQL editor, query history, or a notebook cell on serverless or SQL warehouse compute), with metrics per operator: rows, time, memory, bytes read, spill. Learn to read four things:

1. **Data skipping.** On the scan node, compare **files read** and **files pruned** (and bytes read vs table size). Reading most of the table for a selective filter means the filter column is not a clustering key, or the predicate cannot be pushed down (a function wrapped around the column, a type cast). Fix: cluster on the column, rewrite the predicate.
2. **Join strategy.** Each join node says broadcast hash, shuffle hash or sort-merge. A sort-merge join where one side is small means statistics are missing or the size estimate is wrong; `ANALYZE` the table or add a `BROADCAST` hint.
3. **Shuffle (exchange).** Exchange nodes show bytes shuffled. Huge shuffles point to a join or aggregation on wide data that could be filtered or projected earlier.
4. **Skew and spill.** If one task takes far longer than the others, or the operator shows **spill to disk**, the data is skewed or memory is too small. AQE handles some skew automatically; persistent skew needs a better key or salting.

The **top operators** panel ranks where time went, so start there. For classic compute without the query profile, the Spark UI's SQL / DataFrame tab gives the same plan with metrics ([Section 9](09-debugging-deploying.md#finding-the-diagnostic-information)).

**[📖 Query profile](https://docs.databricks.com/aws/en/sql/user/queries/query-profile)** - operators, metrics, top operators

---

## How the exam tests this section

- **"Reduce maintenance overhead"**: managed tables + Predictive Optimization (+ Liquid Clustering or `CLUSTER BY AUTO`).
- **"Frequent small updates and deletes"**: deletion vectors. Partitioning by primary key and `VACUUM` with zero retention are the traps.
- **"Changing or unknown query patterns"**: `CLUSTER BY AUTO`. **"Known filter columns"**: explicit Liquid Clustering keys.
- **Partitioning a table under about 1 TB, or on a high-cardinality column**: wrong.
- **"Only process what changed downstream"**: change data feed.
- **Query profile questions** give a symptom (many files read, sort-merge join with a tiny side, large spill) and ask for the cause.

Continue with [Section 7: Ensuring Data Security and Compliance](07-security-compliance.md).
