---
last-updated: 2026-10-22
applies-to: Databricks exam page and exam guide of 2026-10-09
---

# Databricks Data Engineer Professional - Study Plan

An eight-week plan for the exam version published 2026-10-09, at about 1-2 hours a day. Every week mixes reading (the notes and the linked docs) with hands-on work in a Databricks workspace; [Databricks Free Edition](https://www.databricks.com/learn/free-edition) is enough for nearly all of it. Section numbers and weights follow the [exam page](https://www.databricks.com/learn/certification/data-engineer-professional).

## 8-Week Study Schedule

### Week 1: Projects, libraries, UDFs and tests (Section 1, part 1)

#### Day 1-2: Bundle project structure
- [ ] Review Notes: `notes/01-developing-code-python-sql.md` (project structure, libraries)
- [ ] Run `databricks bundle init default-python`; read every generated file
- [ ] Move one transformation into `src/` as a pure function; deploy to a dev target
- [ ] Read: [Declarative Automation Bundles](https://docs.databricks.com/aws/en/dev-tools/bundles/)

#### Day 3-4: Libraries and UDFs
- [ ] Install a pinned PyPI package on serverless via the Environment panel; then a wheel from a volume
- [ ] Write the same logic as a SQL UDF, a Python UDF, a pandas UDF and a Unity Catalog Python function; compare run times
- [ ] Read: [User-defined functions](https://docs.databricks.com/aws/en/udf/)

#### Day 5-6: Testing
- [ ] Write pytest tests with `assertDataFrameEqual` and `assertSchemaEqual`; chain functions with `DataFrame.transform`
- [ ] Run them through Databricks Connect or a local Spark session
- [ ] Read: [Unit testing for notebooks](https://docs.databricks.com/aws/en/notebooks/testing)

#### Day 7: Week 1 review
- [ ] Explain, without notes, when to pick each UDF type and why notebook `%pip` installs do not follow code into jobs

### Week 2: Pipelines, CDC, jobs, compute and streaming (Section 1, part 2)

#### Day 8-9: Lakeflow pipelines and AUTO CDC
- [ ] Build Auto Loader bronze -> silver with expectations -> gold materialized view
- [ ] Apply a CDC feed with AUTO CDC as SCD Type 1, then Type 2; send an out-of-order event and confirm `sequence_by` handles it
- [ ] Write down the streaming table vs materialized view decision table from memory
- [ ] Read: [AUTO CDC APIs](https://docs.databricks.com/aws/en/ldp/cdc)

#### Day 10-11: Lakeflow Jobs and serverless settings
- [ ] Build a job with an If/else task and a For each task over three table names; run it from the UI and the CLI
- [ ] Find the performance mode, environment, high memory and auto-optimization settings on a serverless job
- [ ] Read: [Run jobs with serverless compute](https://docs.databricks.com/aws/en/jobs/run-serverless-jobs)

#### Day 12-13: Stateful streaming
- [ ] Run a windowed aggregation with a watermark in append mode; stop and restart from the checkpoint
- [ ] Write a `foreachBatch` `MERGE`; explain why it is idempotent
- [ ] Read: [Watermarks](https://docs.databricks.com/aws/en/structured-streaming/watermarks) and [Output modes](https://docs.databricks.com/aws/en/structured-streaming/output-mode)

#### Day 14: Week 2 review
- [ ] Answer: when would you choose Structured Streaming in a job over a declarative pipeline?

### Week 3: Ingestion and transformation (Sections 2 and 3)

#### Day 15-16: Ingestion
- [ ] Review Notes: `notes/02-data-ingestion-acquisition.md`
- [ ] Ingest JSON, CSV and binary files; try `addNewColumns` and `rescue` schema evolution
- [ ] Read the Lakeflow Connect pages for SQL Server, MySQL and PostgreSQL; list each source prerequisite
- [ ] Read: [Auto Loader schema evolution](https://docs.databricks.com/aws/en/ingestion/cloud-object-storage/auto-loader/schema)

#### Day 17-18: Transformations and VARIANT
- [ ] Review Notes: `notes/03-data-transformation-quality.md`
- [ ] Dedup with `row_number` and with `QUALIFY`; compute a running total and a `lag` delta
- [ ] Parse JSON to `VARIANT`; extract with `variant_get`, `try_variant_get` and colon paths
- [ ] Read: [Query variant data](https://docs.databricks.com/aws/en/semi-structured/variant)

#### Day 19-20: AI functions and quality
- [ ] Run `ai_classify` and `ai_query` over a small text table
- [ ] Build a quarantine table beside a cleaned table using inverted expectations
- [ ] Read: [Pipeline expectations](https://docs.databricks.com/aws/en/ldp/expectations)

#### Day 21: Week 3 review
- [ ] Practice questions tagged Domain 1-3

### Week 4: Cost and performance (Section 6)

#### Day 22-23: Managed tables and clustering
- [ ] Review Notes: `notes/06-cost-performance-optimisation.md`
- [ ] Create the same table partitioned and liquid-clustered; compare files read in the query profile
- [ ] Change clustering keys with `ALTER TABLE ... CLUSTER BY`; try `CLUSTER BY AUTO`
- [ ] Read: [Liquid clustering](https://docs.databricks.com/aws/en/tables/clustering) and [Predictive optimization](https://docs.databricks.com/aws/en/optimizations/predictive-optimization)

#### Day 24-25: Deletion vectors, cache and CDF
- [ ] Run a scattered `MERGE` with and without deletion vectors; read `DESCRIBE HISTORY` metrics
- [ ] Enable change data feed; read it with `table_changes` and as a stream
- [ ] Read: [Deletion vectors](https://docs.databricks.com/aws/en/tables/features/deletion-vectors)

#### Day 26-27: Query profile
- [ ] Find files pruned, join type, exchange size and spill on three of your own queries
- [ ] Read: [Query profile](https://docs.databricks.com/aws/en/sql/user/queries/query-profile)

#### Day 28: Week 4 review
- [ ] Practice questions tagged Domain 6

### Week 5: Monitoring, debugging and deploying (Sections 5 and 9)

#### Day 29-30: Monitoring
- [ ] Review Notes: `notes/05-monitoring-alerting.md`
- [ ] Write the cost-per-job query on `system.billing.usage`; find failed runs in `system.lakeflow.job_run_timeline`
- [ ] Extract expectation metrics from a pipeline event log; create a SQL alert on it
- [ ] List runs with the CLI and the Python SDK
- [ ] Read: [System tables](https://docs.databricks.com/aws/en/admin/system-tables/)

#### Day 31-32: Debugging and repair
- [ ] Review Notes: `notes/09-debugging-deploying.md`
- [ ] Find skew and spill in the Spark UI Stages tab for a skewed join
- [ ] Make a task fail on a bad parameter; repair the run with an override
- [ ] Read: [Repair job failures](https://docs.databricks.com/aws/en/jobs/repair-job-failures)

#### Day 33-34: Deployment
- [ ] Add a `prod` target with `mode: production`, variables and a service principal `run_as`; run `bundle validate` and `bundle plan`
- [ ] Clone the repo into a Git folder; branch, commit and push from the workspace
- [ ] Read: [Bundle deployment modes](https://docs.databricks.com/aws/en/dev-tools/bundles/deployment-modes)

#### Day 35: Week 5 review
- [ ] Practice questions tagged Domain 5 and 9

### Week 6: Security, governance, sharing and modelling (Sections 4, 7, 8, 10)

#### Day 36-37: Security and compliance
- [ ] Review Notes: `notes/07-security-compliance.md`
- [ ] Add a row filter and a column mask; then replace the mask with an ABAC policy on a governed tag
- [ ] Hash a column with a salt; generalise a date to a year
- [ ] Walk through an erasure: `DELETE`, `REORG TABLE ... APPLY (PURGE)`, `VACUUM`
- [ ] Read: [ABAC policies](https://docs.databricks.com/aws/en/data-governance/unity-catalog/abac/policies)

#### Day 38: Governance
- [ ] Review Notes: `notes/08-data-governance.md`
- [ ] Grant `SELECT` at schema level, create a new table, confirm access; add tags and comments; query `information_schema.column_tags`

#### Day 39: Sharing and federation
- [ ] Review Notes: `notes/04-data-sharing-federation.md`
- [ ] Create a share and a recipient (D2D if you have two metastores, otherwise read the D2O flow); create a connection and foreign catalog if you have a source database
- [ ] Read: [OpenSharing](https://docs.databricks.com/aws/en/opensharing)

#### Day 40-41: Data modelling
- [ ] Review Notes: `notes/10-data-modelling.md`
- [ ] Design a star schema at a stated grain; build a materialized view and a metric view over it; query with `MEASURE()`
- [ ] Read: [Metric views](https://docs.databricks.com/aws/en/uc-semantics/metric-views)

#### Day 42: Week 6 review
- [ ] Practice questions tagged Domain 4, 7, 8 and 10

### Week 7: Scenarios and weak areas

#### Day 43-45: Scenarios
- [ ] Work through [scenarios.md](scenarios.md) and the ten sample questions in the exam guide PDF
- [ ] For each miss, note the section and the decision it tested

#### Day 46-49: Weak areas
- [ ] Reread the notes for your two weakest sections; redo their hands-on tasks

### Week 8: Final preparation

#### Day 50-52: Timed practice
- [ ] Full timed run of the [practice questions](../../../resources/practice-questions/databricks-data-engineer-professional.md) (about 2 minutes each)
- [ ] Review every wrong answer against the docs

#### Day 53-55: Final review and exam
- [ ] Reread the fact sheet key facts and the "How the exam tests this section" part of each note
- [ ] Check the exam page and guide again for changes (Databricks asks for this two weeks before the exam)
- [ ] Run the system check for online proctoring, or book a test center

## Study Tips

### Time Allocation by Section Weight

| Section | Weight (page) | Suggested share of study time |
|---------|---------------|-------------------------------|
| 1 Developing Code | 22% | 25% |
| 6 Cost & Performance | 13% | 13% |
| 3 Transformation and Quality | 10% | 10% |
| 5 Monitoring and Alerting | 10% | 9% |
| 7 Security and Compliance | 10% | 10% |
| 9 Debugging and Deploying | 10% | 10% |
| 2 Ingestion | 7% | 8% |
| 8 Governance | 7% | 5% |
| 10 Data Modelling | 6% | 5% |
| 4 Sharing and Federation | 5% | 5% |

### Recommended Daily Schedule
- **30 minutes:** read the note section and its linked docs
- **45-60 minutes:** hands-on in a workspace
- **15 minutes:** practice questions or flashcard review of the day's decisions
