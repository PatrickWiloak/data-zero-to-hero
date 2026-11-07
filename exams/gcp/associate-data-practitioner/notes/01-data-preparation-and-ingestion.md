---
last-updated: 2026-11-07
applies-to: Google Cloud Associate Data Practitioner exam guide v1.0 (read 2026-11-07)
difficulty: beginner
reading-time: 30 min
---

# Section 1: Data Preparation and Ingestion

About **30%** of the exam, the largest section, so roughly 15 to 18 of the 50 to 60 questions. It has two objectives in the official guide: 1.1 prepare and process data, and 1.2 extract and load data into the right Google Cloud storage system. In plain words: decide where the data should be cleaned, get it to Google Cloud with the right tool, and put it in the right store, in the right place on the map.

## Table of Contents

1. [1.1 Prepare and process data](#11-prepare-and-process-data)
2. [1.2 Extract and load data](#12-extract-and-load-data)
3. [How the exam tests this section](#how-the-exam-tests-this-section)
4. [Key terms](#key-terms)

---

## 1.1 Prepare and process data

### ETL, ELT and ETLT

Every pipeline does three jobs: **extract** data from a source, **transform** it (clean, reshape, join), and **load** it somewhere people can use it. The three letters differ only in the order, and the order decides where the heavy computing happens.

In **ETL** you transform before loading, on a separate processing engine. The warehouse only ever receives finished data. This was the default when warehouses were expensive and could not do much heavy work. In **ELT** you load the raw data first and transform it inside the warehouse with SQL. BigQuery separates storage from compute and scales its compute for you, so ELT is the natural style on Google Cloud: land the raw data, keep it, and build clean tables with SQL on top. **ETLT** is the hybrid: a light transform on the way in (drop obviously bad rows, mask a sensitive field, fix types) and the business logic afterwards in the warehouse.

A practical rule for the exam: if the question says the data is already going to BigQuery and the transformation is expressible in SQL, ELT is the answer. If the data must be cleaned or masked *before* it may be stored (compliance), or the processing is not SQL-shaped (parsing logs, streaming windows), reach for a processing engine first, which is ETL or ETLT.

**[📖 Dataform overview](https://docs.cloud.google.com/dataform/docs/overview)** - Google describes Dataform as handling the transform step of ELT: raw data is extracted and loaded into BigQuery first, then Dataform turns it into organized, tested tables.

### Choosing a data transfer tool

Moving data *to* Google Cloud is a different problem from transforming it. The decision turns on volume, network and whether the transfer repeats.

- **Storage Transfer Service** is the managed, online tool for large transfers. Its docs say it is optimized for transfers of more than 1 TiB. Sources include Amazon S3, Azure Blob Storage, other Cloud Storage buckets, on-premises file systems, HDFS and public URLs; the destination is Cloud Storage. It validates integrity and retries automatically, and it can run on a schedule, which is why it is the answer for "copy this S3 bucket into Cloud Storage every night".
- **Transfer Appliance** is a physical device. You copy data onto it, ship it, and Google uploads it to your bucket. The docs say it fits when it would take more than one week to upload your data over the network (their example: 300 TB would take about nine months at 100 Mbps). Think of it as the truck when the pipe is too thin.
- **gcloud storage** (the command-line tool) is for small, ad hoc copies from a laptop or a server. No setup, no schedule.
- **BigQuery Data Transfer Service** is not for files in general; it loads *into BigQuery* on a schedule from supported sources (next section).

**[📖 Storage Transfer Service overview](https://docs.cloud.google.com/storage-transfer/docs/overview)** - sources, destinations and the 1 TiB guidance
**[📖 Transfer Appliance overview](https://docs.cloud.google.com/transfer-appliance/docs/4.0/overview)** - when the offline appliance beats the network

### Assessing and cleaning data quality

Before you clean, you measure. Typical checks: how many nulls per column, duplicate keys, values outside an expected range, inconsistent formats (`"NY"`, `"New York"`), and wrong types (numbers stored as strings). Profiling queries in BigQuery answer most of these:

```sql
-- Standard SQL profile; runs in BigQuery and in most engines (tested in DuckDB 2.0).
SELECT
  COUNT(*)                                   AS row_count,
  COUNT(DISTINCT customer_id)                AS distinct_ids,
  COUNTIF(email IS NULL)                     AS null_emails,   -- BigQuery name; DuckDB uses COUNT_IF
  MIN(order_total) AS min_total, MAX(order_total) AS max_total
FROM orders;
```

(`COUNTIF` is BigQuery syntax and was **not run**; the same query with `COUNT_IF` ran in DuckDB. Everything else is portable.)

Cleaning tools, and when each fits:

| Tool | Style | Reach for it when |
|---|---|---|
| **BigQuery SQL** | Write SQL | Data is already in BigQuery; dedupe, cast, trim, fill nulls |
| **Dataform** | SQL files with dependencies and assertions | Repeatable ELT with tests and a schedule |
| **Cloud Data Fusion** | Visual, drag and drop | Teams who prefer a no-code builder; many source connectors |
| **Dataflow** | Code (Apache Beam) or Google templates | Streaming, very large scale, or non-SQL parsing |

**[📖 Cloud Data Fusion overview](https://docs.cloud.google.com/data-fusion/docs/concepts/overview)** - a fully managed integration service where you build pipelines visually from sources, transforms and sinks
**[📖 Dataflow overview](https://docs.cloud.google.com/dataflow/docs/overview)** - unified stream and batch processing built on Apache Beam, with autoscaling and ready-made templates

---

## 1.2 Extract and load data

### Know the data format

The format you receive decides what is easy later. BigQuery batch loads accept Avro, CSV, JSON, ORC and Parquet.

- **CSV** is human-readable text with no types. The loader must be told (or guess) the schema, and commas, quotes and newlines inside fields cause pain.
- **JSON** carries nesting and field names but is bulky and loosely typed.
- **Avro** and **Parquet** are binary and carry the schema with the data. Parquet is columnar (good for analytics reads); Avro is row-based (good for streaming and record exchange). Prefer them when you control the format.
- **Structured database tables** (rows and columns in Cloud SQL, Oracle, MySQL) arrive through a migration or transfer tool rather than a file.

A second classification the guide asks for: **structured** data fits a fixed table schema; **semi-structured** data (JSON, Avro) has a flexible, nested shape; **unstructured** data (images, audio, PDFs) has no schema. That classification points to the store, below.

### Choose the extraction tool

| Need | Tool |
|---|---|
| Recurring load from SaaS apps, S3, Redshift, Teradata and more into BigQuery | **BigQuery Data Transfer Service** |
| Move a relational database to Cloud SQL (or other managed databases) with minimal downtime | **Database Migration Service** |
| Stream or batch with transformation, any scale | **Dataflow** |
| Visual pipeline from many connectors | **Cloud Data Fusion** |

**[📖 BigQuery Data Transfer Service](https://docs.cloud.google.com/bigquery/docs/dts-introduction)** - managed, scheduled loads into BigQuery with backfills; supported sources include Amazon S3, Azure Blob Storage, Cloud Storage, Redshift, Snowflake, Teradata, Salesforce and Google Ads. It cannot move data out of BigQuery.

### Choose the storage solution

This is a pattern-matching skill. Match the workload shape to the product:

- **Cloud Storage**: objects (files) of any type; lakes, backups, images, raw landing zone.
- **BigQuery**: analytical SQL over large tables; the warehouse.
- **Cloud SQL**: managed MySQL, PostgreSQL or SQL Server for ordinary transactional apps at moderate scale.
- **Firestore**: serverless document database for app data with flexible JSON-like documents.
- **Bigtable**: wide-column NoSQL for huge volumes with very low-latency key lookups (time series, IoT, telemetry).
- **Spanner**: relational with SQL and strong consistency that scales horizontally, including across regions.

(Bigtable is verified: its docs list time-series and IoT data and low-latency single-key access, and describe a sparse sorted key-value table. The Firestore, Spanner and Cloud SQL one-liners are general Google Cloud knowledge; the product pages fetched were truncated, so they are unverified. The Cloud Storage details below are verified.)

**[📖 Bigtable overview](https://docs.cloud.google.com/bigtable/docs/overview)** - use cases and low-latency key access

### Choose the location type

Cloud Storage buckets live in a **region**, **dual-region** or **multi-region**. Per the docs: a region replicates synchronously across zones in one region and has the lowest price; a dual-region replicates asynchronously across two specific regions, with automatic failover and replication charges on write; a multi-region replicates asynchronously across a large geographic area, is priced below dual-region and above region, and always charges outbound transfer on reads. **Turbo replication** is an optional premium feature on dual-region buckets that replicates within 15 minutes for a short, predictable RPO. Zonal is a separate idea: some services (for example Compute Engine disks) place resources in one zone.

**[📖 Cloud Storage locations](https://docs.cloud.google.com/storage/docs/locations)** - location types, redundancy and when to choose each

Decision hints: lowest cost and compute next to the data means region; disaster recovery with control over which two regions means dual-region; serving users across a continent means multi-region.

### Load the data

- `bq load` and `LOAD DATA` statements run load jobs from Cloud Storage into BigQuery; the docs name the `LOAD DATA` SQL statement as one way to create a load job and describe load jobs as a good fit when source data changes infrequently.
- `gcloud storage cp` copies files into buckets.
- **Client libraries** (Python, Java and others) call the APIs from your own code.
- Storage Transfer Service and BigQuery Data Transfer Service for scheduled and large loads.

**[📖 Loading data into BigQuery](https://docs.cloud.google.com/bigquery/docs/loading-data)** - supported batch formats and load options

---

## How the exam tests this section

- **"Which tool?" by volume and network.** Over 1 TiB and online: Storage Transfer Service. Weeks of upload time: Transfer Appliance. A few files: `gcloud storage`.
- **ELT vs ETL.** SQL-expressible and going to BigQuery: ELT. Must be masked or reshaped before storage: ETL or ETLT.
- **Location type.** Disaster recovery in two chosen regions: dual-region. Cheapest: region.
- **Store for the shape of the data.** Files: Cloud Storage. Analytics: BigQuery. Key lookups at huge scale: Bigtable. Relational app: Cloud SQL.
- **Format.** Self-describing (Avro, Parquet) beats CSV when you must preserve types.

## Key terms

- **ETL** - extract, transform, then load: data is reshaped on a processing engine before it reaches the warehouse.
- **ELT** - extract, load, then transform: raw data lands in the warehouse first and is transformed there, usually with SQL.
- **ETLT** - a hybrid with a light transform before loading and the business logic after loading.
- **Storage Transfer Service** - managed online transfer into Cloud Storage from S3, Azure, on-premises, HDFS and URLs, optimized for more than 1 TiB.
- **Transfer Appliance** - a shipped physical device for moving very large data sets when uploading would take more than a week.
- **BigQuery Data Transfer Service** - managed scheduled loads into BigQuery from supported SaaS, storage and warehouse sources, with backfills.
- **Database Migration Service** - managed migration of relational databases to Google Cloud databases with minimal downtime.
- **Cloud Data Fusion** - a managed visual data integration service built on the open source CDAP project.
- **Dataflow** - fully managed stream and batch processing service that runs Apache Beam pipelines.
- **Parquet** - a binary columnar file format that carries its schema, well suited to analytics reads.
- **Avro** - a binary row-based file format that carries its schema, common for streaming and record exchange.
- **Dual-region** - a Cloud Storage location type that replicates objects asynchronously across two specific regions.
- **Multi-region** - a Cloud Storage location type that replicates across a large geographic area.
- **Turbo replication** - an optional premium dual-region feature that replicates objects between regions within 15 minutes.
