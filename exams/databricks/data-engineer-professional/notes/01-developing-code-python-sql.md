---
last-updated: 2026-10-22
applies-to: Databricks Data Engineer Professional exam guide of 2026-10-09
difficulty: advanced
reading-time: 35 min
---

# Section 1: Developing Code for Data Processing using Python and SQL

This is the biggest section of the exam: 22% on the exam page, 23% in the 2026-10-09 exam guide PDF. It is also the broadest. It asks whether you can structure a Python project for deployment, keep its libraries working, choose the right kind of function and pipeline object, build and orchestrate jobs, pick compute, test your code, and reason about stateful streaming. Every objective below is from the **[📖 exam page](https://www.databricks.com/learn/certification/data-engineer-professional)** and the exam guide PDF it links.

A naming note before you start, because the exam uses the newest names and older study material uses the old ones:

| Exam guide says | You may also see | What it is |
|---|---|---|
| Lakeflow Pipelines / Lakeflow Spark Declarative Pipelines | Delta Live Tables (DLT) | Declarative pipeline framework |
| Apache Spark Declarative Pipelines | - | The open-source core of the above, in Apache Spark 4.1+ |
| AUTO CDC | APPLY CHANGES | CDC flow inside a pipeline; same syntax, new keyword |
| Lakeflow Jobs | Databricks Workflows, Jobs | Orchestrator |
| Declarative Automation Bundles | Databricks Asset Bundles (DABs) | Infrastructure-as-code for workspace resources; renamed March 2026, CLI and `databricks.yml` unchanged |
| Git folders | Repos | Git-backed workspace folders |
| OpenSharing | Delta Sharing | Data sharing protocol (Section 4) |

## Table of Contents

1. [Python project structure for bundles](#python-project-structure-for-bundles)
2. [Troubleshooting library installs](#troubleshooting-library-installs)
3. [Choosing a UDF](#choosing-a-udf)
4. [Streaming pipelines with Lakeflow Pipelines and Auto Loader](#streaming-pipelines-with-lakeflow-pipelines-and-auto-loader)
5. [Streaming table or materialized view](#streaming-table-or-materialized-view)
6. [CDC with AUTO CDC, SCD Type 1 and Type 2](#cdc-with-auto-cdc-scd-type-1-and-type-2)
7. [Structured Streaming or Declarative Pipelines](#structured-streaming-or-declarative-pipelines)
8. [Lakeflow Jobs: UI, API, CLI and control flow](#lakeflow-jobs-ui-api-cli-and-control-flow)
9. [Choosing compute and configuration](#choosing-compute-and-configuration)
10. [Unit and integration tests](#unit-and-integration-tests)
11. [Stateful Structured Streaming semantics](#stateful-structured-streaming-semantics)
12. [How the exam tests this section](#how-the-exam-tests-this-section)

---

## Python project structure for bundles

A notebook is a fine place to explore. It is a poor unit of deployment: it cannot be imported cleanly, it is hard to unit test, and copying it between workspaces drifts. The exam's answer is a Python project packaged and deployed by a **Declarative Automation Bundle**.

The default template (`databricks bundle init default-python`) gives you a layout like this:

```text
my_project/
├── databricks.yml          # bundle name, variables, targets (dev, prod)
├── resources/
│   ├── my_job.job.yml      # job definition
│   └── my_pipeline.pipeline.yml
├── src/
│   └── my_project/
│       ├── __init__.py
│       ├── transforms.py   # pure functions: DataFrame in, DataFrame out
│       └── main.py         # entry point for a wheel task
├── tests/
│   └── test_transforms.py
└── pyproject.toml          # package metadata and dependencies
```

Three ideas make this "scalable and modular":

- **Business logic lives in importable modules, not notebooks.** `transforms.py` holds functions that take and return DataFrames. Notebooks and pipeline source files become thin callers. That is what makes the logic testable (see [unit tests](#unit-and-integration-tests)).
- **The package is built as an artifact.** The bundle's `artifacts` section builds a Python wheel from `pyproject.toml` on every deploy, and a job task references it as a `python_wheel_task` with `package_name` and `entry_point`. Every environment runs the same built code.
- **Resources are split into files and pulled in with `include`.** `databricks.yml` lists `include: [resources/*.yml]`, so each job or pipeline has its own file and code review stays readable.

CI/CD integration follows naturally: a CI runner installs the Databricks CLI, runs `databricks bundle validate`, runs the tests, then `databricks bundle deploy -t staging` or `-t prod`. Deployment itself is covered in [Section 9](09-debugging-deploying.md#deploying-with-declarative-automation-bundles).

**[📖 Declarative Automation Bundles](https://docs.databricks.com/aws/en/dev-tools/bundles/)** - what bundles are, the CLI workflow
**[📖 Bundle templates](https://docs.databricks.com/aws/en/dev-tools/bundles/templates)** - `bundle init` and the default Python template
**[📖 Build a Python wheel with a bundle](https://docs.databricks.com/aws/en/dev-tools/bundles/python-wheel)** - artifacts and wheel tasks

---

## Troubleshooting library installs

The objective names three library sources (PyPI, local wheels, source archives) and three environments (serverless, pipelines, bundle-deployed). The skill is knowing where each environment gets its libraries and why an install fails.

**Where libraries come from in each environment:**

| Environment | How you add a library | Notes |
|---|---|---|
| Notebook on classic compute | `%pip install pkg==1.2.3` (notebook-scoped), or a compute-scoped library | `%pip` restarts Python; use `%restart_python` after installs that replace preinstalled packages |
| Serverless notebook or job | The **Environment** side panel or the task's `environments` spec: an environment version plus a dependency list | No init scripts and no compute-scoped libraries on serverless; dependencies are per environment |
| Lakeflow pipeline | The pipeline's environment dependencies (serverless) or `%pip install` at the top of a source notebook (classic) | All pipeline source files share one Python environment |
| Bundle-deployed job | `libraries:` on the task (classic) or `environments:` with `dependencies:` (serverless), often pointing at the wheel the bundle just built | Paths to local wheels resolve relative to the bundle root and are uploaded on deploy |

**Why installs fail, and the fix:**

- **Version conflict with a preinstalled package.** The Databricks Runtime and serverless environment versions ship pinned packages (pandas, numpy, pyarrow and others). Your requirement asks for an incompatible version and pip either fails resolution or silently replaces a package something else needs. Fix: pin a version compatible with the runtime's release notes, or move to a newer runtime or environment version that already has it.
- **Package not reachable.** The compute has no outbound internet, or the company uses a private mirror. Fix: point pip at the private index (an `--index-url` line in the dependency list or a workspace-level default index), or install a wheel from a Unity Catalog volume or workspace file.
- **Source archive with no build tools.** A `.tar.gz` source distribution must be built on install. If it has C extensions and the build toolchain or system headers are missing, the install fails where a prebuilt wheel would have worked. Fix: build a wheel once (in CI) and install the wheel.
- **Wrong architecture or Python version.** A wheel tagged `cp310` will not install on Python 3.12. Fix: rebuild for the runtime's Python version.
- **It works in the notebook but fails in the job.** Notebook-scoped `%pip` installs do not follow the code into a job on different compute. Fix: declare the dependency on the task or environment, not in the session.

**[📖 Libraries](https://docs.databricks.com/aws/en/libraries/)** - library sources and scopes
**[📖 Notebook-scoped Python libraries](https://docs.databricks.com/aws/en/libraries/notebooks-python-libraries)** - `%pip` behaviour and restarts
**[📖 Configure the serverless environment](https://docs.databricks.com/aws/en/compute/serverless/dependencies)** - environment versions and dependency lists

---

## Choosing a UDF

A user-defined function is the last resort, not the first. Built-in Spark SQL functions run inside the engine (and on Photon); any UDF adds overhead. When a built-in will not do, there are four kinds:

| Kind | How it runs | Use it when |
|---|---|---|
| **SQL UDF** (`CREATE FUNCTION ... RETURN expr`) | Inlined into the query plan, so it optimises like a built-in | The logic is expressible in SQL and you want reuse and governance |
| **Python UDF** (`@udf`) | One row at a time, serialised between the JVM and a Python worker | Logic needs Python and data volume is small; slowest option |
| **pandas UDF** (`@pandas_udf`) | Vectorised: batches of rows move as Apache Arrow and arrive as pandas Series | Python logic over large data; often 10x or more faster than a row UDF |
| **Unity Catalog function** (`CREATE FUNCTION ... LANGUAGE PYTHON AS $$...$$`, or a SQL UDF in a schema) | Registered in a catalog and schema, governed by `EXECUTE` privilege | Shared, governed logic that many users and pipelines call, and that row filters, column masks or ABAC policies reference |

```python
import pandas as pd
from pyspark.sql.functions import pandas_udf

@pandas_udf("double")
def to_celsius(f: pd.Series) -> pd.Series:
    return (f - 32) * 5.0 / 9.0

df = df.withColumn("temp_c", to_celsius("temp_f"))
```

```sql
CREATE OR REPLACE FUNCTION main.utils.mask_email(email STRING)
RETURNS STRING
RETURN concat('***@', split_part(email, '@', 2));

CREATE OR REPLACE FUNCTION main.utils.risk_band(score DOUBLE)
RETURNS STRING
LANGUAGE PYTHON
AS $$
  return "high" if score > 0.8 else "low"
$$;
```

"For the given constraints" is the key phrase. Read the question for the constraint and map it:

- "Must be reusable across workspaces and governed" -> Unity Catalog function.
- "Used as a column mask or row filter" -> Unity Catalog (SQL) function; masks and filters require one.
- "Python logic, millions of rows, performance matters" -> pandas UDF.
- "Logic is a few SQL expressions" -> SQL UDF; it is inlined and costs almost nothing.
- "Needs a Python library such as a tokeniser" -> pandas UDF or a Unity Catalog Python function with declared dependencies.

**[📖 User-defined functions](https://docs.databricks.com/aws/en/udf/)** - the UDF types compared
**[📖 pandas UDFs](https://docs.databricks.com/aws/en/udf/pandas)** - Series-to-Series, iterator and grouped variants
**[📖 Unity Catalog UDFs](https://docs.databricks.com/aws/en/udf/unity-catalog)** - governed Python and SQL functions

---

## Streaming pipelines with Lakeflow Pipelines and Auto Loader

A **Lakeflow Spark Declarative Pipeline** is a set of dataset definitions (streaming tables, materialized views, views) plus the flows that fill them. You say what each table should contain; the pipeline works out the dependency graph, creates the tables, manages checkpoints, retries failures, records lineage and data-quality metrics, and scales compute. You do not write `writeStream`, choose checkpoint paths or order the steps.

A typical production streaming pipeline is Auto Loader into bronze, cleaned into silver with expectations, and aggregated into gold:

```python
from pyspark import pipelines as dp
from pyspark.sql.functions import col, current_timestamp

@dp.table(comment="Raw orders, one row per file record")
def orders_bronze():
    return (spark.readStream.format("cloudFiles")
            .option("cloudFiles.format", "json")
            .option("cloudFiles.schemaHints", "amount DECIMAL(12,2)")
            .load("/Volumes/main/raw/orders/")
            .withColumn("ingested_at", current_timestamp()))

@dp.table
@dp.expect_or_drop("valid_amount", "amount >= 0")
def orders_silver():
    return spark.readStream.table("orders_bronze").where(col("order_id").isNotNull())

@dp.materialized_view
def daily_revenue():
    return (spark.read.table("orders_silver")
            .groupBy("order_date").sum("amount"))
```

The SQL equivalent uses `CREATE OR REFRESH STREAMING TABLE ... AS SELECT * FROM STREAM read_files(...)` and `CREATE OR REFRESH MATERIALIZED VIEW`. Older material uses `import dlt` and `@dlt.table`; the current module is `from pyspark import pipelines as dp`.

What makes it "production-ready" in an exam answer:

- **Auto Loader** for files, so each file is processed once and the schema is tracked (see [Section 2](02-data-ingestion-acquisition.md#auto-loader-in-depth)).
- **Expectations** at the silver boundary (see [Section 3](03-data-transformation-quality.md#data-quality-expectations)).
- **Triggered or continuous mode** chosen for the latency need. Triggered runs process what is available then stop, which is cheaper; continuous keeps the pipeline running for low latency.
- **Serverless** pipelines, which also enable incremental refresh of materialized views.
- **Production mode** in the pipeline settings, which restarts on recoverable errors and retries with backoff; development mode reuses the cluster and does not retry, for fast iteration.

**[📖 Lakeflow Spark Declarative Pipelines](https://docs.databricks.com/aws/en/ldp/)** - concepts and how-tos
**[📖 Flows](https://docs.databricks.com/aws/en/ldp/concepts/flows)** - how data moves into pipeline datasets
**[📖 Python language reference](https://docs.databricks.com/aws/en/ldp/developer/python-ref)** - `dp.table`, `dp.materialized_view`, expectations

---

## Streaming table or materialized view

This objective is in the exam guide's own sample questions, so expect it. The two objects behave very differently.

A **streaming table** reads an append-only source incrementally. Each input row is processed **once**, when it arrives. If an upstream row is later updated or deleted, the streaming table does not go back and change what it already produced. It is ideal for ingestion and for low-latency, append-style transformations, and it is cheap because it only ever touches new data.

A **materialized view** stores the result of a query and keeps it **correct** as the sources change. On refresh, it either recomputes or (on serverless) incrementally updates only what changed, including updates, deletes and late-arriving rows for earlier days. It is ideal for aggregations, joins and anything the business reads as "the current truth".

| Requirement in the question | Pick |
|---|---|
| Ingest files or a message bus with low latency | Streaming table |
| Each source row processed exactly once; source is append-only | Streaming table |
| Aggregate must reflect late updates to past periods | Materialized view |
| Joins between tables that both change | Materialized view |
| Dashboards read on a schedule; refresh hourly is fine | Materialized view |
| Source has updates and deletes, and you want the latest state per key | AUTO CDC into a streaming table |

The trap answer is usually "a streaming table because it is cheapest". It is cheapest, but it will be wrong the moment a past row changes. The other trap is "a materialized view cannot aggregate": aggregation is what it is for.

**[📖 Streaming tables](https://docs.databricks.com/aws/en/ldp/concepts/streaming-tables)** - append-only incremental processing
**[📖 Materialized views](https://docs.databricks.com/aws/en/ldp/concepts/materialized-views)** - refresh semantics and incremental refresh

---

## CDC with AUTO CDC, SCD Type 1 and Type 2

Hand-written CDC with `MERGE` has three hard problems: out-of-order events, duplicate events, and deletes. **AUTO CDC** (formerly `APPLY CHANGES`, same syntax) solves all three declaratively. You give it the key, the column that orders events, and how to recognise a delete. It handles ordering by `sequence_by`, so a late-arriving older event never overwrites a newer one.

```python
from pyspark import pipelines as dp
from pyspark.sql.functions import col, expr

dp.create_streaming_table("customers")

dp.create_auto_cdc_flow(
    target="customers",
    source="customers_cdc_feed",
    keys=["customer_id"],
    sequence_by=col("event_ts"),
    apply_as_deletes=expr("op = 'DELETE'"),
    except_column_list=["op", "event_ts"],
    stored_as_scd_type=2,
)
```

```sql
CREATE OR REFRESH STREAMING TABLE customers;

CREATE FLOW customers_cdc AS AUTO CDC INTO customers
FROM STREAM(customers_cdc_feed)
KEYS (customer_id)
APPLY AS DELETE WHEN op = 'DELETE'
SEQUENCE BY event_ts
COLUMNS * EXCEPT (op, event_ts)
STORED AS SCD TYPE 2;
```

**SCD Type 1** (`stored_as_scd_type=1`, the default) keeps only the latest version of each key. An update overwrites; a delete removes the row.

**SCD Type 2** keeps history. Each version of a key is a row, and the pipeline adds `__START_AT` and `__END_AT` columns (populated from the `sequence_by` values). The current version has a null `__END_AT`. Two refinements matter:

- `track_history_column_list` / `track_history_except_column_list` choose which column changes create a new version. If only `last_login` changed and you do not track it, no new history row is written; the current row is updated in place.
- A delete closes the current version (sets `__END_AT`) rather than erasing history.

Use AUTO CDC when the source is a change feed (Debezium, Lakeflow Connect, a Delta change data feed, CDC files). Use a hand-written `MERGE` in `foreachBatch` only when you need logic AUTO CDC cannot express.

**[📖 The AUTO CDC APIs](https://docs.databricks.com/aws/en/ldp/cdc)** - Python and SQL syntax, SCD Type 1 and 2, history tracking

---

## Structured Streaming or Declarative Pipelines

Both run on the same Spark Structured Streaming engine. The choice is about how much you manage yourself.

| Choose Declarative Pipelines when | Choose plain Structured Streaming when |
|---|---|
| You want dependencies, checkpoints, retries and table creation handled for you | You need full control of the query lifecycle and checkpoint locations |
| Data quality expectations and lineage are requirements | You need arbitrary stateful logic (`transformWithState`) or custom sources |
| CDC with SCD Type 1 or 2 (AUTO CDC) | You write to an external system with custom logic in `foreachBatch` across many sinks |
| A team wants SQL-first, declarative ETL | The workload is a single specialised streaming job already written in Spark |
| Materialized views with incremental refresh are useful | You need real-time mode or very fine trigger tuning |

The gap is narrower than it used to be: pipelines now support **sinks** (Kafka, external Delta tables) and a `foreach_batch_sink`. But "operational constraints" in a question still steer the answer: a small team that wants less to operate points to pipelines; a hard requirement for custom state or a non-Delta target points to Structured Streaming in a job.

**[📖 Pipeline sinks](https://docs.databricks.com/aws/en/ldp/concepts/sinks)** - writing pipeline output to external targets
**[📖 Structured Streaming in production](https://docs.databricks.com/aws/en/structured-streaming/production)** - running streams yourself

---

## Lakeflow Jobs: UI, API, CLI and control flow

A **Lakeflow Job** is a DAG of tasks with a trigger. Task types include notebook, Python script, Python wheel, SQL (query, file, dashboard, alert), pipeline, dbt, JAR, Run Job (call another job), and the control-flow tasks below. Tasks pass data with **task values** (`dbutils.jobs.taskValues.set` / `get`) and read **job parameters** through dynamic references such as `{{job.parameters.run_date}}`.

**Three ways to create and automate the same job:**

- **UI:** build the DAG on the Tasks tab, set the trigger (scheduled, file arrival, table update, continuous) and notifications.
- **REST API:** `POST /api/2.2/jobs/create` with a JSON job spec; `POST /api/2.2/jobs/run-now` to trigger with parameter overrides.
- **CLI:** `databricks jobs create --json @job.json`, `databricks jobs run-now <job_id>`, or (the recommended path) define the job in a bundle and `databricks bundle deploy`, then `databricks bundle run my_job`.

**Control flow:**

- **If/else condition task** evaluates a comparison (`==`, `!=`, `>`, `>=`, `<`, `<=`) between two operands, usually a task value or job parameter and a constant. Downstream tasks depend on its `true` or `false` outcome. Example: run the full reload branch only when `{{tasks.check.values.row_count}} > 1000000`.
- **For each task** runs one nested task once per element of an input array (a JSON list or a task value), with a configurable **concurrency**. Example: ingest each of 40 source tables with the same notebook, 8 at a time, passing `{{input}}` as the table name.
- **Run if** dependencies decide when a task runs relative to its upstreams: `All succeeded` (default), `At least one succeeded`, `None failed`, `All done`, `At least one failed`, `All failed`. "Run a cleanup task even if something failed" is `All done`.
- **Retries and timeouts** are per task; a task can retry N times with a minimum interval.

```json
{
  "task_key": "ingest_each_table",
  "for_each_task": {
    "inputs": "[\"orders\",\"customers\",\"items\"]",
    "concurrency": 3,
    "task": {
      "task_key": "ingest_one",
      "notebook_task": {
        "notebook_path": "/Workspace/etl/ingest",
        "base_parameters": {"table": "{{input}}"}
      }
    }
  }
}
```

**[📖 Lakeflow Jobs](https://docs.databricks.com/aws/en/jobs/)** - tasks, triggers, notifications
**[📖 If/else condition task](https://docs.databricks.com/aws/en/jobs/tasks/if-else)** - branching on values
**[📖 For each task](https://docs.databricks.com/aws/en/jobs/tasks/for-each)** - looping with concurrency
**[📖 Jobs API](https://docs.databricks.com/api/jobs/v2/job)** - create, run-now, repair

---

## Choosing compute and configuration

The exam expects you to match compute to the workload and to know the serverless job settings by name.

| Workload | Compute |
|---|---|
| Scheduled production job, no special hardware | **Serverless jobs compute** (no cluster config to manage, fast start, billed per use) |
| Production job that needs GPUs, init scripts, a specific instance type or a JAR with native libs | **Classic jobs compute** (a job cluster created for the run) |
| Interactive development | Serverless notebooks, or an all-purpose cluster |
| SQL and BI | A **SQL warehouse** (serverless where available) |
| Lakeflow pipelines | Serverless pipelines (recommended) or classic pipeline clusters |

**Serverless job settings the guide names:**

- **Performance mode.** With **Performance optimized** on, the job starts and runs faster for time-sensitive work. With it off, the job runs in **standard** mode: fewer DBUs, but expect 4-6 minutes of start-up latency. Pick standard for overnight batch with slack, performance optimized for an SLA.
- **Serverless environments and dependencies.** Each task uses an environment: an environment version (which fixes Python and the base packages) plus your dependency list. Different tasks can use different environments.
- **High memory.** Notebook tasks can set **Memory: High** in the Environment panel when a single-node pandas step or a large collect runs out of memory. It applies to notebook tasks only.
- **Auto-optimization and retries.** Serverless auto-optimization is on by default and may **retry** a failed run automatically. For a job that must run **at most once** (it is not idempotent, for example it sends payments or emails), clear **Enable serverless auto-optimization (may include additional retries)** in the retry policy.

The reasoning pattern: idempotent and time-sensitive -> serverless, performance optimized, auto-optimization on. Not idempotent -> disallow retries. Memory error in a pandas notebook step -> high memory, or move the logic to Spark.

**[📖 Run jobs with serverless compute](https://docs.databricks.com/aws/en/jobs/run-serverless-jobs)** - performance mode, auto-optimization, high memory
**[📖 Serverless compute](https://docs.databricks.com/aws/en/compute/serverless/)** - limitations and supported workloads

---

## Unit and integration tests

**Unit tests** check one transformation on small, hand-built data. **Integration tests** run the pipeline or job end to end in a test environment against realistic data and check the outputs.

The trick that makes unit testing possible is writing transformations as functions and chaining them with **`DataFrame.transform`**:

```python
# src/my_project/transforms.py
from pyspark.sql import DataFrame
import pyspark.sql.functions as F

def clean_orders(df: DataFrame) -> DataFrame:
    return df.where(F.col("order_id").isNotNull()).dropDuplicates(["order_id"])

def add_tax(rate: float):
    def _inner(df: DataFrame) -> DataFrame:
        return df.withColumn("tax", F.round(F.col("amount") * rate, 2))
    return _inner

result = raw.transform(clean_orders).transform(add_tax(0.2))
```

PySpark ships assertion helpers in `pyspark.testing`:

- **`assertDataFrameEqual(actual, expected)`** compares rows (order-insensitive by default) and schema-compatible values, with tolerance options for floats, and prints a readable diff on failure.
- **`assertSchemaEqual(actual.schema, expected_schema)`** compares schemas only: names, types, nullability. Use it to catch an accidental type change.

```python
# tests/test_transforms.py
from pyspark.testing import assertDataFrameEqual, assertSchemaEqual
from my_project.transforms import clean_orders

def test_clean_orders_drops_null_and_duplicate_ids(spark):
    raw = spark.createDataFrame([(1, 10.0), (1, 10.0), (None, 5.0)], "order_id int, amount double")
    expected = spark.createDataFrame([(1, 10.0)], "order_id int, amount double")
    assertDataFrameEqual(clean_orders(raw), expected)
    assertSchemaEqual(clean_orders(raw).schema, expected.schema)
```

Run these with **pytest**: locally against a local Spark session or **Databricks Connect** (which runs the Spark parts on remote compute), or in CI. Integration tests usually deploy the bundle to a `staging` target, run the job with `databricks bundle run`, then query the output tables.

**[📖 Unit testing for notebooks](https://docs.databricks.com/aws/en/notebooks/testing)** - testable functions and test layout
**[📖 Testing with Databricks Connect](https://docs.databricks.com/aws/en/dev-tools/databricks-connect/python/testing)** - pytest against remote compute
**[📖 pyspark.testing](https://spark.apache.org/docs/latest/api/python/reference/pyspark.testing.html)** - `assertDataFrameEqual`, `assertSchemaEqual`

---

## Stateful Structured Streaming semantics

The exam guide sample asks for a windowed aggregation that bounds state and recovers from a driver failure without double counting. Four concepts answer every variant of that question.

**Checkpoints are the memory of the stream.** The checkpoint location holds source offsets (what has been read), the commit log (what has been written) and the state store (running aggregates, dedup keys, join buffers). On restart, the query reads the checkpoint and resumes exactly where it stopped. Two rules: every query needs its own checkpoint, and you cannot freely change the query (for example the grouping keys or the stateful operator) and keep the old checkpoint.

**Exactly-once is end to end only with a replayable source, a checkpoint, and an idempotent sink.** Delta is idempotent: a micro-batch's write is committed with its batch ID, so a replay after failure does not duplicate rows. Kafka, Auto Loader and Delta sources are replayable.

**Watermarks bound state.** `withWatermark("event_time", "15 minutes")` tells the engine: events more than 15 minutes older than the latest event time seen are too late. State for windows older than the watermark can be dropped, and later events for them are ignored. Without a watermark, a windowed aggregation or a stream-stream join keeps state forever and eventually runs out of memory.

**Output modes decide what is emitted:**

| Mode | Emits | Works with |
|---|---|---|
| **Append** | Only rows that will never change again; for windowed aggregates, after the watermark passes the window end | Most sinks, including Delta; needs a watermark for aggregations |
| **Update** | Rows that changed in this micro-batch | Sinks that can upsert, typically via `foreachBatch` + `MERGE` |
| **Complete** | The whole result table every batch | Small aggregates only; state is never dropped |

**`foreachBatch`** hands you each micro-batch as a normal DataFrame so you can do anything a batch job can: `MERGE` into Delta, write to several tables, call a JDBC sink. It is at-least-once by default, so make it idempotent. For Delta writes inside it, pass the `txnAppId` and `txnVersion` (the batch ID) options so a retried batch is skipped:

```python
def upsert(batch_df, batch_id):
    batch_df.createOrReplaceTempView("updates")
    batch_df.sparkSession.sql("""
        MERGE INTO silver.orders t USING updates s ON t.order_id = s.order_id
        WHEN MATCHED THEN UPDATE SET * WHEN NOT MATCHED THEN INSERT *
    """)

(spark.readStream.table("bronze.orders")
   .writeStream.foreachBatch(upsert)
   .option("checkpointLocation", "/Volumes/main/chk/orders_silver")
   .trigger(availableNow=True)
   .start())
```

`MERGE` keyed on the business key is already idempotent; the transaction options matter for plain appends inside `foreachBatch`.

Other stateful operators worth knowing: `dropDuplicatesWithinWatermark` (dedup with bounded state), stream-stream joins (watermarks on both sides plus a time-range condition), and **`transformWithState`**, the newer API for arbitrary per-key state with timers, which supersedes `mapGroupsWithState` / `flatMapGroupsWithState`.

**[📖 Watermarks](https://docs.databricks.com/aws/en/structured-streaming/watermarks)** - late data and state cleanup
**[📖 Output modes](https://docs.databricks.com/aws/en/structured-streaming/output-mode)** - append, update, complete
**[📖 foreachBatch](https://docs.databricks.com/aws/en/structured-streaming/foreach)** - idempotent writes from micro-batches
**[📖 Checkpoints](https://docs.databricks.com/aws/en/structured-streaming/checkpoints)** - what they store and when they break
**[📖 Stateful applications](https://docs.databricks.com/aws/en/stateful-applications/)** - `transformWithState`

---

## How the exam tests this section

- **Most questions are "choose X or Y for this constraint".** Streaming table vs materialized view, pipelines vs Structured Streaming, pandas UDF vs Unity Catalog function, standard vs performance-optimized. Find the constraint word (latency, cost, governed, at most once, late updates) before reading the options.
- **New names are used in the questions.** Expect "Declarative Automation Bundles", "AUTO CDC", "Lakeflow Jobs". An option using an old name is not automatically wrong, but an option that describes a retired behaviour is.
- **Exactly-once questions need all three parts:** replayable source, checkpoint, idempotent sink. An answer that disables checkpointing or uses complete mode "for correctness" is wrong.
- **Code reading.** Questions show a short Python or SQL snippet and ask what it does or what is missing (a watermark, a `sequence_by`, a checkpoint).

Continue with [Section 2: Data Ingestion & Acquisition](02-data-ingestion-acquisition.md).
