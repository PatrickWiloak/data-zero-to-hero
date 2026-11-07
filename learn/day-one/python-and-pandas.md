---
last-updated: 2026-11-07
difficulty: beginner
reading-time: 15 min
---

# Python and pandas for data

> **15-minute read plus typing. Assumes [Your first SQL query](./first-sql-query.md). You need Python 3.10 or newer.**

## Why this page exists

SQL is great for asking questions of tables in a database. Python is what you reach for when the data is a file, needs cleaning, feeds a chart or a model, or must be fetched from somewhere first. The standard Python tool for tables is **pandas**. You will meet it in almost every data job.

You do not have to choose. Analysts use both, often in the same hour.

## Install

In your practice folder with the virtual environment active (see [page 1](./first-sql-query.md#set-up-in-two-minutes)):

```bash
pip install pandas
```

Tested here with pandas 3.1.1 and Python 3.12.

## A DataFrame is a table in memory

pandas calls a table a **DataFrame**. Load `orders.csv` (from page 1) and look at it. Save as `pandas_demo.py`:

```python
import pandas as pd

df = pd.read_csv("orders.csv", parse_dates=["order_date"])
print(df.head(3))
print()
print(df.dtypes)
```

```text
   order_id order_date customer region   product  quantity  unit_price
0      1001 2026-01-05      Ava   East  Notebook         3         4.5
1      1002 2026-01-06      Ben   West       Pen        10         1.2
2      1003 2026-01-06     Cara   East  Backpack         1        39.0

order_id               int64
order_date    datetime64[us]
customer                 str
region                   str
product                  str
quantity                 int64
unit_price             float64
dtype: object
```

`head(3)` shows the first three rows. `parse_dates` told pandas that `order_date` is a date, not text. Without it, dates load as plain text, and you cannot do date arithmetic on text. (The `str` type shown here is how pandas 3 names text columns. Older versions print `object`.)

## The same questions, in pandas

Each pandas line below does what a SQL clause from page 1 did.

| SQL | pandas |
|---|---|
| `SELECT customer, product` | `df[["customer", "product"]]` |
| `WHERE region = 'East'` | `df[df["region"] == "East"]` |
| `quantity * unit_price AS total` | `df["total"] = df["quantity"] * df["unit_price"]` |
| `GROUP BY region` + `sum` | `df.groupby("region")["total"].sum()` |
| `ORDER BY ... DESC` | `.sort_values(..., ascending=False)` |
| `JOIN` | `df.merge(other, on="customer")` |

Add a column, filter, and group:

```python
df["total"] = df["quantity"] * df["unit_price"]

print(df[df["region"] == "East"][["order_id", "customer", "total"]])
print()
print(df.groupby("region")["total"].agg(["count", "sum"]).sort_values("sum", ascending=False))
```

```text
   order_id customer  total
0      1001      Ava   13.5
2      1003     Cara   39.0
4      1005      Ava   24.0
8      1009     Cara    9.0

        count    sum
region              
West        4  147.0
East        4   85.5
North       2   40.5
```

Same numbers as the SQL on page 1. The left-hand numbers (`0`, `2`, `4`, `8`) are the **index**: the original row positions, kept after filtering. That surprises beginners; it is not a bug.

`df["region"] == "East"` produces a column of True/False values, one per row, and `df[...]` keeps the True rows. That pattern, a boolean mask, is the heart of pandas.

### Join, then group

```python
cust = pd.read_csv("customers.csv")
m = df.merge(cust, on="customer", how="left")
print(m.groupby("city")["total"].sum().sort_values(ascending=False))
```

```text
city
Denver    108.0
Boston     85.5
Austin     40.5
Name: total, dtype: float64
```

Gus has no city, so his 39.0 is in no group: the three cities add to 234.0, not the table's 273.0. By default `groupby` drops rows whose key is missing, the same silent loss described on the [spreadsheets page](./spreadsheets-to-sql.md). Always check totals after a join.

### A quick summary of every number column

```python
print(df.describe())
```

```text
         order_id           order_date   quantity  unit_price      total
count    10.00000                   10  10.000000   10.000000  10.000000
mean   1005.50000  2026-01-14 16:48:00   6.300000   13.860000  27.300000
min    1001.00000  2026-01-05 00:00:00   1.000000    1.200000   9.000000
25%    1003.25000  2026-01-06 18:00:00   2.000000    2.025000  14.625000
50%    1005.50000  2026-01-13 12:00:00   3.500000    4.500000  20.250000
75%    1007.75000  2026-01-20 06:00:00   8.750000   30.375000  35.250000
max    1010.00000  2026-01-30 00:00:00  20.000000   39.000000  78.000000
std       3.02765                  NaN   6.566751   17.407929  20.576686
```

`describe()` is the first thing to run on any new file. Count, mean, min, max and quartiles reveal impossible values (a negative quantity, a price of 9999) in one glance.

## Polars: the newer alternative

**Polars** is a newer DataFrame library, written in Rust, that is often faster than pandas and can process files larger than memory using lazy evaluation. Its code reads more like SQL. Install with `pip install polars` (tested here: 2.1.0). The same grouped result:

```python
import polars as pl

df = pl.read_csv("orders.csv", try_parse_dates=True).with_columns(
    (pl.col("quantity") * pl.col("unit_price")).alias("total")
)
print(
    df.group_by("region")
    .agg(pl.len().alias("orders"), pl.col("total").sum().alias("revenue"))
    .sort("revenue", descending=True)
)
```

```text
shape: (3, 3)
┌────────┬────────┬─────────┐
│ region ┆ orders ┆ revenue │
│ ---    ┆ ---    ┆ ---     │
│ str    ┆ u32    ┆ f64     │
╞════════╪════════╪═════════╡
│ West   ┆ 4      ┆ 147.0   │
│ East   ┆ 4      ┆ 85.5    │
│ North  ┆ 2      ┆ 40.5    │
└────────┴────────┴─────────┘
```

Which to learn? Learn pandas first: it is everywhere, in tutorials, job posts and older codebases. Reach for Polars when pandas runs out of memory or time. The ideas (columns, filters, group, join) transfer completely.

## Or just use SQL from Python

DuckDB can run SQL straight on a pandas DataFrame, so you can mix the styles. A DataFrame variable in scope can be named in `FROM`, and `.df()` turns a result back into a DataFrame:

```python
import duckdb
import pandas as pd

df = pd.read_csv("orders.csv")
result = duckdb.sql("SELECT region, count(*) AS orders FROM df GROUP BY region ORDER BY region")
print(result.df())
```

```text
  region  orders
0   East       4
1  North       2
2   West       4
```

Use whichever form is clearer for the step.

## Common beginner mistakes

- **Changing a copy.** Filtering gives a new object. Assign it (`east = df[df["region"] == "East"]`) or the result is lost.
- **Text that looks like numbers.** A column with one stray `N/A` loads as text. Check `df.dtypes`.
- **Dates as text.** Use `parse_dates` or `pd.to_datetime`.
- **Forgetting missing values.** They appear as `NaN`. `df.isna().sum()` counts them per column.

## Try it yourself

1. What is the average `total` per `product`?
2. Show only orders with `total` over 30, sorted by `total`, largest first.
3. How many orders per `customer`? (`df["customer"].value_counts()`.)

## Go deeper

**[📖 pandas: 10 minutes to pandas](https://pandas.pydata.org/docs/user_guide/10min.html)** - the official quick tour.

**[📖 pandas: Group by](https://pandas.pydata.org/docs/user_guide/groupby.html)** - split, apply, combine in full.

**[📖 Polars documentation](https://docs.pola.rs/)** - the user guide and API reference.

**Next:** [Notebooks](./notebooks.md).
