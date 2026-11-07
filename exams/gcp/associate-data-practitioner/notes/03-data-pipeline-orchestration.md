---
last-updated: 2026-11-07
applies-to: Google Cloud Associate Data Practitioner exam guide v1.0 (read 2026-11-07)
difficulty: beginner
reading-time: 20 min
---

# Section 3: Data Pipeline Orchestration

About **18%** of the exam, roughly 9 to 11 questions. Two objectives: 3.1 design and implement simple data pipelines (choose the transformation tool), and 3.2 schedule, automate and monitor basic data processing (choose the orchestration tool, watch the jobs, react to events).

## Table of Contents

1. [3.1 Design simple pipelines](#31-design-simple-pipelines)
2. [3.2 Schedule, automate and monitor](#32-schedule-automate-and-monitor)
3. [How the exam tests this section](#how-the-exam-tests-this-section)
4. [Key terms](#key-terms)

---

## 3.1 Design simple pipelines

### Two different jobs: transform and orchestrate

Keep this split in your head, because the exam mixes the tools on purpose. A **transformation tool** does the work on the data (clean, join, aggregate). An **orchestration tool** decides when each piece runs, in what order, and what happens when a piece fails. Dataflow transforms; Cloud Composer orchestrates. Some tools do a bit of both, and that is where wrong answers hide.

### Choosing the transformation tool

| Tool | What it is | Pick it when |
|---|---|---|
| **BigQuery SQL / scheduled queries** | SQL inside the warehouse | The data is in BigQuery and SQL is enough |
| **Dataform** | SQL workflows with `ref()` dependencies, assertions, Git | Repeatable ELT inside BigQuery with tests |
| **Dataflow** | Managed Apache Beam; batch and streaming | Streaming, big scale, non-SQL logic; Google templates need no Beam code |
| **Dataproc** | Managed Spark and Hadoop clusters | You already have Spark or Hadoop code to move |
| **Cloud Data Fusion** | Visual pipeline builder | No-code, many connectors |
| **Cloud Composer** | Managed Apache Airflow | Orchestration across steps (see 3.2) |

**[📖 Dataform overview](https://docs.cloud.google.com/dataform/docs/overview)** - SQLX files compile to standard SQL; `ref()` builds a dependency tree so actions run in the right order and circular dependencies are flagged at compile time; assertions check uniqueness or nulls after a table builds.
**[📖 Dataflow overview](https://docs.cloud.google.com/dataflow/docs/overview)** - fully managed; batch and streaming share one programming model; exactly-once processing by default; autoscaling adds and removes workers.

### ETL or ELT for a given use case

Apply the rule from Section 1. A concrete walk-through: an online shop wants yesterday's orders joined to customers and rolled up by region each morning. Orders and customers are already in BigQuery. A scheduled query or a Dataform workflow is the simplest correct answer; Dataflow would add a cluster of moving parts for no gain. Now change the story: clickstream events arrive continuously from Pub/Sub, must be parsed from JSON and windowed in five-minute buckets before being stored. That is Dataflow territory.

---

## 3.2 Schedule, automate and monitor

### Scheduled queries

A **BigQuery scheduled query** runs a GoogleSQL statement (including DDL and DML) on a recurring schedule. It is built on the BigQuery Data Transfer Service. Facts from the docs: schedules use cron-like text such as "every 24 hours"; the minimum interval is 5 minutes; queries can use the `@run_time` and `@run_date` parameters; results either overwrite (`WRITE_TRUNCATE`) or append (`WRITE_APPEND`); and a scheduled query can run as a user or a service account. A subtle gotcha: queries scheduled exactly on the hour can run more than once, which can duplicate `INSERT` results, so the docs suggest an off-hour time like 09:03.

**[📖 Scheduling queries](https://docs.cloud.google.com/bigquery/docs/scheduling-queries)** - parameters, write preferences, minimum interval and permissions

### Choosing the orchestration tool

| Situation | Tool |
|---|---|
| One recurring SQL statement in BigQuery | Scheduled query |
| Trigger a job at a time (HTTP target, simple cron) | **Cloud Scheduler** |
| Many steps with dependencies, retries, backfills, across systems | **Cloud Composer** |
| A handful of API/service calls in sequence, serverless, no Airflow | **Workflows** |
| Run a Dataproc job graph | Dataproc Workflow Templates |

**Cloud Composer** is managed Apache Airflow. You describe a workflow as a DAG (a directed acyclic graph of tasks) in Python; DAGs run on a schedule, manually, or on events. It is the heavy and flexible choice. **Workflows** is lighter: YAML or JSON steps that call Cloud Run, Cloud Run functions, Google APIs and other HTTP services, with retries, conditions, loops and parallel branches, serverless and no charge while idle.

**[📖 Cloud Composer overview](https://docs.cloud.google.com/composer/docs/composer-3/composer-overview)** - managed Airflow, DAGs in Python, environments run on GKE
**[📖 Workflows overview](https://docs.cloud.google.com/workflows/docs/overview)** - serverless orchestration of HTTP-based services; steps in YAML or JSON

Rule of thumb for the exam: pick the *simplest* tool that does the job. One SQL statement every night does not need Composer.

### Event-driven ingestion

Not everything runs on a clock. **Pub/Sub** carries messages from producers to subscribers. A **BigQuery subscription** writes messages straight into a BigQuery table with no subscriber code; delivery is at least once, so duplicates are possible. The docs say to use it when messages can be stored as they arrive with no significant processing, and to use Dataflow when you need complex transformation or windowing and aggregation across messages.

**[📖 Pub/Sub BigQuery subscriptions](https://docs.cloud.google.com/pubsub/docs/bigquery)** - direct writes to BigQuery, dead-letter handling, when Dataflow is the better choice

**Eventarc** routes events (a file landing in Cloud Storage, an audit log entry) to targets. Eventarc Standard destinations are Cloud Run services, Cloud Run functions (the current name for Cloud Functions), Workflows, GKE services and internal HTTP endpoints; Eventarc Advanced adds Cloud Run jobs, Pub/Sub topics and buses. In Eventarc Standard a trigger filters events by source and type and routes matches to one destination.

**[📖 Eventarc overview](https://docs.cloud.google.com/eventarc/docs/overview)** - providers, triggers and the Standard and Advanced destination lists

A common chain: a file arrives in a bucket, Eventarc fires, it starts a Cloud Run function or a Workflow, which kicks off a Dataform run or a Dataflow template.

### Monitoring

- **Dataflow job UI** shows a graphical pipeline view, stage progress and execution details; job metrics charts show data freshness, resource utilization and I/O requests. First stop when a pipeline is slow or stuck.
- **Cloud Logging** collects logs; filter by resource and severity to find errors.
- **Cloud Monitoring** charts metrics and drives alerting policies (for example, alert when a job fails or lag grows).

Think of Logging as "what happened" and Monitoring as "how healthy is it, and tell me when it is not".

---

## How the exam tests this section

- **Right-size the tool.** A single nightly SQL statement is a scheduled query, not Composer.
- **Transform vs orchestrate.** Dataflow does not schedule a multi-system workflow; Composer does not do the row-level work.
- **Streaming with processing: Dataflow.** Streaming with no processing: BigQuery subscription.
- **Events:** Eventarc to Cloud Run functions, Workflows or Cloud Run.
- **Debugging a slow Dataflow job:** the job UI first, then Logging.

## Key terms

- **Scheduled query** - a BigQuery feature that runs a GoogleSQL statement on a recurring schedule, built on the Data Transfer Service.
- **Cloud Scheduler** - a managed cron service that triggers HTTP targets, Pub/Sub topics or workflows at set times.
- **Cloud Composer** - Google's managed Apache Airflow service for orchestrating multi-step workflows defined as DAGs.
- **DAG** - a directed acyclic graph of tasks arranged by their dependencies; Airflow workflows are written as DAGs in Python.
- **Workflows** - a serverless orchestration service that runs YAML or JSON steps calling Google Cloud and HTTP-based services.
- **Dataform** - a BigQuery service for developing, testing, versioning and scheduling SQL transformation workflows.
- **Dataproc** - managed Spark and Hadoop clusters for running existing big data jobs on Google Cloud.
- **Pub/Sub** - a messaging service that decouples producers and consumers of event streams.
- **BigQuery subscription** - a Pub/Sub subscription type that writes messages directly into a BigQuery table, at least once.
- **Eventarc** - a serverless eventing service that routes events from sources to targets such as Cloud Run and Workflows.
- **Cloud Logging** - the service that collects and lets you query logs from Google Cloud resources.
- **Cloud Monitoring** - the service that charts metrics and sends alerts based on alerting policies.
