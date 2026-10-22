---
last-updated: 2026-10-22
applies-to: Databricks exam page and exam guide of 2026-10-09
---

# Databricks Data Engineer Professional - Study Strategy

This strategy is for the exam version published on 2026-10-09: ten sections on the exam page (nine in the guide PDF), 59 scored questions in 120 minutes, $200. If you studied for the older five-domain exam, the biggest changes are: serverless compute settings, Lakeflow Jobs control flow, testing, AUTO CDC, `VARIANT` and AI functions, OpenSharing and Clean Rooms, ABAC and anonymisation, Predictive Optimization and `CLUSTER BY AUTO`, bundles, and metric views. Material on Ganglia, `mapGroupsWithState`, hand-written SCD `MERGE` and dynamic views as the main security tool is now secondary.

## How to read the outline

Weight your time by section, but notice that sections lean on each other. Liquid Clustering appears in Section 6 and Section 10; tags appear in Sections 7 and 8; bundles in Sections 1 and 9; materialized views in Sections 1 and 10. Studying in the order below covers each idea once, in the place where it makes most sense.

| Section | Page weight | Study priority |
|---|---|---|
| 1 Developing Code (Python and SQL) | 22% | Highest: broad and code-heavy |
| 6 Cost & Performance Optimisation | 13% | High |
| 3 Transformation, Cleansing, Quality | 10% | High |
| 5 Monitoring and Alerting | 10% | Medium |
| 7 Security and Compliance | 10% | Medium |
| 9 Debugging and Deploying | 10% | Medium |
| 2 Data Ingestion & Acquisition | 7% | Medium (12% in the PDF, with Section 4) |
| 8 Data Governance | 7% | Lower, but quick wins |
| 10 Data Modelling | 6% | Lower |
| 4 Data Sharing and Federation | 5% | Lower |

## Study Approach

### Phase 1: Build (Weeks 1-3)

**Goal:** Write and run the code the exam reads: pipelines, jobs, UDFs, tests and streams.

1. **Section 1 (22%)** - [notes/01](notes/01-developing-code-python-sql.md)
   - Create a bundle from the default Python template; deploy it to a dev target; move logic into a module and test it with `assertDataFrameEqual`
   - Build a Lakeflow pipeline: Auto Loader bronze, expectations in silver, a materialized view in gold
   - Apply a CDC feed with AUTO CDC as SCD Type 1, then Type 2; inspect `__START_AT` / `__END_AT`
   - Build a job with an If/else task and a For each task; run it from the UI, then from the CLI
   - Write a windowed streaming aggregation with a watermark; kill and restart it; confirm no double counting
   - Learn the serverless settings by name: environment, performance mode, high memory, auto-optimization retries
2. **Section 2 (7%)** - [notes/02](notes/02-data-ingestion-acquisition.md)
   - Ingest JSON, CSV and binary files with Auto Loader; try each schema evolution mode
   - Read the Lakeflow Connect setup pages for SQL Server, MySQL and PostgreSQL and note each source-side prerequisite
3. **Section 3 (10%)** - [notes/03](notes/03-data-transformation-quality.md)
   - Rewrite a dedup with `QUALIFY`; parse JSON to `VARIANT` and extract fields; run `ai_classify` on a text column
   - Build a quarantine table next to a cleaned table

### Phase 2: Operate (Weeks 4-5)

**Goal:** Keep it fast, cheap, observable and recoverable.

1. **Section 6 (13%)** - [notes/06](notes/06-cost-performance-optimisation.md)
   - Compare a partitioned table with a liquid-clustered one on the same query in the query profile (files read vs pruned)
   - Enable deletion vectors and time a scattered `MERGE`; read change data feed with `table_changes`
2. **Section 5 (10%)** - [notes/05](notes/05-monitoring-alerting.md)
   - Write the "cost per job last month" query on `system.billing.usage`; query failed runs in `system.lakeflow.job_run_timeline`
   - Extract expectation metrics from a pipeline event log; build a SQL alert on it
3. **Section 9 (10%)** - [notes/09](notes/09-debugging-deploying.md)
   - Break a job task on purpose, read the error, fix it, and repair the run with a parameter override
   - Add a `prod` target with `mode: production` and a service principal `run_as`

### Phase 3: Secure, share, model (Week 6)

1. **Sections 7 and 8 (17%)** - [notes/07](notes/07-security-compliance.md), [notes/08](notes/08-data-governance.md)
   - Grant at catalog level and confirm a new table is covered; add tags and comments
   - Create a column mask, then replace it with an ABAC policy on a governed tag
   - Walk through a right-to-erasure purge: `DELETE`, `REORG ... APPLY (PURGE)`, `VACUUM`
2. **Sections 4 and 10 (11%)** - [notes/04](notes/04-data-sharing-federation.md), [notes/10](notes/10-data-modelling.md)
   - Know when to share, federate or ingest; D2D vs D2O; what Clean Rooms guarantee
   - Design a star schema at a stated grain; define one measure in a metric view and query it with `MEASURE`

### Phase 4: Exam readiness (Weeks 7-8)

- Do the ten sample questions in the exam guide PDF, then this site's [practice questions](../../../resources/practice-questions/databricks-data-engineer-professional.md) and [scenarios](scenarios.md)
- For every miss, write down which section and which "choose X or Y" pair it tested, and reread that part of the notes
- Use Databricks Free Edition for any hands-on gap

## Study Resources

### Official Databricks Resources
- **[📖 Exam page](https://www.databricks.com/learn/certification/data-engineer-professional)** - format, fee, weights
- **[📖 Exam guide PDF (9 Oct 2026)](https://www.databricks.com/sites/default/files/2026-10/Databricks-Certified-Data-Engineer-Professional-Exam-Guide-9-Oct-2026.pdf)** - objectives and sample questions
- **[📖 Databricks Academy](https://www.databricks.com/learn)** - Advanced Data Engineering with Databricks (instructor-led); self-paced Advanced Techniques with Spark Declarative Pipelines, Data Privacy, Performance Optimization, Automated Deployment with Declarative Automation Bundles
- **[📖 Databricks Free Edition](https://www.databricks.com/learn/free-edition)** - a free workspace for practice

### Key Documentation Areas
- **[📖 Lakeflow Spark Declarative Pipelines](https://docs.databricks.com/aws/en/ldp/)** - pipelines, AUTO CDC, expectations, event log
- **[📖 Lakeflow Jobs](https://docs.databricks.com/aws/en/jobs/)** - tasks, control flow, serverless, repair
- **[📖 Declarative Automation Bundles](https://docs.databricks.com/aws/en/dev-tools/bundles/)** - project layout and deployment
- **[📖 Unity Catalog privileges](https://docs.databricks.com/aws/en/data-governance/unity-catalog/manage-privileges/)** - inheritance, ABAC, masks
- **[📖 System tables](https://docs.databricks.com/aws/en/admin/system-tables/)** - monitoring and cost
- **[📖 Liquid clustering](https://docs.databricks.com/aws/en/tables/clustering)** - layout and Predictive Optimization

Databricks warns that AI chatbots and older blog posts often give outdated answers (DLT syntax, Repos, Delta Sharing tokens for Databricks recipients, Z-order advice). When a source disagrees with the current docs, trust the docs.

## Exam Tactics

### Question Strategy
1. **Find the constraint word first**: latency, cost, "at most once", "future tables", "without copying", "minimal code", "neither party sees".
2. **Prefer the managed, automatic option** when it meets the requirement: serverless, managed tables, Predictive Optimization, AUTO CDC, ABAC, bundles.
3. **Eliminate answers that drop correctness for speed**: disabling checkpoints, zero-hour `VACUUM`, a streaming table for an aggregate that must reflect late updates.
4. **Check the name and the behaviour**: an option may use a current name but describe the wrong behaviour.

### Time Management
- **59 scored questions in 120 minutes** (plus any unscored items; extra time is built in) is about 2 minutes each
- **First pass (80 minutes):** answer what you know, flag long scenarios
- **Second pass (30 minutes):** flagged questions
- **Final review (10 minutes):** no blanks; there is no penalty for guessing

### Key Differentiators to Study

| Concept A | Concept B | Key Difference |
|-----------|-----------|----------------|
| Streaming table | Materialized view | Each row once, append-only vs always-correct result incl. late updates |
| Lakeflow Spark Declarative Pipelines | Structured Streaming in a job | Managed checkpoints, quality, lineage vs full control and custom state |
| pandas UDF | Unity Catalog function | Vectorised speed vs governed, shareable, usable in masks |
| Standard serverless | Performance optimized | Fewer DBUs and 4-6 min start vs fast start for SLAs |
| Liquid Clustering keys | `CLUSTER BY AUTO` | Known stable filters vs unknown or changing patterns |
| Deletion vectors | File rewrite | Mark rows vs rewrite whole files on update/delete |
| OpenSharing D2D | OpenSharing D2O | Databricks recipient, no token, more assets vs any platform, token or OIDC |
| Lakehouse Federation | Lakeflow Connect | Query in place vs replicate incrementally with CDC |
| Column mask | ABAC policy | One table vs every tagged column, including future tables |
| Pseudonymisation | Anonymisation | Re-linkable (hash, token) vs irreversible (suppress, generalise) |
| Metric view | Materialized view | One governed definition vs pre-computed speed |
| Repair run | Retry | Human-triggered rerun of failed tasks with overrides vs automatic rerun |

### Common Pitfalls
- **Treating `DELETE` as erasure** - deletion vectors and time travel keep the data until purge and vacuum
- **Partitioning small tables or high-cardinality columns** - creates small files
- **No watermark on a stateful stream** - unbounded state
- **Granting `SELECT` without `USE CATALOG` / `USE SCHEMA`** - access still fails
- **Leaving auto-optimization retries on for a non-idempotent job** - it may run twice
- **Studying from DLT-era material** - syntax and names have moved on (`import dlt`, `APPLY CHANGES`)
