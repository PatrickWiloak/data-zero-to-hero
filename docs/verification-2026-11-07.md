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
