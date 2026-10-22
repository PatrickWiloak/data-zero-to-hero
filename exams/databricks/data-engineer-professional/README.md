---
last-updated: 2026-10-22
applies-to: Databricks exam page and exam guide of 2026-10-09
---

# Databricks Certified Data Engineer Professional

## Exam Overview

The Databricks Certified Data Engineer Professional exam tests advanced skills in building, optimising and maintaining production-grade data engineering on the Databricks platform. Databricks names the platform features it covers: Databricks SQL, Unity Catalog, Auto Loader, Lakeflow Pipelines, Databricks compute (including serverless), Lakeflow Jobs, Lakehouse Federation, OpenSharing, Clean Rooms and the medallion architecture. It also tests streaming, orchestration, debugging, CI/CD with the CLI, REST API and Declarative Automation Bundles, and AI functions.

The exam changed on **2026-10-09**. The old version had five domains (pipeline design, incremental processing, governance, performance, monitoring). The current one has ten sections, a lower fee, and much more on serverless, Lakeflow, ABAC, metric views and deployment. This guide was rebuilt for the new version on 2026-10-22.

**Exam Details** (from the **[📖 exam page](https://www.databricks.com/learn/certification/data-engineer-professional)**, read 2026-10-22):
- **Exam Code:** Databricks Certified Data Engineer Professional (no separate code)
- **Duration:** 120 minutes
- **Number of Questions:** 59 scored multiple-choice questions (the exam guide PDF says 60), plus possible unscored items
- **Passing Score:** Not published by Databricks
- **Cost:** $200 USD, plus applicable taxes
- **Delivery:** Online proctored or test center
- **Languages:** English, Japanese, Portuguese (BR), Korean
- **Validity:** 2 years; recertify by passing the current exam
- **Prerequisites:** None; related training and hands-on experience highly recommended

## Exam Sections

The exam page lists ten sections. The exam guide PDF of the same date (**[📖 Exam Guide, 9 Oct 2026](https://www.databricks.com/sites/default/files/2026-10/Databricks-Certified-Data-Engineer-Professional-Exam-Guide-9-Oct-2026.pdf)**) lists nine, with Data Sharing and Federation folded into Data Ingestion, and slightly different weights. The objectives are the same in both. This guide follows the exam page's ten sections and shows both weights; check the exam guide again two weeks before your exam, as Databricks advises.

| # | Section (exam page) | Page weight | Guide PDF weight | Notes |
|---|---|---|---|---|
| 1 | Developing Code for Data Processing using Python and SQL | 22% | 23% | [01](notes/01-developing-code-python-sql.md) |
| 2 | Data Ingestion & Acquisition | 7% | 12% (incl. section 4) | [02](notes/02-data-ingestion-acquisition.md) |
| 3 | Data Transformation, Cleansing, and Quality | 10% | 12% ("Data Manipulation") | [03](notes/03-data-transformation-quality.md) |
| 4 | Data Sharing and Federation | 5% | in section 2 | [04](notes/04-data-sharing-federation.md) |
| 5 | Monitoring and Alerting | 10% | 10% | [05](notes/05-monitoring-alerting.md) |
| 6 | Cost & Performance Optimisation | 13% | 15% | [06](notes/06-cost-performance-optimisation.md) |
| 7 | Ensuring Data Security and Compliance | 10% | 8% | [07](notes/07-security-compliance.md) |
| 8 | Data Governance | 7% | 5% | [08](notes/08-data-governance.md) |
| 9 | Debugging and Deploying | 10% | 10% | [09](notes/09-debugging-deploying.md) |
| 10 | Data Modelling | 6% | 5% | [10](notes/10-data-modelling.md) |

### What each section covers

1. **Developing Code (22%)** - Python project structure for Declarative Automation Bundles; troubleshooting library installs on serverless, pipelines and bundles; pandas, Python, SQL and Unity Catalog UDFs; streaming pipelines with Lakeflow Pipelines and Auto Loader; Lakeflow Jobs via UI, API and CLI with If/else and For each; streaming table vs materialized view; AUTO CDC with SCD Type 1 and 2; Structured Streaming vs Spark Declarative Pipelines; serverless compute settings (environments, performance mode, high memory, disallowing retries); unit and integration tests with `assertDataFrameEqual`, `assertSchemaEqual` and `DataFrame.transform`; watermarks, output modes, `foreachBatch` and checkpoints.
2. **Data Ingestion (7%)** - Delta, Parquet, JSON, Iceberg, CSV and binary files; Kafka, Kinesis and Pub/Sub; incremental CDC pipelines into Delta or Iceberg targets; Lakeflow Connect CDC from SQL Server, MySQL and PostgreSQL.
3. **Transformation, Cleansing, and Quality (10%)** - window functions, joins and aggregations at scale; `VARIANT` with `parse_json` and `variant_get`; AI functions such as `ai_query`; pipeline expectations to warn, drop, fail or quarantine.
4. **Sharing and Federation (5%)** - OpenSharing Databricks-to-Databricks and Databricks-to-Open; Clean Rooms; Lakehouse Federation with Unity Catalog permissions and connection-level credentials.
5. **Monitoring and Alerting (10%)** - system tables (billing, compute, access, lakeflow); REST API, CLI and SDK; pipeline event logs; alerts on metrics, quality, cost, query health, audit events and agent quality; Lakeflow Jobs UI and API.
6. **Cost & Performance (13%)** - managed tables, Predictive Optimization and Liquid Clustering; deletion vectors and `CLUSTER BY AUTO`; disk cache; change data feed; query profile; Liquid Clustering vs partitioning and Z-order.
7. **Security and Compliance (10%)** - least-privilege ACLs; ABAC policies with governed tags; hashing, tokenisation, suppression and generalisation; PII detection and masking across batch and streaming; purging for retention and GDPR erasure.
8. **Data Governance (7%)** - tags and comments for discoverability; the Unity Catalog permission inheritance model.
9. **Debugging and Deploying (10%)** - Spark UI, cluster logs, system tables and query profiles; repair runs with parameter overrides; deploying with Declarative Automation Bundles; CI/CD with Git folders.
10. **Data Modelling (6%)** - Delta and Iceberg layouts (partition to grain, cluster to access pattern, compaction); dimensional models with materialized views and Unity Catalog metric views.

## Names to know

The exam uses current product names. Older courses and practice tests use the old ones.

| Current name | Former name |
|---|---|
| Lakeflow Spark Declarative Pipelines (the guide says "Lakeflow Pipelines") | Delta Live Tables (DLT) |
| AUTO CDC | APPLY CHANGES |
| Lakeflow Jobs | Databricks Workflows |
| Declarative Automation Bundles | Databricks Asset Bundles (DABs) |
| Git folders | Repos |
| OpenSharing | Delta Sharing |
| Disk cache | Delta cache |
| Data quality monitoring | Lakehouse Monitoring |

## Study Approach

### Phase 1: Build and run code (Weeks 1-3)
1. Sections 1 and 2: bundles, UDFs, Lakeflow pipelines, AUTO CDC, Lakeflow Jobs, serverless settings, tests, stateful streaming, ingestion.
2. Section 3: window functions, `VARIANT`, AI functions, expectations.

### Phase 2: Operate it (Weeks 4-5)
1. Sections 5, 6 and 9: system tables, event logs, alerts, Predictive Optimization, clustering, deletion vectors, query profile, repair runs, bundle deployment.

### Phase 3: Secure, share and model it (Week 6)
1. Sections 4, 7, 8 and 10: OpenSharing, Clean Rooms, federation, ABAC, anonymisation, erasure, inheritance, metric views.

### Phase 4: Exam readiness (Weeks 7-8)
1. Timed practice, then reread the notes for the sections you miss most.

## Study Resources

- **[📖 Exam page](https://www.databricks.com/learn/certification/data-engineer-professional)** - format, fee, section weights
- **[📖 Exam guide PDF (9 Oct 2026)](https://www.databricks.com/sites/default/files/2026-10/Databricks-Certified-Data-Engineer-Professional-Exam-Guide-9-Oct-2026.pdf)** - full objective list and ten sample questions
- **[📖 Databricks Academy](https://www.databricks.com/learn)** - Advanced Data Engineering with Databricks; self-paced courses on advanced Spark Declarative Pipelines, data privacy, performance optimisation and Declarative Automation Bundles
- **[📖 Databricks Free Edition](https://www.databricks.com/learn/free-edition)** - free workspace for the hands-on practice the guide recommends
- **[📖 Databricks documentation](https://docs.databricks.com/aws/en/)** - the authoritative source; prefer it over older blog posts

## Tips for Success

1. **Section 1 is almost a quarter of the exam.** Know the "choose X or Y" pairs: streaming table vs materialized view, pipelines vs Structured Streaming, pandas UDF vs Unity Catalog function, standard vs performance-optimised serverless.
2. **Learn the new names.** Questions say Declarative Automation Bundles, AUTO CDC, OpenSharing, Lakeflow Jobs.
3. **Prefer managed and automatic.** Managed tables, Predictive Optimization, Liquid Clustering, serverless, ABAC: the exam favours the option that removes manual work.
4. **Read for the constraint word.** Latency, cost, "at most once", "future tables", "without copying", "neither party sees raw data".
5. **Know where to look.** System tables for cost and audit, event log for pipeline quality, query profile and Spark UI for slowness, task output for exceptions.
6. **Erasure is more than DELETE.** Purge deletion vectors, vacuum, and handle downstream copies.
7. **Time management.** About 2 minutes per question; flag long scenarios and come back.

## File Index

| File | Description |
|------|-------------|
| [fact-sheet.md](fact-sheet.md) | Exam facts and the key facts per section, with documentation links |
| [strategy.md](strategy.md) | Study phases, resources and exam tactics |
| [practice-plan.md](practice-plan.md) | Eight-week schedule with checkboxes |
| [scenarios.md](scenarios.md) | Exam-style scenarios with solutions |
| [notes/01-developing-code-python-sql.md](notes/01-developing-code-python-sql.md) | Bundles, libraries, UDFs, pipelines, AUTO CDC, Jobs, compute, tests, stateful streaming |
| [notes/02-data-ingestion-acquisition.md](notes/02-data-ingestion-acquisition.md) | File formats, Auto Loader, message buses, CDC targets, Lakeflow Connect |
| [notes/03-data-transformation-quality.md](notes/03-data-transformation-quality.md) | Windows, joins, aggregations, VARIANT, AI functions, expectations |
| [notes/04-data-sharing-federation.md](notes/04-data-sharing-federation.md) | OpenSharing D2D and D2O, Clean Rooms, Lakehouse Federation |
| [notes/05-monitoring-alerting.md](notes/05-monitoring-alerting.md) | System tables, API/CLI/SDK, event logs, alerts, Jobs monitoring |
| [notes/06-cost-performance-optimisation.md](notes/06-cost-performance-optimisation.md) | Predictive Optimization, Liquid Clustering, deletion vectors, cache, CDF, query profile |
| [notes/07-security-compliance.md](notes/07-security-compliance.md) | Least privilege, masks and filters, ABAC, anonymisation, PII pipelines, purging |
| [notes/08-data-governance.md](notes/08-data-governance.md) | Object model, inheritance, ownership, tags, comments, lineage |
| [notes/09-debugging-deploying.md](notes/09-debugging-deploying.md) | Diagnostics, repair runs, bundles, Git folders CI/CD |
| [notes/10-data-modelling.md](notes/10-data-modelling.md) | Table layout, dimensional models, materialized views, metric views |
| [Practice questions](../../../resources/practice-questions/databricks-data-engineer-professional.md) | 30 questions tagged by section |
