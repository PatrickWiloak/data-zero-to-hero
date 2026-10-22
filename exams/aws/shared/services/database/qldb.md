---
last-updated: 2026-10-22
---

# Amazon QLDB (Quantum Ledger Database)

> ⚠️ **Amazon QLDB reached end of support on 2025-07-31.** AWS's own notice read: "Existing customers will be able to use Amazon QLDB until end of support on 07/31/2025" ([archived QLDB developer guide](https://web.archive.org/web/20250103202510/https://docs.aws.amazon.com/qldb/latest/developerguide/what-is.html)). It cannot be created or used today, and AWS now redirects QLDB pages to Amazon Aurora. AWS's recommended path was **Amazon Aurora PostgreSQL**. This page is kept because QLDB appears in the retired Database Specialty (DBS-C01) material and in older architectures; read it as history, and see [What replaced QLDB](#what-replaced-qldb) for what to use now.

## Service Overview and Purpose

Amazon QLDB is a fully managed ledger database that provides a transparent, immutable, and cryptographically verifiable transaction log. QLDB maintains a complete and verifiable history of all changes made to your data.

### Key Characteristics
- **Immutable Ledger**: Complete, verifiable history of data changes
- **Cryptographic Verification**: SHA-256 hashing for integrity
- **ACID Transactions**: Full transactional consistency
- **Serverless**: Automatic scaling and management
- **SQL-Like Queries**: PartiQL query language

## Key Features and Capabilities

### Core Features
- **Journal**: Immutable sequence of data changes
- **Cryptographic Verification**: Verify data integrity
- **History Tracking**: Complete audit trail of all changes
- **PartiQL**: SQL-compatible query language
- **ACID Compliance**: Strong consistency guarantees

### Cryptographic Features
- **Hash Chaining**: Each block contains hash of previous block
- **Digital Signatures**: Cryptographic proof of data integrity
- **Merkle Trees**: Efficient verification of data sets
- **Revision History**: Complete change history for each document

## Configuration and Use Cases

### Ledger Creation
```bash
aws qldb create-ledger \
  --name FinancialLedger \
  --permissions-mode STANDARD \
  --kms-key-id alias/qldb-encryption-key \
  --tags Key=Environment,Value=Production
```

### Data Operations
```python
import boto3
from pyqldb.driver.pooled_qldb_driver import PooledQldbDriver

# Connect to QLDB
driver = PooledQldbDriver(
    ledger_name='FinancialLedger',
    region_name='us-west-2'
)

def create_table():
    def execute_lambda(transaction_executor):
        transaction_executor.execute_statement("""
            CREATE TABLE Transactions
        """)

    driver.execute_lambda(execute_lambda)

def insert_transaction(transaction_data):
    def execute_lambda(transaction_executor):
        transaction_executor.execute_statement(
            "INSERT INTO Transactions ?",
            transaction_data
        )

    driver.execute_lambda(execute_lambda)

def query_transactions(account_id):
    def execute_lambda(transaction_executor):
        result = transaction_executor.execute_statement(
            "SELECT * FROM Transactions WHERE accountId = ?",
            account_id
        )
        return [doc for doc in result]

    return driver.execute_lambda(execute_lambda)

# Insert financial transaction
transaction = {
    'transactionId': 'tx001',
    'accountId': 'acc123',
    'amount': 1000.00,
    'type': 'credit',
    'timestamp': '2023-01-01T10:00:00Z'
}
insert_transaction(transaction)
```

### Verification
```python
def verify_ledger_integrity():
    qldb = boto3.client('qldb')

    # Get digest
    response = qldb.get_digest(Name='FinancialLedger')
    digest = response['Digest']

    # Get revision
    revision_response = qldb.get_revision(
        Name='FinancialLedger',
        BlockAddress={'IonText': block_address},
        DocumentId=document_id
    )

    # Verify integrity
    proof = revision_response['Proof']
    # Verification logic here
```

## Use Cases and Scenarios

### Primary Use Cases
1. **Financial Systems**: Banking transactions and accounting
2. **Supply Chain**: Product provenance and tracking
3. **Healthcare**: Medical records and audit trails
4. **Legal Documents**: Contract management and compliance
5. **Identity Management**: User access and permissions audit

### Common Exam Scenarios
- **Regulatory Compliance**: Industries requiring audit trails
- **Financial Applications**: Banking and payment systems
- **Supply Chain Tracking**: Product authenticity verification
- **Legal and Compliance**: Document integrity verification

## What replaced QLDB

QLDB solved one problem well: a journal you could not quietly rewrite, with a cryptographic digest you could hand to an auditor. With the service gone, that property has to be built from parts, and the right parts depend on which half of QLDB you relied on.

**If you needed history and auditability**, AWS's migration guidance moved ledgers into **Amazon Aurora PostgreSQL**. The [migration walkthrough]({MIG}) normalizes ledger documents into relational tables, keeps each revision's metadata (document ID, version, transaction time) in a JSONB column, does a full load from a QLDB export and then replays ongoing changes from a QLDB stream until cutover. The [audit use case guide]({AUD}) shows how Aurora PostgreSQL features stand in for QLDB's built-ins: audit tables filled by triggers on every insert, update and delete, the `pgAudit` extension for session and object audit logging, Database Activity Streams as a trustworthy record of administrator activity (including uncommitted reads), and AWS DMS to copy changes into a separate audit database.

**If you needed cryptographic verification**, no AWS service now offers QLDB's digest-and-proof API as a managed feature, and AWS's audit guide accepts that Aurora keeps no permanent immutable record by itself. A pattern this guide suggests (not an AWS-documented feature): hash-chain the audit table (each row stores a hash of its content plus the previous row's hash) and write periodic digests to S3 Object Lock in compliance mode, so even an administrator cannot alter the stored proofs. AWS Japan's QLDB migration overview also points to partner ledger products such as Scalar's ScalarDL.

**On other clouds**, the closest managed equivalent is **ledger tables in Azure SQL Database and SQL Server 2022**, which hash every change into a chain and write database digests to immutable storage - see the [DP-300 encryption and compliance note](../../../../azure/dp-300/notes/security-encryption-compliance.md#ledger).

| QLDB capability | Replacement today |
|---|---|
| Immutable journal of every revision | History table maintained by triggers in Aurora PostgreSQL |
| PartiQL queries over documents | SQL over relational tables, JSONB for document-shaped data |
| Digest and proof verification | Your own hash chain plus digests in S3 Object Lock; a partner ledger; or Azure SQL ledger |
| Streams to Kinesis | DMS change data capture or logical replication; Database Activity Streams for the admin audit trail |

## Exam Tips (DBS-C01 era)

The retired Database Specialty exam expected QLDB as the answer for "immutable, cryptographically verifiable ledger". That answer is historical; no current AWS exam should present QLDB as a service to deploy.

- **Know immutable ledger concepts** and cryptographic verification
- **Understand when to use QLDB** vs traditional databases
- **Remember PartiQL** as the query language
- **Know verification capabilities** for data integrity
- **Understand compliance** and audit use cases