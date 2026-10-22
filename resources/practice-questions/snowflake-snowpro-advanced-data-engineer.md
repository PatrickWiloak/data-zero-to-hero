---
last-updated: 2026-08-09
difficulty: advanced
---

# SnowPro Advanced: Data Engineer (DEA-C02) - Practice Questions

15 questions weighted toward data movement and data pipelines and transformations (25-30% each), then performance and optimization (20-25%) and data governance and security (15-20%).

> **Cert page:** [exams/snowflake/snowpro-advanced-data-engineer/](../../exams/snowflake/snowpro-advanced-data-engineer/)

---

### Question 1
**Scenario:** Files land in cloud storage every few minutes and must be loaded with low latency.

A. A scheduled `COPY INTO` run by a task every hour, which reuses a warehouse that is already running for other work
B. Snowpipe with auto-ingest, triggered by storage event notifications
C. A manual load
D. An external table over the bucket, which Snowflake refreshes automatically so each new file is loaded into storage

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Snowpipe loads within a minute or so of the notification and uses serverless compute, so no warehouse idles waiting. Snowpipe also tracks loaded files for 14 days to avoid duplicates, which a hand-rolled `COPY` loop has to reimplement.
</details>

---

### Question 2
**Scenario:** A pipeline must process only rows changed since the last run.

A. A timestamp filter on an `updated_at` column, which also catches hard deletes at the source
B. Compare full snapshots with a `MINUS` query keyed on the primary key on every run, scheduled by a task
C. Time Travel, to restore the table to its earlier state before each run
D. A stream on the source table, which exposes change records and advances its offset when consumed

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Standard streams show net changes including deletes and updates; append-only streams show inserts only and are cheaper. A stream becomes stale if not consumed within the source table's data retention period, which is the failure mode to monitor for.
</details>

---

### Question 3
**Scenario:** A multi-step transformation must run on a schedule with dependencies between steps.

A. A stored procedure with a loop that calls each step in order, run by one scheduled task so dependencies live in code
B. A materialized view
C. A task DAG, with child tasks defined by `AFTER` on their predecessor
D. External cron on a separate server calling each step through the SQL API, with retry logic written by hand

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Only the root task carries a schedule; children fire on completion of their parent. Gating on stream contents means the DAG skips a run entirely when there is nothing new, which is where serverless task credits are saved.
</details>

---

### Question 4
**Scenario:** A pipeline should be declarative rather than a chain of streams and tasks.

A. A dynamic table, defined by its query with a target lag
B. An external table over the staged files, which Snowflake refreshes on a schedule as new files arrive in the bucket
C. A view
D. A materialized view, which Snowflake maintains automatically but which supports only one table and no joins

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** You declare the result you want and the freshness you need instead of orchestrating the steps. Not every query supports incremental refresh; those that do not fall back to full refresh, which changes the cost profile substantially.
</details>

---

### Question 5
**Scenario:** Semi-structured JSON must be loaded and queried efficiently.

A. Store it as a `VARCHAR` string and call `PARSE_JSON` in every query, since a string keeps the original text exactly
B. Load into a `VARIANT` column and query with path notation, using `LATERAL FLATTEN` for arrays
C. Convert to CSV
D. Parse it in the application first

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Snowflake extracts and stores frequently accessed paths columnar-style behind the scenes, so `VARIANT` is not merely a text blob. Where a path is queried constantly, materializing it into a typed column still helps, and consistently large documents may need flattening at load.
</details>

---

### Question 6
**Scenario:** A `COPY INTO` fails partway through on malformed rows.

A. Set `ON_ERROR` to `CONTINUE`, `SKIP_FILE`, or a threshold, and review rejected rows with `VALIDATION_MODE`
B. Use Snowpipe instead, because a pipe validates files first and never receives malformed rows
C. Loading always aborts on the first bad row whatever the settings, so the only option is rerunning the whole statement
D. Fix the source only

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** The default aborts the whole statement, which is correct when partial loads would corrupt downstream results and wrong when a few bad rows in a large file should not block everything. `VALIDATION_MODE` lets you dry-run the load before committing to it.
</details>

---

### Question 7
**Scenario:** Row-level streaming ingestion is required with sub-second latency.

A. Snowpipe Streaming, writing rows directly through the streaming API without staging files
B. Kafka Connect writing batches of files to an internal stage, loaded by Snowpipe once each file closes on size or time
C. `INSERT` statements per row from the application over a pooled connection, committed one at a time
D. Snowpipe file-based auto-ingest, triggered by storage event notifications whenever a new file lands in the stage

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** File-based Snowpipe latency is bounded by how often files are written. Snowpipe Streaming skips the file entirely, and the Kafka connector can use it directly, which is the standard event-streaming path into Snowflake.
</details>

---

### Question 8
**Scenario:** A transformation is written in Python and must run inside Snowflake.

A. A SQL script only, because Python code cannot run on warehouse compute and must always run outside the platform
B. Snowpark Python: DataFrame operations pushed down to SQL, plus UDFs and stored procedures running in the sandbox on warehouse compute
C. An external function that calls a Python service hosted outside Snowflake through an API gateway for each batch
D. Export the data

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Snowpark keeps the data in place and runs on warehouse compute. Vectorized UDFs that process batches with pandas are substantially faster than scalar UDFs when the operation allows it.
</details>

---

### Question 9
**Scenario:** A merge pattern must apply inserts, updates, and deletes from a change stream.

A. A `MERGE` on the key, with branches chosen by stream metadata columns
B. An `UPDATE` statement joined to the stream, which applies changes to existing rows and also inserts the new ones
C. Truncate the target table and reload it in full from the source on every run
D. Delete the matching target rows, then insert every row from the stream, which handles inserts but loses deletes made at the source

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** `METADATA$ACTION` and `METADATA$ISUPDATE` on the stream tell you which branch applies. Duplicate keys in the source cause a nondeterministic merge error, so deduplicating to one row per key before the merge is part of the pattern.
</details>

---

### Question 10
**Scenario:** A load job's warehouse is oversized and finishes in seconds.

A. Use the largest warehouse available so each file loads faster, because one large file is split across every thread in the cluster
B. Use a multi-cluster warehouse so concurrent loads queue less, since extra clusters speed up a single `COPY` statement
C. Right-size to the file count and size, since loading parallelism comes from the number of files; split huge files or combine tiny ones
D. Keep it for speed

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** A single large file cannot use more threads, so a bigger warehouse buys nothing. Thousands of tiny files waste per-file overhead instead, which is why file sizing is the first thing to check on a slow load.
</details>

---

### Question 11
**Scenario:** Pipeline failures must be detected and alerted on.

A. Check the task graph in Snowsight by hand each morning and rerun any failed task, which catches failures within a day
B. Rely on downstream dashboard users to report missing data, since stale tables are visible as soon as a report is opened
C. Query `TASK_HISTORY`, `COPY_HISTORY`, and `PIPE_USAGE_HISTORY`, and set up alerts
D. A final task in each graph that emails the team when all of its steps complete successfully

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** A task that errors leaves its DAG's children unrun, so a silent failure quietly stops the whole pipeline. Alerts evaluate a condition on a schedule and fire an action, which turns the history views into monitoring rather than forensics.
</details>

---

### Question 12
**Scenario:** Data in an external data lake must be queried without loading it.

A. Load it into native tables first with `COPY INTO`, because Snowflake can only query data it stores in its own format
B. External tables over the staged files, or Iceberg tables to read and optionally write the open table format
C. A secure data share from the lake provider, which exposes the files directly without any Snowflake account on the consumer side
D. Not possible

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** External tables need partition metadata refreshed as files arrive and give weaker performance than native tables. Iceberg tables carry their own metadata and support proper schema evolution, which is why they are the current answer for lake data.
</details>

---

### Question 13
**Scenario:** Sensitive columns must be masked in non-production clones.

A. Separate tables built by hand for development, each hand-edited to hold made-up values and kept in sync with production by a nightly job
B. No clone
C. Masking policies attached through tags, which clones inherit, plus role design so the development role sees masked values
D. Delete the sensitive columns from the clone after it is created, so no masking policy is needed anywhere

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Clones inherit policies, so a cloned environment is not automatically an unprotected copy. The remaining work is making sure the development roles are not the same roles the policy unmasks for.
</details>

---

### Question 14
**Scenario:** A transformation pipeline must be developed and tested safely.

A. A separate account reading live production tables through a data share, so tests run on the production data itself
B. A hand-sampled subset of recent rows in a development schema, quick but not production-sized
C. Test in production
D. Zero-copy clone the database for a development branch, run the pipeline against the clone, then discard the clone when the work is finished

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Cloning gives production-representative data instantly at no immediate storage cost, which a sampled extract cannot. Clone lifecycle matters: an abandoned clone accumulates storage as its data diverges from the source.
</details>

---

### Question 15
**Scenario:** Query costs on a pipeline must be attributed and reduced.

A. Run fewer queries across the board by asking every team to cut their workload, which lowers the credit use of every pipeline equally
B. A smaller warehouse only, applied to every pipeline, since query speed does not depend on warehouse size at all
C. Wait for the monthly invoice, then suspend whichever warehouses look the busiest and see if costs fall next month
D. Attribute cost by warehouse and query tag in `QUERY_HISTORY`, then improve pruning and auto-suspend

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** `QUERY_TAG` set on a session is what makes per-pipeline attribution possible at all, since one warehouse usually serves many jobs. The Query Profile's partitions-scanned versus partitions-total figure is the specific number that tells you whether pruning is working.
</details>

---

## Where to go deeper

- [SnowPro Advanced Data Engineer cert page](../../exams/snowflake/snowpro-advanced-data-engineer/) - notes, practice plan, strategy
- [SnowPro Core practice questions](./snowflake-snowpro-core.md) - the prerequisite
- [SnowPro Advanced Architect practice questions](./snowflake-snowpro-advanced-architect.md) - the design counterpart
- [Queues vs streams](../../learn/concepts/queues-vs-streams.md) - plain-English streaming primer
- **[📖 Snowflake certification](https://www.snowflake.com/certifications/)** - official exam guides
