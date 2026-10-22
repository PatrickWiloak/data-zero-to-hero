---
last-updated: 2026-10-22
difficulty: intermediate
---

# Azure Database Administrator Associate (DP-300) - Practice Questions

30 questions for DP-300 prep across all five domains: deployment, secure environment, monitoring and optimization, automation, and high availability and disaster recovery. Questions 16-30 are tagged by domain and spread in line with the official weights (skills measured as of October 27, 2026).

> **Cert page:** [exams/azure/dp-300/](../../exams/azure/dp-300/)

---

### Question 1
**Scenario:** An application uses SQL Server Agent jobs, cross-database queries, and CLR. It must move to a PaaS offering with minimal code change.

A. SQL Server on an Azure VM, which leaves patching and backups with the team
B. Azure SQL Managed Instance, the PaaS option with near-complete SQL Server compatibility
C. Azure SQL Database single database, which is PaaS and low-maintenance but does not offer SQL Agent or cross-database queries
D. Azure Database for PostgreSQL, a managed service that needs the schema and T-SQL code to be ported to a different engine

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Managed Instance is the PaaS option with near-complete SQL Server surface area, including SQL Agent, cross-database queries, CLR, Service Broker, and linked servers. Single database lacks Agent and cross-database queries. A VM would work but is IaaS, so you keep patching and backup duties.
</details>

---

### Question 2
**Scenario:** A single database must survive a regional outage with an RPO measured in seconds and a readable secondary.

A. Long-term retention of backups for up to ten years, which supports compliance but restores from backup rather than replicating
B. Active geo-replication or a failover group to a secondary region
C. Geo-redundant backups only, which copy backups to a paired region but give an RPO of an hour or more and no readable secondary
D. Zone-redundant configuration, which spreads replicas across datacenters in a region but does not protect against a regional outage

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Geo-replication maintains an asynchronous readable secondary in another region with an RPO of about 5 seconds. A failover group adds a listener endpoint so the connection string does not change on failover. Zone redundancy protects against a datacenter, not a region. Backups are recovery, not replication, and have a far larger RPO.
</details>

---

### Question 3
**Scenario:** A query that ran in 200 ms now takes 30 seconds after a statistics update, and the plan changed.

A. Increase the service tier to give the query more CPU, memory, and IOPS, on the assumption that the slow plan is a resource shortage
B. Use Query Store to find the regression and force the previously good plan
C. Rebuild all indexes in the database, which refreshes statistics as a side effect but does not restore the earlier plan on its own
D. Restart the server, which clears the plan cache but may produce the same slow plan again from the updated statistics

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Query Store keeps plan history and runtime stats per query, which is what makes a regression identifiable rather than a guess. Forcing the last known good plan stabilizes it immediately while you investigate. Automatic tuning can do this for you when `FORCE_LAST_GOOD_PLAN` is enabled.
</details>

---

### Question 4
**Scenario:** Column-level protection is required so that support staff see only the last four digits of a card number, without changing queries.

A. Always Encrypted
B. Row-Level Security
C. Transparent Data Encryption at rest on the database files
D. Dynamic Data Masking, using a partial() mask

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Dynamic Data Masking rewrites results at query time for unprivileged users while the data stays intact. Be clear about its limit: it is a presentation control, not a cryptographic one, and a user who can run arbitrary queries can often infer the value. Always Encrypted protects against the DBA and the platform but requires client-side key handling.
</details>

---

### Question 5
**Scenario:** Data must be unreadable to Microsoft operators and to the DBA, including in memory on the server.

A. Auditing, which records who accessed which data but does not stop a privileged user from reading plaintext values
B. Transparent data encryption, which encrypts data files and backups at rest but returns plaintext to anyone who can query
C. Always Encrypted with secure enclaves, keeping keys outside the engine so neither the DBA nor Microsoft operators can read plaintext data
D. Dynamic Data Masking on the sensitive columns, which hides values in results but leaves data readable to privileged users

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Always Encrypted keeps the keys client-side so the engine never sees plaintext, and secure enclaves extend that to allow richer operations such as range comparisons and in-place encryption. TDE encrypts data at rest only, so anyone querying the database sees plaintext.
</details>

---

### Question 6
**Scenario:** Users should only see rows belonging to their own department, enforced in the database.

A. Row-Level Security with a security predicate
B. A view for each department that filters on the department column, with users granted access to only their own view
C. Application filtering, where the app adds a department condition to every query before sending it to the database
D. Column encryption on the department-specific columns, which hides values but does not filter which rows a user can see

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** RLS attaches a predicate to the table so every query is filtered regardless of how it arrives, which closes the gap that per-department views and application-side filtering leave open when someone connects with another tool. Filter predicates control reads and block predicates control writes.
</details>

---

### Question 7
**Scenario:** A database must authenticate application access without storing a password.

A. Microsoft Entra ID authentication using a managed identity, with a contained database user for the app
B. SQL authentication with a strong random password kept in the app's secure configuration store
C. A shared service account used by all app instances, with credentials rotated every quarter
D. A certificate in the connection string, which replaces the password with a private key that must still be stored with the application

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** A managed identity means no credential exists to leak or rotate: Azure issues tokens to the resource itself, and you create a contained database user for that identity. Shared SQL logins spread across configuration files and lose per-application accountability.
</details>

---

### Question 8
**Scenario:** A workload has unpredictable, bursty usage with long idle periods, and cost must follow usage.

A. Business Critical, billed continuously whether or not the database is in use
B. Hyperscale, which suits very large databases with rapid scale-out but bills for provisioned compute while the database is idle
C. Serverless compute tier with auto-pause, which bills per vCore-second only while the database is active
D. Provisioned General Purpose, which gives a fixed number of vCores billed hourly, so cost does not follow usage during idle periods

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Serverless bills per vCore-second within a range and can auto-pause when idle, which matches intermittent workloads. The trade-off is a cold-start delay on the first connection after a pause. Business Critical targets low latency and high IOPS, and Hyperscale targets very large databases with fast restore.
</details>

---

### Question 9
**Scenario:** A 40 TB database needs near-instant restores and rapid scale-out of read replicas.

A. General Purpose
B. Managed Instance General Purpose, with remote storage
C. Hyperscale, with snapshot-based backups
D. Business Critical

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Hyperscale separates compute, log, and page servers, so backups are snapshot-based and restore time is largely independent of database size, and read replicas can be added quickly. General Purpose and Business Critical have much lower size ceilings and size-dependent restore times.
</details>

---

### Question 10
**Scenario:** You must prove who read a sensitive table over the last 90 days.

A. SQL auditing to Log Analytics or a storage account, recording the principal for each read with retention you control
B. Extended events on the client, collected from each application server, which record what the client sent but not what the database served
C. Dynamic Data Masking on the sensitive table, which hides values from users in results but does not record who read them or when
D. Query Store, which keeps query text and runtime stats for tuning but not which principal ran them

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Auditing records database events with the principal that performed them, and shipping it to Log Analytics gives retention and query. Query Store is a performance feature and does not retain identity. Pair auditing with Microsoft Defender for SQL if you also want anomaly alerting.
</details>

---

### Question 11
**Scenario:** A maintenance job must run nightly against an Azure SQL Database single database, which has no SQL Agent.

A. It cannot be automated, because a database without SQL Agent has no way to run a job on a schedule
B. A cron job on the server, which runs scripts on the database host that Azure SQL Database does not give customers access to
C. SQL Server Agent, enabled from the database settings in the portal, which schedules T-SQL job steps inside the single database
D. Elastic Jobs (or Azure Automation / Logic Apps) scheduled against the database

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Single databases have no Agent, so scheduling comes from outside: Elastic Jobs is the native option and can target groups of databases, while Automation runbooks or Logic Apps are common alternatives. Managed Instance is the deployment option that does include SQL Agent.
</details>

---

### Question 12
**Scenario:** Waits show high `PAGEIOLATCH_SH` and the storage is at its IOPS ceiling.

A. Increase MAXDOP so queries use more parallel threads, which spreads the CPU work but leaves storage as the limit
B. Enable Query Store to capture plan history, which helps diagnose regressions but does nothing about waiting on storage
C. Add indexes to reduce reads, and scale the tier or move to a tier with higher IOPS, since the waits show storage is the bottleneck here
D. Rebuild statistics for the busiest tables, which can improve plan choice but does not raise the storage IOPS ceiling

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** That wait type means sessions are waiting on data pages to come from storage, so the fix is reading less (better indexes, fewer scans) or having faster storage. Raising MAXDOP adds parallelism to a problem that is I/O bound and can make contention worse.
</details>

---

### Question 13
**Scenario:** A deleted table must be recovered from four days ago on a database with default settings.

A. Restore the transaction log manually to a point four days ago on the live database, then cancel the delete
B. Undo the delete with a T-SQL command that reverses the last committed transaction on the table
C. Use geo-replication failover to the secondary, which holds a copy from before the delete
D. Point-in-time restore to a new database from a time before the deletion, within the retention window, then copy the table back into the original

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Point-in-time restore always creates a new database rather than overwriting the original, so you restore then extract what you need. The default retention is 7 days and can be raised, which is why checking retention before you need it matters. Failover does not travel back in time; the deletion replicated too.
</details>

---

### Question 14
**Scenario:** Connections from the app tier must not traverse the public internet.

A. TLS 1.2 enforced as the minimum, which encrypts traffic but does not change its path
B. A private endpoint for the logical server, with public network access disabled on the server
C. Encrypting the connection string, which protects the secret but not the network path
D. A firewall rule allowing the app's public IP, which restricts who can connect but still uses the public internet path

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** A private endpoint gives the server a private IP in your VNet, and disabling public network access closes the internet-facing path completely. Firewall rules still use the public endpoint. Note that with a private endpoint you should use `Proxy` or verify redirect behavior for connection policy.
</details>

---

### Question 15
**Scenario:** Several databases with varying, uncorrelated load should share a cost-efficient resource pool.

A. An elastic pool shared by the databases
B. One large single database sized for the combined load
C. Serverless for each
D. Separate provisioned databases, each sized at its own peak

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Elastic pools let many databases share a pool of DTUs or vCores, which works precisely because their peaks do not coincide. Sizing every database for its own peak wastes most of the capacity most of the time. Serverless per database is an alternative but pays per-database overhead.
</details>

---

### Question 16 - Domain 1: Plan and implement data platform resources
**Scenario:** A team already runs its analytics in Microsoft Fabric. It needs a new operational SQL database for a small internal app, and the data must land in OneLake for Power BI reports without building or running an ETL pipeline. Nobody on the team wants to manage servers or networking.

A. Azure SQL Managed Instance, with a Data Factory pipeline that copies changed rows into OneLake every hour
B. SQL Server on an Azure VM, with a scheduled export job that writes Parquet files to a lakehouse folder
C. SQL database in Microsoft Fabric, which replicates its data into OneLake automatically in near real time
D. Azure SQL Database Hyperscale, with a nightly BACPAC export that an analyst loads into the lakehouse

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** SQL database in Fabric is a SaaS database built on the Azure SQL Database engine. Its data is replicated into OneLake automatically, so Power BI and other Fabric workloads can read it with no pipeline to build or run. A needs a pipeline to run and maintain, plus a Managed Instance, which is far more than a small app needs. B brings back server management and still needs an export job. D gives day-old data and a manual load step, and a BACPAC is a deployment artifact, not a feed into the lake.
</details>

---

### Question 17 - Domain 1: Plan and implement data platform resources
**Scenario:** A 3 TB orders table on SQL Server on an Azure VM is partitioned by month. Almost all reads and writes hit the last three months. Partitions older than two years are read only once a year, for audit. You must cut storage without slowing the busy recent partitions.

A. Rebuild only the older partitions with PAGE compression and leave the recent partitions as they are
B. Apply PAGE compression to the whole table so every partition, including the hot ones, uses less space
C. Turn on backup compression for the database so the full and log backups take less storage
D. Move the database files to Premium SSD v2 disks so the larger table is cheaper per GB to store

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Data compression can be set per partition with `ALTER TABLE ... REBUILD PARTITION = n WITH (DATA_COMPRESSION = PAGE)`. Cold partitions get the biggest saving and their extra CPU cost on read barely matters. Run `sp_estimate_data_compression_savings` first to check the gain. B also saves space but adds CPU cost to every insert and update on the hot partitions, which the scenario rules out. C shrinks backups only, not the data files. D changes the disk type, not the amount of data, and Premium SSD v2 is a performance choice, not a storage-saving one.
</details>

---

### Question 18 - Domain 1: Plan and implement data platform resources
**Scenario:** A 4 TB database on SQL Server 2022 on-premises must move to Azure SQL Managed Instance. The business allows less than five minutes of downtime. The team also wants to run read-only tests against the Azure copy for a week before cutover.

A. Export a BACPAC from the source and import it into the Managed Instance during a weekend window
B. Take a full backup to Azure Blob Storage, then restore it once on the Managed Instance and repoint the app
C. Use Azure Data Factory to copy each table into an empty database on the Managed Instance, then switch over
D. Set up the Managed Instance link, test against the readable replica, then fail over when ready

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** The Managed Instance link uses distributed availability group technology to replicate the database continuously into the Managed Instance. The replica is readable, so you can test there while the source stays live, and cutover is a short planned failover. A is an offline method, and a 4 TB BACPAC export and import takes hours. B is also offline: changes made after the backup are lost unless you take the downtime for the whole copy and restore. C copies data but not logins, jobs or most schema objects, and it still needs a long freeze to stay consistent.
</details>

---

### Question 19 - Domain 2: Implement a secure environment
**Scenario:** A finance database in Azure SQL Database holds a payments audit trail. Auditors want cryptographic proof that no row has been changed or deleted, even by a user with db_owner, and new rows must only ever be added.

A. Turn on SQL auditing to a storage account and give the auditors read access to the audit logs
B. Store the data in an append-only ledger table and keep database digests in immutable storage
C. Enable temporal tables so every change to a row is kept in a history table the auditors can query
D. Apply Row-Level Security with a block predicate that stops all updates and deletes on the table

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Ledger hashes each transaction into a chain, and an append-only ledger table blocks updates and deletes at the engine level. Storing digests in immutable Azure Blob Storage means anyone can later verify that the data was not tampered with. A records who did what, but a privileged user could still alter the data, and the log itself is not proof of integrity. C keeps history, but a db_owner can turn system versioning off and edit both tables. D is a permission control that a db_owner can drop, so it offers no cryptographic proof.
</details>

---

### Question 20 - Domain 2: Implement a secure environment
**Scenario:** Security policy says your organization, not Microsoft, must control the key that encrypts an Azure SQL Database at rest. The security team must be able to make the database unreadable by revoking that key.

A. Use Always Encrypted on every column, with the column master key stored in the Windows certificate store
B. Keep the default service-managed TDE and restrict the portal so only the security team can see the server
C. Enable Dynamic Data Masking on all sensitive columns and grant UNMASK only to the security team
D. Configure TDE with a customer-managed key held in Azure Key Vault, using a managed identity for access

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** TDE with a customer-managed key (bring your own key) wraps the database encryption key with a key you own in Key Vault or Managed HSM. If you revoke the server's access to it, the database becomes inaccessible. A protects selected columns from the engine, not the whole database at rest, and a local certificate store does not give central revocation. B leaves Microsoft in control of the key, which breaks the policy. C changes what users see in results and does nothing for data at rest.
</details>

---

### Question 21 - Domain 2: Implement a secure environment
**Scenario:** A support team of eight people needs to read DMVs in an Azure SQL Database to troubleshoot performance. They must not read any table data. People join and leave the team often.

A. Create a contained user for their Microsoft Entra group and grant it VIEW DATABASE STATE
B. Create a SQL login for each person and add each one to the db_datareader role in the database
C. Make the Entra group the server's Microsoft Entra admin so its members can see all server state
D. Add the Entra group to the db_owner role so it can read DMVs and fix issues it finds on the spot

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** One user mapped to an Entra group means joiners and leavers are handled in Entra, not in the database. VIEW DATABASE STATE (or the narrower VIEW DATABASE PERFORMANCE STATE) gives access to the DMVs without any SELECT on tables, which is least privilege. B grants exactly the table reads the team must not have, and individual SQL logins add password management. C and D both grant full control, which goes far beyond reading diagnostics.
</details>

---

### Question 22 - Domain 3: Monitor, configure, and optimize database resources
**Scenario:** You run 40 Azure SQL databases and two managed instances. You want one place with detailed, near real-time performance data such as waits, query runtime and resource use, kept for weeks and queryable with KQL, without installing agents or VMs.

A. Enable Query Store on every database and compare the reports in SSMS one database at a time
B. Create an Extended Events session on each database that writes event files to a storage account
C. Deploy database watcher and send its data to an Azure Data Explorer cluster or Fabric eventhouse
D. Rely on the built-in Azure Monitor platform metrics, such as CPU and DTU percent, on each resource

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Database watcher is the agentless Azure service that collects detailed monitoring data from many Azure SQL databases and managed instances into a single store. You query it with KQL and use its built-in dashboards. A gives per-database query history but no central view and no wait or resource data across the estate. B captures raw events, but you would have to build the collection, parsing and dashboards yourself. D gives coarse resource metrics only, not waits or query detail.
</details>

---

### Question 23 - Domain 3: Monitor, configure, and optimize database resources
**Scenario:** On SQL Server on an Azure VM, a reporting tool connects with its own login and runs heavy queries during business hours. Those queries starve the order-entry app of CPU. The reports must still run, just with less CPU.

A. Set the server-wide max degree of parallelism to 1 so no single query can use more than one core
B. Use Resource Governor to classify the reporting login into a workload group in a CPU-capped pool
C. Scale the VM to a larger size so both workloads have enough CPU during the busy hours
D. Add Query Store hints to each report query so their plans run with fewer threads than before

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Resource Governor uses a classifier function to send sessions from the reporting login to a workload group. That group's resource pool has a CAP_CPU_PERCENT or MAX_CPU_PERCENT limit, so reports keep running but cannot take over the CPU. A hits every workload, including the order-entry app, and can make other queries slower. C spends money and the reports can still grow to fill the new cores. D means tuning each query by hand, misses new reports, and does not set a CPU limit.
</details>

---

### Question 24 - Domain 3: Monitor, configure, and optimize database resources
**Scenario:** A stored procedure looks up orders by customer. A few customers have millions of orders and most have a handful. Performance swings between fast and very slow depending on which customer's plan got cached first. The database is at compatibility level 130 on Azure SQL Database.

A. Clear the procedure cache every hour so a new plan is compiled more often than it is now
B. Update statistics on the orders table with FULLSCAN so the optimizer sees the real data skew
C. Use Query Store to force the plan built for the largest customer so every call uses that plan
D. Raise the compatibility level to 160 to enable parameter sensitive plan optimization

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** This is parameter sensitivity, and parameter sensitive plan (PSP) optimization is an intelligent query processing feature at compatibility level 160. It caches several plan variants for one statement and picks one based on the parameter value. Test the level change first, because it also turns on other optimizer changes. A swaps a random plan for another random plan and adds compile cost. B gives better histograms, but one cached plan still has to serve both large and small customers. C makes small-customer calls pay for a plan built for millions of rows.
</details>

---

### Question 25 - Domain 4: Configure and manage automation of tasks
**Scenario:** On Azure SQL Managed Instance, a nightly SQL Server Agent job sometimes fails, and nobody finds out until the morning. The DBA team wants an email as soon as the job fails.

A. Set up Database Mail with the AzureManagedInstance_dbmail_profile profile, add an operator, and set the job to notify it on failure
B. Add a final job step that runs `xp_cmdshell` to send an email through the team's SMTP relay
C. Create an Azure Monitor alert on the instance's CPU metric so that a failed job shows up as a CPU drop
D. Ask the team to check the job history in SSMS each evening before they leave for the day

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** SQL Agent on Managed Instance sends notifications through Database Mail, and it uses the profile named AzureManagedInstance_dbmail_profile. With an operator defined, the job's notification setting emails on failure. B fails because xp_cmdshell is not supported on Managed Instance, and a final step would not run on every failure path anyway. C watches an indirect signal that will miss many failures. D is manual and still leaves the gap the team wants to close.
</details>

---

### Question 26 - Domain 4: Configure and manage automation of tasks
**Scenario:** The same Azure SQL server, databases, firewall settings and auditing must be deployed to dev, test and prod. Deployments must be repeatable, and reviewers want to see exactly what will change in prod before anything is applied.

A. Build prod by hand in the portal, then export the resource group template and keep it as documentation
B. Write a PowerShell script that calls New-AzSqlDatabase and related cmdlets for each environment in turn
C. Define the resources in a Bicep file with parameter files per environment and run what-if before deploying
D. Write an Azure CLI script that runs az sql db create for each database and stops if any command fails

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Bicep is declarative and idempotent, so the same file reaches the same end state in every environment, and parameter files hold the differences. The what-if operation shows the planned creates, changes and deletes before deployment, which is the review step asked for. A is not repeatable, and exported templates often need cleanup before they deploy cleanly. B and D are imperative scripts: they work, but they do not preview changes, and re-running them needs extra logic to handle resources that already exist.
</details>

---

### Question 27 - Domain 4: Configure and manage automation of tasks
**Scenario:** An elastic job runs nightly index maintenance against a target group that covers a whole logical server. The job agent authenticates with a user-assigned managed identity. Two databases created last week fail with login errors, while all older databases succeed.

A. Restart the elastic job agent so it picks up the new databases on its next scheduled run
B. Create a database user for the job agent's managed identity in each new database and grant it the rights the job needs
C. Raise the job step's retry count and retry interval so the new databases have time to come online
D. Move the job to SQL Server Agent, since elastic jobs cannot reach databases added after the job was created

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** A server-level target group finds new databases on its own, but the job still has to log in to each one. The identity needs a user in master to list databases, and a user with the right permissions in every target database. The new databases were never given that user. A and C do not fix a missing user, so the logins keep failing. D is wrong twice: Azure SQL Database has no SQL Server Agent, and elastic jobs do pick up databases added to a server target.
</details>

---

### Question 28 - Domain 5: Plan and configure a high availability and disaster recovery (HA/DR) environment
**Scenario:** SQL Server on an Azure VM hosts several databases. You need a low-cost standby in another region with an RPO of about 15 minutes. You also want a window to recover from a bad change: a mistaken mass delete should not reach the standby for at least an hour.

A. An Always On availability group with an asynchronous replica in the other region
B. Active geo-replication from the VM's databases to an Azure SQL Database in the other region
C. Zone-redundant disks on the primary VM so the data survives the loss of one datacenter
D. Log shipping to a VM in the other region, with a one-hour restore delay on the secondary

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Log shipping backs up the log on a schedule, copies it, and restores it on the secondary. Its restore delay setting holds back changes, which gives you time to stop a bad delete before it lands on the standby. A 15-minute backup schedule meets the RPO. A replicates changes within seconds, so the mass delete reaches the replica almost at once. B does not exist for SQL Server on a VM, because active geo-replication is an Azure SQL Database feature. C protects within one region only and keeps no delayed copy.
</details>

---

### Question 29 - Domain 5: Plan and configure a high availability and disaster recovery (HA/DR) environment
**Scenario:** A legacy app on SQL Server on Azure VMs needs automatic failover within one region. The whole instance must fail over as a unit, including logins, Agent jobs and linked servers. The team wants one copy of the data on shared storage, not a copy per node.

A. An Always On Failover Cluster Instance on two VMs using Azure shared disks or a Premium file share
B. An Always On availability group with two synchronous replicas across availability zones in the region
C. Log shipping between two VMs, with a script that brings the secondary online and repoints the app
D. Azure Site Recovery replicating the VM to another region and failing over on demand

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** A Failover Cluster Instance protects the whole instance, so server-level objects move with it, and the nodes share one copy of the data. On Azure that storage is Azure shared disks, Premium file shares or Storage Spaces Direct. B keeps a separate copy of the data on each replica, and a standard AG covers user databases, so instance-level objects need extra syncing or a contained AG. C is not automatic and keeps a second copy. D is a cross-region DR tool with a slower recovery time, not in-region automatic failover.
</details>

---

### Question 30 - Domain 5: Plan and configure a high availability and disaster recovery (HA/DR) environment
**Scenario:** An Azure SQL Database uses a failover group to a second region. The quarterly DR test must prove the app runs from the secondary region, and it must not lose any committed transactions.

A. Run a forced failover from the secondary, since it promotes the secondary at once whatever its sync state
B. Run a planned failover from the secondary, which syncs fully before it switches roles, then fail back the same way
C. Restore a geo-backup of the database in the secondary region and point a copy of the app at it
D. Delete the failover group, then create it again with the old secondary set as the new primary

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** A planned (friendly) failover waits until the secondary is fully synchronized before switching roles, so no committed data is lost. The read-write listener moves with it, so the app follows without a connection string change. A is for a real outage: it can lose the transactions not yet replicated. C tests backup recovery, not the failover group, and a geo-backup can be an hour or more behind. D drops the replication relationship and breaks the listener the app depends on, so it is not a test of the real setup.
</details>

---

## Where to go deeper

- [DP-300 cert page](../../exams/azure/dp-300/) - notes, practice plan, strategy
- [DP-900 practice questions](./azure-data-fundamentals-dp-900.md) - the fundamentals below this
- [SQL vs NoSQL](../../learn/concepts/sql-vs-nosql.md) - choosing a data store
- [Databases topic index](../../topics/databases.md) - cross-cloud comparisons
- **[📖 DP-300 study guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/dp-300)** - official skills outline
