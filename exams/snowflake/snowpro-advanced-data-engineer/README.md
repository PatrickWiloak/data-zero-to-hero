# SnowPro Advanced: Data Engineer Certification (DEA-C02)

## Exam Overview

The SnowPro Advanced - Data Engineer Certification validates expertise in building and managing data pipelines on Snowflake. This certification targets data engineers who design, build, and maintain data ingestion, transformation, and orchestration workflows using Snowflake's native capabilities including Snowpipe, tasks, streams, dynamic tables, and Snowpark.

**Exam Details:**
- **Exam Code:** DEA-C02
- **Duration:** 115 minutes
- **Number of Questions:** 65 (multiple select, multiple choice, interactive)
- **Passing Score:** 750 out of 1000
- **Cost:** $375 USD
- **Delivery:** Online proctored or onsite testing center
- **Validity:** 2 years
- **Prerequisites:** SnowPro Core Certification (active)

## Exam Domains (DEA-C02)

> **The exam code is DEA-C02.** This guide previously listed "ADE-C01", which is not a Snowflake code. The domain names and weights below are from Snowflake's official DEA-C02 exam page. Source: the official exam page on [learn.snowflake.com](https://learn.snowflake.com/en/certifications/), read 2026-10-22. The topic sections that follow keep their original grouping; objective-level detail has not yet been checked against the DEA-C02 study guide PDF, which Snowflake serves only through a sign-up form.

| Domain | Weight |
|---|---|
| 1.0 Data Movement | 28% |
| 2.0 Performance Optimization | 19% |
| 3.0 Storage and Data Protection | 14% |
| 4.0 Data Governance | 14% |
| 5.0 Data Transformation | 25% |

### Topic area: Data Movement (domain 1.0, 28%)
- Snowpipe continuous loading architecture
- Snowpipe Streaming for low-latency ingestion
- Bulk data loading with COPY INTO
- External stages and storage integrations
- Data unloading and export patterns
- Connector ecosystem (Kafka, Spark)

**Key Concepts:**
- Snowpipe auto-ingest with cloud event notifications
- Snowpipe Streaming via Ingest SDK (row-level API)
- Stage types: internal, external (S3, Azure Blob, GCS)
- File format options and error handling
- COPY INTO options and transformations during load
- Kafka connector with Snowpipe Streaming mode

### Topic area: Data Pipelines and Transformations (domain 5.0 Data Transformation, 25%)
- Tasks and DAG orchestration
- Streams for change data capture (CDC)
- Dynamic tables for declarative pipelines
- Snowpark for programmatic transformations
- UDFs and stored procedures
- Error handling and pipeline monitoring

**Key Concepts:**
- Task trees (DAGs) with dependencies and scheduling
- Stream types: standard, append-only, insert-only
- Dynamic table target lag and refresh mechanics
- Snowpark DataFrame API for complex transformations
- Python, Java, and Scala UDF development
- TASK_HISTORY and PIPE_USAGE_HISTORY monitoring

### Topic area: Performance and Optimization (domain 2.0, 19%)
- Warehouse sizing for data engineering workloads
- Clustering strategies for pipeline output tables
- Query optimization for transformation queries
- Materialized views vs dynamic tables
- Cost management for pipeline operations

**Key Concepts:**
- Warehouse sizing for COPY INTO vs transformation queries
- Clustering key selection for downstream query patterns
- Pruning optimization in pipeline queries
- Resource monitors for pipeline cost control
- Serverless task credit management

### Topic area: Data Governance and Security (domain 4.0, 14%; Time Travel, cloning and replication fall under 3.0 Storage and Data Protection, 14%)
- Role-based access control for pipeline objects
- Dynamic data masking in pipeline outputs
- Object tagging for pipeline data classification
- Audit and compliance for data movement
- Secure data sharing of pipeline outputs

**Key Concepts:**
- Granting privileges on tasks, streams, and pipes
- Masking policies applied during transformation
- Tag propagation through pipeline stages
- ACCESS_HISTORY for audit trails
- Secure views for sharing transformed data

## Key Study Areas

### Data Ingestion Patterns
- **Snowpipe:** Event-driven continuous loading from cloud storage
- **Snowpipe Streaming:** Row-level API-based ingestion for real-time
- **COPY INTO:** Bulk loading with transformation capabilities
- **Kafka Connector:** Streaming from Kafka topics to Snowflake
- **External Tables:** Query data in place without loading

### Pipeline Orchestration
- **Tasks:** Scheduled or event-driven SQL/procedure execution
- **Streams:** Change tracking on tables for CDC patterns
- **Dynamic Tables:** Declarative pipelines with target lag
- **Snowpark:** Programmatic pipeline logic in Python/Java/Scala

### Transformation Capabilities
- **SQL Transformations:** Views, CTEs, window functions
- **Snowpark DataFrame:** Programmatic transformation chains
- **UDFs:** Custom scalar and table functions
- **Stored Procedures:** Complex multi-step pipeline logic
- **External Functions:** Integration with external services

## Hands-On Skills Required

### Pipeline Development
- Design and implement Snowpipe auto-ingest pipelines
- Build task DAGs with proper dependency chains
- Create streams for change data capture
- Develop Snowpark stored procedures for complex transformations
- Write UDFs for reusable transformation logic

### Pipeline Operations
- Monitor pipeline health using INFORMATION_SCHEMA views
- Troubleshoot failed tasks and pipe errors
- Optimize pipeline performance and cost
- Manage pipeline permissions and access control

## Study Tips

1. **Hands-On Practice:** Build end-to-end pipelines in a trial account
2. **Streaming Focus:** Understand Snowpipe vs Snowpipe Streaming differences
3. **Task DAGs:** Practice building multi-step task dependency trees
4. **Dynamic Tables:** Know when to use dynamic tables vs tasks/streams
5. **Snowpark:** Be comfortable writing Python UDFs and stored procedures
6. **Monitoring:** Know the INFORMATION_SCHEMA views for pipeline health
7. **Cost Awareness:** Understand serverless vs warehouse-based pipeline costs

## Comprehensive Study Resources

### Quick Links
- **[Exam Registration](https://www.snowflake.com/certifications/)** - Snowflake certification portal
- **[Snowflake Documentation](https://docs.snowflake.com/en/)** - Complete documentation
- **[Data Loading Guide](https://docs.snowflake.com/en/user-guide/data-load-overview)** - Loading overview
- **[Snowpipe Guide](https://docs.snowflake.com/en/user-guide/data-load-snowpipe-intro)** - Continuous loading
- **[Snowpark Guide](https://docs.snowflake.com/en/developer-guide/snowpark/index)** - Developer framework

### Recommended Preparation
- Snowflake official Advanced Data Engineer study guide
- Snowflake University data engineering courses
- Hands-on pipeline development in trial account
- Snowflake community and knowledge base articles

## Exam Registration

Register through:
- **Snowflake Certification Portal:** [snowflake.com/certifications](https://www.snowflake.com/certifications/)
- **Online Proctoring:** Exams delivered via online proctoring

## Career Benefits

### Job Opportunities
- Senior Data Engineer
- Snowflake Data Engineer
- Data Pipeline Architect
- Analytics Engineer
- DataOps Engineer

### Professional Development
- Advanced Snowflake data engineering credential
- Demonstrates pipeline design and optimization expertise
- Validates Snowpark and serverless feature proficiency
- Complements data architect and cloud certifications
