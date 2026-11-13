# TODO

Working task list for **data-zero-to-hero** (Data Zero to Hero, https://data.noblerworks.com/). Read this at the start of a work session and keep it current as work completes - check items off with a date, add follow-ups as they surface. History lives in git and CHANGELOG.md.

---

## ✍️ Content refill (loop content-refill; nobler-os docs/LOOPS.md)
- [ ] 2026-11-15 Pat: from 11-15 the `queue-z2h-data` loop builds content items (gaps, deepening, new certs) from [QUEUE.md](QUEUE.md) on local `loop/queue-q-NNN` branches; read and land each per QUEUE.md "Landing a built item" (expires 14 days).
- [ ] 2026-11-15 Pat: first `content-z2h-data` run (Sundays, pilot since 2026-11-13) drafts into `drafts/`, never built; publish or delete each per [drafts/README.md](drafts/README.md).

## Split (2026-10-21)

- [x] 2026-10-21 Split from cloud-data-ai-security-zero-to-hero: data scope, cyan accent, new IndexNow key, Data from Scratch path, family cross-links (CHANGELOG 2026-10-21).
- [x] 2026-10-27 live at data.noblerworks.com. Was: Not deployed: push to `main` deploys via docs-site.yml once the GitHub repo and Pages exist. Pages custom domain `data.noblerworks.com` + DNS CNAME are outside this repo.
- [x] 2026-10-27 topics + description set. After first deploy: `python3 .github/scripts/check-repo-metadata.py --fix` (description + topics; needs admin), then `--check` passes in CI.
- [x] 2026-10-27 social preview uploaded (`assets/brand/social-preview-1280x640.png`; GitHub reports a custom image).
- [ ] After first deploy: `https://data.noblerworks.com/<key>.txt` serves the key in `.github/site/indexnow-key.txt`; the deploy's "Notify IndexNow" step says `accepted` (a first-run 403 is normal; rerun `notify-indexnow.py --all`).
- [x] 2026-11-09 submitted `https://data.noblerworks.com/sitemap.xml` to Search Console via API.
- [x] 2026-10-21 Glossary and YouTube page rewritten data-only (CHANGELOG 2026-10-21 (2)).

## Content (2026-10-21 expansion)

- [x] 2026-11-07 Cross-site learning paths (7 routes, `learn/learning-paths.md`, byte-identical in all four repos, linked from which-site, learn hub and home). Not pushed or deployed: ships with the train.
- [ ] 2026-12-07 re-verify every link in `learn/learning-paths.md` against each repo's `git ls-files` (script: loop the URLs through the four trees) and the matching certs against current exam codes.
- [ ] Judge 2026-11-21: rerun `build-dbt-project.md` on the current dbt release; if dbt v2 is the pip default by then, retest and update the pinned `dbt-core<2`. ✏️ 2026-11-06 early read: dbt-core is 1.12.6, so the `<2` pin holds; guide not rerun yet.
- [x] 2026-11-06 lychee (#7, 11-02): 0 real 404s; 2 dead hosts removed. The rest are cloud.google.com refusing the runner.
- [x] 2026-11-07 "Check yourself" sections (3-4 questions, collapsed answers) added to all 29 `learn/concepts/` pages; strict build renders the `<details>` blocks. Not pushed. New concept pages should get one too.
- [ ] Next concept candidates (backup/PITR done 2026-11-07): Query plans, Spark shuffles and skew, and semantic layers now have long-form pages under `resources/troubleshooting/` and `resources/architecture-patterns/`; a 5-minute concept page each would still help.)
- [x] 2026-10-22 Resources expansion: 4 patterns, 2 interview guides, 3 troubleshooting guides, warehouse migration guide (CHANGELOG 2026-10-22 (3)).
- [ ] Judge 2026-12-22: re-verify version-sensitive facts in the 2026-10-22 pages - Debezium stable line (3.6), Lakeflow Connect sources, Fabric mirroring preview sources, Google "Lakehouse" (ex-BigLake) naming, Census-in-Fivetran branding.
- [x] 2026-10-22 Postmortems (GitLab 2017, Atlassian 2022) and analytics engineer + DBA interview guides added (CHANGELOG 2026-10-22 (4)).
- [x] ✅ 2026-11-06 dba-interview's link to the security site's Snowflake case study returns 200.

## Patrick

- [ ] **Decide** whether the bio should carry a cert count again. If yes, add a `{{personal_certs}}` token in `build-site.py` fed from one checked-in value.
- [x] ✅ 2026-11-06: 2026-11-05: Database Specialty lapses - `README.md` (top quote and About the Author) and `learn/README.md` bio go from 19x / 14x AWS / 64 to **18x / 13x / 63** (source `go-to-market/claims.md`).
- [ ] Distribution: one useful page each to r/dataengineering and r/SQL, and awesome-data-engineering, linking back.

## Exam currency (from the 2026-10-16 vendor sweep, data exams only)

Confidence in brackets: H = vendor page read, M = vendor page plus search, L = third-party.

- [ ] 2026-11-22: Pat (form wants your details): download the COF-C03 / ADA-C02 / DEA-C02 study-guide PDFs from https://learn.snowflake.com/en/certifications/ into `exams/snowflake/`; then Claude remaps the topic sections to objective level.
- [ ] 2026-11-29: DP-300 notes still lack database watcher, SQL DB in Fabric, elastic jobs, Resource Governor, IQP, compression, log shipping.
- [x] 2026-10-22 Databricks DE Professional rebuilt for the 2026-10-09 outline: 10 section notes, README, fact sheet, strategy, plan; bank fully retagged. Page says 10 sections / 59 items, guide PDF 9 / 60.
- [x] 2026-10-22 Databricks DE Associate rebuilt for the May 2026 7-section guide: 7 section notes, README, fact sheet, strategy, plan; bank retagged (no Section 5 question yet).
- [ ] Judge 2027-01-15: recheck the DE Associate exam page and guide for a revision; recheck ABAC DENY policies (Beta) and `SET MANAGED` facts in notes/07.
- [ ] DP-203 is retired: decide whether its guide, practice bank and `labs-by-cert` entry stay as reference or go.
- [ ] "Vertex AI" still appears in `exams/gcp/data-engineer` (renamed Gemini Enterprise Agent Platform 2026-04-22). Sweep with Google's rename table once names settle.
- [x] 2026-11-07 MongoDB Associate Data Modeler guide built from the exam page and Exam Guide v2.0 (validity not stated on any page read).
- [ ] 2026-11-22: Confluent Cloud Certified Operator (CCAC; 90 min, USD 150, 2-year validity per training.confluent.io, read 2026-11-07) not built: the exam guide (course 1159) is a SCORM behind a free training-portal login, so question count and domain weights are unread. Enroll, read it, then build `exams/confluent/cloud-operator`. [M]
- [ ] Snowflake SPS-C01 retires 2026-11-01 and SEA-C01 / DAA-C01 2026-12-01 [M]; no guides here.

## Tooling (shared with the sibling sites)

- [x] 2026-11-10 Freshness workflow on main; dispatched run 42795033183 green: 79 pages, 8 over 120 days (all `last-updated: 2026-05-03`, 191 d), 17 undated READMEs, none near the 365-day fail.
- [ ] 2026-12-15 Re-verify the 8 fact-sheets (DP-900, Confluent x2, GCP x2, MongoDB x3) (2026-05-03) on vendor pages, bump `last-updated` (365-d fail: 2027-05-03); date or exempt the 17 undated READMEs. `python3 .github/scripts/check-freshness.py --days 120`.
- [ ] 🟡 `.github/scripts/site_hooks.py` has drifted from ai-zero-to-hero's port - one canonical module the sites vendor (with a sync check), or record the fork. S-M, low.
- [ ] 🟡 `glossary-add-anchors.py`, `glossary-autolink.py`, `glossary-upgrade-existing-links.py` are a documented manual pipeline (`.github/AUTOMATION.md`, CONTRIBUTING); delete only if that pipeline is retired.
- [x] 2026-11-07 Heading-anchor validation folded into `check-internal-links.py` (640 anchors, 0 broken; ids match the strict build's on all 463 comparable pages).
- [x] 2026-11-07 `check-code-fences.py` added (0 unclosed fences); both run in `structure-validate.yml`, blocking.
- [x] 2026-11-07 Port `check-code-fences.py` and the anchor mode of `check-internal-links.py` to cloud-zero-to-hero and security-zero-to-hero (check each repo's slugify setting and emoji pinning first); not edited from this session. ✅ 2026-11-08: already ported; cloud 3258 links, security 2688, data 2042, 0 broken.
- [x] 2026-11-06 MD060 is already off in `.markdownlint.json`.
- [ ] Judge after the first Monday link-check run on this repo: it opens or updates one `link-check` issue (not a new one each week) and lists no real 404s.

## Content gaps (2026-11-07 review)

Plan: [docs/content-gaps-2026-11-07.md](docs/content-gaps-2026-11-07.md). Cert items are already listed above.

- [x] 2026-11-07 Wave 1: `learn/day-one/` on-ramp (7 pages, samples run; the DuckDB CLI, JupyterLab/VS Code UIs and `pd.read_parquet` were not run - stated on the pages).
- [x] 2026-11-07 Wave 2: service comparisons (warehouses, ETL/orchestration, BI tools) + data analyst and analytics engineer roadmaps. Unverified items are marked on the pages (Tableau, Redshift/Databricks/Snowflake pricing models).
- [ ] Judge 2026-12-22: re-read the vendor pages behind the Wave 2 comparisons (Quick Sight rename, Fabric vs Synapse, Fabric Data Factory, Composer "Managed Airflow Gen 3" naming, Redshift Python UDF end of support).
- [x] 2026-11-07 Wave 3: concept pages - backup/PITR, data observability, warehouse cost control, OLTP normalization, time-series + graph DBs, feature stores. Left: query plans, Spark shuffles/skew, semantic layers 5-minute pages.
- [x] 2026-11-07 Wave 4: 4 projects (Debezium CDC to Iceberg, Dagster, local Iceberg, backup-restore drill), 3 troubleshooting guides (replication lag, deadlocks, Kafka consumer lag), 3 topic indexes (streaming, analytics and BI, governance). All run in Docker or uv venvs; what was NOT run is stated on each page (Airflow mapping, Dagster UI clicks, MySQL/SQL Server/managed-service equivalents, Spark/Trino Iceberg readers, lock-order fix).
- [ ] Verification pass done ([ledger](docs/verification-2026-11-07.md)); Tableau ownership and tiers settled. Left: Tableau prices; Snowflake Core prerequisite only in a 2023 blog (check at registration).
- [ ] Wave 4 left: a streaming or warehouse postmortem from primary sources (the 2 existing postmortems are databases).
- [ ] Judge 2026-12-22: rerun the Wave 4 projects on current releases (Debezium 3.6.x, PyIceberg 0.12, Dagster 1.13, `apache/kafka:latest` is unpinned in the CDC and consumer-lag pages) and re-check the PyIceberg `expire_snapshots` file-cleanup note.
- [x] 2026-11-07: flashcard decks added for 4 Snowflake guides + associate-atlas-administrator. Left: none; dp-203 is retired (reference-only), so no deck by design.
- [x] 2026-11-07 Wave 5: Google Associate Data Practitioner guide added (exam page and guide v1.0 read 2026-11-07; validity not stated on the page; BigQuery ML SQL and the Looker vs Looker Studio comparison not run or checked on a docs page).
- [ ] Judge 2027-01-15: recheck the Associate Data Practitioner exam page for a fee, length or guide revision, and the Renewal FAQ for its validity period.
- [x] 2026-11-07 Wave 5: Databricks Data Analyst Associate guide added (exam page and August 2026 guide read; unverified: Genie embed/channel-app options, the Genie Code slash command names, the exact "trusted assets" label; Unity Catalog and dashboard syntax not run).
- [ ] Judge 2027-01-15: recheck the Data Analyst Associate exam page and guide for a revision (the guide's update table shows terminology changes in Oct 2026).
- [x] 2026-11-07 Wave 5: Databricks Associate Developer for Apache Spark guide added (exam page and guide read; PySpark examples run locally on 4.3.0; not run or not checked on a docs page: JDBC, Spark Connect, stateful UDF API names, Databricks-specific Spark Connect behaviour, pandas-on-Spark behaviour details).
- [ ] Judge 2027-01-15: recheck the Spark Developer exam page and guide for a revision, and rerun the note examples on the then-current PySpark.
- [ ] 2026-11-21 MongoDB Associate Data Modeler: validity and retake rules are not published on the exam page; read the Certification Program Guide (gated download on learn.mongodb.com) and fill `exams/mongodb/associate-data-modeler/fact-sheet.md`. Also Google Associate Data Practitioner has no stated total validity (renewal FAQ only); recheck.
