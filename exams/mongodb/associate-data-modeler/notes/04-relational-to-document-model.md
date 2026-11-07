---
last-updated: 2026-11-07
applies-to: MongoDB Associate Data Modeler exam guide v2.0, docs read 2026-11-07
difficulty: intermediate
reading-time: 15 min
---

# Domain 4: Relational to Document Model

Weighted **12%**. Three objectives: explain how the document model differs from the relational model (4.1), design relationships with embedding and referencing (4.2), and understand how schema validation enforces integrity (4.3). Commands were run on MongoDB 8.3.13 on 2026-11-07.

## Table of Contents

1. [Think in access patterns, not tables](#think-in-access-patterns-not-tables)
2. [Identify the workload first](#identify-the-workload-first)
3. [Embedding](#embedding)
4. [Referencing](#referencing)
5. [One-to-one, one-to-many, many-to-many](#one-to-one-one-to-many-many-to-many)
6. [Schema validation](#schema-validation)
7. [How the exam tests this domain](#how-the-exam-tests-this-domain)
8. [Key terms](#key-terms)

---

## Think in access patterns, not tables

In a relational design you normalize first: split data into tables so each fact is stored once, then join at read time. In MongoDB you start from what the application reads and writes together, and store that together. The docs say "denormalized data models work for most use cases in MongoDB", and that the recommended way to handle related data is to embed it, which "lets your application query the data it needs with a single read operation and avoid slow `$lookup` operations."

Documents are also flexible: "By default, documents in a collection don't need the same fields or data types." Structure is something you add on purpose (validation, below), not something the engine forces.

**[📖 Map Schema Relationships](https://www.mongodb.com/docs/manual/data-modeling/schema-design-process/map-relationships/)** - embed by default, reference for specific goals

## Identify the workload first

The docs' schema design process has four steps: identify your workload, map relationships, apply design patterns, create indexes. Step one is a table of the application's operations with columns Action, Query Type (read or write), Information (fields touched), Frequency and Priority. Their blog example lists "View an article" at 1,000,000 reads per day against "Submit a new article" at 10 writes per day, which tells you to optimize the read.

**[📖 Identify Application Workload](https://www.mongodb.com/docs/manual/data-modeling/schema-design-process/identify-workload/)** - the workload table and example

Objective 4.2's first bullet, "identify application reads and writes to inform data modeling decisions", is exactly this table. Application logs and the database profiler are named as inputs (see [note 05](05-performance-tools-and-techniques.md)).

## Embedding

Embedding stores related data inside one document, as sub-documents or arrays. The docs list the benefits: better read performance, retrieval of related data "in a single database operation", and the ability "to update related data in a single atomic write operation". Use it for "contains" relationships (a contact with an address) and one-to-many relationships where the child is read in the context of the parent.

Limits: documents must be smaller than 16 mebibytes, and embedded data is duplicated if you copy it elsewhere.

**[📖 Embedded Data](https://www.mongodb.com/docs/manual/data-modeling/embedding/)** - benefits, use cases and the 16 MiB limit

## Referencing

A reference stores another document's identifier, so data lives in one place. The docs: "References result in normalized data models because data is divided into multiple collections and not duplicated." Consider references when:

- embedding would duplicate data without enough read benefit to justify it, for example because the embedded data "frequently changes";
- you model complex many-to-many relationships or large hierarchies;
- you frequently query the related entity on its own.

The map-relationships page adds the update angle: "If your application frequently updates related data, consider storing the data in its own collection and using a reference ... you reduce your application's write workload by only needing to update the data in a single place." Joining back uses `$lookup` (or `$graphLookup`).

```javascript
db.users.aggregate([
  {$lookup: {from: "orders", localField: "_id", foreignField: "userId", as: "orders"}},
  {$project: {_id: 0, name: 1, "orders._id": 1, "orders.total": 1}}
])
```

Output (MongoDB 8.3.13, 2026-11-07), shown compactly:

```text
[ { name: 'Ann', orders: [ { _id: 10, total: 59.9 }, { _id: 11, total: 12.5 } ] } ]
```

**[📖 Referenced Data](https://www.mongodb.com/docs/manual/data-modeling/referencing/)** - when to reference and how to query normalized data

## One-to-one, one-to-many, many-to-many

| Relationship | Usual model | Docs support |
|---|---|---|
| One-to-one | Embed the sub-document | "You have 'contains' relationships between entities" |
| One-to-many, bounded and read with the parent | Embed an array of sub-documents | Use case for embedding |
| One-to-many, large or unbounded, or queried alone | Reference from the many side | Unbounded arrays are an anti-pattern ([note 08](08-monitoring-and-evolving-data-models.md)) |
| Many-to-many | References (arrays of ids), or the Single Collection pattern | "Represent complex many-to-many relationships" is a reference use case |

The pages above state the one-to-one and many-to-many use cases but do not give a one-line table; the table is a summary of them, not a quotation.

## Schema validation

Flexibility does not mean no rules. Schema validation lets you "create validation rules for your fields, such as allowed data types and value ranges". Rules are written as a `$jsonSchema` validator on `createCollection` or changed later with `collMod`. Two settings decide how rules apply:

| Setting | Values | Meaning |
|---|---|---|
| `validationLevel` | `strict` (default), `moderate`, `constraint` | Which documents are checked. `moderate`: inserts, and updates to documents that already pass; updates to already-invalid documents skip validation. `constraint` (new in MongoDB 9.0 per the docs, so not available on the 8.3.13 server used for the runs here) also guarantees every document in the collection satisfies the rules. |
| `validationAction` | `error` (default), `warn`, `errorAndLog` (new in 8.1) | `error` rejects violating writes. `warn` allows the write and logs the violation. |

A write can also pass `bypassDocumentValidation` for a single operation, for example when restoring old data from backup. That is rejected when the level is `constraint`.

Here is the progression, run on MongoDB 8.3.13. A `members` collection requires a string `email`:

```text
insert rejected: code 121: Document failed validation       (strict, error)
true                                                         (after validationAction: "warn", an invalid insert is accepted)
1                                                            (moderate level: updating the already-invalid document succeeds)
strict update rejected: ...                                  (back to strict: the same update now fails)
strict                                                       (validationLevel read back from collection options)
```

Rule of thumb: add rules with `moderate` plus `warn` first to find violations in existing data, then tighten. After a rule change, the docs say previously inserted documents "may no longer be valid", and at `strict` an update to an already-invalid document fails even if the update does not introduce the violation.

**[📖 Schema Validation](https://www.mongodb.com/docs/manual/core/schema-validation/)** - purpose and default behavior
**[📖 Specify Validation Level](https://www.mongodb.com/docs/manual/core/schema-validation/specify-validation-level/)** - `strict`, `moderate`, `constraint`
**[📖 Handle Invalid Documents](https://www.mongodb.com/docs/manual/core/schema-validation/handle-invalid-documents/)** - `error`, `warn`, `errorAndLog`
**[📖 Modify Schema Validation](https://www.mongodb.com/docs/manual/core/schema-validation/update-schema-validation/)** - `collMod` and the effect on existing documents
**[📖 Bypass Schema Validation](https://www.mongodb.com/docs/manual/core/schema-validation/bypass-document-validation/)** - per-operation bypass and the `constraint` restriction

## How the exam tests this domain

- **Embed or reference?** Read the scenario for: always read together (embed), updated often or shared (reference), unbounded (reference), queried alone (reference).
- **Relational habits to unlearn:** you do not need a join for every relationship; denormalize deliberately.
- **Validation level versus action:** level picks which documents; action picks what happens to a violation.
- **Atomicity:** a single-document update is atomic, which favors embedding when related fields must change together.

## Key terms

- **Embedding** - storing related data inside a single document as sub-documents or arrays.
- **Referencing** - storing an identifier that points to a document in another collection.
- **Denormalized** - data duplicated or nested so related facts are read together.
- **Normalized** - data split into separate collections without duplication.
- **Workload table** - a list of the application's operations with type, fields, frequency and priority.
- **Schema validation** - rules (usually `$jsonSchema`) that documents must satisfy on write.
- **`validationLevel`** - the setting that decides which documents validation applies to.
- **`validationAction`** - the setting that decides whether a violation is rejected or logged.
