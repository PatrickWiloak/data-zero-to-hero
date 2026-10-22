---
last-updated: 2026-10-22
difficulty: intermediate
---

# Microsoft Fabric Data Engineer Associate (DP-700) - Practice Questions

30 questions for DP-700 prep across ingestion, transformation, real-time intelligence, orchestration, security, and operational monitoring in Microsoft Fabric.

DP-700 is the engineering counterpart to DP-600: more pipelines and Spark, less semantic modeling.

> **Cert page:** [exams/azure/dp-700/](../../exams/azure/dp-700/)

---

### Question 1
**Scenario:** You must ingest 500 GB nightly from an on-premises SQL Server into a lakehouse with the least transformation logic.

A. A pipeline Copy activity using an on-premises data gateway
B. A notebook reading over the public internet
C. Manual CSV export
D. Dataflow Gen2 with a Power Query step for every column, which suits shaping more than bulk movement

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Copy activity is the high-throughput movement tool and the on-premises data gateway is how Fabric reaches a private network. Dataflow Gen2 is friendlier for shaping but slower and more expensive at this volume. A notebook cannot reach an on-premises server without the gateway either.
</details>

---

### Question 2
**Scenario:** Which choice best fits transforming data with complex custom logic over hundreds of millions of rows?

A. Dataflow Gen2, which runs Power Query transformations on Fabric compute and handles custom logic at any row count
B. A pipeline expression
C. A Spark notebook in the lakehouse
D. A KQL query

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Spark gives you distributed compute and full PySpark, Scala, or Spark SQL for arbitrary logic at scale. Dataflow Gen2 targets low-code shaping and is not the right tool for heavy custom transformation. Pipeline expressions are for control flow, not data processing.
</details>

---

### Question 3
**Scenario:** Streaming events must be captured continuously and be queryable within seconds.

A. A pipeline on a 15-minute schedule that copies new events into a lakehouse table the analysts can query
B. Eventstream into an eventhouse KQL database
C. A notebook run daily
D. Dataflow Gen2 refresh set to run every few minutes, loading the stream's latest events into a warehouse table

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Eventstream ingests from sources such as Event Hubs, IoT Hub, and custom endpoints and routes to destinations without code, and an eventhouse is built for immediate query over high-velocity data. Everything else in the list is batch and puts a floor under your latency.
</details>

---

### Question 4
**Scenario:** A pipeline must run activity B only when activity A fails.

A. Chain B on the Failure output of A, so that B runs only when A has ended with an error
B. Run them in parallel with no dependency line, so B ignores whether A fails
C. Chain B on the Success output of A, so B starts only after A works
D. A Wait activity set to the expected duration of A, then B

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Pipeline dependencies come in Success, Failure, Completion, and Skipped, and choosing the right one is how you build error handling. Completion is the "either way" case, which is useful for cleanup steps that must always run.
</details>

---

### Question 5
**Scenario:** A Delta table needs to merge daily changes: update existing rows, insert new ones.

A. `MERGE INTO` the Delta table on the business key, updating matching rows and inserting the new ones
B. Overwrite the whole table with the merged result each day
C. Delete the changed rows by key, then insert the new versions in a second statement, inside the same notebook
D. Append everything, then rely on a downstream view to pick the latest row per business key

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** MERGE is the upsert primitive Delta provides and it is atomic, so readers never see a half-applied batch. Overwriting is wasteful and loses history. Delete-then-insert is not atomic and leaves a window where the data is missing.
</details>

---

### Question 6
**Scenario:** You need to reprocess data from three days ago after discovering a transformation bug.

A. Restore from a Fabric item backup taken at the time, which brings back the earlier table state in place
B. Delta time travel: restore the table `VERSION AS OF` or `TIMESTAMP AS OF` the earlier state
C. It is not possible
D. Re-ingest the three days from source after truncating the table, which replays the original extract

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Delta keeps a transaction log with prior versions, so you can read or restore an earlier state directly, subject to the retention window and whether `VACUUM` has removed the files. This is exactly why the retention setting matters before you need it.
</details>

---

### Question 7
**Scenario:** Notebook runs fail intermittently with out-of-memory errors on a wide join.

A. Reduce the number of columns in the output so that each row written after the join carries less data
B. Run at night
C. Raise the pool's node size and broadcast the small side to avoid the shuffle
D. Switch the join to Dataflow Gen2, whose engine spills to disk and so avoids executor memory limits on wide joins

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** A join between a large and a small table is the standard broadcast case: broadcasting the small side avoids the shuffle that causes the memory pressure. Sizing is the other half. Moving to a lower-scale tool would make it worse, and scheduling does not change memory requirements.
</details>

---

### Question 8
**Scenario:** Several workspaces need the same lakehouse table without copying it.

A. Duplicate the pipeline in each workspace so that every team loads its own copy of the table on its own schedule
B. Grant capacity admin to the consuming teams so they can read the source workspace's tables directly in OneLake
C. Export to CSV and share
D. A shortcut to the source table from the consuming workspaces

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Internal shortcuts point one Fabric item at another so there is one physical copy and one refresh. Duplicating pipelines multiplies cost and guarantees the copies drift. Granting broad admin permissions is not a data-sharing mechanism.
</details>

---

### Question 9
**Scenario:** Access to a warehouse must be limited so a group can query only rows for their region.

A. Workspace roles, assigning the group Viewer on the workspace so the engine hides rows outside their region
B. Row-level security with a predicate on the region column
C. Report-level filters on the region column in each Power BI report, locked so that viewers cannot change them
D. Separate warehouses per region, with the data copied into each and the group given the Contributor role on the workspace that holds them all

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** RLS is enforced by the engine on every query path. Workspace roles are coarse and grant or deny whole items. Report filters are cosmetic. Separate warehouses per region multiply overhead, and a workspace-wide role would still expose every region's rows.
</details>

---

### Question 10
**Scenario:** A pipeline should process only rows changed since the last run.

A. Rely on Spark caching to remember which rows were read last time, since cached data persists between pipeline runs and sessions
B. Randomly sample a fixed share of rows each run, then repeat the pipeline until every row has been picked up
C. Incremental load on a modified-date watermark column, persisting the last high-water value for the next run to start from
D. Full reload each time into a staging table, then diff it against the target to find which rows changed

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** A high-watermark on a monotonic column such as a modified timestamp or an identity value is the standard incremental pattern, with the watermark persisted so the next run picks up from it. Watch for late-arriving data, which is why a small overlap window is common practice.
</details>

---

### Question 11
**Scenario:** You need to know why a scheduled pipeline stopped producing output last Tuesday.

A. Monitor hub run history with the failed activity's error detail
B. Guess from the data in the lakehouse, by looking at the newest file timestamps and row counts
C. The Fabric roadmap, which lists known service incidents and the dates when each was fixed
D. The semantic model refresh log only, since a missing output almost always starts with a failed refresh

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** The Monitor hub lists runs across pipelines, notebooks, dataflows, and refreshes with status, duration, and error output per activity. Starting there tells you whether the pipeline failed, was skipped, or succeeded while the source was empty, which are three very different investigations.
</details>

---

### Question 12
**Scenario:** A lakehouse table is queried through the SQL analytics endpoint and a newly written column is missing.

A. The column was not written, because Delta table schemas can only change when the table is recreated
B. The endpoint is read-only and metadata sync can lag; refresh its metadata
C. SQL endpoints do not support new columns, so the table must be recreated with the column in its original schema
D. Recreate the lakehouse, since the endpoint caches the schema at creation time and cannot be refreshed

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** The SQL analytics endpoint reflects the Delta tables through a metadata sync that is not instantaneous. Knowing that the endpoint is read-only and eventually consistent with Spark writes prevents a lot of wasted debugging.
</details>

---

### Question 13
**Scenario:** Choose between a warehouse and a lakehouse for a team whose skills are entirely T-SQL and who need multi-table transactions.

A. Semantic model, which defines relationships and measures and supports T-SQL writes
B. Lakehouse, whose SQL endpoint supports full T-SQL DML and multi-table transactions
C. KQL database, which supports T-SQL DML and multi-table transactions
D. Warehouse, which supports full T-SQL DDL and DML with multi-table transactions and needs no Spark code

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** The warehouse supports full T-SQL DDL and DML with multi-table transactions. The lakehouse's SQL endpoint is read-only, so writes must go through Spark. Matching the item to the team's skills and write pattern is the decision DP-700 tests repeatedly.
</details>

---

### Question 14
**Scenario:** Secrets for an external API must be used inside a notebook without appearing in code.

A. Put them in a markdown cell at the top of the notebook, which is hidden from users who have read access only
B. Hard-code them in the notebook and restrict the workspace to a small group of trusted engineers
C. Store them in Azure Key Vault and fetch at runtime with `mssparkutils.credentials`
D. Pass them as pipeline parameters in plain text, which are masked in the run history once the pipeline succeeds

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Key Vault keeps the secret out of code, out of git, and out of run history, and rotation happens in one place. Anything embedded in a notebook or a parameter value is visible to anyone with read access to the item and often ends up in exported artifacts.
</details>

---

### Question 15
**Scenario:** A capacity is repeatedly throttled by background jobs, causing report timeouts.

A. Ask users to retry, since throttling clears itself once the smoothing window of the background jobs finishes
B. Switch every report to DirectQuery, so that interactive queries stop waiting on the capacity's cached model refreshes
C. Delete old items from the workspaces, since stored items consume capacity units even when no job is running
D. Use the Capacity Metrics app to find the heavy items, then reschedule, optimize, or move them, and add capacity only if the load is justified

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Fabric smooths background operations over time but sustained overuse leads to throttling that affects interactive users. The metrics app attributes consumption per item, which is the only way to decide between optimizing a job, moving it, or buying capacity. DirectQuery would add load rather than remove it.
</details>

---

### Question 16 - Domain 1: Implement and manage an analytics solution
**Scenario:** You promote notebooks from a Dev workspace to Test and Prod with a deployment pipeline. In each stage the notebook must read that stage's own lakehouse, not the Dev one.

A. Edit each notebook by hand after every deployment to point it at the right lakehouse
B. Keep one shared lakehouse for all three stages so the notebooks never need to change
C. Set deployment rules on the Test and Prod stages that swap the notebook's default lakehouse
D. Copy the Dev lakehouse into Test and Prod before each deployment so the names match

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Deployment rules let each stage override settings such as a notebook's default lakehouse, so the same item deploys unchanged and binds to the right data in each stage. A is manual, error-prone, and gets overwritten on the next deploy. B mixes test and production data, which defeats the point of stages. D copies data needlessly and still leaves the notebook pointed at the Dev item.
</details>

---

### Question 17 - Domain 1: Implement and manage an analytics solution
**Scenario:** Support agents query a warehouse `Customers` table. They must see that an email address exists but not the full value. Finance analysts must still see the real emails.

A. Apply dynamic data masking to the email column and grant UNMASK only to the finance role
B. Remove the email column from the table and store it in a separate warehouse for finance
C. Encrypt the email column in the notebook that loads it and share the key with finance
D. Add row-level security so support agents cannot see any rows that contain an email

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Dynamic data masking returns a masked value such as `aXXX@XXXX.com` to users without UNMASK, while the stored data stays intact for those who have it. B splits the data and adds a second item to secure and maintain. C pushes key management into code and breaks normal querying. D hides whole rows, so support would lose the customers they need to help, not just the email value.
</details>

---

### Question 18 - Domain 1: Implement and manage an analytics solution
**Scenario:** Every notebook in a workspace needs the same two Python libraries and the same Spark settings. Today each engineer installs them at the top of their own notebooks and versions drift.

A. Add a `%pip install` cell to each notebook and agree on versions in a team wiki
B. Create an environment with the libraries and Spark properties and set it as the workspace default
C. Upload the library wheel files to the lakehouse Files area and let each notebook import them
D. Ask the capacity admin to raise the node size so the installs finish faster

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** A Fabric environment bundles libraries, Spark properties, and the compute choice in one item, and the workspace default applies it to every notebook and Spark job definition. A keeps the drift problem because each notebook still pins its own versions. C works for one notebook but has no central version control and nothing enforces it. D changes speed, not consistency.
</details>

---

### Question 19 - Domain 1: Implement and manage an analytics solution
**Scenario:** A partner drops files into a OneLake folder at unpredictable times. A pipeline must start as soon as each file arrives, not on a fixed schedule.

A. Schedule the pipeline every minute and exit early when there is no new file
B. Have the partner email the team so someone can start the pipeline by hand
C. Add a Wait activity loop at the start of a long-running pipeline that checks the folder
D. Add an event-based trigger on the file-created event for that folder that runs the pipeline

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Event-based triggers react to storage events such as a file being created, so the pipeline runs once per arrival with no polling. A burns capacity on hundreds of empty runs a day and still adds up to a minute of delay. B depends on a person and does not scale. C keeps a pipeline running for hours just to wait, which wastes capacity and hits run time limits.
</details>

---

### Question 20 - Domain 1: Implement and manage an analytics solution
**Scenario:** Three engineers edit notebooks and pipelines in one shared workspace and keep overwriting each other's changes. The team wants change history and code review before work reaches the main workspace.

A. Connect the workspace to a Git repo and have each engineer work in their own branch and workspace, merging by pull request
B. Use a deployment pipeline between two workspaces, which records every edit with its author
C. Export the items to OneDrive each evening so earlier copies can be recovered
D. Give only one engineer Contributor rights so that only one person edits at a time

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Fabric Git integration with Azure DevOps or GitHub gives branches, history, and pull requests, and a private workspace per branch stops engineers from overwriting each other. B moves content between stages but is not version control and has no review step. C gives backups, not history per change or review. D removes the conflict by removing two thirds of the team's ability to work.
</details>

---

### Question 21 - Domain 2: Ingest and transform data
**Scenario:** Analysts need a near real-time, read-only copy of an Azure SQL Database in Fabric for reporting. The team wants no pipelines or ETL code to maintain.

A. A pipeline Copy activity scheduled every five minutes with a watermark column
B. Database mirroring of the Azure SQL Database into Fabric
C. A notebook that reads the change feed through JDBC every few minutes
D. A Dataflow Gen2 that refreshes the tables on a short schedule

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Mirroring continuously replicates the source into OneLake as Delta tables with no ETL code, and the data is queryable through a SQL analytics endpoint. A and D are scheduled batch loads that need their own logic and still lag by the schedule interval. C is custom code that the team would have to build, schedule, and support.
</details>

---

### Question 22 - Domain 2: Ingest and transform data
**Scenario:** A dashboard must show the count of sensor readings per device for each fixed five-minute block, with no overlap between blocks and every event counted exactly once.

A. A hopping window of five minutes that advances every minute
B. A session window that closes after five minutes without events
C. A tumbling window of five minutes
D. A sliding window that emits a result whenever an event arrives

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Tumbling windows are fixed-size, contiguous, and non-overlapping, so each event falls in exactly one block. A overlaps, so one event is counted in up to five windows. B groups by gaps in activity, so window length varies with traffic. D also overlaps and emits on every event, which gives a running view rather than fixed blocks.
</details>

---

### Question 23 - Domain 2: Ingest and transform data
**Scenario:** A daily source extract sometimes contains the same order several times with different `modified_at` values. Only the latest version of each order should reach the silver table.

A. Use `SELECT DISTINCT` on all columns before loading the silver table
B. Load everything and ask report authors to filter for the newest row
C. Drop any order ID that appears more than once in the extract
D. Rank rows with `ROW_NUMBER()` over the order ID ordered by `modified_at` descending and keep rank 1

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** A window function partitioned by the business key and ordered by the change timestamp keeps exactly one row per key, and the newest one. A removes only rows that are identical in every column, so versions with different timestamps survive. B pushes data quality onto every consumer and guarantees inconsistent numbers. C throws away orders that changed, which is real data loss.
</details>

---

### Question 24 - Domain 2: Ingest and transform data
**Scenario:** You are building a customer dimension for a star schema. When a customer moves, sales before the move must still report against the old region.

A. Slowly changing dimension Type 2: expire the current row and insert a new row with effective dates
B. Slowly changing dimension Type 1: overwrite the region on the existing row
C. Store only the latest region and recompute history from the fact table at query time
D. Add a new column for each region change, such as `region_2` and `region_3`

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Type 2 keeps a new row per version with a surrogate key, valid-from and valid-to dates, and a current flag, so old facts keep pointing at the old version. B rewrites history, so past sales move to the new region. C has nothing to recompute from, because the old region is gone. D is a limited Type 3 pattern that breaks after a few changes and is hard to query.
</details>

---

### Question 25 - Domain 2: Ingest and transform data
**Scenario:** An eventhouse needs to query a large Delta table that already lives in a lakehouse. Queries must be fast, and the data must not be copied into a second ingestion process.

A. Ingest the Delta table into a native eventhouse table with a nightly pipeline
B. Create a OneLake shortcut to the table in the KQL database with query acceleration enabled
C. Export the Delta table to CSV and use an external table over the files
D. Create a standard OneLake shortcut and add more capacity to make it faster

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** An accelerated shortcut keeps the data in place in OneLake while the eventhouse indexes and caches it, so query performance gets close to native tables without a separate ingestion job. A duplicates the data and adds a pipeline to run and monitor. C loses Delta's schema and transaction log and is slower. D works, but a standard shortcut reads the files on every query, and extra capacity does not fix that.
</details>

---

### Question 26 - Domain 3: Monitor and optimize an analytics solution
**Scenario:** A lakehouse table is written by a streaming job every minute. After a few weeks, reads are slow and the table folder holds tens of thousands of small Parquet files.

A. Increase the streaming trigger frequency so each write finishes sooner
B. Convert the table to CSV so the SQL analytics endpoint reads it faster
C. Run `OPTIMIZE` on the table, with V-Order, on a regular schedule to compact the small files
D. Delete the transaction log so the engine stops tracking old file versions

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Frequent small writes cause a small-file problem, and `OPTIMIZE` compacts them into fewer, larger files, while V-Order improves read speed for the SQL engine and Power BI. A makes the problem worse by writing even more small files. B gives up columnar storage, compression, and Delta features. D corrupts the table, because the log is what defines which files make up the current version.
</details>

---

### Question 27 - Domain 3: Monitor and optimize an analytics solution
**Scenario:** The on-call engineer must be notified in Teams within minutes whenever a critical pipeline run fails, without anyone watching the Monitor hub.

A. Check the Monitor hub every morning and post failures in the team channel by hand
B. Add a Teams post at the end of the pipeline that runs only on the Success path
C. Ask the capacity admin to email a weekly usage report from the Capacity Metrics app
D. Create an Activator rule on the pipeline's job events that sends a Teams message when a run fails

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Fabric emits job events for item runs, and an Activator rule can watch for a failed status and send a Teams message or email straight away. A depends on someone remembering and can lag by a full day. B is wired to the wrong outcome, so it fires on success and stays silent on failure. C reports capacity use, not individual run failures, and only weekly.
</details>

---

### Question 28 - Domain 3: Monitor and optimize an analytics solution
**Scenario:** A lakehouse shortcut to an Azure Data Lake Storage Gen2 container worked for months. Since this morning, queries through it fail with an authorization error. The security team rotated service principal secrets overnight.

A. Update the shortcut's cloud connection with the new service principal secret
B. Delete the lakehouse and recreate it with a new shortcut
C. Copy the container's data into OneLake so the shortcut is no longer needed
D. Grant the analysts Owner on the storage account so their own identities are used

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** The shortcut authenticates through its connection, and the timing points straight at the rotated secret, so updating that credential fixes every item that uses the connection. B throws away a working lakehouse for a credential problem. C changes the architecture and duplicates data to avoid a one-line fix. D over-grants access and does not change the identity the shortcut connection uses.
</details>

---

### Question 29 - Domain 3: Monitor and optimize an analytics solution
**Scenario:** Users say a Fabric warehouse "got slow last week". You need to find which queries ran longest and how often they ran, before deciding what to tune.

A. Run the slowest report again and time it with a stopwatch
B. Query the warehouse's `queryinsights` views for historical request duration and frequency
C. Read the Fabric roadmap for known warehouse performance issues
D. Open the pipeline run history in the Monitor hub for the load jobs

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Query insights keeps history of completed requests, including duration and long-running and frequently run queries, so you can rank the real offenders over the period users mention. A measures one query at one moment and tells you nothing about last week. C lists features, not your workload. D shows load jobs, not the read queries users are complaining about.
</details>

---

### Question 30 - Domain 3: Monitor and optimize an analytics solution
**Scenario:** An eventhouse table keeps a year of telemetry. Nearly all dashboard queries look at the last 30 days, but the hot cache is set to 7 days and queries beyond that are slow.

A. Shorten the retention policy to 30 days so the older data is deleted
B. Move the dashboard queries to a lakehouse SQL analytics endpoint
C. Set the table's caching policy so the hot cache covers 30 days
D. Re-ingest the last 30 days every night so the data is always new

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** The caching policy decides how much recent data sits in fast hot storage, and matching it to the query pattern is the direct fix while the full year stays queryable. A deletes eleven months of data that may still be needed for audits or trends. B moves a real-time workload to an engine built for batch analytics. D creates duplicate data and ingestion cost without changing what is cached.
</details>

---

## Where to go deeper

- [DP-700 cert page](../../exams/azure/dp-700/) - notes, practice plan, strategy
- [DP-600 practice questions](./azure-fabric-analytics-dp-600.md) - the analytics sibling
- [DP-203 practice questions](./azure-data-engineer-dp-203.md) - the Synapse-era predecessor skill set
- [Queues vs streams](../../learn/concepts/queues-vs-streams.md) - the streaming concepts underneath
- **[📖 DP-700 study guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/dp-700)** - official skills outline
