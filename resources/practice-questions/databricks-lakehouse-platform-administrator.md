---
last-updated: 2026-08-09
difficulty: intermediate
---

# Databricks Data + AI Platform Administrator Accreditation - Practice Questions

15 questions across workspace administration, identity and access, data management, cluster and warehouse management, and security. Databricks publishes no domain weights for this accreditation (the real assessment is 25 unproctored questions); the spread here is this guide's choice.

> **Cert page:** [exams/databricks/lakehouse-platform-administrator/](../../exams/databricks/lakehouse-platform-administrator/)

---

### Question 1
**Scenario:** Users, groups, and service principals must be managed once across all workspaces.

A. Shared accounts for each team, so there are fewer identities to maintain
B. Account-level identity federation, with SCIM provisioning from the IdP and groups assigned to workspaces
C. Per-workspace user lists maintained by each workspace admin
D. Manual creation of each user in every workspace they need, with the admin removing them again when they leave the team

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Account-level identities plus SCIM mean joiners and leavers are handled by the IdP, which is what makes deprovisioning reliable. Per-workspace user management guarantees that someone who left last quarter still has access somewhere.
</details>

---

### Question 2
**Scenario:** Data governance must span workspaces with a single permission model.

A. Unity Catalog, with a metastore per region and a three-level `catalog.schema.table` namespace
B. Cloud storage IAM only, with bucket policies per team, since the files hold the data and so are the place to govern it
C. Notebook permissions on each workspace, so that only people who can open a notebook can run the queries against its tables
D. Table ACLs per workspace on the legacy Hive metastore, with the same grants repeated in each workspace by its admin

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Unity Catalog sits above workspaces, so one grant applies everywhere the catalog is attached, and it adds lineage and audit. Storage IAM cannot express table, column, or row scope, and the legacy Hive metastore is per-workspace by design.
</details>

---

### Question 3
**Scenario:** Unity Catalog must access cloud storage without embedded credentials.

A. Access keys stored in the cluster configuration as Spark properties
B. Per-user credentials entered by each engineer in their notebook
C. A storage credential (managed identity or IAM role) plus an external location defining the path scope
D. Public buckets with read access open to the workspace's network range, so that no credentials are needed for the paths

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** The storage credential holds the cloud identity and the external location binds it to a path prefix, so grants are made on the location rather than distributing keys. Cluster-level keys give everyone on the cluster the same access, which defeats fine-grained governance.
</details>

---

### Question 4
**Scenario:** Costs must be attributed to teams.

A. Cluster policies enforcing tags, plus system tables and budget alerts for per-team reporting
B. Estimate from headcount, with each team's share in proportion to its size
C. One shared cluster with no tags, so usage appears in a single line
D. Split evenly across the teams, because the cost difference per team is small

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Tags must be enforced at creation or coverage decays, which is exactly what a cluster policy does by requiring or fixing tag values. System tables then provide the usage data to report on, and budgets alert before the invoice arrives.
</details>

---

### Question 5
**Scenario:** Users keep creating oversized clusters.

A. Ask them not to, with guidance in the team wiki about sensible sizes for each type of workload and a monthly reminder
B. Remove cluster creation entirely, so that all compute has to be requested from the platform team through a ticket
C. Cluster policies limiting instance types, node counts and autotermination, with autoscaling on
D. Delete oversized clusters manually each morning once the platform team has listed them from the clusters page

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Policies set guardrails rather than removing capability, so users keep self-service within bounds you defined. Enforcing autotermination is usually the single biggest saving, because idle clusters are the largest avoidable cost in most workspaces.
</details>

---

### Question 6
**Scenario:** SQL analysts need low-latency queries with automatic scaling.

A. A SQL warehouse, ideally serverless, with autostop and scaling
B. An all-purpose cluster with a large driver and autoscaling workers, shared by all the analysts through the JDBC endpoint
C. A job cluster started for each query by the BI tool, which gives each analyst a fresh and isolated environment
D. A single node cluster with a high-memory instance type, which avoids shuffle and so keeps the query latency low

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** SQL warehouses are optimized for concurrent short queries with result caching, and serverless removes start-up latency. All-purpose clusters are for notebook development and behave badly as a shared BI backend.
</details>

---

### Question 7
**Scenario:** Notebook results containing sensitive data must not be visible to all workspace users.

A. Delete notebooks as soon as they have been run, so no results remain stored
B. Use a single admin account to run every sensitive notebook, so that nobody else has a reason to open the results afterwards
C. Trust users to avoid opening results they do not need, with a written policy reminding them of their responsibilities every quarter
D. Restrict workspace object permissions, apply Unity Catalog grants to the data, and consider disabling result downloads

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Two layers matter: who can open the notebook, and who can read the data it queries. Getting only the first right leaves the data reachable from any other notebook, which is why data-layer grants are the durable control.
</details>

---

### Question 8
**Scenario:** An audit must show who queried a sensitive table last month.

A. Unity Catalog audit logs and system tables, which record query and access events with the principal
B. Cluster logs, which keep a record of every query and the user who submitted it, for as long as the cluster exists
C. Notebook revision history, which shows which user ran each command and which tables were read at that time
D. Driver logs from each cluster, which list the SQL statements executed together with the identity of the person

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Audit logs record the identity, action, and object at the governance layer, independent of which compute ran it. Cluster and driver logs show execution detail without the governed object identity, which is not what an access audit needs.
</details>

---

### Question 9
**Scenario:** A workspace must not be reachable from the public internet.

A. Disable the web UI for all users, so that nobody can open the workspace from a browser anywhere on the internet
B. A firewall on each user's laptop that blocks the workspace unless on VPN
C. Strong passwords with mandatory MFA on the public endpoint
D. Private connectivity to the control plane, IP access lists, and no public IPs on compute

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Network isolation covers three paths: how users reach the workspace, how the control plane reaches compute, and how compute reaches storage and external services. Address all three, since leaving any one public undermines the other two.
</details>

---

### Question 10
**Scenario:** A table's data must be deleted to satisfy a data subject request.

A. Overwrite the file that holds the row with new content, which replaces the old bytes and so removes the personal data
B. Drop the table, which removes the metadata
C. Delete the rows, then `VACUUM` with suitable retention, and confirm no clones or shares retain them
D. Delete the rows and rely on time travel expiring in its own time, since old versions are removed automatically after a while

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Delta deletes are logical until VACUUM removes the files, so time travel would still expose the data. Shallow clones and Delta Sharing recipients are the two places people forget, and both must be checked before declaring the deletion complete.
</details>

---

### Question 11
**Scenario:** Data must be shared with an external organization without copying it.

A. A public bucket holding a copy of the data, with the link sent to the partner so that they can download it when needed
B. Export to CSV and email the files to the partner each week
C. Grant workspace access to the partner's users
D. Delta Sharing, with recipients granted access to specific shares

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Delta Sharing is an open protocol that lets recipients read live data without duplicating it or joining your platform, with revocable, auditable grants. Exports create uncontrolled copies that outlive the relationship.
</details>

---

### Question 12
**Scenario:** A workspace admin must delegate catalog administration to a data team.

A. Grant account admin to the data team's lead
B. Grant catalog ownership or `USE CATALOG` and management privileges to a group, keeping account admin narrow
C. Share the credentials of an existing administrator with the team
D. Grant metastore admin to everyone on the data team

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Unity Catalog privileges are delegable per securable, so a team owns its catalog without gaining rights over others. Metastore admin is the powerful role that should stay with a small platform team.
</details>

---

### Question 13
**Scenario:** Jobs are failing after a runtime upgrade.

A. Ignore the failures and wait for a patch
B. Pin production runtime versions, test upgrades in staging, and adopt LTS releases
C. Always use the newest runtime on every job cluster, since it has the latest fixes and is the best supported release
D. Roll back everything permanently to the oldest runtime that ever worked, and never upgrade the production jobs again

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Runtime upgrades change library versions and can change behavior, so production jobs should pin a version and move deliberately. LTS releases have longer support windows, which reduces how often you must run that migration.
</details>

---

### Question 14
**Scenario:** Secrets must be available to jobs without appearing in notebooks.

A. Hard-code them in a cell at the top and restrict who can open the notebook
B. Secret scopes (optionally backed by a cloud key vault) read through `dbutils.secrets`, with output redaction
C. Environment variables set in the first cell of the notebook
D. A config table in a restricted schema holding the values in plain text

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Secret scopes keep values out of code and out of revision history, and Databricks redacts them from notebook output if printed. Backing the scope with a cloud key vault centralizes rotation with the rest of the organization's secrets.
</details>

---

### Question 15
**Scenario:** Storage costs are growing from old table versions and unused data.

A. Delete files manually from the storage account for the table, starting with the oldest data files in the folder listing
B. Disable Delta on the large tables so that no old versions are kept, and convert them back to plain Parquet files
C. Run `VACUUM` on a schedule with suitable retention, review requirements, and drop or archive unused tables
D. Increase the storage quota for the account

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Retention is a policy decision before it is a cleanup task: the window has to satisfy both concurrent readers and any recovery requirement. Deleting files directly from storage corrupts tables, because the transaction log still references them.
</details>

---

## Where to go deeper

- [Platform Administrator accreditation page](../../exams/databricks/lakehouse-platform-administrator/) - notes, practice plan, strategy
- [Data Engineer Professional practice questions](./databricks-data-engineer-professional.md) - the pipelines this platform runs
- [Cloud cost basics](https://cloud.noblerworks.com/learn/concepts/cloud-cost-basics/) - the cost domain in plain English
- **[📖 Databricks certification](https://www.databricks.com/learn/certification)** - official exam guides
