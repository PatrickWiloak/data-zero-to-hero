# Microsoft Azure Database Administrator Associate (DP-300)

## Exam Overview

> **Outline checked 2026-10-22 against skills measured as of 2026-10-27 (the update takes effect that day).** Microsoft's change log lists one minor change: in "Implement security for data at rest and data in transit", "Implement Always Encrypted with VBS enclaves" became **"Implement Always Encrypted with secure enclaves"** (VBS and Intel SGX). No skills were added or removed and the weights are unchanged. This guide's weights and duration were older than the current outline and are corrected below. Source: [DP-300 study guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/dp-300#change-log).

The Azure Database Administrator Associate certification validates your skills in managing SQL Server and Azure SQL databases - Azure SQL Database, Azure SQL Managed Instance, SQL Server on Azure VMs and SQL Server on-premises - including deployment, security, performance, automation and HA/DR.

**Exam Details:**
- **Exam Code:** DP-300
- **Exam Name:** Administering Microsoft Azure SQL Solutions
- **Duration:** 100 minutes
- **Passing Score:** 700 out of 1000
- **Question Types:** Multiple choice, multiple select, drag and drop, case studies; may include interactive components
- **Cost:** $165 USD in the US (price varies by country)
- **Validity:** 1 year, renewable free with an online assessment on Microsoft Learn
- **Prerequisites:** Experience administering SQL databases

## Exam Domains (skills measured as of 2026-10-27)

| # | Domain | Weight |
|---|--------|--------|
| 1 | Plan and implement data platform resources | 15-20% |
| 2 | Implement a secure environment | 20-25% |
| 3 | Monitor, configure, and optimize database resources | 20-25% |
| 4 | Configure and manage automation of tasks | 15-20% |
| 5 | Plan and configure a high availability and disaster recovery (HA/DR) environment | 20-25% |

### 1. Plan and implement data platform resources (15-20%)
- **Plan and deploy Azure SQL solutions** - choose an offering; automated deployment; Azure Arc-enabled SQL; Azure SQL Database in Microsoft Fabric; table partitioning; sharding; hybrid deployments; patching IaaS and hybrid
- **Configure resources for scale and performance** - SQL Database, Managed Instance and SQL on Azure VMs; table partitioning; data compression
- **Plan and implement a migration strategy** - requirements; online vs offline; migrations to Azure and between Azure SQL services; Managed Instance database copy and move; troubleshooting

### 2. Implement a secure environment (20-25%)
- **Authentication and authorization** - Microsoft Entra ID authentication; security principals; users from Entra identities; object-level permissions; least privilege; troubleshooting; T-SQL management
- **Data at rest and in transit** - TDE; object-level encryption; server and database firewall rules; Always Encrypted; **Always Encrypted with secure enclaves** (changed 2026-10-27); private links and service endpoints
- **Compliance controls** - data classification; server and database audits; change tracking; dynamic data masking; ledger; row-level security

Taught in depth in [Encryption and compliance controls](notes/security-encryption-compliance.md).

### 3. Monitor, configure, and optimize database resources (20-25%)
- **Monitor resource activity and performance** - baselines; metric sources; database watcher; Extended Events
- **Monitor and optimize query performance** - Query Store; blocking; DMVs; index changes; query construct changes; execution plans; Intelligent Insights
- **Configure for optimal performance** - index and statistics maintenance; integrity checks; automatic tuning; server settings; Resource Governor; database-scoped configuration; scaling; intelligent query processing

### 4. Configure and manage automation of tasks (15-20%)
- **SQL Server Agent jobs** - schedules, alerts and notifications, troubleshooting
- **Automate deployment** - ARM and Bicep, Azure PowerShell, Azure CLI, monitoring deployments
- **Database tasks in Azure** - elastic jobs, automation, alerts, troubleshooting

### 5. Plan and configure HA/DR (20-25%)
- **Plan an HA/DR strategy** - RPO/RTO-driven choices; hybrid; Azure-specific options; testing
- **Backup and restore** - strategy; native backup and restore; point-in-time restore; long-term retention; T-SQL; backup to and restore from cloud storage
- **Configure HA/DR** - active geo-replication; Always On availability groups on Managed Instance and VMs; failover groups; Failover Cluster Instances on VMs; log shipping; monitoring and troubleshooting

## Key Azure SQL Services

### Azure SQL Database
- **Serverless** - Auto-scaling compute
- **Hyperscale** - Large-scale databases
- **General Purpose** - Balanced performance
- **Business Critical** - High performance and HA

### Azure SQL Managed Instance
- **Near 100% SQL Server compatibility**
- **VNet integration**
- **Instance pools**

### SQL Server on Azure VMs
- **Full SQL Server control**
- **Custom configurations**
- **Hybrid scenarios**

## Study Tips

### Recommended Study Timeline: 8-10 weeks
- **Weeks 1-2:** Azure SQL fundamentals and deployment
- **Weeks 3-4:** Security and authentication
- **Weeks 5-6:** Performance monitoring and optimization
- **Weeks 7-8:** Automation and maintenance
- **Weeks 9-10:** High availability and disaster recovery

### Key Study Resources
- **Microsoft Learn:** DP-300 learning path (FREE)
- **Hands-on Labs:** Deploy and manage Azure SQL
- **Azure SQL Documentation:** Technical reference
- **SQL Server documentation:** Core concepts

### Hands-on Practice Requirements
- Deploy all Azure SQL deployment options
- Configure security features (TDE, Always Encrypted)
- Monitor and optimize query performance
- Implement geo-replication and failover
- Configure automated backups and restore
- Use Azure Monitor and Query Performance Insight

## 📚 Comprehensive Study Resources

**👉 [Complete Azure Study Resources Guide](../../../.templates/resources-azure.md)**

### Quick Links (DP-300 Specific)
- **[DP-300 Official Exam Page](https://learn.microsoft.com/en-us/certifications/exams/dp-300/)** - Registration
- **[Microsoft Learn - DP-300 Learning Path](https://learn.microsoft.com/en-us/certifications/azure-database-administrator-associate/)** - FREE study path
- **[Azure SQL Documentation](https://docs.microsoft.com/en-us/azure/azure-sql/)** - Complete documentation
- **[Azure Free Account](https://azure.microsoft.com/en-us/free/)** - $200 free credit

## Prerequisites and Next Steps

### Prerequisites
- SQL Server administration experience
- T-SQL query knowledge
- Understanding of database concepts
- Basic Azure knowledge (AZ-900 helpful)

### Career Path
- **Role Focus:** Database Administrator, SQL DBA, Cloud DBA
- **Skills Development:** Cloud database management, HA/DR, performance tuning
- **Next Steps:** Consider DP-420 (Cosmos DB) or AZ-305 (Solutions Architect)

Remember: DP-300 requires strong SQL Server and Azure SQL knowledge. Hands-on database administration experience is essential!
