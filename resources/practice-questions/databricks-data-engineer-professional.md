---
last-updated: 2026-10-22
difficulty: advanced
---

# Databricks Certified Data Engineer Professional - Practice Questions

30 questions for the Data Engineer Professional exam. Questions 16-30 are tagged with the October 2026 exam outline's ten sections, the largest being developing code with Python and SQL (22%) and cost and performance optimisation (13%).

> **Cert page:** [exams/databricks/data-engineer-professional/](../../exams/databricks/data-engineer-professional/)

---

### Question 1
**Scenario:** A streaming job must process only new files arriving in cloud storage, at scale.

A. A cron job that compares file modification timestamps to the last run time and loads any newer files
B. Reprocess everything on each run and rely on a merge into the target table to remove the duplicate rows
C. Auto Loader (`cloudFiles`), which tracks discovered files
D. List the directory each run and diff the result against a table of already-loaded file names

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Directory listing degrades badly once a path holds millions of objects. Auto Loader maintains its own state of processed files and can use cloud notification services instead of listing, with schema inference and evolution built in.
</details>

---

### Question 2
**Scenario:** A Delta table must apply updates and inserts from a change feed atomically.

A. Append everything and de-duplicate on read by key
B. `MERGE INTO` keyed on the business key, with the change type choosing insert, update, or delete
C. Overwrite the table with the latest batch, which drops every row not in the feed
D. Delete the affected keys first, then insert the new versions in a second statement run straight afterwards

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** MERGE is atomic, so readers never see a partially applied batch, and it expresses all three operations in one statement. Delete-then-insert leaves a window where rows are missing, which streaming consumers will observe.
</details>

---

### Question 3
**Scenario:** Downstream jobs need to know exactly which rows changed in a Delta table.

A. Use the transaction log manually, parsing the JSON commit files in `_delta_log` to infer which rows changed
B. Compare full snapshots of the table from two versions with an anti join to find the differences
C. Enable Change Data Feed on the table and read the changes with row-level change types
D. Add a last-modified timestamp column and have each downstream job filter on values after its last run

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** CDF emits inserts, updates (pre and post images), and deletes with commit versions, which is what makes incremental downstream processing correct rather than approximate. Timestamp columns miss deletes entirely and are unreliable under late-arriving data.
</details>

---

### Question 4
**Scenario:** A structured streaming job must guarantee exactly-once processing across restarts.

A. Manual offset tracking in a separate table that the job updates after each micro-batch completes successfully
B. A checkpoint location plus an idempotent sink, which Delta provides with transactional writes
C. Reprocess from the beginning on every restart and drop rows already in the sink
D. Nothing is needed, because Structured Streaming is exactly-once by default without any state saved anywhere

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** The checkpoint records source offsets and the sink's transaction log makes writes idempotent, so a restart neither loses nor duplicates data. Deleting a checkpoint is effectively a full restart, which is why checkpoints are treated as production state.
</details>

---

### Question 5
**Scenario:** A slowly changing dimension must keep history of attribute changes.

A. Use a view that selects the latest value of each attribute from the source table at query time
B. Overwrite the row in place (SCD Type 1) so that each dimension key always holds current values
C. Delete old rows once a new version arrives, keeping one row per key
D. SCD Type 2: close the current row with an end timestamp and insert a new current row

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Type 2 preserves history so a fact from last year joins to the dimension values that were current then. Type 1 overwrites and loses that, which silently changes historical reports. Delta Live Tables has `APPLY CHANGES` support for both patterns.
</details>

---

### Question 6
**Scenario:** Query performance on a large Delta table filtered by two high-cardinality columns is poor.

A. Increase cluster size only, adding workers so that more cores scan the same number of files in parallel
B. Use liquid clustering (or Z-ordering with `OPTIMIZE`) on the filter columns to co-locate related data for file skipping
C. Convert the table to CSV files, which any reader can scan without Delta metadata overhead
D. Add more partitions on the two high-cardinality columns so each filter value gets its own directory

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Multi-dimensional clustering improves data skipping by co-locating similar values so file-level statistics prune more effectively. Partitioning on high-cardinality columns creates many small files and makes performance worse, which is a common misstep.
</details>

---

### Question 7
**Scenario:** A table has accumulated thousands of small files.

A. Repartition to 10,000 partitions so that each task has less data and every file is smaller still
B. Ignore it, because the Delta transaction log already merges small files in the background
C. `OPTIMIZE` to compact, then enable auto compaction and optimized writes
D. Add more nodes so that the many small files can be opened in parallel by additional executors

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Each file adds open and metadata overhead, so small files dominate query time. Fixing the writer prevents recurrence and compaction cleans up existing data. Auto-optimize handles it continuously for streaming writes.
</details>

---

### Question 8
**Scenario:** Access must be governed centrally across workspaces with lineage.

A. Notebook permissions, setting who can view, run, or edit each notebook in the workspace
B. Table ACLs per workspace, configured separately by an admin in every workspace that uses the data, with no shared lineage view
C. Cloud storage permissions only, using IAM roles on the buckets that hold the table files
D. Unity Catalog: a three-level namespace with centralized grants, lineage, and audit across workspaces

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Unity Catalog moves governance above the workspace so one grant applies everywhere, and it captures column-level lineage automatically. Storage-level permissions cannot express table, column, or row scope, and per-workspace ACLs fragment as the estate grows.
</details>

---

### Question 9
**Scenario:** A streaming job's state store grows without bound on a windowed aggregation.

A. Set a watermark so state for closed windows can be dropped and late events are bounded
B. Restart the job daily so the state is rebuilt from the checkpoint
C. Disable checkpointing, so Spark has no state to keep
D. Increase memory on the driver and workers until the whole state store fits in memory comfortably

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Without a watermark, Spark must retain state indefinitely because any old window might still receive an event. The watermark declares a lateness bound, which is what lets old state be evicted, and it also defines which late records are dropped.
</details>

---

### Question 10
**Scenario:** A pipeline must enforce data quality and stop bad records reaching downstream tables.

A. Delta Live Tables expectations, choosing per rule to warn, drop the record, or fail the update
B. Trust the source system to send clean data and reject bad rows itself
C. Add a comment to the table definition that describes the rules each column is expected to follow in detail
D. Check the quality afterwards with a scheduled query and fix any bad rows that have been found, in the next sprint

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Expectations make quality declarative and observable in the pipeline event log, and the three actions map to different severities. Deciding upfront which rules are fatal is the part that turns quality from a dashboard into a gate.
</details>

---

### Question 11
**Scenario:** Old versions of a Delta table must be removed to reduce storage cost.

A. `VACUUM` with a retention period, knowing it limits time travel to that window
B. Nothing can be removed, because Delta keeps every version permanently for audit purposes
C. Drop the table and recreate it from the latest data, so that no old versions remain
D. Delete the old data files directly from cloud storage with a lifecycle rule or the console

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** VACUUM removes files no longer referenced by any retained version, and the default 7-day retention protects concurrent readers and streams. Deleting files manually corrupts the table because the transaction log still references them.
</details>

---

### Question 12
**Scenario:** A job is failing intermittently with skew: one task takes ten times longer than the rest.

A. Increase the timeout so the slowest task has enough time to finish
B. Address skew: use AQE skew join handling, salt the skewed key, or broadcast the small side of the join
C. Reduce partitions to one so that every row is processed by a single task, in order
D. Add nodes so that the cluster has spare capacity to run the slow task more quickly

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Skew means one partition holds far more rows than the others, so total capacity is irrelevant while one task is the critical path. AQE splits skewed partitions automatically in many cases; salting is the manual technique when it does not.
</details>

---

### Question 13
**Scenario:** A production job must be orchestrated with dependencies, retries, and alerting.

A. Databricks Jobs with multiple tasks, dependencies, retries, and notifications, defined as code
B. A scheduled notebook that runs each step in sequence in one cell and emails the owner if the run fails midway
C. An external cron job on a virtual machine that calls the notebook through the REST API and checks the exit code
D. Manual runs by an engineer who starts each notebook in order and watches for failures

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Multi-task jobs express the DAG, retries, and failure notification in one place, and defining them through Asset Bundles or the API makes them reviewable and promotable across environments. A single scheduled notebook has no dependency model.
</details>

---

### Question 14
**Scenario:** Development, staging, and production must use the same code against different data.

A. One shared environment where the three stages are separated only by naming conventions on the tables, with no separate catalogs
B. Copy notebooks between workspaces by hand and edit the paths after each copy
C. Hard-code the paths for each environment inside the notebooks
D. Databricks Asset Bundles or CI/CD, with parameterized catalogs and per-environment configuration

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Parameterizing the catalog and schema means the same artifact is promoted rather than reimplemented, which is what makes staging results meaningful. Copied notebooks drift, and the drift usually surfaces as a production-only bug.
</details>

---

### Question 15
**Scenario:** A pipeline's cost is higher than expected on an all-purpose cluster.

A. Increase the cluster size so that the pipeline finishes sooner and the cluster is idle less often
B. Keep it running continuously so that start-up time is never paid again on the next run
C. Use job clusters that end with the run, right-size the instances, and enable autoscaling for variable load
D. Run more frequently, so each run processes less data and the cluster does less work each time

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** All-purpose clusters bill at a higher rate and often stay up between runs; job clusters exist for the run and stop. Photon and serverless SQL warehouses are the other levers, trading a higher unit rate for enough speedup to reduce total cost.
</details>

---

### Question 16 - Domain 1: Developing Code for Data Processing using Python and SQL
**Scenario:** A Structured Streaming job reads order updates from Kafka. Each micro-batch must upsert into a Silver Delta table by `order_id`, but the streaming writer API only offers append, complete, and update output modes.

A. Use `foreachBatch` and run a Delta `MERGE` keyed on `order_id` inside the batch function
B. Write with output mode `update`, which makes the Delta sink update matching rows by key
C. Append every event to Silver and let downstream queries pick the latest row per key
D. Stop streaming and run a nightly batch job that overwrites Silver from the full topic

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** `foreachBatch` hands each micro-batch to your code as a normal DataFrame, so you can run any batch operation on it, including `MERGE`. That is the standard way to upsert from a stream. B is wrong because `update` output mode controls which aggregation rows are emitted; it does not turn the Delta sink into an upsert. C pushes the de-duplication cost onto every reader and lets Silver grow without bound. D throws away the freshness the stream was built to provide.
</details>

---

### Question 17 - Domain 1: Developing Code for Data Processing using Python and SQL
**Scenario:** A PySpark job applies a row-at-a-time Python UDF that trims, lowercases, and regex-cleans an email column. The stage is slow and Photon shows it is not accelerating that step.

A. Increase `spark.sql.shuffle.partitions` so that the UDF runs across more parallel tasks
B. Cache the DataFrame before the UDF so the input rows are read from memory instead
C. Replace the UDF with built-in functions such as `lower`, `trim`, and `regexp_replace`
D. Rewrite the UDF in Scala, since every JVM UDF is optimized by Catalyst and Photon

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Built-in functions run inside the engine, so Catalyst can optimize them and Photon can vectorize them. A plain Python UDF serializes each row out to a Python worker and back, which is the bottleneck here. A changes parallelism after a shuffle, but the per-row serialization cost stays. B removes a read cost that was never the problem. D is wrong because Scala UDFs are still opaque to the optimizer and are not run by Photon; built-in functions are the fix, with a pandas UDF as the fallback when no built-in fits.
</details>

---

### Question 18 - Domain 1: Developing Code for Data Processing using Python and SQL
**Scenario:** In a Lakeflow Declarative Pipeline, a Bronze dataset ingests append-only click events. A Gold dataset computes daily revenue per region from a Silver table that also receives late corrections and deletes.

A. Make both datasets streaming tables, so each source row is read once and never read again
B. Use a streaming table for the Bronze ingest and a materialized view for the Gold aggregate
C. Make both datasets materialized views, so each is fully recomputed on every pipeline update
D. Use a materialized view for Bronze and a streaming table for the Gold daily aggregate

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Streaming tables process each input row once, which suits append-only ingestion. Materialized views keep their result correct when upstream rows change or are deleted, refreshing incrementally where they can, which suits an aggregate over a table that gets corrections. A would leave Gold wrong after a correction, because a streaming table does not reprocess rows it already read. C wastes compute re-reading all raw clicks on every update. D reverses the fit on both datasets.
</details>

---

### Question 19 - Domain 2: Data Ingestion & Acquisition
**Scenario:** An Auto Loader stream ingests JSON files from a partner who sometimes adds fields without notice. The stream must never stop for a schema change, and no unexpected data may be silently lost.

A. Set `cloudFiles.schemaEvolutionMode` to `failOnNewColumns` and restart the stream by hand
B. Set `cloudFiles.schemaEvolutionMode` to `none`, so new fields are ignored and never logged
C. Turn off schema inference and read every file as a single string column with no parsing
D. Set `cloudFiles.schemaEvolutionMode` to `rescue`, so unknown fields land in `_rescued_data`

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** In `rescue` mode the schema stays fixed and the stream keeps running, while any field that does not match the schema is kept as JSON in the `_rescued_data` column for later review. A stops the stream on every new field, which breaks the first requirement. B keeps the stream running but drops the new fields, which breaks the second. C keeps everything but pushes all parsing downstream and gives up schema enforcement entirely.
</details>

---

### Question 20 - Domain 3: Data Transformation, Cleansing, and Quality
**Scenario:** IoT devices retry sends, so the same `event_id` can arrive twice, minutes apart and with slightly different ingest timestamps. A streaming Silver table must keep one row per `event_id` without its state growing forever.

A. Call `dropDuplicates("event_id")` with no watermark, so every key seen so far is kept in state
B. Set a watermark on event time and call `dropDuplicatesWithinWatermark("event_id")`
C. Group by `event_id` in a windowed aggregation and keep the first row of each window
D. Write duplicates to Silver and run a daily batch job that deletes the extra rows by key

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** `dropDuplicatesWithinWatermark` removes duplicates on the key columns even when other columns, such as timestamps, differ, and the watermark lets Spark evict old keys from state. A de-duplicates correctly, but without a watermark the state keeps every key forever. C misses duplicates that fall into different windows and adds aggregation overhead. D lets downstream readers see duplicates until the cleanup runs.
</details>

---

### Question 21 - Domain 3: Data Transformation, Cleansing, and Quality
**Scenario:** A Bronze table stores application logs as JSON strings whose nested fields change often. Analysts query only a handful of paths, and fixing a `from_json` schema each time a field appears keeps breaking the job.

A. Store the payload as `VARIANT` with `parse_json` and query paths like `payload:user.id::string`
B. Keep the JSON as a plain string and use `get_json_object` on every query that needs a field
C. Define a wide struct schema that lists every field ever seen, and add new ones as they appear
D. Flatten every nested field into its own top-level column at ingest, using `explode` where needed

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** `VARIANT` stores semi-structured data in an efficient binary form without a fixed schema, so new fields need no code change, and path queries read much faster than re-parsing strings. B works but re-parses the full JSON text on every query. C is the brittle schema maintenance the team wants to escape. D bakes today's shape into the table and still breaks on the next new field.
</details>

---

### Question 22 - Domain 4: Data Sharing and Federation
**Scenario:** A retailer must give a supplier read access to a live, governed sales table. The supplier does not use Databricks and wants to read the data from pandas and Power BI. Nightly file exports have gone stale and drifted.

A. Grant the supplier's users `SELECT` on the table through a new workspace account for them
B. Copy the table to a public cloud bucket every hour and send the supplier the bucket path
C. Create a Delta Sharing share and an open sharing recipient, and send its credential securely
D. Configure Lakehouse Federation so that the supplier's own systems can query the table

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Delta Sharing's open protocol lets a recipient on any platform read live shared tables with open-source connectors, including pandas and Power BI, with no copy to maintain. Access is managed and audited in Unity Catalog and can be revoked. A forces a non-Databricks partner into your workspace, which widens your attack surface. B is a copy that still goes stale, and a public bucket is a data leak. D goes the wrong way: Federation lets Databricks query external databases, not share out to partners.
</details>

---

### Question 23 - Domain 5: Monitoring and Alerting
**Scenario:** Finance wants an email whenever any workspace's daily DBU usage for jobs jumps more than 50% above its 7-day average. The account has 12 workspaces.

A. Ask each workspace admin to review the billing console every morning and report any spikes
B. Add a notification to every job that emails finance whenever a run takes longer than usual
C. Read the Spark UI of the largest clusters each day and estimate DBU usage from task time
D. Query `system.billing.usage` across workspaces and attach a Databricks SQL alert to the query

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** The `system.billing.usage` table holds usage for every workspace in the account, so one query can compute daily usage against a rolling average. A SQL alert on that query sends the email when the threshold is crossed. A depends on twelve people noticing, which is how spikes get missed. B measures run duration, not cost, and floods finance with noise. C is manual guesswork that does not see all workspaces.
</details>

---

### Question 24 - Domain 6: Cost & Performance Optimisation
**Scenario:** A GDPR job deletes a few hundred customer rows a day from a 40 TB Delta table. Each `DELETE` takes over an hour because it rewrites every large Parquet file that holds even one matching row.

A. Partition the table by `customer_id`, so each delete only touches one small directory
B. Enable deletion vectors, so deletes mark rows as removed instead of rewriting whole files
C. Run `VACUUM` before each delete, so there are fewer files left for the delete to rewrite
D. Raise the target file size with `OPTIMIZE`, so fewer files need to be scanned by the delete

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Deletion vectors record which rows in a file are removed, so `DELETE`, `UPDATE`, and `MERGE` skip the full file rewrite. The physical rewrite happens later during `OPTIMIZE` or `REORG TABLE ... APPLY (PURGE)`. For GDPR, purge and then `VACUUM` so the deleted data is truly gone. A creates millions of tiny partitions on a high-cardinality key. C removes only unreferenced old files, so the live files still get rewritten. D makes each rewritten file larger, which makes the problem worse.
</details>

---

### Question 25 - Domain 6: Cost & Performance Optimisation
**Scenario:** Queries on a wide Delta table filter on `device_id`, which is column 45 of 60. The query profile shows almost no files skipped, even though the table is compacted.

A. Set `delta.dataSkippingStatsColumns` to include `device_id`, then recompute the statistics
B. Partition the table by `device_id` so that each value gets its own directory of files
C. Run `ANALYZE TABLE ... COMPUTE STATISTICS` daily to give the optimizer fresher stats
D. Add a larger cluster with more memory so that the full scan finishes in less time

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** By default Delta collects min and max file statistics only on the first 32 columns, so a filter on column 45 has nothing to skip with. Naming the column in `delta.dataSkippingStatsColumns` (or reordering columns) fixes that, and liquid clustering on the column improves skipping further. B is the high-cardinality partitioning trap. C feeds the cost-based optimizer for join planning but does not add the per-file statistics that data skipping uses. D pays more to keep doing a full scan.
</details>

---

### Question 26 - Domain 7: Ensuring Data Security and Compliance
**Scenario:** A `customers` table holds national ID numbers. Only the compliance group may see full values; everyone else must see a masked value, even if they query the table directly rather than a view.

A. Create a dynamic view that masks the column and tell analysts to query only the view
B. Encrypt the column in the source application and give the key to every analyst who asks
C. Apply a Unity Catalog column mask function to the column that checks group membership
D. Move the column into a separate table and grant it only to the compliance group

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** A column mask is a SQL function attached to the column itself, so every query against the table returns the masked value unless the function lets the caller see it, for example with `is_account_group_member('compliance')`. A protects only users who go through the view; anyone with `SELECT` on the base table sees raw values. B hands the key out, which defeats the control. D works, but it breaks every existing query and join, and it is harder to maintain than a mask on the original column.
</details>

---

### Question 27 - Domain 8: Data Governance
**Scenario:** Data scientists read raw PDFs and CSV landing files straight from a cloud storage path using personal cloud credentials. Security wants these files governed, audited, and granted like tables.

A. Keep the cloud credentials, but rotate them every 90 days and log who received each one
B. Load every PDF into a Delta table as a binary column, and grant `SELECT` on that table
C. Store the files in the workspace file system, and share the folder with each data scientist
D. Create a Unity Catalog volume over the path and grant `READ VOLUME` to the right groups

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Volumes bring non-tabular files under Unity Catalog, so access uses the same grant model, audit logs, and lineage as tables, and nobody needs personal cloud credentials. A still gives individuals direct storage access that bypasses governance. B can work for some cases, but it is a heavy rewrite of every file just to get access control. C uses workspace-level sharing, which is not governed or audited across workspaces.
</details>

---

### Question 28 - Domain 9: Debugging and Deploying
**Scenario:** Two jobs run `MERGE` into the same Delta table at the same time, each for a different region. One of them often fails with `ConcurrentAppendException`.

A. Schedule the two jobs at least an hour apart so that their writes can never overlap
B. Make the regions explicit in each `MERGE` condition, or enable row-level concurrency
C. Increase the retry count on both jobs until the failing job eventually succeeds
D. Turn off the Delta transaction log checks so that both writers can commit freely

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** The conflict occurs because each `MERGE` reads data the other one might have changed. Adding the region to the match condition shows Delta the two writes are disjoint, and row-level concurrency (with deletion vectors and liquid clustering) detects conflicts per row instead of per file. A removes the concurrency that the design needs and breaks when a run is late. C hides the conflict and wastes compute on failed attempts. D does not exist as a safe option; the checks are what keep the table consistent.
</details>

---

### Question 29 - Domain 9: Debugging and Deploying
**Scenario:** A production job broke overnight because the engineer who created it left the company and their account was deactivated. The job ran as that engineer's identity.

A. Set the job to run as a service principal with only the grants the job needs
B. Reassign the job to another engineer so that it runs under that person's account
C. Re-activate the departed engineer's account and keep it only for running jobs
D. Give the job an admin personal access token that is stored in the notebook code

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** A service principal is a non-human identity that does not leave when people do, and granting it only what the job needs keeps least privilege. Deploying through Asset Bundles can set this run-as identity in code. B repeats the same failure the next time that person leaves. C keeps a former employee's credentials alive, which is an audit finding. D puts an admin secret in source code, which is a serious exposure.
</details>

---

### Question 30 - Domain 10: Data Modelling
**Scenario:** A Gold star schema needs a surrogate key for `dim_customer`. The key must stay unique across many incremental loads, and the team wants no custom key-tracking logic.

A. Use `monotonically_increasing_id()` in each load to assign a new key to every inserted row
B. Use `row_number()` over the whole dataset in each load and use that number as the key
C. Declare the key as `BIGINT GENERATED ALWAYS AS IDENTITY` when creating the table
D. Use the current timestamp in milliseconds at insert time as the key for each new row

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** An identity column has Delta assign unique values across every write, with no tracking code. The values are not guaranteed to be consecutive, which a surrogate key does not need. A is unique only within one DataFrame run, so keys can collide between loads. B restarts at 1 on each load and forces the data into one partition to number it. D collides whenever two rows are inserted in the same millisecond, which a batch insert does all the time.
</details>

---

## Where to go deeper

- [Data Engineer Professional cert page](../../exams/databricks/data-engineer-professional/) - notes, practice plan, strategy
- [Data Engineer Associate practice questions](./databricks-data-engineer-associate.md) - the prerequisite level
- [DP-700 practice questions](./azure-fabric-data-engineer-dp-700.md) - the Fabric counterpart
- [Databases topic index](../../topics/databases.md) - lakehouse in context
- **[📖 Databricks certification](https://www.databricks.com/learn/certification)** - official exam guides
