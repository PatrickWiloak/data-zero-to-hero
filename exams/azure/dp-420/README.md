# Microsoft Certified: Azure Cosmos DB AI Developer Associate (DP-420)

> **Renamed and re-scoped on 2026-10-06.** This certification was the *Azure Cosmos DB Developer Specialty*. Microsoft moved it to Associate level, renamed it **Azure Cosmos DB AI Developer Associate**, and renamed the exam **DP-420: Building AI Applications with Azure Cosmos DB**. The exam code is unchanged. The new outline adds AI retrieval (vector, full-text and hybrid search, RAG), agent memory stores, Microsoft Fabric analytics and fleet management, and drops server-side JavaScript, DevOps and data movement. Existing holders need no action. Source: the [official certification page](https://learn.microsoft.com/en-us/credentials/certifications/azure-cosmos-db-developer-specialty/) and [study guide change log](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/dp-420#change-log), read 2026-10-22.

## Exam Overview

The certification validates that you can design, build and optimize cost-effective, scalable applications on Azure Cosmos DB, including using it as the system of record for agent memory, conversation state, retrieval indexes and grounding data. Microsoft's audience profile expects C# or Python, familiarity with Azure services, generative AI concepts and retrieval-based systems.

**Exam Details:**
- **Exam Code:** DP-420
- **Exam Name:** Building AI Applications with Azure Cosmos DB
- **Duration:** 100 minutes
- **Passing Score:** 700 out of 1000
- **Question Types:** Multiple choice, multiple select, drag and drop, hot area, case studies; may include interactive components
- **Cost:** $165 USD in the US (price varies by country)
- **Validity:** 1 year, renewable free with an online assessment on Microsoft Learn
- **Prerequisites:** None formal; hands-on Cosmos DB for NoSQL development experience

## Exam Domains (skills measured as of 2026-10-06)

| # | Domain | Weight |
|---|--------|--------|
| 1 | Design and develop database solutions | 40-45% |
| 2 | Secure, optimize, and deploy database solutions | 30-35% |
| 3 | Implement AI and analytics capabilities in database solutions | 20-25% |

### 1. Design and develop database solutions (40-45%)

- **Evaluate resource models and SDK solutions** - consistency levels, RU consumption, throughput, account and container settings, creating databases and containers, initializing SDK clients
- **Implement database operations** - queries, parameterized queries, paging and continuation tokens, point reads, create/replace/patch/delete, bulk, batch and transactional operations, TTL, concurrency control
- **Design and implement data models and partitioning strategies** - modeling for access patterns, partition key selection, embed vs reference, hierarchical partition keys and multi-tenant strategies, synthetic keys, partitioning anti-patterns, workload cost estimates
- **Implement change feed** - change feed processor vs pull model, lease containers, latest version vs all versions and deletes mode, container copy jobs
- **Use AI-assisted tools** - the Azure Cosmos DB Agent Kit for recommended practices and faster AI app development

### 2. Secure, optimize, and deploy database solutions (30-35%)

- **Implement database security controls** - authentication, authorization, network security, dynamic data masking
- **Implement availability and recovery** - backup and restore, multi-region reads and writes, per-partition automatic failover
- **Optimize and remediate performance** - query and index metrics, end-to-end latency against objectives, Azure Monitor alerts, diagnostic logs in Log Analytics
- **Fleet management** - fleet-level throughput (pools) and fleet analytics across accounts and subscriptions

### 3. Implement AI and analytics capabilities (20-25%)

- **AI retrieval** - full-text search, vector data types and indexes, vector index sharding (sharded DiskANN), vector search, hybrid search, full-text and vector performance, RAG
- **Agent memory stores** - durable memory, conversation state, semantic memory
- **Operational analytics** - Azure Cosmos DB vs Cosmos DB in Microsoft Fabric, mirroring into Fabric, querying mirrored JSON with T-SQL and Spark, reading and writing with the Cosmos DB Spark connector

### What left the exam

The change log marks these as removed or deleted: server-side programming in JavaScript (stored procedures, triggers, UDFs), the separate data distribution domain, "integrate an Azure Cosmos DB solution", data movement, and DevOps. The older notes in this guide still cover stored procedures and Azure Functions integration because they remain useful in practice; treat them as background, not exam targets.

## Notes in this guide

| Note | Covers |
|------|--------|
| [Cosmos DB fundamentals](notes/cosmos-db-fundamentals.md) | APIs, consistency levels, RUs, global distribution basics |
| [Data modeling and partitioning](notes/cosmos-db-data-modeling-partitioning.md) | Embedding vs referencing, partition keys, synthetic and hierarchical keys, multi-tenancy (Domain 1) |
| [Performance and operations](notes/cosmos-db-performance-operations.md) | Throughput models, query optimization, indexing, change feed, monitoring, security (Domains 1-2) |
| [AI retrieval and agent memory](notes/cosmos-db-ai-retrieval-agent-memory.md) | Vector policies and indexes, sharded DiskANN, full-text, hybrid RRF, RAG, agent memory design (Domain 3) |
| [Fabric, fleets and resilience](notes/cosmos-db-fabric-fleets-resilience.md) | Mirroring and Cosmos DB in Fabric, Spark connector, fleets and pools, dynamic data masking, per-partition failover, change feed modes, copy jobs, Agent Kit (Domains 1-3) |

## Study Tips

### Recommended Study Timeline: 8-10 weeks
- **Weeks 1-2:** Resource model, SDK clients, RUs, consistency, database operations
- **Weeks 3-4:** Data modeling, partition keys, hierarchical keys, change feed modes
- **Weeks 5-6:** Security (Entra ID, data plane RBAC, masking), availability, performance and monitoring, fleets
- **Weeks 7-8:** Vector and full-text indexing, hybrid search, RAG, agent memory, Fabric mirroring
- **Weeks 9-10:** Practice assessment, practice questions, hands-on labs

### Hands-on Practice Requirements
- Create a NoSQL container with a vector embedding policy, a DiskANN index and a full-text index, then run `VectorDistance`, `FullTextScore` and `RRF` queries
- Store chat turns per thread with a TTL and retrieve the latest N and the most similar N
- Implement the change feed processor and read in all versions and deletes mode
- Configure Entra ID data plane RBAC and a dynamic data masking policy
- Mirror a container into Fabric and query it from the SQL analytics endpoint and a notebook

## 📚 Comprehensive Study Resources

**👉 [Complete Azure Study Resources Guide](../../../.templates/resources-azure.md)**

### Quick Links (DP-420 Specific)
- **[📖 Certification page](https://learn.microsoft.com/en-us/credentials/certifications/azure-cosmos-db-developer-specialty/)** - Registration, exam details and renewal
- **[📖 DP-420 study guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/dp-420)** - Official skills outline and change log
- **[📖 Azure Cosmos DB documentation](https://learn.microsoft.com/en-us/azure/cosmos-db/)** - Complete documentation, including the Build AI Applications section
- **[📖 Practice questions](../../../resources/practice-questions/azure-cosmos-db-dp-420.md)** - Original questions for this guide

## Prerequisites and Next Steps

### Prerequisites
- Azure fundamentals
- NoSQL data modeling concepts
- C# or Python (the audience profile names these two)
- Basic generative AI concepts: embeddings, prompts, retrieval

### Career Path
- **Role Focus:** AI application developer, Cosmos DB developer, NoSQL developer
- **Next Steps:** AZ-204 (Azure Developer) or AI-102 for the broader AI side; [AI Zero to Hero](https://ai.noblerworks.com/) covers RAG and agents beyond the database

Remember: the exam now rewards knowing which index, partition key and memory model fits a retrieval workload, not just which consistency level to pick.
