---
last-updated: 2026-10-22
applies-to: Databricks exam page and exam guide of 2026-05-04
---

# Databricks Data Engineer Associate - Fact Sheet

## Exam Overview

**Exam Name:** Databricks Certified Data Engineer Associate
**Duration:** 90 minutes
**Questions:** 45 scored multiple-choice questions, plus possible unscored items
**Passing Score:** Not published by Databricks
**Cost:** $200 USD, plus applicable taxes
**Delivery:** Online proctored or test center
**Languages:** English, Japanese, Portuguese (BR), Korean
**Valid For:** 2 years

Current version effective **2026-05-04**, replacing the five-domain version (Lakehouse Platform 24%, ELT with Spark SQL and Python 29%, Incremental Data Processing 17%, Production Pipelines 13%, Data Governance 17%). Facts read from the exam page and the exam guide PDF on 2026-10-22.

**[📖 Official Exam Page](https://www.databricks.com/learn/certification/data-engineer-associate)** - registration, format, section weights
**[📖 Exam Guide PDF (May 2026)](https://www.databricks.com/sites/default/files/2026-05/databricks-certified-data-engineer-associate-exam-guide-may-2026-000.pdf)** - every objective, recommended training, sample questions
**[📖 Databricks Free Edition](https://docs.databricks.com/aws/en/getting-started/free-edition)** - free practice workspace (replaced Community Edition)
**[📖 Databricks Documentation](https://docs.databricks.com/aws/en/)** - the authoritative reference

## Sections and Weights

| # | Section | Weight |
|---|---|---|
| 1 | Databricks Intelligence Platform | 6% |
| 2 | Data Ingestion and Loading | 21% |
| 3 | Data Transformation and Modeling | 22% |
| 4 | Working with Lakeflow Jobs | 16% |
| 5 | Implementing CI/CD | 10% |
| 6 | Troubleshooting, Monitoring, and Optimization | 10% |
| 7 | Governance and Security | 15% |

## Target Audience

- Data engineers with about six months of hands-on Databricks work
- SQL and Python developers moving into data engineering on Databricks
- Engineers who build ingestion and transformation pipelines and need to orchestrate, deploy and govern them

## Section 1: Databricks Intelligence Platform (6%)

**[📖 Architecture overview](https://docs.databricks.com/aws/en/getting-started/overview)** - control plane and compute plane
**[📖 Delta Lake](https://docs.databricks.com/aws/en/delta/)** - transaction log, time travel
**[📖 Unity Catalog](https://docs.databricks.com/aws/en/data-governance/unity-catalog/)** - governance for data and AI
**[📖 Compute](https://docs.databricks.com/aws/en/compute/)** - every compute type

**Key Facts:**
- Control plane (Databricks-run: UI, scheduler, Unity Catalog) vs compute plane (serverless in Databricks' account, classic in yours); data in your object storage
- Delta Lake = Parquet files + `_delta_log`; ACID, schema enforcement, time travel (`VERSION AS OF`, `RESTORE`), `DESCRIBE HISTORY`
- `VACUUM` removes unneeded files older than retention (default 7 days); versions needing them can no longer be read
- Unity Catalog: one metastore per region, `catalog.schema.object` names, inherited grants, audit, automatic lineage
- SQL warehouse for SQL and BI; serverless jobs compute for most scheduled jobs; jobs cluster when you need custom setup; all-purpose for interactive work
- Serverless: fast start, no init scripts, Scala, R or GPUs; DBUs include the machines
- Classic: DBUs plus your cloud VM bill; all-purpose has a higher DBU rate than jobs compute
- Auto-termination, autoscaling, spot workers, pools and Photon are the classic cost and speed levers
- Access modes: standard (shared, multi-user) and dedicated (single user or group)

## Section 2: Data Ingestion and Loading (21%)

**[📖 Ingestion overview](https://docs.databricks.com/aws/en/ingestion/)** - standard and managed connectors
**[📖 COPY INTO](https://docs.databricks.com/aws/en/ingestion/cloud-object-storage/copy-into/)** - idempotent SQL loads
**[📖 Auto Loader](https://docs.databricks.com/aws/en/ingestion/cloud-object-storage/auto-loader/)** - incremental file ingestion
**[📖 Schema inference and evolution](https://docs.databricks.com/aws/en/ingestion/cloud-object-storage/auto-loader/schema)** - modes and rescued data
**[📖 Lakeflow Connect managed connectors](https://docs.databricks.com/aws/en/ingestion/lakeflow-connect/)** - SaaS and database sources
**[📖 JDBC](https://docs.databricks.com/aws/en/connect/external-systems/jdbc)** - reading databases from notebooks

**Key Facts:**
- Incremental = process only what's new; Auto Loader with `trigger(availableNow=True)` is incremental batch
- Upload UI for one-off small files; volumes (`/Volumes/c/s/v/...`) for raw files
- `COPY INTO` is idempotent (skips loaded files), SQL-only, best up to thousands of files; `force = true` reloads
- Auto Loader: `format("cloudFiles")`, `cloudFiles.format`, `cloudFiles.schemaLocation`; exactly-once via checkpoint; millions of files
- Discovery: directory listing (default), file notification (cloud queues), managed file events on an external location
- `schemaEvolutionMode`: `addNewColumns` (default; stops once, restart adds column), `rescue`, `failOnNewColumns`, `none`
- `_rescued_data` keeps unmatched columns, wrong types and case mismatches instead of dropping them
- Lakeflow Connect managed connectors: Unity Catalog connection, streaming tables, serverless pipeline, SCD Type 1 or 2, CDC for SQL Server, MySQL, PostgreSQL
- Standard connectors: Auto Loader, `COPY INTO`, Kafka, Pub/Sub, Pulsar, SFTP
- JDBC: credentials from secrets; `partitionColumn` + bounds + `numPartitions` for parallel reads; misses source deletes
- Nested data: structs and arrays, `explode`, `from_json`, `:` path extraction, `VARIANT` for changing shapes

## Section 3: Data Transformation and Modeling (22%)

**[📖 Medallion architecture](https://docs.databricks.com/aws/en/lakehouse/medallion)** - bronze, silver, gold
**[📖 MERGE INTO](https://docs.databricks.com/aws/en/sql/language-manual/delta-merge-into)** - upserts
**[📖 Adaptive query execution](https://docs.databricks.com/aws/en/optimizations/aqe)** - automatic tuning
**[📖 Materialized views](https://docs.databricks.com/aws/en/views/materialized)** - refreshable stored results
**[📖 Pipeline expectations](https://docs.databricks.com/aws/en/ldp/expectations)** - warn, drop, fail

**Key Facts:**
- Bronze raw and replayable; silver cleaned, typed, deduplicated; gold business-ready
- `withColumn`, `withColumnRenamed`, `drop`, `split`, `explode` (drops empty arrays) vs `explode_outer`
- `fillna` / `coalesce` replace nulls; `dropna` / `IS NOT NULL` drop rows; `try_cast` returns null on bad values
- PySpark `union` = `unionAll`: keeps duplicates, matches by position; `unionByName` matches by name; SQL `UNION` removes duplicates, `UNION ALL` keeps them
- Broadcast the small side (`F.broadcast`, `/*+ BROADCAST */`); auto below `autoBroadcastJoinThreshold` (10 MB default)
- Latest row per key: `ROW_NUMBER() OVER (PARTITION BY key ORDER BY ts DESC) = 1` with `QUALIFY`
- `approx_count_distinct` for cheap approximate distinct counts; `describe()` and `summary()` for profiles
- `spark.sql.shuffle.partitions` default 200; memory configs are cluster-level; serverless doesn't allow most Spark configs
- `CREATE OR REPLACE TABLE` keeps history and grants; `MERGE` needs one source row per key
- View: no storage, always current. Materialized view: stored, refreshed (`SCHEDULE EVERY`), for expensive gold. Streaming table: append-only incremental
- Expectations: `expect` (keep), `expect_or_drop`, `expect_or_fail`; `CHECK` and `NOT NULL` constraints fail the whole write

## Section 4: Working with Lakeflow Jobs (16%)

**[📖 Lakeflow Jobs](https://docs.databricks.com/aws/en/jobs/)** - tasks, triggers, notifications
**[📖 Triggers](https://docs.databricks.com/aws/en/jobs/triggers)** - scheduled, file arrival, table update, continuous
**[📖 If/else task](https://docs.databricks.com/aws/en/jobs/if-else)** - branching
**[📖 For each task](https://docs.databricks.com/aws/en/jobs/for-each)** - looping
**[📖 Run if dependencies](https://docs.databricks.com/aws/en/jobs/run-if)** - conditional execution

**Key Facts:**
- Task types: notebook, Python script or wheel, SQL (query, file, alert), dashboard, pipeline, dbt, JAR, Run Job, If/else, For each
- Tasks without dependencies run in parallel; default Run if is All succeeded; All done runs cleanup regardless
- Task values: `dbutils.jobs.taskValues.set/get`; reference as `{{tasks.<task>.values.<key>}}`
- Retries per task (count, interval) for transient failures; timeouts stop hung tasks
- If/else compares two operands (`==`, `!=`, `>`, `>=`, `<`, `<=`); the other branch is skipped
- For each runs a nested task per input item with a concurrency limit; `{{input}}` is the current item
- Triggers: scheduled (interval or cron), file arrival (UC external location or volume), table update (up to 10 tables, any or all), continuous, manual
- Wait after last change = debounce; minimum time between triggers = cooldown
- Data-driven triggers for irregular arrivals and upstream dependencies; schedules for fixed deadlines
- Repair run reruns only failed and skipped tasks

## Section 5: Implementing CI/CD (10%)

**[📖 Git folders](https://docs.databricks.com/aws/en/repos/)** - Git integration in the workspace
**[📖 Declarative Automation Bundles](https://docs.databricks.com/aws/en/dev-tools/bundles/)** - resources as code
**[📖 Bundle variables](https://docs.databricks.com/aws/en/dev-tools/bundles/variables)** - substitutions and precedence
**[📖 Databricks CLI](https://docs.databricks.com/aws/en/dev-tools/cli/)** - install and commands

**Key Facts:**
- Git folders: create and switch branches, pull, commit and push, merge, rebase, resolve conflicts in the Git dialog; pull requests are opened and merged in the Git provider
- Bundles were renamed from Databricks Asset Bundles in March 2026; `databricks.yml` and `databricks bundle` commands are unchanged
- A bundle holds code plus YAML for jobs, pipelines, dashboards, schemas, volumes and more
- `variables:` with `default` or `lookup`; reference `${var.name}`; per-target values under `targets.<t>.variables`
- Variable precedence: `--var`, `BUNDLE_VAR_<name>`, `variable-overrides.json`, target value, default
- `mode: development` prefixes names and pauses triggers; `mode: production` expects a service principal run-as
- `bundle validate`, `bundle deploy -t <target>`, `bundle run`, `bundle destroy`, `bundle summary`
- CI authenticates as a service principal (OAuth M2M or workload identity federation) and promotes the same commit through targets

## Section 6: Troubleshooting, Monitoring, and Optimization (10%)

**[📖 Monitoring Lakeflow Jobs](https://docs.databricks.com/aws/en/jobs/monitor)** - matrix view and run details
**[📖 Skew and spill in the Spark UI](https://docs.databricks.com/aws/en/optimizations/spark-ui-guide/long-spark-stage-page)** - reading stage metrics
**[📖 Liquid clustering](https://docs.databricks.com/aws/en/delta/clustering)** - keys and `CLUSTER BY AUTO`
**[📖 Predictive optimization](https://docs.databricks.com/aws/en/optimizations/predictive-optimization)** - automatic maintenance

**Key Facts:**
- Runs tab matrix: one column per run, one row per task; Upstream failed and Skipped are not root causes
- Compare durations with a baseline; find which task slowed, then what changed
- Spark UI stage summary: Max far above Median = skew; non-zero spill = spill; large shuffle read/write = heavy shuffle
- Skew fix order: AQE skew join handling, handle hot or null keys, broadcast, salting last; more executors don't fix skew
- Liquid Clustering: `CLUSTER BY (cols)`, change keys without a rewrite, incremental `OPTIMIZE`; replaces partitioning and Z-order
- Predictive optimization runs `OPTIMIZE`, `VACUUM`, `ANALYZE` automatically on Unity Catalog managed tables only
- Cluster won't start: event log (capacity, quota, init script, network)
- Library conflict: Libraries tab and driver logs; pin versions; `%pip` or job environments
- Driver OOM = too much data pulled to the driver; executor OOM = skew or oversized partitions

## Section 7: Governance and Security (15%)

**[📖 Managed tables](https://docs.databricks.com/aws/en/tables/managed)** - storage and benefits
**[📖 Convert external to managed](https://docs.databricks.com/aws/en/tables/convert-external-managed)** - `SET MANAGED`
**[📖 Privileges reference](https://docs.databricks.com/aws/en/data-governance/unity-catalog/access-control/privileges-reference)** - every privilege
**[📖 Row filters and column masks](https://docs.databricks.com/aws/en/data-governance/unity-catalog/filters-and-masks)** - fine-grained access
**[📖 ABAC](https://docs.databricks.com/aws/en/data-governance/unity-catalog/abac/)** - tag-based policies

**Key Facts:**
- Managed: Unity Catalog stores files; `DROP` deletes data after the `UNDROP` window (7 days default); predictive optimization applies
- External: `LOCATION` in an external location; `DROP` removes metadata only
- `ALTER TABLE ... SET MANAGED` converts external to managed (rollback with `UNSET MANAGED` within 14 days)
- Read one table: `USE CATALOG` + `USE SCHEMA` + `SELECT`; write adds `MODIFY`
- Grants inherit downward, including to future objects; `REVOKE` on a child can't cancel a parent grant
- Grant to groups and service principals; principals with special characters in backticks
- `DENY` statement is legacy Hive metastore only; Unity Catalog uses ABAC DENY policies (Beta, `MANAGE ACCESS CONTROL` only)
- Row filter: boolean SQL UDF via `SET ROW FILTER ... ON (col)`; column mask: UDF via `ALTER COLUMN ... SET MASK`
- `is_account_group_member()` decides who sees what; dynamic views are the older pattern
- ABAC: governed tags + `CREATE POLICY` at catalog or schema; covers future tables

## Exam Tips

1. **Ingestion (21%) and transformation (22%)** are almost half the exam.
2. **New names**: Lakeflow Jobs, Lakeflow Spark Declarative Pipelines, Declarative Automation Bundles, Git folders, Free Edition.
3. **Managed and automatic win**: managed connectors, managed tables, serverless, predictive optimization, ABAC.
4. **Triggers follow the data**: file arrival, table update, or a schedule for a fixed deadline.
5. **Stage metrics**: Max vs Median for skew; spill columns for spill.
6. **Time management**: 2 minutes per question.

## Quick Reference

```sql
-- Ingestion
COPY INTO main.raw.orders FROM '/Volumes/main/raw/landing/orders/' FILEFORMAT = JSON
  COPY_OPTIONS ('mergeSchema' = 'true');
CREATE OR REFRESH STREAMING TABLE main.raw.events AS
  SELECT * FROM STREAM read_files('/Volumes/main/raw/landing/events/', format => 'json');

-- Gold
CREATE OR REPLACE MATERIALIZED VIEW main.gold.daily AS
  SELECT date(ts) AS day, sum(amount) AS revenue FROM main.silver.orders GROUP BY ALL;

-- Delta and layout
DESCRIBE HISTORY main.silver.orders;
RESTORE TABLE main.silver.orders TO VERSION AS OF 12;
ALTER TABLE main.silver.orders CLUSTER BY (customer_id);

-- Governance
ALTER TABLE main.sales.orders_ext SET MANAGED;
GRANT USE CATALOG ON CATALOG main TO `analysts`;
GRANT USE SCHEMA, SELECT ON SCHEMA main.gold TO `analysts`;
ALTER TABLE main.hr.people ALTER COLUMN ssn SET MASK main.sec.mask_ssn;
```

```bash
databricks bundle validate -t test
databricks bundle deploy -t prod
databricks bundle run -t prod orders_job
```
