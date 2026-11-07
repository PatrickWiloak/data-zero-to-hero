---
last-updated: 2026-11-07
applies-to: Databricks Data Analyst Associate exam guide (August 2026 edition, read 2026-11-07)
difficulty: beginner
reading-time: 15 min
---

# Section 8: Data Modeling with Databricks SQL

Weighted **5%**, about two questions. Two objectives: apply star, snowflake and data vault modeling to analytical workloads, and understand how those models align with the Medallion Architecture.

## Table of Contents

1. [Star and snowflake schemas](#star-and-snowflake-schemas)
2. [Data vault](#data-vault)
3. [Medallion alignment](#medallion-alignment)
4. [How the exam tests this section](#how-the-exam-tests-this-section)
5. [Key terms](#key-terms)

---

## Star and snowflake schemas

Analytical models separate *what happened* from *the context around it*. A **fact table** holds events with numeric measures (an order line: quantity, amount) and foreign keys. **Dimension tables** hold descriptive attributes (customer, product, date). In a **star schema** each dimension is one wide, denormalized table joined directly to the fact table, so a query needs one join per dimension and is easy to read and fast. A **snowflake schema** normalizes dimensions further (product splits into product and category tables), saving storage and avoiding repeated values at the price of more joins. For BI on Databricks SQL, star schemas are the usual default because analysts and BI tools handle them well.

```sql
-- Star-schema query shape. Same join-and-group shape as the queries that ran on Spark 4.3.0 locally; not run with these table names.
SELECT c.region, SUM(f.amount) AS revenue
FROM fact_orders f
JOIN dim_customer c ON f.customer_id = c.id
GROUP BY c.region;
```

## Data vault

**Data vault** is a modeling style built for integrating many sources over time with full history and auditability. It has three table types: **hubs** (unique business keys, such as customer number), **links** (relationships between hubs, such as customer-places-order) and **satellites** (descriptive attributes with load timestamps, so changes are kept as history). It is flexible when sources change often and is usually used in a raw or integration layer, with star-schema marts built on top for reporting.

## Medallion alignment

The Medallion architecture organizes data into **bronze** (raw, unvalidated source data, appended for reprocessing and audit, no modeling), **silver** (cleaned, deduplicated, validated; also where nested data is flattened or normalized) and **gold** (aggregated, aligned with business logic, modeled for reporting using a dimensional model). That gives the mapping the guide wants: a data vault, if you use one, fits between bronze and silver or within silver as the integrated history; star or snowflake dimensional models belong in gold. (The docs page says gold uses a dimensional model; the vault placement is common practice that the page does not state.)

**[📖 Medallion architecture](https://docs.databricks.com/aws/en/lakehouse/medallion)** - bronze, silver and gold and where dimensional modeling fits
**[📖 Metric views](https://docs.databricks.com/aws/en/metric-views/)** - governed measures that can model star and snowflake schemas with multi-level joins

## How the exam tests this section

- **Pick the schema:** simple, fast BI means star; normalized dimensions mean snowflake; many changing sources with history means data vault.
- **Pick the layer:** star schemas are gold; raw history is bronze; cleaned and conformed is silver.

## Key terms

- **Fact table** - a table of events with numeric measures and keys that point to dimensions.
- **Dimension table** - a table of descriptive attributes used to group and filter facts.
- **Star schema** - a model with one fact table joined directly to denormalized dimension tables.
- **Snowflake schema** - a model where dimension tables are normalized into further related tables.
- **Data vault** - a modeling approach with hubs, links and satellites that preserves history across many sources.
- **Hub** - a data vault table holding the unique business keys of an entity.
- **Medallion architecture** - a layering of data into bronze, silver and gold, raw to business-ready.
- **Bronze** - the raw layer that keeps source data as ingested for audit and reprocessing.
- **Gold** - the business-ready layer, aggregated and modeled for reporting.
