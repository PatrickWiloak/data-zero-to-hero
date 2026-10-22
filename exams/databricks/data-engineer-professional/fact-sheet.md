---
last-updated: 2026-10-22
applies-to: Databricks exam page and exam guide of 2026-10-09
---

# Databricks Data Engineer Professional - Fact Sheet

## Exam Overview

**Exam Name:** Databricks Certified Data Engineer Professional
**Duration:** 120 minutes
**Questions:** 59 scored multiple-choice questions (exam page; the guide PDF says 60), plus possible unscored items
**Passing Score:** Not published by Databricks
**Cost:** $200 USD, plus applicable taxes
**Delivery:** Online proctored or test center
**Languages:** English, Japanese, Portuguese (BR), Korean
**Valid For:** 2 years

Current version published **2026-10-09**, replacing the five-domain version (and its $300 fee) this guide described before 2026-10-22. Facts read from the exam page and guide on 2026-10-22.

**[📖 Official Exam Page](https://www.databricks.com/learn/certification/data-engineer-professional)** - registration, format, section weights
**[📖 Exam Guide PDF (9 Oct 2026)](https://www.databricks.com/sites/default/files/2026-10/Databricks-Certified-Data-Engineer-Professional-Exam-Guide-9-Oct-2026.pdf)** - every objective, recommended training, sample questions
**[📖 Databricks Documentation](https://docs.databricks.com/aws/en/)** - the authoritative reference

## Sections and Weights

| # | Section | Exam page | Guide PDF |
|---|---|---|---|
| 1 | Developing Code for Data Processing using Python and SQL | 22% | 23% |
| 2 | Data Ingestion & Acquisition | 7% | 12% (includes 4) |
| 3 | Data Transformation, Cleansing, and Quality | 10% | 12% (as "Data Manipulation") |
| 4 | Data Sharing and Federation | 5% | - |
| 5 | Monitoring and Alerting | 10% | 10% |
| 6 | Cost & Performance Optimisation | 13% | 15% |
| 7 | Ensuring Data Security and Compliance | 10% | 8% |
| 8 | Data Governance | 7% | 5% |
| 9 | Debugging and Deploying | 10% | 10% |
| 10 | Data Modelling | 6% | 5% |

The page and the PDF were published together but disagree on the section count, weights and scored-item count. The objectives match. Plan with the page's weights and recheck both two weeks before your exam.

## Target Audience

- Data engineers with about a year or more of hands-on Databricks work across the outline
- Engineers who own production pipelines: ingestion, CDC, orchestration, deployment and on-call
- Platform-minded engineers responsible for cost, performance, security and governance of the lakehouse

## Section 1: Developing Code for Data Processing (22%)

**[📖 Declarative Automation Bundles](https://docs.databricks.com/aws/en/dev-tools/bundles/)** - project structure and deployment
**[📖 Lakeflow Spark Declarative Pipelines](https://docs.databricks.com/aws/en/ldp/)** - streaming tables, materialized views, flows
**[📖 AUTO CDC APIs](https://docs.databricks.com/aws/en/ldp/cdc)** - CDC and SCD Type 1/2
**[📖 Lakeflow Jobs](https://docs.databricks.com/aws/en/jobs/)** - tasks, control flow, triggers
**[📖 Run jobs with serverless compute](https://docs.databricks.com/aws/en/jobs/run-serverless-jobs)** - performance mode, retries, high memory
**[📖 User-defined functions](https://docs.databricks.com/aws/en/udf/)** - SQL, Python, pandas and Unity Catalog UDFs

**Key Facts:**
- Bundles were renamed from Databricks Asset Bundles in March 2026; `databricks.yml` and `databricks bundle ...` commands are unchanged
- Keep logic in importable modules, build a wheel as a bundle artifact, and test functions chained with `DataFrame.transform`
- `pyspark.testing.assertDataFrameEqual` compares data; `assertSchemaEqual` compares schemas
- Serverless has no init scripts or compute-scoped libraries; dependencies go in the environment spec
- Prefer built-in functions; then SQL UDF; pandas UDF for Python at scale; Unity Catalog functions for governed reuse and masks
- Streaming table: append-only source, each row processed once. Materialized view: always-correct result including late updates
- AUTO CDC (formerly APPLY CHANGES): `keys`, `sequence_by`, `apply_as_deletes`, `stored_as_scd_type`; SCD Type 2 adds `__START_AT` and `__END_AT`
- Lakeflow Jobs control flow: If/else condition task, For each task (with concurrency), Run if conditions, per-task retries
- Serverless jobs: **standard** mode is cheaper with 4-6 minute start; **performance optimized** is faster
- Disable serverless auto-optimization to disallow retries for non-idempotent jobs; high memory is for notebook tasks only
- Exactly-once = replayable source + checkpoint + idempotent sink; watermarks bound state; `foreachBatch` is at-least-once unless idempotent

## Section 2: Data Ingestion & Acquisition (7%)

**[📖 Auto Loader](https://docs.databricks.com/aws/en/ingestion/cloud-object-storage/auto-loader/)** - incremental file ingestion
**[📖 Lakeflow Connect](https://docs.databricks.com/aws/en/ingestion/lakeflow-connect/)** - managed connectors
**[📖 Kafka](https://docs.databricks.com/aws/en/connect/streaming/kafka)** - streaming source options

**Key Facts:**
- Auto Loader discovery: directory listing (default) or file notification / managed file events
- `cloudFiles.schemaEvolutionMode`: `addNewColumns` (default), `rescue`, `failOnNewColumns`, `none`; `_rescued_data` keeps unmatched data
- `binaryFile` gives `path`, `modificationTime`, `length`, `content`
- Kafka gives binary `key` and `value` plus `topic`, `partition`, `offset`, `timestamp`; offsets live in the checkpoint
- Land bus data in bronze first, so you can replay after the bus expires it
- Lakeflow Connect database connectors = ingestion gateway (classic compute) + serverless ingestion pipeline
- Source prerequisites: SQL Server change tracking or CDC; MySQL row-based binlog; PostgreSQL logical replication
- Iceberg targets: Unity Catalog managed Iceberg tables, or UniForm on Delta for Iceberg readers

## Section 3: Data Transformation, Cleansing, and Quality (10%)

**[📖 Window functions](https://docs.databricks.com/aws/en/sql/language-manual/sql-ref-window-functions)** - ranking and analytic functions
**[📖 Query variant data](https://docs.databricks.com/aws/en/semi-structured/variant)** - VARIANT paths and functions
**[📖 AI functions](https://docs.databricks.com/aws/en/large-language-models/ai-functions)** - `ai_query` and task functions
**[📖 Pipeline expectations](https://docs.databricks.com/aws/en/ldp/expectations)** - warn, drop, fail, quarantine

**Key Facts:**
- `row_number` unique; `rank` ties with gaps; `dense_rank` ties without gaps; `QUALIFY` filters on a window result
- Broadcast hash join for a small side; sort-merge for large-to-large; AQE converts joins and splits skew at runtime
- `approx_count_distinct` is far cheaper than exact distinct counts
- `parse_json` / `try_parse_json` to VARIANT; `variant_get` / `try_variant_get` and `v:path::type` to extract
- `ai_query(endpoint, request)` calls any Model Serving endpoint; task functions include `ai_classify`, `ai_extract`, `ai_mask`, `ai_parse_document`
- Expectations: `expect` (warn, default), `expect_or_drop`, `expect_or_fail`; quarantine = second table with the inverted rule

## Section 4: Data Sharing and Federation (5%)

**[📖 OpenSharing](https://docs.databricks.com/aws/en/opensharing)** - shares, recipients, providers
**[📖 Clean Rooms](https://docs.databricks.com/aws/en/clean-rooms/)** - privacy-safe collaboration
**[📖 Lakehouse Federation](https://docs.databricks.com/aws/en/query-federation/)** - query and catalog federation

**Key Facts:**
- OpenSharing is the renamed Delta Sharing protocol, now hosted by the Linux Foundation
- Databricks-to-Databricks: recipient identified by metastore sharing identifier, no token; shares views, volumes, models, notebooks; `WITH HISTORY` enables CDF and streaming
- Databricks-to-Open: bearer token via activation link, or OIDC federation; any client
- Share a view to restrict rows or columns for a recipient
- Clean Rooms: collaborators add assets and approved notebooks; outputs go to output tables; nobody sees others' raw data
- Federation: `CREATE CONNECTION` + `CREATE FOREIGN CATALOG`; one credential per connection; Unity Catalog grants per user; foreign catalogs are read-only

## Section 5: Monitoring and Alerting (10%)

**[📖 System tables](https://docs.databricks.com/aws/en/admin/system-tables/)** - billing, compute, access, lakeflow, query
**[📖 Pipeline event log](https://docs.databricks.com/aws/en/ldp/monitor-event-logs)** - health and expectation metrics
**[📖 Databricks SQL alerts](https://docs.databricks.com/aws/en/sql/user/alerts/)** - scheduled query conditions
**[📖 Monitoring Lakeflow Jobs](https://docs.databricks.com/aws/en/jobs/monitor)** - matrix view and run details

**Key Facts:**
- Cost: `system.billing.usage` joined to `system.billing.list_prices`; attribute with `usage_metadata` and `custom_tags`
- Audit: `system.access.audit`; lineage: `system.access.table_lineage` / `column_lineage`
- Jobs and pipelines: `system.lakeflow.jobs`, `job_run_timeline`, `job_task_run_timeline`, `pipelines`
- Event log: `event_log('<pipeline-id>')` or publish to a Unity Catalog table; `flow_progress` holds expectation metrics
- SQL alert states: `OK`, `TRIGGERED`, `ERROR`; job failure notifications are configured on the job
- API: `jobs/runs/list`, `runs/get`, `runs/get-output`; CLI `databricks jobs ...`; SDK `WorkspaceClient().jobs`

## Section 6: Cost & Performance Optimisation (13%)

**[📖 Predictive optimization](https://docs.databricks.com/aws/en/optimizations/predictive-optimization)** - automatic maintenance
**[📖 Liquid clustering](https://docs.databricks.com/aws/en/tables/clustering)** - keys and `CLUSTER BY AUTO`
**[📖 Deletion vectors](https://docs.databricks.com/aws/en/tables/features/deletion-vectors)** - avoiding file rewrites
**[📖 Disk cache](https://docs.databricks.com/aws/en/optimizations/disk-cache)** - local SSD caching
**[📖 Change data feed](https://docs.databricks.com/aws/en/tables/features/change-data-feed)** - row-level changes
**[📖 Query profile](https://docs.databricks.com/aws/en/sql/user/queries/query-profile)** - operator metrics

**Key Facts:**
- Predictive Optimization runs `OPTIMIZE`, `VACUUM` and `ANALYZE` automatically on Unity Catalog managed tables
- Liquid Clustering: up to four keys, incremental, keys changeable with `ALTER TABLE ... CLUSTER BY`; `OPTIMIZE ... FULL` reclusters all
- `CLUSTER BY AUTO` picks keys from query history; needs managed tables with Predictive Optimization
- Partition only very large tables on low-cardinality columns (about 1 GB+ per partition); Z-order is superseded and cannot combine with clustering
- Deletion vectors mark deleted rows instead of rewriting files: fast scattered `MERGE`/`DELETE`, row-level concurrency
- Disk cache (formerly Delta cache) speeds repeated reads; automatic on SQL warehouses and serverless
- CDF columns: `_change_type` (`insert`, `update_preimage`, `update_postimage`, `delete`), `_commit_version`, `_commit_timestamp`
- Query profile: files pruned (skipping), join type, exchange size (shuffle), spill and skew

## Section 7: Ensuring Data Security and Compliance (10%)

**[📖 ABAC](https://docs.databricks.com/aws/en/data-governance/unity-catalog/abac/)** - policies with governed tags
**[📖 Row filters and column masks](https://docs.databricks.com/aws/en/data-governance/unity-catalog/filters-and-masks)** - per-table controls
**[📖 Data classification](https://docs.databricks.com/aws/en/data-governance/unity-catalog/data-classification)** - automatic PII tagging
**[📖 GDPR with Delta and pipelines](https://docs.databricks.com/aws/en/ldp/gdpr)** - deleting personal data

**Key Facts:**
- Grant to groups and service principals; `USE CATALOG` + `USE SCHEMA` + `SELECT` for reading
- Workspace ACLs are separate: `CAN MANAGE RUN` to run a job, `CAN USE` for a warehouse
- `ALTER TABLE ... SET ROW FILTER fn ON (col)`; `ALTER COLUMN c SET MASK fn`
- `CREATE POLICY ... COLUMN MASK | ROW FILTER ... MATCH COLUMNS has_tag_value(...)` on a catalog, schema or table; covers future tables; needs `MANAGE` and `EXECUTE`
- Pseudonymisation (hashing with a salt, tokenisation) keeps joins; anonymisation (suppression, generalisation) is irreversible
- Erasure: `DELETE` + `REORG TABLE ... APPLY (PURGE)` (deletion vectors) + `VACUUM`, plus downstream copies; streaming readers need `skipChangeCommits`

## Section 8: Data Governance (7%)

**[📖 Manage privileges](https://docs.databricks.com/aws/en/data-governance/unity-catalog/manage-privileges/)** - inheritance and grants
**[📖 Tags](https://docs.databricks.com/aws/en/database-objects/tags)** - tagging securables
**[📖 Comments](https://docs.databricks.com/aws/en/comments/)** - manual and AI-generated

**Key Facts:**
- Privileges granted on a catalog or schema are inherited by all child objects, including future ones
- Privileges are additive; an inherited privilege cannot be revoked on a child
- `BROWSE` shows metadata without data access; `MANAGE` lets a non-owner manage grants
- Production objects should be owned by groups or service principals
- `COMMENT ON TABLE`, `ALTER COLUMN ... COMMENT`, `SET TAGS`; query `information_schema.table_tags` and `column_tags`

## Section 9: Debugging and Deploying (10%)

**[📖 Repair job failures](https://docs.databricks.com/aws/en/jobs/repair-job-failures)** - repair runs and overrides
**[📖 Spark UI guide](https://docs.databricks.com/aws/en/optimizations/spark-ui-guide/)** - diagnosing slow jobs
**[📖 Bundle deployment modes](https://docs.databricks.com/aws/en/dev-tools/bundles/deployment-modes)** - development vs production
**[📖 Git folders](https://docs.databricks.com/aws/en/repos/)** - Git in the workspace

**Key Facts:**
- Skew: max task time far above median in the Stages tab; spill above zero means partitions too large
- Driver `OutOfMemoryError` usually means `collect()` / `toPandas()` / an oversized broadcast
- Compute event log and driver logs explain start failures and library errors; enable cluster log delivery for job clusters
- Repair run re-runs failed and skipped tasks only, with optional parameter overrides
- `databricks bundle validate | plan | deploy -t <target> | run | destroy`
- `mode: development` prefixes and pauses; `mode: production` requires a non-personal run identity
- Recommended CI/CD: merge to main, CI runs tests, `bundle deploy` as a service principal

## Section 10: Data Modelling (6%)

**[📖 Data modeling](https://docs.databricks.com/aws/en/transform/data-modeling)** - dimensional models on Databricks
**[📖 Metric views](https://docs.databricks.com/aws/en/uc-semantics/metric-views)** - governed metric definitions
**[📖 File size tuning](https://docs.databricks.com/aws/en/tables/tune-file-size)** - optimized writes and compaction

**Key Facts:**
- Partition to grain and lifecycle (e.g. event date on a very large daily-loaded table); cluster to join and filter keys
- Keep file sizes balanced with optimized writes, auto compaction and `OPTIMIZE`
- Star schema: facts at a declared grain with foreign keys and measures; dimensions with attributes
- Primary and foreign key constraints are informational; `RELY` lets the optimizer use them
- Materialized views pre-compute aggregates; metric views (`CREATE VIEW ... WITH METRICS LANGUAGE YAML`) define measures once, queried with `MEASURE()`

## Exam Tips

1. **Section 1 is the biggest**; master the choose-between pairs and the serverless settings
2. **New names everywhere**: Declarative Automation Bundles, AUTO CDC, OpenSharing, Lakeflow Jobs
3. **Managed and automatic beats manual**: managed tables, Predictive Optimization, `CLUSTER BY AUTO`, ABAC
4. **Match symptom to tool**: system tables, event log, query profile, Spark UI, task output
5. **Erasure is a multi-step process**, not a single `DELETE`
6. **Time management**: about 2 minutes per question

## Quick Reference

```sql
-- CDC in a pipeline
CREATE FLOW f AS AUTO CDC INTO silver.customers FROM STREAM(bronze.cdc)
KEYS (id) APPLY AS DELETE WHEN op = 'DELETE' SEQUENCE BY ts
COLUMNS * EXCEPT (op, ts) STORED AS SCD TYPE 2;

-- Layout and maintenance
CREATE TABLE t (...) CLUSTER BY AUTO;
ALTER TABLE t CLUSTER BY (customer_id, order_date);
ALTER TABLE t SET TBLPROPERTIES (delta.enableChangeDataFeed = true);
REORG TABLE t APPLY (PURGE);
VACUUM t;

-- Security and governance
CREATE POLICY mask_pii ON SCHEMA prod.crm COLUMN MASK prod.sec.mask_str
  TO `analysts` FOR TABLES MATCH COLUMNS has_tag('pii') AS c ON COLUMN c;
GRANT USE CATALOG, USE SCHEMA, SELECT ON CATALOG prod TO `analysts`;
ALTER TABLE t SET TAGS ('domain' = 'sales');

-- Monitoring
SELECT * FROM event_log('<pipeline-id>') WHERE event_type = 'flow_progress';
SELECT * FROM system.lakeflow.job_run_timeline WHERE result_state = 'FAILED';
```

```bash
databricks bundle validate -t prod
databricks bundle deploy -t prod
databricks jobs repair-run <run_id> --rerun-all-failed-tasks
```
