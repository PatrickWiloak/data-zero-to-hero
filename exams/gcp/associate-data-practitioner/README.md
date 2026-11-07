---
last-updated: 2026-11-07
applies-to: Google Cloud exam page and exam guide v1.0, read 2026-11-07
---

# Google Cloud Associate Data Practitioner

## Exam Overview

The Associate Data Practitioner exam tests whether you can do the everyday jobs of data work on Google Cloud: bring data in with the right transfer and storage choices, analyze it with BigQuery, notebooks, Looker and BigQuery ML, schedule and monitor simple pipelines, and manage access, lifecycle, resilience and encryption. It is an entry-level exam with a broad, shallow spread of products, which makes tool selection the main skill.

**Exam Details** (from the **[📖 exam page](https://cloud.google.com/learn/certification/data-practitioner)** and guide, read 2026-11-07):
- **Exam Name:** Associate Data Practitioner (no exam code published)
- **Duration:** 2 hours
- **Number of Questions:** 50-60 multiple choice and multiple select
- **Passing Score:** Not published
- **Cost:** $125 USD plus tax where applicable
- **Delivery:** Online proctored or onsite at a testing center
- **Languages:** English, Japanese
- **Validity:** Total period not stated on the exam page or the FAQ. The **[📖 renewal FAQs](https://support.google.com/cloud-certification/answer/9907853)** say renewal for "all other foundational and associate certifications" is the standard exam only, opens 180 days before the inactive date, adds 3 years, and is allowed up to 30 days after it (read 2026-11-07)
- **Prerequisites:** None; 6+ months of data work on Google Cloud recommended

## Exam Sections

The **[📖 exam guide v1.0](https://services.google.com/fh/files/misc/v1.0_associate_data_practitioner_exam_guide_english.pdf)** lists four sections.

| # | Section | Weight | About | Notes |
|---|---|---|---|---|
| 1 | Data Preparation and Ingestion | ~30% | 15-18 | [01](notes/01-data-preparation-and-ingestion.md) |
| 2 | Data Analysis and Presentation | ~27% | 13-16 | [02](notes/02-data-analysis-and-presentation.md) |
| 3 | Data Pipeline Orchestration | ~18% | 9-11 | [03](notes/03-data-pipeline-orchestration.md) |
| 4 | Data Management | ~25% | 12-15 | [04](notes/04-data-management.md) |

The question counts apply the weights to 50-60 questions; Google does not publish per-section counts.

### What each section covers

1. **Data Preparation and Ingestion** - ETL, ELT and ETLT; Storage Transfer Service, Transfer Appliance and the BigQuery Data Transfer Service; data quality and cleaning; file formats; choosing among Cloud Storage, BigQuery, Cloud SQL, Firestore, Bigtable and Spanner; location types.
2. **Data Analysis and Presentation** - SQL in BigQuery, notebooks, Looker versus Looker Studio, LookML parameters, BigQuery ML (train, evaluate, predict), AutoML, remote LLM models and the Model Registry.
3. **Data Pipeline Orchestration** - choosing Dataflow, Dataproc, Data Fusion, Composer or Dataform; scheduled queries; Workflows; Pub/Sub to BigQuery; Eventarc; the Dataflow job UI, Logging and Monitoring.
4. **Data Management** - IAM and least privilege; Cloud Storage access control; Analytics Hub; storage classes and lifecycle rules; HA and DR for Cloud Storage and Cloud SQL; CMEK, CSEK, GMEK and Cloud KMS; encryption at rest and in transit.

## Study Approach

### Phase 1: Ingest and analyze (Weeks 1-2)
1. Section 1: tool selection tables for transfer, processing and storage; do a BigQuery load from Cloud Storage.
2. Section 2: run queries in the BigQuery console; train a small BigQuery ML model; look at Looker Studio.

### Phase 2: Orchestrate and manage (Weeks 3-4)
1. Section 3: create a scheduled query; read the Composer and Workflows overviews and say when you would use each.
2. Section 4: set a bucket lifecycle rule; grant a role at dataset level; read the Cloud SQL HA page.

### Phase 3: Exam readiness (Week 5)
1. Practice questions, then the "How the exam tests this section" ending of each note for the sections you miss.

## Study Resources

- **[📖 Exam page](https://cloud.google.com/learn/certification/data-practitioner)** - format, fee, sample questions link
- **[📖 Exam guide v1.0 (PDF)](https://services.google.com/fh/files/misc/v1.0_associate_data_practitioner_exam_guide_english.pdf)** - every objective
- **[📖 Skills Boost learning path](https://www.cloudskillsboost.google/paths/1336)** - Google's training and labs
- **[📖 Free trial](https://console.cloud.google.com/freetrial)** - the exam page says new customers get $300 in credits and free usage of over 20 products up to monthly limits

## Tips for Success

1. **Choose the simplest sufficient tool.** The exam rewards right-sizing, not the most powerful service.
2. **Know the pairs:** Storage Transfer Service vs Transfer Appliance; Composer vs Workflows vs scheduled queries; CMEK vs CSEK vs default.
3. **Read for the constraint:** volume, frequency, latency, compliance, who needs access.
4. **Practice BigQuery ML syntax** well enough to recognise `CREATE MODEL`, `ML.EVALUATE`, `ML.PREDICT`.

## File Index

| File | Description |
|------|-------------|
| [fact-sheet.md](fact-sheet.md) | Exam facts and key facts per section, with documentation links |
| [strategy.md](strategy.md) | Study phases and exam tactics |
| [practice-plan.md](practice-plan.md) | Five-week schedule with checkboxes |
| [scenarios.md](scenarios.md) | Exam-style scenarios with solutions |
| [notes/01-data-preparation-and-ingestion.md](notes/01-data-preparation-and-ingestion.md) | ETL/ELT, transfer tools, formats, storage, locations |
| [notes/02-data-analysis-and-presentation.md](notes/02-data-analysis-and-presentation.md) | SQL, notebooks, Looker, BigQuery ML |
| [notes/03-data-pipeline-orchestration.md](notes/03-data-pipeline-orchestration.md) | Transform and orchestration tools, events, monitoring |
| [notes/04-data-management.md](notes/04-data-management.md) | IAM, lifecycle, HA/DR, encryption |
| [Practice questions](../../../resources/practice-questions/gcp-associate-data-practitioner.md) | 28 questions tagged by section |
