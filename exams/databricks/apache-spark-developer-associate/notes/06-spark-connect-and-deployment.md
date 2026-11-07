---
last-updated: 2026-11-07
applies-to: Databricks Associate Developer for Apache Spark exam guide (published 2026-10-08, read 2026-11-07)
difficulty: intermediate
reading-time: 15 min
---

# Section 6: Using Spark Connect to Deploy Applications

Weighted **5%**, about two questions. Two objectives: describe the features of Spark Connect, and describe the deployment mode types (client, cluster, local).

## Table of Contents

1. [Spark Connect](#spark-connect)
2. [Deployment modes](#deployment-modes)
3. [How the exam tests this section](#how-the-exam-tests-this-section)
4. [Key terms](#key-terms)

---

## Spark Connect

In classic Spark your application's driver code runs inside the Spark driver process, so the client and the cluster are tightly bound. **Spark Connect**, introduced in Spark 3.4, splits them. A thin client library translates DataFrame operations into **unresolved logical plans**, encodes them with protocol buffers and sends them over **gRPC** to a Spark Connect server, which turns them into Spark's logical plans and runs them with the normal engine and optimizer. Results stream back as **Apache Arrow**-encoded batches.

**[📖 Spark Connect overview](https://spark.apache.org/docs/latest/spark-connect-overview.html)** - architecture, differences from classic Spark, how to connect

What that buys you, according to the docs:

- **Stability** - each application runs in its own client process, so a memory-hungry client does not take down the shared driver, and clients can have their own dependencies.
- **Upgradability** - the server can be upgraded independently of clients, provided the protocol stays backward compatible.
- **Debuggability and observability** - you can debug from an IDE and use your own logging and metrics libraries.
- **Embeddability** - the thin client fits application servers, IDEs, notebooks and other languages.

What changes for your code:

- The client does not run in the driver process and cannot reach driver JVM internals; in PySpark there is no Py4J, so private fields such as `df._jdf` are unavailable.
- The protocol is built on logical plans, so **RDD** APIs and the `SparkContext` are not supported; neither is static Spark configuration access. You get a *session*.
- Spark Connect has no built-in authentication; it works with authenticating proxies.

How to connect (the default port is 15002):

```python
# Per the Spark docs; not run (needs a Spark Connect server).
from pyspark.sql import SparkSession
spark = SparkSession.builder.remote("sc://localhost").getOrCreate()
# or: export SPARK_REMOTE="sc://localhost"   or:   ./bin/pyspark --remote "sc://localhost"
type(spark)   # pyspark.sql.connect.session.SparkSession  (".connect." in the name)
```

The client-only package for standalone Python applications is `pyspark-client`. On Databricks, serverless and standard-access-mode compute use Spark Connect style connectivity, which is why RDD code and `SparkContext` often fail there (this Databricks detail is general knowledge that I did not check on a docs page in this pass).

## Deployment modes

`--deploy-mode` on `spark-submit` says where the **driver** runs.

| Mode | Where the driver runs | Typical use |
|---|---|---|
| **Client** (default) | In the `spark-submit` process on the submitting machine; input and output attached to the console | Interactive work and shells; submit from a gateway machine near the workers |
| **Cluster** | On a worker node inside the cluster | Production jobs submitted from far away, for example a laptop, to cut driver-executor latency |
| **Local** | Everything in one JVM on one machine (master `local`, `local[K]`, `local[*]`) | Development and tests |

**[📖 Submitting applications](https://spark.apache.org/docs/latest/submitting-applications.html)** - deploy modes and master URLs

Master URLs: `local` is one thread; `local[K]` K threads; `local[*]` as many threads as logical cores (my runs used `local[1]` and `local[2]`); `spark://HOST:7077` a standalone cluster; `yarn`; `k8s://HOST:PORT`. Standalone mode does not support cluster deploy mode for Python applications. In configuration, values set in code on the SparkConf override `spark-submit` flags, which override `spark-defaults.conf`.

## How the exam tests this section

- **Where is the driver?** Client: with the submitter. Cluster: on a worker. Local: same JVM.
- **Spark Connect features:** thin client, gRPC, unresolved logical plans, Arrow results, independent upgrades, no RDDs.
- **Why Connect:** isolation and remote access from any application.

## Key terms

- **Spark Connect** - a client-server architecture where a thin client sends unresolved logical plans over gRPC to a remote Spark server.
- **Unresolved logical plan** - the language-neutral description of a DataFrame query that Spark Connect clients send to the server.
- **gRPC** - the remote procedure call protocol Spark Connect uses between client and server.
- **Client mode** - a deploy mode where the driver runs in the process that submitted the application.
- **Cluster mode** - a deploy mode where the driver runs on a worker node inside the cluster.
- **Local mode** - running Spark entirely in one JVM on one machine, for development and testing.
- **spark-submit** - the command-line script that launches a Spark application on a cluster.
- **Master URL** - the setting that tells Spark which cluster manager or local mode to use, such as local[*] or yarn.
