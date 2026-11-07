---
last-updated: 2026-11-07
applies-to: Databricks Associate Developer for Apache Spark exam guide (published 2026-10-08, read 2026-11-07)
difficulty: intermediate
reading-time: 15 min
---

# Section 7: Using Pandas API on Apache Spark

Weighted **5%**, about two questions. Two objectives: explain the advantages of the pandas API on Spark, and create and invoke pandas UDFs.

Examples ran on PySpark 4.3.0 with pandas 2.x on 2026-11-07. One local observation: with pandas 3.x installed, `import pyspark.pandas` failed with an ImportError in this PySpark version, and PySpark printed a warning that it does not yet fully support pandas 3. Use pandas below 3 with PySpark 4.3.0.

## Table of Contents

1. [The pandas API on Spark](#the-pandas-api-on-spark)
2. [Pandas UDFs](#pandas-udfs)
3. [How the exam tests this section](#how-the-exam-tests-this-section)
4. [Key terms](#key-terms)

---

## The pandas API on Spark

**pandas** is the standard Python data library, but it runs on one machine and holds everything in memory. The **pandas API on Spark** (`import pyspark.pandas as ps`) lets you write pandas-style code that runs distributed on Spark, so a data scientist can scale a familiar script past one machine's memory without learning the DataFrame API. The advantages are exactly that: familiar syntax, Spark's scale, and easy conversion between pandas, pandas-on-Spark and PySpark DataFrames.

```python
# Ran.
import pyspark.pandas as ps
psdf = ps.DataFrame({"a": [1, 1, 2], "b": [4.0, 5.0, 6.0]})
psdf.groupby("a")["b"].mean().sort_index()    # a=1 -> 4.5, a=2 -> 6.0
psdf.to_spark()                                # a PySpark DataFrame
```

Behaviours that differ from single-machine pandas, per the tutorial's section headings (I could read only the page's outline, so these are pointers to check, not detailed claims): rows have a configurable default index type (`sequence`, `distributed` or `distributed-sequence`); combining objects built on different DataFrames is restricted unless you enable `compute.ops_on_diff_frames`; not every pandas function is implemented; and operations that need a global order or a single partition can be slow. The best-practice rule is the same as for all Spark work: avoid shuffles and avoid pulling everything onto one partition.

**[📖 Pandas API on Spark tutorials](https://spark.apache.org/docs/latest/api/python/tutorial/pandas_on_spark/index.html)** - overview, index types, best practices, conversion to and from PySpark

## Pandas UDFs

A Python UDF handles one row at a time. A **pandas UDF** (vectorized UDF) receives a whole batch as a `pandas.Series`, computes on it with pandas, and returns a Series of the same length; Spark moves the data between the JVM and Python in columnar **Apache Arrow** batches. That batch processing avoids per-row overhead, so pandas UDFs are usually faster than row-at-a-time UDFs.

**[📖 Pandas UDFs](https://spark.apache.org/docs/latest/api/python/tutorial/sql/arrow_pandas.html)** - types, type hints, Arrow batch size and grouped map

```python
# Ran.
import pandas as pd
from pyspark.sql.functions import pandas_udf

@pandas_udf("long")
def times2(s: pd.Series) -> pd.Series:      # Series to Series
    return s * 2

df.select(times2("x").alias("y")).show()    # x = 1,2,3  ->  y = 2,4,6
```

The forms to recognise, all selected by **type hints** (the older `PandasUDFType` is deprecated):

| Form | Signature | Use |
|---|---|---|
| Series to Series | `pd.Series -> pd.Series` | Column-wise transform; output length equals input length |
| Iterator of Series | `Iterator[pd.Series] -> Iterator[pd.Series]` | Expensive one-time setup, such as loading a model, done once per partition |
| Series to scalar | `pd.Series -> float` | Aggregation, used with `groupBy().agg()` or windows |
| Grouped map | `groupby().applyInPandas(func, schema)` | A pandas DataFrame per group in, a pandas DataFrame out |

```python
# Ran: grouped map subtracts each group's mean.
def sub(pdf: pd.DataFrame) -> pd.DataFrame:
    return pdf.assign(v=pdf.v - pdf.v.mean())
g.groupby("id").applyInPandas(sub, schema="id int, v double")    # id1: -1.0, 1.0   id2: 0.0
```

Caution from the docs: with grouped map, all data for a group is loaded into memory before the function runs, and `maxRecordsPerBatch` does not apply to groups, so a skewed group can cause out-of-memory errors. Arrow batch size for other pandas UDFs is controlled by `spark.sql.execution.arrow.maxRecordsPerBatch` (default 10,000 rows).

## How the exam tests this section

- **Why use the pandas API on Spark:** familiar pandas code that scales out.
- **Pandas UDF versus Python UDF:** vectorized batches with Arrow versus row at a time.
- **Which form:** Series to Series for transforms; grouped map for per-group pandas logic; iterator for expensive initialization.
- **Same length rule:** a Series to Series UDF returns as many values as it received.

## Key terms

- **pandas API on Spark** - a pandas-compatible API in pyspark.pandas that runs distributed on Spark.
- **Pandas UDF** - a vectorized user-defined function that processes batches as pandas Series using Apache Arrow.
- **Apache Arrow** - a columnar in-memory format Spark uses to move data efficiently between the JVM and Python.
- **Series to Series** - a pandas UDF form that takes one or more Series and returns a Series of the same length.
- **Iterator of Series** - a pandas UDF form that processes a stream of batches so expensive setup runs once per partition.
- **applyInPandas** - a grouped operation that applies a function to a pandas DataFrame for each group and returns a pandas DataFrame.
- **Type hint** - the Python annotation that tells Spark which kind of pandas UDF a function is.
