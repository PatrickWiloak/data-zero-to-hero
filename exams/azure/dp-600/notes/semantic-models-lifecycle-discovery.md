---
last-updated: 2026-10-22
applies-to: DP-600 skills measured as of 2026-10-19
difficulty: intermediate
reading-time: 30 min
---

# Semantic Models, Lifecycle and Discovery (DP-600 current outline)

> **Why this note exists.** The older notes in this guide were written against the four-domain DP-600 outline. The current outline (unchanged in substance by the 2026-10-19 update, which only fixed a wording) asks for several skills those notes do not teach: the OneLake catalog and Real-Time hub, Power BI project files, XMLA deployment, calculation groups, field parameters, the large model format, and the two flavours of Direct Lake. This note fills that gap. Source of the skills list: the **[📖 DP-600 study guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/dp-600)**.

## Table of Contents

1. [Maintaining the analytics development lifecycle](#maintaining-the-analytics-development-lifecycle)
2. [Security and governance on analytics items](#security-and-governance-on-analytics-items)
3. [Getting data: OneLake catalog, Real-Time hub, OneLake integration](#getting-data-onelake-catalog-real-time-hub-onelake-integration)
4. [Querying four ways](#querying-four-ways)
5. [Storage modes and Direct Lake](#storage-modes-and-direct-lake)
6. [Modeling features: calculation groups, format strings, field parameters](#modeling-features-calculation-groups-format-strings-field-parameters)
7. [Large models, composite models and incremental refresh](#large-models-composite-models-and-incremental-refresh)
8. [Worked scenarios](#worked-scenarios)
9. [How the exam tests this](#how-the-exam-tests-this)

---

## Maintaining the analytics development lifecycle

An analytics engineer's semantic model is code: it has measures, relationships and security rules that break reports when they change. The lifecycle objective asks you to treat it that way - versioned, reviewed, promoted through environments, and assessed for impact before it ships.

### Version control for a workspace

Fabric workspaces connect to a Git repository in **Azure DevOps or GitHub**. Once connected, each supported item in the workspace is serialized to files in a folder of the repo, and the workspace shows which items differ from the branch. You commit from the workspace to Git, or update the workspace from Git. A common team pattern is one feature workspace per developer, each connected to its own branch, with pull requests merging into the branch connected to a shared development workspace.

**[📖 Git integration](https://learn.microsoft.com/en-us/fabric/cicd/git-integration/intro-to-git-integration)** - supported items, branching, commit and update

### Power BI Desktop projects (.pbip)

A classic `.pbix` file is a binary blob: Git can store it but cannot diff it, and two people cannot merge their changes. Saving as a **Power BI project (.pbip)** writes the report and semantic model as a folder of text files - the model definition in TMDL and the report definition as JSON - so you get readable diffs, code review and merges. The `.pbip` file itself is just a pointer that opens the folder in Desktop.

When the exam says "developers must review each other's measure changes in pull requests", the answer involves .pbip (or Git integration in the service, which uses the same text formats), not .pbix.

**[📖 Power BI Desktop projects](https://learn.microsoft.com/en-us/power-bi/developer/projects/projects-overview)** - folder structure and source control

### Deployment pipelines

Deployment pipelines promote content between workspaces that represent stages - by default **Development, Test and Production**. Deploying copies item definitions forward (not data); **deployment rules** swap stage-specific settings such as a data source connection or a parameter value, so the Test stage points at the test lakehouse without anyone editing the model. Git and deployment pipelines complement each other: Git holds history and review; pipelines move approved content between environments.

**[📖 Deployment pipelines](https://learn.microsoft.com/en-us/fabric/cicd/deployment-pipelines/intro-to-deployment-pipelines)** - stages, rules and what gets deployed

### Impact analysis

Before you rename a lakehouse column or drop a warehouse view, you need to know what depends on it. Fabric's lineage view and **impact analysis** list downstream items - dataflows, semantic models, reports, dashboards - across workspaces, with counts of affected workspaces and viewers, and can notify the owners. The exam phrasing is "perform impact analysis of downstream dependencies from lakehouses, warehouses, dataflows, and semantic models".

### The XMLA endpoint

Semantic models in Fabric (and Premium) capacities expose an **XMLA endpoint**, the same protocol Analysis Services uses. With read-write enabled on the capacity, external tools such as Tabular Editor, SQL Server Management Studio and ALM Toolkit can deploy model changes, script partitions, run refresh commands on individual tables or partitions, and manage roles. It is how enterprise teams deploy models from CI pipelines, and it is the only way to perform some operations, such as refreshing a single partition.

Two connected facts: XMLA writes need the workspace on a Fabric or Premium capacity (Pro workspaces do not support XMLA write operations), and Microsoft recommends enabling the **large semantic model storage format** for any model you write to through XMLA because it improves write performance.

**[📖 Semantic model connectivity with the XMLA endpoint](https://learn.microsoft.com/en-us/fabric/enterprise/powerbi/service-premium-connect-tools)** - enabling read-write and supported tools

### Reusable assets

| Asset | What it contains | Use it to |
|---|---|---|
| `.pbit` (Power BI template) | Report, model, queries and parameters, **no data** | Give analysts a governed starting point that loads their own data |
| `.pbids` (Power BI data source file) | A JSON connection definition, no data | Hand users a one-click connection to an approved source |
| Shared semantic model | One published model used by many reports | Define measures once; avoid copies of the same logic drifting apart |

The shared semantic model is the governance answer to "every report has its own version of revenue". Reports connect live to one endorsed model instead of importing their own.

---

## Security and governance on analytics items

Fabric layers access control from coarse to fine, and exam questions usually turn on picking the narrowest layer that meets the requirement.

| Layer | Grants | Example |
|---|---|---|
| Workspace roles (Admin, Member, Contributor, Viewer) | Everything in the workspace | A team that builds together |
| Item permissions (share an item) | One lakehouse, warehouse or model | A consumer who needs one model, not the workspace |
| Row-level security (RLS) | Rows | Sales reps see only their region |
| Column-level / object-level security (CLS/OLS) | Columns or whole tables | Hide salary columns from most users |
| File-level (OneLake security, folders) | Files and folders in a lakehouse | Restrict a raw zone folder |

**Sensitivity labels** (from Microsoft Purview) classify items and follow data into exports. **Endorsement** marks items as Promoted (owner says it is ready) or Certified (an authorized reviewer says it meets the organization's standard), which surfaces them first in discovery. Endorsement does not grant access; labels do not either.

---

## Getting data: OneLake catalog, Real-Time hub, OneLake integration

### Discovery

The **OneLake catalog** is Fabric's central place to find and understand items across the tenant. Its Explore tab lists items you can access with details, owners, endorsement and lineage; Govern shows governance status and recommendations for data you own; Secure manages access. It is also embedded in Teams and Excel. When a question says "analysts must find certified semantic models before building new ones", the OneLake catalog is the tool.

The **Real-Time hub** is the equivalent for streaming data: a tenant-wide catalog of event streams and KQL tables, plus connectors for sources such as Azure Event Hubs, Kafka and Confluent Cloud, Amazon Kinesis, Google Pub/Sub and database change data capture feeds. Every tenant has one automatically.

**[📖 OneLake catalog](https://learn.microsoft.com/en-us/fabric/governance/onelake-catalog-overview)** - Explore, Govern and Secure
**[📖 Real-Time hub](https://learn.microsoft.com/en-us/fabric/real-time-hub/real-time-hub-overview)** - streams, KQL tables and connectors

### Choosing a data store

| Store | Strength | Query with |
|---|---|---|
| Lakehouse | Files plus Delta tables; Spark-first; schema on read for files | Spark, SQL analytics endpoint (read-only T-SQL) |
| Warehouse | Full T-SQL with multi-table transactions and DML | T-SQL |
| Eventhouse (KQL database) | High-volume time-series and telemetry, near real-time | KQL |

### OneLake integration for Eventhouse and semantic models

Two features copy data *out* of an engine's private storage into OneLake as Delta, so every other engine can read it:

- **Eventhouse OneLake availability.** Turn it on at the database or table level and the KQL data gets a Delta representation in OneLake. Power BI in Direct Lake mode, the warehouse, notebooks and the SQL analytics endpoint can then query it. While it is on you cannot rename tables, alter a column type, apply RLS on those tables, or delete, truncate or purge data; to do those, turn it off (which soft-deletes the OneLake copy), make the change and turn it back on.
- **Semantic model OneLake integration.** For Import-mode models, Fabric writes the imported tables to Delta tables in OneLake, so the data a model already refreshes becomes available to lakehouses (via shortcuts), SQL and notebooks without rebuilding anything.

**[📖 Eventhouse OneLake availability](https://learn.microsoft.com/en-us/fabric/real-time-intelligence/event-house-onelake-availability)** - behaviour and restrictions

---

## Querying four ways

The "Query and analyze data" objective lists the same task four times: select, filter and aggregate using the **Visual query editor**, **SQL**, **KQL** and **DAX**. The exam checks that you can read and fix each.

```sql
-- SQL (warehouse or SQL analytics endpoint)
SELECT Region, SUM(SalesAmount) AS Sales
FROM dbo.FactSales
WHERE OrderDate >= '2026-01-01'
GROUP BY Region;
```

```kusto
// KQL (eventhouse)
Sales
| where OrderDate >= datetime(2026-01-01)
| summarize Sales = sum(SalesAmount) by Region
```

```dax
// DAX query (DAX query view or XMLA)
EVALUATE
SUMMARIZECOLUMNS (
    'Geography'[Region],
    FILTER ( ALL ( 'Date'[Date] ), 'Date'[Date] >= DATE ( 2026, 1, 1 ) ),
    "Sales", SUM ( 'FactSales'[SalesAmount] )
)
```

The **Visual query editor** builds the same shape with Power Query-style steps in the browser for users who do not write SQL; it generates SQL against the warehouse or endpoint. Recognize the KQL shape: the table comes first, then pipes; `summarize ... by` is GROUP BY; `where` filters.

---

## Storage modes and Direct Lake

| Mode | Where data lives at query time | Freshness | Trade-off |
|---|---|---|---|
| Import | Copy in the model's VertiPaq memory | As of last refresh | Fastest queries; refresh cost and size limits |
| DirectQuery | Source database, queried live | Live | Every visual queries the source; slower; source load |
| Dual | Both; engine picks per query | Mixed | Used for dimension tables in composite models |
| Direct Lake | Delta tables in OneLake, loaded column by column into VertiPaq on demand | Near live after reframing | Import-like speed without copying data |

**Direct Lake** reads Parquet column data straight from OneLake into the same VertiPaq engine Import uses, so queries are fast, but a "refresh" is only **framing**: a cheap metadata operation that points the model at the latest Delta table version. With automatic updates enabled, the model reframes when the Delta tables change.

### Direct Lake on OneLake vs Direct Lake on SQL

The exam asks you to choose between the two:

| | Direct Lake on OneLake | Direct Lake on SQL analytics endpoint |
|---|---|---|
| Sources | One or more Fabric items with Delta tables | A single Fabric item, discovered through its SQL analytics endpoint |
| Fallback to DirectQuery | **Never**; a query that cannot be served in Direct Lake fails | Falls back to DirectQuery, e.g. for SQL views or SQL-based granular security |
| SQL views | Not supported (use a materialized lake view) | Supported via fallback |
| Mixing with Import tables | Yes | Not in the same model (build a composite model on top instead) |
| Security | Needs access to the OneLake files; SQL-based RLS is **not** applied | Permission checks through the SQL endpoint |

The **Direct Lake behavior** property on a Direct Lake on SQL model controls fallback: **Automatic** (fall back when needed, the default), **DirectLakeOnly** (fail instead of falling back, so you notice) or **DirectQueryOnly** (for testing). Fallback is the most common Direct Lake performance surprise: a report is suddenly slow because a view or a guardrail limit pushed queries to DirectQuery.

**[📖 Direct Lake overview](https://learn.microsoft.com/en-us/fabric/fundamentals/direct-lake-overview)** - the two flavours, fallback and comparison with Import and DirectQuery

---

## Modeling features: calculation groups, format strings, field parameters

### Bridge tables and many-to-many

When customers can belong to many segments and segments contain many customers, a direct many-to-many relationship gives ambiguous totals. The classic fix is a **bridge table** (CustomerSegment) with one row per pair, related one-to-many from each side, with the filter direction set so a segment filter flows through the bridge to customers and then to facts. Know that totals over a many-to-many path are not additive across segments: a customer in two segments counts in both.

### Calculation groups

Ten base measures times six time variants (YTD, prior year, YoY %...) is sixty measures to write and maintain. A **calculation group** defines the six variants once as **calculation items** that wrap whatever measure is in context, using `SELECTEDMEASURE()`:

```dax
-- Calculation item "YTD"
CALCULATE ( SELECTEDMEASURE (), DATESYTD ( 'Date'[Date] ) )
```

A user puts the calculation group column on a slicer or columns and every measure gains the variants. Calculation groups have a **precedence** when several apply, and they cannot be combined with implicit measures (dragging a raw numeric column), which is why models that use them should define explicit measures.

### Dynamic format strings

A measure's format can itself be a DAX expression, so one measure can show as currency for one market and as a percentage in another context, or switch between thousands and millions by magnitude. Calculation items can carry format strings too, which is how a "YoY %" item renders as a percentage while "YTD" keeps the base measure's currency format.

### Field parameters

**Field parameters** let report users choose which field or measure a visual uses - switch an axis between Product, Region and Channel, or a value between Sales, Margin and Units - from a slicer, without bookmarks or duplicate visuals. Under the hood a field parameter is a calculated table listing the fields (with `NAMEOF`).

### DAX functions named in the outline

- **Variables** (`VAR ... RETURN`) evaluate once and make complex measures readable and faster.
- **Iterators** (`SUMX`, `AVERAGEX`, `RANKX`) evaluate an expression row by row over a table; watch for iterating large fact tables when an aggregation would do.
- **Table filtering** (`FILTER`, `CALCULATETABLE`, `KEEPFILTERS`, `ALL` family) shapes filter context.
- **Windowing** (`WINDOW`, `OFFSET`, `INDEX`) compute running totals, previous-row comparisons and top-N within a partition without self-joins.
- **Information** functions (`ISINSCOPE`, `HASONEVALUE`, `SELECTEDVALUE`, and the `INFO.*` model metadata functions) let measures adapt to context or describe the model.

---

## Large models, composite models and incremental refresh

### Large semantic model storage format

By default a semantic model in the service is limited to 1 GB. Turning on the **large semantic model storage format** (in a Fabric or Premium capacity) lets it grow up to the capacity's limit, and is required beyond 10 GB. It also enables on-demand loading of data pages after eviction and improves XMLA write performance, which is why it is recommended for any model deployed through XMLA. Desktop uploads are still limited to 10 GB; large models grow in the service through refresh.

**[📖 Large semantic models](https://learn.microsoft.com/en-us/power-bi/enterprise/service-premium-large-models)** - limits, enabling, on-demand load

### Composite models

A composite model mixes storage modes, or builds on top of an existing published model. The DP-600 phrasing "design and build composite models" covers both:

- Import plus DirectQuery tables in one model, usually with **Dual** dimensions so slicers stay fast.
- **DirectQuery to a Power BI semantic model or Analysis Services**: extend a certified shared model with a local table (a budget spreadsheet, a mapping table) without copying or changing the shared model.

### Incremental refresh

An Import model over five years of sales should not reload five years nightly. **Incremental refresh** partitions a table by date: you define `RangeStart` and `RangeEnd` datetime parameters, filter the source query with them (the filter must fold to the source), and set a policy such as "store 5 years, refresh the last 7 days". The service then refreshes only recent partitions. Options include detecting changes on a last-modified column and adding a real-time DirectQuery partition for the current period (a hybrid table).

**[📖 Incremental refresh](https://learn.microsoft.com/en-us/power-bi/connect-data/incremental-refresh-overview)** - policies, parameters and hybrid tables

---

## Worked scenarios

### Scenario 1: Measure changes need code review

**Situation:** Three analytics engineers edit the same sales model. Changes have overwritten each other twice.

**Answer:** Save the model as a Power BI project (.pbip) or connect the workspace to Git, give each engineer a branch, and merge through pull requests; promote the merged result with a deployment pipeline that uses deployment rules for data source connections.

### Scenario 2: A Direct Lake report got slow overnight

**Situation:** A Direct Lake on SQL model over a warehouse became slow after a developer replaced a table with a SQL view.

**Answer:** The view forces fallback to DirectQuery. Materialize the data as a table (or a materialized lake view), and set Direct Lake behavior to DirectLakeOnly in development so fallback fails loudly instead of degrading silently.

### Scenario 3: Sixty time-intelligence measures

**Situation:** A model has 10 base measures and the business wants YTD, QTD, prior year, YoY and YoY % for each.

**Answer:** A calculation group with five calculation items using `SELECTEDMEASURE()`, with a dynamic format string on the YoY % item.

### Scenario 4: Telemetry in an eventhouse, reports in Power BI

**Situation:** Device telemetry lands in an eventhouse; finance wants it in a Direct Lake model with lakehouse data.

**Answer:** Turn on OneLake availability for the KQL database (or the needed tables), then build the model on the Delta representation. Plan schema changes around the restrictions while availability is on.

---

## How the exam tests this

- **Pick the narrowest control.** Item permission over workspace role; RLS/CLS/OLS for rows and columns; endorsement for trust, labels for classification, neither grants access.
- **Lifecycle tool by need.** Diffs and merges: .pbip / Git. Promotion between environments: deployment pipelines with rules. Partition-level deployment and refresh: XMLA endpoint. Starter kit without data: .pbit. Connection only: .pbids.
- **Direct Lake flavour.** Multiple sources or Import mix: on OneLake (no fallback). Views or SQL security needed: on SQL (fallback; control with Direct Lake behavior).
- **Reduce measure sprawl.** Calculation groups; field parameters for user-selectable fields; dynamic format strings for context-dependent formatting.
- **Scale.** Large model format beyond 10 GB and for XMLA writes; incremental refresh with `RangeStart`/`RangeEnd` that fold; composite models to extend shared models.
- **Discovery.** OneLake catalog for items, Real-Time hub for streams.

---

**Related notes:** [Microsoft Fabric overview](microsoft-fabric-overview.md) · [Analytics and reporting in Fabric](analytics-reporting-fabric.md) · [Data engineering in Fabric](data-engineering-fabric.md)
