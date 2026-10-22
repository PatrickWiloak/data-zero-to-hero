---
last-updated: 2026-10-22
difficulty: beginner
---

# Azure Data Fundamentals (DP-900) - Practice Questions

30 questions for DP-900 prep across the four official domains: core data concepts (25-30%), relational data (20-25%), non-relational data (15-20%), and analytics workloads (25-30%). Questions 16 to 30 are tagged with their domain.

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

### Question 16 - Domain 1: Describe core data concepts
**Scenario:** A retailer receives product records from suppliers as JSON. Some records include a "color" field, others add nested "dimensions" objects, and new fields appear without notice. How should the team classify this data?

A. Structured, because every record can be loaded into a table with fixed columns
B. Unstructured, because the records have no organization that software can read
C. Semi-structured, because each record carries its own field names and shape
D. Relational, because the records could be joined to a supplier table by ID

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Semi-structured data such as JSON, XML, or YAML is self-describing: the field names travel with each record, and records can differ in shape. A is wrong because structured data needs one schema defined up front, which these varying records do not have. B is wrong because unstructured data (images, audio, free text) has no field-level organization, while JSON clearly does. D is wrong because "relational" describes a storage model, not the shape of the incoming data, and being joinable by ID does not make data relational.
</details>

---

### Question 17 - Domain 1: Describe core data concepts
**Scenario:** An analytics team stores billions of sales rows in a data lake. Most queries read only 4 of 60 columns and scan whole months at a time. Which file format should they store the data in?

A. Parquet, a compressed columnar format that lets queries read only the needed columns
B. CSV, a plain-text delimited format that every tool can open without a library
C. JSON, a text format that keeps field names in every record for readability
D. Avro, a row-based binary format that stores its schema alongside the records

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Parquet stores data by column with compression and column statistics, so a query that touches 4 of 60 columns reads a small fraction of the file. That is why it is the default for lakehouse tables (Delta and Iceberg tables store their data as Parquet). B is wrong because CSV is row-based and uncompressed, so every query reads every column. C is wrong because JSON repeats field names in every record and is slow to scan at this scale. D is wrong because Avro is row-based; it suits streaming and message serialization with schema evolution, not column-selective analytics.
</details>

---

### Question 18 - Domain 1: Describe core data concepts
**Scenario:** A company is hiring for a role that will manage database user permissions, schedule and test backups, apply updates, and tune a slow production database. Which role is this?

A. Data analyst, who explores data and builds reports for business decisions
B. Data engineer, who builds pipelines that move and transform data between stores
C. Data scientist, who trains statistical and machine learning models on data
D. Database administrator, who keeps database systems secure, available, and fast

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Permissions, backups and restores, patching, and performance tuning of an operational database are the core duties of a database administrator. A is wrong because analysts consume data to answer business questions; they do not run the database. B is wrong because data engineers build ingestion and transformation pipelines, not day-to-day database operations. C is wrong because data scientists build models and experiments and rely on others to operate the underlying systems.
</details>

---

### Question 19 - Domain 1: Describe core data concepts
**Scenario:** A sales director wants someone to clean a prepared dataset, define how tables relate, and publish interactive dashboards that answer "which regions are missing target". The data is already landed and refreshed by an existing pipeline. Which role fits best?

A. Data engineer, because any work that touches the dataset belongs to pipeline owners
B. Data analyst, because modeling and visualizing data for business questions is their job
C. Database administrator, because dashboards query the database and need tuning
D. Data scientist, because spotting regions below target requires a predictive model

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Data analysts clean and model data for reporting, build visualizations, and turn data into answers for business users, often in Power BI. A is wrong because the pipeline already exists; the remaining work is modeling and reporting, not ingestion. C is wrong because a DBA runs the database platform and does not build business dashboards. D is wrong because comparing actuals to targets is descriptive reporting, not prediction, so no machine learning model is needed.
</details>

---

### Question 20 - Domain 2: Identify considerations for relational data on Azure
**Scenario:** A developer must add a new "loyalty_tier" column to the existing Customers table, then set every current customer to "Bronze". Which pair of SQL statement types does this need, in order?

A. DML to add the column, then DDL to set the values
B. DDL (ALTER TABLE) to add the column, then DML (UPDATE) to set the values
C. DCL (GRANT) to add the column, then DML (INSERT) to set the values
D. DDL (CREATE TABLE) to add the column, then DCL (DENY) to set the values

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Data Definition Language changes structure (CREATE, ALTER, DROP), so ALTER TABLE adds the column. Data Manipulation Language changes rows (INSERT, UPDATE, DELETE, SELECT), so UPDATE sets the value on existing rows. A is wrong because it reverses the two categories. C is wrong because GRANT is Data Control Language for permissions, and INSERT would add new rows instead of changing existing ones. D is wrong because CREATE TABLE makes a new table rather than altering one, and DENY controls permissions, not values.
</details>

---

### Question 21 - Domain 2: Identify considerations for relational data on Azure
**Scenario:** A reporting team needs to query customer orders, but must never see the customers' payment card columns stored in the same table. The DBA wants a reusable object they can grant access to without copying the data. What should the DBA create?

A. An index on the non-sensitive columns, so reporting queries skip the card data
B. A second table holding a nightly copy of the order columns without the card data
C. A view that selects only the permitted columns, with access granted on the view
D. A stored procedure that deletes the card columns before reports run each day

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** A view is a saved query that behaves like a virtual table. Granting access to the view, and not the base table, exposes only the selected columns with no data copy. A is wrong because an index speeds up lookups but does not restrict which columns a user can read. B is wrong because it duplicates data and creates a sync job the scenario wanted to avoid. D is wrong because deleting the card data destroys information the business still needs; access control, not deletion, is the goal.
</details>

---

### Question 22 - Domain 2: Identify considerations for relational data on Azure
**Scenario:** A company is moving an on-premises SQL Server estate to Azure. The application uses SQL Server Agent jobs and cross-database queries. They want Microsoft to handle patching and backups, and they do not need operating system access. Which option fits best?

A. Azure SQL Managed Instance, which keeps instance-level features in a managed service
B. Azure SQL Database single database, which isolates each database as its own unit
C. SQL Server on Azure Virtual Machines, which gives full control of the guest OS
D. Azure Database for PostgreSQL, which runs an open-source engine as a managed service

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** SQL Managed Instance is the PaaS option with near-complete SQL Server compatibility, including SQL Agent and cross-database queries, while Azure manages patching and backups. That makes it the usual lift-and-shift target. B is wrong because a single Azure SQL Database does not support SQL Agent or cross-database queries in the same way. C is wrong because a VM works but leaves patching and OS management with the customer, which they wanted to avoid. D is wrong because PostgreSQL is a different engine and would force application rewrites.
</details>

---

### Question 23 - Domain 2: Identify considerations for relational data on Azure
**Scenario:** A startup runs a web app on MySQL using an open-source framework. They want to move to Azure with no engine change and no servers to patch. Which service should they choose?

A. Azure SQL Database, migrating the schema to SQL Server's T-SQL dialect first
B. Azure Cosmos DB for NoSQL, storing each table's rows as JSON documents
C. SQL Server on Azure Virtual Machines, installing MySQL alongside SQL Server
D. Azure Database for MySQL flexible server, a managed MySQL community engine

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Azure Database for MySQL (flexible server) runs the MySQL community engine as a managed service with patching, backups, and high availability options, so the app connects with its existing driver. Azure offers the same model for PostgreSQL. A is wrong because it requires converting to a different engine and dialect. B is wrong because moving to a document database means redesigning the data model. C is wrong because a VM is IaaS, so the team would still patch servers, which they wanted to avoid.
</details>

---

### Question 24 - Domain 3: Describe considerations for working with non-relational data on Azure
**Scenario:** Several Windows servers being moved to Azure read and write shared configuration files through a mapped network drive. The team wants a managed replacement for the on-premises file server with no application changes. What should they use?

A. Azure Blob Storage, reached through the REST API from each server
B. Azure Files, a managed file share mounted over SMB like a network drive
C. Azure Table storage, with one entity per configuration file
D. Azure Data Lake Storage Gen2, with a hierarchical namespace for folders

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Azure Files provides fully managed file shares over SMB (and NFS), so existing servers can mount it as a drive and keep using normal file paths. A is wrong because Blob Storage is object storage accessed by API, which would require code changes. C is wrong because Table storage is a key-value store for structured entities, not a file system. D is wrong because Data Lake Storage Gen2 is built on Blob Storage for analytics workloads and is not a drop-in SMB file share.
</details>

---

### Question 25 - Domain 3: Describe considerations for working with non-relational data on Azure
**Scenario:** An app stores millions of simple device settings records. Each lookup uses a device group and device ID, there are no joins, and the budget is tight. Which option is the lowest-cost fit?

A. Azure Table storage, a key-value store addressed by partition key and row key
B. Azure SQL Database, with a settings table and a composite primary key
C. Azure Files, with one small text file per device in a share
D. Azure Cosmos DB for Apache Gremlin, modeling each device as a graph vertex

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Table storage is a low-cost NoSQL key-value store. Each entity is found by PartitionKey (here, the device group) plus RowKey (the device ID), which matches the access pattern exactly. B is wrong because a relational database works but costs more and adds features the app does not need. C is wrong because millions of tiny files are slow to manage and query. D is wrong because a graph API is for relationship traversal, and there are no relationships to traverse here.
</details>

---

### Question 26 - Domain 3: Describe considerations for working with non-relational data on Azure
**Scenario:** A team has a working app that uses MongoDB drivers and queries. They want a managed, globally distributed database on Azure while changing as little code as possible. What should they choose?

A. Azure Cosmos DB for Apache Cassandra, using CQL to query wide-column tables
B. Azure Cosmos DB for Table, migrating documents into key-value entities
C. Azure Cosmos DB for MongoDB, which accepts the existing MongoDB wire protocol
D. Azure Cosmos DB for NoSQL, rewriting queries in its SQL-like query language

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Cosmos DB offers several APIs so existing apps can move without rewrites. The API for MongoDB is compatible with MongoDB drivers and tools, so the app keeps its code while gaining Cosmos DB's global distribution and managed scaling. A is wrong because Cassandra uses CQL and a column-family model, which means a rewrite. B is wrong because the Table API is a simple key-value model, not a document model. D is wrong because the NoSQL API is the native choice for new apps, but moving to it means rewriting MongoDB queries.
</details>

---

### Question 27 - Domain 4: Describe an analytics workload on Azure
**Scenario:** A company wants one SaaS analytics platform where data engineers use Spark notebooks, analysts use SQL, and Power BI reports read the same lake copy of the data. Nobody wants to manage separate storage accounts per tool. Which service best fits?

A. Azure Data Factory, which schedules and runs copy and transform pipelines
B. Azure Stream Analytics, which runs SQL queries over streaming events
C. Azure SQL Database, which hosts a relational data warehouse schema
D. Microsoft Fabric, which unifies these workloads over a single OneLake store

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Microsoft Fabric is a SaaS analytics platform. Data engineering (Spark), data warehousing (SQL), real-time analytics, and Power BI all work on data in OneLake, one logical lake for the whole tenant, stored in open Delta Parquet format. A is wrong because Data Factory orchestrates movement but is not a platform for notebooks, SQL analytics, and reporting together. B is wrong because Stream Analytics handles streaming queries only. C is wrong because an operational SQL database is not a unified lake-based analytics platform. Azure Databricks is the other big-platform answer to know, but it is a Spark-first service rather than an all-in-one SaaS with built-in Power BI.
</details>

---

### Question 28 - Domain 4: Describe an analytics workload on Azure
**Scenario:** A factory streams sensor readings every second. Engineers want a dashboard that shows temperature spikes within seconds and lets them query recent and historical readings together. Which Microsoft service area is designed for this?

A. A nightly Data Factory pipeline that loads readings into a warehouse for reports
B. Fabric Real-Time Intelligence, using Eventstream to ingest and an Eventhouse to query
C. Azure Blob Storage archive tier, holding readings until an analyst requests them
D. Power BI import mode, refreshed on its scheduled refresh slots each day

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Real-Time Intelligence in Microsoft Fabric ingests streams with Eventstream, stores and queries them in an Eventhouse (KQL database), and shows them on real-time dashboards with latency of seconds. Azure Event Hubs plus Azure Stream Analytics is the classic Azure equivalent. A is wrong because a nightly batch is hours late for spike alerts. C is wrong because the archive tier takes hours to rehydrate and is for rarely read data. D is wrong because scheduled import refreshes run a limited number of times per day, not every few seconds.
</details>

---

### Question 29 - Domain 4: Describe an analytics workload on Azure
**Scenario:** In a Power BI model, an analyst needs "Total Sales" to recalculate correctly when a user filters by region, year, or product. It should not add a stored value to every row of the table. What should the analyst create?

A. A calculated column that multiplies quantity by price on each row of the table
B. A new import table that pre-sums sales for every region and year pair
C. A measure, a DAX calculation evaluated at query time in the current filter context
D. A hierarchy that groups year, quarter, and month for drill-down

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Measures are evaluated when a visual is queried, so they respect whatever filters and slicers are applied, and they store nothing per row. A is wrong because a calculated column is computed at refresh and stored on every row; it is useful for slicing, not for filter-aware totals. B is wrong because a pre-aggregated table cannot follow arbitrary filter combinations such as product. D is wrong because a hierarchy organizes drill-down levels but does not compute any value.
</details>

---

### Question 30 - Domain 4: Describe an analytics workload on Azure
**Scenario:** A finance team wants a Power BI visual that shows how monthly revenue has changed over the last three years, so readers can spot seasonality and trend. Which visual is the best default?

A. A line chart with month on the x-axis and revenue on the y-axis
B. A pie chart with one slice for each month across the three years
C. A card visual showing total revenue for the full three-year period
D. A table listing every invoice with its date and amount

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Line charts show change over a continuous time axis, which makes trend and seasonal patterns easy to see. B is wrong because a pie chart shows parts of a whole, and 36 slices are unreadable and hide the time order. C is wrong because a card shows one number and no change over time. D is wrong because a detail table forces readers to work out the trend themselves.
</details>

---

## Where to go deeper

- [DP-900 cert page](../../exams/azure/dp-900/) - notes, practice plan, strategy
- [AZ-900 practice questions](https://cloud.noblerworks.com/resources/practice-questions/azure-fundamentals-az-900/) - the cloud fundamentals sibling
- [SQL vs NoSQL](../../learn/concepts/sql-vs-nosql.md) - the choice in plain English
- [Databases topic index](../../topics/databases.md) - everything the repo has on data stores
- **[📖 DP-900 study guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/dp-900)** - official skills outline
