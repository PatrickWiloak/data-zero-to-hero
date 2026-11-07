---
last-updated: 2026-11-07
---

# Fact verification pass, 2026-11-07

A claim-by-claim check of the pages written on 2026-11-07 against vendor and project primary documentation fetched the same day. Skipped: README and index pages, generated files, `docs/content-gaps-2026-11-07.md`, and the exams/snowflake and exams/mongodb notes (already corrected). Counts are approximate groupings of checkable claims (product behavior, defaults, names, flags, versions), not a line-exact audit.

**Classes.** VERIFIED: the fetched page supports it. FIXED: the page said something the source contradicts or does not support, now changed. UNVERIFIABLE: no fetched source settles it; the page now says so, or the claim was softened or removed.

## Per page

| Page | Verified | Fixed | Unverifiable |
|---|---|---|---|
| resources/service-comparison-bi-tools.md | 17 | 1 | 1 (Tableau ownership, packaging) |
| resources/service-comparison-warehouses.md | 22 | 2 | 0 |
| resources/service-comparison-etl-orchestration.md | 24 | 5 | 0 |
| learn/concepts/backup-and-point-in-time-restore.md | 14 | 2 | 1 (warehouse time travel, already marked on page) |
| learn/concepts/warehouse-cost-control.md | 14 | 2 | 1 (auto-suspend setting names, already marked) |
| learn/concepts/time-series-and-graph-databases.md | 8 | 1 | 2 (rollup/retention features, default chunk size; both marked or not asserted) |
| learn/concepts/data-observability.md | 8 | 1 | 0 |
| learn/concepts/database-normalization-for-oltp.md | 9 | 0 | 0 |
| learn/concepts/feature-stores-for-data-engineers.md | 11 | 0 | 0 |
| resources/troubleshooting/kafka-consumer-lag.md | 20 | 4 | 1 (group state names) |
| resources/troubleshooting/replication-lag.md | 19 | 0 | 0 |
| resources/troubleshooting/deadlocks-and-lock-waits.md | 17 | 2 | 1 (`xml_deadlock_report` event name; removed) |
| resources/certification-roadmap-analytics-engineer.md | 6 | 2 | 1 (Core as Advanced prerequisite) |
| resources/certification-roadmap-data-analyst.md | 3 | 0 | 0 |
| resources/hands-on-projects/debezium-cdc-to-lakehouse.md | 6 | 1 (version note) | 0 |
| resources/hands-on-projects/iceberg-lakehouse-local.md | 5 | 0 | 0 |
| resources/hands-on-projects/airflow-or-dagster-pipeline.md | 3 | 0 | 0 |
| resources/hands-on-projects/backup-and-restore-drill.md | 5 | 0 | 0 |
| topics/streaming.md | 6 | 1 | 0 |
| topics/data-governance.md | 5 | 1 | 0 |
| topics/analytics-and-bi.md | 3 | 0 | 0 |
| learn/day-one/* (7 pages) | 12 | 0 | 0 |

Hands-on and day-one pages were run for real on 2026-11-07; only their vendor claims (versions, defaults, flags) were checked here, not the pasted outputs. The Dagster "open source" wording is counted under the ETL page.

## Fixes

| Page | Old claim | New claim | Source |
|---|---|---|---|
| bi-tools | Superset has "dozens of built-in chart types" | 40+ pre-installed visualization types | https://superset.apache.org/ |
| bi-tools | Tableau: "could only read the help index" | Help also names Prep Builder, Bridge, Mobile; ownership still unverified (marketing pages return 403) | https://help.tableau.com/current/online/en-us/to_signin.htm |
| warehouses | Multi-cluster warehouses add and remove clusters as load changes | True for Auto-scale mode only; Maximized mode runs all clusters | https://docs.snowflake.com/en/user-guide/warehouses-multicluster |
| warehouses | Redshift "stops supporting" Python UDFs after 2026-06-30 | Date has passed; "no longer supports", enforced in phases | https://docs.aws.amazon.com/redshift/latest/mgmt/welcome.html |
| etl | Dataflow described as serverless | Fully managed (the Dataflow overview does not use "serverless") | https://docs.cloud.google.com/dataflow/docs/overview |
| etl | Composer "hosts the web server" | Runs the Airflow web server UI (hosting location is not stated) | https://docs.cloud.google.com/composer/docs/composer-3/composer-overview |
| etl | dbt "hosted product" | dbt OSS engine plus the hosted dbt platform | https://docs.getdbt.com/docs/introduction |
| etl | Dagster "open-source orchestrator" | Docs link a public GitHub repo; page text does not say open source | https://dagster.io/docs |
| backup-pitr | Restore into "an empty data directory" | Remove old files from the cluster data directory, then put the base backup there | https://www.postgresql.org/docs/current/continuous-archiving.html |
| backup-pitr | SQLite "has no built-in log archiving" | SQLite WAL page describes recycling the WAL, not archiving | https://www.sqlite.org/wal.html |
| cost-control | BigQuery on-demand "only direct cost control is quotas" | Quotas plus a maximum bytes billed setting | https://docs.cloud.google.com/bigquery/docs/best-practices-costs |
| cost-control | Partition and cluster tables, select only needed columns (cost page) | Partitioning and column selection cited to the query-optimization page; clustering only in the LIMIT note | https://docs.cloud.google.com/bigquery/docs/best-practices-performance-compute |
| time-series-graph | Cypher `MATCH (p:Person)-[:LIKES]->(t:Technology) RETURN p, t` as Neo4j's example | The page's example is `MATCH (p:Person)-[r:LIKES]->(t:Technology) RETURN p,r,t` | https://neo4j.com/docs/getting-started/cypher/ |
| observability | Snowflake monitors, no edition stated | Requires Enterprise Edition | https://docs.snowflake.com/en/user-guide/data-quality-intro |
| kafka-lag | Group states listed as Stable, Empty, PreparingRebalance, CompletingRebalance, Dead | Only Stable is shown in the run and the docs page; list removed | https://kafka.apache.org/42/operations/basic-kafka-operations/ |
| kafka-lag | `--all-groups`, `--dry-run` presented as documented | Worked on the 4.3.1 broker; not listed on the operations docs page | https://kafka.apache.org/42/operations/basic-kafka-operations/ |
| kafka-lag | MSK lag metrics "at the right monitoring level" | `MaxOffsetLag`, `SumOffsetLag` are DEFAULT level; `OffsetLag` is `PER_TOPIC_PER_PARTITION` | https://docs.aws.amazon.com/msk/latest/developerguide/metrics-details.html |
| kafka-lag | Event Hubs "last enqueued sequence number" | Offset and checkpoint comparison; no lag metric named on the features page | https://learn.microsoft.com/en-us/azure/event-hubs/event-hubs-features |
| deadlocks | SQL Server `xml_deadlock_report` event | system_health session records deadlocks with the graph (event name not on the fetched page) | https://learn.microsoft.com/en-us/sql/relational-databases/extended-events/use-the-system-health-session |
| deadlocks | MySQL victim is the transaction that changed the fewest rows | InnoDB "tries to pick small transactions" by rows inserted, updated, deleted | https://dev.mysql.com/doc/refman/8.4/en/innodb-deadlock-detection.html |
| roadmap-analytics-eng | DP-600 uses "SQL, KQL and DAX" | SQL, DAX and PySpark | https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/dp-600 |
| roadmap-analytics-eng | SnowPro Core is a prerequisite for Advanced exams | Repo guides say so; Snowflake pages fetched do not; confirm before booking | https://learn.snowflake.com/en/certifications/snowpro-advanced-architect/ |
| debezium project | Tested on 3.6.3.Final, no currency note | Notes 3.7.1.Final is latest stable on 2026-11-07 | https://debezium.io/releases/ |
| topics/streaming | DP-700 covers "Real-Time Intelligence and Eventstreams" | Eventstreams and Spark structured streaming (study guide wording) | https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/dp-700 |
| topics/data-governance | Databricks DE Professional covers "lineage" | Unity Catalog permissions, masking and governance tags | https://www.databricks.com/learn/certification/data-engineer-professional |

## Still unverifiable

- Tableau ownership, editions and licensing (tableau.com returns 403 to fetchers).
- Whether Snowflake requires SnowPro Core before Advanced exams (not on any Snowflake page fetched).
- Warehouse time travel and snapshot features across platforms (stated as unchecked on the backup page).
- Auto-suspend setting names per platform (stated as unchecked on the cost page).
- TimescaleDB rollup, retention and time-bucket features, and the default chunk interval (the hypertables page names neither).
- Kafka consumer group state names beyond Stable, and `xml_deadlock_report` as the deadlock event name.
- Not run and not checked: Airflow mapping, Spark/Trino Iceberg readers, MySQL/SQL Server/managed-service commands beyond the doc-level checks above.

## Cert guides pass

A second pass over the four cert guides added on 2026-11-07 (GCP Associate Data Practitioner, Databricks Data Analyst Associate, Databricks Associate Developer for Apache Spark, MongoDB Associate Data Modeler): exam logistics re-fetched from the vendor pages and guides, every practice question's key and distractors judged against vendor docs, and note claims with a number, default, syntax or named behavior checked. Same classes as above. Counts are approximate groupings of checkable claims.

| Guide | Logistics verified | Logistics fixed | Bank (items) | Wrong keys | Note claims verified | Note claims fixed | Unverifiable (marked) |
|---|---|---|---|---|---|---|---|
| gcp/associate-data-practitioner | 7 of 8 (duration, count, price, languages, delivery, prerequisites, weights) | 1 (validity and retakes) | 28 | 0 (1 explanation fixed) | 46 | 6 | 4 |
| databricks/data-analyst-associate | 10 of 10 | 0 | 25 | 0 | 55 | 2 | 3 |
| databricks/apache-spark-developer-associate | 10 of 10 | 0 | 24 | 0 | 62 | 3 | 3 |
| mongodb/associate-data-modeler | 7 of 9 (validity and retakes not stated) | 1 (validity wording) | 25 | 0 | 48 | 3 | 5 |

No keyed answer was wrong and no distractor was accidentally correct in any of the 102 questions. One explanation stated an unsupported capability (GCP Q20).

### Fixes

| Page | Old | New | Source |
|---|---|---|---|
| gcp fact-sheet, README | Validity "see the Renewal FAQ" | Total validity not stated; FAQ: "all other foundational and associate" certifications renew by standard exam only, window opens 180 days before the inactive date, adds 3 years, renewable up to 30 days after; retake wait "varies by exam" | https://support.google.com/cloud-certification/answer/9907853 |
| gcp notes/03, bank Q20 | Dataflow job UI supports CPU and memory profiling | Job graph, execution details, stage progress and job metrics charts (data freshness, resource utilization, I/O); no profiling on the page | https://docs.cloud.google.com/dataflow/docs/guides/monitoring-overview |
| gcp notes/03 | Eventarc destinations include Cloud Run services and jobs, Workflows, Cloud Run functions | Standard: Cloud Run services, Cloud Run functions, Workflows, GKE, internal HTTP; Advanced adds Cloud Run jobs, Pub/Sub, buses | https://docs.cloud.google.com/eventarc/docs/overview |
| gcp notes/02 | Colab Enterprise "inside BigQuery Studio and Vertex-family tooling", not fetched | Colab Enterprise notebooks in BigQuery Studio; BigQuery DataFrames implements pandas and scikit-learn APIs over SQL | https://docs.cloud.google.com/bigquery/docs/notebooks-introduction |
| gcp notes/02 | Looker vs Looker Studio "general knowledge" | Sourced to Google's product post (Studio: no-cost, no centralized model; LookML: version-controlled semantic layer); docs intro page does not compare them | https://cloud.google.com/blog/products/data-analytics/looker-next-evolution-business-intelligence-data-studio |
| gcp notes/01 | Storage product mapping unchecked | Bigtable verified (time series, IoT, low latency, sparse sorted table); Firestore, Spanner, Cloud SQL lines marked unverified (pages truncated) | https://docs.cloud.google.com/bigtable/docs/overview |
| gcp notes/04 | Table expiration "confirm the setting names"; 90-day long-term storage "not fetched" | Default table expiration applies only to new tables; `time_partitioning_expiration` / `expirationMs` for partitions; 90 consecutive days per partition, reads do not reset it (search-returned docs text; the storage-overview page does not state the figure) | https://docs.cloud.google.com/bigquery/docs/best-practices-storage |
| databricks analyst notes/05, fact-sheet | Photon "always on for SQL warehouses and serverless" | "The default engine for all SQL warehouses" | https://docs.databricks.com/aws/en/compute/photon |
| databricks analyst notes/07 | "Trusted assets" described as equivalent to example SQL and functions, attributed to benchmarks page | Set-up page lists trusted assets without defining them; the reading is marked unverified | https://docs.databricks.com/aws/en/genie/set-up |
| spark notes/03 | `applyInPandasWithState` and `transformWithState` "family" from memory; HDFS state store default stated as fact | `transformWithState` encouraged since Spark 4.0, `mapGroupsWithState` / `flatMapGroupsWithState` legacy; PySpark names and state store default marked unverified | https://spark.apache.org/docs/latest/streaming/apis-on-dataframes-and-datasets.html#arbitrary-stateful-operations |
| spark notes/07 | `PandasUDFType` "is deprecated" | "Will be deprecated in a future release" | https://spark.apache.org/docs/latest/api/python/tutorial/sql/arrow_pandas.html |
| spark notes/07 | pandas API differences were "pointers to check" | Default index type is `distributed-sequence`; `sequence` uses a single partition; `compute.ops_on_diff_frames` exists but the options table (True) contradicts its prose (disallowed by default) | https://spark.apache.org/docs/latest/api/python/tutorial/pandas_on_spark/options.html |
| mongodb notes/04, fact-sheet | `validationLevel: constraint` listed with 8.x levels, "(newer)" | New in MongoDB 9.0; the 8.3.13 server used for the runs does not have it | https://www.mongodb.com/docs/manual/core/schema-validation/specify-validation-level/ |
| mongodb notes/05 | Profiler production advice "general practice" | Docs: profiler can degrade performance, worst at level 2 or low level-1 threshold; consider implications before production | https://www.mongodb.com/docs/manual/tutorial/manage-the-database-profiler/ |
| mongodb fact-sheet, README | Validity "not stated on the pages read" | Exam page re-read; Certification Program Guide is a gated download and unread, so validity and retake rules stay unverified | https://learn.mongodb.com/pages/mongodb-associate-data-modeler-exam |

### Verified without change (selection)

- GCP: 2 hours, 50-60 questions, USD 125, English and Japanese, no prerequisites, 6+ months recommended; section weights 30/27/18/25 and all four objective lists match the guide PDF; storage-class minimums, lifecycle actions and 24-hour delay, soft delete 7 days, Cloud SQL HA (about 60 seconds, synchronous, same IP, double cost, standby cannot read), backup retention 1 day to 10 years, scheduled queries (5-minute minimum, on-the-hour duplicates, `@run_time`), BigQuery subscription at-least-once, CSEK not a Cloud KMS feature, Analytics Hub now BigQuery sharing, BQML model types and Model Registry deployment.
- Databricks analyst: 45 scored plus up to 10 unscored, 90 minutes, USD 200, English, 2 years, 14-day retake at full fee, nine weights summing to 100; managed-table 7-day recovery, OpenSharing naming, 5 GB UI upload limit, `SET TAG` on DBR 16.1+, `APPLY TAG`, tag inheritance stops at columns, `BROWSE` for lineage, result cache 24 hours and UI cache 7 days, predictive optimization on managed tables only, `CLUSTER BY AUTO` requirements and four-key limit, Genie 50-object and 500-benchmark limits, feedback alone does not change behavior, alert states, Quartz cron, admin-created destinations. The `/explain` command is in the exam guide's own answer key.
- Spark: same logistics as the analyst exam (guide published 2026-10-08), seven weights summing to 100, Python only; defaults (200 shuffle partitions, 128 MB, 10 MB broadcast, AQE since 3.2.0, 64 MB advisory, skew factor 5 and 256 MB), JDK 17 and G1GC default since 4.0.0, Spark Connect (3.4, gRPC, Arrow, port 15002, no built-in authentication, `pyspark-client`), deploy modes, save modes, 10,000-row Arrow batches.
- MongoDB: 75 questions, 110 minutes, USD 150, English, online proctored, no prerequisites (exam page, read through a browser because the page is script-rendered); `errorAndLog` new in 8.1, 32-field compound limit, ESR and ERS wording, shard key factors including `reshardCollection` from 5.0, Archive, Bucket, Outlier, Inheritance and Schema Versioning page wording, Performance Advisor on M10 or higher.

### Still unverifiable

- GCP: total certification validity (the FAQ gives only the 3-year renewal extension; the Associate Cloud Engineer row is ambiguous); Firestore, Spanner and Cloud SQL one-line descriptions; the BigQuery 90-day long-term storage figure on a fetched docs page; Looker docs-side comparison page; Dataflow "exactly-once by default" and Data Fusion built on CDAP were not re-fetched.
- Databricks analyst: exact meaning of "trusted assets"; embedded links, external app integrations and channel apps for Genie (no docs page read); passing score (not published).
- Spark: PySpark stateful operator names and the default state store provider; whether serverless compute always uses Photon; Databricks serverless using Spark Connect connectivity; behavior of local runs (cache storage level, ANSI union errors) is as run, not documented.
- MongoDB: certification validity and retake rules (gated Certification Program Guide; forum reports of a 15-day wait are unofficial); domain weights and objective text come from the Exam Guide v2.0 read earlier today and sit behind a login viewer, so they were not re-fetched; gradual migration practice; the cardinality-selectivity link; Subset in sharded clusters (guide wording plus inference).
