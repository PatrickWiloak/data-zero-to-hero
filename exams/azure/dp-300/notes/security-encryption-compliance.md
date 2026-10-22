---
last-updated: 2026-10-22
applies-to: DP-300 skills measured as of 2026-10-27
difficulty: intermediate
reading-time: 25 min
---

# Encryption and Compliance Controls for Azure SQL (DP-300)

> **Exam domain:** Implement a secure environment (20-25%), objectives "Implement security for data at rest and data in transit" and "Implement compliance controls for sensitive data". The 2026-10-27 outline update is a minor change in exactly this area: the objective that read "Implement Always Encrypted with VBS enclaves" now reads **"Implement Always Encrypted with secure enclaves"**, which covers both enclave technologies Azure SQL Database offers. Source: the **[📖 DP-300 study guide change log](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/dp-300#change-log)**.

## Table of Contents

1. [Who each control protects against](#who-each-control-protects-against)
2. [Transparent data encryption](#transparent-data-encryption)
3. [Always Encrypted](#always-encrypted)
4. [Always Encrypted with secure enclaves](#always-encrypted-with-secure-enclaves)
5. [Ledger](#ledger)
6. [Classification, auditing, change tracking, masking and RLS](#classification-auditing-change-tracking-masking-and-rls)
7. [Worked scenarios](#worked-scenarios)
8. [How the exam tests this](#how-the-exam-tests-this)

---

## Who each control protects against

Most DP-300 security questions are really one question: **who is the adversary?** Each control defends against a different one, and picking the wrong control is the usual distractor.

| Control | Protects against | Does not protect against |
|---|---|---|
| TLS (encryption in transit) | Someone sniffing the network | Anyone with database access |
| TDE (encryption at rest) | Stolen disks, backups or data files | A DBA or app with a login: data is decrypted for them |
| Always Encrypted | DBAs, cloud operators and malware on the server: keys never reach the engine | The application that holds the keys |
| Dynamic data masking | Casual exposure to non-privileged users in query results | Anyone determined: it is not encryption |
| Row-level security | Users reading rows outside their scope | Users with elevated permissions |
| Ledger | Undetected tampering, including by DBAs | Tampering itself: it makes changes evident, it does not block them |

---

## Transparent data encryption

TDE encrypts data and log files, and therefore backups, at the page level as they are written to disk, and decrypts as pages are read into memory. It is **on by default** for new Azure SQL Database and Managed Instance databases with a service-managed key. The alternative is a **customer-managed key** in Azure Key Vault or Managed HSM ("bring your own key"), which gives you control of rotation and the ability to revoke access to the database by revoking the key.

TDE is transparent to every user with permissions: a DBA running `SELECT` sees plaintext. That is the point and the limit. When a question says "protect against a stolen backup file", TDE is enough; when it says "the DBA must not see salaries", it is not.

**[📖 Transparent data encryption](https://learn.microsoft.com/en-us/azure/azure-sql/database/transparent-data-encryption-tde-overview)** - service-managed vs customer-managed keys

---

## Always Encrypted

Always Encrypted encrypts chosen columns **in the client driver**. The database stores ciphertext and never holds the keys in plaintext, so DBAs, cloud administrators and anyone who dumps server memory see only ciphertext.

Two key types:

- **Column master key (CMK)** - lives outside the database (Azure Key Vault, Windows certificate store, HSM). The database holds only metadata pointing to it.
- **Column encryption key (CEK)** - encrypts the column data; stored in the database, but itself encrypted by the CMK.

Two encryption types, and this is where questions focus:

| | Deterministic | Randomized |
|---|---|---|
| Same plaintext gives | Same ciphertext | Different ciphertext |
| Supports without an enclave | Equality: `=`, joins, `GROUP BY`, indexes on equality | Nothing beyond storage and retrieval |
| Leakage | Patterns: an attacker can count repeated values | None from ciphertext |
| Fits | Lookup by an exact value (SSN equals) | Data you only store and return |

Without an enclave the engine cannot do anything with randomized columns, and the only operation on deterministic columns is equality. Initial encryption and key rotation require moving data out to a client tool (SSMS wizard or PowerShell) and back.

---

## Always Encrypted with secure enclaves

A **secure enclave** is a protected region of memory inside the Database Engine process that the rest of the engine, the OS and debuggers cannot see into. The client driver sends the column encryption keys to the enclave over a secure channel; the engine delegates operations on encrypted columns to the enclave, which decrypts, computes and returns results. Keys and plaintext still never appear in the engine outside the enclave.

That unlocks two things:

1. **In-place encryption.** Encrypt a column, rotate a key or change from deterministic to randomized with `ALTER TABLE` inside the database, without a round trip through a client tool. Faster and more reliable for large tables.
2. **Rich confidential queries** on **randomized**, enclave-enabled columns: comparison operators, `BETWEEN`, `IN`, `LIKE`, `DISTINCT`, joins, and (in Azure SQL Database and recent SQL Server versions) `ORDER BY` and `GROUP BY`. You can also create nonclustered indexes on those columns. Deterministic columns still support only equality.

Microsoft strongly recommends **Accelerated Database Recovery** before indexing enclave-enabled randomized columns (it is on by default in Azure SQL Database and Managed Instance). Be aware of the trade-off: range queries on an indexed encrypted column reveal ordering, so a DBA scanning the index could learn which row holds the maximum salary.

### VBS vs Intel SGX enclaves

This is the distinction behind the 2026-10-27 wording change.

| | VBS enclaves | Intel SGX enclaves |
|---|---|---|
| Technology | Software: Windows hypervisor (Virtual Secure Mode) | Hardware trusted execution environment |
| Where | SQL Server 2019 and later; Azure SQL Database on any purchasing model and hardware except DC-series, including DTU and elastic pools | Azure SQL Database on **DC-series** hardware only (vCore) |
| Attestation | SQL Server: Host Guardian Service or none. Azure SQL Database: **not supported** | **Mandatory**, via Microsoft Azure Attestation |
| Protects against | Privileged users in your organization, including DBAs; memory dumps | Also stronger protection against OS-level admin attacks |
| Constraints | Few | DC-series uses physical cores, max 40 cores, no serverless |
| Future | Recommended default | **End of support 2027-10-31**; Azure then moves remaining DC-series databases to standard-series with VBS |

**Attestation** lets the client verify that the enclave is genuine and runs Microsoft's signed code before sending it keys. The application sets an attestation protocol in its connection configuration (for example `Attestation Protocol=None` for VBS in Azure SQL Database). The client also needs a driver that supports enclave computations.

**Configuring it in Azure SQL Database (VBS):** enable the VBS enclave on the database (portal, PowerShell or CLI), provision an **enclave-enabled** column master key (the `ENCLAVE_COMPUTATIONS` property), create column encryption keys from it, encrypt columns with randomized encryption, and connect with enclave computations enabled.

**[📖 Always Encrypted with secure enclaves](https://learn.microsoft.com/en-us/sql/relational-databases/security/encryption/always-encrypted-enclaves)** - how it works, supported operations, attestation
**[📖 Plan for secure enclaves in Azure SQL Database](https://learn.microsoft.com/en-us/azure/azure-sql/database/always-encrypted-enclaves-plan)** - VBS vs SGX choice and DC-series limits

---

## Ledger

Ledger makes a database **tamper-evident**. Every change to a ledger table is hashed (SHA-256) into a chain of blocks; previous row versions are kept in a history table. Periodically a **database digest** - the hash of the latest block - is written outside the database, ideally to **Azure immutable blob storage** or **Azure Confidential Ledger** (automatic digest storage), where even an administrator cannot alter it. Later, `sys.sp_verify_database_ledger` recomputes the hashes and compares them with the stored digests; any rewritten history breaks the chain.

Two kinds of ledger table:

- **Updatable ledger tables** allow updates and deletes; prior versions move to a history table, and a ledger view shows the full change trail.
- **Append-only ledger tables** allow inserts only, for logs and audit records that must never change.

Rules that show up as distractors: you cannot convert an existing table into a ledger table (create a new ledger table and migrate the data), and you cannot turn ledger off for a table once it is on. A whole database can be created as a **ledger database**, making every table a ledger table.

Ledger is also the closest Azure analogue to the retired Amazon QLDB: cryptographically verifiable history inside a normal relational engine.

**[📖 Ledger overview](https://learn.microsoft.com/en-us/sql/relational-databases/security/ledger/ledger-overview)** - use cases, table types, digests and verification

---

## Classification, auditing, change tracking, masking and RLS

The rest of "Implement compliance controls for sensitive data", briefly:

- **Data discovery and classification** labels columns with an information type and sensitivity label (for example Confidential - GDPR). Classification feeds auditing: the audit log records which sensitivity labels each query touched. Manage it in the portal, with T-SQL (`ADD SENSITIVITY CLASSIFICATION`) or through Microsoft Purview.
- **Auditing** writes database events to Azure Storage, a Log Analytics workspace or Event Hubs. Server-level audit policies apply to every database on the logical server; database-level policies add to them.
- **Change tracking** records *that* a row changed (keys and version), not the old values, for lightweight sync scenarios. **Change data capture** records the changed values themselves. Pick change tracking when a client only needs "what changed since version N".
- **Dynamic data masking** hides values in results for users without `UNMASK` (which can be granted at database, schema, table or column level). Masked users can still infer values with crafted predicates, so it is a convenience control.
- **Row-level security** uses an inline table-valued **predicate function** and a **security policy**: filter predicates silently remove rows on read; block predicates stop writes that would violate the policy.

**[📖 Data discovery and classification](https://learn.microsoft.com/en-us/azure/azure-sql/database/data-discovery-and-classification-overview)** - labels, audit integration
**[📖 Auditing for Azure SQL](https://learn.microsoft.com/en-us/azure/azure-sql/database/auditing-overview)** - destinations, server vs database policies
**[📖 Row-level security](https://learn.microsoft.com/en-us/sql/relational-databases/security/row-level-security)** - predicates and policies

---

## Worked scenarios

### Scenario 1: DBAs must not read salaries, analysts need range filters

**Situation:** A Business Critical Azure SQL Database on standard-series hardware stores salaries. Outsourced DBAs must never see them; the HR app filters `WHERE Salary BETWEEN @a AND @b`.

**Answer:** Always Encrypted with a **VBS** secure enclave and **randomized** encryption on an enclave-enabled key. Deterministic encryption would not support the range filter, TDE would show DBAs plaintext, and SGX would require moving to DC-series hardware (and reaches end of support in 2027).

### Scenario 2: Encrypting a 2 TB column without downtime windows

**Situation:** Encrypting an existing column with the SSMS wizard would move all the data to the client and back.

**Answer:** With an enclave-enabled column master key and a secure enclave, encrypt **in place** with `ALTER TABLE ... ALTER COLUMN`, inside the database.

### Scenario 3: Prove to an auditor that payment records were never changed

**Answer:** Create the table as an append-only (or updatable) **ledger** table, enable automatic digest storage to immutable blob storage or Azure Confidential Ledger, and run `sys.sp_verify_database_ledger` for the auditor.

### Scenario 4: Lost backup tape

**Answer:** TDE already covers it: backups of a TDE database are encrypted. Use a customer-managed key if you must be able to revoke access.

---

## How the exam tests this

- **Adversary first.** Stolen media: TDE. DBAs and cloud operators: Always Encrypted. Casual exposure: DDM. Proof of no tampering: ledger.
- **Deterministic vs randomized.** Equality, joins and grouping without an enclave need deterministic; anything richer needs randomized plus an enclave.
- **Secure enclaves, not just VBS.** VBS everywhere except DC-series, no attestation in Azure SQL Database; SGX on DC-series only, Microsoft Azure Attestation required, end of support 2027-10-31.
- **In-place encryption** needs enclave-enabled keys.
- **Ledger rules.** Cannot convert existing tables; cannot disable; digests stored outside the database; verify with `sys.sp_verify_database_ledger`.

---

**Related notes:** [Database administration overview](database-administration-overview.md) · [Deployment and management](sql-deployment-management.md) · [Performance monitoring and optimization](performance-monitoring-optimization.md)
