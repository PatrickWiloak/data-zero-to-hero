---
last-updated: 2026-11-07
applies-to: Databricks exam page and exam guide (August 2026 edition), read 2026-11-07
---

# Databricks Data Analyst Associate - Fact Sheet

## Exam Overview

**Exam Name:** Databricks Certified Data Analyst Associate
**Exam Code:** None published (no separate code)
**Duration:** 90 minutes
**Questions:** 45 scored multiple-choice questions, plus up to 10 unscored items
**Passing Score:** Not published; set statistically and may change
**Cost:** $200 USD plus applicable taxes (exam page and guide, read 2026-11-07)
**Delivery:** Online proctored or test center; no test aids
**Languages:** English
**Valid For:** 2 years; recertify by passing the current exam
**Retakes:** Allowed 14 days after an attempt; each retake costs the full fee
**Prerequisites:** None; related training highly recommended; 6+ months of hands-on Databricks analysis recommended

Registration: through the exam delivery platform at webassessor.com/databricks (per the exam page). All SQL follows ANSI standards and Python is not required. The guide's update table lists "Exam Published October 19, 2026" and "Exam Guide Updated October 8, 2026". Check the guide two weeks before your exam date.

**[📖 Official exam page](https://www.databricks.com/learn/certification/data-analyst-associate)** - format, fee, weights, registration
**[📖 Exam guide PDF (August 2026)](https://www.databricks.com/sites/default/files/2026-10/Databricks-Certified-Data-Analyst-Associate-Exam-Guide-August-2026.pdf)** - every objective and ten sample questions
**[📖 Databricks Free Edition](https://www.databricks.com/learn/free-edition)** - free workspace for practice (linked from the guide)
**[📖 Databricks documentation](https://docs.databricks.com)** - the authoritative reference

## Sections and Weights

| # | Section | Weight |
|---|---|---|
| 1 | Understanding of the Databricks Data Intelligence Platform | 11% |
| 2 | Managing Data | 8% |
| 3 | Importing Data | 5% |
| 4 | Executing queries using Databricks SQL and SQL Warehouses | 20% |
| 5 | Analyzing Queries | 15% |
| 6 | Working with Dashboards and Visualizations | 16% |
| 7 | Developing, Sharing, and Maintaining AI/BI Genie Agents (Genie Spaces) | 12% |
| 8 | Data Modeling with Databricks SQL | 5% |
| 9 | Securing Data | 8% |

## Target Audience

- Business and data analysts who query and visualize data on Databricks
- SQL developers moving into the Databricks Data Intelligence Platform
- People who build dashboards and Genie agents for business users

## Section 1: Platform (11%)

**[📖 AI/BI overview](https://docs.databricks.com/aws/en/ai-bi/)** - dashboards, Genie, Unity Catalog semantics
**[📖 Unity Catalog](https://docs.databricks.com/aws/en/data-governance/unity-catalog/)** - securable objects, three-level namespace
**[📖 Marketplace](https://docs.databricks.com/aws/en/marketplace/)** - listings and consumers

**Key Facts:**
- Delta Lake = open table format with a transaction log; Unity Catalog = governance, lineage, access control
- Names are catalog.schema.object; tables are managed or external
- Lineage is on the table's Lineage tab, down to columns; external lineage via external metadata
- Metric views define measures once and query them by any dimension

## Section 2: Managing Data (8%)

**[📖 Tags](https://docs.databricks.com/aws/en/data-governance/unity-catalog/tags)** - governed tags
**[📖 Lineage](https://docs.databricks.com/aws/en/data-governance/unity-catalog/data-lineage)** - Lineage tab

**Key Facts:**
- Governed tags have enforced values and controlled assignment; regular tags need ownership or APPLY TAG
- Tags on catalogs, schemas and tables are inherited downward, not by columns
- Clean with COALESCE, IS NOT NULL, TRIM; equality with NULL never matches

## Section 3: Importing Data (5%)

**[📖 Ingest with SQL and Spark APIs](https://docs.databricks.com/aws/en/ingestion/)** - layers of ingestion
**[📖 Upload files](https://docs.databricks.com/aws/en/ingestion/file-upload/)** - UI upload

**Key Facts:**
- UI upload to a volume needs WRITE VOLUME, USE SCHEMA, USE CATALOG; 5 GB limit; Create table gives a managed table
- Auto Loader or streaming tables for continuous files; Lakeflow Connect for SaaS and databases; Delta Sharing for shared data; Marketplace for third-party listings
- Federation queries external databases in place, read-only

## Section 4: Executing Queries (20%)

**[📖 SQL warehouses](https://docs.databricks.com/aws/en/compute/sql-warehouse/)** - serverless recommended
**[📖 Materialized views](https://docs.databricks.com/aws/en/views/materialized)** - syntax and refresh
**[📖 JOIN syntax](https://docs.databricks.com/aws/en/sql/language-manual/sql-ref-syntax-qry-select-join)** - join types
**[📖 Table history](https://docs.databricks.com/aws/en/delta/history)** - time travel and retention

**Key Facts:**
- Streaming table: continuous append-only; materialized view: stored, refreshed, for BI; view: computed on read
- Joins: INNER (default), LEFT, RIGHT, FULL, CROSS, LEFT SEMI, LEFT ANTI; UNION removes duplicates, UNION ALL keeps them
- approx_count_distinct: HyperLogLog++, default accuracy within 5%
- CREATE OR REPLACE TABLE keeps history and privileges
- History 30 days; deleted-file retention 7 days; VACUUM makes older versions unreadable

## Section 5: Analyzing Queries (15%)

**[📖 Query history](https://docs.databricks.com/aws/en/sql/user/queries/query-history)** - filters and profile
**[📖 Query caching](https://docs.databricks.com/aws/en/sql/user/queries/query-caching)** - three caches
**[📖 Photon](https://docs.databricks.com/aws/en/compute/photon)** - engine
**[📖 Liquid clustering](https://docs.databricks.com/aws/en/delta/clustering)** - CLUSTER BY, AUTO

**Key Facts:**
- Photon: native vectorized C++ engine; default engine for all SQL warehouses; no UDF or stateful streaming support
- Result cache entries last 24 hours; UI cache up to 7 days; invalidated when tables change
- Predictive optimization runs OPTIMIZE, VACUUM, ANALYZE on managed tables only
- CLUSTER BY AUTO needs predictive optimization and managed tables; up to four keys otherwise

## Section 6: Dashboards and Visualizations (16%)

**[📖 AI/BI dashboards](https://docs.databricks.com/aws/en/dashboards/)** - build, share, embed
**[📖 Dashboard parameters](https://docs.databricks.com/aws/en/dashboards/parameters)** - :keyword syntax
**[📖 SQL alerts](https://docs.databricks.com/aws/en/sql/user/alerts/)** - states
**[📖 Create an alert](https://docs.databricks.com/aws/en/sql/user/alerts/create)** - conditions and destinations

**Key Facts:**
- Parameters: :keyword; date ranges use .min and .max; filters outperform parameters on large data
- Publish with shared or individual data permissions; embed via iframe; account-level sharing; email and Slack subscriptions
- Alert states: OK, TRIGGERED, ERROR; schedule supports Quartz cron

## Section 7: Genie Agents (12%)

**[📖 Genie](https://docs.databricks.com/aws/en/genie/)** - overview
**[📖 Set up a Genie Agent](https://docs.databricks.com/aws/en/genie/set-up)** - data, warehouse, instructions
**[📖 Benchmarks](https://docs.databricks.com/aws/en/genie/benchmarks)** - monitoring and accuracy

**Key Facts:**
- Up to 50 tables, views or metric views per agent; pro or serverless warehouse; CAN USE on the warehouse for the author
- Permissions: CAN MANAGE, CAN EDIT, CAN RUN, CAN VIEW; viewers also need SELECT on the data
- Feedback does not change behaviour by itself; edit instructions and example queries; benchmarks up to 500 questions

## Section 8: Data Modeling (5%)

**[📖 Medallion architecture](https://docs.databricks.com/aws/en/lakehouse/medallion)** - layers

**Key Facts:**
- Star: fact table plus denormalized dimensions; snowflake: normalized dimensions; data vault: hubs, links, satellites
- Gold is modeled with a dimensional model

## Section 9: Securing Data (8%)

**[📖 Manage privileges](https://docs.databricks.com/aws/en/data-governance/unity-catalog/manage-privileges/)** - GRANT, ownership

**Key Facts:**
- Read needs USE CATALOG, USE SCHEMA, SELECT; grant to groups
- Owner has all privileges including granting; MANAGE delegates privilege management
- Protect PII with tags, column masks, row filters, lineage

## Exam Tips

1. **Executing queries, dashboards and analyzing queries are 51%.** Be fluent in SQL and the dashboard workflow.
2. **Learn the object choice:** view, materialized view, streaming table.
3. **Read queries for defects:** GROUP BY, HAVING, join type, NULL logic.
4. **Know alert versus dashboard versus job.**
5. **Time:** about 2 minutes per question.

## Quick Reference

```sql
-- Syntax per docs; not run.
CREATE OR REPLACE MATERIALIZED VIEW main.gold.daily AS SELECT date(ts) d, SUM(x) s FROM main.silver.t GROUP BY date(ts);
SELECT * FROM main.silver.t VERSION AS OF 12;
ALTER TABLE main.silver.t CLUSTER BY (customer_id);
GRANT USE CATALOG ON CATALOG main TO `analysts`;
```
