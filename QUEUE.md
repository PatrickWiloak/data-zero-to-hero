# Queue

Content work the **queue loop** builds for this site (nobler-os `scripts/loops/queue-loop.mjs`, loop `queue-z2h-data`, docs/LOOPS.md "queue"). Items are written by the **content-gaps loop** (`content-gaps-z2h-data`, since 2026-11-13) from the gap docs, TODO.md content lines, Search Console and the vendors' cert pages, already `approved` (Patrick, 2026-11-13: "auto-build, I land"). Patrick may add, edit or delete items by hand too. The loop reads this file at `origin/main` only.

**A push to this repo publishes the site**, so nothing here lands on its own: each built item waits on a local branch for Patrick.

## Format

Each item is a `## Q-NNN: title` section with these fields, then `### Spec` and `### Done when`:

- `status`: `proposed` | `approved` (the loop may build it) | `in-progress` | `done` | `failed`. The loop builds `approved` items, oldest `added` first. It writes `done` + a `result` line on its branch; `failed` (with the reason) lives in its state file (`~/.cache/loops/queue/state-data-zero-to-hero.json`). To retry, change the spec or run `node ~/coding/nobler-os/scripts/loops/queue-loop.mjs --repo data-zero-to-hero --retry Q-NNN`.
- `added`: YYYY-MM-DD. `source`: where the item came from (gap doc, TODO line, search data, vendor page). `after` (optional): items that must be landed first.
- `allow`: the backticked paths the change may touch (`*` within a folder, `**` any depth), including generated files its generators rewrite. Anything else changed fails the item. Never QUEUE.md or gate configuration.
- `### Done when`: one backticked command per line, run by the loop in the worktree; every one must exit 0. Allowed commands start with `python3 .github/scripts/check-internal-links.py`, `python3 .github/scripts/check-readme-counts.py --check`, `python3 .github/scripts/build-certs-index.py --check`, `python3 .github/scripts/build-provider-indexes.py --check`, `python3 .github/scripts/build-flashcards.py --check`, `python3 .github/scripts/build-lab-map.py --check`, `python3 .github/scripts/check-code-fences.py`, `python3 .github/scripts/check-cert-freshness.py`, `python3 .github/scripts/balance-answer-positions.py --check`, `bash .github/scripts/validate-cert-structure.sh`, `bash .github/scripts/validate-frontmatter.sh`, `grep`, `test -f`, `test ! -f` - no `;`, `&`, `|`, `<`, `>`, `$`, backticks or backslashes. The repo's full local gate (`scripts/check-ci-local.mjs`) always runs after them, and an independent reviewer must approve the diff.

The builder may read vendor docs sites (WebFetch, vendor domains only) to check exam codes and facts.

## Landing a built item

The loop commits to a local `loop/queue-<id>` branch and never pushes; the branch expires after 14 days. Read it, then land it:

```bash
git -C ~/coding/data-zero-to-hero log -p main..loop/queue-q-001
git -C ~/coding/data-zero-to-hero merge --ff-only loop/queue-q-001
ship data-zero-to-hero --no-deploy        # the push deploys the site
```

## Q-001: Database migration guide - cutover runbook and rollback section

- status: approved
- added: 2026-11-16
- source: Search Console 28 days: /resources/migration-guides/database-migration, 908 impressions, position 19.9, 0 clicks (the page names rollback only in passing)
- allow: `resources/migration-guides/database-migration.md`

### Spec

In `resources/migration-guides/database-migration.md`, add a new `## Cutover Runbook and Rollback` section placed after `## Downtime Minimization Strategies` and before `## Data Validation and Integrity Checks`. It must contain: (1) a numbered cutover runbook with a time-boxed order of steps (freeze writes, drain CDC to zero lag, final row-count and checksum check, reset sequences and identity values on the target to max(id)+1, switch connection strings or DNS, smoke tests); (2) a "Rollback" subsection explaining reverse replication (target back to source) so the source stays current after cutover, and a go/no-go rule with a rollback deadline after which rolling back loses writes; (3) a short table of what to reset or re-point per target (sequences and identity columns, DNS TTL lowered ahead of time, connection pools, scheduled jobs, linked servers); (4) a worked example for a PostgreSQL target with a runnable `SELECT setval(...)` statement for resetting a sequence. Keep the existing house style (plain headings, bullet and table use as in neighbouring sections), do not delete existing text, and add a link to the section from any contents list if the page has one. Cite the AWS DMS and Azure DMS documentation pages already linked under "Key Documentation Links" rather than adding new vendor claims.

### Done when

- `python3 .github/scripts/check-internal-links.py`
- `python3 .github/scripts/check-code-fences.py`
- `bash .github/scripts/validate-frontmatter.sh`
- `grep -qF '## Cutover Runbook and Rollback' resources/migration-guides/database-migration.md`
- `grep -qF 'setval' resources/migration-guides/database-migration.md`
- `grep -qi 'reverse replication' resources/migration-guides/database-migration.md`

## Q-002: DP-300 notes - add the missing administration topics

- status: done
- added: 2026-11-16
- source: TODO.md line "2026-11-29: DP-300 notes still lack database watcher, SQL DB in Fabric, elastic jobs, Resource Governor, IQP, compression, log shipping"
- allow: `exams/azure/dp-300/notes/*.md`, `exams/azure/dp-300/README.md`, `exams/azure/dp-300/fact-sheet.md`
- result: 2026-11-18 loop/queue-q-002 (not pushed) - done-when + gate passed, reviewer approved: Seven sections, all in scope, match the current DP-300 skills measured and the Learn pages checked (database watcher ret

### Spec

Add short, exam-oriented sections to the existing DP-300 notes (do not create new files). Before writing, read the DP-300 skills-measured study guide at https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/dp-300 and the Azure SQL docs on learn.microsoft.com, and cover only what they support. Place each under the best-fitting note: `performance-monitoring-optimization.md` gets `### Intelligent Query Processing (IQP)` (what it is, which features, compatibility level requirement), `### Data Compression (Row and Page)` (when to use each, `sp_estimate_data_compression_savings`) and `### Resource Governor` (pools, workload groups, classifier function, applies to SQL Server and Managed Instance); `sql-deployment-management.md` gets `### Elastic Jobs` (agent, job database, targets, versus SQL Agent), `### Log Shipping` (primary, secondary, monitor, versus availability groups for HA/DR) and `### SQL Database in Microsoft Fabric` (what it is and how it differs from Azure SQL Database); `database-administration-overview.md` gets `### Database Watcher` (monitoring service, targets, data store, versus Query Store and Azure SQL Analytics). Each section needs a one-paragraph explanation, a comparison or decision rule, and one T-SQL or portal-step example where applicable. If the vendor page shows a topic is retired or not in the current skills measured, say so in one line instead of padding. Update the `last-updated` date in `exams/azure/dp-300/fact-sheet.md` only if its facts were re-checked against the vendor page.

### Done when

- `bash .github/scripts/validate-cert-structure.sh`
- `bash .github/scripts/validate-frontmatter.sh`
- `python3 .github/scripts/check-internal-links.py`
- `python3 .github/scripts/check-code-fences.py`
- `grep -qF 'Resource Governor' exams/azure/dp-300/notes/performance-monitoring-optimization.md`
- `grep -qF 'Elastic Jobs' exams/azure/dp-300/notes/sql-deployment-management.md`
- `grep -qF 'Log Shipping' exams/azure/dp-300/notes/sql-deployment-management.md`
- `grep -qF 'Database Watcher' exams/azure/dp-300/notes/database-administration-overview.md`
