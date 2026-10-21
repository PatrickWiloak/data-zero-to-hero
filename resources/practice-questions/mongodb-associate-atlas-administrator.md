---
last-updated: 2026-08-09
difficulty: intermediate
---

# MongoDB Associate Atlas Administrator - Practice Questions

15 questions weighted toward cluster management (25%), security and access and data management (20% each), then performance (15%), monitoring and backup (10% each).

> **Cert page:** [exams/mongodb/associate-atlas-administrator/](../../exams/mongodb/associate-atlas-administrator/)

---

### Question 1
**Scenario:** An Atlas cluster must survive the loss of a cloud availability zone.

A. Manual failover
B. A replica set spread across at least three availability zones, which Atlas does by default on dedicated tiers
C. A single-region M0 cluster, since the free tier's members are already placed in separate zones by shared infrastructure
D. A standalone instance placed in a single availability zone, protected with nightly snapshots that can be restored after a failure

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Atlas places the three replica set members in separate zones automatically on dedicated tiers, so zone loss triggers an election rather than an outage. Surviving the loss of a whole region requires a multi-region cluster with electable members elsewhere.
</details>

---

### Question 2
**Scenario:** Application traffic must reach Atlas without traversing the public internet.

A. VPC peering or a private endpoint (PrivateLink or Private Service Connect)
B. A VPN client installed on each developer machine, connecting laptops to the Atlas project from home
C. IP access list only, with the application servers' public addresses allowlisted for the cluster
D. Public endpoint with TLS, since TLS encrypts every packet and so keeps the traffic off the public internet entirely

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** The IP access list restricts who may connect over the public endpoint but the traffic still leaves your network. Private endpoints are the stronger option because they work across overlapping CIDR ranges and do not require the routing changes peering does.
</details>

---

### Question 3
**Scenario:** Which Atlas tier supports auto-scaling of compute?

A. M0 free tier
B. Dedicated clusters from M10 upward, which scale the tier within configured bounds
C. Every tier, including the free M0, because auto-scaling is a project-wide setting that all clusters inherit automatically
D. Shared tiers only, because dedicated clusters keep the fixed size that was chosen when they were created

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Shared tiers have fixed resources. Setting both a minimum and a maximum on dedicated tiers is what makes auto-scaling safe: an unbounded maximum turns a traffic spike into a bill. Storage auto-scaling is a separate setting and scales up only.
</details>

---

### Question 4
**Scenario:** A cold dataset must move to cheaper storage while remaining queryable.

A. Online Archive, which moves data matching a rule to object storage yet keeps it queryable
B. A separate cluster
C. `mongodump` to S3, which stores compressed copies cheaply and lets applications query them in place
D. Delete it

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Online Archive keeps the data addressable while removing it from the cluster's storage and working set. Query performance against archived data is much slower, so the archiving rule should reflect access patterns rather than age alone.
</details>

---

### Question 5
**Scenario:** Which Atlas feature recommends indexes based on observed queries?

A. Performance Advisor, which analyzes slow queries and suggests indexes
B. The profiler only, which logs slow operations and then creates the matching indexes on its own
C. Atlas Search
D. Real-Time Performance Panel, which shows live operations and their current latency

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Performance Advisor surfaces suggestions from actual slow query logs, and it also flags unused indexes, which cost write throughput and storage for nothing. The Real-Time Performance Panel shows what is happening right now rather than making recommendations.
</details>

---

### Question 6
**Scenario:** Database users must authenticate without static passwords.

A. No authentication
B. Cloud provider IAM authentication, x.509 certificates, or federated identity via LDAP or OIDC
C. API keys
D. SCRAM passwords only

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** IAM authentication ties database access to a workload's existing cloud role, so there is no password to rotate or leak. Note that database users are a separate concept from Atlas organization and project users, who administer the platform rather than read data.
</details>

---

### Question 7
**Scenario:** A cluster must be restored to a specific moment before an accidental deletion.

A. Replica set members
B. Online Archive
C. Continuous cloud backup with point-in-time restore
D. A snapshot restore only, using the most recent scheduled snapshot taken before the accidental deletion

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Snapshots give you fixed points; continuous backup replays the oplog to any moment inside the retention window. Replication does not help here at all, because the deletion replicates to every member immediately.
</details>

---

### Question 8
**Scenario:** Encryption keys for Atlas must be controlled by the customer.

A. Filesystem encryption on the underlying disks in each cloud account, which already gives the customer full control of the keys
B. Enable TLS on every connection, which lets the customer manage the keys used for storage encryption
C. Not possible, because Atlas always generates and manages every encryption key itself on behalf of customers
D. Encryption at rest with customer-managed keys in AWS KMS, Azure Key Vault, or GCP KMS

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Atlas encrypts at rest by default with provider-managed keys; customer key management puts the key lifecycle, including revocation, in your control. Client-side field level encryption is the separate, stronger control where Atlas never sees plaintext for those fields.
</details>

---

### Question 9
**Scenario:** Which Atlas capability runs code in response to database changes?

A. Cron on an application server
B. Atlas Triggers: database triggers on change events, plus scheduled and authentication triggers
C. Alerts
D. Change streams only

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Triggers are the managed layer over change streams, so there is no long-running listener process of your own to keep alive and resume. Scheduled triggers cover the cron use case in the same place.
</details>

---

### Question 10
**Scenario:** An alert must fire when a cluster's disk approaches capacity.

A. Nothing exists
B. An Atlas alert on the disk space used metric, routed to email, Slack, PagerDuty, or a webhook
C. A support ticket
D. Manual checks

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Atlas has default alerts and supports custom conditions on any exposed metric. Disk is the one to configure deliberately, since a full disk stops writes; storage auto-scaling reduces but does not eliminate the need for the alert.
</details>

---

### Question 11
**Scenario:** Users in different countries must read from a nearby region while writes stay authoritative.

A. Sharding by country on a single-region cluster, which splits the data across shards but keeps every shard in one region
B. A CDN
C. A multi-region cluster with read-only or electable nodes in extra regions, read preference `nearest`
D. Separate clusters per region, each holding a full copy that is kept in sync with a nightly mongodump restore

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Read-only nodes serve local reads without participating in elections. If write locality is the requirement rather than read locality, global clusters with zone sharding are the mechanism, pinning documents to a region by shard key.
</details>

---

### Question 12
**Scenario:** Atlas infrastructure must be managed as code.

A. Not possible, because Atlas clusters, users and network settings can only be configured through the web console by hand
B. Shell scripts that connect to the database with mongosh and run commands to create clusters, users and network rules
C. The Atlas Administration API, the Terraform Atlas provider, the Atlas CLI, or the Kubernetes Operator
D. Console only, with every setting changed through the web interface and recorded in a change log by the admin

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** All of these sit on the Administration API. Terraform is the common choice where cluster configuration should live beside the rest of the infrastructure, and programmatic API keys with a project-scoped role are how you authenticate them.
</details>

---

### Question 13
**Scenario:** Which Atlas feature enables vector similarity search for a retrieval-augmented generation application?

A. A text index, which ranks documents by term frequency and can compare embeddings by cosine similarity
B. Atlas Search with a full-text index, queried with `$search`
C. Atlas Vector Search, indexing embedding fields and queried with `$vectorSearch`
D. Data Federation

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Vector Search does approximate nearest-neighbor search over embeddings stored alongside the source documents, so there is no separate vector database to keep in sync. Combining it with normal filters in the same query is the practical advantage.
</details>

---

### Question 14
**Scenario:** Data in Atlas and in an S3 bucket must be queried together.

A. Copy the S3 data into Atlas with a scheduled job, then query only the Atlas collections
B. Atlas Data Federation, one MQL endpoint over the cluster and object storage
C. Not possible, because Atlas can only query data that is stored inside its own clusters
D. Export the Atlas collections and the S3 files into a data warehouse and query them there on a schedule

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Federation avoids the copy, which is the whole point when the object storage data is large and cold. Expect object storage query performance rather than cluster performance, and partition the S3 layout to match the filters you run.
</details>

---

### Question 15
**Scenario:** A project's costs must be attributed to teams.

A. Manual estimation
B. One project for everything, then split the monthly invoice by hand using each cluster's share of storage
C. Separate accounts
D. Use organizations, projects, and cluster tags, since billing reports break down by project

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** The project is the natural allocation boundary because it also scopes network access, users, and alerts, so the security boundary and the cost boundary line up. Retrofitting a project split later means recreating clusters.
</details>

---

## Where to go deeper

- [Atlas Administrator cert page](../../exams/mongodb/associate-atlas-administrator/) - notes, practice plan, strategy
- [MongoDB Associate DBA practice questions](./mongodb-associate-dba.md) - the self-managed counterpart
- [MongoDB Associate Developer practice questions](./mongodb-associate-developer.md) - the application-side view
- [Embeddings and vector search](https://ai.noblerworks.com/learn/concepts/embeddings-and-vector-search/) - context for Atlas Vector Search
- **[📖 MongoDB University](https://learn.mongodb.com/)** - official courses and exam pages
