---
last-updated: 2026-10-22
difficulty: intermediate
reading-time: 28 min
---

# DBA Interview Guide: What Gets Tested and Worked Answers

Database administrator interviews test whether you can be trusted with the system of record. The questions sound technical (indexes, isolation levels, replication), but underneath almost every one is the same concern: **will this person keep the data safe, keep it available, and make careful decisions under pressure?** This guide covers what each area tests, works through example answers the way a strong candidate would talk through them, and lists the traps that cost people offers.

Examples use PostgreSQL by default, with SQL Server, MySQL and managed cloud equivalents noted where they differ. Most interviewers care more about the reasoning than the product, so say which engine you are describing and how the answer would change on another. For the SQL coding round, see the [SQL interview guide](./sql-interview-guide.md).

---

## What the modern DBA role looks like

The job has shifted. Fewer DBAs install software on physical servers; more run managed databases (Amazon RDS and Aurora, Azure SQL, Cloud SQL and AlloyDB) alongside some self-managed systems, and work closely with developers. Interviews reflect that mix:

| Area | What interviewers test |
|---|---|
| Backup and recovery | RPO and RTO, full plus log backups, point-in-time recovery, and proving restores work |
| High availability and DR | Replication modes, failover, split brain, cross-region designs |
| Performance | Reading query plans, indexing, statistics, finding the top resource consumers |
| Concurrency | Transactions, isolation levels, locking, deadlocks, MVCC |
| Maintenance | Vacuum or index maintenance, bloat, statistics, upgrades |
| Change management | Schema changes on large live tables, migrations, rollback plans |
| Security | Least privilege, authentication, encryption, auditing |
| Operations | Monitoring, alerting, capacity planning, incident response |

A senior loop usually includes a troubleshooting scenario ("the database is slow, go") and a design question ("design the database tier for this system").

---

## 1. Backup and recovery

This is the most important section. Interviewers often start here because a DBA who gets backups wrong can lose the company's data, whatever else they know.

### Worked example

**Question:** "Design the backup strategy for a 2 TB PostgreSQL OLTP database. The business says it can lose at most 5 minutes of data and must be back within an hour."

**A strong answer:**

*"Those two numbers are the RPO, 5 minutes, and the RTO, one hour. I'd design for both and then prove them."*

*"For **RPO**, nightly dumps aren't enough; I need continuous protection. In PostgreSQL that means **physical base backups plus continuous WAL archiving**: a base backup, say daily, and every WAL segment shipped to object storage as it's completed, with `archive_timeout` set so a segment is shipped at least every minute even on a quiet system. To recover, you restore the base backup and replay WAL up to any point in time. Tools like pgBackRest or Barman manage this. On a managed service such as RDS, automated backups do the equivalent within the retention window."*

*"For **RTO**, the question is how long restoring 2 TB and replaying a day of WAL takes. That depends on storage throughput and WAL volume, so I'd measure it rather than guess. If restore from backup can't meet an hour, I'd rely on a **standby replica** for failover, which covers hardware and zone failures in minutes, and keep backups for the cases a replica can't handle."*

*"That distinction matters: a replica protects against losing a server, but it faithfully copies a bad `DELETE` or a corrupt write. So backups are for logical mistakes and disasters, replicas are for availability. I'd want both."*

*"Then the parts people skip:"*

- *"**Copies outside the blast radius**: backups in a different account or region, with immutability or object lock, so a compromised admin or ransomware can't delete them."*
- *"**Monitoring the output**: alert if the newest base backup is older than about 26 hours or the newest archived WAL is more than a few minutes old. Not just 'the job exited zero'."*
- *"**Automated restore tests**: restore to a scratch instance weekly, run sanity checks, record the time taken. That's how I'd know the RTO is real."*
- *"**Retention** that matches the business: how far back might someone discover a mistake? Often 30 days of PITR plus monthly long-term copies."*

### Traps

- **Calling replication a backup.** This is the single most common way to fail this question.
- **Only logical dumps** (`pg_dump`, `mysqldump`) for a large OLTP database. They are useful for migrations and table-level restores, but restore slowly and give only a point-in-time snapshot of when they ran.
- **Never mentioning restore testing.** The [GitLab 2017 postmortem](../postmortem-gitlab-database-2017.md) is the standard story of backups that had silently failed for an unknown time.
- **For SQL Server**: forgetting that log backups require the **full** (or bulk-logged) recovery model, and that in full recovery without log backups the transaction log grows until the disk fills.

**[📖 PostgreSQL: Continuous archiving and point-in-time recovery](https://www.postgresql.org/docs/current/continuous-archiving.html)** - base backups, WAL archiving and recovery targets
**[📖 SQL Server: Recovery models](https://learn.microsoft.com/en-us/sql/relational-databases/backup-restore/recovery-models-sql-server)** - simple, full and bulk-logged, and what each allows
**[📖 Amazon RDS: Point-in-time recovery](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_PIT.html)** - how managed PITR works and that it restores to a new instance

---

## 2. High availability and disaster recovery

### Worked example

**Question:** "What's the difference between synchronous and asynchronous replication, and which would you use?"

**A strong answer:**

*"With **asynchronous** replication the primary commits and returns to the client without waiting for the standby. It's fast, but if the primary dies, transactions that hadn't reached the standby are lost, so the RPO is 'whatever the replication lag was'. With **synchronous** replication the primary waits until at least one standby confirms it has the transaction before acknowledging the commit, so a committed transaction survives losing the primary. The cost is latency on every commit, and if the synchronous standby is unavailable, commits can stall unless you have more than one candidate."*

*"A common design is synchronous to a standby in another availability zone in the same region, where the round trip is a millisecond or two, and asynchronous to a standby in another region for disaster recovery, where synchronous latency would hurt every transaction. Managed services package this: Multi-AZ deployments on RDS, zone-redundant configurations on Azure SQL, regional high availability on Cloud SQL."*

**Follow-up:** "What is split brain?" *"Two nodes both believe they're the primary and accept writes, so the data diverges. It happens when a failover system can't tell the difference between 'the primary is dead' and 'I can't reach the primary'. Prevention is fencing (making sure the old primary can't accept writes, by cutting its access or shutting it down) and quorum-based decisions, which is why HA managers like Patroni use a consensus store and why you need an odd number of voters."*

### Traps

- **Assuming failover is free.** Clients need to reconnect, connection strings or DNS must point at the new primary, and in-flight transactions fail.
- **Never testing failover.** A failover path that has never run in production will surprise you.
- **Ignoring replication lag** when reading from replicas. A user writes, then reads from a lagging replica, and their change seems to have vanished. See [Eventual consistency](../../learn/concepts/eventual-consistency.md).

**[📖 PostgreSQL: High availability, load balancing and replication](https://www.postgresql.org/docs/current/warm-standby.html)** - log shipping and streaming replication, synchronous and asynchronous

See [Replication and sharding](../../learn/concepts/replication-and-sharding.md).

---

## 3. Performance troubleshooting

### Worked example

**Question:** "Users say the application got slow this morning. Nothing was deployed. Walk me through what you'd do."

**A strong answer works from wide to narrow:**

1. **Confirm and scope.** *"Is it everything or one feature? When did it start? I'd check the monitoring dashboards: CPU, memory, disk I/O and latency, connections, replication lag, lock waits. A sudden change at a specific time is a clue."*
2. **What is running now?** *"In PostgreSQL, `pg_stat_activity` shows active sessions, their queries, how long they've run and what they're waiting on. A long-running transaction, a batch job that started early, or a pile of sessions waiting on one lock each point somewhere different."*
3. **What has used the most resources?** *"`pg_stat_statements` ranks queries by total time, calls and mean time. If one query's mean time jumped, that's my suspect. On SQL Server, Query Store does the same and keeps plan history."*
4. **Why is that query slow?** *"Run `EXPLAIN (ANALYZE, BUFFERS)` on it, on a replica if it's heavy, and look for where estimated rows differ wildly from actual rows, sequential scans on large tables, and nested loops over large row counts."*
5. **Common causes when nothing was deployed:**
   - *"**Plan change** after statistics were updated or data grew past a threshold, so the optimiser picked a different join or stopped using an index."*
   - *"**Stale statistics** after a big data load, so estimates are wrong. `ANALYZE` the table."*
   - *"**Parameter-sensitive plans**: on SQL Server, a plan compiled for an unusual parameter value gets reused for typical ones (parameter sniffing)."*
   - *"**Blocking**: one long transaction holding locks."*
   - *"**Resource exhaustion**: disk nearly full, I/O credits exhausted on burstable cloud storage, connection limits reached."*
   - *"**Bloat** or autovacuum falling behind on a hot table."*
6. **Fix safely and follow up.** *"The immediate fix might be `ANALYZE`, killing a runaway query, or adding an index concurrently. Then the follow-up: why did it happen, and what alert would have warned us earlier?"*

### Indexing questions you will get

**"Why isn't my index being used?"** Good reasons to list:

- The query applies a **function or cast** to the column (`WHERE lower(email) = ...`, or comparing a text column to a number), so the plain index can't be used. Fix the query, or create an expression index.
- The **leading column** of a composite index isn't in the filter. An index on `(customer_id, created_at)` helps `WHERE customer_id = ?` and `WHERE customer_id = ? AND created_at > ?`, but not `WHERE created_at > ?` alone.
- The filter matches **a large share of the table**, so a sequential scan really is cheaper.
- **Statistics are stale**, so the optimiser thinks the filter matches more rows than it does.
- A **leading wildcard** (`LIKE '%abc'`) can't use a B-tree index.

**"What does a covering index do?"** It contains every column the query needs (in PostgreSQL and SQL Server via `INCLUDE` columns), so the engine can answer from the index alone without visiting the table. Faster reads, at the cost of a larger index and slower writes.

**"Why not index every column?"** Every index slows inserts, updates and deletes, uses storage and memory, and has to be maintained. Index for the queries you actually run, and drop unused indexes (PostgreSQL's `pg_stat_user_indexes` shows scan counts).

**[📖 PostgreSQL: Using EXPLAIN](https://www.postgresql.org/docs/current/using-explain.html)** - reading plans, costs and actual row counts
**[📖 PostgreSQL: pg_stat_statements](https://www.postgresql.org/docs/current/pgstatstatements.html)** - finding the queries that use the most time

See [Indexes explained](../../learn/concepts/indexes-explained.md) and [Slow SQL query diagnosis](../troubleshooting/slow-sql-query-diagnosis.md).

---

## 4. Concurrency: transactions, isolation and locking

### Worked example: isolation levels

**Question:** "Explain isolation levels and which one you'd use."

**A strong answer explains the anomalies first, then the levels:**

*"Isolation levels decide which anomalies concurrent transactions can see:"*

- *"**Dirty read**: reading another transaction's uncommitted change."*
- *"**Non-repeatable read**: reading the same row twice and getting different values because someone committed in between."*
- *"**Phantom read**: re-running a query and getting new rows that someone inserted."*
- *"**Serialization anomalies** such as write skew: two transactions each read, decide based on what they read, and write, and the combined result is something no serial order could produce."*

*"The SQL standard defines Read Uncommitted, Read Committed, Repeatable Read and Serializable. Engines implement them differently, which is worth saying out loud. PostgreSQL's default is **Read Committed**; its Repeatable Read is snapshot isolation, which in PostgreSQL also prevents phantoms; and its Serializable uses serializable snapshot isolation, which detects dangerous patterns and aborts one transaction with a serialization failure that the application must retry. SQL Server's default is also Read Committed, implemented with locks unless Read Committed Snapshot Isolation is turned on."*

*"I'd use Read Committed for most OLTP work, and Serializable for logic where correctness depends on what was read, such as 'only book the room if no overlapping booking exists', with retry logic in the application. Alternatively, a unique or exclusion constraint can enforce that rule in the database itself, which is often simpler."*

### Worked example: deadlocks

**Question:** "What is a deadlock and how do you prevent them?"

**A strong answer:**

*"Transaction A locks row 1 and wants row 2; transaction B has locked row 2 and wants row 1. Neither can proceed. Databases detect the cycle and abort one transaction, the deadlock victim, with an error. So deadlocks aren't hangs, they're errors the application has to handle by retrying."*

*"Prevention: access rows and tables in a **consistent order** across the codebase; keep transactions **short** and never wait for user input or a slow external call inside one; make sure the rows being updated are found via an **index**, so the engine locks only those rows rather than scanning and locking more; and for hot rows like counters, rethink the design. To investigate, PostgreSQL logs deadlock details (and `log_lock_waits` logs long lock waits); SQL Server captures deadlock graphs in the system_health Extended Events session."*

### Traps

- **Reciting the standard's definitions** without saying how your engine actually behaves.
- **Saying Serializable "locks everything"**; in snapshot-based engines it doesn't, but it can abort transactions that must be retried.
- **Treating deadlocks as a database bug** rather than an access-pattern problem.

**[📖 PostgreSQL: Transaction isolation](https://www.postgresql.org/docs/current/transaction-iso.html)** - the levels as PostgreSQL implements them, with examples
**[📖 PostgreSQL: Explicit locking](https://www.postgresql.org/docs/current/explicit-locking.html)** - lock modes, row locks and deadlocks

See [Transactions and ACID](../../learn/concepts/transactions-and-acid.md).

---

## 5. Maintenance: MVCC, vacuum and bloat

### Worked example

**Question:** "A PostgreSQL table is 10 times bigger on disk than its data should need, and queries on it keep getting slower. What's going on?"

**A strong answer:**

*"PostgreSQL uses MVCC: an `UPDATE` writes a new version of the row and marks the old one dead, and a `DELETE` marks rows dead. Dead row versions stay until **VACUUM** removes them and makes the space reusable. If vacuum falls behind, the table and its indexes fill with dead tuples, which is bloat: more pages to read for the same data."*

*"Common reasons autovacuum falls behind:"*

- *"A **long-running transaction**, or an abandoned session left 'idle in transaction', or a stale replication slot. Vacuum can't remove row versions that some transaction might still need to see, so one old transaction holds back cleanup for the whole database. I'd look at `pg_stat_activity` for old `xact_start` values and at `pg_replication_slots`."*
- *"Autovacuum settings too conservative for a hot table. The default triggers vacuum after a fraction of the table changes; for a huge, frequently updated table, per-table settings with a lower threshold help."*
- *"Very high update rates on a few rows."*

*"To fix: end the blocking transaction, let vacuum catch up, and tune per-table autovacuum. Plain `VACUUM` makes space reusable but rarely shrinks the file; `VACUUM FULL` rewrites the table but takes an exclusive lock, so for a live table I'd use an online tool such as pg_repack instead. I'd also mention **transaction ID wraparound**: PostgreSQL must 'freeze' old rows periodically, and if vacuum can't keep up for long enough, the database will eventually refuse writes to protect itself. Monitoring the age of the oldest transaction ID is a standard alert."*

### Traps

- **Recommending `VACUUM FULL` on a busy production table** without mentioning the lock.
- **Disabling autovacuum** to "improve performance". It just defers the problem and risks wraparound.
- **On SQL Server**, the equivalent discussion is index fragmentation, page splits, fill factor and statistics updates; know which engine you are being asked about.

**[📖 PostgreSQL: Routine vacuuming](https://www.postgresql.org/docs/current/routine-vacuuming.html)** - why vacuum exists, autovacuum, and preventing transaction ID wraparound

---

## 6. Schema changes on large live tables

### Worked example

**Question:** "You need to add an index and a new column to a 500-million-row table that serves live traffic. How do you do it without downtime?"

**A strong answer:**

*"The risk is locking. Many DDL operations take a lock that blocks reads or writes for as long as they run, and on 500 million rows that could be a long time. Also, even a fast DDL statement waits in the lock queue behind any long-running query, and everything queued behind it waits too, so I'd set a short `lock_timeout` and retry rather than letting it block the table."*

*"For the **index** in PostgreSQL, `CREATE INDEX CONCURRENTLY` builds it without blocking writes. It's slower, can't run inside a transaction, and if it fails it leaves an invalid index I need to drop and retry. SQL Server Enterprise has `ONLINE = ON`; MySQL's InnoDB supports online DDL for many index operations."*

*"For the **column**: in PostgreSQL 11 and later, adding a column with a constant default is a metadata-only change, so it's fast. Adding a column and then backfilling it with computed values is the slow part. I'd backfill in small batches (say 10,000 rows per transaction, keyed on the primary key) with pauses, watching replication lag, rather than one giant `UPDATE` that generates huge WAL, bloats the table and holds locks."*

*"For adding a `NOT NULL` constraint after the backfill, I'd add a `CHECK (col IS NOT NULL) NOT VALID` constraint, then `VALIDATE CONSTRAINT` separately, which checks existing rows without blocking writes."*

*"And the process around it: test on a production-sized copy, schedule it in a quiet period, have a rollback plan, and use the **expand and contract** pattern for anything that changes how the application reads data: add the new structure, deploy code that writes both, backfill, switch reads, then remove the old structure in a later release."*

### Traps

- **Running a plain `CREATE INDEX`** or a single huge `UPDATE` on a hot table.
- **Forgetting the lock queue**: a quick `ALTER TABLE` waiting behind a long report blocks every query behind it.
- **No rollback plan** and no test on realistic data volume.

See the [database migration guide](../migration-guides/database-migration.md).

---

## 7. Security

**Question:** "How do you secure a production database?"

A strong answer covers layers, briefly, then offers to go deeper on any:

- **Network**: no public endpoint; reachable only from application subnets or through a private endpoint; TLS required for connections.
- **Authentication**: no shared accounts; individual identities for humans, ideally federated through the identity provider (IAM database authentication on RDS, Microsoft Entra ID for Azure SQL); separate credentials per application with secrets in a secrets manager, rotated.
- **Authorisation**: least privilege through roles. Applications get only the rights they need (often no DDL); humans get read-only by default with elevated access granted temporarily and logged. Revoke default broad grants (for example on the `public` schema in older PostgreSQL versions).
- **Encryption**: at rest (storage encryption with managed keys) and in transit (TLS); column-level encryption or masking for the most sensitive fields.
- **Auditing**: log logins, privilege changes and access to sensitive tables (pgAudit, SQL Server Audit), shipped somewhere the DBA cannot alter.
- **Backups are data too**: encrypted, access-controlled, and copied to a separate account.
- **Patching**: a schedule for minor version updates, and a plan for major upgrades before end of support.

For the security side of data platforms, the [Snowflake customer breaches case study](https://security.noblerworks.com/resources/case-study-snowflake-customer-breaches-2024/) on the security site shows what password-only logins without network restrictions cost.

---

## 8. Design question: the database tier for a new system

**Question:** "Design the database tier for an e-commerce platform with 50,000 orders a day, growing fast, with a 99.95% availability target."

A strong answer walks through:

1. **Workload.** Read-heavy catalogue browsing, write-heavy checkout, reporting. Ask about data volume, growth, peak traffic, latency targets, regions and compliance.
2. **Engine choice.** A relational database for orders and payments (transactions, constraints); a cache for sessions and hot catalogue reads; possibly a search engine for product search; reporting moved off the primary to a replica or a warehouse via [change data capture](../../learn/concepts/change-data-capture.md). See [SQL vs NoSQL](../../learn/concepts/sql-vs-nosql.md) and [OLTP vs OLAP](../../learn/concepts/oltp-vs-olap.md).
3. **Availability.** A managed primary with a synchronous standby in a second zone and automatic failover; read replicas for read scaling; application retry logic for failover.
4. **Recovery.** PITR with a retention window that matches how long mistakes go unnoticed; cross-region backup copies; a warm standby in another region if the business needs regional DR; measured restore times.
5. **Scaling path.** Vertical scaling first, then read replicas, connection pooling, caching, and only then partitioning or sharding by a key such as customer ID, with the trade-offs that brings (cross-shard queries, rebalancing).
6. **Operations.** Monitoring and alerts (replication lag, connections, storage, slow queries, backup age), runbooks, and who is on call.

The interviewer is checking that you ask about requirements first, separate HA from DR, mention backups and restore testing without being prompted, and don't reach for sharding before simpler options.

---

## Behavioural questions DBAs get

**"Tell me about a time you made a mistake in production."** Interviewers want honesty, a fast and calm response, clear communication, and what you changed afterwards (a guard rail, a runbook, an automated check). Blaming someone else is the wrong answer; so is claiming you have never made one.

**"A developer wants direct write access to production to fix data. What do you do?"** Understand the urgency, offer a safe path (a reviewed script, run by you or through a change process, inside a transaction with a `SELECT` of the affected rows first), and keep the audit trail. Saying "no" without an alternative is a weak answer.

**"How do you work with development teams?"** Reviewing schema changes and query patterns early, providing self-service tools (query insights, safe migration tooling), and teaching rather than gatekeeping.

---

## Common traps across the whole interview

- **Replication is not backup.** Worth repeating, because it comes up in several rounds.
- **"It depends" with nothing after it.** Say what it depends on and pick an answer for a stated assumption.
- **Engine confusion.** Mixing PostgreSQL, SQL Server and MySQL behaviour without saying which you mean.
- **Heroics over process.** Interviewers prefer the candidate who tests the restore every week to the one with a dramatic story about saving the day.
- **Ignoring the human side**: communication during incidents, change approval, working with developers.

---

## Practise like this

1. Set up a local PostgreSQL, take a base backup with WAL archiving, delete a table, and restore to the moment before you deleted it. Time it. You will remember this far better than reading about it.
2. Create a table with a few million rows, run `EXPLAIN ANALYZE` on queries with and without suitable indexes, and watch the plan change.
3. Open two `psql` sessions and reproduce a non-repeatable read at Read Committed, then show it disappears at Repeatable Read. Then create a deadlock deliberately and read the error.
4. Update a large table repeatedly, watch it bloat, and watch autovacuum clean it up; then hold a transaction open in another session and see vacuum stall.
5. Read two real postmortems: [GitLab 2017](../postmortem-gitlab-database-2017.md) and [Atlassian 2022](../postmortem-atlassian-site-deletion-2022.md). Be ready to explain what you would have done differently.

---

## Related

- [SQL interview guide](./sql-interview-guide.md) - the SQL coding round
- [Data engineer interview prep](./data-engineer-interview.md) - the broader data engineering interview
- [Analytics engineer interview guide](./analytics-engineer-interview.md) - the modelling and dbt interview
- [Database certification roadmap](../certification-roadmap-database-specialist.md)
- [Database services compared](../service-comparison-databases.md) - AWS, Azure and GCP side by side
- [Indexes explained](../../learn/concepts/indexes-explained.md), [Transactions and ACID](../../learn/concepts/transactions-and-acid.md), [Replication and sharding](../../learn/concepts/replication-and-sharding.md)
- Certs: [Azure DP-300](../../exams/azure/dp-300/), [GCP Professional Cloud Database Engineer](../../exams/gcp/cloud-database-engineer/), [MongoDB Associate DBA](../../exams/mongodb/associate-dba/)
