---
last-updated: 2026-11-07
applies-to: Databricks Data Analyst Associate exam guide (August 2026 edition, read 2026-11-07)
difficulty: beginner
reading-time: 20 min
---

# Section 2: Managing Data

Weighted **8%**, about four questions. Three objectives: discover, query and manage certified datasets with Unity Catalog; tag a data asset with governed tags and view its lineage in Catalog Explorer; and clean data in SQL by removing invalid rows and handling missing values.

## Table of Contents

1. [Discovering and trusting data](#discovering-and-trusting-data)
2. [Tags and lineage](#tags-and-lineage)
3. [Cleaning data in SQL](#cleaning-data-in-sql)
4. [How the exam tests this section](#how-the-exam-tests-this-section)
5. [Key terms](#key-terms)

---

## Discovering and trusting data

Analysts spend as much time asking "which table should I use?" as writing queries. Catalog Explorer is the answer: browse catalogs and schemas, search, read descriptions and sample data, look at owners and permissions, and check lineage. A **certified** dataset is one the owning team has marked as trustworthy, so it is the one to prefer over a random copy. You need `USE CATALOG`, `USE SCHEMA` and `SELECT` to query a table, and the **BROWSE** privilege to see an object's metadata without reading its data (the lineage docs require at least `BROWSE` on the parent catalog to view lineage).

**[📖 Manage privileges](https://docs.databricks.com/aws/en/data-governance/unity-catalog/manage-privileges/)** - GRANT and REVOKE, ownership and who can grant

## Tags and lineage

Tags make assets findable and enforce policy. To add one: open the object in Catalog, find **Tags** on the Overview page, choose **Add tags**, and pick a key and value. Governed tags (lock icon) appear under the **Governed** header; you need permission to assign them, and the value must come from the tag's policy. Regular tags need ownership of the object or the `APPLY TAG` privilege. In SQL, `SET TAG` works on Databricks Runtime 16.1 and above, per the docs.

**[📖 Tags in Unity Catalog](https://docs.databricks.com/aws/en/data-governance/unity-catalog/tags)** - governed tags and how to apply them

Lineage is on the table's **Lineage** tab. External systems that Unity Catalog does not control, such as a Salesforce source or a Tableau dashboard, can be registered as **external metadata** objects and linked to Unity Catalog objects so they appear in the same graph. This is what "external lineage" means in the guide.

**[📖 Data lineage](https://docs.databricks.com/aws/en/data-governance/unity-catalog/data-lineage)** - the Lineage tab, column lineage and external lineage

## Cleaning data in SQL

Bad data comes in a few flavours: missing values, impossible values, duplicates and inconsistent labels. SQL gives you a short toolkit.

| Problem | Tool |
|---|---|
| Missing value should become a default | `COALESCE(col, 'unknown')` |
| Rows with a required field missing should go | `WHERE col IS NOT NULL` |
| Impossible values (negative age, future date) | `WHERE age BETWEEN 0 AND 120` |
| Duplicate rows | `SELECT DISTINCT`, or `ROW_NUMBER()` over a key to keep one |
| Inconsistent text | `TRIM`, `LOWER`, `UPPER` |

```sql
-- Clean customers into a new table. The COALESCE and IS NOT NULL pattern ran on Spark 4.3.0 locally.
-- CREATE OR REPLACE TABLE on Unity Catalog was not run.
CREATE OR REPLACE TABLE main.silver.customers_clean AS
SELECT id,
       TRIM(name)                  AS name,
       COALESCE(region, 'unknown') AS region
FROM main.bronze.customers
WHERE id IS NOT NULL;
```

Two details trip people up. First, `NULL` is not equal to anything, including another `NULL`, so `WHERE region = NULL` returns nothing; use `IS NULL`. Second, `COUNT(col)` ignores nulls while `COUNT(*)` counts rows, so the choice changes your answer when data is missing. Cleaning in a new table (rather than in place) keeps the raw copy for audit; `CREATE OR REPLACE TABLE` replaces the contents while keeping the table's history and privileges, and is covered again in Section 4.

## How the exam tests this section

- **Where do I look?** Lineage and tags are in Catalog Explorer, on the object.
- **Which tag?** Governed when values and who may assign must be controlled.
- **Cleaning scenarios** ask which clause removes or replaces bad values: `WHERE ... IS NOT NULL` removes, `COALESCE` replaces.
- **NULL logic:** equality with NULL never matches; counts skip nulls.

## Key terms

- **Certified dataset** - a table the owning team has marked as trusted for general use.
- **BROWSE** - a Unity Catalog privilege that lets a user see an object's metadata without reading its data.
- **Governed tag** - a tag whose allowed values and assignment permissions are enforced by an administrator.
- **External lineage** - lineage for sources and consumers outside Unity Catalog, registered as external metadata objects.
- **COALESCE** - a SQL function that returns the first non-null value in its argument list.
- **NULL** - the SQL marker for a missing value, which never equals anything, even another NULL.
