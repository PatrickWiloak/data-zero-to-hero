---
last-updated: 2026-11-25
difficulty: n/a
---

# Content gaps and improvement plan (2026-11-25)

**Scope:** data.noblerworks.com at `origin/main` (commit 9cd4e82), all pillars. **Input:** a Gemini
(Antigravity CLI) audit pasted by Patrick, treated as leads only. **Method:** every figure re-measured with
`git ls-files` and `git grep` against `origin/main`; vendor facts read on the vendor's own page on
2026-11-25. A topic is called a gap only when the search found no page that covers it. Earlier review:
[content-gaps-2026-11-07.md](content-gaps-2026-11-07.md). Open items are tracked in `TODO.md`.

## Gemini claims checked

18 claims: **12 confirmed, 4 overstated, 2 false.**

| Claim | Verdict | Measured figure |
|---|---|---|
| A streaming or warehouse postmortem is still open from 11-07 | confirmed | `git ls-files \| grep -i postmortem` = 2 files, GitLab 2017 and Atlassian 2022, both database incidents |
| `drafts/resources/architecture-patterns/open-table-formats-compared.md` is unpublished | confirmed | Only file under `drafts/` besides its README; 2,041 words. Verdict at the end of this doc |
| Short concept page missing: query plans | overstated | `learn/concepts/indexes-explained.md` has "Reading the plan" (`EXPLAIN ANALYZE`, Seq vs Index Scan, estimated vs actual rows); `sql-joins-explained.md` covers join algorithms and `EXPLAIN`. Long form: `resources/troubleshooting/slow-sql-query-diagnosis.md` (2,205 words). Not a gap |
| Short concept page missing: Spark shuffles and skew | confirmed | In `learn/concepts/`, "shuffle" = 1 paragraph (sql-joins-explained) + 1 line (window-functions-explained); "skew" hits only mean training-serving skew and write skew, never data skew. Long form: `resources/troubleshooting/spark-job-performance.md` (2,345 words) |
| Short concept page missing: semantic layers | overstated | `learn/concepts/data-modeling-for-analytics.md` has "A note on the metrics layer"; `resources/architecture-patterns/semantic-layer.md` is 1,878 words. A concept page would repeat both. Not a gap |
| DP-300 notes lack database watcher, SQL DB in Fabric, elastic jobs, Resource Governor, IQP, compression, log shipping | **false** | All 7 are headings on `origin/main` (Q-002, commit 7d5a1bb): Database Watcher in `database-administration-overview.md`; IQP, Data Compression (Row and Page), Resource Governor in `performance-monitoring-optimization.md`; SQL Database in Microsoft Fabric, Elastic Jobs, Log Shipping in `sql-deployment-management.md`. The TODO line is stale (ticked in this commit) |
| "Vertex AI" rename sweep needed in `exams/gcp/data-engineer` | overstated | 20 mentions in 7 files, but 6 are deliberate "Agent Platform (formerly Vertex AI)". 14 bare mentions remain, all in `notes/machine-learning-operations.md` (12) and `notes/data-processing-systems.md` (2). The current exam guide PDF names neither "Vertex AI" nor "Agent Platform" (0 hits) |
| DP-203 keep-vs-remove decision open | confirmed | `exams/azure/dp-203/` is marked RETIRED and kept as reference; the decision is Patrick's (TODO "Exam currency"), not a loop item |
| 11 fact-sheets stamped 2026-05-03 | confirmed | 11: DEA-C01, DAS-C01, DBS-C01, DP-900, Confluent x2, GCP x2 (data-engineer, cloud-database-engineer), MongoDB x3 (atlas-administrator, dba, developer). The 2026-12-15 TODO line says 8; DAS-C01 and DBS-C01 are retired, so they matter least |
| dp-300, dp-420, dp-600, dp-900 lack both `scenarios.md` and `strategy.md` | confirmed | Both files absent in all 4. Also absent: dp-203 both (retired), dp-700 `strategy.md` |
| 18 of 28 certs have no lab in `labs-by-cert.md` | confirmed | The generated page says "10 of 28". 2 of the 18 are retired AWS specialties |
| Missing cert: Confluent Cloud Certified Operator (CCAC) | confirmed | No `exams/confluent/cloud-operator`. Already tracked and blocked: the exam guide is behind a training-portal login (TODO 2026-11-22) |
| Missing cert: dbt Analytics Engineering | confirmed | 0 hits for a dbt cert guide. Vendor page (getdbt.com, read 2026-11-25): "dbt Analytics Engineering Certification Exam", supported version 1.11 |
| Missing cert: Astronomer Airflow | confirmed | 0 hits for "astronomer". Vendor page (astronomer.io/certification, read 2026-11-25): Apache Airflow 3 Fundamentals and Apache Airflow 3 DAG Authoring, 75 questions, 60 minutes, USD 150, pass 53 of 75 |
| Missing cert: MongoDB Data Modeler | **false** | `exams/mongodb/associate-data-modeler/` exists (fact sheet `last-updated: 2026-11-07`) |
| No Data Analyst interview guide | confirmed | 5 guides in `resources/interview-prep/` (analytics engineer, data engineer, data modeling, DBA, SQL); none for analysts. The SQL guide covers only the SQL round |
| No SQL/CLI cheat sheets (DuckDB, dbt, psql, kafka CLI, PySpark) | confirmed | No reference page. The commands are spread across pages (e.g. `kafka-consumer-groups` on 7, `dbt build` on 4, `duckdb` on 13) |
| No warehouse cost-optimization playbooks | overstated | `learn/concepts/warehouse-cost-control.md` (864 words), pricing models in `resources/service-comparison-warehouses.md`, cost sections in 3 Snowflake cert notes, BigQuery slots in the GCP data-engineer notes. Per-warehouse playbooks would be deepening, not a gap |

**Found while checking (not in the Gemini audit):** DP-700 is shown as **Retired** on the published Azure index
(`exams/azure/README.md` line 15, from `docs/certs.json` `status: retired`). Microsoft's certification page
(read 2026-11-25, last updated 2026-10-19) shows DP-700 live with no retirement notice. Cause:
`classify_status()` in `.github/scripts/build-certs-index.py` returns `retired` when the README's first 2,500
characters contain "was retired"; the DP-700 README says DP-203 "was retired on 2025-03-31".

## Plan, in order

### Wave 1 - fix what is wrong

- **DP-700 status.** In `.github/scripts/build-certs-index.py`, match the retired banner only on the README
  title (`- RETIRED`) or a "This exam ... was retired" sentence about the cert itself, then rerun
  `build-certs-index.py` and `build-provider-indexes.py`. Done when `docs/certs.json` has
  `azure/dp-700` `status: active` and `exams/azure/README.md` shows DP-700 as Ready. Evidence: the
  measurement above. (A generator fix, so it may be a hand change rather than a queue item.)
- **Map existing labs to certs that fit them** (frontmatter only, `certs:` in each project, then
  `build-lab-map.py`): Databricks DE Professional to the Debezium CDC, Iceberg and Dagster projects; DP-700
  to build-data-pipeline and iceberg-lakehouse-local; GCP Cloud Database Engineer to the backup-and-restore
  drill. The builder must check each pairing against the cert's outline before adding it. Evidence:
  `labs-by-cert.md` lists none of these 3 certs.

### Wave 2 - complete the four live Azure guides

`scenarios.md` and `strategy.md` for `exams/azure/dp-300`, `dp-420`, `dp-600` and `dp-900` (8 files), in the
shape of `exams/azure/pl-300/`. Evidence: `git ls-files exams/azure/<cert>/scenarios.md` is empty for all 4.
Overlap to reuse, not copy: each `practice-plan.md` has an exam-day section, and dp-900's README has
"Exam Strategy". Scenarios must be original, never vendor questions.

### Wave 3 - new pages with no existing cover

- **Data analyst interview guide** at `resources/interview-prep/data-analyst-interview.md`: metrics and
  business-case questions, A/B tests and basic stats, dashboard critique, SQL (link the SQL guide rather than
  repeat it). Link from `resources/certification-roadmap-data-analyst.md`. Evidence: no analyst guide among
  the 5.
- **A streaming or analytics-pipeline postmortem** from a primary source. Two checked candidates, neither
  used on any of the four sites: Cloudflare's control plane and analytics outage postmortem (blog.cloudflare.com, incident
  2023-11-02 to 11-04; Kafka and ClickHouse in one data center; Logpush logs unprocessed, some
  datasets with permanent gaps), and Parse.ly, "Kafkapocalypse" (March 2015). Path:
  `resources/postmortem-cloudflare-analytics-2023.md` or similar. Evidence: 2 postmortems, both database.
- **Spark shuffles and skew** concept page at `learn/concepts/spark-shuffles-and-skew.md` (5-10 minutes,
  with a "Check yourself" section): what a shuffle is, wide vs narrow transformations, data skew, salting,
  AQE skew join. Link the long-form troubleshooting page. Evidence: 0 concept pages cover data skew.

### Wave 4 - new cert guides (vendor pages read 2026-11-25)

- **dbt Analytics Engineering Certification** (supported version 1.11) at a new `exams/dbt/` provider.
  The exam page did not show question count, length, fee or validity; read the study guide before writing
  the fact sheet.
- **Astronomer Apache Airflow 3 Fundamentals and DAG Authoring** at a new `exams/astronomer/` provider.
- Each new provider changes `docs/certs.json` totals: run `build-certs-index.py`,
  `build-provider-indexes.py` and `check-readme-counts.py --fix` in the same change.
- Confluent CCAC stays blocked on the gated guide (existing TODO line). Not queueable yet.

### Wave 5 - deepening

- **Labs for certs no project can fit:** a MongoDB lab (4 MongoDB certs, 0 projects mention MongoDB) and a
  local PySpark lab for the Spark Developer Associate (the only PySpark in a project is a Glue job script).
- **Command cheat sheets** (one page each, or one page with a section per tool): psql, DuckDB CLI, dbt,
  Kafka CLI, PySpark DataFrame basics. Every command must be run. Gather from the pages that already use
  them and link back.
- **GCP data-engineer leftovers:** the 14 bare "Vertex AI" mentions in 2 notes files. Use Google's rename
  table; keep SDK and package names (`aiplatform`, `vertexai`) as they are.

### Not queue items

- **Freshness:** the 11 fact-sheets at 2026-05-03 are listed in [freshness.md](freshness.md); the
  re-verify step is the existing 2026-12-15 TODO line (which should say 11, not 8).
- **DP-203:** keep or remove is Patrick's decision.
- **Query plans and semantic layers:** covered (see the table). Drop them from the TODO concept candidates.

## Draft verdict: open-table-formats-compared.md

**Publish after these fixes.** The structure, decision rule and most facts hold. Checked against the cited
docs on 2026-11-25:

| Statement in the draft | Source says | Fix |
|---|---|---|
| Hudi example uses `preCombineField = 'ts'` | Hudi SQL DDL docs (version 1.2.1) list `orderingFields` as the table property; `preCombineField` appears only in older examples | Use `orderingFields = 'ts'` and say older releases used `preCombineField` |
| Merge-on-read uses position and equality deletes (v2); "the version 3 spec adds deletion vectors" | Iceberg spec: v3 adds binary deletion vectors and prohibits new position delete files; v4 is "under active development and has not been formally adopted" (adaptive metadata tree, no new equality deletes) | Say v3 replaces position delete files with deletion vectors; add one line that v4 (not adopted) changes the metadata tree the diagram shows |
| Delta multi-cluster writes to S3 "have needed special handling in the past" | Delta storage docs: concurrent writes from several drivers "can lead to data loss"; multi-cluster needs the DynamoDB LogStore, "new and experimental" | Present tense: multi-cluster S3 writes need the DynamoDB LogStore |
| UniForm writes Iceberg "(and Hudi)" metadata | Delta UniForm docs: Iceberg needs Delta 3.1+; Hudi is "preview", Delta 3.2+ | Add the versions and the Hudi preview label |
| `write.delete.mode` / `write.update.mode` / `write.merge.mode` | Correct (Iceberg Configuration page; default copy-on-write) | Cite the Iceberg Configuration page; the draft cites only the spec and DDL pages |

Confirmed as written: Iceberg `PARTITIONED BY (days(...))` and `ADD PARTITION FIELD bucket(16, ...)`
(Spark DDL page); Hudi `type = 'mor'` and `primaryKey`; Delta liquid clustering (Delta 3.1 preview, 3.2
supported, keys changeable with `ALTER TABLE`); Apache XTable still incubating; Delta data files are Parquet.
Internal links and anchors resolve once the page moves to `resources/architecture-patterns/`.
Publishing is Patrick's call (TODO "Content refill").
