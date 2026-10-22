---
last-updated: 2026-10-22
applies-to: Databricks Data Engineer Professional exam guide of 2026-10-09
difficulty: advanced
reading-time: 20 min
---

# Section 5: Monitoring and Alerting

Weighted 10% on both the exam page and the 2026-10-09 exam guide PDF. The objectives name five tools: system tables, the REST API / CLI / SDK, pipeline event logs, alerts, and the Lakeflow Jobs UI and API. The skill tested is picking the right one for the question asked: "how much did it cost", "who did what", "is the pipeline healthy", "tell me when it breaks".

Source: the **[📖 exam page](https://www.databricks.com/learn/certification/data-engineer-professional)** and its linked exam guide.

## Table of Contents

1. [Which tool answers which question](#which-tool-answers-which-question)
2. [System tables](#system-tables)
3. [REST API, CLI and SDK](#rest-api-cli-and-sdk)
4. [Pipeline event logs](#pipeline-event-logs)
5. [Alerts](#alerts)
6. [Monitoring in the Lakeflow Jobs UI and API](#monitoring-in-the-lakeflow-jobs-ui-and-api)
7. [How the exam tests this section](#how-the-exam-tests-this-section)

---

## Which tool answers which question

| Question | Tool |
|---|---|
| What did each job, pipeline or warehouse cost last month? | `system.billing.usage` joined with `system.billing.list_prices` |
| Who read or changed this table, and when? | `system.access.audit` |
| Which jobs fail most, and how long do runs take over time? | `system.lakeflow.*` tables, or the Jobs UI matrix view |
| Did today's pipeline drop rows for failing expectations? | Pipeline event log (`flow_progress` events) |
| Notify me when a metric crosses a threshold | A Databricks SQL alert on a query |
| A script or CI step must check run status | Jobs REST API, CLI or Python SDK |
| Why was this one query slow? | Query profile ([Section 6](06-cost-performance-optimisation.md#reading-the-query-profile)) |

---

## System tables

**System tables** are Databricks-managed Delta tables in the `system` catalog of Unity Catalog. They hold operational data for the whole account: billing, compute, audit, lineage, jobs, pipelines and queries. Because they are ordinary governed tables, you query them with SQL, build dashboards on them, join them, and grant access to them like any other table. An account admin must enable the schemas and grant access; most users see nothing until then.

The schemas the exam names:

| Schema | Key tables | Use |
|---|---|---|
| `system.billing` | `usage`, `list_prices` | DBU consumption per workload, with `usage_metadata` (job_id, warehouse_id, cluster_id, pipeline id) and `custom_tags`; join prices for cost |
| `system.compute` | `clusters`, `warehouses`, `node_types`, `node_timeline` | Configuration history and per-node utilisation (CPU, memory) of classic compute |
| `system.access` | `audit`, `table_lineage`, `column_lineage` | Who did what (audit events from every service) and data lineage |
| `system.lakeflow` | `jobs`, `job_tasks`, `job_run_timeline`, `job_task_run_timeline`, `pipelines` | Job and pipeline definitions and every run with timing and result |
| `system.query` | `history` | Every SQL warehouse and serverless query, with duration and rows |

```sql
-- Cost per job for last month
SELECT u.usage_metadata.job_id,
       SUM(u.usage_quantity * p.pricing.default) AS list_cost
FROM system.billing.usage u
JOIN system.billing.list_prices p
  ON u.sku_name = p.sku_name
 AND u.usage_start_time >= p.price_start_time
 AND (p.price_end_time IS NULL OR u.usage_start_time < p.price_end_time)
WHERE u.usage_date >= date_trunc('month', add_months(current_date(), -1))
  AND u.usage_date <  date_trunc('month', current_date())
  AND u.usage_metadata.job_id IS NOT NULL
GROUP BY ALL
ORDER BY list_cost DESC;

-- Failed job runs in the last 7 days
SELECT job_id, run_id, result_state, period_start_time, period_end_time
FROM system.lakeflow.job_run_timeline
WHERE result_state IN ('FAILED', 'TIMED_OUT')
  AND period_start_time > current_timestamp() - INTERVAL 7 DAYS;
```

Cost attribution works best with **tags**: put `custom_tags` (team, project, cost centre) on jobs, pipelines and compute, and they appear in `billing.usage`. Serverless workloads use **budget policies** to carry tags.

**[📖 System tables](https://docs.databricks.com/aws/en/admin/system-tables/)** - enabling, schemas, retention
**[📖 Billable usage system table](https://docs.databricks.com/aws/en/admin/system-tables/billing)** - columns and cost queries
**[📖 Jobs system tables](https://docs.databricks.com/aws/en/admin/system-tables/jobs)** - the `system.lakeflow` schema
**[📖 Audit log system table](https://docs.databricks.com/aws/en/admin/system-tables/audit-logs)** - service names and action names
**[📖 Compute system tables](https://docs.databricks.com/aws/en/admin/system-tables/compute)** - clusters, node timeline
**[📖 Query history system table](https://docs.databricks.com/aws/en/admin/system-tables/query-history)** - SQL query monitoring

---

## REST API, CLI and SDK

Everything in the UI is also an API. Programmatic monitoring is used in CI/CD, in external schedulers, and in custom alerting.

| Task | REST | CLI | Python SDK |
|---|---|---|---|
| List recent runs of a job | `GET /api/2.2/jobs/runs/list?job_id=...` | `databricks jobs list-runs --job-id 123` | `w.jobs.list_runs(job_id=123)` |
| Get one run's state and task results | `GET /api/2.2/jobs/runs/get?run_id=...` | `databricks jobs get-run 456` | `w.jobs.get_run(run_id=456)` |
| Get a task's output or error | `GET /api/2.2/jobs/runs/get-output` | `databricks jobs get-run-output <task_run_id>` | `w.jobs.get_run_output(...)` |
| Pipeline updates and events | `GET /api/2.0/pipelines/{id}/updates`, `/events` | `databricks pipelines list-updates`, `list-pipeline-events` | `w.pipelines.list_pipeline_events(...)` |

```python
from databricks.sdk import WorkspaceClient
w = WorkspaceClient()   # auth from env vars, a config profile, or the notebook context
for run in w.jobs.list_runs(job_id=123, limit=20):
    print(run.run_id, run.state.result_state, run.execution_duration)
```

A run's state has a `life_cycle_state` (PENDING, RUNNING, TERMINATED, SKIPPED, INTERNAL_ERROR) and, when finished, a `result_state` (SUCCESS, FAILED, TIMEDOUT, CANCELED...). Polling scripts check both.

**[📖 Databricks CLI](https://docs.databricks.com/aws/en/dev-tools/cli/)** - install, auth, command groups
**[📖 Databricks SDK for Python](https://docs.databricks.com/aws/en/dev-tools/sdk-python)** - `WorkspaceClient` and auth
**[📖 Jobs API](https://docs.databricks.com/api/jobs/v2/job)** - runs/list, runs/get, get-output

---

## Pipeline event logs

Every Lakeflow pipeline writes an **event log**: a Delta table of everything the pipeline did. It is where pipeline health and data-quality metrics live.

- Query it with the table-valued function `SELECT * FROM event_log('<pipeline-id>')` or `event_log(TABLE(catalog.schema.my_streaming_table))`.
- Or **publish** it to a named Unity Catalog table in the pipeline settings (`event_log: {catalog, schema, name}`), so other users and dashboards can read it with normal grants. Databricks recommends a view over it before granting access.

Important columns: `event_type`, `timestamp`, `level` (INFO, WARN, ERROR), `origin` (pipeline, update, flow), `message`, `error`, and `details` (a JSON payload per event type).

| `event_type` | Tells you |
|---|---|
| `create_update`, `update_progress` | An update started, and its state changes (RUNNING, COMPLETED, FAILED) |
| `flow_progress` | Per-flow state and metrics: rows written, backlog, and **data quality** (`details:flow_progress.data_quality.expectations`) |
| `flow_definition` | Inputs and outputs of each flow: lineage |
| `planning_information` | Why a materialized view was refreshed incrementally or fully |
| `cluster_resources` | Autoscaling decisions |
| `user_action` | Who started, stopped or edited the pipeline |

```sql
SELECT e.dataset, e.name AS expectation,
       SUM(e.passed_records) AS passed, SUM(e.failed_records) AS failed
FROM (
  SELECT explode(from_json(details:flow_progress.data_quality.expectations,
         'array<struct<name:string,dataset:string,passed_records:bigint,failed_records:bigint>>')) AS e
  FROM event_log('<pipeline-id>')
  WHERE event_type = 'flow_progress')
GROUP BY ALL;
```

Build a dashboard and an alert on this query, and you have data-quality monitoring for the pipeline.

**[📖 Monitor pipelines with the event log](https://docs.databricks.com/aws/en/ldp/monitor-event-logs)** - querying, publishing, event types, expectation metrics

---

## Alerts

The guide's phrase is "Databricks Lakehouse alerts". In the product, the general alerting feature is **Databricks SQL alerts**: an alert runs a SQL query on a schedule, evaluates a **condition** against the result (for example `failed > 0` or `cost > 5000`), and sends **notifications** (email, Slack, Teams, PagerDuty, webhook) when the state changes. States are `OK`, `TRIGGERED` and `ERROR`. Because the input is any SQL query, one mechanism covers everything the objective lists:

| Signal the objective names | Query the alert runs |
|---|---|
| Governed business metrics | A metric view or gold table (`MEASURE(net_revenue)` below target) |
| Data quality and anomaly signals | The pipeline event log; data quality monitoring output tables |
| Usage and cost | `system.billing.usage` (daily spend above budget) |
| SQL warehouse / query health | `system.query.history` (long-running or failed queries) |
| Audit and security events | `system.access.audit` (permission changes, failed logins, unusual downloads) |
| AI agent quality | Evaluation or inference tables of a served agent |
| Lakeflow Job branching | An **SQL alert task** inside a job; its result feeds an If/else condition so the job takes a different path when the alert fires |

**Data quality monitoring** (formerly Lakehouse Monitoring) adds automatic checks on Unity Catalog tables: **anomaly detection** for freshness and completeness across a schema, and **data profiling** that writes profile and drift metric tables you can alert on.

Separately, **job and pipeline notifications** (on start, success, failure, duration threshold exceeded, streaming backlog threshold) are configured on the job or pipeline itself, not as SQL alerts. "Tell the on-call engineer when the nightly job fails" is a job failure notification; "tell finance when spend crosses a budget" is a SQL alert on billing data.

**[📖 Databricks SQL alerts](https://docs.databricks.com/aws/en/sql/user/alerts/)** - conditions, schedules, notifications, states
**[📖 Data quality monitoring](https://docs.databricks.com/aws/en/data-governance/unity-catalog/data-quality-monitoring)** - anomaly detection and data profiling

---

## Monitoring in the Lakeflow Jobs UI and API

The **Jobs & Pipelines** page shows every job with its recent runs. Open a job and the **Runs** tab shows the **matrix view**: one column per run, one row per task, coloured by result. It is the fastest way to see "which task fails, and since when". Each run shows duration per task, so you can spot a task that is slowly getting longer (data growth) or suddenly much longer (skew, a bad join, a new upstream).

Per run you can open the task's output, logs, the Spark UI of the compute, and (for serverless and SQL) the query profile. Jobs can set **duration warning thresholds** and **streaming backlog** thresholds that send a notification before a hard timeout. The same data is available from `jobs/runs/list` and `runs/get` in the API, and historically from `system.lakeflow.job_run_timeline`.

**[📖 Monitoring and observability for Lakeflow Jobs](https://docs.databricks.com/aws/en/jobs/monitor)** - matrix view, run details, notifications

---

## How the exam tests this section

- **Cost questions** go to `system.billing.usage` joined to `list_prices`. Spark UI, driver logs and PDF exports are the distractors because they are not queryable governed tables.
- **"Who did it"** is `system.access.audit`.
- **Pipeline quality metrics** come from the event log, `flow_progress` events.
- **"Notify when..."**: job failure means job notifications; a threshold on data means a SQL alert.
- **Automation** means the REST API, CLI or SDK, not screen scraping the UI.

Continue with [Section 6: Cost & Performance Optimisation](06-cost-performance-optimisation.md).
