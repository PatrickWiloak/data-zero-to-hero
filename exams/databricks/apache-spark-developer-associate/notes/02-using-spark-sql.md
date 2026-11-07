---
last-updated: 2026-11-07
applies-to: Databricks Associate Developer for Apache Spark exam guide (published 2026-10-08, read 2026-11-07)
difficulty: intermediate
reading-time: 25 min
---

# Section 2: Using Spark SQL

Weighted **20%**, about nine questions. Four objectives: read and write DataFrames from common data sources (JDBC, files), including overwrite and partitioning by column; run SQL directly on files and know the save modes; save to persistent tables with sorting and partitioning; and register DataFrames as temporary views.

Examples ran on PySpark 4.3.0 in local mode on 2026-11-07 unless marked "not run".

## Table of Contents

1. [Reading and writing data sources](#reading-and-writing-data-sources)
2. [Save modes](#save-modes)
3. [Partitioning on write](#partitioning-on-write)
4. [SQL on files and temporary views](#sql-on-files-and-temporary-views)
5. [Persistent tables, bucketing and sorting](#persistent-tables-bucketing-and-sorting)
6. [How the exam tests this section](#how-the-exam-tests-this-section)
7. [Key terms](#key-terms)

---

## Reading and writing data sources

Every read starts at `spark.read`, every write at `df.write`. Name the format explicitly or use a shortcut method. If you do not name one, Spark uses the default source, Parquet unless `spark.sql.sources.default` is changed. Built-in short names include `json`, `parquet`, `jdbc`, `orc`, `csv` and `text`.

**[📖 Generic load/save functions](https://spark.apache.org/docs/latest/sql-data-sources-load-save-functions.html)** - default source, formats, save modes and persistent tables

```python
df = spark.read.format("json").load("/data/people.json")        # explicit format
df = spark.read.parquet("/data/people.parquet")                 # shortcut
df.write.format("parquet").mode("overwrite").save("/out/people")
```

For **CSV** you usually set options: `header`, `inferSchema` (costs an extra pass over the data) and, better, an explicit schema. For **JDBC** you give a URL, a table (or query) and credentials, and for large tables you split the read across executors with `partitionColumn`, `lowerBound`, `upperBound` and `numPartitions`; without those the whole table is read by a single task. (JDBC options are standard Spark; I did not run a JDBC read because it needs a database.)

```python
# Not run: needs a database and driver.
jdbc_df = (spark.read.format("jdbc")
           .option("url", "jdbc:postgresql://host:5432/shop")
           .option("dbtable", "public.orders")
           .option("user", "reader").option("password", "...")
           .option("partitionColumn", "id").option("lowerBound", 1)
           .option("upperBound", 1000000).option("numPartitions", 8)
           .load())
```

## Save modes

A write must decide what to do if data already exists at the target. The four modes:

| Mode | Behaviour if data exists |
|---|---|
| `error` / `errorifexists` (default) | Throws an exception |
| `append` | Adds the new rows |
| `overwrite` | Replaces the existing data |
| `ignore` | Does nothing, like `CREATE TABLE IF NOT EXISTS` |

The docs add a warning worth remembering: save modes do not use locking and are not atomic, and with overwrite the existing data is deleted before the new data is written.

```python
# Ran on PySpark 4.3.0 locally; df has 2 rows, written partitioned by customer.
# append -> 4 rows, ignore -> 4 rows (no change), overwrite -> 2 rows;
# a write with the default mode to an existing path raised AnalysisException.
```

## Partitioning on write

`partitionBy("col")` writes one directory per value, such as `customer=ana/`. It makes later reads that filter on that column skip whole directories, which is partition pruning. The docs advise using `partitionBy` for low-cardinality columns, since each value becomes a directory; high-cardinality columns create a flood of small files.

```python
# Ran on PySpark 4.3.0 locally.
o.write.mode("overwrite").partitionBy("customer").parquet(path)
sorted(os.listdir(path))   # ['customer=ana', 'customer=bo', 'customer=cy']
spark.read.parquet(path).printSchema()   # the partition column comes back as a column: id, d, amount, customer
```

Do not confuse the on-disk partitioning (`partitionBy`) with in-memory partitions (`repartition`): the first is directory layout for reads, the second is parallelism during processing.

## SQL on files and temporary views

You can query a file directly with SQL, without loading it first, by prefixing the path with the format:

```python
# Ran on PySpark 4.3.0 locally.
spark.sql(f"select count(*) n from parquet.`{path}`").collect()   # [Row(n=4)]
```

The same pattern works with `json`, `csv`, `orc`, `text` and `delta` (when the Delta format is available, as on Databricks). To query a DataFrame with SQL, register it as a **temporary view**:

```python
o.createOrReplaceTempView("o")
spark.sql("select customer, sum(amount) s from o group by customer order by customer").show()
# ana 50.0 / bo 0.0 / cy 70.0   (ran)
```

A temporary view lives only in the current session. A **global temporary view** (`createGlobalTempView`, queried as `global_temp.name`) is shared across sessions of the same application. Neither is stored anywhere permanent.

## Persistent tables, bucketing and sorting

`df.write.saveAsTable("name")` stores the data and registers the table in the metastore, so it survives restarts, and you read it back with `spark.table("name")`. If you give a `path` option the table is external: dropping it leaves the data. Without a path, the data lives in the warehouse directory and dropping the table deletes it. `partitionBy` works with both `save` and `saveAsTable`; **`bucketBy`** and **`sortBy`** work only with `saveAsTable` (persistent tables). Bucketing hashes a column into a fixed number of buckets and is meant for high-cardinality columns, for example to avoid a shuffle in repeated joins; sorting within buckets helps lookups.

```python
# Per the Spark docs; not run (needs a metastore-backed table).
df.write.bucketBy(42, "name").sortBy("age").saveAsTable("people_bucketed")
df.write.partitionBy("favorite_color").bucketBy(42, "name").saveAsTable("users_partitioned_bucketed")
```

## How the exam tests this section

- **Save mode questions:** pick the mode from the story (rerun-safe load: overwrite; add rows: append; protect existing: error or ignore).
- **partitionBy versus bucketBy:** low cardinality gets directories; high cardinality gets buckets; bucketing needs `saveAsTable`.
- **Temp views:** session-scoped; `createOrReplaceTempView` makes a DataFrame queryable by SQL.
- **SQL on files:** ``SELECT * FROM parquet.`/path` ``.
- **JDBC parallel reads** need partition column, bounds and number of partitions.

## Key terms

- **Save mode** - the write setting that decides what happens when data already exists at the target: error, append, overwrite or ignore.
- **partitionBy** - a writer option that stores output in one directory per value of the given columns, enabling partition pruning on reads.
- **bucketBy** - a writer option for persistent tables that hashes a column into a fixed number of buckets.
- **Temporary view** - a session-scoped name for a DataFrame that lets you query it with SQL.
- **Global temporary view** - a view shared across sessions of one Spark application, queried through the global_temp database.
- **saveAsTable** - a writer method that stores data and registers a persistent table in the metastore.
- **External table** - a table created with an explicit path, where dropping the table leaves the data in place.
- **Partition pruning** - skipping directories whose partition values cannot match a query's filter.
- **JDBC** - the database connectivity interface Spark uses to read from and write to relational databases.
