---
last-updated: 2026-11-07
applies-to: Google Cloud Associate Data Practitioner exam guide v1.0 (read 2026-11-07)
difficulty: beginner
reading-time: 30 min
---

# Section 4: Data Management

About **25%** of the exam, roughly 12 to 15 questions. Four objectives: 4.1 access control and governance, 4.2 lifecycle management, 4.3 high availability and disaster recovery for Cloud Storage and Cloud SQL, 4.4 security and compliance (encryption keys).

## Table of Contents

1. [4.1 Access control and governance](#41-access-control-and-governance)
2. [4.2 Lifecycle management](#42-lifecycle-management)
3. [4.3 High availability and disaster recovery](#43-high-availability-and-disaster-recovery)
4. [4.4 Security and compliance](#44-security-and-compliance)
5. [How the exam tests this section](#how-the-exam-tests-this-section)
6. [Key terms](#key-terms)

---

## 4.1 Access control and governance

### IAM and least privilege

Identity and Access Management answers "who can do what on which resource". A **principal** (user, group, service account) is granted a **role**, which is a bundle of **permissions**, on a resource. **Least privilege** means granting the narrowest role at the lowest level that still lets the person do the job.

Three kinds of role:

- **Basic roles** (Owner, Editor, Viewer) are broad and cover almost everything in a project. Avoid them for production data access.
- **Predefined roles** are curated by Google per service. Use these by default.
- **Custom roles** are your own list of permissions, useful when predefined roles are too wide, or when you need definitions that never change (the docs note predefined roles can gain permissions over time).

BigQuery's predefined roles are the ones to know. `roles/bigquery.dataViewer` reads and queries table data; `dataEditor` adds creating, updating and deleting tables; `dataOwner` grants everything on a dataset; `jobUser` lets a principal run jobs (including queries) in a project but gives no data access by itself; `user` reads dataset metadata and runs jobs; `admin` manages everything in a project. A classic pairing: an analyst needs `dataViewer` on the dataset (to read) plus `jobUser` on the project (to run queries).

**[📖 BigQuery IAM roles](https://docs.cloud.google.com/bigquery/docs/access-control)** - the predefined roles and what each permits

### Cloud Storage access control

Two models exist. **Uniform bucket-level access** uses IAM alone and is the recommended setting. **Fine-grained access** combines IAM with per-object ACLs, and the docs warn of an increased chance of unintentional data exposure. Other tools: **signed URLs** give time-limited access to one object without a Google account; **public access prevention** blocks `allUsers` and `allAuthenticatedUsers`. IAM and ACLs work in parallel: access granted by either applies.

**[📖 Cloud Storage access control overview](https://docs.cloud.google.com/storage/docs/access-control)** - uniform vs fine-grained, signed URLs, public access prevention

### Sharing data: Analytics Hub

**Analytics Hub** (now presented as BigQuery sharing) shares BigQuery datasets or Pub/Sub topics across organizations without copying them. A publisher puts a dataset in a **listing** inside a **data exchange**; a subscriber subscribes and gets a read-only **linked dataset** in their own project that always reflects current data. Use it to give partners or other teams live access with no exports; use plain IAM when everyone is in the same organization and project structure.

**[📖 Analytics Hub introduction](https://docs.cloud.google.com/bigquery/docs/analytics-hub-introduction)** - exchanges, listings, linked datasets and when to share

---

## 4.2 Lifecycle management

### Storage classes

Cloud Storage prices objects by how often you touch them. Colder classes cost less to store and more to read.

| Class | Minimum storage duration | Intended access |
|---|---|---|
| Standard | none | Frequently accessed |
| Nearline | 30 days | About once a month or less |
| Coldline | 90 days | At most once a quarter |
| Archive | 365 days | Less than once a year |

All classes use the same APIs and have millisecond access; the difference is price and retrieval fees. **Autoclass** automatically moves objects between classes for you, which suits unpredictable access patterns.

**[📖 Cloud Storage classes](https://docs.cloud.google.com/storage/docs/storage-classes)** - minimum durations, access guidance and Autoclass

### Lifecycle rules

An **Object Lifecycle Management** configuration is a set of rules; each rule has one action and one or more conditions, and an object must match all conditions. Actions: **Delete**, **SetStorageClass** (move to a colder class) and **AbortIncompleteMultipartUpload**. A common rule: move to Nearline at age 30, to Archive at 365, delete at 2,555 days. Caveats from the docs: changes can take up to 24 hours to apply, actions are asynchronous, and early deletion fees still apply when an object leaves a class before its minimum duration.

**[📖 Object Lifecycle Management](https://docs.cloud.google.com/storage/docs/lifecycle)** - actions, conditions and timing

In BigQuery, the equivalent cost control is expiration: you can set a default table or partition expiration so old data is deleted automatically (I did not fetch the BigQuery expiration page for this guide; confirm the setting names in the docs).

### Archiving

For archive needs, think of which service fits the access pattern: Cloud Storage Archive class for files kept for years and rarely read; BigQuery long-term storage pricing applies automatically to tables not modified for 90 days (general knowledge, not fetched); backups of databases belong in the database's own backup feature. Choose by retention period and how fast you must get the data back.

---

## 4.3 High availability and disaster recovery

Two terms to separate: **high availability (HA)** keeps the service running through a failure, usually automatically and fast. **Disaster recovery (DR)** restores service and data after a bigger failure, measured by **RPO** (how much data you can lose) and **RTO** (how long you can be down).

### Cloud Storage

Location type is your redundancy dial. A region protects against zone failures; dual-region and multi-region protect against a whole region failing, with asynchronous replication between regions. Turbo replication on dual-region buckets targets replication within 15 minutes for a tighter RPO. For accidental deletion rather than outages, object versioning and soft delete are the tools (soft delete keeps deleted objects for seven days by default, per the lifecycle docs).

### Cloud SQL

- **Backups**: automated backups run on a schedule you set; on-demand backups can be taken any time. Retention for automated backups ranges from 1 day to 10 years depending on the option. Backups cover the whole instance disk. **Point-in-time recovery (PITR)** relies on automated backups plus transaction logs, so do not delete them manually.
- **High availability**: an HA ("regional") instance has a primary and a standby in different zones of one region. Writes replicate synchronously to both. If the primary or its zone fails, the standby becomes the new primary, usually with about 60 seconds of unavailability, and clients reconnect to the same IP address. It costs about twice a standalone instance. The standby does not serve reads.
- **Read replicas** are for scaling reads, not for HA by themselves. A replica in another region can be promoted for DR, with data loss depending on replication lag.

**[📖 Cloud SQL backups](https://docs.cloud.google.com/sql/docs/mysql/backup-recovery/backups)** - automated vs on-demand, retention
**[📖 Cloud SQL high availability](https://docs.cloud.google.com/sql/docs/mysql/high-availability)** - regional instances, failover, read replicas vs HA

Rule: zone failure with automatic recovery means HA; "scale reads" means read replicas; "restore to just before someone deleted the table" means PITR; "survive a region loss" means cross-region replica or backup copy.

---

## 4.4 Security and compliance

### Encryption at rest and in transit

**At rest** means stored data; **in transit** means data moving across a network. Google Cloud encrypts customer content at rest by default with AES-256, with no action by you, and encrypts data in transit within its networks and for internet requests over TLS.

**[📖 Default encryption at rest](https://docs.cloud.google.com/docs/security/encryption/default-encryption)** - AES-256 data encryption keys wrapped by key encryption keys
**[📖 Encryption in transit](https://docs.cloud.google.com/docs/security/encryption-in-transit)** - TLS for requests to Google Cloud services, encryption between internal services

### Which key owner?

| Option | Who holds the key | Pick it when |
|---|---|---|
| **GMEK** (Google-managed) | Google, default, free | No special requirement |
| **CMEK** (customer-managed) | You, in **Cloud KMS** | You must control rotation, see key audit logs, or meet a regulation requiring customer-managed keys |
| **CSEK** (customer-supplied) | You supply the key material with each request; Google does not store it | You must hold the key outside Google entirely (Cloud Storage, Compute Engine) |

**Cloud KMS** is the service where CMEKs are created and managed: versions, automatic rotation for symmetric keys, IAM control, and use via envelope encryption (the KMS key wraps the data encryption keys). Organization policies can require CMEK. Per the Cloud KMS docs, CSEK is not a Cloud KMS feature.

**[📖 Cloud KMS overview](https://docs.cloud.google.com/kms/docs/key-management-service)** - key versions, rotation, CMEK and the CSEK difference

Compliance questions usually say "regulations require the company to control encryption keys" (CMEK) or "keys must never be stored by Google" (CSEK). If nothing is said, the default is fine.

---

## How the exam tests this section

- **Least privilege wording.** "Only needs to view data" means Data Viewer, not Editor, not a basic role.
- **Uniform bucket-level access** is the recommended setting; ACL-heavy answers are usually traps.
- **Class by access frequency.** Monthly: Nearline. Quarterly: Coldline. Yearly or less: Archive. Unknown pattern: Autoclass.
- **HA vs DR vs scaling.** Standby in another zone is HA; replica is read scaling or DR; PITR is human error.
- **Key ownership.** Default, then CMEK for control, then CSEK for "Google never holds the key".

## Key terms

- **Least privilege** - granting only the permissions a principal needs, at the lowest practical resource level.
- **Basic roles** - the broad legacy Owner, Editor and Viewer roles that cover nearly everything in a project.
- **Predefined roles** - Google-curated role bundles for a specific service, such as BigQuery Data Viewer.
- **Uniform bucket-level access** - a Cloud Storage setting that uses IAM alone for access control and turns off object ACLs.
- **Signed URL** - a link that gives time-limited access to one Cloud Storage object without a Google account.
- **Analytics Hub** - the BigQuery sharing service that publishes datasets in exchanges and listings without copying data.
- **Linked dataset** - a read-only dataset in a subscriber's project that points to the shared data without copying it.
- **Nearline** - a Cloud Storage class for data accessed about once a month or less, with a 30-day minimum duration.
- **Coldline** - a Cloud Storage class for data accessed at most once a quarter, with a 90-day minimum duration.
- **Archive** - the coldest Cloud Storage class, for data accessed less than once a year, with a 365-day minimum duration.
- **Autoclass** - a bucket setting that moves objects between storage classes automatically based on access.
- **Object Lifecycle Management** - bucket rules that delete objects or change their storage class when conditions are met.
- **RPO** - recovery point objective, the maximum amount of data loss measured in time that is acceptable.
- **RTO** - recovery time objective, the maximum time the service can be unavailable after a failure.
- **Point-in-time recovery** - restoring a Cloud SQL database to a chosen moment using backups plus transaction logs.
- **CMEK** - customer-managed encryption keys, created and controlled by you in Cloud KMS.
- **CSEK** - customer-supplied encryption keys, where you provide the key material and Google does not store it.
- **GMEK** - Google-managed encryption keys, the default with no setup.
- **Cloud KMS** - the service for creating, rotating and controlling cryptographic keys, including CMEKs.
