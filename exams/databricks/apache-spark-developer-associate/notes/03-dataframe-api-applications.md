---
last-updated: 2026-11-07
applies-to: Databricks Associate Developer for Apache Spark exam guide (published 2026-10-08, read 2026-11-07)
difficulty: intermediate
reading-time: 45 min
---

# Section 3: Developing Apache Spark DataFrame/Dataset API Applications

Weighted **30%**, about 13 questions, the largest section. Ten objectives: manipulate columns, rows and structure (add, drop, split, rename, filter, explode); deduplicate and validate; aggregate; work with dates; combine DataFrames (joins, broadcast, cross, union); read and write with schemas; sort, iterate, print schemas and convert to lists; user-defined functions including stateful ones; broadcast variables and accumulators; and broadcast joins.

Everything below ran on PySpark 4.3.0 in local mode on 2026-11-07 unless marked "not run".

## Table of Contents

1. [Columns, rows and structure](#columns-rows-and-structure)
2. [Deduplication, validation and missing data](#deduplication-validation-and-missing-data)
3. [Aggregation](#aggregation)
4. [Dates](#dates)
5. [Combining DataFrames](#combining-dataframes)
6. [Schemas and input/output](#schemas-and-inputoutput)
7. [Sorting, iterating and converting](#sorting-iterating-and-converting)
8. [User-defined functions](#user-defined-functions)
9. [Broadcast variables and accumulators](#broadcast-variables-and-accumulators)
10. [How the exam tests this section](#how-the-exam-tests-this-section)
11. [Key terms](#key-terms)

---

## Columns, rows and structure

DataFrames are immutable: every method returns a new DataFrame. The workhorse methods:

| Goal | Method |
|---|---|
| Pick columns | `select("a", F.col("b"))` |
| Add or replace a column | `withColumn("name", expr)` |
| Rename | `withColumnRenamed("old", "new")` |
| Drop columns | `drop("a", "b")` |
| Keep rows | `filter(cond)` or `where(cond)` |
| Split a string into an array | `F.split(col, pattern)`, then index with `[0]` |
| One row per array element | `F.explode(col)` (drops rows with empty arrays) or `F.explode_outer(col)` (keeps them with null) |

```python
# Ran. orders has columns id, cust, d (string), amount (nullable), tags (array<string>)
o = (orders.withColumn("d", F.to_date("d"))
           .withColumnRenamed("cust", "customer")
           .withColumn("amount", F.coalesce("amount", F.lit(0.0)))
           .drop("tags"))
# explode on tags returns (1,a) (1,b) (2,c) (2,c); explode_outer also returns (3,NULL)
# split "a-b" on "-" and take element 0 -> a
```

The difference between `explode` and `explode_outer` is a favourite: an order whose tag list is empty vanishes with `explode` (id 3 disappeared in the run) and survives as a null with `explode_outer`.

Conditions combine with `&`, `|` and `~` (not `and`/`or`), and each comparison needs parentheses: `(F.col("a") > 1) & (F.col("b") == "x")`.

## Deduplication, validation and missing data

`dropDuplicates()` with no arguments removes fully identical rows (`distinct()` is the same); with a column list it keeps one row per distinct combination of those columns. Missing data: `df.na.drop()` (or `dropna`) removes rows with nulls (`how="any"`/`"all"`, `subset=[...]`); `df.na.fill(value)` (or `fillna`) fills them; `F.coalesce` picks the first non-null across columns. Validation is usually a filter plus a count, for example "rows where amount < 0" or "ids that appear more than once":

```python
# Ran. 4 rows, id 2 appears twice.
orders.dropDuplicates(["id"]).select("id").orderBy("id")   # 1, 2, 3
```

## Aggregation

`groupBy("k").agg(F.count("*"), F.sum("x"), F.avg("x"))` aggregates per group; `df.agg(...)` with no groupBy aggregates the whole DataFrame. `F.approx_count_distinct` estimates distinct counts cheaply, `F.mean` is the average, and `df.summary()` / `df.describe()` produce count, mean, min and max (and quartiles for `summary`). Aggregates ignore nulls, so `F.count("amount")` counts only non-null values while `F.count("*")` counts rows.

```python
# Ran. Rows: amounts 50.0, null, null, 70.0
orders.agg(F.count("*"), F.approx_count_distinct("id"), F.mean("amount")).show()   # n=4, approx=3, mean=60.0
orders.select("id", "amount").summary("count", "mean", "min", "max").show()         # amount count = 2
```

## Dates

Spark has `DateType` and `TimestampType`. `F.to_date(col[, fmt])` parses a string to a date, `F.from_unixtime(sec[, fmt])` turns epoch seconds into a formatted string in the session time zone, `F.date_format(col, pattern)` formats a date, and `F.year`, `F.month`, `F.dayofmonth` extract components. The classic task "Unix epoch to date string, then extract a component":

```python
# Ran with spark.sql.session.timeZone = UTC, ts = 1428476400
dd.select(F.from_unixtime("ts"),                              # 2015-04-08 07:00:00
          F.to_date(F.from_unixtime("ts")),                   # 2015-04-08
          F.year(F.to_date(F.from_unixtime("ts"))),           # 2015
          F.date_format(F.from_unixtime("ts"), "yyyy-MM"))    # 2015-04
```

Because `from_unixtime` renders in the session time zone, the same epoch value can show a different clock time under a different `spark.sql.session.timeZone`.

## Combining DataFrames

```python
# Ran. c = (1 ana)(2 bo)(4 di);  p = (1 east 10)(2 west 20)
c.join(p, "id", "inner")    # ids 1, 2
c.join(p, "id", "left")     # ids 1, 2, 4  (4 has NULL region and q)
c.crossJoin(p.select("region")).count()   # 6  = 3 x 2
c.join(F.broadcast(p), ["id"], "left")    # physical plan: BroadcastHashJoin ... LeftOuter, BuildRight
c.join(p, (c.id == p.id) & (c.name != "zzz"), "inner")   # explicit condition; multiple conditions combine with &
```

- **Join types** (the third argument): `inner` (default), `left`, `right`, `full`, `left_semi`, `left_anti`, and `crossJoin` for the Cartesian product. Pass a list of column names for **multiple keys** (`["id", "date"]`); joining by name (rather than by expression) keeps one copy of the key column.
- **Broadcast join:** `F.broadcast(small_df)` hints that the small side should be sent to every executor, so the large side is not shuffled. Spark broadcasts automatically below `spark.sql.autoBroadcastJoinThreshold` (10 MB default); the hint overrides size statistics. The docs list the hint priority BROADCAST > MERGE > SHUFFLE_HASH > SHUFFLE_REPLICATE_NL when several are given.
- **Union:** `df1.union(df2)` (alias `unionAll`) stacks rows **by position** and keeps duplicates; `unionByName` matches by column name. In the run, `a(a, b)` unioned with `b(b, a)` by position put 20 into column `a` (a silent bug), while `unionByName` returned (1,10) and (2,20) correctly. To remove duplicates after a union call `distinct()`: the ids lists gave 5 rows with duplicates and 3 distinct.

**[📖 Performance tuning: join strategy](https://spark.apache.org/docs/latest/sql-performance-tuning.html)** - broadcast threshold, join hints and their priority

Under Spark 4's default ANSI mode, positional union of columns with incompatible types (a string column unioned with an integer column) raised a cast error in my run instead of silently coercing, so type-mismatched unions fail loudly.

## Schemas and input/output

A **schema** is a `StructType` of `StructField(name, dataType, nullable)`. You can write it as a DDL string (`"id int, customer string"`) or build it from `pyspark.sql.types`. Supplying a schema when reading avoids the extra pass of inference and prevents surprises; `df.printSchema()` prints it as a tree. Write with `df.write.mode(...).format(...).save(path)` and read with `spark.read.schema(schema).format(...).load(path)`. Save modes and partitioning are in Section 2.

**[📖 Spark SQL data types](https://spark.apache.org/docs/latest/sql-ref-datatypes.html)** - numeric, string, datetime, complex types and StructType

## Sorting, iterating and converting

`orderBy("a", F.col("b").desc())` sorts (a wide operation: global sort shuffles). `printSchema()` prints the schema. To pull data into Python: `collect()` returns a list of `Row` objects, `take(n)` / `head(n)` / `limit(n)` fetch a few, `toPandas()` returns a pandas DataFrame, and `toLocalIterator()` streams rows. All of them move data to the **driver**, so do not use them on big data. `foreach` and `foreachPartition` run a function on the executors.

```python
# Ran.
o.limit(2).collect()                      # [Row(id=1, customer='ana', ...), Row(id=2, ...)]
o.select("id").toPandas()["id"].tolist()  # [1, 2, 2, 3]
[r.id for r in o.select("id").collect()]  # [1, 2, 2, 3]
```

## User-defined functions

When no built-in function fits, write a UDF. A plain Python UDF runs row by row in a Python worker, so it is slower than built-ins (the Catalyst optimizer cannot see inside it) and must handle `None`. Prefer built-in functions whenever one exists; vectorized pandas UDFs are covered in Section 7.

```python
from pyspark.sql.functions import udf
from pyspark.sql.types import IntegerType

@udf(returnType=IntegerType())
def plus1(v):
    return None if v is None else v + 1

df.select(plus1("x").alias("y")).show()   # ran: 2, 3, 4
```

The guide also mentions UDFs "with or without stateful operators, including StateStores". Stateful processing keeps information between rows or micro-batches in a **state store**; in PySpark the streaming operators for it are `applyInPandasWithState` and the `transformWithState` family (those API names are from general knowledge; I did not check them in the docs). Those operators belong to Structured Streaming (Section 5); the docs note that `transformWithState` is recommended since Spark 4.0 for complex stateful applications and that the HDFS-backed state store is the default while RocksDB is an option for large state. I did not run a stateful UDF.

## Broadcast variables and accumulators

These are Spark's two kinds of shared variables.

- **Broadcast variable** - read-only data cached once on each executor rather than shipped with every task. Use it for a lookup table used in a function. `b = sc.broadcast({1: "one"})`, read with `b.value`.
- **Accumulator** - a variable tasks can only add to, and only the driver can read. Typical use: counting bad records. Updates inside **actions** are applied exactly once, even if tasks restart; updates inside **transformations** may be applied more than once on re-execution, and because transformations are lazy the accumulator stays at 0 until an action runs.

```python
# Ran.
b = sc.broadcast({1: "one", 2: "two"})
sc.parallelize([1, 2, 3]).map(lambda k: b.value.get(k, "?")).collect()   # ['one', 'two', '?']
acc = sc.accumulator(0)
sc.parallelize(range(5)).foreach(lambda v: acc.add(v))
acc.value                                                                  # 10
acc2 = sc.accumulator(0)
m = sc.parallelize(range(5)).map(lambda v: (acc2.add(1), v)[1])
acc2.value   # 0 before any action;  after m.count() it was 5
```

**[📖 Shared variables](https://spark.apache.org/docs/latest/rdd-programming-guide.html)** - broadcast variables, accumulators and their guarantees

## How the exam tests this section

- **Read the code:** which methods are transformations, which are actions; what a given join or union returns.
- **explode versus explode_outer; union versus unionByName; collect versus show.**
- **Null handling:** `coalesce`, `na.fill`, `na.drop`; aggregates skip nulls.
- **Broadcast join:** when and why; the hint and the 10 MB default.
- **Accumulator gotcha:** zero until an action.
- **UDF versus built-in:** prefer built-ins; UDFs must handle None.

## Key terms

- **DataFrame** - a distributed table of named, typed columns that Spark can optimize.
- **withColumn** - a DataFrame method that adds a column or replaces one of the same name, returning a new DataFrame.
- **explode** - a function that turns each element of an array column into its own row and drops rows with empty arrays.
- **explode_outer** - like explode but keeps rows with empty or null arrays, producing a null element.
- **dropDuplicates** - a method that removes duplicate rows, optionally considering only a subset of columns.
- **approx_count_distinct** - an aggregate that estimates the number of distinct values cheaply.
- **Broadcast join** - a join strategy that sends the small side to every executor so the large side is not shuffled.
- **union** - a method that stacks two DataFrames by column position and keeps duplicate rows.
- **unionByName** - a method that stacks two DataFrames by matching column names instead of positions.
- **Cross join** - a join that returns every combination of rows from both sides.
- **Schema** - a StructType describing the names, types and nullability of a DataFrame's columns.
- **UDF** - a user-defined function applied to columns; a Python UDF runs row by row and is slower than built-ins.
- **Broadcast variable** - a read-only value cached on each executor and shared by all tasks.
- **Accumulator** - a shared variable that tasks can only add to and only the driver can read.
- **State store** - the storage for per-key state kept between micro-batches in stateful streaming operations.
