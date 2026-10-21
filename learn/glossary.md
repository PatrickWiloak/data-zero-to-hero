---
last-updated: 2026-10-21
---

# 📖 Data Glossary

> **Data engineering, analytics and database terms in plain English - from ACID to Z-ordering.**
>
> Use Ctrl+F. This is a reference doc, not something to read end-to-end. Where a concept page explains a term properly, the definition links to it.

Cloud, security and AI terms have their own glossaries on the sibling sites: **[Cloud glossary](https://cloud.noblerworks.com/learn/glossary/)** (compute, networking, DevOps, Kubernetes), **[Security glossary](https://security.noblerworks.com/learn/glossary/)** (identity, encryption, compliance) and **[AI glossary](https://ai.noblerworks.com/docs/GLOSSARY/)** (models, embeddings, LLMs).

Categories:
- [Databases and SQL](#databases-and-sql)
- [Transactions and consistency](#transactions-and-consistency)
- [Distributed data](#distributed-data)
- [Analytics and modeling](#analytics-and-modeling)
- [Lakes, lakehouses and file formats](#lakes-lakehouses-and-file-formats)
- [Pipelines and orchestration](#pipelines-and-orchestration)
- [Streaming](#streaming)
- [Quality, governance and contracts](#quality-governance-and-contracts)
- [Cloud data services](#cloud-data-services)
- [Adjacent terms](#adjacent-terms)

---

## Databases and SQL

<a id="term-anti-join"></a>**Anti-join** - A query that returns rows from one table with no match in another, written with `LEFT JOIN ... WHERE right.key IS NULL` or `NOT EXISTS`. See [SQL joins explained](./concepts/sql-joins-explained.md).

<a id="term-b-tree"></a>**B-tree** - The default index structure in relational databases: a shallow, sorted tree that finds a key in a few page reads and supports range scans. See [Indexes explained](./concepts/indexes-explained.md).

<a id="term-clustered-index"></a>**Clustered index** - An index that is the table: rows are stored in key order. SQL Server and MySQL InnoDB primary keys work this way.

<a id="term-column-store"></a>**Column store** - A storage layout that keeps each column's values together on disk. Fast for scanning and aggregating a few columns; slow for fetching whole rows. See [OLTP vs OLAP](./concepts/oltp-vs-olap.md).

<a id="term-composite-index"></a>**Composite index** - An index on several columns, sorted by the first and then the next. Only usable when the query filters on a leftmost prefix of its columns.

<a id="term-constraint"></a>**Constraint** - A rule the database enforces on every write: primary key, foreign key, unique, `NOT NULL`, `CHECK`.

<a id="term-covering-index"></a>**Covering index** - An index that contains every column a query needs, so the table itself is never read.

<a id="term-cte-common-table-expression"></a>**CTE (Common Table Expression)** - A named subquery declared with `WITH name AS (...)` at the top of a query. Makes long SQL readable.

<a id="term-document-db"></a>**Document DB** - Stores JSON-like documents, each with its own shape. MongoDB, Firestore, Cosmos DB for NoSQL.

<a id="term-explain"></a>**EXPLAIN** - The command that shows the plan a database chose for a query: scans, joins, estimated and actual rows. The first step in fixing a slow query.

<a id="term-fan-out"></a>**Fan-out** - Row multiplication caused by joining to a table that is not unique on the join key. Totals inflate and nothing errors.

<a id="term-foreign-key"></a>**Foreign key** - A column that must match a primary key in another table, which the database can enforce.

<a id="term-full-table-scan"></a>**Full table scan** - Reading every row of a table to answer a query, because no usable index exists or the filter matches most rows.

<a id="term-graph-db"></a>**Graph DB** - Stores nodes and edges, for queries about relationships. Neo4j, Amazon Neptune.

<a id="term-hash-join"></a>**Hash join** - A join algorithm that builds a hash table from the smaller input and streams the larger one through it. The usual choice for big analytical joins.

<a id="term-index"></a>**Index** - A separate data structure, usually a sorted copy of some columns, that lets queries find rows without scanning. Speeds reads, slows writes. See [Indexes explained](./concepts/indexes-explained.md).

<a id="term-join"></a>**Join** - Combining rows from two tables by matching a column. Inner, left, right, full outer and cross joins differ in what they do with unmatched rows. See [SQL joins explained](./concepts/sql-joins-explained.md).

<a id="term-key-value-store"></a>**Key-value store** - The simplest database model: get and put by key. Redis, DynamoDB, etcd.

<a id="term-materialized-view"></a>**Materialized view** - A view whose result is stored and refreshed, so queries read precomputed data.

<a id="term-natural-key"></a>**Natural key** - A key that comes from the business or source system: an email, an order number, a SKU.

<a id="term-normalization"></a>**Normalization** - Designing tables so each fact is stored once (third normal form is the usual target). Good for writes and integrity; needs joins to read.

<a id="term-nosql"></a>**NoSQL** - Any database that is not a traditional relational SQL database: document, key-value, wide-column, graph, time-series. See [SQL vs NoSQL](./concepts/sql-vs-nosql.md).

<a id="term-null"></a>**NULL** - SQL's marker for a missing or unknown value. `NULL = NULL` is not true, which affects joins, filters and `NOT IN`.

<a id="term-orm-object-relational-mapper"></a>**ORM (Object-Relational Mapper)** - A library that maps database rows to objects in application code. SQLAlchemy, Prisma, Hibernate.

<a id="term-partial-index"></a>**Partial index** - An index over only the rows matching a condition, such as `WHERE status = 'failed'`. Small and fast for rare values.

<a id="term-primary-key"></a>**Primary key** - The column or columns that uniquely identify each row.

<a id="term-query-planner-optimizer"></a>**Query planner (optimizer)** - The part of the database that turns SQL into an execution plan, choosing join order, algorithms and indexes from table statistics.

<a id="term-relational-db"></a>**Relational DB** - Tables of rows and columns, related through keys and queried with SQL. PostgreSQL, MySQL, SQL Server, Oracle.

<a id="term-row-store"></a>**Row store** - A storage layout that keeps each row's values together. Fast for reading or writing whole rows; the OLTP default.

<a id="term-schema"></a>**Schema** - The structure of your data: tables, columns, types and constraints. Also used for a namespace of tables inside a database.

<a id="term-selectivity"></a>**Selectivity** - The fraction of rows a filter keeps. Indexes help most when selectivity is low (few rows match).

<a id="term-stored-procedure"></a>**Stored procedure** - Procedural code stored and run inside the database.

<a id="term-surrogate-key"></a>**Surrogate key** - A key generated by the warehouse or database, with no business meaning, such as an integer or a hash. In Type 2 dimensions it identifies one version of a record.

<a id="term-time-series-db"></a>**Time-series DB** - Optimized for timestamped measurements. InfluxDB, TimescaleDB, Amazon Timestream.

<a id="term-view"></a>**View** - A saved query that behaves like a table and is computed when read.

<a id="term-window-function"></a>**Window function** - A function computed over a set of related rows that keeps every row, using `OVER (PARTITION BY ... ORDER BY ...)`. `ROW_NUMBER`, `LAG`, running `SUM`. See [Window functions explained](./concepts/window-functions-explained.md).

<a id="term-wide-column-store"></a>**Wide-column store** - A NoSQL model of rows with flexible, sparse columns grouped into families. Cassandra, Bigtable, HBase.

---

## Transactions and consistency

<a id="term-acid"></a>**ACID** - Atomicity, Consistency, Isolation, Durability. The promises a transactional database makes about each transaction. See [Transactions and ACID](./concepts/transactions-and-acid.md).

<a id="term-atomicity"></a>**Atomicity** - All of a transaction's changes happen, or none do.

<a id="term-base"></a>**BASE** - Basically Available, Soft state, Eventually consistent. The relaxed guarantees of many distributed NoSQL systems.

<a id="term-cap-theorem"></a>**CAP theorem** - During a network partition, a distributed system must choose between consistency (every read sees the latest write) and availability (every request gets an answer). See [Eventual consistency](./concepts/eventual-consistency.md).

<a id="term-deadlock"></a>**Deadlock** - Two transactions each waiting for a lock the other holds. The database aborts one.

<a id="term-dirty-read"></a>**Dirty read** - Reading another transaction's uncommitted change.

<a id="term-durability"></a>**Durability** - Once committed, a change survives crashes.

<a id="term-eventual-consistency"></a>**Eventual consistency** - Reads might be stale right after a write but will catch up if writes stop. See [Eventual consistency](./concepts/eventual-consistency.md).

<a id="term-isolation-level"></a>**Isolation level** - How much concurrent transactions can see of each other: read uncommitted, read committed, repeatable read, serializable.

<a id="term-mvcc-multi-version-concurrency-control"></a>**MVCC (Multi-Version Concurrency Control)** - Keeping several versions of each row so readers see a consistent snapshot and never block writers. Used by PostgreSQL, Oracle, MySQL InnoDB and lakehouse table formats.

<a id="term-optimistic-concurrency"></a>**Optimistic concurrency** - Proceed without locks and check for conflicts at commit, retrying if one occurred.

<a id="term-phantom-read"></a>**Phantom read** - Running the same query twice in one transaction and getting a different set of rows, because another transaction inserted matching rows.

<a id="term-serializable"></a>**Serializable** - The strongest isolation level: concurrent transactions behave as if they ran one at a time.

<a id="term-snapshot-isolation"></a>**Snapshot isolation** - Each transaction reads from a consistent snapshot taken at its start. Prevents most anomalies but allows write skew.

<a id="term-transaction"></a>**Transaction** - A group of operations that succeed or fail together.

<a id="term-transactional-outbox"></a>**Transactional outbox** - Writing an event to an `outbox` table in the same transaction as the business change, so a separate process can publish it reliably.

<a id="term-two-phase-commit-2pc"></a>**Two-phase commit (2PC)** - A protocol for committing one transaction across several systems. Correct but couples their availability.

<a id="term-write-ahead-log-wal"></a>**Write-ahead log (WAL)** - The log a database writes changes to before applying them, used for crash recovery, replication and change data capture.

<a id="term-write-skew"></a>**Write skew** - Two transactions each read the same data, each make a valid decision, and together break a rule.

---

## Distributed data

<a id="term-consistent-hashing"></a>**Consistent hashing** - A way of assigning keys to nodes so that adding or removing a node moves only a small share of keys.

<a id="term-distributed-sql"></a>**Distributed SQL** - Relational databases that shard and replicate automatically while keeping SQL and transactions. Spanner, CockroachDB, YugabyteDB, TiDB.

<a id="term-failover"></a>**Failover** - Promoting a replica to primary when the primary fails.

<a id="term-hot-partition"></a>**Hot partition** - A shard or partition that receives far more traffic than the others, capping throughput.

<a id="term-leader-follower-replication"></a>**Leader-follower replication** - One primary accepts writes and streams its log to read-only replicas. Also called primary-replica.

<a id="term-multi-leader-replication"></a>**Multi-leader replication** - Several nodes accept writes and replicate to each other, needing a conflict resolution rule.

<a id="term-quorum"></a>**Quorum** - The number of replicas that must acknowledge a write or answer a read. With N replicas, W + R > N means reads overlap the latest write.

<a id="term-read-replica"></a>**Read replica** - A read-only copy of a database used to spread reads or to recover from failure.

<a id="term-replica"></a>**Replica** - A copy of a database kept in sync with a primary.

<a id="term-replication-lag"></a>**Replication lag** - How far a replica is behind its primary. The cause of read-your-writes bugs.

<a id="term-shard-key"></a>**Shard key** - The column used to decide which shard a row lives on. Called the partition key in DynamoDB and Cosmos DB.

<a id="term-sharding"></a>**Sharding** - Splitting a dataset across several servers by a key, so each holds a different slice. See [Replication and sharding](./concepts/replication-and-sharding.md).

<a id="term-split-brain"></a>**Split brain** - Two nodes both believing they are primary and accepting conflicting writes.

---

## Analytics and modeling

<a id="term-accumulating-snapshot"></a>**Accumulating snapshot** - A fact table with one row per process instance (an order), updated as it reaches milestones (paid, shipped, delivered).

<a id="term-additive-measure"></a>**Additive measure** - A number that can be summed across every dimension, like revenue. Compare semi-additive and non-additive.

<a id="term-bus-matrix"></a>**Bus matrix** - A grid of business processes against the dimensions they share. The planning document for a dimensional warehouse.

<a id="term-conformed-dimension"></a>**Conformed dimension** - A dimension shared by several fact tables with the same keys and meaning, so their numbers line up.

<a id="term-cube"></a>**Cube** - A pre-aggregated multidimensional structure for fast slicing. Largely replaced by fast columnar warehouses.

<a id="term-data-mart"></a>**Data mart** - A subset of the warehouse built for one business area, such as finance or marketing.

<a id="term-data-warehouse"></a>**Data warehouse** - A database optimized for analytical queries over large, historical datasets. BigQuery, Redshift, Snowflake, Fabric Warehouse.

<a id="term-dimension"></a>**Dimension** - A table describing the context of facts: customer, product, store, date. Grouped and filtered on. See [Data modeling for analytics](./concepts/data-modeling-for-analytics.md).

<a id="term-dimensional-modeling"></a>**Dimensional modeling** - Designing analytical tables as facts and dimensions, the Kimball method.

<a id="term-fact"></a>**Fact** - A table of measurable events, such as order lines or page views, with numbers to add up and keys to dimensions.

<a id="term-grain"></a>**Grain** - What one row of a fact table means. Declared before any column is chosen.

<a id="term-htap-hybrid-transactionalanalytical-processing"></a>**HTAP (Hybrid Transactional/Analytical Processing)** - Systems that serve both OLTP and OLAP workloads, usually by keeping row and column copies in sync.

<a id="term-measure"></a>**Measure** - A numeric column in a fact table: quantity, revenue, duration.

<a id="term-metrics-layer-semantic-layer"></a>**Metrics layer (semantic layer)** - Version-controlled definitions of business metrics that every BI tool reads, so "revenue" means one thing. dbt semantic layer, Cube, LookML.

<a id="term-olap-online-analytical-processing"></a>**OLAP (Online Analytical Processing)** - Large aggregate queries over historical data. Warehouses and lakehouses. See [OLTP vs OLAP](./concepts/oltp-vs-olap.md).

<a id="term-oltp-online-transaction-processing"></a>**OLTP (Online Transaction Processing)** - Many small, fast reads and writes from applications. Operational databases.

<a id="term-one-big-table-obt"></a>**One Big Table (OBT)** - A wide, denormalized table with facts and every descriptive attribute pre-joined, for a specific use case.

<a id="term-periodic-snapshot"></a>**Periodic snapshot** - A fact table with one row per thing per period, such as stock on hand per product per day.

<a id="term-role-playing-dimension"></a>**Role-playing dimension** - One dimension used several times by a fact under different names, like `dim_date` as order date and ship date.

<a id="term-scd-slowly-changing-dimension"></a>**SCD (Slowly Changing Dimension)** - A dimension whose attributes change over time, and the policy for handling it. Type 1 overwrites; Type 2 keeps a row per version. See [Slowly changing dimensions](./concepts/slowly-changing-dimensions.md).

<a id="term-semi-additive-measure"></a>**Semi-additive measure** - A number that can be summed across some dimensions but not time, like account balance or stock on hand.

<a id="term-snowflake-schema"></a>**Snowflake schema** - A star schema whose dimensions are further normalized into sub-dimensions. Not related to the Snowflake warehouse.

<a id="term-star-schema"></a>**Star schema** - One fact table joined directly to several dimension tables. The standard analytical shape.

---

## Lakes, lakehouses and file formats

<a id="term-apache-hudi"></a>**Apache Hudi** - An open table format focused on fast upserts and incremental processing on object storage.

<a id="term-apache-iceberg"></a>**Apache Iceberg** - An open table format with ACID commits, schema and partition evolution, hidden partitioning and time travel. Supported by most engines.

<a id="term-avro"></a>**Avro** - A row-based binary format with an embedded schema, common for streaming and schema registries.

<a id="term-bronze-silver-gold"></a>**Bronze, silver, gold** - The medallion layers: raw data as landed, cleaned and conformed data, and business-ready aggregates.

<a id="term-columnar-storage"></a>**Columnar storage** - Storing data column by column, so analytical queries read only the columns they need and compress well.

<a id="term-compaction"></a>**Compaction** - Rewriting many small files into fewer large ones so queries run faster.

<a id="term-data-lake"></a>**Data lake** - Raw and processed files in object storage, read with a schema applied at query time. See [Warehouses, lakes, and lakehouses](./concepts/warehouses-lakes-lakehouses.md).

<a id="term-data-lakehouse"></a>**Data lakehouse** - Open table formats on object storage that give a lake warehouse features: ACID, schema enforcement, fast SQL.

<a id="term-delta-lake"></a>**Delta Lake** - An open table format with a transaction log, ACID commits and time travel. The default on Databricks.

<a id="term-hive-partitioning"></a>**Hive partitioning** - Directory layout that encodes partition values in folder names, like `dt=2026-10-21/`.

<a id="term-medallion-architecture"></a>**Medallion architecture** - Organizing a lakehouse into bronze, silver and gold layers.

<a id="term-object-storage"></a>**Object storage** - Flat, cheap, durable storage of files addressed by key. Amazon S3, Azure Blob Storage / ADLS Gen2, Google Cloud Storage.

<a id="term-orc"></a>**ORC** - A columnar file format from the Hadoop ecosystem, similar to Parquet.

<a id="term-parquet"></a>**Parquet** - The standard columnar file format for analytics: compressed, typed, with per-block statistics for skipping. See [File formats and partitioning](./concepts/file-formats-and-partitioning.md).

<a id="term-partition-pruning"></a>**Partition pruning** - Skipping whole partitions or files a query cannot need, based on its filter.

<a id="term-schema-on-read"></a>**Schema-on-read** - Applying structure when data is queried rather than when it is written. Typical of lakes.

<a id="term-schema-on-write"></a>**Schema-on-write** - Enforcing structure when data is written. Typical of warehouses.

<a id="term-small-files-problem"></a>**Small files problem** - Thousands of tiny files slowing every query and listing. Fixed by batching writes and compaction.

<a id="term-table-format"></a>**Table format** - A metadata layer that turns a folder of files into a table with transactions and schema. Delta Lake, Iceberg, Hudi.

<a id="term-time-travel"></a>**Time travel** - Querying a table as it was at an earlier version or timestamp.

<a id="term-z-ordering-clustering"></a>**Z-ordering (clustering)** - Physically co-locating related values across several columns so min/max statistics skip more data.

<a id="term-zone-map"></a>**Zone map** - Min and max values stored per block or file, used to skip data that cannot match a filter.

---

## Pipelines and orchestration

<a id="term-backfill"></a>**Backfill** - Running a pipeline over a past date range, to load history or correct data after a fix.

<a id="term-batch-processing"></a>**Batch processing** - Processing a bounded set of data on a schedule, such as everything from yesterday. See [Batch vs streaming](./concepts/batch-vs-streaming.md).

<a id="term-cdc-change-data-capture"></a>**CDC (Change Data Capture)** - Capturing every insert, update and delete from a database, usually by reading its transaction log, and emitting them as events. See [Change data capture](./concepts/change-data-capture.md).

<a id="term-dag-directed-acyclic-graph"></a>**DAG (Directed Acyclic Graph)** - A set of tasks with dependencies and no cycles. How orchestrators and dbt represent a pipeline.

<a id="term-dbt-data-build-tool"></a>**dbt (data build tool)** - A tool for transforming data in the warehouse with version-controlled SQL models, tests and documentation. See [Analytics engineering and dbt](./concepts/analytics-engineering-and-dbt.md).

<a id="term-elt"></a>**ELT** - Extract, Load, Transform: load raw data first and transform inside the warehouse.

<a id="term-etl"></a>**ETL** - Extract, Transform, Load: transform data before loading it into the target. See [ETL vs ELT](./concepts/etl-vs-elt.md).

<a id="term-full-refresh"></a>**Full refresh** - Rebuilding a table from scratch instead of incrementally.

<a id="term-high-water-mark"></a>**High-water mark** - The latest timestamp or ID a pipeline has processed, used to pick up only newer rows next time.

<a id="term-idempotent"></a>**Idempotent** - Running the operation multiple times produces the same result. The property that makes pipeline retries and backfills safe.

<a id="term-incremental-load"></a>**Incremental load** - Processing only new or changed data since the last run.

<a id="term-merge-upsert"></a>**MERGE (upsert)** - A SQL statement that inserts new rows and updates or deletes matching ones in one step.

<a id="term-orchestrator"></a>**Orchestrator** - A tool that schedules and runs pipeline tasks in dependency order with retries. Airflow, Dagster, Prefect, Step Functions. See [Data pipelines and orchestration](./concepts/data-pipelines-and-orchestration.md).

<a id="term-reverse-etl"></a>**Reverse ETL** - Copying modeled data from the warehouse back into operational tools such as a CRM.

<a id="term-seed"></a>**Seed** - In dbt, a small CSV file in the repository loaded as a table.

<a id="term-snapshot"></a>**Snapshot** - In dbt, a model that records changes to a source table over time as Type 2 history. More generally, a copy of data at a point in time.

<a id="term-staging"></a>**Staging** - The first transformation layer: one cleaned, renamed model per source table.

---

## Streaming

<a id="term-allowed-lateness"></a>**Allowed lateness** - How long a stream processor keeps a window's state after it fires, so late events can update the result.

<a id="term-at-least-once"></a>**At-least-once** - Delivery that never loses a message but may deliver it more than once. The common default.

<a id="term-at-most-once"></a>**At-most-once** - Delivery that may lose messages but never duplicates them.

<a id="term-checkpoint"></a>**Checkpoint** - A durable snapshot of a stream job's state and input positions, used to resume after failure.

<a id="term-consumer-group"></a>**Consumer group** - A set of consumers sharing the partitions of a topic. Different groups read the same topic independently.

<a id="term-consumer-lag"></a>**Consumer lag** - How far behind the latest message a consumer is.

<a id="term-dead-letter-queue-dlq"></a>**Dead-letter queue (DLQ)** - Where messages that repeatedly fail processing are sent for inspection.

<a id="term-event-time"></a>**Event time** - When an event happened, as stamped by its source. Compare processing time.

<a id="term-exactly-once"></a>**Exactly-once** - Processing whose observable effect happens exactly once, built from at-least-once delivery plus deduplication or transactions.

<a id="term-kafka"></a>**Kafka** - The open-source distributed log for event streaming: topics split into partitions, retained and replayable. Managed as Amazon MSK, Confluent Cloud, Event Hubs Kafka endpoint.

<a id="term-kappa-architecture"></a>**Kappa architecture** - Streaming-only analytics where reprocessing means replaying the log through a new job version. See [Streaming analytics](../resources/architecture-patterns/streaming-analytics.md).

<a id="term-lambda-architecture"></a>**Lambda architecture** - Analytics with a batch layer for correct results and a speed layer for fresh ones, merged at query time.

<a id="term-offset"></a>**Offset** - A message's position within a partition. Consumers commit offsets to record progress.

<a id="term-partition-stream"></a>**Partition (stream)** - An ordered subset of a topic. Order is guaranteed within a partition, not across them.

<a id="term-processing-time"></a>**Processing time** - When a system processed an event, as opposed to when it happened.

<a id="term-pubsub"></a>**Pub/Sub** - Publish-subscribe messaging, where publishers send to a topic and every subscription gets a copy. Also the name of Google Cloud's managed service.

<a id="term-queue"></a>**Queue** - A buffer that delivers each message to one consumer and removes it once handled. See [Queues vs streams](./concepts/queues-vs-streams.md).

<a id="term-schema-registry"></a>**Schema registry** - A service that stores message schemas and enforces compatibility between versions. Confluent Schema Registry, AWS Glue Schema Registry.

<a id="term-session-window"></a>**Session window** - A window bounded by gaps in activity rather than fixed times.

<a id="term-sliding-hopping-window"></a>**Sliding (hopping) window** - Fixed-length windows that overlap, starting every slide interval.

<a id="term-state-stream"></a>**State (stream)** - Data a stream job keeps between events: open windows, counts, join buffers.

<a id="term-stream-processing"></a>**Stream processing** - Continuously processing unbounded data as it arrives. Flink, Spark Structured Streaming, Dataflow, Kafka Streams.

<a id="term-topic"></a>**Topic** - A named stream of messages in Kafka and similar systems.

<a id="term-tumbling-window"></a>**Tumbling window** - Fixed-size, non-overlapping time windows. Each event belongs to exactly one.

<a id="term-watermark"></a>**Watermark** - A stream processor's estimate that no events older than a given event time will still arrive. Decides when windows close. See [Stream processing: windows and watermarks](./concepts/stream-processing-windows-and-watermarks.md).

---

## Quality, governance and contracts

<a id="term-abac-attribute-based-access-control"></a>**ABAC (Attribute-Based Access Control)** - Access decided by attributes and tags, such as "columns tagged PII are masked unless the user is in a group".

<a id="term-business-glossary"></a>**Business glossary** - Agreed definitions of business terms, kept in a catalog.

<a id="term-classification"></a>**Classification** - Labelling data by sensitivity: public, internal, confidential, PII.

<a id="term-data-catalog"></a>**Data catalog** - A searchable inventory of datasets with owners, descriptions, lineage, classifications and usage. See [Data governance and catalogs](./concepts/data-governance-and-catalogs.md).

<a id="term-data-contract"></a>**Data contract** - A versioned, enforced agreement between a data producer and its consumers covering schema, meaning, quality and freshness. See [Data contracts](./concepts/data-contracts.md).

<a id="term-data-governance"></a>**Data governance** - The decisions and controls over who owns data, who may use it, what it means, and how long it is kept.

<a id="term-data-lineage"></a>**Data lineage** - The record of where a dataset came from and what depends on it, down to columns. See [Data quality and lineage](./concepts/data-quality-and-lineage.md).

<a id="term-data-mesh"></a>**Data mesh** - Decentralizing data ownership to domain teams that publish data products under federated governance. See [Data mesh](../resources/architecture-patterns/data-mesh.md).

<a id="term-data-owner"></a>**Data owner** - The person accountable for a dataset: access decisions and what "correct" means.

<a id="term-data-product"></a>**Data product** - A dataset published deliberately for others, with an owner, documentation, quality guarantees and a contract.

<a id="term-data-quality"></a>**Data quality** - How fit data is for its use: accurate, complete, timely, unique, valid and consistent.

<a id="term-data-steward"></a>**Data steward** - The person who maintains a dataset's definitions and handles quality issues day to day.

<a id="term-dynamic-masking"></a>**Dynamic masking** - Showing a column's value only to authorized users and a masked version to everyone else, at query time.

<a id="term-freshness"></a>**Freshness** - How recently data was updated, usually checked against an agreed limit.

<a id="term-metadata"></a>**Metadata** - Data about data: schemas, owners, descriptions, lineage, statistics.

<a id="term-pii-personally-identifiable-information"></a>**PII (Personally Identifiable Information)** - Data that identifies a person, such as name, email or national ID. Subject to privacy law.

<a id="term-quarantine"></a>**Quarantine** - Setting aside records that fail checks instead of loading them or failing the whole pipeline.

<a id="term-reconciliation"></a>**Reconciliation** - Comparing totals or row sets between a source and a target to prove nothing was lost or duplicated.

<a id="term-retention-policy"></a>**Retention policy** - How long data is kept before deletion or archive.

<a id="term-row-level-security"></a>**Row-level security** - Filtering which rows each user can see in the same table.

<a id="term-schema-evolution"></a>**Schema evolution** - Changing a schema over time in ways existing readers and writers can tolerate.

<a id="term-sla-service-level-agreement"></a>**SLA (Service Level Agreement)** - A promise to consumers, such as "the daily table is complete by 06:00".

---

## Cloud data services

### AWS

<a id="term-aws"></a>**AWS** - Amazon Web Services. The largest public cloud.

<a id="term-amazon-athena"></a>**Amazon Athena** - Serverless SQL over files and tables in S3.

<a id="term-amazon-aurora"></a>**Amazon Aurora** - AWS's MySQL- and PostgreSQL-compatible relational database with a distributed storage layer.

<a id="term-amazon-data-firehose"></a>**Amazon Data Firehose** - Managed delivery of streaming data into S3, Redshift, OpenSearch and others, with buffering and format conversion.

<a id="term-amazon-emr"></a>**Amazon EMR** - Managed Spark, Hive, Flink and other big-data frameworks.

<a id="term-amazon-kinesis-data-streams"></a>**Amazon Kinesis Data Streams** - AWS's managed stream: shards, ordering per partition key, replayable retention.

<a id="term-amazon-msk"></a>**Amazon MSK** - Managed Streaming for Apache Kafka.

<a id="term-amazon-rds"></a>**Amazon RDS** - Managed PostgreSQL, MySQL, MariaDB, SQL Server, Oracle and Db2.

<a id="term-amazon-redshift"></a>**Amazon Redshift** - AWS's columnar data warehouse, provisioned or serverless.

<a id="term-amazon-s3"></a>**Amazon S3** - AWS's object storage. The foundation of most AWS data lakes.

<a id="term-aws-dms-database-migration-service"></a>**AWS DMS (Database Migration Service)** - Migrates databases and runs ongoing CDC replication between them.

<a id="term-aws-glue"></a>**AWS Glue** - Serverless Spark ETL, crawlers, and the Glue Data Catalog of table definitions.

<a id="term-aws-lake-formation"></a>**AWS Lake Formation** - Fine-grained permissions and governance over a data lake on AWS.

<a id="term-dynamodb"></a>**DynamoDB** - AWS's managed key-value and document database. Single-digit-millisecond reads at any scale; partition key design decides performance.

### Azure

<a id="term-azure"></a>**Azure** - Microsoft's public cloud.

<a id="term-adls-gen2-azure-data-lake-storage"></a>**ADLS Gen2 (Azure Data Lake Storage)** - Blob storage with a hierarchical namespace, the base of Azure data lakes.

<a id="term-azure-data-factory"></a>**Azure Data Factory** - Azure's managed data integration and orchestration service.

<a id="term-azure-databricks"></a>**Azure Databricks** - Databricks as a first-party Azure service.

<a id="term-azure-event-hubs"></a>**Azure Event Hubs** - Azure's managed event stream, with a Kafka-compatible endpoint.

<a id="term-azure-sql-database"></a>**Azure SQL Database** - Managed SQL Server engine as a service.

<a id="term-azure-stream-analytics"></a>**Azure Stream Analytics** - Managed SQL-based stream processing.

<a id="term-cosmos-db"></a>**Cosmos DB** - Azure's globally distributed multi-model NoSQL database with five tunable consistency levels.

<a id="term-microsoft-fabric"></a>**Microsoft Fabric** - Microsoft's unified analytics platform: lakehouse, warehouse, pipelines, real-time intelligence and Power BI on OneLake.

<a id="term-microsoft-purview"></a>**Microsoft Purview** - Azure's data catalog, classification, lineage and governance service.

<a id="term-onelake"></a>**OneLake** - The single logical data lake underneath Microsoft Fabric, storing tables as Delta.

<a id="term-power-bi"></a>**Power BI** - Microsoft's BI and reporting tool.

<a id="term-synapse-analytics"></a>**Synapse Analytics** - Azure's earlier warehouse and analytics workspace, now largely succeeded by Fabric.

### Google Cloud

<a id="term-gcp"></a>**GCP** - Google Cloud Platform.

<a id="term-bigquery"></a>**BigQuery** - Google Cloud's serverless columnar data warehouse, billed by storage and by data scanned or reserved capacity.

<a id="term-bigtable"></a>**Bigtable** - Google Cloud's wide-column NoSQL database for very high throughput key lookups.

<a id="term-cloud-sql"></a>**Cloud SQL** - Managed PostgreSQL, MySQL and SQL Server on Google Cloud.

<a id="term-cloud-storage-gcs"></a>**Cloud Storage (GCS)** - Google Cloud's object storage (equivalent to S3 / Blob).

<a id="term-dataflow"></a>**Dataflow** - Managed Apache Beam: one pipeline model for batch and streaming.

<a id="term-dataplex"></a>**Dataplex** - Google Cloud's data catalog, lineage, quality and governance service.

<a id="term-dataproc"></a>**Dataproc** - Managed Spark and Hadoop on Google Cloud.

<a id="term-datastream"></a>**Datastream** - Google Cloud's serverless CDC service into BigQuery and Cloud Storage.

<a id="term-firestore"></a>**Firestore** - Google Cloud's serverless document database.

<a id="term-spanner"></a>**Spanner** - Google Cloud's globally distributed relational database with strong consistency and SQL.

### Data platforms

<a id="term-apache-airflow"></a>**Apache Airflow** - The most widely used open-source orchestrator, defining pipelines as Python DAGs. Managed as Amazon MWAA and Cloud Composer.

<a id="term-apache-flink"></a>**Apache Flink** - An open-source stream processor with event-time windows, state and exactly-once checkpoints.

<a id="term-apache-spark"></a>**Apache Spark** - The open-source distributed engine for large-scale batch and streaming data processing.

<a id="term-databricks"></a>**Databricks** - A lakehouse platform built on Spark and Delta Lake, with Unity Catalog for governance.

<a id="term-duckdb"></a>**DuckDB** - An in-process analytical SQL database. Reads Parquet and CSV directly; ideal for local work.

<a id="term-mongodb"></a>**MongoDB** - The most widely used document database, self-managed or as MongoDB Atlas.

<a id="term-postgresql"></a>**PostgreSQL** - The leading open-source relational database, with a large extension ecosystem.

<a id="term-snowflake"></a>**Snowflake** - A cloud data platform with separated storage and compute, running on AWS, Azure and Google Cloud.

<a id="term-unity-catalog"></a>**Unity Catalog** - Databricks' governance layer for tables, files, models and permissions, also available as open source.

---

## Adjacent terms

Terms that belong mainly to a sibling site but come up constantly in data work.

<a id="term-dns-domain-name-system"></a>**DNS (Domain Name System)** - Translates names (`example.com`) to IP addresses. The original eventually consistent system. More on the [Cloud glossary](https://cloud.noblerworks.com/learn/glossary/).

<a id="term-embedding"></a>**Embedding** - A vector of numbers representing the meaning of text or another item, used for similarity search. More on the [AI glossary](https://ai.noblerworks.com/docs/GLOSSARY/).

<a id="term-encryption-at-rest"></a>**Encryption at rest** - Data encrypted on disk, usually with keys from a key management service. More on the [Security glossary](https://security.noblerworks.com/learn/glossary/).

<a id="term-observability"></a>**Observability** - Understanding what is happening inside a system from its outputs: logs, metrics and traces. Data teams apply it to pipelines as data observability. More on the [Cloud glossary](https://cloud.noblerworks.com/learn/glossary/).

<a id="term-vector-database"></a>**Vector database** - A store for embeddings with approximate nearest-neighbor search. pgvector, Pinecone, Weaviate, Qdrant, Milvus. See [Vector databases for data engineers](./concepts/vector-databases-for-data-engineers.md).

---

## Want a term added?

Open an issue or PR. See [CONTRIBUTING.md](../CONTRIBUTING.md).
