---
last-updated: 2026-10-22
applies-to: DP-420 skills measured as of 2026-10-06
difficulty: intermediate
reading-time: 30 min
---

# Fabric Analytics, Fleets, Masking, Failover and Change Feed Modes

> **Exam domains:** pieces of all three. "Design and implement operational analytics" (Domain 3), "Implement Fleet management and monitoring", "Implement database security controls" (dynamic data masking) and "Implement availability and recovery solutions" (partition failover) from Domain 2, and "Implement change feed" (all versions and deletes mode, copy jobs) plus "AI-assisted tools" (Agent Kit) from Domain 1. These are the skills the 2026-10-06 change log marks as new or majorly changed outside AI retrieval, which has [its own note](cosmos-db-ai-retrieval-agent-memory.md).

## Table of Contents

1. [Operational analytics: why not query the OLTP container](#operational-analytics-why-not-query-the-oltp-container)
2. [Mirroring Azure Cosmos DB into Fabric](#mirroring-azure-cosmos-db-into-fabric)
3. [Cosmos DB in Microsoft Fabric](#cosmos-db-in-microsoft-fabric)
4. [Querying with T-SQL and Spark](#querying-with-t-sql-and-spark)
5. [Fleets, fleetspaces and pools](#fleets-fleetspaces-and-pools)
6. [Fleet analytics](#fleet-analytics)
7. [Dynamic data masking](#dynamic-data-masking)
8. [Per-partition automatic failover](#per-partition-automatic-failover)
9. [Change feed modes](#change-feed-modes)
10. [Container copy jobs](#container-copy-jobs)
11. [The Agent Kit](#the-agent-kit)
12. [Worked scenarios](#worked-scenarios)
13. [How the exam tests this](#how-the-exam-tests-this)

---

## Operational analytics: why not query the OLTP container

Cosmos DB is built for point reads and partition-scoped queries at predictable latency. An analyst's question - "revenue by region by week for the last year" - is the opposite shape: it scans everything, groups, and aggregates. Run it against the transactional container and it fans out across every partition, consumes RUs your application needed, and may throttle real users with 429s. That is the core HTAP (hybrid transactional/analytical processing) problem: the same data, two workloads that should never share compute.

The old DP-420 answer was the analytical store plus Azure Synapse Link. The 2026-10-06 outline replaces that objective with **Microsoft Fabric**: either mirror an Azure Cosmos DB account into Fabric, or build on Cosmos DB in Fabric directly. In both cases the analytical copy lives in **OneLake** as Delta tables, and analytics engines read that copy, never the transactional replicas.

**[📖 Analytics and BI with Fabric](https://learn.microsoft.com/en-us/azure/cosmos-db/analytics-and-business-intelligence-overview)** - Microsoft's overview of the options

---

## Mirroring Azure Cosmos DB into Fabric

Mirroring continuously replicates an Azure Cosmos DB for NoSQL database into Fabric OneLake in near real time, with no pipeline to build ("no-ETL"). The properties that make it the default answer for "analytics on operational data":

- **No RU consumption.** Replication does not draw on the throughput you provisioned for the application. Workload isolation is complete.
- **Open format.** Data lands as Delta (Parquet) in OneLake, so every Fabric engine - the SQL analytics endpoint, Spark, Power BI in Direct Lake mode - can read it, and so can tools outside Microsoft.
- **Inserts, updates and deletes** all replicate.

The prerequisites and limits are where the exam finds its distractors:

- **Continuous backup is required** (7-day or 30-day; 7-day is free and recommended if you are enabling it only for mirroring). Mirroring reads changes through that machinery; it does **not** use the analytical store or the change feed, and you can keep using those independently.
- **API for NoSQL only.** MongoDB (RU), Cassandra, Gremlin and Table accounts cannot be mirrored, nor can sovereign clouds.
- **Source authentication** is read-write account keys or Entra ID with data plane RBAC (`readMetadata` and `readAnalytics`); read-only keys and managed identities are not supported. If you rotate keys, update the mirroring connection or replication fails.
- **Customer-managed keys on OneLake** are not supported for the mirror.
- Mirroring produces a **read-only** copy. The SQL analytics endpoint can define views and query, but cannot change the data.

Each mirrored database gives you two items in the workspace: the **mirrored database** (replication status, start and stop controls, and a read-only data explorer view of the source, which does consume source RUs) and an auto-generated **SQL analytics endpoint** over the Delta tables, with one table per container.

**[📖 Mirroring Azure Cosmos DB](https://learn.microsoft.com/en-us/fabric/database/mirrored-database/azure-cosmos-db)** - how replication works and what you get
**[📖 Mirroring limitations](https://learn.microsoft.com/en-us/fabric/database/mirrored-database/azure-cosmos-db-limitations)** - account types, security and data limits

---

## Cosmos DB in Microsoft Fabric

Cosmos DB in Fabric is the same engine as Azure Cosmos DB for NoSQL, provisioned and managed as a Fabric item inside a workspace instead of as an Azure resource. Its data appears in OneLake automatically, so the analytical copy comes for free. Vector, full-text and hybrid search are built in.

The exam objective is literally "Choose between Azure Cosmos DB and Cosmos DB in Microsoft Fabric". The deciding factors:

| Need | Points to |
|---|---|
| Full control of account settings: regions, consistency, multi-region writes, private networking, CMK | Azure Cosmos DB (mirror it into Fabric for analytics) |
| Stored procedures, triggers or UDFs | Azure Cosmos DB (not supported in Fabric) |
| Private Link or customer-managed keys today | Azure Cosmos DB (not currently available in Fabric) |
| An app or serving layer whose team already lives in Fabric, minimal database administration, analytics on the same data by default | Cosmos DB in Fabric |
| Serving curated OneLake data back to an app at low latency ("reverse ETL") | Cosmos DB in Fabric, or an Azure Cosmos DB target |

Current Fabric limits worth knowing: containers must use **autoscale** throughput (portal default 5,000 RU/s max, SDK minimum 1,000, ceiling 50,000 without a support ticket), at most 25 containers per database, and Fabric item permissions apply to all Cosmos DB artifacts in a workspace. These limits change; check the page before an exam sitting.

**[📖 What is Cosmos DB in Microsoft Fabric?](https://learn.microsoft.com/en-us/fabric/database/cosmos-db/overview)** - positioning and integration scenarios
**[📖 Cosmos DB in Fabric limitations](https://learn.microsoft.com/en-us/fabric/database/cosmos-db/limitations)** - quotas, security and programmability gaps

---

## Querying with T-SQL and Spark

Once data is in OneLake, two engines cover the objective "Query Azure Cosmos DB mirrored JSON data in Microsoft Fabric with T-SQL and Spark".

**T-SQL through the SQL analytics endpoint.** Each container is a table. Top-level properties become columns; nested objects and arrays arrive as JSON text that you unpack with `OPENJSON` and `JSON_VALUE`. You can join a mirrored Cosmos DB table with a Warehouse or Lakehouse table in the same workspace, which is the whole point: orders from Cosmos DB joined to finance data from SQL, in one query, with zero RU cost.

```sql
SELECT o.customerId,
       JSON_VALUE(o.shipping, '$.country') AS country,
       SUM(li.qty * li.price)              AS revenue
FROM   orders AS o
CROSS APPLY OPENJSON(o.lineItems)
       WITH (qty INT '$.qty', price DECIMAL(10,2) '$.price') AS li
GROUP BY o.customerId, JSON_VALUE(o.shipping, '$.country');
```

One documented limit: JSON strings over 8 KB are truncated when queried from the SQL analytics endpoint. The workaround Microsoft gives is a Lakehouse shortcut to the mirrored data and a Spark notebook.

**Spark.** In a notebook you can read the mirrored Delta tables through a Lakehouse shortcut like any other Delta table. Separately, the **Azure Cosmos DB Spark connector** (format `cosmos.oltp`) reads from and writes to the *transactional* container - in Azure Cosmos DB or in Cosmos DB in Fabric. That is the tool for the objective "Read and write data into Azure Cosmos DB from Microsoft Fabric": for example, scoring customers in a notebook and writing the scores back to a container the app reads.

```python
df = (spark.read.format("cosmos.oltp")
      .options(**config)                       # endpoint, database, container, auth
      .option("spark.cosmos.read.inferSchema.enabled", "true")
      .load())

scores.write.format("cosmos.oltp").options(**config).mode("append").save()
```

Know the difference: reading **mirrored** data costs no RUs; reading or writing through the **connector** hits the transactional container and consumes RUs, so throttle it (the connector supports throughput control) or schedule it off-peak.

**[📖 Spark notebooks with Cosmos DB in Fabric](https://learn.microsoft.com/en-us/fabric/database/cosmos-db/how-to-use-spark-notebooks)** - connector configuration, read, write and catalog API
**[📖 Query nested mirrored data](https://learn.microsoft.com/en-us/fabric/database/mirrored-database/azure-cosmos-db-how-to-query-nested)** - OPENJSON patterns on the SQL analytics endpoint

---

## Fleets, fleetspaces and pools

A B2B SaaS vendor often gives each customer its own Cosmos DB account: strong performance isolation (no noisy neighbour on RUs) and strong security isolation (separate keys, CMK, network rules). The cost is operational. Each account is provisioned for its own peak, most sit idle most of the time, and nobody can see usage across hundreds of accounts in one place. **Fleets** address exactly that.

- A **fleet** maps to one multi-tenant application and groups many database accounts, even across subscriptions and resource groups.
- A **fleetspace** is a group of accounts inside a fleet with similar performance characteristics. Every account in a fleet belongs to exactly one fleetspace.
- A **pool** is optional, set on a fleetspace: a quantity of shared RU/s that any resource in the fleetspace's accounts can draw on.

The throughput rule: each database or container keeps its own **dedicated RU/s** (the guaranteed floor; for autoscale, the autoscale max), and dedicated RU/s are consumed **first**. Only when a resource exceeds its dedicated throughput does it draw from the pool, instead of being throttled. So you size each tenant for its typical load and let the pool absorb spikes.

Microsoft is explicit about when pooling does not help: it pays off with many tenants whose busy periods do not coincide. With few tenants, or tenants that are all idle or all busy at once, there is nothing to share.

**[📖 Fleets overview](https://learn.microsoft.com/en-us/azure/cosmos-db/fleet)** - concepts and limits
**[📖 Fleet pools](https://learn.microsoft.com/en-us/azure/cosmos-db/fleet-pools)** - dedicated vs pooled RU/s with a worked ISV example

---

## Fleet analytics

Fleet analytics (preview) exports cost, usage and configuration data for every account in a fleet, aggregated **hourly**, as Delta tables in a star schema into **Fabric OneLake or ADLS Gen2**. You then query it with SQL, KQL or Spark and build Power BI reports: which tenants are most active, how provisioning trends over time, when keys were last rotated.

Pick the monitoring tool by the question being asked:

| Tool | Grain | Scope | Use for |
|---|---|---|---|
| Azure Monitor metrics | 1 minute, pre-aggregated, 90-day retention | One account | Near real-time health, metric alerts |
| Azure Monitor logs (Log Analytics) | Per request | One account | Root cause of a specific slow or failed request |
| Fleet analytics | 1 hour, pre-aggregated | Whole fleet | Cross-account trends, cost attribution, capacity planning |

**[📖 Fleet analytics (preview)](https://learn.microsoft.com/en-us/azure/cosmos-db/fleet-analytics)** - schema, destinations and the comparison above

---

## Dynamic data masking

Dynamic data masking (DDM) redacts sensitive values in query results for users who lack an **unmask** permission, while the stored data stays unchanged. It is enforced server-side, at query time, through **data plane RBAC**.

Masking strategies:

| Strategy | Effect |
|---|---|
| Default | Strings become `XXXX`, numbers become `0`, booleans become `false` |
| Custom string | `MaskSubstring(start, length)` masks part of a string, e.g. `Washington` becomes `WasXXXXXon` |
| Email | Keeps the first letter and the domain ending, e.g. `aXXXX@XXXXXXXXX.com` |

Operational facts the exam can test:

- DDM requires **Microsoft Entra ID** identities; it does not work with account keys (a key is all-powerful and carries no role to evaluate).
- Unmasking is a data action, `.../containers/items/unmask`. The built-in **Data Contributor** role includes it; the built-in **Data Reader** role does not. To let an analyst read unmasked data without write rights, create a custom role with read, query and unmask actions.
- Enabling DDM on an account is **one-way**: it cannot be turned off afterwards.

Masking is not encryption and not authorization. A user without read permission still cannot read; a user with read but no unmask sees masked values; the data at rest is protected by encryption, not by DDM.

**[📖 Dynamic data masking](https://learn.microsoft.com/en-us/azure/cosmos-db/dynamic-data-masking)** - policy setup and role definitions

---

## Per-partition automatic failover

In a single-write-region account, a regional outage used to mean failing over the **whole account**: every partition moves to the next region, an operation that could take 15-30 minutes or more and often needed a human to start it. Multi-region writes avoid the wait but bring conflict resolution.

**Per-partition automatic failover (PPAF)** sits between the two. Each partition independently notices that its write region is unhealthy (within tens of seconds), picks a new write region using the account's **failover priority order** and replica freshness, and moves - alone. Healthy partitions keep writing to the original region. Microsoft quotes an RTO under 3 minutes at P99, versus 15-30 minutes or more for account-level failover.

What stays the same: your consistency level, region list, partition keys, endpoint and connection string. The SDK caches routing per partition key range and transparently sends the next write for an affected partition to its new region; an up-to-date SDK is the only application change. Failback is automatic too, using incremental catch-up of only the writes made during the outage.

On divergent writes: with Strong consistency nothing diverges (RPO = 0). With Session, Consistent Prefix or Eventual, a few acknowledged writes in the failed region may not have replicated; on failback PPAF reconciles them with **last-writer-wins** on the system timestamp by default. You can opt out via support and reconcile from the conflict feed yourself.

The guardrails are prime exam material. While PPAF is enabled you cannot use **Bounded Staleness**, **serverless** (provisioned or autoscale throughput is required), **Synapse Link**, or **in-account restore**, cannot switch between Strong and non-Strong, and must disable PPAF before a partition merge or taking a region offline.

**[📖 Per-partition automatic failover](https://learn.microsoft.com/en-us/azure/cosmos-db/per-partition-automatic-failover)** - how it works, limits, reconciliation

---

## Change feed modes

The change feed is the ordered log of changes to a container. The exam now asks you to choose a **mode** as well as a consumption model.

| | Latest version | All versions and deletes |
|---|---|---|
| Captures | Creates and updates | Creates, updates, **deletes** (including TTL expirations) |
| Intermediate versions | No: only the latest state at read time | Yes: every change, in order |
| Metadata | None about operation type | Operation type, and whether a delete came from TTL |
| Retention | Whole life of the container; can start from the beginning | Only within the continuous backup retention window |
| Prerequisite | None (default) | **Continuous backup** on the account; NoSQL only |

The classic workaround for deletes in latest version mode is a **soft delete**: set `deleted: true` and a short TTL, so the change feed sees an update and TTL removes the item later. All versions and deletes mode makes that unnecessary, which is why it appears in scenarios about auditing deletes or keeping a downstream copy exactly in sync.

The consumption model is a separate choice:

- **Change feed processor** (push): the SDK distributes partition key ranges across instances, checkpoints progress in a **lease container**, and rebalances when instances come and go. Use it for always-on processing at scale; Azure Functions' Cosmos DB trigger is built on it.
- **Pull model**: you request pages with a continuation token and manage state yourself. Use it when you need full control of pacing, or to read a specific range.

Each consumer reads in one mode, but different applications can read the same container in different modes.

**[📖 Change feed modes](https://learn.microsoft.com/en-us/azure/cosmos-db/change-feed-modes)** - the full comparison
**[📖 Change feed processor](https://learn.microsoft.com/en-us/azure/cosmos-db/change-feed-processor)** - leases, instances and estimator

---

## Container copy jobs

Several container settings cannot change in place: the **partition key**, unique keys, the container or database name, and whether throughput is at database or container level. Some features, such as hierarchical partition keys, need a new container. **Container copy jobs** (preview) move the data for you, using server-side compute allocated on the destination account.

- **Offline copy:** stop writes to the source, run the job, wait for completion, repoint the application. Simple; needs downtime.
- **Online copy:** requires **continuous backup** and **all versions and deletes** change feed on the source, plus the `EnableOnlineContainerCopy` capability. The job copies existing data and then replicates ongoing changes using the all versions and deletes feed. When caught up, you stop writes briefly, call the completion API, and repoint. Replace and delete operations on the source cost an extra 50-100% of the base write RUs while online copy is enabled.

**[📖 Container copy jobs](https://learn.microsoft.com/en-us/azure/cosmos-db/container-copy)** - online and offline procedures

---

## The Agent Kit

The newest objective is "Design and implement Azure Cosmos DB solutions by using AI-assisted tools", and it names one tool: the **Azure Cosmos DB Agent Kit** (`AzureCosmosDB/cosmosdb-agent-kit`). It is a set of skills in the Agent Skills format that an AI coding assistant loads on demand, so the assistant follows Cosmos DB best practice when it writes or reviews your code. Install it with `npx skills add AzureCosmosDB/cosmosdb-agent-kit`, then ask in natural language ("help me choose a partition key for my orders collection", "why is this query so expensive").

Its coverage maps closely onto the exam: partition key selection, hot partition prevention, data modeling, query optimization, indexing (including vector), SDK usage (client reuse, retries, error handling), security patterns, throughput and scaling, global distribution, monitoring, vector, full-text and hybrid search, and design patterns such as change feed and materialized views.

How to reason about it: the kit makes an assistant's suggestions better, it does not make them correct. The exam frames it as a way to "follow recommended solutions" and "accelerate AI application development"; a reviewer still owns the partition key decision.

**[📖 Agent Kit overview](https://learn.microsoft.com/en-us/azure/cosmos-db/gen-ai/agent-kit/overview)** - what the kit covers
**[📖 Use the Agent Kit](https://learn.microsoft.com/en-us/azure/cosmos-db/gen-ai/agent-kit/usage)** - example prompts and best use cases

---

## Worked scenarios

### Scenario 1: Analysts are throttling the app

**Situation:** A retailer's Power BI reports query the orders container directly. At month end the app sees 429s.

**Answer:** Enable continuous backup (7-day) and mirror the database into Fabric. Point reports at the SQL analytics endpoint or a Direct Lake semantic model. Mirroring consumes no RUs, so the reports stop competing with the app.

### Scenario 2: A downstream copy that misses deletes

**Situation:** A service keeps a search projection in sync from the change feed processor. Deleted products never disappear from the projection.

**Answer:** Latest version mode does not capture deletes. Enable continuous backup and read in all versions and deletes mode, handling the delete operation type. (The older answer, soft delete with TTL, still works if continuous backup is not an option.)

### Scenario 3: Changing a partition key without downtime

**Situation:** A container partitioned by `/status` has three hot partitions. The team wants `/customerId` and cannot take an outage.

**Answer:** Create the target container with the new key, enable continuous backup, all versions and deletes and online copy on the source, run an online container copy job, then cut over after calling the completion API.

### Scenario 4: Hundreds of tenant accounts, all over-provisioned

**Situation:** An ISV runs one account per customer, each provisioned at 4,000 RU/s for rare peaks; average use is 300 RU/s.

**Answer:** Put the accounts in a fleet and fleetspace, lower dedicated throughput to typical load, and configure a pool sized for the expected concurrent peaks. Use fleet analytics to watch per-tenant usage and tune.

### Scenario 5: Support staff should not see card numbers

**Situation:** Support engineers query a customer container through an internal tool that uses account keys. Compliance wants partial masking of card numbers.

**Answer:** Move the tool to Entra ID with data plane RBAC (DDM does not work with keys), enable DDM, define a custom-string mask on the card number path, and give support a role without the unmask action. Remember that DDM cannot be disabled once enabled.

---

## How the exam tests this

- **Prerequisite chains.** Continuous backup is required by Fabric mirroring, all versions and deletes change feed, and online copy jobs (and integrated embeddings need all versions and deletes). Expect "what must you enable first".
- **RU accounting.** Mirroring and querying mirrored data: no RUs. The Spark connector against the container: RUs. Data explorer view of the mirror's source: RUs.
- **Mode vs model.** Deletes needed: all versions and deletes mode. Push processing at scale with checkpoints: change feed processor with a lease container. Manual pacing: pull model.
- **PPAF guardrails.** No Bounded Staleness, no serverless, no Synapse Link, no in-account restore; single write region; RTO under 3 minutes at P99.
- **DDM.** Entra ID only, unmask is a data action, Data Reader lacks it, one-way enablement.
- **Fleet vocabulary.** Fleet = application, fleetspace = group of accounts, pool = shared RU/s, dedicated RU/s consumed first, fleet analytics = hourly, cross-account, in OneLake or ADLS.
- **Azure vs Fabric Cosmos DB.** Needs stored procedures, Private Link, CMK or full account control: Azure Cosmos DB. Lives in Fabric with minimal administration: Cosmos DB in Fabric.

---

**Related notes:** [AI retrieval and agent memory](cosmos-db-ai-retrieval-agent-memory.md) · [Performance and operations](cosmos-db-performance-operations.md) · [Data modeling and partitioning](cosmos-db-data-modeling-partitioning.md)
