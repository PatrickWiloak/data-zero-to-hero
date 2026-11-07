---
last-updated: 2026-11-07
---

# Google Cloud Associate Data Practitioner - Practice Questions

28 scenario-based questions for Associate Data Practitioner prep. Questions follow the official guide's four sections and weights: Data Preparation and Ingestion (~30%), Data Analysis and Presentation (~27%), Data Pipeline Orchestration (~18%) and Data Management (~25%).

> **Cert page:** [exams/gcp/associate-data-practitioner/](../../exams/gcp/associate-data-practitioner/)

> Original scenario questions for study. They contain no vendor exam content.

---

### Question 1 - Section 1: Data Preparation and Ingestion
**Scenario:** A retailer must copy 80 TiB from an Amazon S3 bucket into Cloud Storage and then keep the two in sync nightly. The network link is fast and stable. Which tool fits best?

A. Storage Transfer Service with a scheduled transfer job from S3 to a Cloud Storage bucket
B. Transfer Appliance shipped to the retailer's data center and loaded by Google
C. A script on a laptop that runs gcloud storage cp against both buckets each night
D. BigQuery Data Transfer Service configured to copy the objects into a dataset

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Storage Transfer Service is built for large online transfers (its docs say it is optimized for more than 1 TiB), reads from S3 and can run on a schedule. Transfer Appliance is for data too slow to upload over the network; the link here is fast. Running a laptop script does not scale to this volume, and BigQuery Data Transfer Service loads into BigQuery, not into buckets.

**Key Concept:** exams/gcp/associate-data-practitioner/notes/01-data-preparation-and-ingestion.md
</details>

---

### Question 2 - Section 1: Data Preparation and Ingestion
**Scenario:** A research lab has 400 TB on local disks and a 100 Mbps office connection. Uploading would take many months. What should they use?

A. Transfer Appliance, shipping the data to Google for upload into a Cloud Storage bucket
B. Storage Transfer Service with agents installed on the lab's file servers
C. Database Migration Service configured with the lab's file share as the source
D. A bq load job started from each researcher's workstation

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Transfer Appliance is the offline option for cases where upload over the network would take more than about a week. Storage Transfer Service would still depend on the thin link. bq load loads into BigQuery and is limited by the same link, and Database Migration Service moves databases, not file shares.

**Key Concept:** exams/gcp/associate-data-practitioner/notes/01-data-preparation-and-ingestion.md
</details>

---

### Question 3 - Section 1: Data Preparation and Ingestion
**Scenario:** Order data already sits in BigQuery. An analyst needs to deduplicate, standardize country codes and join to a customer table every morning, all expressible in SQL. Which approach is the best fit?

A. ETL: export the tables, transform them on a separate cluster, then reload the results
B. ELT: keep the raw data in BigQuery and transform it there with SQL, for example with Dataform
C. ETL with a custom Dataflow pipeline that reads and rewrites every table each day
D. Move the data to Cloud SQL first because it handles joins better than BigQuery

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** When the data is already in the warehouse and the logic is SQL, ELT keeps everything in one place with the least machinery. Exporting and reloading adds cost and delay, a Dataflow pipeline is more than the job needs, and Cloud SQL is a transactional database, not a better engine for warehouse-scale joins.

**Key Concept:** exams/gcp/associate-data-practitioner/notes/01-data-preparation-and-ingestion.md
</details>

---

### Question 4 - Section 1: Data Preparation and Ingestion
**Scenario:** A company must replicate objects across two chosen regions for disaster recovery and wants automatic failover, with a predictable replication target of 15 minutes for the most critical bucket. Which setup matches?

A. Two single-region buckets synchronized by a nightly scheduled script
B. A dual-region bucket with turbo replication enabled
C. A multi-region bucket, which always replicates within 15 minutes
D. A single-region bucket in the primary region with object versioning on

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Dual-region buckets replicate asynchronously between two specific regions, and turbo replication is the premium option that replicates within 15 minutes. A single region only spans zones, multi-region has no stated 15-minute target, and a nightly script gives a recovery point measured in a day.

**Key Concept:** exams/gcp/associate-data-practitioner/notes/01-data-preparation-and-ingestion.md
</details>

---

### Question 5 - Section 1: Data Preparation and Ingestion
**Scenario:** A team receives daily files that must keep column types exactly (dates, decimals, nested records) when loaded into BigQuery. Which format is the best choice for the producer to send?

A. Parquet or Avro, because both carry the schema with the data
B. CSV, because every tool can read plain text without a schema
C. Plain text logs, because BigQuery parses types from the text itself
D. Compressed ZIP archives of CSV files, because compression preserves types

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Parquet and Avro are binary formats that embed the schema, so types and nesting survive the load. CSV has no types and relies on guessing or a supplied schema, free text has no structure, and zip compression is unrelated to type preservation (and is not a BigQuery load format).

**Key Concept:** exams/gcp/associate-data-practitioner/notes/01-data-preparation-and-ingestion.md
</details>

---

### Question 6 - Section 1: Data Preparation and Ingestion
**Scenario:** An IoT platform stores billions of sensor readings and needs single-digit-millisecond lookups of recent readings by device key. Which storage product is the best match?

A. Cloud Storage
B. BigQuery
C. Cloud SQL
D. Bigtable

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Bigtable is the wide-column NoSQL store for massive volumes with low-latency key lookups, typical for time series and IoT. BigQuery is an analytical warehouse, Cloud SQL is for moderate-scale relational workloads, and Cloud Storage is object storage without row-level lookups.

**Key Concept:** exams/gcp/associate-data-practitioner/notes/01-data-preparation-and-ingestion.md
</details>

---

### Question 7 - Section 1: Data Preparation and Ingestion
**Scenario:** Before loading a customer file, an engineer wants to know how many rows have missing email addresses and how many customer IDs repeat. What is the right first step?

A. Delete every row with any empty field before looking at the data
B. Run profiling queries that count nulls and duplicate keys, then decide what cleaning is needed
C. Load the file and rely on downstream dashboards to reveal the problems
D. Convert the file to JSON, which removes duplicates automatically

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Assessing data quality means measuring it first: null counts, duplicate keys, ranges and formats. Deleting rows blindly can destroy valid data, waiting for dashboards pushes errors to users, and changing format does not deduplicate anything.

**Key Concept:** exams/gcp/associate-data-practitioner/notes/01-data-preparation-and-ingestion.md
</details>

---

### Question 8 - Section 1: Data Preparation and Ingestion
**Scenario:** A business wants a recurring, scheduled load of Google Ads and Salesforce data into BigQuery with no custom code. Which service fits?

A. Storage Transfer Service
B. Database Migration Service
C. Transfer Appliance
D. BigQuery Data Transfer Service

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** BigQuery Data Transfer Service automates scheduled loads into BigQuery from supported SaaS sources, including Google Ads and Salesforce. Storage Transfer Service targets Cloud Storage, Transfer Appliance is a physical device, and Database Migration Service migrates relational databases.

**Key Concept:** exams/gcp/associate-data-practitioner/notes/01-data-preparation-and-ingestion.md
</details>

---

### Question 9 - Section 2: Data Analysis and Presentation
**Scenario:** A marketing manager asks which customers are likely to buy again in the next 30 days. Purchase history is in BigQuery. Which approach is the most direct?

A. Run K-means clustering and treat the largest cluster as the buyers
B. Export the table to a spreadsheet and sort by most recent purchase
C. Build a time series forecast with ARIMA_PLUS on the total order count
D. Train a classification model such as logistic regression with BigQuery ML using CREATE MODEL

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** A yes/no outcome per customer is a classification problem, and BigQuery ML trains logistic regression with SQL where the data already is. Sorting by recency is not a prediction model, ARIMA_PLUS forecasts a series of values over time rather than per-customer outcomes, and clusters are unlabeled groups, not predictions.

**Key Concept:** exams/gcp/associate-data-practitioner/notes/02-data-analysis-and-presentation.md
</details>

---

### Question 10 - Section 2: Data Analysis and Presentation
**Scenario:** After training a BigQuery ML model, the team wants to measure how well it performs on data the model has not seen. Which step do they run?

A. ML.EVALUATE on a held-out table
B. Export the model and score it in a notebook
C. ML.PREDICT on the training table and compare to the training labels
D. CREATE OR REPLACE MODEL again with more columns

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** ML.EVALUATE returns metrics such as precision, recall and ROC AUC; run on held-out data it shows generalization. Predicting on training data flatters the model, retraining changes the model rather than measuring it, and exporting is unnecessary because evaluation is built in.

**Key Concept:** exams/gcp/associate-data-practitioner/notes/02-data-analysis-and-presentation.md
</details>

---

### Question 11 - Section 2: Data Analysis and Presentation
**Scenario:** A large company wants one governed definition of 'net revenue' reused by every dashboard, maintained in version control. Which product best supports this?

A. A spreadsheet that every analyst copies before use
B. A notebook that recomputes the metric whenever someone asks
C. Looker Studio, with each report author writing their own calculated field
D. Looker, with the metric defined once in LookML

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Looker is built around a semantic model in LookML where metrics are defined once, kept in Git and reused. Separate calculated fields, spreadsheet copies and ad hoc notebooks all let definitions drift between teams.

**Key Concept:** exams/gcp/associate-data-practitioner/notes/02-data-analysis-and-presentation.md
</details>

---

### Question 12 - Section 2: Data Analysis and Presentation
**Scenario:** A small team wants to build a quick, shareable report over a BigQuery table in an afternoon with no modeling layer. Which tool fits?

A. Looker Studio
B. Cloud Composer with a DAG that emails charts
C. LookML project with Git branches and developer mode
D. A Dataproc cluster running a custom web application

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Looker Studio is the self-service tool for fast ad hoc reports. A LookML project is heavyweight for this goal, Dataproc is for Spark workloads and Composer is orchestration, not reporting.

**Key Concept:** exams/gcp/associate-data-practitioner/notes/02-data-analysis-and-presentation.md
</details>

---

### Question 13 - Section 2: Data Analysis and Presentation
**Scenario:** An analyst wants to classify thousands of free-text support tickets stored in BigQuery using a Gemini model without moving the data out. What is the supported approach?

A. Download the tickets as CSV and paste them into a chat window in batches
B. Export the table to Cloud Storage and process it with Transfer Appliance
C. Create a remote model over the hosted model through a connection and call an AI function over the column
D. Train a linear regression model on the ticket text

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** BigQuery can call hosted models through a remote model and a connection, then apply AI functions to columns in SQL. Manual copy and paste does not scale, linear regression is not a language model, and Transfer Appliance is a data shipping device.

**Key Concept:** exams/gcp/associate-data-practitioner/notes/02-data-analysis-and-presentation.md
</details>

---

### Question 14 - Section 2: Data Analysis and Presentation
**Scenario:** To lower query cost on a large events table, an analyst should change which habit?

A. Select only the needed columns and filter on the partitioning column
B. Copy the table into a new dataset before every query
C. Always use SELECT * so BigQuery can cache the widest result
D. Run the query repeatedly until the result appears faster

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** BigQuery reads data by column, so selecting fewer columns and pruning partitions reduces bytes processed. SELECT * reads everything, repeating a query does not lower the cost of the first run, and copying the table adds storage and cost.

**Key Concept:** exams/gcp/associate-data-practitioner/notes/02-data-analysis-and-presentation.md
</details>

---

### Question 15 - Section 2: Data Analysis and Presentation
**Scenario:** A data team wants to organize, version and deploy BigQuery ML models to an endpoint for online prediction. Which capability is intended for this?

A. Analytics Hub
B. Dataform assertions
C. Model Registry
D. Cloud Scheduler

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** The Model Registry is where models are organized and versioned, and registered BigQuery ML models can be deployed to endpoints. Cloud Scheduler triggers jobs, Analytics Hub shares datasets and Dataform assertions test table data.

**Key Concept:** exams/gcp/associate-data-practitioner/notes/02-data-analysis-and-presentation.md
</details>

---

### Question 16 - Section 3: Data Pipeline Orchestration
**Scenario:** Every night at 02:15 a single SQL statement must rebuild a summary table in BigQuery. Which is the simplest correct choice?

A. A BigQuery scheduled query
B. A Cloud Composer environment with a one-task DAG
C. A Dataproc cluster with a Spark job triggered by cron
D. A Dataflow streaming job that runs all day

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** A scheduled query runs a recurring GoogleSQL statement with no extra infrastructure. Composer, Dataproc and an always-on streaming job all add moving parts the task does not need.

**Key Concept:** exams/gcp/associate-data-practitioner/notes/03-data-pipeline-orchestration.md
</details>

---

### Question 17 - Section 3: Data Pipeline Orchestration
**Scenario:** A pipeline has twelve steps across BigQuery, Dataflow templates and an external API, with dependencies, retries and backfills required. Which service should orchestrate it?

A. Cloud Logging
B. A single BigQuery scheduled query
C. Cloud Composer
D. Transfer Appliance

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Composer (managed Airflow) models dependent tasks as a DAG with retries and backfills across systems. A scheduled query runs one statement, Logging records events and Transfer Appliance moves data physically.

**Key Concept:** exams/gcp/associate-data-practitioner/notes/03-data-pipeline-orchestration.md
</details>

---

### Question 18 - Section 3: Data Pipeline Orchestration
**Scenario:** Messages arrive on a Pub/Sub topic and simply need to be stored in a BigQuery table as they come, with no transformation. What is the least-effort option?

A. A Composer DAG that polls the topic every minute
B. A Cloud SQL instance that the publishers write to
C. A BigQuery subscription on the topic
D. A Dataflow job with windowing and custom code

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** A BigQuery subscription writes messages directly into a table with no subscriber code, which the docs recommend when no significant processing is needed. Dataflow is for complex transforms or windowing, polling from Composer is inefficient and Cloud SQL is the wrong target.

**Key Concept:** exams/gcp/associate-data-practitioner/notes/03-data-pipeline-orchestration.md
</details>

---

### Question 19 - Section 3: Data Pipeline Orchestration
**Scenario:** When a new file lands in a Cloud Storage bucket, a Cloud Run function must start. Which service connects the event to the function?

A. Analytics Hub
B. Cloud Scheduler
C. Dataform
D. Eventarc

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Eventarc routes events such as object creation to targets including Cloud Run functions. Cloud Scheduler is time based, Dataform builds SQL workflows and Analytics Hub shares data.

**Key Concept:** exams/gcp/associate-data-practitioner/notes/03-data-pipeline-orchestration.md
</details>

---

### Question 20 - Section 3: Data Pipeline Orchestration
**Scenario:** A streaming Dataflow job is falling behind. Where should the engineer look first to see stage progress and spot a bottleneck?

A. The IAM roles page of the project
B. The Dataflow job UI
C. The Looker Studio report gallery
D. The Cloud Storage lifecycle configuration

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** The Dataflow job UI shows the pipeline graph, stage progress and execution details, with job metrics charts. IAM, lifecycle rules and report galleries do not describe job progress.

**Key Concept:** exams/gcp/associate-data-practitioner/notes/03-data-pipeline-orchestration.md
</details>

---

### Question 21 - Section 4: Data Management
**Scenario:** An analyst must run queries on one dataset and must not be able to modify tables. Which grant follows least privilege?

A. BigQuery Data Owner on the dataset
B. BigQuery Admin on the project
C. BigQuery Data Viewer on the dataset plus BigQuery Job User on the project
D. The basic Editor role on the project

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Data Viewer allows reading table data, and Job User lets the analyst run query jobs; together they cover the need without write access. Basic Editor, Admin and Data Owner all allow changes and are broader than required.

**Key Concept:** exams/gcp/associate-data-practitioner/notes/04-data-management.md
</details>

---

### Question 22 - Section 4: Data Management
**Scenario:** A bucket holds data that must never be exposed through per-object ACL mistakes. Which Cloud Storage setting is recommended?

A. Fine-grained access with ACLs on each object
B. Uniform bucket-level access, so IAM alone controls access
C. Making the bucket public and relying on obscure object names
D. Granting allUsers the Storage Object Viewer role

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Uniform bucket-level access uses IAM alone and is the recommended mode; the docs warn that fine-grained access raises the chance of unintended exposure. Public grants and secret names are the opposite of access control.

**Key Concept:** exams/gcp/associate-data-practitioner/notes/04-data-management.md
</details>

---

### Question 23 - Section 4: Data Management
**Scenario:** A company wants to give a partner live, read-only access to a BigQuery dataset without copying it or sending exports. What should it use?

A. Analytics Hub, publishing the dataset in a listing
B. Storage Transfer Service into the partner's bucket
C. Copying the dataset into the partner's project every week
D. A nightly export of the table to CSV on a shared drive

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Analytics Hub shares datasets across organizations as a listing, and the subscriber gets a read-only linked dataset that always reflects current data. Exports and copies create stale duplicates.

**Key Concept:** exams/gcp/associate-data-practitioner/notes/04-data-management.md
</details>

---

### Question 24 - Section 4: Data Management
**Scenario:** Backup files are read about once a year for audits and must be kept for seven years at the lowest storage cost. Which storage class is the best fit?

A. Nearline
B. Standard
C. Coldline
D. Archive

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Archive is intended for data accessed less than once a year, with a 365-day minimum duration and the lowest storage price. Standard is for hot data, Nearline suits roughly monthly access and Coldline roughly quarterly access.

**Key Concept:** exams/gcp/associate-data-practitioner/notes/04-data-management.md
</details>

---

### Question 25 - Section 4: Data Management
**Scenario:** Log objects should move to a colder class after 30 days and be deleted after two years, with no manual work. What do you configure?

A. Object Lifecycle Management rules with SetStorageClass and Delete actions
B. A Cloud Scheduler job that emails an administrator each month
C. A Dataform assertion on the bucket
D. Cloud SQL point-in-time recovery

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Lifecycle rules apply conditions such as age and perform actions such as SetStorageClass and Delete automatically. The other options do not act on bucket objects.

**Key Concept:** exams/gcp/associate-data-practitioner/notes/04-data-management.md
</details>

---

### Question 26 - Section 4: Data Management
**Scenario:** A Cloud SQL database must survive the loss of a zone automatically, with clients reconnecting to the same address. What should be enabled?

A. Nightly on-demand backups only
B. A read replica in the same zone
C. High availability, creating a standby in another zone
D. A larger machine type

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** An HA (regional) instance keeps a standby in a different zone, replicates writes synchronously and fails over automatically with the same connection address. A same-zone replica fails with the zone, backups restore slowly and a bigger machine does not add zone redundancy.

**Key Concept:** exams/gcp/associate-data-practitioner/notes/04-data-management.md
</details>

---

### Question 27 - Section 4: Data Management
**Scenario:** A regulation says the company must control the lifecycle and rotation of the keys that encrypt its BigQuery data, with audit logs of key use. Which option meets this?

A. Disabling encryption at rest to simplify key handling
B. Google-managed encryption keys, the default
C. Customer-supplied keys held in a laptop's local folder
D. Customer-managed encryption keys created in Cloud KMS

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** CMEK gives the customer ownership of the key, control of rotation and audit logging of key use. Google-managed keys are the default without that control, and encryption at rest cannot be turned off. CSEK is a Cloud Storage and Compute Engine option where Google does not store the key, and is not the choice for this requirement.

**Key Concept:** exams/gcp/associate-data-practitioner/notes/04-data-management.md
</details>

---

### Question 28 - Section 4: Data Management
**Scenario:** After a developer deletes rows in a Cloud SQL database by mistake at 14:05, the team wants to restore the database to 14:00. Which feature provides this?

A. Turbo replication
B. Point-in-time recovery, which uses automated backups plus transaction logs
C. A read replica, which already contains the deleted rows
D. Autoclass on the backup bucket

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** PITR restores to a chosen moment using automated backups and logs. A read replica replicates the deletion too, and Autoclass and turbo replication are Cloud Storage features.

**Key Concept:** exams/gcp/associate-data-practitioner/notes/04-data-management.md
</details>

---
