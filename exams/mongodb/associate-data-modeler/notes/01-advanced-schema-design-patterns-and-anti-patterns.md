---
last-updated: 2026-11-07
applies-to: MongoDB Associate Data Modeler exam guide v2.0, docs read 2026-11-07
difficulty: intermediate
reading-time: 15 min
---

# Domain 1: Advanced Schema Design Patterns and Anti-patterns

Weighted **15%**. Two objectives: apply the Approximation and Schema Versioning patterns to evolving data (1.1), and manage the schema lifecycle through evolution and migration (1.2). The commands below were run on MongoDB 8.3.13 (`mongo:8` Docker image) on 2026-11-07.

## Table of Contents

1. [What a schema design pattern is](#what-a-schema-design-pattern-is)
2. [The Approximation pattern](#the-approximation-pattern)
3. [Document versioning and schema versioning](#document-versioning-and-schema-versioning)
4. [Planning a migration](#planning-a-migration)
5. [How the exam tests this domain](#how-the-exam-tests-this-domain)
6. [Key terms](#key-terms)

---

## What a schema design pattern is

A pattern is a named, repeatable answer to a data-shape problem. MongoDB documents are flexible, so the question is never "what is the one correct table layout" but "which shape makes my most frequent operations cheap". The docs introduce patterns this way: "Use schema design patterns to optimize your data model based on how your application queries and uses data."

**[📖 Schema Design Patterns](https://www.mongodb.com/docs/manual/data-modeling/design-patterns/)** - the current pattern families: computed values, group data, polymorphic data, versioning, archive and single collection

Note the naming. The current manual groups Approximation under "Handle Computed Values" (next to the Computed pattern), Bucket and Outlier under "Group Data", and Inheritance under "Polymorphic Data". The exam guide uses the individual pattern names, so know both the name and the family it lives in.

## The Approximation pattern

Some numbers are read constantly and nobody needs them exact: a city population, a page-view counter, passengers on a route. Updating the stored value on every change creates a write storm for no business benefit. The Approximation pattern writes only when the value has moved by a chosen step.

The docs state it directly: "Instead of updating values every time data changes, the approximation pattern updates data based on a larger granularity, which results in fewer updates and a lower application workload." Their example inserts a new population document only when the population changes by 100 or more.

**[📖 Use the Approximation Pattern](https://www.mongodb.com/docs/manual/data-modeling/design-patterns/computed-values/approximation-schema-pattern)** - the 40,000-person city example and when approximate values are acceptable

When to pick it: high-frequency changes, readers who accept statistically valid rather than exact values. When not to: money, inventory counts that gate a sale, anything audited. The trade-off to name on the exam is "fewer writes in exchange for precision".

## Document versioning and schema versioning

Two different problems share one docs page, "Document and Schema Versioning". Keep them apart.

**Document Versioning** keeps the history of one document. "In the Document Versioning Pattern, older data versions are retained in a separate collection from the current data." The docs example is an insurance policy: each revision is stored, and historical data "does not impact queries on current data". It fits when documents are updated infrequently.

**Schema Versioning** handles a change in the shape of documents. The docs say it "lets you have different versions of your schema in the same collection, which avoids large-scale schema migrations when requirements change." You add a `schemaVersion` field so the application knows how to read each document.

**[📖 Document and Schema Versioning](https://www.mongodb.com/docs/manual/data-modeling/design-patterns/data-versioning/)** - use cases: no downtime allowed, migrations that take hours to weeks, or no need to update old documents
**[📖 Maintain Different Schema Versions](https://www.mongodb.com/docs/manual/data-modeling/design-patterns/data-versioning/schema-versioning)** - the `schemaVersion` field procedure
**[📖 Keep a History of Document Versions](https://www.mongodb.com/docs/manual/data-modeling/design-patterns/data-versioning/document-versioning)** - the history collection procedure

Here is schema versioning running. Version 1 stores `home` as a top-level field. Version 2 moves contact details into a `contacts` array:

```javascript
db.contacts.insertMany([
  {_id: 1, name: "Taylor", home: "209-555-7788", schemaVersion: 1},
  {_id: 2, name: "Sam", schemaVersion: 2, contacts: [{type: "cell", value: "503-555-0110"}]}
]);
db.contacts.find({schemaVersion: 2}, {name: 1}).toArray();
```

Output (mongosh on MongoDB 8.3.13, run 2026-11-07):

```text
[
  {
    _id: 2,
    name: 'Sam'
  }
]
```

Both shapes live in one collection and the query selects one by `schemaVersion`. One warning from the docs matters for indexes: if the same field appears at different levels in different documents, "you may need multiple indexes to support queries on that field."

## Planning a migration

Objective 1.2 asks you to explain schema evolution and to "plan and execute a schema migration strategy". The fetched docs give you the decision inputs rather than a step list. Versioning patterns are most useful when any of these hold:

- Application downtime is not an option for migration.
- Updating documents to the new schema may take hours, days, or weeks.
- Updating documents to the new schema is not a requirement.

So the strategy question on the exam is usually "update everything now, or let versions coexist". If downtime is unacceptable or the data set is huge, let versions coexist with `schemaVersion`, and migrate documents gradually. Gradual migration (rewriting a document to the newest version when the application touches it) is a common way to finish the job, but the pages above do not describe it, so treat it as general practice rather than documented behavior.

Schema validation (domain 4) is the other half of lifecycle control: it can be tightened with `collMod` once the migration is done. See [note 04](04-relational-to-document-model.md).

**[📖 Designing Your Schema](https://www.mongodb.com/docs/manual/data-modeling/schema-design-process/)** - the four-step process and the point that the flexible schema lets you evolve a model without rewriting existing documents

## How the exam tests this domain

- **Name the pattern from the symptom.** Frequent noisy counter updates that nobody needs exact: Approximation. Policy or record history that must be queryable: Document Versioning. New field layout while old documents stay: Schema Versioning.
- **Spot the trade-off.** Approximation trades precision for fewer writes. Schema Versioning trades a single uniform shape for application code that handles several, and possibly extra indexes.
- **Pick a migration strategy from constraints.** "No downtime" or "hours to weeks" points to coexisting versions.

## Key terms

- **Schema design pattern** - a named, reusable way to shape documents so the application's frequent operations are fast.
- **Approximation pattern** - update a stored value only when it changes by a set step, because exact values are not needed.
- **Document Versioning pattern** - keep older versions of a document in a separate collection from the current data.
- **Schema Versioning pattern** - keep several schema versions in one collection and mark each document with a `schemaVersion` field.
- **Schema evolution** - changing the shape of documents over time as requirements change.
- **Schema migration** - moving existing documents from one schema shape to another.
