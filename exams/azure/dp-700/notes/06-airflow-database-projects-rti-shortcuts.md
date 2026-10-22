---
last-updated: 2026-10-22
applies-to: DP-700 skills measured as of 2026-10-19
difficulty: intermediate
reading-time: 20 min
---

# Airflow, Database Projects, OneLake Security and Real-Time Shortcuts

> **Why this note exists.** Notes 01-05 cover most of the DP-700 outline. A 2026-10-22 check against the skills measured as of 2026-10-19 found objectives they did not teach: the four workspace settings (Spark, domain, OneLake, Apache Airflow), database projects, OneLake security as a data-plane model, native tables vs OneLake shortcuts in Real-Time Intelligence, and query acceleration for shortcuts. This note covers those. Source of the skills list: the **[📖 DP-700 study guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/dp-700)**.

## Table of Contents

1. [Apache Airflow jobs and their workspace settings](#apache-airflow-jobs-and-their-workspace-settings)
2. [Database projects for the warehouse](#database-projects-for-the-warehouse)
3. [OneLake security: control plane vs data plane](#onelake-security-control-plane-vs-data-plane)
4. [Native tables vs OneLake shortcuts in Real-Time Intelligence](#native-tables-vs-onelake-shortcuts-in-real-time-intelligence)
5. [Query acceleration for OneLake shortcuts](#query-acceleration-for-onelake-shortcuts)
6. [Worked scenarios](#worked-scenarios)
7. [How the exam tests this](#how-the-exam-tests-this)

---

## Apache Airflow jobs and their workspace settings

Fabric gives you three orchestrators. **Pipelines** are low-code activity graphs. **Notebooks** can call other notebooks (`notebookutils.notebook.run`, or `runMultiple` for a DAG of notebooks). **Apache Airflow jobs** run Python-defined DAGs on a managed Airflow runtime - the successor to Azure Data Factory's Workflow Orchestration Manager. Choose Airflow when the team already writes Airflow DAGs, needs Airflow's operator ecosystem, or wants orchestration as code in Git; choose pipelines when you want no-code orchestration and Fabric-native activities.

The workspace-settings objective is about **where those DAGs run**. Under workspace settings, **Data Factory > Apache Airflow runtime settings**, you pick the default pool for Airflow jobs in the workspace:

| | Starter pool (default) | Custom pool |
|---|---|---|
| Size | Fixed: large compute node | Configurable: node size (small or large), extra nodes, autoscale |
| Start | Instant | Created stopped; resume takes up to about 5 minutes |
| Idle behaviour | Shuts down after 20 minutes of inactivity | Always on until manually paused |
| Fits | Development | Production |

Each **extra node** adds capacity for three more workers, so more DAGs can run concurrently; **autoscale** lets the pool add and remove nodes with load. If the workspace setting that allows item-level compute customization is off, every Airflow job uses the starter pool.

The trade-off mirrors Spark pools: the starter pool costs nothing while idle but a DAG scheduled after a quiet period waits for it; the custom pool is ready but billed while on. A production DAG that must start on the minute belongs on a custom pool. Private networks and virtual networks are not currently supported for Airflow jobs, which can rule them out for locked-down estates.

**[📖 Apache Airflow jobs](https://learn.microsoft.com/en-us/fabric/data-factory/apache-airflow-jobs-concepts)** - when to use them, features vs ADF
**[📖 Apache Airflow job workspace settings](https://learn.microsoft.com/en-us/fabric/data-factory/apache-airflow-jobs-workspace-settings)** - starter vs custom pools

### The other three workspace settings

The same objective names Spark, domain and OneLake workspace settings. Each answers a different question.

**Spark settings** (workspace settings, Data Engineering/Science) decide how notebooks and Spark job definitions get compute. The **starter pool** is prehydrated medium-size nodes, so sessions start in seconds; a **custom pool** lets you choose node size, autoscale, dynamic executor allocation and even single-node clusters, and requires the capacity admin to allow customized workspace pools. A **default environment** pins the Spark runtime version, libraries and Spark properties for the workspace. The **customize compute configuration for items** toggle decides whether individual notebooks may override driver and executor sizes. **High concurrency** mode lets several notebooks (or pipeline notebook activities) share one Spark session, cutting start-up time and cost. Job settings include optimistic job admission (the default) versus reserving maximum cores, the interactive session timeout (20 minutes by default) and a maximum job lifetime that kills runaway jobs.

**Domain settings** assign the workspace to a governance **domain** (Finance, Sales...). A workspace admin who is a domain contributor does this in the workspace's own settings. Domains group data for discovery and delegated governance; assigning one does not change who can see or access items - that is still workspace roles and item permissions.

**OneLake settings** include **shortcut caching**: for S3, S3-compatible, Google Cloud Storage and on-premises gateway shortcuts, OneLake can cache files read through the shortcut for 1-28 days (reset on each access; files over 1 GB are not cached) to cut cross-cloud egress cost and latency. You can reset the cache from the same page.

**[📖 Spark workspace administration settings](https://learn.microsoft.com/en-us/fabric/data-engineering/workspace-admin-settings)** - pools, environment, jobs, high concurrency
**[📖 Domains](https://learn.microsoft.com/en-us/fabric/governance/domains)** - domain roles and workspace assignment
**[📖 OneLake shortcuts: caching](https://learn.microsoft.com/en-us/fabric/onelake/onelake-shortcuts)** - shortcut cache workspace setting

---

## Database projects for the warehouse

Git integration (note 04) versions the warehouse as an item from the Fabric portal. **Database projects** take the warehouse into a developer's IDE. In Visual Studio Code with the SQL Database Projects extension (or SSMS for interactive work), the warehouse schema becomes an SDK-style project: one `.sql` file per table, view, function and procedure. The project builds with **DacFx** into a `.dacpac`, which you can compare with a live warehouse, script as a change, or publish to another environment.

Why it matters on the exam: database projects are the answer when the requirement is **schema as code** - build-time validation that a view still compiles after a column rename, a schema compare between test and production, or a CI pipeline in Azure DevOps or GitHub that publishes the warehouse. Deployment pipelines then promote between workspaces, and Git integration keeps the portal and the repository in step.

| Need | Tool |
|---|---|
| Version history and branching from the portal | Git integration |
| Promote content Dev → Test → Prod between workspaces | Deployment pipelines |
| Schema as code, schema compare, build validation, .dacpac publish from CI | Database projects |

**[📖 Warehouse development and deployment](https://learn.microsoft.com/en-us/fabric/data-warehouse/source-control)** - portal, Git, IDE and CI/CD options (preview)

---

## OneLake security: control plane vs data plane

The security objective lists many controls; OneLake security is the one that changes the model. Fabric enforces two planes:

- **Control plane** - workspace roles (Admin, Member, Contributor, Viewer) and item permissions govern what you can *do to* an item: create, configure, share, delete.
- **Data plane** - **OneLake security roles**, defined on an item (such as a lakehouse), govern what *data* you can read, scoped down to folders, tables, schemas, rows and columns.

The subtlety: Admins, Members and Contributors already have data access through their role. OneLake security roles mainly restrict **Viewers and users with Read permission**. So "analysts in the Viewer role should see only the `sales` schema of the gold lakehouse, from Spark as well as SQL" is a OneLake security role; giving them Contributor would bypass it. Because the rules live in OneLake rather than in one engine, they apply consistently across engines that honour OneLake security, unlike SQL-only row-level security on the SQL analytics endpoint.

Shortcuts carry security too: OneLake folder security applies to shortcuts based on the roles defined in the lakehouse where the data is actually stored, so a shortcut does not become a way around the source's rules.

**[📖 Data security in OneLake](https://learn.microsoft.com/en-us/fabric/onelake/security/get-started-security)** - planes, roles, shortcuts, audit, encryption

---

## Native tables vs OneLake shortcuts in Real-Time Intelligence

An eventhouse (KQL database) can hold data two ways:

- **Native tables** - data is ingested into the eventhouse. It is indexed, compressed and cached (hot cache on SSD per the caching policy), so KQL queries are fast. Update policies and materialized views work on native tables, which is how you build streaming transformations inside the eventhouse.
- **OneLake shortcuts** - a reference from the KQL database to Delta or Parquet data elsewhere (a lakehouse, a warehouse, ADLS, S3, Google Cloud Storage). Nothing is copied; the shortcut appears under Shortcuts and is queried with `external_table("name")`. Queries are slower because data is fetched from storage on demand without the eventhouse's indexes, and shortcuts behave like external tables: **no materialized views and no update policies**.

How to choose:

| Requirement | Choose |
|---|---|
| Sub-second queries on high-volume streaming telemetry | Native table (ingest via Eventstream) |
| Update policies or materialized views over the data | Native table |
| Occasional joins to data that already lives in a lakehouse, without duplicating it | Shortcut |
| Data owned and maintained by another team or cloud | Shortcut |
| Shortcut data queried often and needing near-native speed | Shortcut with query acceleration |

```kusto
// Join live telemetry (native) with a device dimension that lives in a lakehouse (shortcut)
Telemetry
| where Timestamp > ago(1h)
| join kind=inner (external_table("DimDevice")) on DeviceId
| summarize avg(Temperature) by Site
```

**[📖 OneLake shortcuts in a KQL database](https://learn.microsoft.com/en-us/fabric/real-time-intelligence/onelake-shortcuts)** - creating and querying shortcuts

---

## Query acceleration for OneLake shortcuts

Query acceleration closes most of the gap between the two options. You set a **query acceleration policy** on a shortcut (or external Delta table) with a number of days to cache (the `Hot` property). The eventhouse then indexes and caches that window of the shortcut's data as it lands in OneLake, existing data and new updates alike, giving performance comparable to ingested data - without an ingestion pipeline and without a duplicate copy to keep in sync.

What it does not change: the table is still an external table, so materialized views and update policies are still unsupported. And it is not free: accelerated data is billed on the OneLake premium cache meter like native eventhouse data, and indexing consumes capacity units. Size the hot window to what is queried often.

Operational limits worth knowing: up to 900 columns; performance may degrade beyond about 2.5 million data files; Parquet files over 6 GB compressed are not cached; changing Delta advanced features (column mapping, partitioning) requires disabling the policy first; schema changes need a matching `.alter external delta table` and may restart acceleration from scratch. Progress is visible with `.show external table operations query_acceleration statistics`.

The DP-700 objective is literally "choose between query acceleration for OneLake shortcuts and standard OneLake shortcuts". Standard shortcut: rarely queried, cost-sensitive, latency-tolerant. Accelerated: frequently queried, needs eventhouse-like speed, and you accept cache and indexing cost.

**[📖 Query acceleration for OneLake shortcuts](https://learn.microsoft.com/en-us/fabric/real-time-intelligence/query-acceleration-overview)** - scenarios, behaviour, limits, billing

---

## Worked scenarios

### Scenario 1: Nightly DAG starts late

**Situation:** An Airflow DAG scheduled for 02:00 regularly starts several minutes late. The workspace uses default Airflow settings.

**Answer:** The starter pool shuts down after 20 minutes idle and needs time to resume. Create a custom pool (always on until paused), size nodes for the DAG load, and set it as the workspace default for production.

### Scenario 2: Warehouse schema drift between environments

**Situation:** Views in production fail after a column rename that passed in development, because nobody compared schemas before deployment.

**Answer:** Manage the warehouse as a database project, build it in CI (a broken view fails the build), use schema compare against production, and publish the .dacpac through the release pipeline.

### Scenario 3: Telemetry enrichment from a lakehouse dimension

**Situation:** Real-time dashboards join streaming telemetry in an eventhouse with a device table maintained in a lakehouse. Joins are slow; the device table changes daily.

**Answer:** Keep telemetry in native tables, create a OneLake shortcut to the device table, and enable query acceleration on it with a short hot window. No duplicate pipeline, near-native join performance.

### Scenario 4: Viewers must see only part of a lakehouse

**Situation:** Business analysts with the Viewer role should read only the `finance` schema of a lakehouse from both notebooks and SQL.

**Answer:** A OneLake security role on the lakehouse scoped to that schema, with the analysts as members. SQL-endpoint RLS alone would not cover Spark access, and a Contributor role would grant everything.

---

## How the exam tests this

- **Orchestrator choice.** Code-first DAGs and Airflow operators: Apache Airflow job. No-code: pipeline. Notebook chains: `runMultiple`.
- **Airflow pools.** Starter = instant, shuts down after 20 idle minutes, development. Custom = configurable, always on, production; extra nodes add 3 workers each.
- **Lifecycle tool.** Git for history, deployment pipelines for promotion, database projects for schema as code and .dacpac.
- **OneLake security.** Data-plane roles restrict Viewers and Read users; Admin/Member/Contributor already have data access.
- **RTI storage.** Native tables for speed and update policies or materialized views; shortcuts for zero-copy access; query acceleration when shortcuts must be fast, with cache cost and external-table limits.

---

**Related notes:** [01 - Fabric architecture and workspaces](01-fabric-architecture-and-workspaces.md) · [02 - Ingestion, pipelines and Eventstream](02-ingestion-pipelines-eventstream.md) · [04 - Security, governance and deployment](04-security-governance-deployment.md) · [05 - Monitoring and optimization](05-monitoring-optimization.md)
