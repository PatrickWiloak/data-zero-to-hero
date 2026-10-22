# Databricks Data + AI Platform Administrator Accreditation

> **This is an accreditation, not a proctored certification.** Databricks lists it under "Accreditations" on its [certification page](https://www.databricks.com/learn/training/certification), separate from the Certified Associate and Professional exams, and calls it "the final assessment in the Databricks Data + AI Platform Administrator specialty learning pathway". This guide used to describe a "Databricks Certified Lakehouse Platform Administrator" exam with 45 questions, 90 minutes, $200 and two-year validity; those details were wrong and are corrected below from the [official accreditation page](https://www.databricks.com/learn/certification/platform-administrator-accreditation), read 2026-10-22. The directory keeps its old name so existing links keep working.

## Exam Overview

The accreditation tests your knowledge of managing and administering the Databricks platform with Unity Catalog. Databricks' page names the areas: user and group administration; compute resource management (clusters, SQL warehouses, MLflow, orchestration); and governance and security (permissions, data governance, secrets, Delta Sharing, model governance). The learning pathway's modules cover platform administration fundamentals, identity administration, workspace administration and security, compute resource administration, data administration, and CI/CD administration.

**Exam Details:**
- **Credential:** Databricks Accredited Platform Administrator (badge and certificate)
- **Number of Questions:** 25 multiple-choice questions
- **Duration:** Unlimited time
- **Passing Score:** Not stated on the current page; Databricks' 2022 launch post gave 80%
- **Cost:** Free for Databricks customers and partners
- **Delivery:** Online, not proctored, in Databricks Academy
- **Validity:** 1 year
- **Prerequisites:** The "Platform Administration with Databricks" course in the Platform Administrator learning pathway; working knowledge of Python or Scala is expected but not tested

## Exam Domains

Databricks publishes topic areas but **no domain weights** for this accreditation. The topic areas below are this guide's grouping of the official areas; the percentages this guide used to show were not Databricks figures and have been removed.

### Topic area: Workspace Administration
- Configure and manage Databricks workspaces
- Manage workspace settings and features
- Implement workspace-level governance
- Manage workspace objects and permissions

**Key Concepts:**
- Workspace deployment and configuration
- Account-level vs workspace-level administration
- Workspace settings (features, defaults, quotas)
- Notebook and repo management
- Secret management (Databricks secrets, secret scopes)
- Workspace object permissions
- Databricks CLI and REST API for administration
- Terraform provider for Databricks
- Workspace monitoring and audit logs
- Cost management and budgets

### Topic area: Identity and Access
- Configure identity providers and SSO
- Manage users, groups, and service principals
- Implement role-based access control
- Manage authentication and authorization

**Key Concepts:**
- Identity federation with cloud identity providers
- SCIM provisioning for user/group sync
- Single Sign-On (SSO) configuration (SAML, OIDC)
- Service principals for automation
- Personal access tokens and OAuth
- Account-level groups vs workspace-level groups
- Workspace assignment for users and groups
- Entitlements (workspace access, cluster creation, SQL access)
- Admin roles (account admin, workspace admin, metastore admin)

### Topic area: Data Management
- Administer Unity Catalog
- Manage data access and permissions
- Configure external data sources
- Implement data lifecycle management

**Key Concepts:**
- Unity Catalog metastore setup and configuration
- Catalog and schema management
- Managed vs external tables and volumes
- Storage credentials and external locations
- GRANT and REVOKE administration
- Data lineage configuration
- Delta Sharing for data sharing
- System tables for monitoring and auditing
- Catalog federation
- Information schema queries

### Topic area: Cluster and Warehouse Management
- Configure and manage compute resources
- Implement cluster policies
- Manage SQL warehouses
- Optimize compute costs

**Key Concepts:**
- All-purpose clusters vs job clusters
- Cluster policies for governance and cost control
- Cluster pool configuration for faster startup
- Autoscaling configuration and behavior
- Spot instance and preemptible VM strategies
- SQL warehouse types (classic, pro, serverless)
- SQL warehouse sizing and scaling
- Serverless compute configuration
- Runtime version management
- Init scripts and cluster libraries

### Topic area: Security
- Implement network security
- Configure data encryption
- Manage compliance requirements
- Implement security best practices

**Key Concepts:**
- VPC/VNet peering and private connectivity
- Private Link and private endpoints
- Network security groups and firewall rules
- IP access lists
- Customer-managed keys (CMK) for encryption
- Encryption at rest and in transit
- Compliance certifications (SOC 2, HIPAA, FedRAMP)
- Audit log configuration and analysis
- Secure cluster connectivity (no public IPs)
- Data exfiltration prevention

## Key Concepts to Master

### Unity Catalog Administration
- Metastore-workspace binding
- Three-level namespace management
- Permission inheritance model
- Storage credential lifecycle
- External location configuration
- System tables for auditing

### Compute Governance
- Cluster policy design and enforcement
- Cost control through policies and quotas
- Resource allocation strategies
- Serverless compute benefits and configuration
- Pool management for startup optimization

### Security Architecture
- Network isolation patterns
- Data encryption layers
- Identity federation design
- Audit logging and compliance
- Secret management best practices

## Study Approach

### Phase 1: Foundation (Week 1-2)
1. Review Databricks workspace architecture
2. Learn identity and access management concepts
3. Understand Unity Catalog administration model
4. Study basic cluster configuration

### Phase 2: Core Skills (Week 3-4)
1. Practice workspace administration tasks
2. Configure identity providers and SCIM
3. Set up Unity Catalog with proper permissions
4. Design cluster policies and SQL warehouses

### Phase 3: Exam Prep (Week 5-6)
1. Take practice exams and review incorrect answers
2. Focus on identity, Unity Catalog permissions and compute governance
3. Review security and network configuration
4. Practice scenario-based administration questions

## Study Resources

- **[Databricks Academy](https://www.databricks.com/learn)** - Platform administration learning path
- **[📖 Accreditation page](https://www.databricks.com/learn/certification/platform-administrator-accreditation)** - Official details and topic areas
- **[Administration Guide](https://docs.databricks.com/en/admin/index.html)** - Admin documentation
- **[Unity Catalog Documentation](https://docs.databricks.com/en/data-governance/unity-catalog/index.html)** - Unity Catalog guide
- **[Security Guide](https://docs.databricks.com/en/security/index.html)** - Security best practices

## Tips for Success

1. **Workspace and account administration** - know which settings live in the account console and which in the workspace
2. **Identity management is critical** - SCIM, SSO, and service principals are key topics
3. **Unity Catalog administration** - Not just using UC, but administering it
4. **Cluster policies** - Know how to create and enforce governance
5. **Security layers** - Understand network, identity, data, and audit security
6. **Know the admin console** - Understand what can be configured and where
7. **Cloud provider differences** - Be aware of AWS/Azure/GCP differences
8. **Cost management** - Cluster policies, serverless, and monitoring costs
9. **API and CLI** - Know Databricks CLI and REST API for automation
10. **Scenario-based thinking** - Questions often present real admin challenges

## File Index

| File | Description |
|------|-------------|
| [fact-sheet.md](fact-sheet.md) | Comprehensive reference with documentation links |
| [practice-plan.md](practice-plan.md) | Week-by-week study schedule with checkboxes |
| [scenarios.md](scenarios.md) | Exam-style scenarios with solutions |
| [strategy.md](strategy.md) | Study phases, resources, and exam tactics |
| [notes/01-workspace-administration.md](notes/01-workspace-administration.md) | Workspace configuration and management |
| [notes/02-identity-access.md](notes/02-identity-access.md) | Identity, SSO, and access control |
| [notes/03-data-cluster-management.md](notes/03-data-cluster-management.md) | Unity Catalog, clusters, policies, and warehouses |
| [notes/04-security.md](notes/04-security.md) | Network and data security |
