---
last-updated: 2026-10-22
applies-to: Databricks exam page and exam guide of 2026-05-04
---

# Databricks Data Engineer Associate - Study Plan

## 6-Week Study Schedule

About 1-1.5 hours a day. Do the hands-on items in **[📖 Databricks Free Edition](https://docs.databricks.com/aws/en/getting-started/free-edition)**; it is serverless-only, so read about classic cluster settings rather than clicking them.

### Week 1: Platform and file ingestion (Sections 1-2)

#### Day 1-2: The platform
- [ ] Read `notes/01-intelligence-platform.md`
- [ ] Sketch control plane, compute plane and storage from memory
- [ ] Create a Delta table, update it twice, run `DESCRIBE HISTORY`, query `VERSION AS OF`, `RESTORE`
- [ ] Write a one-line rule for when to use each compute type

#### Day 3-4: COPY INTO and upload
- [ ] Read `notes/02-data-ingestion-loading.md` up to Auto Loader
- [ ] Upload a CSV with the file upload UI into a managed table
- [ ] Load JSON files from a volume with `COPY INTO`; rerun it and confirm nothing reloads

#### Day 5-6: Auto Loader and schema evolution
- [ ] Ingest the same files with Auto Loader and `trigger(availableNow=True)`
- [ ] Add a new field to one file; try `addNewColumns` and `rescue`; find `_rescued_data`
- [ ] Do the same in SQL with a streaming table and `read_files`

#### Day 7: Review
- [ ] Without notes: Auto Loader vs `COPY INTO`, the four evolution modes, the three discovery modes

### Week 2: Connectors and custom ingestion (Section 2)

#### Day 8-9: Lakeflow Connect
- [ ] Read the managed connectors section and the Salesforce and SQL Server connector docs
- [ ] List the pieces: connection, gateway (databases), ingestion pipeline, destination, SCD type, schedule
- [ ] Know the standard connectors: Auto Loader, `COPY INTO`, Kafka, Pub/Sub, Pulsar, SFTP

#### Day 10-11: JDBC, REST and nested data
- [ ] Store a secret and read it with `dbutils.secrets.get`
- [ ] Call a public REST API with paging; write the result to a table
- [ ] Explode a nested array, extract fields with `:` paths and `from_json`, try `VARIANT`

#### Day 12-14: Review and choose
- [ ] For ten made-up sources, pick the ingestion method and say why
- [ ] Practice questions tagged Section 2

### Week 3: Transformation and modeling (Section 3)

#### Day 15-16: Cleaning and columns
- [ ] Read `notes/03-data-transformation-modeling.md`
- [ ] Write a bronze-to-silver cleaning step in PySpark and in SQL: nulls, casts, standardised values
- [ ] Practise `withColumn`, `withColumnRenamed`, `drop`, `split`, `explode`, `explode_outer`

#### Day 17-18: Joins, unions, aggregation
- [ ] Every join type, a join on two keys, a broadcast join, a cross join
- [ ] `union`, `unionByName`, SQL `UNION` vs `UNION ALL`
- [ ] Deduplicate with `dropDuplicates` and with `ROW_NUMBER()` + `QUALIFY`
- [ ] Aggregate with `count`, `countDistinct`, `approx_count_distinct`, `mean`; run `summary()`

#### Day 19-20: Tuning, gold objects, quality
- [ ] Change `spark.sql.shuffle.partitions` and re-measure a `groupBy`
- [ ] Build a view, a materialized view (with a schedule) and a streaming table
- [ ] Build a small declarative pipeline with `expect`, `expect_or_drop`, `expect_or_fail`; read the metrics
- [ ] Add a `CHECK` constraint and watch a bad insert fail

#### Day 21: Review
- [ ] Practice questions tagged Section 3

### Week 4: Lakeflow Jobs (Section 4)

#### Day 22-24: Tasks and control flow
- [ ] Read `notes/04-lakeflow-jobs.md`
- [ ] Build a job: notebook task, pipeline task, SQL task, dashboard task, with dependencies
- [ ] Set a task value and branch on it with If/else
- [ ] Add a For each with three inputs and concurrency 2
- [ ] Add a cleanup task with Run if All done; set retries on one task

#### Day 25-27: Triggers
- [ ] Try a scheduled trigger with cron, then a file arrival trigger on a volume, then a table update trigger
- [ ] Set wait after last change and minimum time between triggers; explain each
- [ ] Fail a task on purpose and use repair run

#### Day 28: Review
- [ ] Practice questions tagged Section 4

### Week 5: CI/CD, troubleshooting, governance (Sections 5-7)

#### Day 29-30: CI/CD
- [ ] Read `notes/05-cicd.md`
- [ ] Create a Git folder; branch, commit, push; open a pull request in the provider
- [ ] Install the CLI; `databricks bundle init`; add variables and `dev`/`prod` targets; `validate`, `deploy`, `run`, `destroy`

#### Day 31-32: Troubleshooting and optimization
- [ ] Read `notes/06-troubleshooting-monitoring-optimization.md`
- [ ] Read the stage summary metrics for a skewed join; spot Max vs Median
- [ ] Create a table with `CLUSTER BY`, change its keys, run `OPTIMIZE`; read about predictive optimization
- [ ] Read the job matrix view and a failed task's output

#### Day 33-34: Governance
- [ ] Read `notes/07-governance-security.md`
- [ ] Managed vs external table; drop and `UNDROP`; `SET MANAGED`
- [ ] Minimum grants for read and for write; `SHOW GRANTS`
- [ ] A row filter and a column mask with `is_account_group_member`; read one ABAC policy example

#### Day 35: Review
- [ ] Practice questions tagged Sections 5-7

### Week 6: Exam preparation

#### Day 36-38: Full review
- [ ] Reread every note's "How the exam tests this section"
- [ ] Review the fact sheet and the key differentiators table in `strategy.md`
- [ ] Work through `scenarios.md`

#### Day 39-41: Practice
- [ ] All practice questions, timed (2 minutes each)
- [ ] The five sample questions in the exam guide PDF
- [ ] Reread the notes for every section you missed

#### Day 42: Exam day
- [ ] Light review of the fact sheet only
- [ ] Stable internet, quiet room, ID ready for the proctor

## Study Tips

### Time allocation by section weight

| Section | Weight | Suggested hours |
|---|---|---|
| Data Transformation and Modeling | 22% | 12-14 |
| Data Ingestion and Loading | 21% | 11-13 |
| Working with Lakeflow Jobs | 16% | 8-10 |
| Governance and Security | 15% | 8-9 |
| Implementing CI/CD | 10% | 5-6 |
| Troubleshooting, Monitoring, and Optimization | 10% | 5-6 |
| Databricks Intelligence Platform | 6% | 3-4 |

### Recommended daily schedule
- **30 min** reading notes and docs
- **40 min** hands-on in Free Edition
- **15 min** flashcards or practice questions

### Key resources
- **[📖 Exam page](https://www.databricks.com/learn/certification/data-engineer-associate)** - format and weights
- **[📖 Exam guide PDF (May 2026)](https://www.databricks.com/sites/default/files/2026-05/databricks-certified-data-engineer-associate-exam-guide-may-2026-000.pdf)** - objectives and sample questions
- **[📖 Databricks Academy](https://www.databricks.com/learn)** - recommended courses
