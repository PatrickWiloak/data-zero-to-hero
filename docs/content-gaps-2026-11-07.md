---
last-updated: 2026-11-07
difficulty: n/a
---

# Content gaps and improvement plan (2026-11-07)

A gap review of data.noblerworks.com, done by listing the tree with `git ls-files` and searching it with
`git grep`. A topic is called missing only when the search found nothing (or one passing mention).
The open items are tracked in `TODO.md` under "Content gaps (2026-11-07 review)".

## Where the site stands

| Area | Has | Compared with the cloud site |
|---|---|---|
| Concept pages | 23 | Strong on modelling, pipelines and SQL |
| Day-one on-ramp | none | Cloud has 15 day-one pages |
| Topic indexes | 2 (databases, data engineering) | Cloud has 7 |
| Hands-on projects | 4 + lab map | No CDC, orchestration or lakehouse build |
| Service comparisons | 2 (databases, messaging) | Cloud has 7 |
| Roadmaps | 2 (data engineer, database specialist) | No analyst or analytics engineer path |
| Postmortems | 2 | |
| Cert guides | 24, all over 8,000 words | 6 have no flashcard deck (all 4 Snowflake, DP-203, Atlas admin) |

The site serves data engineers and DBAs well. Its gaps are the **first step** (there is no on-ramp for
someone who has never written SQL) and the **analyst side** of the field.

## Plan, in order

### Wave 1 - the on-ramp

A `learn/day-one/` section, the way the cloud site has one: first SQL query (SELECT, WHERE, GROUP BY),
spreadsheets to SQL, Python and pandas or Polars for data, notebooks, CSV/JSON/Parquet in practice,
and a local database (DuckDB or Postgres in Docker). Link it from `learn/data-from-scratch.md`.

### Wave 2 - comparisons and roadmaps

- Service comparisons: **warehouses** (Redshift, Fabric/Synapse, BigQuery, Snowflake, Databricks SQL),
  **ETL and orchestration** services (Glue, Data Factory, Dataflow, managed Airflow), and **BI tools**
  (Power BI, Looker, Tableau, QuickSight, Superset). These products appear on many pages but no page
  compares them.
- Roadmaps: **data analyst** and **analytics engineer** (an interview guide exists for the second, but
  no roadmap).

### Wave 3 - concept pages (the TODO candidates plus new ones)

- From TODO: backup and point-in-time restore; short concept pages for query plans, Spark shuffles and skew,
  and semantic layers (long-form versions exist).
- New: **data observability** (one passing mention), **warehouse cost control / FinOps for data**,
  **database normalisation for OLTP**, **time-series and graph databases**, **feature stores**
  (cross-link to ai.noblerworks.com).

### Wave 4 - projects and troubleshooting

- Hands-on: CDC with Debezium into a lakehouse table, an Airflow (or Dagster) orchestration project,
  an Iceberg lakehouse on local storage, and a backup-and-restore drill.
- Troubleshooting: replication lag, deadlocks and lock waits, Kafka consumer lag.
- Topic indexes: streaming, analytics and BI, data governance.
- Postmortems: one streaming or warehouse incident with a public writeup (choose from primary sources only).

### Wave 5 - certs (already in TODO)

- Flashcard decks for the 6 guides without one.
- New guides once codes are verified: Databricks Data Analyst and Spark Developer, Google Associate Data
  Practitioner, Confluent CCAC, MongoDB Data Modeler.
- Decide DP-203 (retired): keep as reference or remove.

## How to do each item

Every new page uses the house style in `CLAUDE.md`. Projects must be run end to end before they ship (the
dbt project is the model). Run `check-readme-counts.py --fix` in the same commit.
