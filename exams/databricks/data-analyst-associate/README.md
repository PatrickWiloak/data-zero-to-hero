---
last-updated: 2026-11-07
applies-to: Databricks exam page and exam guide (August 2026 edition), read 2026-11-07
---

# Databricks Certified Data Analyst Associate

## Exam Overview

The Databricks Certified Data Analyst Associate exam tests whether you can do basic analytics tasks on the Databricks Data Intelligence Platform: find and manage governed data with Unity Catalog, import it, query it with Databricks SQL, tune and troubleshoot queries, build AI/BI dashboards and alerts, create and maintain Genie agents, model data for analytics and keep it secure. Everything is SQL (ANSI standard) and no Python is required.

**Exam Details** (from the **[📖 exam page](https://www.databricks.com/learn/certification/data-analyst-associate)** and **[📖 exam guide](https://www.databricks.com/sites/default/files/2026-10/Databricks-Certified-Data-Analyst-Associate-Exam-Guide-August-2026.pdf)**, read 2026-11-07):
- **Exam Name:** Databricks Certified Data Analyst Associate (no separate code)
- **Duration:** 90 minutes
- **Number of Questions:** 45 scored multiple-choice questions, plus up to 10 unscored
- **Passing Score:** Not published
- **Cost:** $200 USD plus taxes
- **Delivery:** Online proctored or test center; no test aids
- **Languages:** English
- **Validity:** 2 years; retakes after 14 days at full price
- **Prerequisites:** None; 6+ months of hands-on analysis recommended

## Exam Sections

| # | Section | Weight | About | Notes |
|---|---|---|---|---|
| 1 | Understanding of the Databricks Data Intelligence Platform | 11% | 5 | [01](notes/01-databricks-platform.md) |
| 2 | Managing Data | 8% | 4 | [02](notes/02-managing-data.md) |
| 3 | Importing Data | 5% | 2 | [03](notes/03-importing-data.md) |
| 4 | Executing queries using Databricks SQL and SQL Warehouses | 20% | 9 | [04](notes/04-executing-queries.md) |
| 5 | Analyzing Queries | 15% | 7 | [05](notes/05-analyzing-queries.md) |
| 6 | Working with Dashboards and Visualizations | 16% | 7 | [06](notes/06-dashboards-and-visualizations.md) |
| 7 | Developing, Sharing, and Maintaining AI/BI Genie Agents (Genie Spaces) | 12% | 5-6 | [07](notes/07-genie-agents.md) |
| 8 | Data Modeling with Databricks SQL | 5% | 2 | [08](notes/08-data-modeling.md) |
| 9 | Securing Data | 8% | 4 | [09](notes/09-securing-data.md) |

Question counts apply the weights to 45 scored items, rounded; Databricks does not publish per-section counts.

### What each section covers

1. **Platform** - Delta Lake, Unity Catalog, Databricks SQL, Lakeflow, Mosaic AI, Lakebase; catalogs, schemas, managed and external tables, views, metric views, lineage; Marketplace.
2. **Managing Data** - discover certified datasets, governed tags, lineage, cleaning with SQL.
3. **Importing Data** - S3 and Auto Loader, managed connectors, Delta Sharing, API intake, Marketplace; UI upload.
4. **Executing Queries** - SQL warehouses, Genie Code, federation, materialized views versus streaming tables, aggregates, joins, unions, tables, time travel.
5. **Analyzing Queries** - Photon, query history and profile, Delta audit, caching, Predictive Optimization, Liquid Clustering, fixing queries.
6. **Dashboards** - AI/BI dashboards, visualizations, parameters, sharing and embedding, refresh schedules, alerts, chart choice.
7. **Genie Agents** - purpose and components, creation, permissions and distribution, optimization with benchmarks and feedback.
8. **Data Modeling** - star, snowflake, data vault; alignment with Medallion.
9. **Securing Data** - privileges, ownership, three-level namespace, PII.

## Names to know

| Name in the guide | Note |
|---|---|
| Genie Agents (Genie spaces) | Docs say a Genie Agent was formerly a Genie space |
| Genie Code (Databricks Assistant) | The AI assistant in notebooks and the SQL editor |
| Lakeflow Declarative Pipelines (Delta Live Tables) | Pipeline framework behind streaming tables and materialized views |
| OpenSharing | Current docs name for the Delta Sharing platform |
| Data Intelligence Platform (Lakehouse Platform) | The platform |

## Study Approach

### Phase 1: Data and queries (Weeks 1-3)
1. Sections 1-3 briefly, then Section 4 slowly: write every join and aggregate yourself in Free Edition.
2. Section 5: use Query History and the query profile on your own queries.

### Phase 2: Dashboards and Genie (Weeks 3-4)
1. Section 6: build a two-page dashboard with a date parameter and create an alert.
2. Section 7: create a Genie agent on a small schema, add instructions and a benchmark.

### Phase 3: Model, secure, review (Week 5)
1. Sections 8 and 9, then practice questions and the "How the exam tests this section" endings.

## Study Resources

- **[📖 Exam page](https://www.databricks.com/learn/certification/data-analyst-associate)** - format, fee, registration
- **[📖 Exam guide PDF](https://www.databricks.com/sites/default/files/2026-10/Databricks-Certified-Data-Analyst-Associate-Exam-Guide-August-2026.pdf)** - objectives, sample questions and answer key
- **[📖 Databricks Free Edition](https://www.databricks.com/learn/free-edition)** - free workspace
- **[📖 Databricks documentation](https://docs.databricks.com)** - authoritative reference
- Courses named in the guide (plain text, names change): Data Analysis with Databricks (instructor-led); AI/BI for Data Analysts and SQL Analytics on Databricks (self-paced, Databricks Academy)

## Tips for Success

1. **SQL fluency is the core.** Sections 4-6 are about half the exam.
2. **Know which object to create** (view, materialized view, streaming table).
3. **Use current names;** older materials say Assistant and Genie spaces.
4. **Understand why a query is wrong,** not just how to write it.

## File Index

| File | Description |
|------|-------------|
| [fact-sheet.md](fact-sheet.md) | Exam facts and key facts per section, with documentation links |
| [strategy.md](strategy.md) | Study phases and exam tactics |
| [practice-plan.md](practice-plan.md) | Five-week schedule with checkboxes |
| [scenarios.md](scenarios.md) | Exam-style scenarios with solutions |
| [notes/01-databricks-platform.md](notes/01-databricks-platform.md) | Platform components, Catalog Explorer objects, Marketplace |
| [notes/02-managing-data.md](notes/02-managing-data.md) | Discovery, tags, lineage, cleaning |
| [notes/03-importing-data.md](notes/03-importing-data.md) | Ingestion methods and UI upload |
| [notes/04-executing-queries.md](notes/04-executing-queries.md) | Warehouses, views, aggregates, joins, tables, time travel |
| [notes/05-analyzing-queries.md](notes/05-analyzing-queries.md) | Photon, history, caching, optimization |
| [notes/06-dashboards-and-visualizations.md](notes/06-dashboards-and-visualizations.md) | Dashboards, parameters, sharing, alerts |
| [notes/07-genie-agents.md](notes/07-genie-agents.md) | Genie creation, permissions, optimization |
| [notes/08-data-modeling.md](notes/08-data-modeling.md) | Star, snowflake, data vault, Medallion |
| [notes/09-securing-data.md](notes/09-securing-data.md) | Privileges, ownership, PII |
| [Practice questions](../../../resources/practice-questions/databricks-data-analyst-associate.md) | 25 questions tagged by section |
