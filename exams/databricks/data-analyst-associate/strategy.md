---
last-updated: 2026-11-07
applies-to: Databricks exam guide (August 2026 edition), read 2026-11-07
---

# Databricks Data Analyst Associate - Study Strategy

Weights: Queries 20%, Dashboards 16%, Analyzing 15%, Genie 12%, Platform 11%, Managing 8%, Securing 8%, Importing 5%, Modeling 5%. Spend hours roughly in that proportion, but keep an hour for the small sections because they are cheap marks.

## Study Approach

### Phase 1: Data and queries (Weeks 1-3)
- Read notes 01 to 03 in one sitting; spend the time on note 04. In Free Edition write each join type, a LEFT ANTI, a UNION versus UNION ALL, an `approx_count_distinct`, and a time travel query.
- Read note 05 and look at Query History and a query profile for something you ran.

### Phase 2: Dashboards and Genie (Weeks 3-4)
- Build a dashboard with two datasets, a Date Range parameter linked to a filter, and a text widget; publish and subscribe yourself.
- Create a SQL alert with a threshold and test the condition.
- Create a Genie agent, add instructions, an example query and a benchmark question.

### Phase 3: Model, secure, review (Week 5)
- Notes 08 and 09, then the practice bank in two sittings.

## Exam Tactics

1. Read the **last sentence first**: what exactly is asked (the object, the clause, the setting).
2. For SQL questions, **run the query in your head** on three rows, including a NULL.
3. For "which feature" questions, **match the verb**: schedule/notify (alert), see (dashboard), ask in English (Genie), precompute (materialized view).
4. **Multiple select:** the stem says how many; verify each choice alone.
5. **Time:** 45 questions in 90 minutes; flag and return.

## Common Traps

- WHERE where HAVING is needed; missing GROUP BY.
- INNER JOIN when the story says keep all customers.
- UNION when duplicates must be kept.
- Thinking Genie feedback retrains the agent.
- Forgetting USE CATALOG and USE SCHEMA.
- Assuming time travel reaches the full 30 days of history; VACUUM and the 7-day deleted-file retention limit it.
