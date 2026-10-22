# Databricks Data Engineer Associate - Practice Questions

25 scenario-based questions for Databricks DE Associate prep. Every question is tagged with the May 2026 exam guide's seven sections, the largest being data transformation and modeling (22%) and data ingestion and loading (21%).

> **Cert page:** [exams/databricks/data-engineer-associate/](../../exams/databricks/data-engineer-associate/)

---

### Question 1 - Domain 1: Databricks Intelligence Platform
**Scenario:** A pipeline reads from S3, transforms with Spark, and writes Delta tables. Which storage layer should the curated tables use?

A. CSV, since it is plain text that any Spark job can write and that every downstream tool can open
B. JSON with one record per line, which keeps the nested structure of the source and supports schema evolution
C. Delta Lake (which is Parquet + transaction log)
D. Parquet, which is columnar and compressed and so also gives ACID transactions through its file footers

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Delta Lake is the default Databricks lakehouse format. ACID transactions, time travel, schema evolution, optimized reads (Z-order). Plain Parquet lacks ACID; CSV/JSON lack columnar performance.
</details>

---

### Question 2 - Domain 2: Data Ingestion and Loading
**Scenario:** A streaming job reads Kafka and writes to Delta. The query crashes after 4 hours and you need to resume from where it left off. What feature?

A. Delta time travel, which lets the query reread the table at the version where it stopped and carry on
B. Manual offset tracking in a separate table, updated by the job itself after each micro-batch is written
C. Spark Structured Streaming checkpointing
D. Auto Loader, which records the offset of each Kafka partition and restarts the query from that position

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Structured Streaming uses checkpoint directories to track progress. Specify `option("checkpointLocation", "s3://...")` and Spark records what's been processed. On restart, the query resumes from the last checkpoint. Delta sink ensures exactly-once.
</details>

---

### Question 3 - Domain 2: Data Ingestion and Loading
**Scenario:** Auto Loader (`cloudFiles`) is best for what use case?

A. Streaming Kafka topics, where it manages each partition's offsets
B. A one-time bulk migration of historical files
C. ETL between Delta tables, where it follows the transaction log versions of the source table as they appear
D. Incrementally ingesting new cloud storage files, with schema inference and evolution

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Auto Loader uses cloud notifications (or directory listing) to detect new files in S3/ADLS/GCS and ingest them incrementally. Handles schema inference, schema drift, and rescued data. Built-in for incremental file ingestion patterns.
</details>

---

### Question 4 - Domain 3: Data Transformation and Modeling
**Scenario:** Which Delta operation is appropriate for upserts (insert if not exists, update if exists)?

A. MERGE INTO, matching on the key to update existing rows and insert new ones
B. COPY INTO, which loads files and updates the rows that match the target's key as part of the load
C. UPDATE, which changes existing rows and also inserts the rows that do not yet exist in the target
D. INSERT, which adds each source row as a new row

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** `MERGE INTO target USING source ON match_condition WHEN MATCHED THEN UPDATE SET ... WHEN NOT MATCHED THEN INSERT VALUES ...` is the upsert primitive. Common for slowly-changing-dimension and CDC merge patterns.
</details>

---

### Question 5 - Domain 1: Databricks Intelligence Platform
**Scenario:** A team has a long-running data pipeline that takes 3 hours daily. They want to optimize. Which technique reduces compute cost the most?

A. Add more workers to the cluster so each stage has more parallel tasks
B. Photon plus autoscaling clusters with min nodes = 1, so the faster engine shortens the run and idle workers are released
C. Switch to the standard runtime, which drops the extra libraries
D. Run on serverless SQL only, since the pipeline code can be rewritten as queries

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Photon is Databricks' vectorized query engine; often 3-5x faster than Spark on the same workload. Autoscaling with low min lets the cluster scale up under load and shrink when idle. More workers without Photon doesn't fix the per-worker bottleneck.
</details>

---

### Question 6 - Domain 6: Troubleshooting, Monitoring, and Optimization
**Scenario:** Delta Lake `OPTIMIZE` does what?

A. Compacts small files into larger files for read performance, optionally co-locating data with Z-ordering
B. Updates table statistics so the optimizer can choose better plans
C. Vacuums orphaned files the log no longer references
D. Deletes old data once it is outside the time travel window

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** `OPTIMIZE table` compacts many small files (the "small files problem" hurting query perf) into larger ones. Optionally with `ZORDER BY (col)` to co-locate data on a column for predicate pushdown. `VACUUM` deletes orphaned files; `ANALYZE TABLE` updates stats.
</details>

---

### Question 7 - Domain 7: Governance and Security
**Scenario:** Unity Catalog three-level namespace is:

A. catalog.schema.table, with the catalog as the top level governed by Unity Catalog
B. workspace.database.table
C. database.schema.table
D. region.workspace.table, which names the cloud region and the workspace that hold the table's files

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Unity Catalog uses `<catalog>.<schema>.<table>` (e.g., `prod.sales.orders`). Replaces the older two-level Hive metastore (`database.table`) with a third level for governance / data sharing across workspaces.
</details>

---

### Question 8 - Domain 7: Governance and Security
**Scenario:** A team needs to share a Delta table with another organization that uses Snowflake. What feature?

A. CSV export of the table to a shared bucket, refreshed on a schedule so the other side has recent data, then loaded by hand
B. Delta Sharing, an open protocol that works with Snowflake and Power BI, with no Databricks at the receiver
C. Snowflake Sharing only, which reads the Delta table directly from the Databricks workspace
D. SSO federation between the two organizations' identity providers, which exposes the tables to the other side's analysts

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Delta Sharing is an open protocol for sharing live data without copies. Receivers don't need Databricks - they can use any client implementing the protocol (including Iceberg clients such as Snowflake, which can read shared data with the provider's credential file).

**Key Concept:** [Read data shared using Delta Sharing open sharing](https://docs.databricks.com/aws/en/delta-sharing/read-data-open)
</details>

---

### Question 9 - Domain 6: Troubleshooting, Monitoring, and Optimization
**Scenario:** What does `VACUUM` do on a Delta table?

A. Removes data files that the log no longer references and that are older than the retention threshold (default 7 days)
B. Deletes the table's data files and its transaction log, leaving only the empty definition behind
C. Backs up the table to a second location, keeping the last 7 days of its versions
D. Compacts small files into larger ones, the same as OPTIMIZE does

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** VACUUM is the cleanup that actually deletes orphaned data files (after deletes/updates create old versions). Default retention is 7 days; you can `VACUUM table RETAIN 168 HOURS` for 7 days. Setting retention < 7 days requires `spark.databricks.delta.retentionDurationCheck.enabled=false` and is risky for time travel.
</details>

---

### Question 10 - Domain 3: Data Transformation and Modeling
**Scenario:** Delta Live Tables (DLT) primary value proposition?

A. Lower cost, because the service runs on cheaper hardware than a Spark cluster of the same size would use
B. Faster than vanilla Spark, because it replaces the Spark engine
C. SQL-only authoring, which avoids Python so that analysts can write a whole pipeline without help from an engineer or a notebook
D. Declarative pipelines: declare tables; DLT handles dependencies, retries, lineage and quality

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** DLT is declarative ETL. You write `CREATE STREAMING TABLE ...` and `CREATE LIVE TABLE ...` in SQL or Python; DLT figures out dependency order, retries, expectations (data quality), lineage, and incremental processing.
</details>

---

### Question 11 - Domain 3: Data Transformation and Modeling
**Scenario:** Streaming Tables (DLT) vs Materialized Views (DLT) - when to use each?

A. They're identical, since each keeps a table up to date
B. Streaming tables for incremental processing of new append-only data; materialized views recompute their results when the source changes
C. MV is for historical data only
D. Streaming is for real-time only, while a materialized view can only run once a day

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Streaming tables incrementally process appended source data (efficient for append-only streams). Materialized views recompute on-demand or schedule when their definition or source changes (suitable for transforms with joins, aggregations).
</details>

---

### Question 12 - Domain 3: Data Transformation and Modeling
**Scenario:** A pipeline must enforce data quality. Which DLT feature?

A. Expectations (`@dlt.expect_or_drop`, `@dlt.expect_or_fail`, `@dlt.expect`)
B. Manual SQL checks run in a notebook cell after each update, with the results compared against the expected counts
C. Cluster permissions set so that only the pipeline owner can write to the target tables of the pipeline
D. Spark `assert` statements in the notebook, which stop the pipeline run when a row fails the check

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** DLT expectations declaratively enforce data quality. `expect` warns; `expect_or_drop` removes bad rows; `expect_or_fail` halts the pipeline. Metrics are surfaced in the DLT UI.
</details>

---

### Question 13 - Domain 1: Databricks Intelligence Platform
**Scenario:** An analyst needs ad-hoc SQL on Delta tables with serverless compute. Which Databricks product?

A. ML compute, a runtime with the machine learning libraries preinstalled, meant for training rather than SQL
B. An all-purpose cluster, which becomes serverless when it autoscales and is designed for ad-hoc SQL
C. Databricks SQL warehouse (formerly SQL Endpoint)
D. A job cluster, which starts on demand for each query and shuts down again when the query ends

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Databricks SQL warehouses (Serverless or Pro/Classic) are optimized for ad-hoc and BI SQL workloads. Quick startup, autoscaling, integrates with BI tools (Tableau, Power BI). All-purpose is interactive notebook work; Job clusters are for scheduled runs.
</details>

---

### Question 14 - Domain 1: Databricks Intelligence Platform
**Scenario:** Time-travel a Delta table to last week's state for a comparison query?

A. Use snapshots taken by the metastore each night, which are queried with a `SNAPSHOT` clause in the SELECT
B. Time travel isn't supported, because Delta overwrites data files in place when rows are updated or deleted
C. Restore from a backup of the storage account, then load that copy into a second table for the comparison
D. `SELECT * FROM tbl VERSION AS OF 12345` or `TIMESTAMP AS OF '2026-04-20'`

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Delta Lake time travel: `SELECT * FROM tbl VERSION AS OF <n>` or `TIMESTAMP AS OF '<date>'`. Useful for debugging, audit, reproducible analysis. Limited by the table's `delta.deletedFileRetentionDuration` (default 7 days) - past that, files may be vacuumed.
</details>

---

### Question 15 - Domain 7: Governance and Security
**Scenario:** Which is the correct ACL pattern in Unity Catalog?

A. `chmod` on the underlying S3 path
B. `GRANT SELECT ON TABLE catalog.schema.tbl TO `analyst-group``
C. IAM only, with a bucket policy
D. `GRANT SELECT ON TABLE tbl TO USER alice`, repeated for each analyst, using the default catalog and schema

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Unity Catalog uses ANSI-style GRANT/REVOKE on three-level objects. Best practice: grant to groups (managed in your IdP via SCIM). Underlying cloud-storage IAM is only for the workspace metastore identity, not user access.
</details>

---

### Question 16 - Domain 6: Troubleshooting, Monitoring, and Optimization
**Scenario:** A Delta table has accumulated 10,000 small files from frequent micro-batch writes. Read latency is poor. Which command compacts files without changing schema?

A. `OPTIMIZE table_name`, optionally with `ZORDER BY` on common filter columns; it bin-packs small files and leaves the schema unchanged
B. `MSCK REPAIR` on the table, which merges small files into larger ones as it re-registers the partitions
C. `CONVERT TO DELTA` on the table, which rewrites the data files at the target size without a schema change
D. `VACUUM`, which merges the small files and removes the older versions in the same pass over the table

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** OPTIMIZE bin-packs small files into ~1 GB target files; ZORDER BY (when added) co-locates rows by a column for skipping. VACUUM removes old data files past the retention period.
</details>

---

### Question 17 - Domain 3: Data Transformation and Modeling
**Scenario:** Difference between MERGE INTO and a separate DELETE + INSERT pattern?

A. MERGE is slower, since it must scan the whole target table for every source row, so the manual pattern is faster in practice for large tables
B. MERGE only works on partitioned tables, because it needs the partition columns to match rows
C. They produce the same output and the same guarantees, so the choice between them is only one of style
D. MERGE INTO is atomic in one transaction and supports MATCHED / NOT MATCHED conditions; delete+insert needs explicit transaction handling

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** MERGE INTO is the canonical Delta upsert. Atomic, supports complex match conditions, CDC-friendly. Manual delete+insert has race conditions and double the I/O.
</details>

---

### Question 18 - Domain 2: Data Ingestion and Loading
**Scenario:** Delta Live Tables (DLT) - what does Auto Loader solve?

A. Cluster autoscaling for the pipeline, which adds and removes workers as the number of new files arriving in the storage account changes through the day
B. SQL query optimization, by rewriting the joins and aggregations in the pipeline's queries to a faster execution plan
C. Incremental ingestion of new files with exactly-once semantics, schema evolution and a rescued data column
D. Hyperparameter tuning for the models trained on the bronze tables

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Auto Loader (`cloudFiles` source) tracks processed files in a checkpoint, processes only new arrivals, and handles schema drift gracefully. Standard pattern for ingesting from S3 / ADLS / GCS into bronze Delta tables.
</details>

---

### Question 19 - Domain 7: Governance and Security
**Scenario:** Unity Catalog: which scope does `GRANT SELECT ON SCHEMA` apply to?

A. The whole metastore, so the user can select from every catalog and schema attached to it
B. A single table named in the statement, since grants on a schema apply only to the object they name
C. All current and future tables and views in the schema
D. The cluster only, so the user can run the SELECT from the one cluster where the grant was issued

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Schema-level grants cascade to current and future objects unless explicitly overridden. Use schema grants for team-wide access; table grants for fine-grained.
</details>

---

### Question 20 - Domain 3: Data Transformation and Modeling
**Scenario:** Bronze ingests raw JSON. Silver applies cleansing. Gold serves aggregates. Typical refresh cadence?

A. Bronze is daily only, because raw JSON can only be ingested in a batch once the day's files have all arrived in the storage location
B. Bronze near-real-time (Auto Loader or DLT continuous), Silver follows, Gold on a business schedule or on demand
C. All three refresh hourly, so every layer is exactly as fresh as the one that feeds it
D. Gold refreshes first, since aggregates are what users query, and the others catch up later

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Medallion architecture: bronze captures raw arrival, silver normalizes for joinability, gold aggregates for BI. Refresh frequencies cascade based on downstream needs.
</details>

---

### Question 21 - Domain 1: Databricks Intelligence Platform
**Scenario:** Time Travel in Delta - which is true?

A. Time Travel is a paid add-on that must be enabled per workspace before the history is kept for a table in that workspace
B. Time Travel only works on partitioned tables, because the versions are tracked at partition level
C. Time Travel is unlimited, since the transaction log keeps every version of the data files forever and VACUUM only removes logs
D. Query old versions with `VERSION AS OF` or `TIMESTAMP AS OF` until VACUUM removes the files (default 7 days)

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Delta keeps version history in `_delta_log/`. Old data files remain queryable until VACUUM removes them past `delta.deletedFileRetentionDuration` (default 7 days). For longer retention, raise the property.
</details>

---

### Question 22 - Domain 4: Working with Lakeflow Jobs
**Scenario:** A nightly notebook job fails 1 in 5 times due to transient cluster startup issues. Best fix?

A. Disable logging, since writing log files to storage at startup is what makes it fail
B. Switch to an interactive cluster that stays running, and attach the notebook to it for each run each night, sized for the job
C. Manual retry every morning by the on-call engineer
D. Configure job-level retries and use job clusters (a fresh cluster per run), plus library version pinning

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Job clusters spin up fresh per run, isolating from drift on shared clusters. Retries handle transient failures. Pin library versions for reproducibility.
</details>

---

### Question 23 - Domain 1: Databricks Intelligence Platform
**Scenario:** Photon - what is it and when does it help most?

A. A serverless tier of compute that starts in seconds and is billed per query rather than by the cluster hour, with no clusters to size
B. A SQL syntax extension that adds window and pivot functions to Spark SQL, helping the queries that use those functions the most
C. A vectorized C++ engine replacing JVM operators; helps CPU-heavy SQL/DataFrame work, little for IO-bound or UDF-heavy code
D. A model registry for tracking versions of trained models, which helps most when many teams share the same models and need approvals

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Photon accelerates analytic SQL/DF workloads. Best ROI on heavy joins/aggregations on large columnar data. Python UDFs and external IO get less benefit since the bottleneck is elsewhere.
</details>

---

### Question 24 - Domain 2: Data Ingestion and Loading
**Scenario:** A streaming pipeline reads Kafka and writes Delta. What configuration achieves exactly-once?

A. Structured Streaming with a unique checkpoint per query and a Delta sink; avoid `failOnDataLoss=false` unless intended
B. Disable checkpoints, so the query always restarts from the earliest offset
C. Set startingOffsets=latest, so the query never reads a message twice after a restart
D. Use foreachBatch always, writing each micro-batch with a plain append

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Structured Streaming + Delta gives exactly-once when checkpoint location is unique and the sink is transactional. Delta's transaction log handles re-runs safely.
</details>

---

### Question 25 - Domain 1: Databricks Intelligence Platform
**Scenario:** Cost optimization: workload runs 16 hours/day, idle 8 hours. Best cluster choice?

A. An all-purpose cluster running 24/7, so that it is always warm when the first job of the day starts
B. Job cluster plus Workflows scheduling, or a SQL warehouse with auto-stop at 5-15 minutes idle
C. Use serverless for everything, including long Spark jobs that need custom libraries and instance types
D. Photon disabled on every cluster

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Auto-stop is the simplest cost lever (idle == off). Job clusters scale to zero between runs by design. SQL Warehouses have native auto-stop.
</details>

---

## Scoring guide

- **22-25:** Schedule the exam.
- **17-21:** Re-read weak-area notes.
- **<17:** Hands-on Delta Lake / DLT practice + re-read fact-sheet.

DE Associate exam: ~45 questions, 90 minutes. Multi-choice + multi-select. Strong focus on Delta Lake operations, DLT, ELT patterns, and Unity Catalog.
