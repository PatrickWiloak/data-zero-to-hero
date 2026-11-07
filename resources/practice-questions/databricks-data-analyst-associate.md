---
last-updated: 2026-11-07
---

# Databricks Certified Data Analyst Associate - Practice Questions

25 scenario-based questions for Databricks Data Analyst Associate prep, tagged with the nine sections of the August 2026 exam guide. Weights: Executing queries 20%, Dashboards and visualizations 16%, Analyzing queries 15%, Genie 12%, Platform 11%, Managing data 8%, Securing data 8%, Importing data 5%, Data modeling 5%.

> **Cert page:** [exams/databricks/data-analyst-associate/](../../exams/databricks/data-analyst-associate/)

> Original scenario questions for study. They contain no vendor exam content.

---

### Question 1 - Section 1: Understanding of the Databricks Data Intelligence Platform
**Scenario:** An engineer drops a Unity Catalog table and, a week later, the files in cloud storage are still there and untouched. What best explains this?

A. The table was a view, and views always keep their source files
B. The table was external, so DROP removed only metadata
C. The table was managed, so DROP always keeps the files for ever
D. The table was streaming, and streaming tables cannot be dropped

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Dropping an external table removes the Unity Catalog entry only; you manage the files. Dropping a managed table deletes its files after the recovery period (7 days by default). Views store no files, and streaming tables can be dropped.

**Key Concept:** exams/databricks/data-analyst-associate/notes/01-databricks-platform.md
</details>

---

### Question 2 - Section 1: Understanding of the Databricks Data Intelligence Platform
**Scenario:** A data team wants to see which downstream dashboards and tables would be affected if they rename a column. Where should they look first?

A. The query history of the SQL warehouse
B. The Marketplace listing for the table
C. The Photon settings of the warehouse
D. The Lineage tab of the table in Catalog Explorer

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Unity Catalog captures lineage down to column level and shows consumers such as dashboards and jobs on the table's Lineage tab, which supports impact analysis. Query history lists runs, Marketplace is for shared listings and Photon is an engine setting.

**Key Concept:** exams/databricks/data-analyst-associate/notes/01-databricks-platform.md
</details>

---

### Question 3 - Section 1: Understanding of the Databricks Data Intelligence Platform
**Scenario:** An analyst needs to try a third-party weather dataset without building any ingestion pipeline. Which platform feature fits?

A. Liquid clustering
B. Lakeflow Jobs
C. Databricks Marketplace
D. Photon

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Marketplace is the exchange where providers publish datasets, notebooks and models that consumers connect to from a workspace. Jobs schedule work, Photon accelerates queries and Liquid clustering lays out table data.

**Key Concept:** exams/databricks/data-analyst-associate/notes/01-databricks-platform.md
</details>

---

### Question 4 - Section 2: Managing Data
**Scenario:** A column named region contains NULL for some rows. A report must show the text unknown instead of blank cells. Which expression does this?

A. region = 'unknown'
B. COALESCE(region, 'unknown')
C. WHERE region IS NOT NULL
D. COUNT(region)

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** COALESCE returns the first non-null value, so nulls become the default text. An equality test returns a boolean, IS NOT NULL removes rows instead of replacing values, and COUNT ignores nulls rather than replacing them.

**Key Concept:** exams/databricks/data-analyst-associate/notes/02-managing-data.md
</details>

---

### Question 5 - Section 2: Managing Data
**Scenario:** Which Catalog Explorer option helps control exactly which values may be used to classify a table as containing sensitive data?

A. Governed tags with allowed values
B. A Genie sample question
C. A comment written on the table
D. Regular tags typed freely by users

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Governed tags have enforced allowed values and controlled assignment, so classification stays consistent. Regular tags and comments are free-form, and a sample question does not classify data.

**Key Concept:** exams/databricks/data-analyst-associate/notes/02-managing-data.md
</details>

---

### Question 6 - Section 3: Importing Data
**Scenario:** A partner organization wants to give your team read-only access to its tables without sending file copies. Which mechanism is built for this?

A. Delta Sharing, with your team as recipient
B. A Genie space shared by link
C. Lakeflow Jobs copying the tables every night
D. Uploading CSV exports through the UI every week

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Delta Sharing (OpenSharing) lets a provider share read-only tables with recipients without copying files. Weekly uploads and nightly copies duplicate data, and a Genie agent is a question interface rather than a data-sharing protocol.

**Key Concept:** exams/databricks/data-analyst-associate/notes/03-importing-data.md
</details>

---

### Question 7 - Section 4: Executing Queries
**Scenario:** A pipeline needs a table that incrementally processes continuously arriving sensor events, appending each row once. Which object suits it?

A. Streaming table
B. Standard view
C. External table over CSV
D. Materialized view refreshed nightly

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Streaming tables process append-only data incrementally, row by row. A view recomputes on read, a nightly materialized view is not continuous and an external CSV table has no incremental processing.

**Key Concept:** exams/databricks/data-analyst-associate/notes/04-executing-queries.md
</details>

---

### Question 8 - Section 4: Executing Queries
**Scenario:** BI dashboards repeatedly run the same expensive aggregation over mostly static data. What should the analyst create to speed them up?

A. Temporary view
B. Foreign catalog
C. Streaming table
D. Materialized view

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** A materialized view stores precomputed results and refreshes them, so repeated heavy queries read stored data. A temporary view recomputes each time, a streaming table is for incremental ingestion and a foreign catalog exposes external data.

**Key Concept:** exams/databricks/data-analyst-associate/notes/04-executing-queries.md
</details>

---

### Question 9 - Section 4: Executing Queries
**Scenario:** A query lists each customer and the sum of their order amounts. Customers with no orders must still appear with a NULL total. Which join is right?

A. UNION of the two tables
B. LEFT JOIN from customers to orders
C. INNER JOIN between customers and orders
D. LEFT ANTI JOIN from customers to orders

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** A left join keeps every customer and fills missing order columns with NULL. An inner join drops customers without orders, a left anti join returns only customers without orders, and a union stacks rows rather than joining columns.

**Key Concept:** exams/databricks/data-analyst-associate/notes/04-executing-queries.md
</details>

---

### Question 10 - Section 4: Executing Queries
**Scenario:** A team wants to estimate the number of distinct visitors in a table of billions of rows, accepting a small error to save compute. Which function fits?

A. SUM(DISTINCT visitor_id)
B. COUNT(*)
C. approx_count_distinct
D. MEDIAN(visitor_id)

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** approx_count_distinct uses HyperLogLog++ for a cheap estimate, within about 5 percent by default. COUNT(*) counts rows, summing distinct IDs is meaningless for counting, and a median is a different statistic.

**Key Concept:** exams/databricks/data-analyst-associate/notes/04-executing-queries.md
</details>

---

### Question 11 - Section 4: Executing Queries
**Scenario:** Replacing a table must keep its history and existing privileges while creating it empty with three integer columns. Which statement does that?

A. CREATE TABLE IF NOT EXISTS with the three columns
B. CREATE OR REPLACE TABLE with the three columns
C. DROP TABLE followed by CREATE TABLE
D. CREATE EXTERNAL TABLE with a new location

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** CREATE OR REPLACE TABLE swaps the definition while retaining history and privileges. Dropping and recreating loses them, IF NOT EXISTS does nothing when the table exists, and an external table changes the storage model.

**Key Concept:** exams/databricks/data-analyst-associate/notes/04-executing-queries.md
</details>

---

### Question 12 - Section 5: Analyzing Queries
**Scenario:** A time travel query against a table as it was 30 days ago fails, although table history is kept for 30 days by default. What is the most likely cause?

A. The warehouse was resized during the month
B. The table schema changed at some point since then
C. Photon is disabled
D. VACUUM removed the files for that version (7-day default retention)

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** History metadata can outlast the data files: VACUUM deletes files older than the deleted-file retention (7 days by default), so older versions cannot be read. Schema changes, warehouse size and Photon do not block time travel.

**Key Concept:** exams/databricks/data-analyst-associate/notes/05-analyzing-queries.md
</details>

---

### Question 13 - Section 5: Analyzing Queries
**Scenario:** An analyst reruns the exact same query a minute later and it returns almost instantly, with no data changes. What most likely happened?

A. The table was converted to a streaming table
B. Predictive optimization rewrote the query
C. The warehouse was switched to classic
D. The result cache served the stored result

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Result caching stores query results for SQL warehouse queries and serves identical queries until the underlying data changes. The other options are not things that happen between identical runs.

**Key Concept:** exams/databricks/data-analyst-associate/notes/05-analyzing-queries.md
</details>

---

### Question 14 - Section 5: Analyzing Queries
**Scenario:** Which tables does Predictive Optimization maintain automatically?

A. Unity Catalog managed tables
B. Any CSV file sitting in a volume
C. Only temporary views in a session
D. Only external tables stored on S3

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Predictive optimization runs OPTIMIZE, VACUUM and ANALYZE on Unity Catalog managed tables; external tables are not supported. Views and raw files are not tables it maintains.

**Key Concept:** exams/databricks/data-analyst-associate/notes/05-analyzing-queries.md
</details>

---

### Question 15 - Section 5: Analyzing Queries
**Scenario:** Queries on a large table filter mostly on customer_id and order_date, and the pattern may change over time. What layout choice fits best?

A. Partitioning by customer_id values
B. Liquid clustering, possibly CLUSTER BY AUTO
C. Storing each customer in a separate table
D. ZORDER on every column

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Liquid clustering lets keys be changed without rewriting data, and AUTO picks keys from query history. Partitioning on a high-cardinality column creates too many small partitions, and one table per customer is unmanageable.

**Key Concept:** exams/databricks/data-analyst-associate/notes/05-analyzing-queries.md
</details>

---

### Question 16 - Section 6: Dashboards and Visualizations
**Scenario:** A dashboard has a Date Range parameter linked to a filter widget. What does the selected range do?

A. Its min and max values are substituted into the dataset query, for example in a WHERE clause
B. It changes the dashboard theme
C. It only changes the label on the x-axis
D. It schedules the refresh

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Parameters substitute values into queries at run time, with date ranges exposed as .min and .max. They are not just labels, themes or schedules.

**Key Concept:** exams/databricks/data-analyst-associate/notes/06-dashboards-and-visualizations.md
</details>

---

### Question 17 - Section 6: Dashboards and Visualizations
**Scenario:** An operations lead must be told immediately when the 15-minute average temperature exceeds a limit. What should the analyst set up?

A. Larger SQL warehouse with more clusters for the query
B. Notebook whose output logs are reviewed by hand each morning
C. SQL alert with a threshold and a notification destination
D. Dashboard that the lead opens and refreshes manually every few minutes

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** A SQL alert runs a query on a schedule, checks the condition and notifies chosen users or destinations. Manual dashboards and log reading are not notifications, and a bigger warehouse does not alert anyone.

**Key Concept:** exams/databricks/data-analyst-associate/notes/06-dashboards-and-visualizations.md
</details>

---

### Question 18 - Section 6: Dashboards and Visualizations
**Scenario:** A finished dashboard must be viewable by colleagues who have no workspace access but do have accounts in the same Databricks account. What is supported?

A. Copying the dashboard queries into each colleague's notebook
B. Making the underlying table readable by anyone on the internet
C. Emailing a static screenshot of the dashboard each day
D. Publish and share at the account level

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Published AI/BI dashboards can be shared with anyone in the Databricks account, even people without workspace access. Screenshots, copies and public tables are not the supported sharing path.

**Key Concept:** exams/databricks/data-analyst-associate/notes/06-dashboards-and-visualizations.md
</details>

---

### Question 19 - Section 6: Dashboards and Visualizations
**Scenario:** Management wants to see how monthly revenue changed over the last two years. Which visualization communicates this best?

A. Line chart
B. Pie chart
C. Scatter plot of order IDs
D. Word cloud

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** A line chart shows change over time clearly. Pie charts show share of a total, and scatter plots and word clouds do not show a time trend.

**Key Concept:** exams/databricks/data-analyst-associate/notes/06-dashboards-and-visualizations.md
</details>

---

### Question 20 - Section 7: Genie Agents
**Scenario:** Business users get wrong answers from a Genie Agent because it misreads the term active customer. What is the best fix?

A. Add an instruction defining active customer plus an example query
B. Wait, because feedback from users retrains the agent automatically over time
C. Attach every table in the metastore so the agent has more context to use
D. Give every business user CAN MANAGE so they can correct the answers

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Instructions and example queries teach the agent business definitions. The docs say feedback alone does not change behaviour, more permissions do not help accuracy, and attaching everything adds noise.

**Key Concept:** exams/databricks/data-analyst-associate/notes/07-genie-agents.md
</details>

---

### Question 21 - Section 7: Genie Agents
**Scenario:** A user has CAN RUN on a Genie Agent but gets no data back for any question. What is a likely missing piece?

A. Photon enabled on the agent
B. CAN MANAGE on the warehouse
C. SELECT on the underlying tables
D. Ownership of the catalog

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Consumers need agent access plus SELECT on the data, because data access is checked against their own permissions. The other items are not required to ask questions.

**Key Concept:** exams/databricks/data-analyst-associate/notes/07-genie-agents.md
</details>

---

### Question 22 - Section 7: Genie Agents
**Scenario:** How can an owner measure whether changes to a Genie Agent improved accuracy?

A. Count how many users opened the agent during the week
B. Increase the warehouse size behind the agent
C. Run benchmark questions with known-good SQL answers
D. Disable feedback

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Benchmarks compare the agent's results with supplied answers before and after changes. Usage counts, warehouse size and disabling feedback do not measure correctness.

**Key Concept:** exams/databricks/data-analyst-associate/notes/07-genie-agents.md
</details>

---

### Question 23 - Section 8: Data Modeling
**Scenario:** Analysts need simple, fast BI queries over sales, with one wide table per business entity joined directly to the sales events. Which model is this?

A. Bronze layer
B. Data vault
C. Star schema
D. Fully normalized third normal form

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** A star schema joins denormalized dimension tables directly to a fact table. Data vault uses hubs, links and satellites, third normal form splits entities more finely, and bronze is a raw layer, not a model.

**Key Concept:** exams/databricks/data-analyst-associate/notes/08-data-modeling.md
</details>

---

### Question 24 - Section 9: Securing Data
**Scenario:** A user has SELECT on a table but cannot query it. Which missing privileges are the most likely cause?

A. Ownership of the metastore itself
B. Photon on the warehouse
C. MODIFY on the table itself
D. USE CATALOG and USE SCHEMA

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Reading a table requires USE CATALOG, USE SCHEMA and SELECT. MODIFY is for writes, and metastore ownership and Photon are unrelated.

**Key Concept:** exams/databricks/data-analyst-associate/notes/09-securing-data.md
</details>

---

### Question 25 - Section 9: Securing Data
**Scenario:** A table holds email addresses. Support staff should see a masked value, while the compliance group sees the real value. Which feature does this?

A. Materialized view refresh
B. Column mask
C. Cluster by auto
D. Table rename

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** A Unity Catalog column mask returns a transformed value depending on who is querying. Renaming, refreshing and clustering do not change what a user sees in a column.

**Key Concept:** exams/databricks/data-analyst-associate/notes/09-securing-data.md
</details>

---
