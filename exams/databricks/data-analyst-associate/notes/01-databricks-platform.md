---
last-updated: 2026-11-07
applies-to: Databricks Data Analyst Associate exam guide (August 2026 edition, read 2026-11-07)
difficulty: beginner
reading-time: 20 min
---

# Section 1: Understanding of the Databricks Data Intelligence Platform

Weighted **11%** in the exam guide, about five questions. The guide lists three objectives: describe the core components of the platform; understand catalogs, schemas, managed and external tables, access controls, views, certified tables, metric views and lineage in Catalog Explorer; and describe Databricks Marketplace.

## Table of Contents

1. [The platform in one picture](#the-platform-in-one-picture)
2. [Objects you meet in Catalog Explorer](#objects-you-meet-in-catalog-explorer)
3. [Marketplace](#marketplace)
4. [How the exam tests this section](#how-the-exam-tests-this-section)
5. [Key terms](#key-terms)

---

## The platform in one picture

Think of Databricks as one place where data is stored once, in open table formats, and used by several kinds of work. The exam guide names the pieces an analyst should be able to point at:

- **Delta Lake** is the table format. A Delta table is data files plus a transaction log, which is what gives you reliable updates, history and time travel (Section 4).
- **Unity Catalog** is the governance layer: one place that names every table, controls who can read it, records lineage and audits access.
- **Databricks SQL** is the analyst's workspace: the SQL editor, SQL warehouses, dashboards, alerts and Genie.
- **Lakeflow Declarative Pipelines** and **Lakeflow Jobs** are how engineers build and schedule data pipelines. Analysts mostly consume what they produce, but you should know the names.
- **Mosaic AI** is the umbrella for building and serving machine learning and AI applications. **Lakebase** is the platform's managed Postgres-style database for transactional workloads. The exam only asks you to recognise their roles, so a one-line description of each is enough.
- The **Data Intelligence Engine** is the layer that uses what the platform knows about your data (metadata, lineage, usage) to power AI features such as Genie and assistants.

As an analyst, your daily path is: find a table in Catalog Explorer, query it from a SQL warehouse, build a dashboard or Genie space on it, and let Unity Catalog decide who sees what.

**[📖 AI/BI overview](https://docs.databricks.com/aws/en/ai-bi/)** - AI/BI is Databricks' business intelligence solution built from dashboards, Genie Agents and Unity Catalog semantics.

## Objects you meet in Catalog Explorer

Unity Catalog names data with three levels: **catalog.schema.object**. A **catalog** is the top container (often one per environment or business unit), a **schema** groups related objects, and the object is a table, view, volume, function or model. Per the docs, every governed asset is a *securable object* and you grant permissions on it to users, groups or service principals.

| Object | What it is | Remember |
|---|---|---|
| Managed table | Unity Catalog manages governance and storage | Default and recommended; dropping it deletes the data after a recovery window (7 days by default) |
| External table | Governance only; data lives in storage you manage | Dropping it removes the metadata, not your files |
| View | A saved query, computed every time it is read | No stored data, always current |
| Volume | Governed storage for non-tabular files | Files of any format |
| Metric view | A governed definition of measures and dimensions | Define a metric once, query it by any grouping |

**[📖 Unity Catalog overview](https://docs.databricks.com/aws/en/data-governance/unity-catalog/)** - securable objects, the three-level namespace, managed versus external
**[📖 Managed tables](https://docs.databricks.com/aws/en/tables/managed)** - managed tables are the default, and dropped data is deleted after a recovery period of 7 days by default

**Certified tables and tags.** Teams mark trusted datasets so analysts know what to use. Tags are key-value labels on objects; **governed tags** have enforced allowed values and controlled assignment (shown with a lock icon), while regular tags are free-form. You apply tags on an object's Overview page in Catalog Explorer, and tags on a catalog, schema or table are inherited by the objects below (not by columns).

**[📖 Tags in Unity Catalog](https://docs.databricks.com/aws/en/data-governance/unity-catalog/tags)** - governed tags, regular tags, applying tags in Catalog Explorer

**Metric views** separate the *measure* (for example revenue per distinct customer) from the *dimensions* you group and filter by. Ordinary views lock in grouping at creation, so every new grouping needs another view; a metric view is defined once and queried at runtime by any available field, from SQL, dashboards, Genie and alerts.

**[📖 Metric views](https://docs.databricks.com/aws/en/metric-views/)** - governed business metrics defined once

**Lineage** shows where a table comes from and where it goes, captured automatically down to the column level for queries run on Databricks. Open a table, choose the **Lineage** tab and select **See Lineage Graph**. Use it for impact analysis ("what breaks if I change this column?") and root cause ("where did this number come from?").

**[📖 Data lineage](https://docs.databricks.com/aws/en/data-governance/unity-catalog/data-lineage)** - lineage graph, column lineage and external lineage

## Marketplace

**Databricks Marketplace** is an exchange where providers publish datasets, notebooks, AI models and more that you can connect to from your workspace. It is built on OpenSharing (Delta Sharing). Some listings are available instantly after you accept terms; others need provider approval. Shared data can also be used outside Databricks, for example from Power BI or pandas.

**[📖 Marketplace](https://docs.databricks.com/aws/en/marketplace/)** - assets, consumers and providers

## How the exam tests this section

- **Name the layer.** Governance, lineage and access control are Unity Catalog; tables with history are Delta Lake; the analyst surface is Databricks SQL.
- **Managed or external?** "Dropping the table left the files behind" means external. "Data removed after a recovery window" means managed.
- **Lineage questions** ask where to see it (the table's Lineage tab in Catalog Explorer) and why (impact analysis).
- **Marketplace** is the answer when the story is "get a third-party dataset into the workspace without copying files by hand".

## Key terms

- **Delta Lake** - the open table format of data files plus a transaction log that gives Databricks tables reliability and history.
- **Unity Catalog** - the Databricks governance layer for data and AI, covering access control, lineage and auditing.
- **Catalog** - the top level of the three-level namespace, a container for schemas.
- **Schema** - the middle level of the namespace that groups tables, views, volumes and functions.
- **Securable object** - any governed asset in Unity Catalog that privileges can be granted on.
- **Managed table** - a table whose governance and storage lifecycle Unity Catalog controls.
- **External table** - a table where Unity Catalog governs access but you manage the underlying storage.
- **Metric view** - a Unity Catalog object that defines measures once so they can be queried by any dimension.
- **Governed tag** - an account-level tag with enforced allowed values and controlled assignment.
- **Lineage** - an automatically captured record of the upstream sources and downstream consumers of data, down to columns.
- **Marketplace** - the Databricks exchange for discovering and connecting to shared datasets, notebooks and models.
