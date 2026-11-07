---
last-updated: 2026-11-07
applies-to: Databricks Data Analyst Associate exam guide (August 2026 edition, read 2026-11-07)
difficulty: intermediate
reading-time: 40 min
---

# Section 4: Executing Queries Using Databricks SQL and SQL Warehouses

Weighted **20%**, the largest section, about nine questions. It combines product knowledge (warehouses, Genie Code, federation, materialized views) with plain SQL (aggregates, joins, sorting, filtering, table creation, time travel).

## Table of Contents

1. [SQL warehouses and Genie Code](#sql-warehouses-and-genie-code)
2. [Federated queries](#federated-queries)
3. [Views, materialized views and streaming tables](#views-materialized-views-and-streaming-tables)
4. [Aggregates](#aggregates)
5. [Joins and set operations](#joins-and-set-operations)
6. [Sorting and filtering](#sorting-and-filtering)
7. [Creating managed and external tables](#creating-managed-and-external-tables)
8. [Time travel](#time-travel)
9. [How the exam tests this section](#how-the-exam-tests-this-section)
10. [Key terms](#key-terms)

---

## SQL warehouses and Genie Code

A **SQL warehouse** is the compute that runs your SQL. It appears in the compute drop-down in the query editor, Catalog Explorer and dashboards. There are serverless, pro and classic types, and Databricks recommends serverless when available: it starts instantly, scales automatically and Databricks handles capacity and upgrades. A stopped warehouse restarts automatically when you run a query against it, a scheduled job assigned to it runs, a JDBC/ODBC connection arrives, or a dashboard attached to it opens. Data access is still governed by Unity Catalog: the warehouse is the engine, not the permission.

**[📖 SQL warehouses](https://docs.databricks.com/aws/en/compute/sql-warehouse/)** - types, role in query execution and restart behaviour

**Genie Code** (the guide's name, with "Databricks Assistant" in brackets) is the AI assistant that works in notebooks and the SQL editor. It can generate and run code, explain it and debug errors, and its actions are limited by your Unity Catalog permissions. For exam purposes remember the use cases: write a query from plain English, explain a confusing query, and fix an error. The exact slash command names (such as /explain) appear in the exam guide's sample answers but were not on the docs page I read, so treat them as a guide-only detail.

**[📖 Genie Code](https://docs.databricks.com/aws/en/genie-code/)** - the in-workspace AI coding and data assistant

## Federated queries

With **Lakehouse Federation** you create a *connection* (credentials and URL for the external system), then a *foreign catalog* on top of it. The external tables then look like Unity Catalog tables you can grant on and join to Delta tables in one query. Query federation pushes work down to the external database; it is read-only and best for ad hoc reporting and proof of concept. For high volumes and low latency the docs recommend Lakeflow Connect instead.

```sql
-- Join a Delta table to a federated PostgreSQL table (names are examples). Not run: needs a federated source.
SELECT o.order_id, c.segment
FROM main.sales.orders AS o
JOIN pg_catalog_foreign.public.customers AS c
  ON o.customer_id = c.id;
```

**[📖 Lakehouse Federation](https://docs.databricks.com/aws/en/query-federation/)** - connections, foreign catalogs and pushdown

## Views, materialized views and streaming tables

Three ways to save a query, differing in *when the work happens*:

| Object | Stores data? | Computed | Use when |
|---|---|---|---|
| **View** (dynamic) | No | Every time it is read | You want it always current and the query is cheap |
| **Materialized view** | Yes (managed table) | On refresh (scheduled, on trigger or manual); incremental when cost-effective | Frequent, expensive queries, such as BI dashboards |
| **Streaming table** | Yes | Incrementally, row by row, append-only | Continuously arriving data such as sensor events |

A materialized view stores query results and refreshes through a serverless pipeline. Refresh is incremental when cost-effective, and requires Delta source tables with row tracking; otherwise it is a full refresh. Syntax:

```sql
-- Syntax per the Databricks docs; NOT run (needs Unity Catalog and serverless pipelines).
CREATE OR REPLACE MATERIALIZED VIEW main.gold.daily_revenue
SCHEDULE CRON '0 0 6 * * ?' AT TIME ZONE 'UTC'
AS SELECT date(order_ts) AS day, SUM(amount) AS revenue
   FROM main.silver.orders
   GROUP BY date(order_ts);

REFRESH MATERIALIZED VIEW main.gold.daily_revenue;
```

Streaming tables process each input row once and are meant for append-only ingestion and low-latency transformations. The docs warn that they do not recompute joins when dimension tables change, whereas materialized views do, so a materialized view is the safer choice when you join to changing dimensions.

**[📖 Materialized views](https://docs.databricks.com/aws/en/views/materialized)** - syntax, refresh and incremental behaviour
**[📖 Streaming tables](https://docs.databricks.com/aws/en/ldp/streaming-tables)** - when to use them and how they differ from materialized views

## Aggregates

Aggregates collapse many rows to one value per group: `COUNT`, `SUM`, `AVG` (mean), `MIN`, `MAX`. Any non-aggregated column in the `SELECT` list must appear in `GROUP BY`; that rule is why "missing GROUP BY" is the classic query bug. `HAVING` filters *after* aggregation, `WHERE` before.

`approx_count_distinct(expr)` estimates distinct counts using the HyperLogLog++ algorithm. The default is accurate to within 5%, and you can set a tighter relative standard deviation. It is much cheaper than `COUNT(DISTINCT ...)` on huge data. For summary statistics, `DESCRIBE`-style profiling in a notebook (`df.summary()`) gives count, mean, min, quartiles and max.

```sql
-- Ran on Spark 4.3.0 locally (Databricks SQL uses the same function names).
SELECT region, COUNT(*) AS number_of_customers
FROM customers
GROUP BY region
ORDER BY region;

SELECT approx_count_distinct(customer_id) AS approx_customers,
       COUNT(DISTINCT customer_id)        AS exact_customers,
       ROUND(AVG(amount), 2)              AS mean_amount
FROM orders;
```

**[📖 approx_count_distinct](https://docs.databricks.com/aws/en/sql/language-manual/functions/approx_count_distinct)** - syntax and accuracy

## Joins and set operations

Databricks SQL supports INNER (default), LEFT, RIGHT, FULL, CROSS, and the filtering joins LEFT SEMI and LEFT ANTI. Join on one key with `ON a.id = b.id` or on several with `ON a.k1 = b.k1 AND a.k2 = b.k2`; `USING (id)` matches same-named columns and shows each once. A join with no criteria behaves like a cross join.

Picture two lists, customers and orders. **INNER** keeps only customers who have orders. **LEFT** keeps every customer, with nulls where no order exists. **FULL** keeps everything from both sides. **LEFT ANTI** returns customers with no orders at all, a very useful pattern.

```sql
-- Ran on Spark 4.3.0 locally with customers(id,name,region) and orders(order_id,customer_id,amount).
SELECT c.name, SUM(o.amount) AS total      -- Ana 75.0, Bo 70.0, Cy NULL (left join keeps Cy)
FROM customers c LEFT JOIN orders o ON c.id = o.customer_id
GROUP BY c.name;

SELECT c.name FROM customers c
LEFT ANTI JOIN orders o ON c.id = o.customer_id;   -- Cy
```

**Set operations** stack result sets with the same number of compatible columns. `UNION` removes duplicate rows; `UNION ALL` keeps them and is faster. In the local run, `UNION` of the two id lists returned 1, 2, 3, 9, and `UNION ALL` returned all 7 rows.

**[📖 JOIN syntax](https://docs.databricks.com/aws/en/sql/language-manual/sql-ref-syntax-qry-select-join)** - every join type and ON versus USING

## Sorting and filtering

`WHERE` filters rows, `ORDER BY col [ASC|DESC]` sorts, `LIMIT n` keeps the first n. Combine them for "top five customers by revenue". Remember filtering order: `FROM`/joins, `WHERE`, `GROUP BY`, `HAVING`, `SELECT`, `ORDER BY`, `LIMIT`.

## Creating managed and external tables

```sql
-- Managed table (Unity Catalog stores the data). Syntax per docs; not run.
CREATE OR REPLACE TABLE main.warehouse_db.product_inventory (width INT, length INT, height INT);

-- Table from a join of sources (CTAS).
CREATE TABLE main.gold.order_facts AS
SELECT o.order_id, o.amount, c.region
FROM main.silver.orders o JOIN main.silver.customers c ON o.customer_id = c.id;
```

`CREATE OR REPLACE TABLE` creates the table or replaces it while keeping its history and privileges, which `DROP` then `CREATE` does not. **CTAS** (`CREATE TABLE ... AS SELECT`) builds a table from a query, and that is how you combine CSV, Parquet and Delta sources into one dataset: read each into the query, join them, and write the result. An **external table** adds a `LOCATION` in an external location, and dropping it leaves the files in place; managed tables are the default and are cleaned up after the recovery window when dropped.

**[📖 Managed tables](https://docs.databricks.com/aws/en/tables/managed)** - default type, DROP behaviour and UNDROP window

## Time travel

Every write to a Delta table creates a version. Query an old one with `VERSION AS OF` or `TIMESTAMP AS OF`, list versions with `DESCRIBE HISTORY`, and roll back with `RESTORE`.

```sql
-- Syntax per the Databricks docs; not run.
DESCRIBE HISTORY main.silver.orders;
SELECT * FROM main.silver.orders VERSION AS OF 123;
SELECT * FROM main.silver.orders TIMESTAMP AS OF '2026-10-01';
RESTORE TABLE main.silver.orders TO VERSION AS OF 123;
```

Limits: history is kept 30 days by default (`logRetentionDuration`), but data files that `VACUUM` has removed are gone: deleted-file retention defaults to **7 days**, and Databricks recommends using only the past 7 days for time travel unless you raise both settings. That is why a 30-day-old query can fail even though history is nominally 30 days.

**[📖 Table history and time travel](https://docs.databricks.com/aws/en/delta/history)** - DESCRIBE HISTORY, time travel syntax, retention and RESTORE

## How the exam tests this section

- **Pick the object:** streaming table for continuous append-only data; materialized view for heavy repeated BI queries; view for cheap always-current logic.
- **Spot the broken query:** missing `GROUP BY`, `WHERE` where `HAVING` is needed, join type returning too few or many rows.
- **Join types in words:** "customers with no orders" is LEFT ANTI (or LEFT JOIN with IS NULL); "all customers even without orders" is LEFT.
- **Time travel failures** after about a week are explained by `VACUUM` and retention settings.
- **Create-or-replace** keeps history and privileges; drop-and-create does not.

## Key terms

- **SQL warehouse** - the compute resource that runs SQL queries in Databricks SQL, available as serverless, pro or classic.
- **Genie Code** - the AI assistant in notebooks and the SQL editor that writes, explains and debugs code.
- **Foreign catalog** - a Unity Catalog catalog that exposes an external database's tables through a connection.
- **Materialized view** - a managed table that stores the results of a query and is refreshed on a schedule or trigger.
- **Streaming table** - a Delta table that incrementally and append-only processes each input row once.
- **approx_count_distinct** - a function that estimates distinct values using HyperLogLog++, within 5 percent by default.
- **LEFT ANTI JOIN** - a join that returns left-side rows that have no match on the right.
- **UNION ALL** - a set operation that stacks results and keeps duplicate rows.
- **CTAS** - CREATE TABLE AS SELECT, which builds a table from the result of a query.
- **Time travel** - querying an earlier version of a Delta table with VERSION AS OF or TIMESTAMP AS OF.
- **VACUUM** - a command that removes data files no longer referenced by a Delta table beyond the retention period.
- **RESTORE** - a command that rolls a Delta table back to an earlier version or timestamp.
