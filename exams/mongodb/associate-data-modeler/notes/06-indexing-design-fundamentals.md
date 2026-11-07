---
last-updated: 2026-11-07
applies-to: MongoDB Associate Data Modeler exam guide v2.0, docs read 2026-11-07
difficulty: intermediate
reading-time: 14 min
---

# Domain 6: Indexing Design Fundamentals

Weighted **15%**. Three objectives: apply indexing best practices (6.1: index types and costs, the ESR guideline, index prefixes), optimize indexes (6.2: sort order, cardinality, selectivity), and verify with query analysis tools (6.3). The experiment below was run on MongoDB 8.3.13 on 2026-11-07 against 20,000 documents.

## Table of Contents

1. [Why indexes matter and what they cost](#why-indexes-matter-and-what-they-cost)
2. [Index types](#index-types)
3. [Compound indexes and prefixes](#compound-indexes-and-prefixes)
4. [The ESR guideline](#the-esr-guideline)
5. [Sort order, cardinality and selectivity](#sort-order-cardinality-and-selectivity)
6. [Proving it with explain](#proving-it-with-explain)
7. [How the exam tests this domain](#how-the-exam-tests-this-domain)
8. [Key terms](#key-terms)

---

## Why indexes matter and what they cost

An index is a sorted structure over some fields that lets MongoDB jump to matching documents instead of scanning everything. Step four of the schema design process is "create indexes to support common query patterns".

They are not free. The docs' anti-pattern page says unnecessary indexes "consume additional disk space and can degrade write performance", because every insert and update must also maintain each index. So the discipline is: one index per important query shape, remove the rest.

**[📖 Create Indexes (schema design process)](https://www.mongodb.com/docs/manual/data-modeling/schema-design-process/create-indexes/)** - indexes as the last step of the process
**[📖 Schema Design Anti-Patterns](https://www.mongodb.com/docs/manual/data-modeling/design-antipatterns/)** - "Remove Unnecessary Indexes"

## Index types

From the index types page:

| Type | Purpose |
|---|---|
| Single field | Sorts data from one field |
| Compound | Sorts data from several fields; supports queries on the first field or any prefix |
| Multikey | Indexes values stored in arrays |
| Wildcard | Collections with flexible schemas where field names differ or are unknown |
| Geospatial | Queries on geospatial coordinate data |
| Hashed | Indexes the hash of a value; supports hashed sharding |
| Text | Supports `$text` queries on string content |

Matching type to workload is the exam skill: array field to filter on, multikey; unpredictable attribute names, wildcard; a hashed shard key, hashed.

**[📖 Index Types](https://www.mongodb.com/docs/manual/core/indexes/index-types/)** - every index type with its purpose

## Compound indexes and prefixes

A compound index is a B-tree sorted by the first field, then the second within it, and so on, so field order is everything. A single compound index can have up to 32 fields and at most one hashed field.

Prefixes are the leading fields of the index. For `{ item: 1, location: 1, stock: 1 }` the prefixes are `{ item: 1 }` and `{ item: 1, location: 1 }`. The index supports queries on `item`; on `item` and `location`; and on all three. It can also help a query on `item` and `stock` because `item` is a prefix, "however, the index is not as efficient as `{ item: 1, stock: 1 }`." A query on `location` alone cannot use it as a prefix.

**[📖 Compound Indexes](https://www.mongodb.com/docs/manual/core/indexes/index-types/index-compound/)** - field limit, field order, hashed field and index prefixes

## The ESR guideline

ESR tells you how to order the keys of a compound index: **Equality, Sort, Range.**

- **Equality** fields (exact matches) go first. The docs: "Placing equality fields first keeps the remaining index fields in sorted order."
- **Sort** fields go next, so the index delivers rows already ordered and no in-memory sort is needed.
- **Range** fields (`$gt`, `$lt`, `$gte` and similar) go last.

Two caveats the page states. "If avoiding in-memory sorts is critical, place sort fields before range fields (ESR)." But "If your range predicate in the query is very selective, then put it before sort fields (ERS)", because filtering to a few documents first makes an in-memory sort cheap. Use `hint()` to force an index while you test.

**[📖 The ESR (Equality, Sort, Range) Guideline](https://www.mongodb.com/docs/manual/tutorial/equality-sort-range-guideline/)** - the rule, the ERS exception and examples

## Sort order, cardinality and selectivity

**Sort order.** Each key is ascending (`1`) or descending (`-1`). The docs: the direction of a field in a compound index "is relevant only if there is a sort on multiple fields in opposite directions", and a compound index supports a sort that matches its order or its exact reverse. A leaderboard sorted by `score` descending then `username` ascending needs `{score: -1, username: 1}` (or its reverse).

**Selectivity.** A selective predicate matches few documents. "The more selective the equality matches, the more efficient the indexed query." That is why a field like `status` with four values makes a weak leading key on its own, and why selective ranges can justify ERS.

**Cardinality** is the number of distinct values of a field. High-cardinality fields (user id) tend to be selective; low-cardinality fields (a four-value status) are not by themselves. The fetched index pages use "selective" rather than defining cardinality for indexes; the shard key page defines cardinality for sharding (note 07). Treat the cardinality-selectivity link here as the working explanation, not a quotation.

**[📖 Compound Index Sort Order](https://www.mongodb.com/docs/manual/core/indexes/index-types/index-compound/sort-order/)** - when direction matters

## Proving it with explain

Objective 6.3 says use explain plans to confirm the expected index is used. The query finds open events from `ts` 5000 onward and sorts by `user`: equality on `status`, range on `ts`, sort on `user`. Two indexes were built and compared:

```javascript
db.events.createIndex({status: 1, ts: 1, user: 1})   // E, R, S  (range before sort)
db.events.createIndex({status: 1, user: 1, ts: 1})   // E, S, R  (the ESR order)
```

Reduced output from `explain("executionStats")` (MongoDB 8.3.13, 2026-11-07):

```text
no index:                   docsExamined: 20000  keysExamined: 0     nReturned: 3750  sortInMemory: true
E,R,S index (status,ts,user): docsExamined: 3750   keysExamined: 3750  nReturned: 3750  sortInMemory: true
ESR index (status,user,ts):   docsExamined: 3750   keysExamined: 3875  nReturned: 3750  sortInMemory: false
```

Both indexes cut the documents read from 20,000 to 3,750. Only the ESR order removed the in-memory sort, because the sort field sits before the range field. Notice it examined slightly more keys (3,875 against 3,750): ESR trades a few extra key reads for no sort stage, which is exactly the "if avoiding in-memory sorts is critical" case. The second index was selected with `hint()` for a controlled comparison.

## How the exam tests this domain

- **Order the keys:** given a query with an equality, a sort and a range, choose the ESR order; if the range is stated as very selective, ERS can be right.
- **Prefix questions:** which queries can this compound index serve? Only leading-field combinations.
- **Direction questions:** opposite-direction multi-field sorts need a matching index direction.
- **Costs:** more indexes mean slower writes and more disk.
- **Verification:** `IXSCAN` and a low docs-examined-to-returned ratio confirm the index works; `COLLSCAN` does not.

## Key terms

- **Compound index** - an index over several fields, sorted by the first, then the second within it, and so on.
- **Index prefix** - a leading subset of a compound index's fields that the index can serve on its own.
- **ESR guideline** - order compound index keys as Equality, Sort, Range.
- **Multikey index** - an index over array values.
- **Wildcard index** - an index for collections whose field names vary or are unknown.
- **Hashed index** - an index over the hash of a field, used for hashed sharding.
- **Selectivity** - how few documents a predicate matches; more selective means more efficient indexed queries.
- **`hint()`** - a cursor method that forces a particular index, useful when testing.
