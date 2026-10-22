# Microsoft Fabric Analytics Engineer Associate (DP-600)

> **Outline checked 2026-10-22 against skills measured as of 2026-10-19.** Microsoft's change log for the 2026-10-19 update lists one minor change, a wording fix in "Query and analyze data" ("Visual query editor"); no skills were added, removed or reweighted. This guide was also behind the earlier outline (it still showed the retired four-domain layout), so the domains below are now the current three. Source: [DP-600 study guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/dp-600#change-log).

## Exam Overview

The Microsoft Fabric Analytics Engineer Associate certification validates that you can design, create and manage analytical assets in Microsoft Fabric - semantic models, warehouses and lakehouses - by preparing and enriching data, securing and maintaining analytics assets, and implementing semantic models. You query with SQL, KQL and DAX.

**Exam Details:**
- **Exam Code:** DP-600
- **Exam Name:** Implementing Analytics Solutions Using Microsoft Fabric
- **Duration:** 100 minutes
- **Passing Score:** 700 out of 1000
- **Question Types:** Multiple choice, multiple select, drag and drop, case studies; may include interactive components
- **Cost:** $165 USD in the US (price varies by country)
- **Validity:** 1 year, renewable free with an online assessment on Microsoft Learn
- **Prerequisites:** Experience with Microsoft Fabric and Power BI

## Exam Domains (skills measured as of 2026-10-19)

| # | Domain | Weight |
|---|--------|--------|
| 1 | Maintain a data analytics solution | 25-30% |
| 2 | Prepare data | 45-50% |
| 3 | Implement and manage semantic models | 25-30% |

### 1. Maintain a data analytics solution (25-30%)

- **Implement security and governance** - workspace-level and item-level access controls; row-level, column-level, object-level and file-level access control; sensitivity labels; endorsement
- **Maintain the analytics development lifecycle** - version control for a workspace; Power BI Desktop projects (.pbip); deployment pipelines; impact analysis of downstream dependencies from lakehouses, warehouses, dataflows and semantic models; deploying and managing semantic models through the XMLA endpoint; reusable assets (.pbit templates, .pbids data source files, shared semantic models)

### 2. Prepare data (45-50%)

- **Get data** - create a data connection; discover data with the OneLake catalog and Real-Time hub; ingest or access data; choose between data stores; OneLake integration for Eventhouse and semantic models
- **Transform data** - views, functions and stored procedures; enrich with new columns or tables; star schema for a lakehouse or warehouse; denormalize; aggregate; merge or join; resolve duplicates, missing data and nulls; convert data types; filter
- **Query and analyze data** - select, filter and aggregate with the Visual query editor, SQL, KQL and DAX

### 3. Implement and manage semantic models (25-30%)

- **Design and build semantic models** - storage modes; star schema for a semantic model; bridge tables and many-to-many relationships; DAX variables, iterators, table filtering, windowing and information functions; calculation groups, dynamic format strings and field parameters; large semantic model storage format; composite models
- **Optimize enterprise-scale semantic models** - query and visual performance; DAX performance; Direct Lake configuration including default fallback and refresh behavior; Direct Lake on OneLake vs Direct Lake on SQL analytics endpoint; incremental refresh

The skills newest to this guide (OneLake catalog, Real-Time hub, .pbip/.pbit/.pbids, XMLA deployment, calculation groups, field parameters, large model format, the two Direct Lake flavours) are taught in [Semantic models, lifecycle and discovery](notes/semantic-models-lifecycle-discovery.md).

## Key Microsoft Fabric Components

### Data Engineering
- **Lakehouses** - Unified data storage
- **Data Factory** - ETL/ELT pipelines
- **Dataflows Gen2** - Self-service data prep
- **Apache Spark** - Distributed data processing

### Data Science
- **Notebooks** - Interactive development
- **ML Models** - Training and deployment
- **Experiments** - Model management

### Data Warehousing
- **Data Warehouse** - SQL-based analytics
- **SQL Endpoint** - Querying capabilities

### Real-Time Analytics
- **Eventstreams** - Streaming data
- **KQL Database** - Fast analytics
- **Real-time Dashboards** - Live insights

### Business Intelligence
- **Power BI** - Reports and dashboards
- **Semantic Models** - Data models
- **Datamart** - Self-service analytics

## Study Tips

### Recommended Study Timeline: 8-10 weeks
- **Weeks 1-2:** Microsoft Fabric fundamentals and architecture
- **Weeks 3-4:** Data ingestion and transformation
- **Weeks 5-6:** Data modeling and semantic models
- **Weeks 7-8:** Analysis, visualization, and DAX
- **Weeks 9-10:** Practice exams and hands-on labs

### Key Study Resources
- **Microsoft Learn:** DP-600 learning path (FREE)
- **Fabric Trial:** Hands-on practice with Fabric
- **Power BI Desktop:** Practice report creation
- **Documentation:** Microsoft Fabric documentation

### Hands-on Practice Requirements
- Create and manage Fabric workspaces
- Build end-to-end data pipelines
- Design lakehouse architectures
- Create semantic models with DAX
- Build Power BI reports and dashboards
- Implement real-time analytics solutions

## 📚 Comprehensive Study Resources

**👉 [Complete Azure Study Resources Guide](../../../.templates/resources-azure.md)**

### Quick Links (DP-600 Specific)
- **[DP-600 Official Exam Page](https://learn.microsoft.com/en-us/credentials/certifications/fabric-analytics-engineer-associate/)** - Registration and exam details
- **[Microsoft Learn - DP-600 Learning Path](https://learn.microsoft.com/en-us/credentials/certifications/fabric-analytics-engineer-associate/)** - FREE official study path
- **[Microsoft Fabric Documentation](https://learn.microsoft.com/en-us/fabric/)** - Complete Fabric documentation
- **[Microsoft Fabric Free Trial](https://learn.microsoft.com/en-us/fabric/get-started/fabric-trial)** - 60-day free trial

## Prerequisites and Next Steps

### Prerequisites
- Understanding of data analytics concepts
- Experience with Power BI
- Familiarity with data warehousing and ETL
- Basic understanding of SQL and Python

### Career Path
- **Role Focus:** Fabric Analytics Engineer, Data Engineer, BI Developer
- **Skills Development:** Modern data stack, lakehouse architectures, real-time analytics
- **Next Steps:** DP-700 (Fabric Data Engineer) for the engineering side of the same platform

Remember: DP-600 focuses on the modern analytics platform of Microsoft Fabric. Hands-on experience with Fabric is essential!
