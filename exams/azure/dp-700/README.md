# Microsoft Certified: Fabric Data Engineer Associate (DP-700)

## Overview

The Fabric Data Engineer Associate certification validates the ability to ingest, transform, secure, and serve analytical data using Microsoft Fabric. Fabric is Microsoft's unified analytics platform combining Power BI, Synapse, Data Factory, and Lakehouse (OneLake) into a SaaS experience.

DP-700 launched in late 2024 as Microsoft's modern data-engineering credential. It replaced the older DP-203 (Azure Data Engineer Associate), which was retired on 2025-03-31.

> **Outline checked 2026-10-22 against skills measured as of 2026-10-19.** Microsoft's change log marks two minor changes (in "Configure Microsoft Fabric workspace settings" and "Optimize performance"); compared with the previous published outline the visible edits are wording ("Eventstream" and "Eventhouse" in the singular). No skills were added or removed and the weights are unchanged. Source: [DP-700 study guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/dp-700#change-log).

---

## Quick Reference

| Detail | Info |
|---|---|
| **Exam Code** | DP-700 |
| **Full Name** | Microsoft Certified: Fabric Data Engineer Associate |
| **Provider** | Microsoft |
| **Format** | Multiple choice, multiple response, case studies, Labs (some sittings) |
| **Duration** | 100 minutes |
| **Cost** | $165 USD |
| **Validity** | 1 year (renewable for free annually via Microsoft Learn assessment) |
| **Passing Score** | 700 / 1000 |
| **Languages** | English (more added over time) |

**[📖 Official DP-700 page](https://learn.microsoft.com/credentials/certifications/fabric-data-engineer-associate/)**
**[📖 Microsoft Learn DP-700 Study Guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/dp-700)**

---

## Exam Domains

Per the official Skills Measured outline:

| # | Domain | Weight |
|---|---|---|
| 1 | Implement and manage an analytics solution | 30-35% |
| 2 | Ingest and transform data | 30-35% |
| 3 | Monitor and optimize an analytics solution | 30-35% |

---

## Key Topics (skills measured as of 2026-10-19)

### Domain 1 - Implement and manage an analytics solution (30-35%)

- **Workspace settings:** Spark (starter vs custom pools, environments), domain, OneLake and Apache Airflow workspace settings
- **Lifecycle management:** version control (Git), database projects, deployment pipelines
- **Security and governance:** workspace-level and item-level access; row-, column-, object- and folder/file-level access; dynamic data masking; sensitivity labels; endorsement; Fabric audit logs; OneLake security
- **Orchestration:** choose between Dataflow Gen2, a pipeline and a notebook; schedules and event-based triggers; parameters and dynamic expressions in notebook and pipeline patterns

### Domain 2 - Ingest and transform data (30-35%)

- **Loading patterns:** full and incremental loads, preparing data for a dimensional model, streaming loads
- **Batch:** choose a data store; choose between Dataflows Gen2, notebooks, KQL and T-SQL; OneLake shortcuts; mirroring; pipelines; PySpark, SQL and KQL transforms; denormalize, group and aggregate; duplicate, missing and late-arriving data
- **Streaming:** choose a streaming engine; native tables vs OneLake shortcuts in Real-Time Intelligence; query acceleration vs standard shortcuts; Eventstream, Spark structured streaming and KQL processing; windowing functions

### Domain 3 - Monitor and optimize an analytics solution (30-35%)

- **Monitor:** ingestion, transformation, semantic model refresh, alerts
- **Resolve errors:** pipeline, Dataflow Gen2, notebook, Eventhouse, Eventstream, T-SQL and OneLake shortcut errors
- **Optimize:** Lakehouse tables, pipelines, the data warehouse, Eventstream and Eventhouse, Spark, queries

---

## Study Materials in This Guide

| File | Description |
|---|---|
| [fact-sheet.md](fact-sheet.md) | Reference with Microsoft Learn links and high-yield facts |
| [practice-plan.md](practice-plan.md) | 6-week study plan |
| [scenarios.md](scenarios.md) | 12 exam-style scenarios |
| [notes/01-fabric-architecture-and-workspaces.md](notes/01-fabric-architecture-and-workspaces.md) | Fabric concepts, OneLake, capacity |
| [notes/02-ingestion-pipelines-eventstream.md](notes/02-ingestion-pipelines-eventstream.md) | Pipelines, Dataflows, Eventstream, Mirroring |
| [notes/03-lakehouse-warehouse-notebooks.md](notes/03-lakehouse-warehouse-notebooks.md) | Lakehouse, Warehouse, Spark notebooks |
| [notes/04-security-governance-deployment.md](notes/04-security-governance-deployment.md) | RBAC, sensitivity labels, deployment pipelines, Git |
| [notes/05-monitoring-optimization.md](notes/05-monitoring-optimization.md) | Monitoring hub, capacity tuning, query optimization |
| [notes/06-airflow-database-projects-rti-shortcuts.md](notes/06-airflow-database-projects-rti-shortcuts.md) | Spark, domain, OneLake and Airflow workspace settings; database projects; OneLake security; native tables vs shortcuts and query acceleration in Real-Time Intelligence |

---

## Recommended Study Time

| Background | Estimated Prep Time |
|---|---|
| Existing Azure data engineer (DP-203 or similar) | 4-6 weeks |
| Power BI / Power Query background | 6-8 weeks |
| New to Microsoft data platform | 10-12 weeks |

---

## Hands-on Setup

- **[Microsoft Fabric Free Trial](https://learn.microsoft.com/fabric/get-started/fabric-trial)** - 60-day free trial of Fabric capacity
- Create a workspace, ingest a sample dataset, build a Lakehouse, run a Dataflow, write a Spark notebook
- Try all four ingestion patterns: Pipelines, Dataflows Gen2, Notebooks, Eventstream

---

## Companion Materials

- **[Azure Data Engineer DP-203](../dp-203/)** - covers Synapse / ADLS Gen2 (older but still active)
- **[Azure DP-600](../dp-600/)** - Fabric Analytics Engineer (overlaps with DP-700 on platform basics)
- **[Service Comparison: Databases](../../../resources/service-comparison-databases.md)**
- **[AWS DEA-C01](../../aws/associate/data-engineer-dea-c01/)** - AWS counterpart
- **[Databricks DE Associate](../../databricks/data-engineer-associate/)** - lakehouse counterpart

---

## After DP-700

- **Fabric Analytics Engineer (DP-600)** - covers Fabric from analyst angle
- **Azure Data Scientist (DP-100)** - ML / data science track
- **Azure Solutions Architect Expert (AZ-305)** - for architects designing Fabric solutions
