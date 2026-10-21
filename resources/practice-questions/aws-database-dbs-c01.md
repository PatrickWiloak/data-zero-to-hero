---
last-updated: 2026-08-09
difficulty: advanced
---

# AWS Certified Database - Specialty (DBS-C01) - Practice Questions

15 questions for DBS-C01 prep, weighted toward workload-specific database design (26%), deployment and migration (20%), then operations, monitoring and troubleshooting, and security (18% each).

DBS-C01 has been retired by AWS. The material remains useful for database work on AWS and for the database domains of other exams; confirm availability before planning to sit it.

> **Cert page:** [exams/aws/specialty/database-dbs-c01/](../../exams/aws/specialty/database-dbs-c01/)

---

### Question 1
**Scenario:** A workload needs single-digit millisecond key-value reads at any scale, with no server management.

A. Amazon Neptune, a graph database built for traversing highly connected data
B. Amazon DynamoDB, a serverless key-value and document database with consistent single-digit millisecond reads at any scale
C. Amazon Redshift, a columnar data warehouse built for large analytical queries
D. Amazon RDS for PostgreSQL, a managed relational database that scales by instance size and read replicas

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** DynamoDB is the managed key-value and document store with predictable latency independent of table size, provided access is by partition key. Redshift is an analytical warehouse, Neptune is a graph database, and RDS is relational with different scaling characteristics.
</details>

---

### Question 2
**Scenario:** A DynamoDB table's partition key is `orderStatus` with four possible values.

A. Add more capacity, since provisioned throughput is divided evenly across however many partitions the table has
B. This is a hot partition problem: pick a high-cardinality key matching the main access pattern, and use a GSI for status queries
C. Use a sort key only, since the sort key alone determines how items are spread across partitions
D. This is fine, because adaptive capacity redistributes the load from four values across all partitions automatically

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Four values means all traffic concentrates on at most four partitions, so throughput is capped regardless of provisioned capacity. High-cardinality partition keys distribute the load, and a global secondary index serves the low-cardinality query pattern without dictating the base table's layout.
</details>

---

### Question 3
**Scenario:** An Aurora cluster must survive a regional failure with low RPO and a readable secondary region.

A. Aurora Global Database, with typical replication lag under a second and readable secondary clusters
B. Multi-AZ, with a synchronous standby in another AZ of the same region
C. Automated backups, which can be restored to a new cluster in another region after the failure is declared
D. A read replica in the same region as the failed writer

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Global Database uses storage-level replication to secondary regions, giving fast cross-region reads and rapid promotion. Multi-AZ protects against an AZ failure within a region, and backups have an RPO measured in minutes at best.
</details>

---

### Question 4
**Scenario:** A migration must move an Oracle database to Aurora PostgreSQL with minimal downtime.

A. Manual rewriting only, translating every table and stored procedure by hand before a single cutover weekend
B. Snapshot restore of the Oracle database into Aurora PostgreSQL, which converts the engine format automatically
C. Export and import with a dump file, taking the source offline
D. AWS SCT for the schema and code, then DMS with full load plus change data capture for the data

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** SCT converts schema, stored procedures, and application SQL, flagging what needs manual work, and DMS with ongoing replication lets the source stay live until cutover. That CDC phase is what turns a weekend outage into a short switchover.
</details>

---

### Question 5
**Scenario:** A read-heavy RDS workload has repeated identical queries and needs latency reduced.

A. Add indexes only, since the queries scan unindexed columns
B. Increase the instance size only, so queries get more CPU
C. Add read replicas and an in-memory cache such as ElastiCache, with a defined invalidation strategy
D. Switch to Redshift, whose columnar storage returns repeated transactional lookups faster than RDS ever could

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Replicas scale read throughput and a cache removes the repeated work entirely. The part that must be designed rather than assumed is invalidation: lazy loading with TTL is simple, write-through keeps the cache fresh, and choosing wrongly gives you stale data.
</details>

---

### Question 6
**Scenario:** DynamoDB items must expire automatically after 90 days.

A. A lifecycle rule on the table, similar to the rules that expire objects in S3 buckets after a set age
B. Time to Live (TTL) on an attribute holding an epoch timestamp
C. A DynamoDB Streams trigger that deletes each item once it is 90 days old
D. Manual deletion by an administrator

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** TTL deletes expired items in the background without consuming write capacity, and deletions appear in DynamoDB Streams if you need to react. Deletion is not instant at the expiry time, which matters if you were relying on it for access control rather than housekeeping.
</details>

---

### Question 7
**Scenario:** An RDS instance shows high `CPUUtilization` and slow queries.

A. Add a read replica and point the application at it, which offloads reads but does not fix an inefficient query on the primary
B. Immediately scale up to the next instance class, then check whether CPU falls before looking at queries
C. Use Performance Insights to find top SQL by wait event and load, then fix the query or index before resizing
D. Restart the instance to clear the buffer pool and reset connections, then watch whether CPU stays high

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Performance Insights attributes database load to specific SQL and wait types, which usually reveals a missing index or a query pattern rather than genuine capacity exhaustion. Scaling first pays indefinitely for a fixable problem.
</details>

---

### Question 8
**Scenario:** Database credentials must rotate automatically and never appear in application code.

A. Parameter Store SecureString with manual rotation, retrieved by the application at startup
B. AWS Secrets Manager with automatic rotation, or IAM database authentication
C. Environment variables set on the compute resources, updated by redeploying the application after each change
D. A configuration file in the application package, encrypted with a key kept in the same deployment

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Secrets Manager handles scheduled rotation with a Lambda that updates both the secret and the database. IAM database authentication goes further by removing the stored password entirely in favor of short-lived tokens. Parameter Store stores secrets but does not rotate them for you.
</details>

---

### Question 9
**Scenario:** A graph of relationships must be queried with traversals many hops deep.

A. Amazon Neptune with Gremlin or openCypher, which keeps traversal cost proportional to the data touched
B. Redshift, whose columnar engine is built for aggregate queries over large fact tables
C. DynamoDB, which fetches items by key quickly but has no native multi-hop traversal
D. RDS with recursive CTEs, which follow relationships using repeated self-joins in SQL

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Purpose-built graph databases keep traversal cost proportional to the subgraph touched, while relational joins degrade sharply with depth. Recursive CTEs work for shallow cases and become impractical for deep, highly connected traversals.
</details>

---

### Question 10
**Scenario:** DynamoDB provisioned capacity is throttling during unpredictable spikes.

A. Add a GSI, which spreads the throttled requests across a second index
B. Reduce the item size only, because smaller items use fewer capacity units
C. Switch to on-demand capacity mode, or keep provisioned with auto scaling and consider adaptive capacity
D. Raise the table's provisioned capacity once, to the average load seen over the last month, and leave it fixed for the year

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** On-demand absorbs spikes with no capacity planning and costs more per request, which is the right trade for genuinely unpredictable traffic. Auto scaling on provisioned mode reacts on a delay, so it handles trends better than sudden bursts.
</details>

---

### Question 11
**Scenario:** Point-in-time recovery is needed for a DynamoDB table to any second in the last 35 days.

A. Enable point-in-time recovery on the table
B. Global tables, which replicate every write to another region but also replicate accidental deletes
C. On-demand backups, which are discrete snapshots taken when requested and restore only to that moment
D. DynamoDB Streams, which keep a 24-hour ordered log of item changes but cannot restore a whole table

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** PITR gives continuous backups restorable to any second within the retention window, which is the protection against an accidental bulk delete. On-demand backups are discrete snapshots, and global tables replicate errors to every region rather than protecting against them.
</details>

---

### Question 12
**Scenario:** Data at rest and in transit must be encrypted with a customer-managed key.

A. Encryption is automatic and sufficient, because the default AWS owned key already gives you control over the key policy
B. Encrypt backups only, since encrypted snapshots protect the data wherever the database files are stored
C. Enable encryption at rest with a KMS customer managed key at creation, and require TLS
D. Application-level encryption only, which leaves the connection and the storage layer without any encryption of their own

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** For RDS, encryption at rest must be enabled when the instance is created; enabling it later requires a snapshot-and-restore into a new encrypted instance. A customer managed key gives you key policy control and the ability to revoke, which is usually why the requirement exists.
</details>

---

### Question 13
**Scenario:** An Aurora writer fails. What happens to connections?

A. A reader is promoted, the cluster endpoint follows it, and existing connections must reconnect
B. Nothing changes, because the writer's storage is shared and clients keep their sessions on the new instance
C. Data must be restored from the latest automated backup before the cluster accepts any writes again, which takes hours
D. The cluster is lost, because the failed writer holds the only copy of the committed data

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Aurora promotes a replica within roughly 30 seconds and the endpoint follows, but sessions break, so the application needs retry logic. Using the reader endpoint for reads and the writer endpoint for writes, rather than instance endpoints, is what makes failover transparent to routing.
</details>

---

### Question 14
**Scenario:** A time-series workload writes millions of metrics per second and queries recent windows.

A. DocumentDB, a document database for JSON data that is queried by fields rather than time windows
B. Neptune, a graph database for relationship traversals rather than ordered measurements
C. RDS MySQL, a relational engine where huge append-only tables and indexes grow without bound
D. Amazon Timestream (or a purpose-built time-series store)

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Time-series engines separate recent hot data from historical cold storage automatically and provide time-oriented query functions. Forcing this pattern into a general-purpose relational database produces enormous tables and index maintenance costs that grow without bound.
</details>

---

### Question 15
**Scenario:** A DynamoDB query needs a different partition key from the base table.

A. A new table maintained by the application, written alongside the base table with every update
B. A scan with a filter on the alternate attribute, run for each lookup
C. A local secondary index, which can use a different partition key from the base table
D. A global secondary index with the alternate partition key and its own capacity

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** A GSI can use any attributes as its key and has its own capacity. An LSI shares the base table's partition key and only offers an alternate sort key, and it must be created with the table. A filtered scan reads everything and then discards, so it costs the whole table on every query.
</details>

---

## Where to go deeper

- [DBS-C01 cert page](../../exams/aws/specialty/database-dbs-c01/) - notes, practice plan, strategy
- [Solutions Architect Professional practice questions](https://cloud.noblerworks.com/resources/practice-questions/aws-solutions-architect-professional/) - database choice in architecture context
- [SQL vs NoSQL](../../learn/concepts/sql-vs-nosql.md) - the decision in plain English
- [Databases topic index](../../topics/databases.md) - cross-cloud comparisons
- **[📖 AWS Certification](https://aws.amazon.com/certification/)** - official exam guides
