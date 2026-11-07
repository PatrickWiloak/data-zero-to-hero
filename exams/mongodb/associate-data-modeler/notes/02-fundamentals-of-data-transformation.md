---
last-updated: 2026-11-07
applies-to: MongoDB Associate Data Modeler exam guide v2.0, docs read 2026-11-07
difficulty: intermediate
reading-time: 12 min
---

# Domain 2: Fundamentals of Data Transformation

Weighted **6%**. Two objectives: define aggregation and explain how data moves through a pipeline (2.1), and build and analyze pipelines with core stages (2.2). The pipeline below was run on MongoDB 8.3.13 on 2026-11-07.

## Table of Contents

1. [A pipeline is an assembly line](#a-pipeline-is-an-assembly-line)
2. [The core stages](#the-core-stages)
3. [A worked pipeline](#a-worked-pipeline)
4. [Stage order matters](#stage-order-matters)
5. [Reading a pipeline with explain](#reading-a-pipeline-with-explain)
6. [How the exam tests this domain](#how-the-exam-tests-this-domain)
7. [Key terms](#key-terms)

---

## A pipeline is an assembly line

An aggregation pipeline is a list of stages. Documents enter the first stage, each stage transforms them, and the output of one stage is the input of the next. The docs: "Each stage performs an operation on the input documents ... The documents that a stage outputs are then passed to the next stage in the pipeline." A stage need not output one document per input document: some filter documents out and some create new ones. The same stage can appear several times, except `$out`, `$merge` and `$geoNear`.

A pipeline run with `aggregate()` does not change your data, unless it contains `$merge` or `$out`.

**[📖 Aggregation Pipeline](https://www.mongodb.com/docs/manual/core/aggregation-pipeline/)** - stages, expressions and the rule that only `$merge` and `$out` write

## The core stages

The guide names eight stages. One sentence each, from the reference pages:

- **`$match`** filters documents with a query predicate; matches go to the next stage.
- **`$unwind`** "deconstructs an array field from the input documents to output a document for each element". By default it drops documents where the array is null, missing or empty; set `preserveNullAndEmptyArrays: true` to keep them.
- **`$group`** combines documents that share a group key into one document per key, with accumulators such as `$sum`. It does not order its output.
- **`$project`** reshapes documents: include, exclude or rename fields.
- **`$sort`** orders documents.
- **`$limit`** keeps only the first N documents.
- **`$lookup`** is a left outer join to a collection in the same database. It adds an array field holding the matching foreign documents.
- **`$set`** adds fields and keeps all existing ones. It is an alias for `$addFields`.

**[📖 $unwind](https://www.mongodb.com/docs/manual/reference/operator/aggregation/unwind/)** - definition and `preserveNullAndEmptyArrays`
**[📖 $group](https://www.mongodb.com/docs/manual/reference/operator/aggregation/group/)** - group key in `_id`, accumulators, unordered output
**[📖 $lookup](https://www.mongodb.com/docs/manual/reference/operator/aggregation/lookup/)** - left outer join; unindexed lookups on large collections are slow
**[📖 $set (aggregation)](https://www.mongodb.com/docs/manual/reference/operator/aggregation/set/)** - adds fields; alias for `$addFields`
**[📖 $match](https://www.mongodb.com/docs/manual/reference/operator/aggregation/match/)** - filtering stage

## A worked pipeline

Question: which product earns the most revenue from paid orders? Orders embed their line items, and product names live in a second collection.

```javascript
db.orders.aggregate([
  {$match: {status: "paid"}},
  {$unwind: "$items"},
  {$lookup: {from: "products", localField: "items.sku", foreignField: "_id", as: "p"}},
  {$set: {line: {$multiply: ["$items.qty", "$items.price"]}, productName: {$first: "$p.name"}}},
  {$group: {_id: "$productName", revenue: {$sum: "$line"}}},
  {$sort: {revenue: -1}},
  {$limit: 5},
  {$project: {_id: 0, product: "$_id", revenue: 1}}
]).toArray();
```

Output (mongosh, MongoDB 8.3.13, 2026-11-07):

```text
[ { revenue: 20, product: 'Book' }, { revenue: 15, product: 'Pen' } ]
```

(mongosh prints each field on its own line; shortened here.) Walk it through: `$match` keeps two paid orders, `$unwind` turns three line items into three documents, `$lookup` attaches product names, `$set` computes each line total, `$group` sums per product, `$sort` and `$limit` rank, `$project` tidies the output.

The `$unwind` behavior is easy to test. Order 3 had an empty `items` array:

```text
unwind without preserve, order 3 (empty items): 0
with preserveNullAndEmptyArrays: 1
```

## Stage order matters

Because each stage feeds the next, order changes results and cost. Filter early with `$match` so later stages see fewer documents; the docs add that early stages can use indexes: "For early stages in your aggregation pipeline, consider indexing the query fields." A `$limit` before a `$sort` returns a different answer from a `$sort` before a `$limit`. A `$group` before a `$match` on a field the group removed finds nothing.

**[📖 Aggregation Pipeline Optimization](https://www.mongodb.com/docs/manual/core/aggregation-pipeline-optimization/)** - which stages can use indexes and how the optimizer reorders

## Reading a pipeline with explain

Objective 2.2 asks you to "interpret and analyze pipeline performance using explain() plans". Pass `{explain: true}` or call `explain` on the aggregate. The things to read are the same as for a find: whether the first stage used an index scan or a collection scan, and how many documents and keys were examined. Domain 5 covers the fields in detail ([note 05](05-performance-tools-and-techniques.md)).

## How the exam tests this domain

- **Predict the output of a short pipeline** by walking stages in order: count documents after each stage.
- **`$unwind` on an empty or missing array** drops the document unless `preserveNullAndEmptyArrays` is true.
- **`$set` versus `$project`:** `$set` keeps existing fields; `$project` lists what stays.
- **`$lookup` cost:** it works, but frequent lookups on big unindexed collections are slow, which is a modeling signal (see [note 08](08-monitoring-and-evolving-data-models.md)).

## Key terms

- **Aggregation pipeline** - an ordered list of stages where each stage's output feeds the next.
- **Stage** - one step in a pipeline, such as `$match` or `$group`.
- **Accumulator** - an expression in `$group` (for example `$sum`) that combines values across a group.
- **Group key** - the value in `$group`'s `_id` that decides which documents share an output document.
- **`$lookup`** - a stage that performs a left outer join with another collection in the same database.
- **`$unwind`** - a stage that outputs one document per element of an array field.
