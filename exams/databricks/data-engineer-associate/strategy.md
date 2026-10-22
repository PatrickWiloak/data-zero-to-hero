---
last-updated: 2026-10-22
applies-to: Databricks exam page and exam guide of 2026-05-04
---

# Databricks Data Engineer Associate - Study Strategy

The exam follows the seven-section outline in effect since 2026-05-04. Weights: Platform 6%, Ingestion 21%, Transformation 22%, Lakeflow Jobs 16%, CI/CD 10%, Troubleshooting 10%, Governance 15%. Plan your hours roughly in proportion, but don't skip the small sections: CI/CD and troubleshooting are new and older courses barely cover them.

## Study Approach

### Phase 1: Platform and ingestion (Weeks 1-2)

**Goal:** know how the platform fits together, and get data in every way the exam names.

1. **Section 1 - Intelligence Platform** ([notes](notes/01-intelligence-platform.md))
   - Draw the control plane, compute plane and storage from memory
   - Explain what Delta Lake adds to Parquet, and what Unity Catalog governs
   - Practise the "which compute?" decision: SQL warehouse, serverless jobs, jobs cluster, all-purpose cluster

2. **Section 2 - Ingestion and Loading** ([notes](notes/02-data-ingestion-loading.md))
   - Load the same folder of JSON files with the upload UI, `COPY INTO` and Auto Loader; rerun each and see which reload
   - Add a new field to a file and watch each `schemaEvolutionMode` handle it; find the value in `_rescued_data`
   - Read the Lakeflow Connect docs for one SaaS and one database connector; know the connection, gateway and pipeline pieces
   - Read a table over JDBC with secrets and parallel partitions; call a public REST API from a notebook

3. **Resources**
   - **[📖 Databricks Free Edition](https://docs.databricks.com/aws/en/getting-started/free-edition)** - your practice workspace
   - **[📖 Auto Loader](https://docs.databricks.com/aws/en/ingestion/cloud-object-storage/auto-loader/)** - incremental file ingestion
   - **[📖 Lakeflow Connect](https://docs.databricks.com/aws/en/ingestion/lakeflow-connect/)** - managed connectors

### Phase 2: Transform and orchestrate (Weeks 3-4)

**Goal:** write and read transformation code fluently in both PySpark and SQL, then orchestrate it.

1. **Section 3 - Transformation and Modeling** ([notes](notes/03-data-transformation-modeling.md))
   - Build bronze to silver to gold for one dataset, once in PySpark and once in SQL
   - Practise every join type and `union` vs `unionByName` vs SQL `UNION`
   - Compare `countDistinct` and `approx_count_distinct` on a large table
   - Change `spark.sql.shuffle.partitions` and the broadcast threshold, and re-measure
   - Build one view, one materialized view and one streaming table; know when each fits
   - Add expectations with each action and read their metrics

2. **Section 4 - Lakeflow Jobs** ([notes](notes/04-lakeflow-jobs.md))
   - Build a job: pipeline task, then an If/else on a task value, then SQL and dashboard tasks
   - Add a For each over three inputs with concurrency 2
   - Switch the trigger between scheduled, file arrival and table update; break a task and repair the run

3. **Resources**
   - **[📖 Lakeflow Spark Declarative Pipelines](https://docs.databricks.com/aws/en/ldp/)** - streaming tables, materialized views, expectations
   - **[📖 Lakeflow Jobs](https://docs.databricks.com/aws/en/jobs/)** - tasks, control flow, triggers

### Phase 3: Ship, operate and govern (Week 5)

**Goal:** the newer sections, where older study material is thin.

1. **Section 5 - CI/CD** ([notes](notes/05-cicd.md))
   - Create a Git folder, branch, commit, push, and open a pull request in your Git provider
   - `databricks bundle init`, add a variable, add `dev` and `prod` targets, then `validate`, `deploy`, `run` and `destroy`

2. **Section 6 - Troubleshooting, Monitoring, Optimization** ([notes](notes/06-troubleshooting-monitoring-optimization.md))
   - Create a skewed join and read the stage summary metrics; turn AQE skew handling off and on
   - Create a table with `CLUSTER BY`, change its keys, run `OPTIMIZE`
   - Read the matrix view of a job with a failed task; read a cluster event log

3. **Section 7 - Governance and Security** ([notes](notes/07-governance-security.md))
   - Create a managed and an external table, drop both, `UNDROP` one, convert external to managed
   - Grant a group read access to one schema with the minimum privileges; check with `SHOW GRANTS`
   - Add a row filter and a column mask, then read the ABAC docs and one policy example

### Phase 4: Exam readiness (Week 6)

1. Reread each note's "How the exam tests this section" ending
2. Work through [scenarios.md](scenarios.md) and the [practice questions](../../../resources/practice-questions/databricks-data-engineer-associate.md)
3. Try the five sample questions in the exam guide PDF
4. Re-read the exam guide two weeks before your exam for any change

## Study Resources

### Official Databricks resources
- **[📖 Exam page](https://www.databricks.com/learn/certification/data-engineer-associate)** - format, weights, registration
- **[📖 Exam guide PDF (May 2026)](https://www.databricks.com/sites/default/files/2026-05/databricks-certified-data-engineer-associate-exam-guide-may-2026-000.pdf)** - objectives and sample questions
- **[📖 Databricks Academy](https://www.databricks.com/learn)** - Data Engineering with Databricks (instructor-led); self-paced Data Ingestion with Lakeflow Connect, Deploy Workloads with Lakeflow Jobs, DevOps Essentials for Data Engineering, Data Interoperability with Unity Catalog, Build Data Pipelines with Lakeflow Spark Declarative Pipelines, Get Started with Data Governance on Databricks
- **[📖 Databricks documentation](https://docs.databricks.com/aws/en/)** - the reference behind every note

### Documentation deep dives
- **[📖 Declarative Automation Bundles](https://docs.databricks.com/aws/en/dev-tools/bundles/)** - bundles end to end
- **[📖 Unity Catalog](https://docs.databricks.com/aws/en/data-governance/unity-catalog/)** - governance model
- **[📖 Spark UI guide](https://docs.databricks.com/aws/en/optimizations/spark-ui-guide/)** - diagnosing slow jobs

### Community resources
- **[📖 Databricks Community](https://community.databricks.com/)** - questions and answers
- **[📖 Apache Spark SQL guide](https://spark.apache.org/docs/latest/sql-programming-guide.html)** - Spark SQL and DataFrame reference

## Exam Tactics

### Question strategy
1. **Find the constraint word**: "least maintenance", "most cost-effective", "only when new data arrives", "without changing each table", "same code in every environment".
2. **Prefer the managed, built-in option** over custom code: managed connectors over API scripts, Auto Loader over hand-tracked files, bundles over clicking, ABAC over per-table rules.
3. **Translate old names** in options: DLT, Repos, DABs, Workflows, SQL endpoint, high-concurrency cluster.
4. **Eliminate by limitation**: serverless can't run init scripts; SQL warehouses can't run Python; `REVOKE` on a child can't cancel a parent grant; Unity Catalog has no `DENY` statement.

### Time management
- **45 questions in 90 minutes** = 2 minutes per question
- **First pass (60 minutes):** answer what you know, flag the rest
- **Second pass (20 minutes):** flagged questions
- **Final check (10 minutes):** nothing left blank (there's no penalty for guessing)

### Key differentiators
These pairs are easy to confuse and often tested:

| Concept A | Concept B | Key difference |
|---|---|---|
| Auto Loader | `COPY INTO` | Streaming source, millions of files, rescued data vs one idempotent SQL statement for thousands of files |
| Managed connector | JDBC / REST in a notebook | Configured, CDC and schema changes handled vs code you maintain, misses deletes |
| `addNewColumns` | `rescue` | Stops once and adds the column vs never changes the schema, keeps data in `_rescued_data` |
| View | Materialized view | Recomputed on every read vs stored and refreshed |
| Streaming table | Materialized view | Append-only, each row once vs always-correct result of a query |
| PySpark `union` | SQL `UNION` | Keeps duplicates vs removes them |
| Scheduled trigger | File arrival / table update | Runs on time vs runs when data changes |
| If/else | For each | Branch on a condition vs loop over items |
| Git folders | Bundles | Version code in the workspace vs define and deploy resources across environments |
| Liquid Clustering | Partitioning / Z-order | Change keys without rewrite, incremental vs fixed layout, full rewrites |
| Managed table | External table | Data deleted on drop, auto-maintained vs data kept on drop, path you control |
| Row filter / column mask | ABAC policy | Per table vs per catalog or schema by tag, includes future tables |

### Common pitfalls
- Picking "add more workers" for skew; it doesn't split the hot partition
- Running production jobs on an all-purpose cluster
- Forgetting `USE CATALOG` and `USE SCHEMA` in minimum-privilege answers
- Scheduling a job "a bit after the upstream usually finishes" instead of a table update trigger
- Writing a nightly `OPTIMIZE`/`VACUUM` job for managed tables that predictive optimization already maintains
