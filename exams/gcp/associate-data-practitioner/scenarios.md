---
last-updated: 2026-11-07
applies-to: Google Cloud exam guide v1.0, read 2026-11-07
---

# Google Cloud Associate Data Practitioner - Exam-Style Scenarios

Original scenarios for practice. They are not vendor exam questions.

## Scenario 1: A Nightly Report

### Scenario
Orders and customers are loaded into BigQuery every day. The finance team needs a revenue-by-region table rebuilt at 03:30 each night. The logic is one SQL statement.

**Question:** Which service should schedule the rebuild?

**Options:**
A. A BigQuery scheduled query
B. A Cloud Composer environment
C. A Dataproc cluster
D. A Dataflow streaming job

**Correct Answer:** A

**Explanation:**
- A scheduled query runs a recurring GoogleSQL statement with no extra infrastructure
- The minimum interval is 5 minutes, so nightly is well within range

**Why other options are wrong:**
- **B:** Composer is for multi-step dependent workflows; it is more than one statement needs
- **C:** Dataproc runs Spark and Hadoop code, which is not required here
- **D:** A streaming job runs continuously and is not a scheduler

---

## Scenario 2: Moving a Large Archive

### Scenario
A media company has 600 TB of footage in an on-premises file system. Their internet link would need roughly a year to upload it.

**Question:** What is the best way to move it to Cloud Storage?

**Options:**
A. Storage Transfer Service over the existing link
B. Transfer Appliance
C. BigQuery Data Transfer Service
D. Scripts using gcloud storage cp from one workstation

**Correct Answer:** B

**Explanation:**
- Transfer Appliance fits when network upload would take more than a week
- Data is shipped to Google and uploaded to a bucket

**Why other options are wrong:**
- **A:** Still limited by the thin link
- **C:** Loads into BigQuery, not Cloud Storage
- **D:** Does not solve the bandwidth problem

---

## Scenario 3: Retention Cost

### Scenario
A bucket stores application logs. They are read often for 30 days, rarely until 365 days, and must be deleted after 3 years.

**Question:** How should the cost be controlled with the least manual work?

**Options:**
A. Move objects by hand each quarter
B. Lifecycle rules: SetStorageClass to a colder class at 30 days, Delete at about 1095 days
C. Store everything in Standard and delete manually
D. Copy the logs to Cloud SQL

**Correct Answer:** B

**Explanation:**
- Lifecycle rules act automatically on age conditions
- Colder classes cost less to store; mind minimum durations to avoid early deletion fees

**Why other options are wrong:**
- **A:** Manual work and error-prone
- **C:** Pays the highest storage price for cold data
- **D:** Cloud SQL is not log archival storage

---

## Scenario 4: A Failing Zone

### Scenario
A Cloud SQL for MySQL database backs an order system. Management wants it to survive a zone outage with an automatic switch and the same connection address.

**Question:** Which configuration meets the goal?

**Options:**
A. A read replica in the same zone
B. High availability with a standby in another zone
C. Daily on-demand backups only
D. A scheduled query that exports data hourly

**Correct Answer:** B

**Explanation:**
- An HA instance replicates synchronously to a standby in another zone and fails over automatically
- Clients reconnect using the same address; expect roughly a minute of unavailability

**Why other options are wrong:**
- **A:** A same-zone replica fails with the zone
- **C:** Backups restore slowly and manually
- **D:** Exports are not a failover mechanism

---

## Scenario 5: Predicting Churn

### Scenario
A subscription business has a BigQuery table with customer usage and a column showing who cancelled. They want a churn score for each current customer.

**Question:** What is the most direct approach?

**Options:**
A. K-means clustering
B. A logistic regression model trained with BigQuery ML, evaluated with ML.EVALUATE and applied with ML.PREDICT
C. Export the table and sort by last login
D. An ARIMA_PLUS forecast of total customers

**Correct Answer:** B

**Explanation:**
- Cancelled or not is a labeled yes/no outcome, which is classification
- Evaluate on held-out rows before predicting on current customers

**Why other options are wrong:**
- **A:** Clustering is unlabeled grouping
- **C:** Sorting is not a model
- **D:** Forecasts a series, not a score per customer
