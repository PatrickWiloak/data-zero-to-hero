---
last-updated: 2026-11-07
applies-to: MongoDB Associate Data Modeler exam guide v2.0, docs read 2026-11-07
difficulty: advanced
reading-time: 16 min
---

# Domain 8: Monitoring and Evolving Data Models

Weighted **27%**, the largest domain, about a quarter of the exam. Despite its title, the guide's two objectives are about recognizing and fixing design problems: apply the Inheritance, Computed and Extended Reference patterns (8.1), and identify and resolve schema anti-patterns such as unbounded arrays and bloated documents (8.2). Commands were run on MongoDB 8.3.13 on 2026-11-07.

## Table of Contents

1. [How a model is monitored and evolved](#how-a-model-is-monitored-and-evolved)
2. [Inheritance pattern](#inheritance-pattern)
3. [Computed pattern](#computed-pattern)
4. [Extended Reference pattern](#extended-reference-pattern)
5. [Anti-pattern: unbounded arrays](#anti-pattern-unbounded-arrays)
6. [Anti-pattern: bloated documents](#anti-pattern-bloated-documents)
7. [Other anti-patterns in the docs](#other-anti-patterns-in-the-docs)
8. [How the exam tests this domain](#how-the-exam-tests-this-domain)
9. [Key terms](#key-terms)

---

## How a model is monitored and evolved

Models are not finished at launch. Access patterns change, data grows, and what was fine at 1,000 documents hurts at 100 million. The docs give two tools that spot problems: "The MongoDB Atlas Performance Advisor (available for M10 clusters or higher) and MongoDB Compass Performance Insights identify schema design anti-patterns in your database." In Atlas, open the cluster, then Performance Advisor, then "Explore Recommendations" on the Improve Schema card. Fixes then use the patterns in this note and in domain 7.

**[📖 Schema Design Anti-Patterns](https://www.mongodb.com/docs/manual/data-modeling/design-antipatterns/)** - the five anti-patterns and how Performance Advisor and Compass report them

## Inheritance pattern

Use it "when your documents are mostly similar and you want to keep them in the same collection so they can be read together." A parent entity holds the common fields, and children add their own. The docs example is a bookstore: `book` is the parent (title, author, rating, genres) and `ebook`, `printed` and `audiobook` are children with fields such as `pages` and `download_url` or `narrators` and `duration`. All live in one `books` collection with a `product_type` field. It "improves performance for queries that need to access all books, regardless of type."

It belongs to the Polymorphic Data family: documents in one collection that differ in fields. Use the plain Polymorphic pattern when shapes vary freely; use Inheritance when a clear parent-child shape exists.

**[📖 Polymorphic Data](https://www.mongodb.com/docs/manual/data-modeling/design-patterns/polymorphic-data/)** - Polymorphic versus Inheritance with scenarios
**[📖 Use the Inheritance Pattern](https://www.mongodb.com/docs/manual/data-modeling/design-patterns/polymorphic-data/inheritance-schema-pattern/)** - the `books` example

## Computed pattern

If a value is requested often and is expensive to derive, compute it once and store it. The docs: "If reads are significantly more common than writes, the computed pattern reduces the frequency of data computation." You can recompute on each write that changes the source data, or in a periodic job. The caveat: with periodic updates "the returned computed value is not guaranteed to be exact."

Here is a periodic recompute with `$group` and `$merge` (run on MongoDB 8.3.13), turning per-screening rows into per-movie totals:

```javascript
db.screenings.aggregate([
  {$group: {_id: "$movie_id", viewers: {$sum: "$num_viewers"}, revenue: {$sum: "$revenue"}}},
  {$merge: {into: "movie_totals"}}
])
db.movie_totals.find()
```

Output:

```text
[ { _id: 1, viewers: 444, revenue: 4440 } ]
```

Reads now fetch one small document instead of summing every screening. Computed and Approximation are siblings under "Handle Computed Values"; Approximation also trades exactness for fewer writes ([note 01](01-advanced-schema-design-patterns-and-anti-patterns.md)).

**[📖 Store Computed Data](https://www.mongodb.com/docs/manual/data-modeling/design-patterns/computed-values/computed-schema-pattern/)** - when reads outnumber writes, and the exactness caveat
**[📖 Handle Computed Values](https://www.mongodb.com/docs/manual/data-modeling/design-patterns/handle-computed-values/)** - computed versus approximation use cases

## Extended Reference pattern

You referenced customers from orders, but now every order read needs a `$lookup` just for the customer name. MongoDB's pattern article: "Instead of duplicating all of the information on the customer, we only copy the fields we access frequently." You keep the reference and add a small copy of the hot fields.

```javascript
db.orders.updateMany({userId: 1}, {$set: {customer: {name: "Ann"}}})
db.orders.findOne({_id: 10})
```

Output:

```text
{ _id: 10, userId: 1, total: 59.9, customer: { name: 'Ann' } }
```

The order can now be displayed with no join. Two rules from the article: copy fields that rarely change ("a user_id and a person's name are good options"), and only the data needed. Duplication is the cost, and sometimes it is a benefit: the shipping address at the time of the order is more correct in the order than the customer's current address.

**[📖 Building with Patterns: The Extended Reference Pattern](https://www.mongodb.com/company/blog/building-with-patterns-the-extended-reference-pattern)** - reducing repeated joins by copying frequently read fields
**[📖 Reduce $lookup Operations](https://www.mongodb.com/docs/manual/data-modeling/design-antipatterns/reduce-lookup-operations/)** - the anti-pattern this pattern answers

## Anti-pattern: unbounded arrays

An array that can grow forever. The docs: it "might exceed the 16MB BSON document size limit" and can "strain application resources and decrease index performance". The fix is to "use subsetting and referencing to bound arrays". The bookstore example only shows three reviews per book, so it keeps those in the book and the rest elsewhere. You saw the working mechanism in [note 07](07-schema-design-optimization.md) (Subset with `$slice`), where five reviews produced a three-element array plus five rows in a `reviews` collection.

Other solutions: reference from the many side (each review holds a `bookId`), or the Bucket pattern with a bounded bucket.

**[📖 Avoid Unbounded Arrays](https://www.mongodb.com/docs/manual/data-modeling/design-antipatterns/unbounded-arrays/)** - the problem and the subset and reference fixes

## Anti-pattern: bloated documents

Large documents full of fields that are not read together. The docs: "Storing data fields that are related to each other but not accessed together can create bloated documents that lead to excessive RAM and bandwidth usage." The working set no longer fits in RAM, so queries hit disk. Fix: "restructure your schema with smaller documents and use document references to separate fields that aren't returned together." In the example, a homepage that shows only title, author and cover image gets a `mainBookInfo` collection, with the rest in `additionalBookDetails`.

Bloat and unbounded arrays often arrive together: the large array is the bloat.

**[📖 Bloated Documents](https://www.mongodb.com/docs/manual/data-modeling/design-antipatterns/bloated-documents/)** - the working set explanation and the two-collection split

## Other anti-patterns in the docs

| Anti-pattern | Definition (from the docs) |
|---|---|
| Avoid Unbounded Arrays | An array can grow to exceed the document size limit and hurt index performance |
| Reduce Number of Collections | Too many collections can decrease storage engine performance |
| Remove Unnecessary Indexes | Extra indexes use disk space and degrade write performance |
| Reduce Bloated Documents | Excessively large documents degrade common queries |
| Reduce `$lookup` Operations | Too many `$lookup` operations increase complexity and reduce performance |

Note the tension with domain 4: referencing is sometimes right, but if you keep joining the same data, embed it or copy hot fields (Extended Reference).

## How the exam tests this domain

This is the biggest slice, so expect scenarios, not definitions. A usual shape: a short description of a symptom, then four candidate fixes.

- **Symptoms to anti-pattern.** Document growing with every event: unbounded array. Slow list pages that need only a few fields: bloated documents. Every read does a `$lookup`: reduce lookups with Extended Reference or embedding. Slow writes with many seldom-used indexes: unnecessary indexes.
- **Pick the pattern:** shared parent fields with type-specific extras, Inheritance. Expensive repeated calculation with reads over writes, Computed. Hot fields from a related entity, Extended Reference.
- **Watch the cost:** Computed may be stale; Extended Reference duplicates data; Inheritance needs a type field and queries that account for it.
- **Tooling:** Performance Advisor (M10 and up) and Compass Performance Insights report schema anti-patterns.

## Key terms

- **Inheritance pattern** - storing similar documents with a shared parent shape and type-specific fields in one collection.
- **Polymorphic data** - data in a single collection that varies in fields or data types.
- **Computed pattern** - storing a derived value ahead of time so reads need only one lookup.
- **Extended Reference pattern** - copying a few frequently read fields from a related document into the referencing one.
- **Unbounded array** - an array whose size can grow without limit, risking the 16 MiB document cap and index slowdowns.
- **Bloated document** - a document containing many fields that are not accessed together, enlarging the working set.
- **Performance Advisor** - the Atlas tool (M10 clusters or higher) that recommends schema and index improvements.
- **Schema anti-pattern** - a design choice that looks reasonable but degrades performance as data or traffic grows.
