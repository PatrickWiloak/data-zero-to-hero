---
last-updated: 2026-11-07
applies-to: Databricks Data Analyst Associate exam guide (August 2026 edition, read 2026-11-07)
difficulty: beginner
reading-time: 15 min
---

# Section 3: Importing Data

Weighted **5%**, about two questions. Two objectives: explain the approaches for bringing data into Databricks (ingestion from S3, data sharing with Delta Sharing, API-driven intake, Auto Loader, Lakeflow Connect managed connectors, Marketplace), and use the workspace UI to upload a data file.

## Table of Contents

1. [Which way in?](#which-way-in)
2. [Uploading a file in the UI](#uploading-a-file-in-the-ui)
3. [How the exam tests this section](#how-the-exam-tests-this-section)
4. [Key terms](#key-terms)

---

## Which way in?

An analyst rarely builds ingestion pipelines, but you must be able to match a situation to a method. Ask two questions: *where is the data*, and *how often does it change*?

| Situation | Method |
|---|---|
| One small file on your laptop | UI upload |
| Files landing continuously in cloud storage such as S3 | **Auto Loader**, or a streaming table over the files |
| Tables in SaaS apps or databases that must stay in sync | **Lakeflow Connect managed connectors** |
| Another organization shares data with you | **Delta Sharing** (called OpenSharing in current docs) |
| A third-party dataset you want to try or buy | **Marketplace** |
| An application pushes records | An **API-driven** load, for example the REST API or SQL Statement API called by your code |
| Query external databases without copying | **Lakehouse Federation** (query in place; read-only) |

The docs describe a spectrum for SQL and Spark ingestion: Structured Streaming is the most customizable, Lakeflow pipelines add declarative orchestration and quality handling, and managed connectors add source-specific authentication, change data capture and retries. The advice is to start with the most managed option and move down only when it does not support your source. For SQL users reading cloud object storage, the docs recommend `CREATE STREAMING TABLE` over `COPY INTO`.

**[📖 Ingest with SQL and Spark APIs](https://docs.databricks.com/aws/en/ingestion/)** - the three layers and Auto Loader through SQL
**[📖 OpenSharing (Delta Sharing)](https://docs.databricks.com/aws/en/delta-sharing/)** - shares, providers and recipients; Databricks-to-Databricks versus open sharing
**[📖 Lakehouse Federation](https://docs.databricks.com/aws/en/query-federation/)** - query external databases in place through connections and foreign catalogs
**[📖 Marketplace](https://docs.databricks.com/aws/en/marketplace/)** - public and private listings

Delta Sharing is *sharing*, not copying: a **share** is a read-only collection of tables, a **provider** shares it and a **recipient** receives it. With Databricks-to-Databricks sharing the recipient has a Unity Catalog workspace and needs no token; with open sharing any user can read it, with a bearer token or OIDC, from tools like pandas, Spark or Power BI.

## Uploading a file in the UI

For quick, small work. In the workspace choose **New** then **Add or upload data**, then **Upload files to a volume**, or use **Add data** in Catalog Explorer. You need `WRITE VOLUME` on the volume plus `USE SCHEMA` and `USE CATALOG`. The UI upload limit is **5 GB**; for larger files use the Databricks SDK for Python. From files in a volume you can click **Create table** to make a **Unity Catalog managed table**: choose new or overwrite, pick catalog and schema, name the table, and optionally override column names and types. You need `CREATE TABLE` on the schema and a running SQL warehouse.

**[📖 Upload files to a volume](https://docs.databricks.com/aws/en/ingestion/file-upload/)** - steps, privileges, the 5 GB limit and Create table

## How the exam tests this section

- **Match the method to the source:** continuous files mean Auto Loader or a streaming table; SaaS and databases mean managed connectors; a partner's data means Delta Sharing; a catalog of third-party data means Marketplace.
- **UI upload facts:** volume permissions, the managed-table result, the size limit.
- **Share versus copy:** Delta Sharing and Federation do not copy data into your workspace.

## Key terms

- **Auto Loader** - the Databricks mechanism that incrementally ingests new files as they arrive in cloud storage.
- **Streaming table** - a Delta table, owned by a pipeline, that incrementally processes append-only data.
- **Lakeflow Connect** - managed connectors that ingest data from SaaS applications and databases with built-in change data capture.
- **Delta Sharing** - the open protocol, called OpenSharing in current docs, for sharing read-only data with recipients inside or outside Databricks.
- **Share** - a read-only collection of tables and other assets that a provider shares with recipients.
- **Lakehouse Federation** - governed, read-only querying of external databases through connections and foreign catalogs.
- **Volume** - Unity Catalog governed storage for files of any format.
