---
last-updated: 2026-10-22
applies-to: Databricks exam page and exam guide of 2026-05-04
---

# Databricks Certified Data Engineer Associate

## Exam Overview

The Databricks Certified Data Engineer Associate exam tests whether you can do foundational data engineering on the Databricks Data Intelligence Platform: know the platform and its compute, ingest data with Auto Loader, `COPY INTO` and Lakeflow Connect, transform it with PySpark and SQL into silver and gold objects, orchestrate it with Lakeflow Jobs, ship it with Git folders and Declarative Automation Bundles, keep it healthy, and govern it with Unity Catalog.

The exam changed on **2026-05-04**. The old version had five domains (Lakehouse Platform, ELT with Spark SQL and Python, Incremental Data Processing, Production Pipelines, Data Governance). The current one has seven sections, adds Lakeflow Connect, job triggers and control flow, CI/CD with bundles, troubleshooting with the Spark UI, Liquid Clustering, predictive optimization and ABAC, and gives the platform overview much less weight. This guide was rebuilt for the new version on 2026-10-22.

**Exam Details** (from the **[📖 exam page](https://www.databricks.com/learn/certification/data-engineer-associate)** and exam guide, read 2026-10-22):
- **Exam Code:** Databricks Certified Data Engineer Associate (no separate code)
- **Duration:** 90 minutes
- **Number of Questions:** 45 scored multiple-choice questions, plus possible unscored items
- **Passing Score:** Not published by Databricks
- **Cost:** $200 USD, plus applicable taxes
- **Delivery:** Online proctored or test center; no test aids
- **Languages:** English, Japanese, Portuguese (BR), Korean
- **Validity:** 2 years; recertify by passing the current exam
- **Prerequisites:** None; training and six months of hands-on Databricks experience highly recommended

## Exam Sections

The exam page and the **[📖 exam guide PDF (May 2026)](https://www.databricks.com/sites/default/files/2026-05/databricks-certified-data-engineer-associate-exam-guide-may-2026-000.pdf)** agree on seven sections and their weights.

| # | Section | Weight | About | Notes |
|---|---|---|---|---|
| 1 | Databricks Intelligence Platform | 6% | 3 questions | [01](notes/01-intelligence-platform.md) |
| 2 | Data Ingestion and Loading | 21% | 9-10 | [02](notes/02-data-ingestion-loading.md) |
| 3 | Data Transformation and Modeling | 22% | 10 | [03](notes/03-data-transformation-modeling.md) |
| 4 | Working with Lakeflow Jobs | 16% | 7 | [04](notes/04-lakeflow-jobs.md) |
| 5 | Implementing CI/CD | 10% | 4-5 | [05](notes/05-cicd.md) |
| 6 | Troubleshooting, Monitoring, and Optimization | 10% | 4-5 | [06](notes/06-troubleshooting-monitoring-optimization.md) |
| 7 | Governance and Security | 15% | 7 | [07](notes/07-governance-security.md) |

The question counts are the weights applied to 45 scored items, rounded; Databricks doesn't publish per-section counts.

### What each section covers

1. **Intelligence Platform (6%)** - architecture (control plane and compute plane), Delta Lake and Unity Catalog as core components; compute services (serverless, SQL warehouses, all-purpose and jobs clusters) with their limits and cost models, and choosing one per workload.
2. **Ingestion and Loading (21%)** - batch, streaming and incremental patterns; local file upload; `COPY INTO`; Auto Loader with schema enforcement and evolution in directory listing or file notification mode; Lakeflow Connect standard and managed connectors; JDBC/ODBC and REST from notebooks, scheduled as jobs; choosing between methods; semi-structured and nested data.
3. **Transformation and Modeling (22%)** - cleaning bronze into silver; joins (inner, left, broadcast, multiple keys, cross) and unions; adding, dropping, splitting and renaming columns, filtering, exploding arrays; deduplication and aggregation, including approximate count distinct; basic Spark tuning parameters; gold objects (tables, views, materialized views, streaming tables); data quality checks.
4. **Lakeflow Jobs (16%)** - notebook, SQL, dashboard and pipeline tasks in a DAG; retries; If/else branching and For each looping; scheduled, file arrival and table update triggers; time-based vs data-driven triggers.
5. **CI/CD (10%)** - branches, commits, pushes and pull requests with Git folders; Declarative Automation Bundles with variables, targets and overrides; deploying jobs and pipelines across dev, test and prod; the Databricks CLI in CI/CD.
6. **Troubleshooting, Monitoring, Optimization (10%)** - run history trends; job statuses and task graphs; skew, shuffle and spill in the Spark UI; Liquid Clustering and predictive optimization; cluster start failures, library conflicts and out-of-memory errors.
7. **Governance and Security (15%)** - managed vs external tables and converting between them; `GRANT`, `REVOKE` and `DENY` to users, groups and service principals; row filters and column masks; ABAC policies.

## Names to know

The exam uses current product names. Older courses, practice tests and the exam guide's own sample questions still use some old ones.

| Current name | Former name |
|---|---|
| Lakeflow Spark Declarative Pipelines | Delta Live Tables (DLT) |
| AUTO CDC | APPLY CHANGES |
| Lakeflow Jobs | Databricks Workflows, Jobs |
| Lakeflow Connect | (new umbrella for ingestion connectors) |
| Declarative Automation Bundles | Databricks Asset Bundles (DABs) |
| Git folders | Repos |
| Databricks Free Edition | Community Edition |
| SQL warehouse | SQL endpoint |
| Standard / dedicated access mode | Shared / single user (and the older "high concurrency") |
| Data Intelligence Platform | Lakehouse Platform |

## Study Approach

### Phase 1: Platform and ingestion (Weeks 1-2)
1. Section 1: how the platform fits together, Delta Lake, Unity Catalog, and which compute to pick.
2. Section 2: upload, `COPY INTO`, Auto Loader and its schema modes, Lakeflow Connect managed connectors, JDBC and REST ingestion.

### Phase 2: Transform and orchestrate (Weeks 3-4)
1. Section 3: PySpark and SQL cleaning, joins, unions, aggregation, tuning, gold objects and expectations. Write the code in Free Edition; this is the largest section.
2. Section 4: build a multi-task job with an If/else, a For each and a file arrival trigger.

### Phase 3: Ship, operate and govern (Week 5)
1. Section 5: a Git folder workflow and a small bundle with dev and prod targets, deployed with the CLI.
2. Section 6: read the Spark UI on a skewed join; Liquid Clustering and predictive optimization.
3. Section 7: managed vs external, grants, row filters, column masks, ABAC.

### Phase 4: Exam readiness (Week 6)
1. Timed practice, then reread the "How the exam tests this section" ending of each note for the sections you miss most.

## Study Resources

- **[📖 Exam page](https://www.databricks.com/learn/certification/data-engineer-associate)** - format, fee, section weights, registration
- **[📖 Exam guide PDF (May 2026)](https://www.databricks.com/sites/default/files/2026-05/databricks-certified-data-engineer-associate-exam-guide-may-2026-000.pdf)** - full objective list, recommended training and five sample questions
- **[📖 Databricks Academy](https://www.databricks.com/learn)** - the instructor-led Data Engineering with Databricks course, and self-paced courses on Lakeflow Connect, Lakeflow Jobs, DevOps essentials, Unity Catalog interoperability, Spark Declarative Pipelines and data governance
- **[📖 Databricks Free Edition](https://docs.databricks.com/aws/en/getting-started/free-edition)** - free serverless workspace for hands-on practice (replaced Community Edition)
- **[📖 Databricks documentation](https://docs.databricks.com/aws/en/)** - the authoritative source; prefer it over older blog posts

## Tips for Success

1. **Ingestion plus transformation is 43% of the exam.** Know Auto Loader vs `COPY INTO` vs Lakeflow Connect cold, and be able to read PySpark and SQL transformation code.
2. **Learn the new names.** Questions say Lakeflow Jobs, Lakeflow Spark Declarative Pipelines, Declarative Automation Bundles, Git folders.
3. **Prefer managed and automatic.** Managed connectors, managed tables, serverless, predictive optimization, Liquid Clustering, ABAC: the exam favours the option that removes manual work.
4. **Match the trigger to the data.** Irregular arrivals mean file arrival; upstream tables mean table update; a deadline means a schedule.
5. **Read the Spark UI numbers.** Max far above Median is skew; non-zero spill is spill.
6. **Grants need gates.** `USE CATALOG` + `USE SCHEMA` + `SELECT`, inherited downward.
7. **Time management.** 2 minutes per question; flag long scenarios and come back.

## File Index

| File | Description |
|------|-------------|
| [fact-sheet.md](fact-sheet.md) | Exam facts and the key facts per section, with documentation links |
| [strategy.md](strategy.md) | Study phases, resources and exam tactics |
| [practice-plan.md](practice-plan.md) | Six-week schedule with checkboxes |
| [scenarios.md](scenarios.md) | Exam-style scenarios with solutions |
| [notes/01-intelligence-platform.md](notes/01-intelligence-platform.md) | Architecture, Delta Lake, Unity Catalog, compute choices |
| [notes/02-data-ingestion-loading.md](notes/02-data-ingestion-loading.md) | Upload, COPY INTO, Auto Loader, schema evolution, Lakeflow Connect, JDBC and REST, nested data |
| [notes/03-data-transformation-modeling.md](notes/03-data-transformation-modeling.md) | Medallion, cleaning, joins, unions, aggregation, tuning, gold objects, data quality |
| [notes/04-lakeflow-jobs.md](notes/04-lakeflow-jobs.md) | Tasks, DAGs, task values, retries, If/else, For each, triggers |
| [notes/05-cicd.md](notes/05-cicd.md) | Git folders, bundles, variables and targets, the CLI, a CI/CD flow |
| [notes/06-troubleshooting-monitoring-optimization.md](notes/06-troubleshooting-monitoring-optimization.md) | Jobs monitoring, Spark UI, skew and spill, Liquid Clustering, predictive optimization, failures |
| [notes/07-governance-security.md](notes/07-governance-security.md) | Managed and external tables, grants, row filters, column masks, ABAC |
| [Practice questions](../../../resources/practice-questions/databricks-data-engineer-associate.md) | 25 questions tagged by section |
