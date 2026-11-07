---
last-updated: 2026-11-07
applies-to: Databricks Data Analyst Associate exam guide (August 2026 edition, read 2026-11-07)
difficulty: beginner
reading-time: 15 min
---

# Section 9: Securing Data

Weighted **8%**, about four questions. Three objectives: use Unity Catalog roles and sharing settings to secure workspace objects; understand the three-level namespace; and apply storage and management best practices, including table ownership and PII protection.

## Table of Contents

1. [Privileges, ownership and inheritance](#privileges-ownership-and-inheritance)
2. [The three-level namespace](#the-three-level-namespace)
3. [Protecting PII](#protecting-pii)
4. [Sharing settings](#sharing-settings)
5. [How the exam tests this section](#how-the-exam-tests-this-section)
6. [Key terms](#key-terms)

---

## Privileges, ownership and inheritance

Unity Catalog uses `GRANT` and `REVOKE` on securable objects:

```sql
-- Syntax per the Databricks docs; not run (needs Unity Catalog).
GRANT USE CATALOG ON CATALOG main TO `analysts`;
GRANT USE SCHEMA, SELECT ON SCHEMA main.gold TO `analysts`;
REVOKE SELECT ON SCHEMA main.gold FROM `analysts`;
```

To read a table a user needs `USE CATALOG` on the catalog, `USE SCHEMA` on the schema and `SELECT` on the table. Grants on a catalog or schema flow down to what is inside it. Grant to **groups**, not individuals, so access follows team membership. Principals with special characters are wrapped in backticks.

Every securable object has an **owner**, who holds all privileges on it, including the right to grant them. Others who can grant are the owner of the containing catalog or schema, anyone with `MANAGE` on the object, and metastore admins. Transfer ownership with `ALTER <type> <name> OWNER TO <principal>`. Good practice: have groups or service principals own production tables, not a person who might leave.

**[📖 Manage privileges](https://docs.databricks.com/aws/en/data-governance/unity-catalog/manage-privileges/)** - GRANT syntax, USE privileges, ownership and who can grant

## The three-level namespace

Every object is addressed `catalog.schema.object` (table, view, volume, function or model). Securables above that, such as storage credentials, external locations, connections and shares, sit directly under the metastore. The layers are also security boundaries: you can open a whole catalog to a group, or a single schema, or one table, and the narrower the better.

**[📖 Unity Catalog overview](https://docs.databricks.com/aws/en/data-governance/unity-catalog/)** - securable objects and the namespace

## Protecting PII

Personally identifiable information needs layered protection:

1. **Tag it.** Mark columns and tables containing PII with governed tags so people and policies can find them.
2. **Restrict it.** Grant the minimum; separate PII into its own schema with narrower grants.
3. **Mask or filter it.** Unity Catalog **column masks** show a redacted value to people who should not see the real one, and **row filters** hide rows by user. Genie and dashboards respect these.
4. **Own it properly.** Groups own sensitive tables; avoid personal ownership.
5. **Trace it.** Lineage shows every downstream copy of a PII column, and audit logs record access.

Managed tables help the storage side: Unity Catalog manages the files, so there is no separate bucket to leave open, and dropped data is deleted after the recovery period.

## Sharing settings

Workspace objects such as dashboards and Genie agents have their own sharing permissions (CAN VIEW, CAN RUN, CAN EDIT, CAN MANAGE). Dashboard publishing offers shared or individual data permissions. With individual permissions, each viewer's own Unity Catalog access applies; with shared, the publisher's. For data shared externally, Delta Sharing shares are read-only, and deleting a share or a recipient immediately removes access.

## How the exam tests this section

- **Least privilege wording:** the narrowest grant that works, to a group, on the lowest object.
- **Missing gate:** a user with `SELECT` who still cannot query is missing `USE CATALOG` or `USE SCHEMA`.
- **Ownership:** owners can grant; production tables should be owned by groups or service principals.
- **PII:** tags to find, masks and filters to protect, lineage to trace.

## Key terms

- **GRANT** - the SQL statement that gives a principal a privilege on a securable object.
- **REVOKE** - the SQL statement that removes a privilege from a principal.
- **USE CATALOG** - the privilege required on a catalog before objects inside it can be accessed.
- **USE SCHEMA** - the privilege required on a schema before objects inside it can be accessed.
- **Owner** - the principal that holds all privileges on a securable object, including the right to grant them.
- **MANAGE** - a privilege that lets a user manage privileges on an object without owning it.
- **PII** - personally identifiable information, which needs tagging, restriction and masking.
- **Principal** - a user, group or service principal that can be granted privileges.
