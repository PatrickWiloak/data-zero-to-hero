---
last-updated: 2026-08-09
difficulty: advanced
---

# SnowPro Advanced: Administrator (ADA-C02) - Practice Questions

15 questions weighted toward security and access control (25-30%), account and organization management and resource management (20-25% each), then performance monitoring (15-20%) and data management and compliance (10-15%).

> **Cert page:** [exams/snowflake/snowpro-advanced-administrator/](../../exams/snowflake/snowpro-advanced-administrator/)

---

### Question 1
**Scenario:** A warehouse's credit consumption must be capped for a business unit.

A. A budget alert only, which emails the finance team when spending crosses a threshold but cannot suspend the warehouse
B. Suspend the warehouse by hand whenever the business unit gets close to its monthly credit budget
C. A resource monitor with notify, suspend and suspend-immediately triggers
D. Switch the warehouse to a smaller size and hope the business unit's credit use falls enough

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Resource monitors are the only enforcement mechanism: they act on a credit quota per interval and can notify at one threshold then suspend at another. Suspend lets running queries finish; suspend immediately kills them, which is a deliberate choice rather than a default.
</details>

---

### Question 2
**Scenario:** Which role can create other roles and manage account-level objects but should not be used day to day?

A. PUBLIC
B. SYSADMIN, which owns warehouses and databases and is the role that should create all roles and users
C. USERADMIN
D. ACCOUNTADMIN, which should be limited to a small number of users with MFA enforced

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** The recommended hierarchy grants object ownership under SYSADMIN and user and role administration under USERADMIN, with SECURITYADMIN managing grants. Reserving ACCOUNTADMIN for billing and account settings limits the damage any one compromised session can do.
</details>

---

### Question 3
**Scenario:** A query ran successfully a few hours ago but its results must be recovered without rerunning it.

A. `RESULT_SCAN` against the query ID, within the 24-hour result cache retention
B. Not possible
C. Fail-safe
D. Query the ACCOUNT_USAGE QUERY_HISTORY view, which returns the rows each statement produced for 14 days

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Snowflake keeps persisted query results for 24 hours (extended on reuse up to 31 days from first execution), and RESULT_SCAN reads them, serving them them with no warehouse running at all. Time Travel restores table state rather than query results, and Fail-safe is a Snowflake-managed recovery of last resort.
</details>

---

### Question 4
**Scenario:** A table was dropped an hour ago and must be recovered.

A. `UNDROP TABLE`, within the Time Travel retention period
B. Recreate the table from the original DDL and reload the data from the source system
C. Restore the table from the nightly backup that Snowflake takes of every database
D. Contact support for Fail-safe recovery, since dropped tables are only recoverable by Snowflake staff

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Time Travel defaults to one day and extends to 90 on Enterprise editions for permanent objects. Fail-safe adds seven days after Time Travel expires but is only accessible through Snowflake support, so it is a disaster mechanism rather than an operational one. Transient and temporary tables have no Fail-safe.
</details>

---

### Question 5
**Scenario:** Users must not be able to see sensitive column values unless they hold a specific role.

A. Move the sensitive columns into a separate table and rely on the schema name to hide it
B. A view for each role that selects only the permitted columns, with direct SELECT on the base table kept open
C. Revoke SELECT on the table from every role except the account administrators
D. A dynamic data masking policy on the column, keyed on CURRENT_ROLE

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Masking policies apply at query time wherever the column is referenced, including through views, which is what per-role view proliferation fails to guarantee. Row access policies are the row-level counterpart, and both can be attached to object tags for scaled application.
</details>

---

### Question 6
**Scenario:** A multi-cluster warehouse is configured with min 1 and max 5 clusters in auto-scale mode.

A. Additional clusters start under concurrency pressure and shut down when demand falls, billing only for running clusters
B. All five clusters run continuously
C. Queries fail past cluster one
D. It behaves like a larger warehouse

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Multi-cluster addresses concurrency, not single-query performance; a larger warehouse size addresses the latter. Maximized mode runs all clusters continuously, which is a deliberate choice for predictable heavy concurrency.
</details>

---

### Question 7
**Scenario:** Access to Snowflake must be restricted to a corporate network.

A. Private connectivity only, such as AWS PrivateLink, which does not by itself block logins from other public IP addresses
B. A network policy with allowed IP ranges, applied at account or user level, plus network rules for finer control
C. A firewall
D. MFA only

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Network policies are evaluated before authentication. Set them carefully, since an account-level policy that excludes your own address locks you out, and only a user with the right privilege from an allowed address can undo it. Private connectivity such as PrivateLink is the complementary control on the network path.
</details>

---

### Question 8
**Scenario:** Data must be shared with an external organization without copying it.

A. An external table
B. A secure share via Snowflake Secure Data Sharing, or a listing on the Marketplace, where the consumer pays their own compute
C. A replicated database
D. Export to S3

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Sharing grants read access to the provider's storage, so there is no copy to keep in sync and no storage cost for the consumer. Consumers on a different cloud or region require a reader account or database replication first.
</details>

---

### Question 9
**Scenario:** A query is slow and the Query Profile shows a high percentage of bytes spilled to remote storage.

A. Add a materialized view
B. The warehouse is too small for the operation's memory; increase its size or process less data
C. Add a clustering key on the join columns, then wait for automatic clustering to reorganize the micro-partitions
D. Increase the query timeout so the statement is allowed to run longer before Snowflake cancels it at the configured limit for the warehouse

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Spilling to local disk is a warning sign and spilling to remote storage is a serious one, because the operation no longer fits in memory. Look for exploding joins and unnecessary sorts before simply buying a larger warehouse.
</details>

---

### Question 10
**Scenario:** A large table's queries filter on a date column that does not match the natural load order.

A. An index
B. A materialized view over the whole table with no clustering key, refreshed in the background by Snowflake
C. A clustering key on the date column, letting automatic clustering reorganize micro-partitions
D. A larger warehouse

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Snowflake prunes micro-partitions by their min and max values, so clustering improves pruning for the chosen filter. Automatic clustering consumes credits continuously, so it earns its cost only on large tables queried often on that key.
</details>

---

### Question 11
**Scenario:** Account activity must be audited for the past year.

A. Not available
B. The query history page only
C. The `SNOWFLAKE.ACCOUNT_USAGE` schema, which retains up to one year with some latency, versus `INFORMATION_SCHEMA` for recent, low-latency data
D. Warehouse logs

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** The trade-off is the point: `ACCOUNT_USAGE` has up to 45 minutes of latency but long retention, while `INFORMATION_SCHEMA` is current but limited to between 7 days and 6 months depending on the view. Choosing the wrong one produces either stale answers or missing history.
</details>

---

### Question 12
**Scenario:** A database must be available in a second region for disaster recovery.

A. Database replication to a second region, with failover configured
B. A share
C. Manual export of the data to files, then import into an account in the second region
D. Time Travel, with the retention period extended to 90 days on important tables

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Replication keeps a read-only secondary current; failover promotes it to primary. Account-level replication also carries users, roles, and warehouses, which is what makes the secondary usable rather than just a copy of the data.
</details>

---

### Question 13
**Scenario:** Credit consumption must be attributed to teams.

A. Count the queries each team runs and split the monthly credit bill in proportion to those counts
B. Estimate each team's share from its headcount and divide the credit bill in proportion to team size
C. A single warehouse for everyone, with each team asked to report its own query usage in a shared spreadsheet at month end
D. Warehouses per team plus object tagging, reported through WAREHOUSE_METERING_HISTORY

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** The warehouse is the unit of compute billing, so warehouse boundaries define the attribution you can report on. Tags extend this to storage and to grouping warehouses under a cost center without creating more of them.
</details>

---

### Question 14
**Scenario:** A user's password authentication must be replaced with key-based authentication for a service account.

A. Rotate the password more often, using a scheduled ALTER USER statement to set a new password every thirty days across environments
B. Key pair authentication, with an RSA public key on the user and a second key slot for rotation
C. OAuth only
D. SSO through the corporate identity provider, with the service account signing in through the browser flow

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Service accounts cannot complete an interactive SSO or MFA flow, so key pairs are the standard. The `RSA_PUBLIC_KEY_2` slot is what makes rotation a zero-downtime operation rather than a coordinated outage.
</details>

---

### Question 15
**Scenario:** Storage costs are rising faster than data volume.

A. Run a manual compression job on the largest tables each weekend to shrink their micro-partitions
B. Delete the oldest data from every table, whatever the retention policy says, and then recheck costs
C. Investigate Time Travel retention, Fail-safe, and cloned or stale objects
D. Move to a smaller warehouse

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Zero-copy clones cost nothing at creation but accumulate storage as the two copies diverge. Long Time Travel retention on a high-churn table multiplies its storage, which is why retention should be set per object rather than globally.
</details>

---

## Where to go deeper

- [ADA-C02 cert page](../../exams/snowflake/snowpro-advanced-administrator/) - notes, practice plan, strategy
- [SnowPro Core practice questions](./snowflake-snowpro-core.md) - the prerequisite
- [SnowPro Advanced Architect practice questions](./snowflake-snowpro-advanced-architect.md) - the design counterpart
- [SQL vs NoSQL](../../learn/concepts/sql-vs-nosql.md) - plain-English data foundation
- **[📖 Snowflake certification](https://www.snowflake.com/certifications/)** - official exam guides
