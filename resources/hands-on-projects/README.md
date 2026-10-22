---
last-updated: 2026-10-21
---

# Hands-on projects

Guided builds with an estimated time, a goal you can explain to an interviewer, and inline code or commands. Most run on free-tier or trial accounts.

## Data builds

| Build | Time | What you'll have at the end |
|---|---|---|
| [Build a data pipeline](./build-data-pipeline.md) | 4-5 hours | Ingest → transform → load with scheduled runs |
| [Design a star schema](./design-star-schema.md) | 2-3 hours | A bus matrix, declared grains, a Type 2 dimension and SQL that proves the design |
| [Build a dbt project](./build-dbt-project.md) | 4-5 hours | A tested star schema in dbt: snapshots, an incremental fact with a contract, unit tests, CI selection |
| [Build a streaming pipeline](./build-streaming-pipeline.md) | 4-6 hours | Kafka into a Parquet lake, event-time windows with a watermark, and a batch reconciliation |

The [Data from Scratch](../../learn/data-from-scratch.md) path also sets a small "Do" exercise in every phase - a SQL warm-up, a star schema redesign, a streaming consumer, data-quality checks - that build up to the same pipeline. A sensible order for the builds: star schema design, then the batch pipeline, then dbt, then streaming.

## Builds on the sibling sites

Hands-on projects outside data live with their subject:

- **Cloud** - [3-tier app](https://cloud.noblerworks.com/resources/hands-on-projects/deploy-3-tier-app/), [CI/CD pipeline](https://cloud.noblerworks.com/resources/hands-on-projects/build-ci-cd-pipeline/), [Terraform infrastructure](https://cloud.noblerworks.com/resources/hands-on-projects/terraform-infrastructure/), [serverless app](https://cloud.noblerworks.com/resources/hands-on-projects/serverless-application/) and more on [Cloud Zero to Hero](https://cloud.noblerworks.com/)
- **Security** - [implement zero trust](https://security.noblerworks.com/resources/hands-on-projects/implement-zero-trust/) on [Security Zero to Hero](https://security.noblerworks.com/)
- **AI** - [RAG pipeline](https://ai.noblerworks.com/build/projects/build-rag-pipeline/), [deploy an ML model](https://ai.noblerworks.com/build/projects/deploy-ml-model/) and more on [AI Zero to Hero](https://ai.noblerworks.com/)

## Related

- [Concepts](../../learn/concepts/) - the "why" behind each pattern
- [Architecture patterns](../architecture-patterns/) - reference designs each build follows
- [Topic indexes](../../topics/) - all four pillars per subject

## Which lab for which cert

Each lab maps to the certs it exercises. The reverse index lives in [labs-by-cert.md](./labs-by-cert.md).

<!-- BEGIN GENERATED: lab-cert-map - run .github/scripts/build-lab-map.py -->

| Lab | Certs it supports |
|------|-------------------|
| [Hands-On Project: Build a Data Pipeline](./build-data-pipeline.md) | [DEA-C01](../../exams/aws/associate/data-engineer-dea-c01/), [DP-203](../../exams/azure/dp-203/), [Professional Data Engineer](../../exams/gcp/data-engineer/), [Databricks Certified Data Engineer Associate](../../exams/databricks/data-engineer-associate/) |
| [Hands-On Project: Build a dbt Project](./build-dbt-project.md) | [DP-600](../../exams/azure/dp-600/), [COF-C03](../../exams/snowflake/snowpro-core/), [DEA-C02](../../exams/snowflake/snowpro-advanced-data-engineer/), [Databricks Certified Data Engineer Associate](../../exams/databricks/data-engineer-associate/) |
| [Hands-On Project: Build a Streaming Pipeline](./build-streaming-pipeline.md) | [Confluent Certified Developer for Apache Kafka](../../exams/confluent/certified-developer/), [DEA-C01](../../exams/aws/associate/data-engineer-dea-c01/), [Professional Data Engineer](../../exams/gcp/data-engineer/), [Databricks Certified Data Engineer Associate](../../exams/databricks/data-engineer-associate/) |
| [Hands-On Project: Design a Star Schema](./design-star-schema.md) | [PL-300](../../exams/azure/pl-300/), [DP-600](../../exams/azure/dp-600/), [COF-C03](../../exams/snowflake/snowpro-core/), [DEA-C01](../../exams/aws/associate/data-engineer-dea-c01/) |

<!-- END GENERATED: lab-cert-map -->
