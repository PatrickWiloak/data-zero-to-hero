---
last-updated: 2026-11-07
applies-to: Google Cloud exam page and exam guide v1.0, read 2026-11-07
---

# Google Cloud Associate Data Practitioner - Fact Sheet

## Exam Overview

**Exam Name:** Associate Data Practitioner
**Exam Code:** None published on the exam page
**Duration:** 2 hours
**Questions:** 50-60 multiple choice and multiple select questions
**Passing Score:** Not published on the exam page or guide
**Cost:** $125 USD plus tax where applicable (exam page, read 2026-11-07)
**Delivery:** Online proctored from home, or onsite at a Pearson VUE test center
**Languages:** English, Japanese
**Valid For:** The exam page does not state a validity period; see the Renewal FAQ
**Prerequisites:** None; 6+ months of hands-on experience with data on Google Cloud recommended
**Level:** Associate

Registration is through Google Cloud's certification portal (the exam page links to cp.certmetrics.com). The renewal FAQ lists renewal terms for "other foundational and associate certifications": the renewal window opens 180 days before the inactive date, renewal adds 3 years, and the standard exam is the only option. Read that FAQ on the date you plan to renew, because the table was hard to parse when this guide was written.

**[📖 Official exam page](https://cloud.google.com/learn/certification/data-practitioner)** - format, fee, delivery, links to the guide and sample questions
**[📖 Exam guide v1.0 (PDF)](https://services.google.com/fh/files/misc/v1.0_associate_data_practitioner_exam_guide_english.pdf)** - the full section and objective list
**[📖 Renewal FAQs](https://support.google.com/cloud-certification/answer/9907853)** - renewal window and extension
**[📖 Training path](https://www.cloudskillsboost.google/paths/1336)** - Google Cloud Skills Boost path for the exam

## Sections and Weights

| # | Section | Weight |
|---|---|---|
| 1 | Data Preparation and Ingestion | ~30% |
| 2 | Data Analysis and Presentation | ~27% |
| 3 | Data Pipeline Orchestration | ~18% |
| 4 | Data Management | ~25% |

## Target Audience

- Analysts and junior data engineers who work with data on Google Cloud
- People who need to ingest, transform, analyze, orchestrate and secure data, not design large systems
- A step before the Professional Data Engineer certification

## Section 1: Data Preparation and Ingestion (~30%)

**[📖 Storage Transfer Service](https://docs.cloud.google.com/storage-transfer/docs/overview)** - online transfers, optimized for more than 1 TiB
**[📖 Transfer Appliance](https://docs.cloud.google.com/transfer-appliance/docs/4.0/overview)** - offline device when upload takes over a week
**[📖 BigQuery Data Transfer Service](https://docs.cloud.google.com/bigquery/docs/dts-introduction)** - scheduled loads into BigQuery
**[📖 Cloud Storage locations](https://docs.cloud.google.com/storage/docs/locations)** - region, dual-region, multi-region

**Key Facts:**
- ELT loads raw data first and transforms in the warehouse; ETL transforms first; ETLT is a hybrid
- Storage Transfer Service: S3, Azure Blob, on-premises, HDFS, URLs into Cloud Storage; Transfer Appliance when upload takes more than a week
- BigQuery batch loads accept Avro, CSV, JSON, ORC and Parquet; Avro and Parquet carry their schema
- Region is cheapest; dual-region gives cross-region redundancy; multi-region spans a large geography; turbo replication targets 15 minutes
- Cloud Data Fusion is visual; Dataflow is code or templates on Apache Beam

## Section 2: Data Analysis and Presentation (~27%)

**[📖 Introduction to BigQuery ML](https://docs.cloud.google.com/bigquery/docs/bqml-introduction)** - model types and remote models
**[📖 ML.EVALUATE](https://docs.cloud.google.com/bigquery/docs/reference/standard-sql/bigqueryml-syntax-evaluate)** - evaluation metrics
**[📖 Generative AI in BigQuery](https://docs.cloud.google.com/bigquery/docs/generative-ai-overview)** - remote models and AI functions

**Key Facts:**
- BigQuery ML trains models with SQL: `CREATE MODEL` (MODEL_TYPE required), `ML.EVALUATE`, `ML.PREDICT`
- Classification: logistic regression or boosted trees; regression: linear; clustering: K-means; recommendation: matrix factorization; time series: ARIMA_PLUS
- `ML.EVALUATE` classification metrics: precision, recall, accuracy, F1, log loss, ROC AUC; default threshold 0.5
- Remote models call hosted models such as Gemini; AI functions run over columns
- Looker uses LookML for governed shared metrics; Looker Studio is self-service reporting

## Section 3: Data Pipeline Orchestration (~18%)

**[📖 Scheduling queries](https://docs.cloud.google.com/bigquery/docs/scheduling-queries)** - scheduled query rules
**[📖 Cloud Composer](https://docs.cloud.google.com/composer/docs/composer-3/composer-overview)** - managed Airflow
**[📖 Workflows](https://docs.cloud.google.com/workflows/docs/overview)** - serverless orchestration
**[📖 Eventarc](https://docs.cloud.google.com/eventarc/docs/overview)** - event routing
**[📖 Pub/Sub BigQuery subscriptions](https://docs.cloud.google.com/pubsub/docs/bigquery)** - direct writes to BigQuery

**Key Facts:**
- Scheduled queries: minimum interval 5 minutes; `@run_time` and `@run_date`; avoid exactly on the hour
- Composer = Airflow DAGs in Python; Workflows = YAML or JSON steps calling services; pick the simplest tool
- Dataform = SQLX plus `ref()` dependencies and assertions
- BigQuery subscription = at-least-once, no processing; use Dataflow for windowing or complex transforms
- Eventarc destinations: Cloud Run, Workflows, Cloud Run functions

## Section 4: Data Management (~25%)

**[📖 BigQuery IAM roles](https://docs.cloud.google.com/bigquery/docs/access-control)** - predefined roles
**[📖 Cloud Storage classes](https://docs.cloud.google.com/storage/docs/storage-classes)** - minimum durations
**[📖 Object Lifecycle Management](https://docs.cloud.google.com/storage/docs/lifecycle)** - rules and actions
**[📖 Cloud SQL high availability](https://docs.cloud.google.com/sql/docs/mysql/high-availability)** - standby and failover
**[📖 Cloud KMS](https://docs.cloud.google.com/kms/docs/key-management-service)** - CMEK and keys

**Key Facts:**
- Least privilege: Data Viewer to read, Job User to run queries, avoid basic roles
- Uniform bucket-level access is recommended; fine-grained ACLs raise exposure risk
- Classes: Standard none, Nearline 30 days, Coldline 90 days, Archive 365 days; Autoclass automates
- Lifecycle actions: Delete, SetStorageClass, AbortIncompleteMultipartUpload; changes take up to 24 hours
- Cloud SQL HA: standby in another zone, synchronous writes, about 60 seconds failover, standby serves no reads
- GMEK default; CMEK in Cloud KMS; CSEK supplied by you and not a KMS feature
- Analytics Hub shares datasets without copying, as read-only linked datasets

## Exam Tips

1. **Pick the simplest tool** that meets the requirement: a scheduled query before Composer, a BigQuery subscription before Dataflow.
2. **Match storage class to access frequency**: monthly, quarterly, yearly.
3. **Least privilege wording** points to predefined roles at the narrowest scope.
4. **HA, replicas and backups are different tools**: failover, read scaling, human error.
5. **Time management:** about 2 minutes per question across 50-60 questions.

## Quick Reference

```sql
-- BigQuery ML (not run: needs a BigQuery project)
CREATE OR REPLACE MODEL `proj.ds.m` OPTIONS (model_type = 'LOGISTIC_REG', input_label_cols = ['label'])
AS SELECT * FROM `proj.ds.train`;
SELECT * FROM ML.EVALUATE(MODEL `proj.ds.m`, (SELECT * FROM `proj.ds.test`));
SELECT * FROM ML.PREDICT(MODEL `proj.ds.m`, (SELECT * FROM `proj.ds.new_rows`));
```
