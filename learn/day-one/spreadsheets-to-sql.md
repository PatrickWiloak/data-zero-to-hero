---
last-updated: 2026-11-07
difficulty: beginner
reading-time: 12 min
---

# From spreadsheets to SQL

> **12-minute read. Assumes you have used Excel or Google Sheets and finished [Your first SQL query](./first-sql-query.md).**

## Why this page exists

If you work in spreadsheets, you already think in data. This page maps what you do there to the SQL that does the same job, so you are translating, not starting over. It also says honestly when a spreadsheet is still the right tool.

## The big difference

A spreadsheet mixes data, calculations and presentation in one grid. A cell can hold anything, and a formula lives next to the value it reads.

A SQL table holds **only data**: every column has one type, every row is one record. Calculations live in a **query** that you write separately and can rerun. Nothing in the table changes when you ask a question, so you cannot break the data by exploring it.

| Spreadsheet | SQL |
|---|---|
| A workbook | A database |
| A sheet (tab) | A table |
| A header row | Column names |
| A row | A row (a record) |
| A formula in a cell | An expression in a query |
| Filter button | `WHERE` |
| Sort button | `ORDER BY` |
| Pivot table | `GROUP BY` |
| `SUMIF`, `COUNTIF`, `AVERAGEIF` | An aggregate with `WHERE` |
| `VLOOKUP`, `XLOOKUP`, `INDEX/MATCH` | `JOIN` |
| Remove duplicates | `SELECT DISTINCT` |

All examples use the same `orders.csv` from page 1 (see that page to create it), loaded into DuckDB as `orders`. A second small file, `customers.csv`, is used for the lookup:

```csv
customer,city,tier
Ava,Boston,gold
Ben,Denver,silver
Cara,Boston,silver
Dev,Austin,bronze
Eli,Denver,gold
Fay,Austin,bronze
```

Load both:

```python
import duckdb

duckdb.sql("CREATE TABLE orders AS SELECT * FROM read_csv('orders.csv')")
duckdb.sql("CREATE TABLE customers AS SELECT * FROM read_csv('customers.csv')")
```

## SUMIF becomes SUM with WHERE

Spreadsheet: `=SUMIF(D:D, "West", total_column)` after you added a `total` column by hand.

SQL:

```sql
SELECT sum(quantity * unit_price) AS west_revenue
FROM orders
WHERE region = 'West'
```

```text
┌──────────────┐
│ west_revenue │
│    double    │
├──────────────┤
│        147.0 │
└──────────────┘
```

You did not need a helper column. The calculation sits inside the query.

## A pivot table becomes GROUP BY

A pivot table with rows = region, columns = product and values = sum of quantity is a `GROUP BY` on two columns. SQL returns it as a tall list, one row per pair:

```sql
SELECT region, product, sum(quantity) AS units
FROM orders
GROUP BY region, product
ORDER BY region, product
```

```text
┌─────────┬──────────┬────────┐
│ region  │ product  │ units  │
│ varchar │ varchar  │ int128 │
├─────────┼──────────┼────────┤
│ East    │ Backpack │      1 │
│ East    │ Notebook │      5 │
│ East    │ Pen      │     20 │
│ North   │ Notebook │      5 │
│ North   │ Pen      │     15 │
│ West    │ Backpack │      3 │
│ West    │ Notebook │      4 │
│ West    │ Pen      │     10 │
└─────────┴──────────┴────────┘
```

Two things to notice. First, there is no row for North and Backpack: SQL only returns combinations that exist, where a pivot shows a blank cell. Second, the result is "tall", not a wide grid. Most database tools can pivot it wide afterwards, but a tall result is what charts and other tools prefer anyway.

## VLOOKUP becomes JOIN

A `VLOOKUP` pulls a value from another table by matching a key. In SQL that is a `JOIN`. We look up each order's customer city:

```sql
SELECT o.order_id, o.customer, c.city, c.tier
FROM orders AS o
LEFT JOIN customers AS c ON o.customer = c.customer
ORDER BY o.order_id
```

```text
┌──────────┬──────────┬─────────┬─────────┐
│ order_id │ customer │  city   │  tier   │
│  int64   │ varchar  │ varchar │ varchar │
├──────────┼──────────┼─────────┼─────────┤
│     1001 │ Ava      │ Boston  │ gold    │
│     1002 │ Ben      │ Denver  │ silver  │
│     1003 │ Cara     │ Boston  │ silver  │
│     1004 │ Dev      │ Austin  │ bronze  │
│     1005 │ Ava      │ Boston  │ gold    │
│     1006 │ Eli      │ Denver  │ gold    │
│     1007 │ Ben      │ Denver  │ silver  │
│     1008 │ Fay      │ Austin  │ bronze  │
│     1009 │ Cara     │ Boston  │ silver  │
│     1010 │ Gus      │ NULL    │ NULL    │
└──────────┴──────────┴─────────┴─────────┘
```

Gus is not in `customers.csv`. A spreadsheet `VLOOKUP` would show `#N/A`. A `LEFT JOIN` keeps every order and fills the missing side with `NULL`, which is SQL's word for "no value". An inner `JOIN` (just `JOIN`) would silently **drop** Gus's order instead:

```sql
SELECT c.city, sum(o.quantity * o.unit_price) AS revenue
FROM orders AS o
JOIN customers AS c ON o.customer = c.customer
GROUP BY c.city
ORDER BY revenue DESC
```

```text
┌─────────┬─────────┐
│  city   │ revenue │
│ varchar │ double  │
├─────────┼─────────┤
│ Denver  │   108.0 │
│ Boston  │    85.5 │
│ Austin  │    40.5 │
└─────────┴─────────┘
```

These three cities add up to 234.0, but the whole table totals 273.0 (10 orders averaging 27.3). The 39.0 difference is Gus's backpack. Silent row loss after a join is one of the most common data bugs, so after any join, compare a count or total against the table you started from. The [SQL joins explained](../concepts/sql-joins-explained.md) concept page covers every join type.

## Remove duplicates becomes DISTINCT

```sql
SELECT count(*) AS orders, count(DISTINCT customer) AS customers
FROM orders
```

```text
┌────────┬───────────┐
│ orders │ customers │
│ int64  │   int64   │
├────────┼───────────┤
│     10 │         7 │
└────────┴───────────┘
```

Ten orders from seven different customers. Nothing was deleted: you counted unique values instead.

## What a spreadsheet does better

- **Looking at and editing a small table by hand.** Nothing beats a grid for 200 rows you are fixing manually.
- **Quick one-off charts and what-if models.**
- **Sharing with people who do not know SQL.**

Excel's practical limit is about one million rows per sheet; it gets slow well before then. Databases keep working at that size and far beyond.

## What SQL does better

- **Repeatable.** The query is text. Rerun it tomorrow on new data and get a fresh answer. A chain of manual clicks cannot be rerun.
- **Reviewable.** You can read a query, put it in git and ask a colleague to check it.
- **Safe.** Reading data never changes it.
- **Large.** Millions of rows are normal.
- **Combinable.** Joining ten tables is routine.

## The mistake to avoid

Do not copy a SQL result into a spreadsheet, edit it by hand, and then treat the spreadsheet as the truth. Now two versions exist and the edits are invisible. If the numbers need changing, change the query or the source data, and rerun.

## Try it yourself

1. Write the SQL for `COUNTIF(region, "East")`.
2. A pivot of average `unit_price` by product: write it with `GROUP BY product`.
3. Add a row for a customer who has no orders to `customers.csv`. Does the inner `JOIN` above change? (It should not. Why?)

**Next:** [A local database to practice on](./local-practice-database.md).
