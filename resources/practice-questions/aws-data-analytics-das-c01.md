---
last-updated: 2026-08-09
difficulty: advanced
---

# AWS Certified Data Analytics - Specialty (DAS-C01) - Practice Questions

15 questions for DAS-C01 prep, weighted toward processing (24%) and storage and data management (22%), then collection, analysis and visualization, and security (18% each).

DAS-C01 was retired on 2024-04-08 and replaced by the Data Engineer Associate (DEA-C01); it can no longer be taken. The analytics content is still useful, but several services have since been renamed or retired (Kinesis Data Analytics for SQL is discontinued, Firehose is now Amazon Data Firehose); see the table on the [cert page](../../exams/aws/specialty/data-analytics-das-c01/#services-retired-or-renamed-since-this-exam).

> **Cert page:** [exams/aws/specialty/data-analytics-das-c01/](../../exams/aws/specialty/data-analytics-das-c01/)

---

### Question 1
**Scenario:** Clickstream events arrive at 50,000 records per second and must be available to several independent consumers.

A. SQS, which keeps each record available to many independent consumers until every one of them has deleted it
B. Kinesis Data Streams, where several consumers read the same shards independently
C. Direct writes to S3 from each producer, with consumers polling the bucket for new objects as they arrive
D. A single Lambda

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** A stream retains records for a configurable window and lets many consumers read independently at their own position, which a queue does not: SQS delivers each message to one consumer and then it is gone. Enhanced fan-out gives each consumer its own throughput.
</details>

---

### Question 2
**Scenario:** A Kinesis stream has a hot shard because one partition key dominates.

A. Use a higher-cardinality partition key so writes spread evenly across all shards, then resize the shards
B. Add consumers to the stream, so that more readers drain the hot shard faster and the producers stop being throttled
C. Shrink the records so the hot shard's 1 MB per second limit is hit less, with the same skewed key
D. Increase retention to 7 days so that the hot shard keeps the throttled records until it has room

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Each shard has a fixed write limit, so a skewed key concentrates writes and throttles regardless of total shard count. This is the same hot-partition reasoning as DynamoDB, and the fix is the same: distribute the key.
</details>

---

### Question 3
**Scenario:** Athena queries over a large S3 dataset are slow and expensive.

A. Add more Athena capacity by raising the query concurrency quota, so each query gets more workers to scan the data
B. Query the raw JSON with more workers by splitting the workload into several parallel queries on each prefix
C. Convert to Parquet or ORC, compress, and partition by common filters
D. Move the objects to a faster S3 storage class, such as Intelligent-Tiering, so reads are quicker during scans

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Athena bills by data scanned, so the three levers are all about scanning less: columnar formats read only the needed columns, compression shrinks bytes, and partitioning prunes whole prefixes. Together they routinely cut cost by an order of magnitude.
</details>

---

### Question 4
**Scenario:** A data lake needs table-level and column-level permissions across accounts.

A. S3 bucket policies alone, with one prefix per column group and cross-account principals listed in each policy
B. Lake Formation permissions on Glue Data Catalog resources
C. IAM policies on prefixes, attached to roles in each account and scoped to the keys that hold each column
D. Public read

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Lake Formation adds fine-grained grants at database, table, column, and row level, enforced across Athena, Redshift Spectrum, EMR, and Glue. Bucket policies operate on prefixes, which cannot express "these three columns" without physically splitting the data.
</details>

---

### Question 5
**Scenario:** Redshift queries scan a huge fact table joined to a small dimension.

A. Fact table DISTSTYLE KEY on the join column; dimension DISTSTYLE ALL
B. EVEN distribution for the fact and dimension tables, so every node holds an equal slice and join work is balanced
C. Remove the sort keys so that the fact table's blocks are written in arrival order and scans skip fewer zone maps
D. Increase the node count only, so each node holds a smaller slice of the fact table and scans finish sooner

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Distribution style decides how much data moves between nodes at join time. Co-locating the fact rows by join key and replicating a small dimension to every node removes the redistribution step entirely, which is usually a larger win than adding nodes.
</details>

---

### Question 6
**Scenario:** A Glue ETL job must process only new files since the last run.

A. Delete files after processing, so the next run sees only the new arrivals without any need for job state
B. Enable job bookmarks so Glue tracks processed data
C. Put a timestamp in each filename and have the script compare it with the time of the previous successful run
D. Reprocess everything each night, since Glue deduplicates records already written to the target on its own

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Bookmarks persist state between runs so the job resumes from where it left off. They must be enabled and, when reprocessing is needed, explicitly reset, which is the operational detail that trips people up during backfills.
</details>

---

### Question 7
**Scenario:** Streaming data must land in S3 in Parquet, batched by size or time, with no code.

A. Kinesis Data Streams alone, which writes Parquet objects to S3 once its shards have buffered enough records
B. A Lambda function per record, which converts each event to a Parquet object and writes it to S3 individually
C. Firehose with record format conversion to Parquet from a Glue table schema, plus size and interval buffering hints
D. Direct PUT from each producer to S3, with the producer batching and converting records to Parquet itself

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Firehose is the managed delivery service: it buffers, converts JSON to Parquet using a Glue table schema, compresses, partitions, and retries. Writing per-record from Lambda produces the small-file problem that then wrecks query performance downstream.
</details>

---

### Question 8
**Scenario:** EMR jobs are expensive and run nightly.

A. Transient clusters created per run for the nightly job, with spot task nodes and instance fleets
B. A long-running cluster sized for the peak job, with core and task nodes on-demand and reused for every nightly run
C. Use bigger instances so each nightly job finishes sooner and the billed hours drop
D. Move the job to one EC2 instance running Spark in local mode

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** A cluster that only exists during the job stops paying between runs, and task nodes are the safe place for spot because they hold no HDFS data. Keeping core nodes on-demand protects the data while most of the compute runs at spot pricing.
</details>

---

### Question 9
**Scenario:** A QuickSight dashboard must be fast over a large dataset.

A. Import into SPICE with a scheduled refresh, so queries run in memory
B. Increase the source database size
C. Direct query against the source for every visual, which always shows live data and removes the refresh step
D. Reduce the number of visuals on the page to a few, since the load per visual is what limits dashboard speed

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** SPICE is the in-memory engine that decouples dashboard performance from the source and protects the source from dashboard load. The trade-off is data freshness bounded by the refresh schedule, so direct query remains right when near-real-time is the requirement.
</details>

---

### Question 10
**Scenario:** Small files are accumulating in the data lake and query performance is degrading.

A. Ignore it, since S3 merges small objects automatically in the background once their combined size reaches the block size, so no action is needed
B. Add more partitions on a high-cardinality column, so each query opens a smaller number of objects per partition
C. Compact small files into larger objects on a schedule and tune producer buffering and output partitions, so queries open fewer objects
D. Switch to CSV, since text files are split on newline boundaries and so avoid the open overhead of each file

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Every file carries listing and open overhead, so thousands of tiny objects dominate query time. Fixing it at the source (buffer hints, fewer output partitions) prevents recurrence, and periodic compaction cleans up what already exists. More partitions makes the problem worse.
</details>

---

### Question 11
**Scenario:** Redshift must query data in S3 without loading it.

A. UNLOAD, which exports query results from the cluster to S3 so they can be read there without loading
B. COPY into Redshift, which reads the files in place and leaves them in S3 as external tables, with no data movement
C. A federated query to RDS, which presents the S3 files as tables in the catalog without loading them
D. Redshift Spectrum with external tables in the Glue Data Catalog, reading the S3 files in place (no loading)

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Spectrum reads S3 directly using the catalog for schema, so cold historical data stays in cheap storage while joins with local tables still work. The same partitioning and columnar-format advice applies, because Spectrum also bills by data scanned.
</details>

---

### Question 12
**Scenario:** PII must be masked for analysts but visible to a small compliance team.

A. Dropping the PII columns from the shared table for analysts, then restoring them for compliance on request
B. Application filtering in each dashboard tool, which removes or hides the PII columns for the analysts' sessions
C. Two copies of the data, one with PII removed for analysts and a full one in a restricted prefix for compliance
D. Lake Formation column permissions and data filters, or dynamic masking, enforced in the catalog for every query engine

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Enforcing at the catalog and query layer means it applies to every engine and every tool, which is what makes it a control rather than a convention. Maintaining two copies doubles storage and guarantees they drift.
</details>

---

### Question 13
**Scenario:** A streaming aggregation must compute a five-minute rolling count.

A. A batch job every hour that aggregates the previous hour and publishes five-minute counts
B. A windowed query in a stream processor such as Managed Service for Apache Flink, using tumbling or sliding windows
C. A DynamoDB scan every five minutes, counting the items whose timestamp falls in the latest window
D. Athena on raw data, scheduled every minute with a WHERE clause on the event time of the last five minutes

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Windowed aggregation is the native stream primitive, with tumbling, sliding, and session windows, plus watermarks to handle late-arriving events. Batch approaches put a floor under latency equal to the batch interval.
</details>

---

### Question 14
**Scenario:** A Glue crawler keeps creating new tables instead of updating one.

A. Run it less often, since the crawler splits tables when runs overlap and so needs more time between them
B. That is expected, because each crawler run creates a new table version and registers it under a fresh name
C. Inconsistent schemas or partition layout; fix the layout or set the crawler's grouping behavior
D. Delete the catalog and rebuild it, since crawlers keep stale table definitions that cannot be updated in place

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Crawlers group objects into tables by similarity of schema and path structure, so mixed formats or an inconsistent prefix layout fragment the result. Setting a table-level grouping policy and enforcing a consistent partition layout at write time is the durable fix.
</details>

---

### Question 15
**Scenario:** Analytics data must be retained for 7 years but is rarely queried after 90 days.

A. Tier it: recent data in the warehouse, older data in S3 with lifecycle transitions, queried via Spectrum or Athena
B. Keep everything in Redshift on RA3 nodes, since managed storage costs the same as archive tiers
C. Store everything in S3 Standard and query it with Athena, since Standard is the cheapest class for data this rarely read
D. Delete after 90 days, then restore from the nightly backups if an auditor requests older data within the seven year period

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Matching storage cost to access frequency is the main lever on a long-retention analytics estate. Keeping the query path available through Spectrum or Athena means archived data is still reachable, which is what makes the tiering acceptable to the business.
</details>

---

## Where to go deeper

- [DAS-C01 cert page](../../exams/aws/specialty/data-analytics-das-c01/) - notes, practice plan, strategy
- [Data Engineer Associate practice questions](./aws-data-engineer-associate.md) - the current-generation exam
- [DP-203 practice questions](./azure-data-engineer-dp-203.md) - the Azure counterpart
- [Queues vs streams](../../learn/concepts/queues-vs-streams.md) - question 1 in plain English
- **[📖 AWS Certification](https://aws.amazon.com/certification/)** - official exam guides
