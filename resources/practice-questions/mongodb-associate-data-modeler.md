---
last-updated: 2026-11-07
---

# MongoDB Associate Data Modeler - Practice Questions

25 scenario-based questions for MongoDB Associate Data Modeler prep, tagged with the eight domains of Exam Guide v2.0. Domain weights: Monitoring and Evolving Data Models 27%, Schema Design Optimization 16%, Advanced Schema Design Patterns and Anti-patterns 15%, Indexing Design Fundamentals 15%, Relational to Document Model 12%, Fundamentals of Data Transformation 6%, Performance Tools and Techniques 6%, CRUD Operations 3%. The bank follows those weights: 7, 4, 4, 4, 3, 1, 1, 1 questions.

> **Cert page:** [exams/mongodb/associate-data-modeler/](../../exams/mongodb/associate-data-modeler/)

> Original scenario questions for study. They contain no vendor exam content.

---

### Question 1 - Domain 1: Advanced Schema Design Patterns & Anti-patterns
**Scenario:** A page-view counter document is updated thousands of times per minute, but the dashboard only needs a figure that is roughly right. Writes are the bottleneck. Which approach fits best?

A. Add an index on the counter field so each update finds its document faster
B. Split the counter into one document per view and sum them when the dashboard loads
C. Embed the counter in a user document so it is read with related data
D. Update the stored value only when it has moved by a fixed step, such as 1,000

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** This is the Approximation pattern: update at a larger granularity, giving fewer writes at the cost of precision. An index adds write cost. One document per view multiplies writes. Embedding does not reduce update frequency.

**Key Concept:** exams/mongodb/associate-data-modeler/notes/01-advanced-schema-design-patterns-and-anti-patterns.md
</details>

---

### Question 2 - Domain 1: Advanced Schema Design Patterns & Anti-patterns
**Scenario:** A contact collection stores phone numbers as top-level fields. The team adds a `contacts` array for new records but cannot rewrite 400 million old documents without downtime. What should they do?

A. Add a `schemaVersion` field and let old and new shapes coexist there
B. Drop the collection and reload every document in the new shape
C. Disable schema validation permanently so old shapes are never checked
D. Move every document to a new database first, then rename it afterwards

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Schema Versioning lets several schema versions live in one collection, identified by `schemaVersion`, which avoids large migrations. The other choices require downtime or do not address the shapes.

**Key Concept:** exams/mongodb/associate-data-modeler/notes/01-advanced-schema-design-patterns-and-anti-patterns.md
</details>

---

### Question 3 - Domain 1: Advanced Schema Design Patterns & Anti-patterns
**Scenario:** An insurer must keep every revision of a policy and query the current policy quickly. Policies are updated rarely. Which pattern fits?

A. Bucket, grouping each policy's revisions into one document per month
B. Outlier, moving the policies with the most revisions to a separate collection
C. Document Versioning, keeping old versions in a separate collection
D. Approximation, saving a revision only when a policy changes by a set amount

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Document Versioning retains older versions in a separate collection from current data, so current-data queries are unaffected. Bucket groups series, Outlier isolates large documents, Approximation reduces updates.

**Key Concept:** exams/mongodb/associate-data-modeler/notes/01-advanced-schema-design-patterns-and-anti-patterns.md
</details>

---

### Question 4 - Domain 1: Advanced Schema Design Patterns & Anti-patterns
**Scenario:** After adding a `schemaVersion` pattern, the same field now appears at the top level in old documents and inside a sub-document in new ones. What is a likely consequence?

A. MongoDB rejects the new documents because the field path differs from the old ones
B. Queries on that field may need more than one index to stay fast
C. The field cannot be queried at all in documents that use the nested shape
D. The collection must be sharded to keep both shapes searchable

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** The versioning docs warn that a field stored at different levels in different documents may need multiple indexes to support queries on it.

**Key Concept:** exams/mongodb/associate-data-modeler/notes/01-advanced-schema-design-patterns-and-anti-patterns.md
</details>

---

### Question 5 - Domain 2: Fundamentals of Data Transformation
**Scenario:** A collection has an order with `items: []`. A pipeline runs `{$unwind: "$items"}` with no options. What happens to that order?

A. It is output once with `items` set to null
B. It is output once with `items` set to an empty array
C. The pipeline stops with an error about the empty array
D. It is dropped, so no document is output for it

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** By default `$unwind` outputs no document when the array is null, missing or empty. Set `preserveNullAndEmptyArrays: true` to keep it.

**Key Concept:** exams/mongodb/associate-data-modeler/notes/02-fundamentals-of-data-transformation.md
</details>

---

### Question 6 - Domain 3: CRUD Operations
**Scenario:** A developer needs to add one element to an array field and change a status field on one order in a single operation. Which call is correct?

A. `updateOne` with `$set` and `$push` in one update document
B. `replaceOne` with the full order document, including the new array element and status
C. `insertOne` with the new element, then a separate write for the status
D. `deleteOne` then `insertOne` with the full modified order

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** `updateOne` with update operators changes only the named fields, and the write is atomic on that single document. `replaceOne` would replace the whole document.

**Key Concept:** exams/mongodb/associate-data-modeler/notes/03-crud-operations.md
</details>

---

### Question 7 - Domain 4: Relational to Document Model
**Scenario:** An application always displays an author's address together with the author's name and rarely updates the address. Which model fits?

A. Reference the address in a separate collection and join on each read
B. Store each address field as its own collection keyed by author id
C. Embed the address in the author document as a nested sub-document
D. Use `$lookup` on every read to attach the address from elsewhere

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Data read together and rarely changing suits embedding: one read, atomic updates, no joins. Referencing adds lookups for no benefit.

**Key Concept:** exams/mongodb/associate-data-modeler/notes/04-relational-to-document-model.md
</details>

---

### Question 8 - Domain 4: Relational to Document Model
**Scenario:** Product details such as a manufacturer's contact information are shared by thousands of products and change often. Which is best?

A. Embed the manufacturer in every product so each read needs no join
B. Reference the manufacturer from each product by its `_id`
C. Copy the manufacturer into a log file that products read from
D. Store all products in a single array inside one manufacturer document

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Frequently changing, shared data is a reference use case: update it in one place instead of thousands of copies.

**Key Concept:** exams/mongodb/associate-data-modeler/notes/04-relational-to-document-model.md
</details>

---

### Question 9 - Domain 4: Relational to Document Model
**Scenario:** A collection has validation `validationLevel: "moderate"`. An existing document already violates the rules. What happens when it is updated?

A. The update always fails until the document is fixed
B. The collection is locked until the invalid document is corrected
C. Validation switches to `strict` for the collection after the update
D. The update is not required to pass validation, since the document already failed

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** At `moderate`, inserts and updates to already-valid documents are checked, but updates to existing documents that already fail the rules are not required to pass.

**Key Concept:** exams/mongodb/associate-data-modeler/notes/04-relational-to-document-model.md
</details>

---

### Question 10 - Domain 5: Performance Tools and Techniques
**Scenario:** The profiler is at its default setting. What is the state of profiling and the slow-operation threshold?

A. Off, with a default threshold of 100 ms
B. Level 2, with a 50 ms threshold
C. Level 1, with a 100 ms threshold
D. Off, with no threshold setting

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** The profiler is off by default (level 0) and the default `slowms` threshold is 100 milliseconds, which applies once you enable level 1.

**Key Concept:** exams/mongodb/associate-data-modeler/notes/05-performance-tools-and-techniques.md
</details>

---

### Question 11 - Domain 6: Indexing Design Fundamentals
**Scenario:** A query filters on `status` (equality), sorts by `created`, and filters `amount` with `$gt`. Which compound index follows the ESR guideline?

A. `{amount: 1, created: 1}`
B. `{created: 1, amount: 1, status: 1}`
C. `{status: 1, created: 1, amount: 1}`
D. `{status: 1, amount: 1, created: 1, _id: 1}`

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** ESR orders keys Equality, Sort, Range: status, created, amount. This avoids an in-memory sort.

**Key Concept:** exams/mongodb/associate-data-modeler/notes/06-indexing-design-fundamentals.md
</details>

---

### Question 12 - Domain 6: Indexing Design Fundamentals
**Scenario:** A compound index is `{item: 1, location: 1, stock: 1}`. Which query cannot use it as a prefix?

A. Filter on `item` only, the first indexed field
B. Filter on `location` only, with no condition on `item`
C. Filter on `item` and `location`, the first two fields
D. Filter on `item`, `location` and `stock`, all three fields

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Prefixes are the leading fields. `location` alone is not a prefix, so the index cannot be used efficiently for that query.

**Key Concept:** exams/mongodb/associate-data-modeler/notes/06-indexing-design-fundamentals.md
</details>

---

### Question 13 - Domain 6: Indexing Design Fundamentals
**Scenario:** A query's range predicate is extremely selective and returns only a handful of documents, and the query also sorts. What does the ESR guidance allow?

A. Never place a range field before a sort field in any index
B. Use a text index in place of the compound index
C. Drop the equality field to leave room for the range
D. Place the range before the sort (ERS) to reduce documents to sort

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** The guideline says that if the range predicate is very selective, put it before the sort fields (ERS), because the in-memory sort then handles few documents.

**Key Concept:** exams/mongodb/associate-data-modeler/notes/06-indexing-design-fundamentals.md
</details>

---

### Question 14 - Domain 6: Indexing Design Fundamentals
**Scenario:** A team adds twelve indexes 'just in case' to a write-heavy collection. What is the most likely cost?

A. Degraded write performance and extra disk use per index
B. Smaller documents, because indexed fields leave the data
C. Faster inserts, because the server picks the best index per write
D. Automatic sharding once the index count passes ten

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Unnecessary indexes consume disk space and degrade write performance because each write must update them.

**Key Concept:** exams/mongodb/associate-data-modeler/notes/06-indexing-design-fundamentals.md
</details>

---

### Question 15 - Domain 7: Schema Design Optimization
**Scenario:** A product document holds every review ever written, but pages show only the ten newest. The working set no longer fits in RAM. Which pattern helps?

A. Approximation, saving review counts only at coarse steps
B. Schema Versioning, tagging each product with a version number
C. Subset, keeping the ten newest reviews in the product and the rest elsewhere
D. Inheritance, storing reviews as a subtype of the product

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Subset keeps the frequently used part (recent reviews) in the main document and the rest in another collection, shrinking the working set. The trade-off is extra reads for older reviews.

**Key Concept:** exams/mongodb/associate-data-modeler/notes/07-schema-design-optimization.md
</details>

---

### Question 16 - Domain 7: Schema Design Optimization
**Scenario:** An IoT service stores one document per sensor reading and paginates in groups of 100. Queries and storage are inefficient. Which pattern fits?

A. Outlier, moving sensors with extreme reading counts elsewhere
B. Bucket, grouping readings into bounded documents
C. Archive, moving old readings to cold storage
D. Single Collection, storing sensors and readings under one type field

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Bucket groups a long series into fewer documents, which helps pagination. Keep buckets bounded, for example by time window.

**Key Concept:** exams/mongodb/associate-data-modeler/notes/07-schema-design-optimization.md
</details>

---

### Question 17 - Domain 7: Schema Design Optimization
**Scenario:** Ninety-nine percent of book documents have under 50 sales entries, but a bestseller has 400,000, slowing common queries. Which pattern fits?

A. Approximation: record sales counts in coarse steps instead of exactly
B. Document Versioning: keep each past sales state in a separate collection
C. Schema Versioning: tag the bestseller with a new schema version number
D. Outlier: keep entries up to a threshold, move the rest elsewhere

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Outlier isolates unusual documents so common queries are not affected; the cost is more complex update logic.

**Key Concept:** exams/mongodb/associate-data-modeler/notes/07-schema-design-optimization.md
</details>

---

### Question 18 - Domain 7: Schema Design Optimization
**Scenario:** You are choosing a shard key. Which field is most likely to create a write hotspot?

A. A monotonically increasing timestamp, e.g. creation time
B. A hashed user id, which spreads inserts across shards
C. A compound of region and user id, which spreads by location
D. A high-cardinality random id, such as a UUID

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** A monotonic key sends all new inserts to the chunk with the MaxKey upper bound, making one shard the bottleneck.

**Key Concept:** exams/mongodb/associate-data-modeler/notes/07-schema-design-optimization.md
</details>

---

### Question 19 - Domain 8: Monitoring and Evolving Data Models
**Scenario:** Every order read requires a `$lookup` just to show the customer's name, which almost never changes. What is the best fix?

A. Add more indexes to orders so each `$lookup` runs faster
B. Embed the entire customer history in each order
C. Use Extended Reference: copy the customer's name into the order
D. Remove the customer reference and store only an email in the order

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Extended Reference copies only the few frequently read, stable fields, removing the join. The cost is duplication.

**Key Concept:** exams/mongodb/associate-data-modeler/notes/08-monitoring-and-evolving-data-models.md
</details>

---

### Question 20 - Domain 8: Monitoring and Evolving Data Models
**Scenario:** A bookstore stores ebooks, printed books and audiobooks. They share title and author, with format-specific extra fields, and are often listed together. Which pattern fits?

A. Bucket, grouping all formats of a title into one document per month
B. Inheritance, in one collection with a type field
C. Archive, moving older formats to a separate cold collection
D. Three databases, one per format, joined by `$lookup`

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Inheritance groups similar entities with shared parent fields and type-specific child fields in one collection, so queries across all types are fast.

**Key Concept:** exams/mongodb/associate-data-modeler/notes/08-monitoring-and-evolving-data-models.md
</details>

---

### Question 21 - Domain 8: Monitoring and Evolving Data Models
**Scenario:** An analytics page computes total revenue per movie by summing millions of screening rows on every request. Reads far outnumber writes. What should you do?

A. Add a second `$sum` stage to speed up the aggregation
B. Embed all screenings into each user document
C. Disable the page during peak hours
D. Store the computed totals and refresh them on writes or on a schedule

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** The Computed pattern stores derived values ahead of time. With periodic refresh the value may not be exact.

**Key Concept:** exams/mongodb/associate-data-modeler/notes/08-monitoring-and-evolving-data-models.md
</details>

---

### Question 22 - Domain 8: Monitoring and Evolving Data Models
**Scenario:** A `reviews` array inside each book document grows with every review and some documents near 16 MiB. Which description and fix fit?

A. Unbounded array; subset the array and reference the rest
B. Bloated document; add an index on the reviews array to speed reads
C. Outlier pattern deficiency; ignore it because few documents are large
D. Normal; shard the collection so each shard holds fewer reviews

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** An array with no limit is an unbounded array anti-pattern. Bound it with the Subset pattern or references.

**Key Concept:** exams/mongodb/associate-data-modeler/notes/08-monitoring-and-evolving-data-models.md
</details>

---

### Question 23 - Domain 8: Monitoring and Evolving Data Models
**Scenario:** A list page shows only title, author and cover image, but every query returns the full description, reviews and metadata. Which anti-pattern is this?

A. Too many collections, so queries must touch several of them
B. Unnecessary indexes, which slow every list query
C. Bloated documents, with fields not read together
D. Excess `$lookup` operations joining title, author and cover

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Fields not accessed together stored in one large document bloat it and the working set. Split rarely used fields into another collection.

**Key Concept:** exams/mongodb/associate-data-modeler/notes/08-monitoring-and-evolving-data-models.md
</details>

---

### Question 24 - Domain 8: Monitoring and Evolving Data Models
**Scenario:** Which tool is described as identifying schema design anti-patterns on Atlas clusters of size M10 or higher?

A. Database profiler level 0, set per database
B. Performance Advisor, which flags schema anti-patterns on Atlas
C. `mongodump`, which exports collection data to BSON
D. `collMod`, which changes collection options such as validation

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** The Atlas Performance Advisor (M10 or higher) and Compass Performance Insights identify schema design anti-patterns.

**Key Concept:** exams/mongodb/associate-data-modeler/notes/08-monitoring-and-evolving-data-models.md
</details>

---

### Question 25 - Domain 8: Monitoring and Evolving Data Models
**Scenario:** An application runs many `$lookup` operations between the same two collections on its hottest queries. Which statement is accurate?

A. `$lookup` is free on large collections because it uses a cache
B. `$lookup` removes the need for indexes on the joined collection
C. `$lookup` only runs on sharded collections spread across shards
D. Frequent `$lookup` can slow queries; consider embedding hot fields

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** The docs call too many `$lookup` operations an anti-pattern and suggest reducing reliance on them through an embedded model; Extended Reference is a middle path.

**Key Concept:** exams/mongodb/associate-data-modeler/notes/08-monitoring-and-evolving-data-models.md
</details>

---
