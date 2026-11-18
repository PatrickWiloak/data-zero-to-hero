---
last-updated: 2026-11-18
---

# Azure Database Administration Overview

## Overview
Azure Database Administration covers planning, implementing, and managing SQL Server and Azure SQL database solutions. Focus areas include deployment, security, performance, high availability, disaster recovery, and automation.

## Azure SQL Deployment Options

### Azure SQL Database
- **Fully managed PaaS**
- **Best for:** Cloud-native applications
- **Features:** Auto-patching, backups, high availability
- **Scaling:** Vertical (compute/storage) and horizontal (sharding)
- **Limitations:** Some SQL Server features unavailable

### Azure SQL Managed Instance
- **Near 100% SQL Server compatibility**
- **Best for:** Lift-and-shift migrations
- **Features:** Instance-level features, cross-database queries, SQL Agent
- **VNet integration:** Native virtual network support

### SQL Server on Azure VMs
- **Full control:** IaaS solution
- **Best for:** Specific SQL Server versions, OS access needed
- **You manage:** OS, SQL Server, patches, backups

## Security Implementation

### Authentication Methods
- **SQL Authentication:** Username/password
- **Azure AD Authentication:** Recommended, supports MFA
- **Windows Authentication:** SQL VM only

### Authorization
- **Server-level roles:** sysadmin, securityadmin
- **Database-level roles:** db_owner, db_datareader, db_datawriter
- **Row-Level Security (RLS):** Filter rows by user
- **Dynamic Data Masking:** Obfuscate sensitive data

### Encryption
- **TDE (Transparent Data Encryption):** Encrypt at rest
- **Always Encrypted:** Column-level encryption
- **SSL/TLS:** Encryption in transit

### Network Security
- **Firewall rules:** IP-based access control
- **Virtual Network rules:** VNet access
- **Private endpoints:** Private IP access

## Performance Tuning

### Query Performance
- **Query Store:** Track query performance over time
- **Execution plans:** Analyze query execution
- **Indexes:** Proper indexing strategy
- **Statistics:** Keep statistics updated

### Monitoring
- **Dynamic Management Views (DMVs):** Real-time insights
- **Extended Events:** Lightweight tracing
- **Azure Monitor:** Metrics and alerts
- **Query Performance Insight:** Azure portal tool

### Database Watcher

> **Retiring:** Microsoft's page says database watcher retires on April 30, 2027 and won't reach general availability. Since November 9, 2026 you can't create new watchers or edit existing ones; existing watchers keep collecting data until retirement. Microsoft points to Database Hub in Microsoft Fabric as the replacement. The DP-300 skills measured as of October 27, 2026 still lists "Monitor by using database watcher", so know what it is.

Database watcher is a managed monitoring service (in preview) for Azure SQL Database and Azure SQL Managed Instance. You create a **watcher** resource, pick **SQL targets** (databases, elastic pools or managed instances, up to 100 per watcher), and grant the watcher's managed identity limited read access to system views. It collects data from more than 70 catalog views and DMVs into a **data store** you own: an Azure Data Explorer cluster or a Real-Time Analytics database in Microsoft Fabric. You see it in Azure portal dashboards (estate-wide heatmaps, top queries, per-resource views), query it with KQL, and alert on it with Azure Monitor. It does not monitor SQL Server on VMs or on-premises.

| Tool | Scope | Where data lives | Use it for |
|------|-------|------------------|------------|
| **Database watcher** | Many databases, pools and managed instances in one view | Your Azure Data Explorer or Fabric Real-Time Analytics database | Estate-wide, near real-time monitoring with long retention |
| **Query Store** | One database | Inside that database | Query plan history, regressions and plan forcing |
| **Azure SQL Analytics** | Many databases via diagnostic settings | Log Analytics workspace | Legacy only: Microsoft's page for it is archived and says it is no longer in active development |

**Decision rule:** a question about plan regression or forcing a plan in one database -> Query Store. A question about one dashboard across a fleet of Azure SQL resources with data kept in Azure Data Explorer -> database watcher. For new monitoring work after November 9, 2026, Microsoft directs you to Database Hub in Fabric instead.

**Portal steps (existing watcher):** open the watcher resource, check **Status** on the **Overview** page (a new watcher isn't started automatically, so select **Start** if needed), then open **Dashboards** to see the estate view and drill into a target.

- **[📖 Monitor Azure SQL workloads with database watcher](https://learn.microsoft.com/en-us/azure/azure-sql/database-watcher-overview)** - What it collects, supported targets, data store options, limits and the retirement notice
- **[📖 Azure SQL Analytics (preview)](https://learn.microsoft.com/en-us/previous-versions/azure/azure-monitor/insights/azure-sql)** - Archived page for the older Log Analytics solution

### Automatic Tuning
- **Create index:** Automatically create missing indexes
- **Drop index:** Remove unused indexes
- **Force plan:** Force good execution plans

## High Availability and Disaster Recovery

### High Availability Options
- **Built-in HA (SQL Database):** 99.99% SLA
- **Business Critical tier:** Zone-redundant, read replicas
- **Always On Availability Groups (SQL VM):** Multi-replica HA

### Backup and Recovery
- **Automated backups:** Full, differential, transaction log
- **Retention:** 7-35 days (SQL Database)
- **Long-term retention:** Up to 10 years
- **Point-in-time restore:** Recover to specific moment
- **Geo-restore:** Restore from geo-redundant backup

### Geo-Replication
- **Active geo-replication:** Up to 4 readable secondaries
- **Auto-failover groups:** Automatic failover with transparent connection string

## Automation

### PowerShell
```powershell
# Create SQL Database
New-AzSqlDatabase -ResourceGroupName "myResourceGroup" `
    -ServerName "myserver" `
    -DatabaseName "mydatabase" `
    -Edition "Standard" `
    -RequestedServiceObjectiveName "S1"
```

### Azure CLI
```bash
# Create SQL Database
az sql db create \
    --resource-group myResourceGroup \
    --server myserver \
    --name mydatabase \
    --service-objective S1
```

### T-SQL Automation
```sql
-- Create SQL Agent job (Managed Instance/SQL VM)
EXEC msdb.dbo.sp_add_job @job_name = 'DailyBackup'

-- Index maintenance
CREATE INDEX idx_name ON table_name(column_name) WITH (ONLINE = ON)
```

## Best Practices

### Deployment
1. **Choose right option:** PaaS vs IaaS based on requirements
2. **Use resource groups:** Organize related resources
3. **Tag resources:** For cost tracking and management
4. **Plan capacity:** Right-size compute and storage
5. **Test in dev/test environments:** Before production

### Security
1. **Use Azure AD authentication**
2. **Implement least privilege** access
3. **Enable TDE** for all databases
4. **Use private endpoints** for isolation
5. **Regular security audits**

### Performance
1. **Monitor query performance** regularly
2. **Implement proper indexing** strategy
3. **Update statistics** regularly
4. **Enable automatic tuning** where appropriate
5. **Use appropriate service tier** for workload

### HADR
1. **Regular backup testing**
2. **Document recovery procedures**
3. **Implement geo-replication** for critical databases
4. **Monitor replication lag**
5. **Practice failover procedures**

## Study Tips

### Key Concepts
- Deployment options and when to use each
- Authentication and authorization methods
- Encryption options (TDE, Always Encrypted)
- HA/DR solutions
- Performance tuning techniques
- Backup and restore strategies

### Common Scenarios
1. **Lift-and-shift migration** → SQL Managed Instance
2. **New cloud app** → Azure SQL Database
3. **Specific SQL Server version needed** → SQL VM
4. **Global availability needed** → Geo-replication
5. **Performance issues** → Query Store analysis

### Remember
- SQL Database = Fully managed, cloud-native
- Managed Instance = Near-complete compatibility
- SQL VM = Full control, more management
- Always use Azure AD authentication when possible
- Enable TDE by default
- Test backup restores regularly
