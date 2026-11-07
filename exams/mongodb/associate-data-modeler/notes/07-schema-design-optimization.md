---
last-updated: 2026-11-07
applies-to: MongoDB Associate Data Modeler exam guide v2.0, docs read 2026-11-07
difficulty: advanced
reading-time: 16 min
---

# Domain 7: Schema Design Optimization

Weighted **16%**. Two objectives: apply schema optimization patterns for read and write performance (7.1: Single Collection, Subset, Bucket, Outlier, and Archive) and understand how modeling choices affect a sharded environment (7.2). Commands were run on MongoDB 8.3.13 on 2026-11-07.

## Table of Contents

1. [Single Collection pattern](#single-collection-pattern)
2. [Subset pattern](#subset-pattern)
3. [Bucket pattern](#bucket-pattern)
4. [Outlier pattern](#outlier-pattern)
5. [Archive pattern](#archive-pattern)
6. [Modeling in a sharded cluster](#modeling-in-a-sharded-cluster)
7. [How the exam tests this domain](#how-the-exam-tests-this-domain)
8. [Key terms](#key-terms)

---

## Single Collection pattern

Use an array of references to "group related documents of different types into a single collection". The docs say it is especially useful for many-to-many relationships "with only one copy of the data". Its defining traits: frequently accessed documents live together in one collection, relationships are stored as pointers inside each document, and an index on the pointer field lets you fetch related documents "in a single query without the use of database join operations".

The docs recommend considering it for "high-velocity operations, low-latency requirements, and frequent write operations", because smaller documents allow smaller writes than large embedded ones. Their example keeps students and classes as separate document types in one collection, linked by a `links` array with `doc_type` and `target` fields, so a class schedule change is written once rather than into every student.

**[📖 Single Collection Pattern](https://www.mongodb.com/docs/manual/data-modeling/design-patterns/single-collection/)** - characteristics, when to use it and the students and classes example

## Subset pattern

Problem: a document holds a big array whose tail is rarely read, such as every review for a product. The working set (frequently accessed data and indexes kept in RAM) grows, and once it exceeds RAM, performance drops as data is read from disk.

Solution: keep only the part the application shows in the main document and move the rest to another collection. MongoDB's pattern article: "In the Product collection, we'll only keep the ten most recent reviews", with the full set in a separate Reviews collection. The cost: you must manage the subset, and fetching older data takes extra trips to the database.

You can maintain the subset in one update with `$push` and `$slice`. Run on MongoDB 8.3.13 (keep the last three of five reviews, while all five go to `reviews`):

```javascript
db.books.updateOne({_id: 1}, {$push: {recentReviews: {$each: [{n: i}], $slice: -3}}})
```

Result after five reviews (reduced):

```text
{ _id: 1, title: 'B', recentReviews: [ { n: 3 }, { n: 4 }, { n: 5 } ] }
reviews total: 5
```

The `$slice` trick is the mechanism used in this run; it is not stated in the pattern article, which only describes the pattern.

**[📖 Building with Patterns: The Subset Pattern](https://www.mongodb.com/company/blog/building-with-patterns-the-subset-pattern)** - working set, the ten-recent-reviews example and the trade-off
**[📖 Avoid Unbounded Arrays](https://www.mongodb.com/docs/manual/data-modeling/design-antipatterns/unbounded-arrays/)** - the manual's use of the subset pattern to bound an array

## Bucket pattern

Problem: a long series of similar small items about one entity (stock trades, sensor readings). One document per item means many documents and clumsy pagination. Solution: "The bucket pattern separates long series of data into distinct objects." Group by a natural unit, a time window or the page size the application shows. The docs add that it helps pagination: "grouping your data based on the elements that your application shows per page", and that time series collections "apply the bucket pattern automatically".

```javascript
db.readings.insertOne({sensor: "s1", day: "2026-11-07", count: 0, readings: []})
// three times:
db.readings.updateOne({sensor: "s1", day: "2026-11-07"}, {$push: {readings: {t: i, v: 20 + i}}, $inc: {count: 1}})
```

Result (reduced):

```text
{ sensor: 's1', day: '2026-11-07', count: 3, readings: [ { t: 0, v: 20 }, { t: 1, v: 21 }, { t: 2, v: 22 } ] }
```

One document per sensor per day holds the whole bucket plus a running count. Buckets must stay bounded (a day, a fixed number of readings); an unbounded bucket becomes the unbounded-array problem again.

**[📖 Group Data](https://www.mongodb.com/docs/manual/data-modeling/design-patterns/group-data/)** - when to bucket versus outlier
**[📖 Group Data with the Bucket Pattern](https://www.mongodb.com/docs/manual/data-modeling/design-patterns/group-data/bucket-pattern/)** - stock-trade example and pagination

## Outlier pattern

Problem: most documents are a similar size, but a few are drastically larger. The docs: a drastically different document "can cause performance issues for common queries". Their example is a book-sales collection where a bestseller's `customers_purchased` array balloons. Solution: pick a threshold (50 sales in the example), keep up to the threshold in the main document, and store the extras in a separate collection. Common queries no longer drag a giant document along.

Pros: typical queries stay fast, and edge cases do not disrupt the UI. Con: "more complex logic to handle updates."

**[📖 Group Data with the Outlier Pattern](https://www.mongodb.com/docs/manual/data-modeling/design-patterns/group-data/outlier-pattern/)** - threshold, pros and cons

## Archive pattern

Old data that is rarely read slows down the data people do read. The pattern moves it elsewhere. Options listed: external file storage such as Amazon S3, a separate cheaper cluster, or a separate collection on the same cluster. The docs say "in most cases, moving data to external file storage is the best option in terms of cost and performance." Tips: archive documents should use an embedded model (so related parts stay consistent in time), and the document's age should sit in a single field. Atlas Online Archive moves infrequently accessed data to a MongoDB-managed read-only federated database instance on cloud object storage.

**[📖 Archive Pattern](https://www.mongodb.com/docs/manual/data-modeling/design-patterns/archive/)** - storage options, tips and the five-year sales example

## Modeling in a sharded cluster

Sharding splits a collection across shards by a shard key. The model you choose decides which fields are available as a key and how queries route.

The shard key page lists the factors for choosing one:

- **Cardinality** - the number of distinct values. It "determines the maximum number of chunks the balancer can create". A key like `continent` has cardinality 7, so at most 7 chunks. Low cardinality limits scaling.
- **Frequency** - how often a value occurs. If most documents share a few values, those chunks become a bottleneck and may become indivisible.
- **Monotonic change** - an always-increasing key sends all inserts to the chunk holding `MaxKey`, making one shard the write bottleneck.
- **Query patterns** - the key should help common queries.

You can change the shard key later: refine it with `refineCollectionShardKey`, or reshard with `reshardCollection` (MongoDB 5.0 and later). The docs also note a document's shard key value can be updated unless the key is `_id`.

How this ties back to schema design (objective 7.2): a field that is a good shard key must exist in your documents and be indexed, so the model must carry it. And patterns that keep working-set documents small, like Subset, reduce what each shard must hold in RAM. The fetched pages state the shard key factors; the link from Subset to sharded clusters comes from the exam guide's wording and the working-set explanation, since the pages do not discuss it directly.

**[📖 Shard Keys](https://www.mongodb.com/docs/manual/core/sharding-shard-key/)** - what a shard key is and how to change it
**[📖 Choose a Shard Key](https://www.mongodb.com/docs/manual/core/sharding-choose-a-shard-key/)** - cardinality, frequency, monotonic change

## How the exam tests this domain

- **Match the symptom to the pattern.** Rarely read tail of an array bloating documents: Subset. Long series about one entity: Bucket. A few giant documents among normal ones: Outlier. Old data slowing hot data: Archive. Many-to-many with one copy of the data and frequent small writes: Single Collection.
- **Know each pattern's price.** Subset needs extra reads for the rest. Outlier needs update logic. Archive needs a second place to look.
- **Shard key traps:** low cardinality, a hot value, and a monotonically increasing key like a timestamp.

## Key terms

- **Single Collection pattern** - storing related documents of different types in one collection, linked by arrays of references.
- **Subset pattern** - keeping only the frequently used part of a large related set in the main document and the rest in another collection.
- **Working set** - the frequently accessed data and indexes that MongoDB keeps in RAM.
- **Bucket pattern** - grouping a long series of related items into fewer, larger documents.
- **Outlier pattern** - separating unusually large documents or data from the typical ones.
- **Archive pattern** - moving rarely accessed old data to separate storage.
- **Shard key** - the field or fields that decide how a collection's documents are distributed across shards.
- **Cardinality** - the number of distinct values a shard key can take, which caps the number of chunks.
- **Monotonic shard key** - a key whose values only increase or decrease, sending inserts to a single chunk.
