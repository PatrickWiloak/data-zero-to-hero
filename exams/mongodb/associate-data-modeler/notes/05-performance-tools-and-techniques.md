---
last-updated: 2026-11-07
applies-to: MongoDB Associate Data Modeler exam guide v2.0, docs read 2026-11-07
difficulty: intermediate
reading-time: 10 min
---

# Domain 5: Performance Tools and Techniques

Weighted **6%**. Two objectives: find and fix slow queries with the profiler, slow query log and `explain()` (5.1), and understand how indexes affect query performance (5.2). Commands were run on MongoDB 8.3.13 on 2026-11-07, on a 20,000-document collection.

## Table of Contents

1. [Two questions to ask](#two-questions-to-ask)
2. [explain](#explain)
3. [The database profiler](#the-database-profiler)
4. [Putting an index in the picture](#putting-an-index-in-the-picture)
5. [How the exam tests this domain](#how-the-exam-tests-this-domain)
6. [Key terms](#key-terms)

---

## Two questions to ask

Tuning always starts with two questions. First, which operations are slow? The profiler and the slow query log answer that. Second, why is this operation slow? `explain()` answers that. Skipping the first wastes time on queries nobody runs; skipping the second leads to guessing at indexes.

## explain

Chain `.explain("executionStats")` to a query to run it and get the plan plus real numbers. The fields to read, per the docs' analysis tutorial:

- `queryPlanner.winningPlan.stage` - `COLLSCAN` means a collection scan; an `IXSCAN` stage means an index was used.
- `executionStats.nReturned` - documents returned.
- `executionStats.totalKeysExamined` - index entries read. Zero means no index was used.
- `executionStats.totalDocsExamined` - documents read. The healthy shape is `nReturned` close to `totalDocsExamined`.

Run on a collection of 20,000 events with no useful index:

```javascript
db.events.find({status: "open", ts: {$gte: 5000}}).sort({user: 1}).explain("executionStats")
```

Reduced output:

```text
no index:  { docsExamined: 20000, keysExamined: 0,    nReturned: 3750, sortInMemory: true }
```

Twenty thousand documents were read to return 3,750, and the sort happened in memory. That ratio is the signal.

**[📖 Analyze Query Plan](https://www.mongodb.com/docs/manual/tutorial/analyze-query-plan/)** - reading `COLLSCAN`, `nReturned`, `totalKeysExamined`
**[📖 Explain Results](https://www.mongodb.com/docs/manual/reference/explain-results/)** - the full field reference

## The database profiler

The profiler records operations to a capped `system.profile` collection in each profiled database. It is off by default (level 0). Level 1 records operations slower than the `slowms` threshold (default 100 milliseconds) or matching a filter; level 2 records every operation.

```javascript
db.getProfilingStatus()
db.setProfilingLevel(1, {slowms: 1})
db.events.find({user: "u7"}).sort({ts: -1}).toArray()
db.setProfilingLevel(0)
db.system.profile.find({ns: "shop.events", op: "query"}).sort({ts: -1}).limit(1)
```

Output (reduced):

```text
{ was: 0, slowms: 100, slowinprogms: Long('5000'), sampleRate: 1, ok: 1 }
{ planSummary: 'COLLSCAN', docsExamined: 20000, nreturned: 40, keysExamined: 0 }
```

The first line confirms the defaults: profiler off, 100 ms threshold. The profile entry shows a `COLLSCAN` reading 20,000 documents for 40 results, a clear index candidate. The profiler adds overhead, so the usual practice is level 1 with a sensible threshold rather than level 2 in production (the docs describe the levels; the production advice is general practice).

**[📖 Database Profiler](https://www.mongodb.com/docs/manual/tutorial/manage-the-database-profiler/)** - levels, `slowms`, `system.profile`
**[📖 Database Profiler Output](https://www.mongodb.com/docs/manual/reference/database-profiler/)** - fields such as `planSummary` and `docsExamined`

## Putting an index in the picture

Objective 5.2: indexes are central to query execution. Without one MongoDB must scan the collection. With a matching index it reads only the relevant keys. Re-running the same query with a compound index on `{status, user, ts}` (built to follow the ESR guideline, see [note 06](06-indexing-design-fundamentals.md)):

```text
ESR index: { docsExamined: 3750, keysExamined: 3875, nReturned: 3750, sortInMemory: false }
```

Documents examined fell from 20,000 to 3,750 and the in-memory sort disappeared. To judge index effectiveness over time, use the profiler and monitoring tools: operations that still show `COLLSCAN`, or `docsExamined` far above `nreturned`, are not served by an index.

## How the exam tests this domain

- **Which tool finds the slow query, and which explains it?** Profiler or slow log finds; `explain()` explains.
- **Read an explain snippet:** `COLLSCAN` plus large `totalDocsExamined` versus small `nReturned` means missing or unusable index.
- **Know the defaults:** profiler off, `slowms` 100.

## Key terms

- **`explain()`** - a method that returns the query plan and, with `executionStats`, real execution numbers.
- **`COLLSCAN`** - a plan stage meaning MongoDB read every document in the collection.
- **`IXSCAN`** - a plan stage meaning an index was scanned.
- **`totalDocsExamined`** - the number of documents read while executing a query.
- **Database profiler** - a facility that records operations to `system.profile` based on a level and the `slowms` threshold.
- **`slowms`** - the duration threshold, 100 ms by default, above which operations count as slow.
