---
last-updated: 2026-08-09
difficulty: beginner
---

# Azure Data Fundamentals (DP-900) - Practice Questions

15 questions for DP-900 prep, roughly evenly weighted across core data concepts, relational data, non-relational data, and analytics workloads (25-30% each).

DP-900 tests recognition and vocabulary rather than implementation, so these questions match that level.

> **Cert page:** [exams/azure/dp-900/](../../exams/azure/dp-900/)

---

### Question 1
**Scenario:** Which describes structured data?

A. Free-text emails and documents, whose content is kept in files without a predefined schema
B. Images
C. Data organized into rows and columns with a predefined schema
D. Video files

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Structured data has a schema defined before the data is written, which is what makes SQL tables queryable in the way they are. Semi-structured data such as JSON carries its own schema in the payload. Video, images, and free text are unstructured.
</details>

---

### Question 2
**Scenario:** Which workload type describes a system handling many small, fast inserts and updates from an application?

A. Batch analytics over the full history of sales
B. OLTP (online transaction processing)
C. OLAP
D. Data warehousing for historical reporting

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** OLTP is transaction processing: high volumes of short reads and writes, normalized to avoid duplication. OLAP is analytical processing: fewer, larger queries scanning history, usually denormalized into a star schema. The two have opposite design goals, which is why organizations run both.
</details>

---

### Question 3
**Scenario:** What does ACID stand for in a relational database?

A. Analytics, Caching, Indexing, Data
B. Access, Control, Identity, Directory
C. Available, Consistent, Isolated, Distributed - the four guarantees of a replicated cluster
D. Atomicity, Consistency, Isolation, Durability

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Atomicity means all or nothing, consistency means valid state to valid state, isolation means concurrent transactions do not interfere, and durability means a committed transaction survives a crash. These four are the guarantees applications rely on when moving money or booking inventory.
</details>

---

### Question 4
**Scenario:** Which Azure service is a fully managed relational PaaS database with automatic patching and backups?

A. Azure Blob Storage, which holds unstructured objects rather than relational tables
B. Azure Files, which offers managed SMB file shares rather than a relational engine
C. Azure Cosmos DB, a NoSQL database that is not a relational PaaS engine
D. Azure SQL Database

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Azure SQL Database is the relational PaaS offering. Cosmos DB is the NoSQL multi-model service. Blob Storage is object storage for unstructured data, and Azure Files provides SMB and NFS file shares.
</details>

---

### Question 5
**Scenario:** A key-value, document, graph, and column-family API from one service.

A. Azure SQL Managed Instance (relational)
B. Azure Data Factory
C. Azure Cosmos DB (multi-model)
D. Azure Synapse Analytics (data warehouse)

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Cosmos DB is multi-model, offering NoSQL (document), MongoDB, Cassandra (column-family), Gremlin (graph), and Table APIs on the same underlying engine with global distribution. The other services in the list are relational, analytical, and orchestration services respectively.
</details>

---

### Question 6
**Scenario:** Unstructured files such as images and backups need cheap, massively scalable storage.

A. Azure Blob Storage
B. Azure Table Storage (key-value)
C. Azure Cache for Redis (in-memory)
D. Azure SQL DB

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Blob Storage is object storage with hot, cool, cold, and archive access tiers so you can trade retrieval cost against storage cost. Table Storage is a key-value store for structured non-relational data. Redis is an in-memory cache.
</details>

---

### Question 7
**Scenario:** Which service orchestrates data movement and transformation across sources on a schedule?

A. Azure Monitor, which collects metrics and logs but does not move or transform data between sources
B. Azure DevOps, which manages code repositories and release pipelines for software, not data movement
C. Azure Data Factory
D. Azure Functions, which runs event-driven code but is not a managed data integration service

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Data Factory (and its Fabric equivalent, Data pipelines) is the ETL and ELT orchestration service with connectors, scheduling, and monitoring built in. Functions can move data but gives you none of the orchestration, retry, and lineage plumbing.
</details>

---

### Question 8
**Scenario:** The difference between ETL and ELT.

A. ETL transforms before loading into the target; ELT loads raw data first and transforms inside the target system
B. They are identical
C. ETL cannot handle relational data
D. ELT is only for streaming

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** ELT became the common pattern because cloud data warehouses and lakehouses have cheap, elastic compute, so it is often better to land the raw data and transform in place. Keeping the raw copy also means a transformation bug is fixable without re-extracting from source.
</details>

---

### Question 9
**Scenario:** A data warehouse's fact table typically contains what?

A. Report definitions
B. User permissions
C. Descriptive attributes such as product name and category, which belong in the dimension tables instead
D. Numeric measures and foreign keys to dimensions, at a defined grain

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Facts hold the measurements (sales amount, quantity) plus keys, while dimensions hold the descriptive attributes you slice by. The grain, meaning what one row represents, is the first thing to define because everything else follows from it.
</details>

---

### Question 10
**Scenario:** Which best describes a data lake?

A. A caching layer
B. Storage for large volumes of raw data in native formats, with schema applied on read
C. A reporting tool
D. A relational database with a fixed schema

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Schema-on-read is the defining property: you store first and interpret later, which suits data whose future use is not yet known. The risk is that without governance a lake becomes a swamp, which is why cataloging and the medallion layering pattern exist.
</details>

---

### Question 11
**Scenario:** Which service provides interactive reports and dashboards for business users?

A. Power BI
B. Azure Databricks
C. Azure Data Lake Storage
D. Azure Data Box

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Power BI is the visualization and self-service BI layer. Databricks is a Spark-based analytics and machine learning platform. Data Lake Storage is where the data sits, and Data Box is a physical appliance for bulk offline transfer.
</details>

---

### Question 12
**Scenario:** Which describes streaming data processing?

A. Processing large sets at scheduled intervals, such as a nightly job over the previous day's files
B. Processing events continuously as they arrive, often with windowed aggregation
C. Only for relational databases
D. Manual data entry

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Stream processing acts on events in near real time, typically over time windows such as tumbling or sliding windows. Batch processes bounded sets on a schedule. Azure Stream Analytics, Event Hubs, and Fabric Real-Time Intelligence are the services in this space.
</details>

---

### Question 13
**Scenario:** A normalized relational design primarily aims to do what?

A. Reduce data redundancy and update anomalies by storing each fact once
B. Speed up analytical scans
C. Store images efficiently
D. Eliminate the need for indexes

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Normalization removes duplication so an update happens in one place. The trade-off is more joins, which is why analytical models deliberately denormalize into star schemas where read performance matters more than update anomalies.
</details>

---

### Question 14
**Scenario:** Who is typically responsible for building and maintaining data pipelines?

A. Data scientist (machine learning)
B. Database administrator
C. Data engineer
D. Analyst

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Data engineers build ingestion and transformation pipelines and the storage that serves them. Analysts model and visualize for business questions, DBAs run and secure database systems, and data scientists build statistical and machine learning models. DP-900 expects you to distinguish these roles.
</details>

---

### Question 15
**Scenario:** Which statement about a relational primary key is correct?

A. It may contain duplicate values as long as another column in the same row is unique
B. It uniquely identifies each row and cannot be null
C. It must be an integer, because text and GUID columns cannot serve as primary keys
D. Each table can have many primary keys, one for every column that must stay unique

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Uniqueness and not-null are the defining constraints. A key can be any type and can span multiple columns as a composite key. A table has at most one primary key, though it can have additional unique constraints, and foreign keys are the references from other tables to it.
</details>

---

## Where to go deeper

- [DP-900 cert page](../../exams/azure/dp-900/) - notes, practice plan, strategy
- [AZ-900 practice questions](https://cloud.noblerworks.com/resources/practice-questions/azure-fundamentals-az-900/) - the cloud fundamentals sibling
- [SQL vs NoSQL](../../learn/concepts/sql-vs-nosql.md) - the choice in plain English
- [Databases topic index](../../topics/databases.md) - everything the repo has on data stores
- **[📖 DP-900 study guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/dp-900)** - official skills outline
