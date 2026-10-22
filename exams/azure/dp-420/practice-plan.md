# Azure Cosmos DB AI Developer Associate (DP-420) Practice Plan

## Overview

**Exam:** DP-420 - Building AI Applications with Azure Cosmos DB (Azure Cosmos DB AI Developer Associate since 2026-10-06)
**Timeline:** 8-10 weeks
**Study Time:** 8-12 hours per week
**Difficulty:** Associate level

This practice plan targets developers who design, build and optimize applications on Azure Cosmos DB, including AI retrieval and agent memory. Weeks 5-8 were rebuilt for the 2026-10-06 outline: stored procedures, triggers, UDFs and DevOps left the exam; vector, full-text and hybrid search, agent memory, Fabric, fleets, dynamic data masking and per-partition failover arrived.

## Prerequisites

- [ ] Experience with Azure and NoSQL databases
- [ ] Proficiency in C#, Python, or Java
- [ ] Understanding of data modeling concepts
- [ ] Active Azure subscription with Cosmos DB access

## Study Resources

- [ ] [Official certification page and learning paths](https://learn.microsoft.com/en-us/credentials/certifications/azure-cosmos-db-developer-specialty/) (FREE)
- [ ] [DP-420 study guide and change log](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/dp-420)
- [ ] [AI retrieval and agent memory note](notes/cosmos-db-ai-retrieval-agent-memory.md) and [Fabric, fleets and resilience note](notes/cosmos-db-fabric-fleets-resilience.md)
- [ ] [Practice questions](../../../resources/practice-questions/azure-cosmos-db-dp-420.md)
- [ ] [Cosmos DB Documentation](https://docs.microsoft.com/en-us/azure/cosmos-db/)
- [ ] [Complete Azure Study Resources](../../../.templates/resources-azure.md)
- [ ] README.md in this directory

## Week-by-Week Breakdown

### Week 1-2: Data Models and Cosmos DB Fundamentals

**Learning Objectives:**
- Master NoSQL data modeling
- Understand Cosmos DB architecture
- Learn partitioning strategies
- Explore different APIs

**Daily Tasks:**
- [ ] Study NoSQL vs relational data modeling
- [ ] Learn Cosmos DB request units (RUs)
- [ ] Understand partitioning and partition keys
- [ ] Explore Core (SQL) API
- [ ] Learn MongoDB API
- [ ] Study Cassandra and Gremlin APIs
- [ ] Practice data model design patterns

**Hands-on Labs:**
- [ ] Create Cosmos DB account with different APIs
- [ ] Design partition key for sample applications
- [ ] Model one-to-many relationships
- [ ] Implement embedding vs referencing patterns
- [ ] Calculate RU consumption for operations
- [ ] Practice with Data Explorer

**Practice Questions:**
- [ ] Complete 40-50 questions on data modeling

### Week 3-4: Implementing Data Models and Distribution

**Learning Objectives:**
- Master container and item operations
- Implement indexing policies
- Configure global distribution
- Understand consistency levels

**Daily Tasks:**
- [ ] Learn CRUD operations with SDKs
- [ ] Study indexing policies and optimization
- [ ] Understand consistency levels (Strong, Bounded, Session, etc.)
- [ ] Configure multi-region writes
- [ ] Implement conflict resolution
- [ ] Learn about hierarchical partition keys
- [ ] Practice bulk operations

**Hands-on Labs:**
- [ ] Implement CRUD with .NET/Python SDK
- [ ] Configure custom indexing policy
- [ ] Set up geo-replication
- [ ] Test different consistency levels
- [ ] Implement bulk insert operations
- [ ] Configure automatic failover
- [ ] Monitor cross-region latency

**Practice Questions:**
- [ ] Complete 50-60 questions on implementation

### Week 5-6: Change Feed, Query Optimization and AI Retrieval

**Learning Objectives:**
- Implement change feed in both modes
- Optimize queries with query and index metrics
- Configure vector, full-text and hybrid search
- Build a RAG retrieval path

**Daily Tasks:**
- [ ] Change feed processor vs pull model; lease containers
- [ ] Latest version vs all versions and deletes mode (needs continuous backup)
- [ ] Container copy jobs: online vs offline, prerequisites
- [ ] Vector embedding policy: data types, dimensions, distance functions
- [ ] flat vs quantizedFlat vs diskANN; sharded DiskANN with `vectorIndexShardKey`
- [ ] Full-text policy and index; `FullTextContains*` and `FullTextScore`
- [ ] Hybrid search with `RRF` and weights

**Hands-on Labs:**
- [ ] Implement the change feed processor and read deletes in all versions and deletes mode
- [ ] Create a container with vector and full-text policies and indexes
- [ ] Run `VectorDistance`, `FullTextScore` and `RRF` queries; compare RU charges
- [ ] Measure recall with brute force (`VectorDistance(..., true)`) vs diskANN
- [ ] Optimize a slow query using query metrics and index metrics

**Practice Questions:**
- [ ] Complete 40-50 questions on change feed, queries and retrieval

### Week 7: Agent Memory, Fabric and Performance

**Learning Objectives:**
- Design agent memory stores
- Choose between Azure Cosmos DB and Cosmos DB in Fabric
- Monitor latency and cost

**Daily Tasks:**
- [ ] One document per turn; thread ID or tenant + thread partition keys; TTL for short-term memory
- [ ] Semantic memory: embeddings on turns or summaries
- [ ] Fabric mirroring prerequisites and limits; SQL analytics endpoint; Spark connector (`cosmos.oltp`)
- [ ] Autoscale vs manual vs serverless; Azure Monitor alerts; diagnostic logs in Log Analytics
- [ ] Fleets, fleetspaces, pools and fleet analytics

**Hands-on Labs:**
- [ ] Store and retrieve chat turns (latest N, semantic, hybrid)
- [ ] Mirror a database into Fabric and query nested JSON with T-SQL
- [ ] Write data back to a container from a Fabric notebook
- [ ] Set up a Log Analytics query for high-RU requests

**Practice Questions:**
- [ ] Complete 40-50 questions on memory, analytics and optimization

### Week 8: Security and Availability

**Learning Objectives:**
- Implement authentication, authorization and network security
- Configure dynamic data masking
- Configure backup, restore and failover

**Daily Tasks:**
- [ ] Entra ID and data plane RBAC; built-in Data Reader vs Data Contributor
- [ ] Firewall, private endpoints, managed identities
- [ ] Dynamic data masking: strategies, unmask action, one-way enablement
- [ ] Periodic vs continuous backup; point-in-time restore
- [ ] Multi-region reads and writes; per-partition automatic failover and its guardrails

**Hands-on Labs:**
- [ ] Configure a custom role with and without unmask; compare query results
- [ ] Perform a point-in-time restore
- [ ] Configure private link
- [ ] Review per-partition automatic failover settings on a test account

**Practice Questions:**
- [ ] Complete 40-50 questions on security and availability

### Week 9-10: Practice and Review

**Daily Tasks:**
- [ ] Review all exam domains
- [ ] Take 3-4 full practice exams
- [ ] Build end-to-end Cosmos DB application
- [ ] Review weak areas
- [ ] Practice coding scenarios
- [ ] Review best practices
- [ ] Final exam preparation

**Practice Focus:**
- [ ] Practice exam 1 score: _____%
- [ ] Practice exam 2 score: _____%
- [ ] Practice exam 3 score: _____%
- [ ] Comprehensive review completed
- [ ] Exam scheduled

## Key Topics Checklist

### Design and develop database solutions (40-45%)
- [ ] Consistency levels, RU evaluation, throughput, account and container settings
- [ ] SDK client initialization (singleton client, preferred regions)
- [ ] Queries, parameters, paging, continuation tokens, point reads
- [ ] Create, replace, patch, delete; bulk, batch and transactional operations
- [ ] TTL and optimistic concurrency with ETags
- [ ] Data modeling, embed vs reference, partition keys, hierarchical and synthetic keys
- [ ] Partitioning anti-patterns and cost estimates
- [ ] Change feed modes, processor vs pull, lease containers, copy jobs
- [ ] Agent Kit

### Secure, optimize, and deploy database solutions (30-35%)
- [ ] Authentication, authorization, network security
- [ ] Dynamic data masking
- [ ] Backup and restore; multi-region reads and writes; per-partition automatic failover
- [ ] Query and index metrics; end-to-end latency; Azure Monitor alerts; Log Analytics
- [ ] Fleet pools and fleet analytics

### Implement AI and analytics capabilities (20-25%)
- [ ] Full-text search and BM25
- [ ] Vector data types, dimensions, indexes, sharded DiskANN
- [ ] Vector search, hybrid search (RRF), performance tuning
- [ ] RAG design
- [ ] Agent memory: durable stores, conversation state, semantic memory
- [ ] Azure Cosmos DB vs Cosmos DB in Fabric; mirroring; T-SQL and Spark over mirrored data; Spark connector

## Hands-on Lab Checklist

- [ ] Build multi-tenant application with Cosmos DB
- [ ] Implement change feed processor
- [ ] Create globally distributed app
- [ ] Optimize queries with indexing
- [ ] Build a RAG app with hybrid search
- [ ] Build an agent memory store with TTL and semantic recall
- [ ] Monitor and troubleshoot issues

## Additional Resources

- [DP-420 certification page](https://learn.microsoft.com/en-us/credentials/certifications/azure-cosmos-db-developer-specialty/)
- [Cosmos DB Best Practices](https://learn.microsoft.com/en-us/azure/well-architected/service-guides/cosmos-db)
- [Cosmos DB SDKs](https://learn.microsoft.com/en-us/azure/cosmos-db/sdk-dotnet-v3)

---

**Success Strategy:** Hands-on development with the SDKs, NoSQL data modeling, and building at least one retrieval app end to end.
