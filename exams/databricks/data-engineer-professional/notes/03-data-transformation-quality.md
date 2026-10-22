---
last-updated: 2026-10-22
applies-to: Databricks Data Engineer Professional exam guide of 2026-10-09
difficulty: advanced
reading-time: 25 min
---

# Section 3: Data Transformation, Cleansing, and Quality

The exam page calls this section "Data Transformation, Cleansing, and Quality" (10%); the 2026-10-09 exam guide PDF calls it "Data Manipulation" (12%). The four objectives are the same: advanced transformations at scale, semi-structured data with `VARIANT`, AI functions inside pipelines, and data-quality expectations.

Source: the **[📖 exam page](https://www.databricks.com/learn/certification/data-engineer-professional)** and its linked exam guide.

## Table of Contents

1. [Window functions](#window-functions)
2. [Joins at scale](#joins-at-scale)
3. [Aggregations and deduplication](#aggregations-and-deduplication)
4. [Semi-structured data with VARIANT](#semi-structured-data-with-variant)
5. [AI functions in pipelines](#ai-functions-in-pipelines)
6. [Data quality expectations](#data-quality-expectations)
7. [How the exam tests this section](#how-the-exam-tests-this-section)

---

## Window functions

A window function computes a value for each row from a set of related rows, without collapsing them the way `GROUP BY` does. The window is defined by three parts: `PARTITION BY` (which rows belong together), `ORDER BY` (their order), and an optional frame (`ROWS BETWEEN ...` or `RANGE BETWEEN ...`).

The patterns the exam uses most:

```sql
-- Latest record per key (the classic dedup)
SELECT * FROM (
  SELECT *, row_number() OVER (PARTITION BY customer_id ORDER BY updated_at DESC) AS rn
  FROM bronze.customers)
WHERE rn = 1;

-- Databricks shorthand for the same thing
SELECT * FROM bronze.customers
QUALIFY row_number() OVER (PARTITION BY customer_id ORDER BY updated_at DESC) = 1;

-- Running total and 7-row moving average
SELECT order_date, amount,
       sum(amount) OVER (ORDER BY order_date ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW) AS running_total,
       avg(amount) OVER (ORDER BY order_date ROWS BETWEEN 6 PRECEDING AND CURRENT ROW) AS ma7
FROM gold.daily_sales;

-- Change from previous event
SELECT device_id, ts, reading,
       reading - lag(reading) OVER (PARTITION BY device_id ORDER BY ts) AS delta
FROM silver.readings;
```

Know the differences between `row_number` (always unique), `rank` (ties share a rank, then gaps) and `dense_rank` (ties share, no gaps). The PySpark form uses `Window.partitionBy(...).orderBy(...)` with `F.row_number().over(w)`.

**At scale**, a window with `PARTITION BY` shuffles all rows of a key to one task. A very skewed key (one customer with half the rows) makes one task do half the work. A window with **no** `PARTITION BY` moves every row to a single task; avoid it on large data. Watch for both in the query profile ([Section 6](06-cost-performance-optimisation.md#reading-the-query-profile)).

**[📖 Window functions](https://docs.databricks.com/aws/en/sql/language-manual/sql-ref-window-functions)** - syntax, frames, ranking and analytic functions

---

## Joins at scale

Spark picks a physical join strategy. Knowing which, and why, is what turns a slow join into a fast one.

| Strategy | When Spark uses it | Cost |
|---|---|---|
| **Broadcast hash join** | One side is small (under `spark.sql.autoBroadcastJoinThreshold`, or AQE sees it is small at runtime) | No shuffle of the big side; fastest |
| **Shuffle hash join** | Medium sides; one side's partitions fit in memory | Shuffles both sides |
| **Sort-merge join** | Both sides large (the default for big-to-big) | Shuffles and sorts both sides |

Tools you control:

- **Hints:** `SELECT /*+ BROADCAST(d) */ ...` forces a broadcast when you know the dimension is small.
- **Adaptive Query Execution (AQE)**, on by default, switches a sort-merge join to broadcast when the real size is small, coalesces tiny shuffle partitions and **splits skewed partitions** during skew joins.
- **Filter and project early** so less data shuffles. Join on the keys that a table is clustered by so data skipping helps.

Join types matter for correctness, not just speed: `LEFT ANTI JOIN` finds rows with no match (new records), `LEFT SEMI JOIN` keeps rows that have a match without duplicating them. Stream-static joins (streaming fact, static Delta dimension) pick up the latest dimension version each micro-batch; stream-stream joins need watermarks on both sides.

**[📖 Adaptive Query Execution](https://docs.databricks.com/aws/en/optimizations/aqe)** - runtime join conversion and skew handling

---

## Aggregations and deduplication

Aggregations are `groupBy().agg(...)` in PySpark and `GROUP BY` in SQL. At scale, three things matter:

- **Exact vs approximate distinct.** `count(DISTINCT x)` on billions of rows is expensive. `approx_count_distinct(x)` (HyperLogLog) is far cheaper with a small, tunable error. Use it for dashboards; use exact counts for billing.
- **Grouping sets.** `GROUP BY GROUPING SETS ((region), (region, product), ())`, `ROLLUP` and `CUBE` produce several aggregation levels in one pass.
- **Pivot.** `pivot()` turns row values into columns; give it the list of values so Spark does not have to compute them first.

**Deduplication** in batch is `dropDuplicates(["key"])` (arbitrary row kept) or the `row_number()` pattern above (the row you choose is kept). In streaming, use `dropDuplicatesWithinWatermark` so the state of seen keys is bounded.

---

## Semi-structured data with VARIANT

`VARIANT` is a Databricks SQL type for semi-structured data such as JSON. It stores the data in an efficient binary encoding, keeps the full nested structure, and lets you query fields without declaring a schema up front. Compared with keeping JSON as a `STRING`, reads are much faster because fields do not have to be re-parsed from text on every query. Compared with parsing into a fixed `STRUCT`, it does not break when the payload shape changes.

```sql
-- Bronze: store raw payloads as VARIANT
CREATE TABLE bronze.events AS
SELECT parse_json(raw_string) AS payload, ingested_at
FROM raw_events;

-- Silver: extract typed fields
SELECT
  payload:user.id::BIGINT                         AS user_id,      -- colon path + cast
  variant_get(payload, '$.device.os', 'STRING')   AS os,           -- fails on bad cast
  try_variant_get(payload, '$.amount', 'DECIMAL(10,2)') AS amount  -- NULL on bad cast
FROM bronze.events;
```

The functions to know:

| Function | What it does |
|---|---|
| `parse_json(str)` | String to `VARIANT`; error on invalid JSON |
| `try_parse_json(str)` | Same, but `NULL` on invalid JSON (safer in bronze) |
| `variant_get(v, path, type)` | Extract a field at a JSON path and cast; error if the cast fails |
| `try_variant_get(v, path, type)` | Same, `NULL` if the cast fails |
| `v:field.sub[0]` | Colon path syntax; returns `VARIANT`, cast with `::TYPE` |
| `schema_of_variant(v)` / `schema_of_variant_agg(v)` | Discover the shape of one value, or across rows |
| `variant_explode(v)` | One row per element of an array or object |
| `to_json(v)` | Back to a string |

Auto Loader and `read_files` can ingest JSON straight into a `VARIANT` column, which is the modern bronze pattern for event payloads whose shape you do not control. Databricks can also **shred** frequently accessed fields of a `VARIANT` column into separate columnar storage for faster reads; check the current docs for the table property and runtime requirements.

**[📖 Query variant data](https://docs.databricks.com/aws/en/semi-structured/variant)** - path syntax, casting, explode
**[📖 parse_json](https://docs.databricks.com/aws/en/sql/language-manual/functions/parse_json)** - string to VARIANT
**[📖 variant_get](https://docs.databricks.com/aws/en/sql/language-manual/functions/variant_get)** - typed extraction
**[📖 try_variant_get](https://docs.databricks.com/aws/en/sql/language-manual/functions/try_variant_get)** - extraction that returns NULL on failure

---

## AI functions in pipelines

**AI functions** are SQL functions that call a model from inside a query, so enrichment and classification run as part of the pipeline instead of a separate service.

- **`ai_query(endpoint, request, ...)`** is the general one. It sends each row's `request` to a Model Serving endpoint (a Databricks-hosted foundation model, an external model, or your own custom model) and returns the response. You can ask for structured output with a `responseFormat` (a JSON schema), and set `failOnError => false` so one bad row returns an error value instead of failing the whole query.
- **Task-specific functions** wrap common jobs with no prompt to write: `ai_classify`, `ai_extract`, `ai_analyze_sentiment`, `ai_summarize`, `ai_translate`, `ai_mask` (redact entities), `ai_fix_grammar`, `ai_similarity`, `ai_gen`, and `ai_parse_document` (text and layout from PDFs and images, often fed from `binaryFile` ingestion).

```sql
CREATE OR REFRESH MATERIALIZED VIEW silver.tickets_enriched AS
SELECT ticket_id, body,
       ai_classify(body, ARRAY('billing', 'bug', 'feature request', 'other')) AS category,
       ai_query('support-summary-endpoint',  -- any Model Serving endpoint
                concat('Summarise this support ticket in one sentence: ', body)) AS summary
FROM silver.tickets;
```

Production points:

- AI functions are designed for **batch inference** at scale; Databricks parallelises the calls. Run them in a materialized view or a scheduled job, not row by row from a notebook loop.
- **Cost and determinism.** Every row is a model call. Process only new or changed rows (a streaming table, or a materialized view with incremental refresh), and do not re-run inference on unchanged history.
- **Governance.** The endpoint is a Unity Catalog-governed object; the caller needs permission to query it. Use `ai_mask` or a column mask before sending PII to a model if policy requires.

**[📖 AI functions](https://docs.databricks.com/aws/en/large-language-models/ai-functions)** - task-specific functions and batch inference
**[📖 ai_query](https://docs.databricks.com/aws/en/sql/language-manual/functions/ai_query)** - parameters, structured output, error handling

---

## Data quality expectations

An **expectation** is a named Boolean constraint on a pipeline dataset. Every row is checked, and the pipeline records how many passed and failed in the event log ([Section 5](05-monitoring-alerting.md#pipeline-event-logs)). You choose what happens on failure:

| Action | Python | SQL | Effect |
|---|---|---|---|
| **Warn** (default) | `@dp.expect` | `EXPECT (cond)` | Bad rows are kept; metric recorded |
| **Drop** | `@dp.expect_or_drop` | `EXPECT (cond) ON VIOLATION DROP ROW` | Bad rows are removed from the target |
| **Fail** | `@dp.expect_or_fail` | `EXPECT (cond) ON VIOLATION FAIL UPDATE` | The update stops; the transaction rolls back |

`expect_all`, `expect_all_or_drop` and `expect_all_or_fail` take a dictionary of several rules.

```sql
CREATE OR REFRESH STREAMING TABLE silver.orders (
  CONSTRAINT valid_id     EXPECT (order_id IS NOT NULL) ON VIOLATION FAIL UPDATE,
  CONSTRAINT valid_amount EXPECT (amount >= 0)          ON VIOLATION DROP ROW,
  CONSTRAINT has_country  EXPECT (country IS NOT NULL)
) AS SELECT * FROM STREAM(bronze.orders);
```

**Quarantine** is the pattern when you must neither keep bad rows in silver nor lose them. There is no "quarantine" keyword; you build it from two datasets reading the same source with opposite rules:

```python
rules = {"valid_id": "order_id IS NOT NULL", "valid_amount": "amount >= 0"}
quarantine_rule = "NOT({})".format(" AND ".join(rules.values()))

@dp.table
@dp.expect_all_or_drop(rules)
def orders_clean():
    return spark.readStream.table("bronze.orders")

@dp.table
def orders_quarantine():
    return spark.readStream.table("bronze.orders").where(quarantine_rule)
```

A data steward reviews the quarantine table, fixes the source, and reprocesses. An alternative is one table with an `is_quarantined` flag column and two views over it.

Choosing the action:

- **Fail** when a bad row means something is fundamentally broken (a null primary key, a currency that does not exist) and downstream must not see partial data.
- **Drop** when bad rows are expected noise and losing them is acceptable.
- **Warn** when you want visibility first, before deciding on a stricter rule.
- **Quarantine** when the requirement says "do not lose records" and "keep silver clean".

Outside pipelines, the same ideas are Delta `CHECK` constraints (`ALTER TABLE t ADD CONSTRAINT c CHECK (amount >= 0)`, which fail the write) and data quality monitoring on tables ([Section 5](05-monitoring-alerting.md#alerts)).

**[📖 Manage data quality with pipeline expectations](https://docs.databricks.com/aws/en/ldp/expectations)** - actions, multiple expectations, quarantine examples

---

## How the exam tests this section

- **"Latest row per key"** is `row_number()` or `QUALIFY`; "ties must share a position" is `rank` or `dense_rank`.
- **JSON of varying shape, fast field access, no fixed schema** is `VARIANT` with `parse_json` and `variant_get` or colon paths. Regex on a string and exploding into a fixed struct are the distractors.
- **"Classify / enrich / extract inside the pipeline"** is an AI function, usually `ai_query` or a task-specific one, run in batch.
- **Expectations questions** turn on the action word: keep and count (warn), remove (drop), stop (fail), keep separately (quarantine).

Continue with [Section 4: Data Sharing and Federation](04-data-sharing-federation.md).
