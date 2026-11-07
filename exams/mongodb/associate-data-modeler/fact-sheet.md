---
last-updated: 2026-11-07
applies-to: MongoDB Associate Data Modeler exam page and Exam Guide v2.0, read 2026-11-07
---

# MongoDB Associate Data Modeler - Fact Sheet

## Exam Overview

**Exam Name:** MongoDB Associate Data Modeler (the exam page heading reads "MongoDB Data Modeler Exam"; the guide title is "MongoDB Associate Data Modeler Exam Guide")
**Exam Code:** None published
**Duration:** 110 minutes
**Questions:** 75 multiple-choice questions, one correct response each. The guide says there may be more than 75, because a handful of unscored questions can be added.
**Passing Score:** Not published; MongoDB sets it statistically per exam and does not publish the required percentage. Only the overall percentage must be met, not a pass in each domain.
**Cost:** $150 USD (exam page and guide, read 2026-11-07)
**Valid For:** Not stated on the exam page (re-read 2026-11-07); the Certification Program Guide is a gated download and was not read, so validity and retake rules are unverified
**Delivery:** Online, proctored; reference materials are not allowed
**Languages:** English
**Prerequisites:** None
**Discounts:** Completing a Learning Path earns 50% off the exam; students and educators can get a free exam through the student and educator programs (exam page)
**Extended time:** Available for candidates with a disability or for whom English is a second language; email certification@mongodb.com at least 72 hours ahead (guide)

The guide is "Version 2.0". The credential is described as validating "knowledge and skills needed to show competency in data modeling on MongoDB": design, build and evolve effective data models, and incorporate data governance into the work. A score report states pass or fail and the percent correct for each domain.

**[📖 Exam page](https://learn.mongodb.com/pages/mongodb-associate-data-modeler-exam)** - format, fee, time, discounts and study links
**[📖 Exam study guide](https://learn.mongodb.com/courses/associate-data-modeler-exam-study-guide)** - the free guide (a PDF shown in the course viewer) with domains, weights and objectives
**[📖 MongoDB Data Modeling Path](https://learn.mongodb.com/learning-paths/mongodb-data-modeling-certification-learning-path)** - the recommended learning path
**[📖 Practice questions](https://learn.mongodb.com/learn/course/associate-data-modeler-practice-questions/prep-questions/practice-questions)** - MongoDB's own sample questions (requires a MongoDB University login)
**[📖 Certification Program Guide](https://learn.mongodb.com/courses/program-guide)** - program policies and testing experience

## Domains and Weights

| # | Domain | Weight |
|---|---|---|
| 1 | Advanced Schema Design Patterns & Anti-patterns | 15% |
| 2 | Fundamentals of Data Transformation | 6% |
| 3 | CRUD Operations | 3% |
| 4 | Relational to Document Model | 12% |
| 5 | Performance Tools and Techniques | 6% |
| 6 | Indexing Design Fundamentals | 15% |
| 7 | Schema Design Optimization | 16% |
| 8 | Monitoring and Evolving Data Models | 27% |

Domains 8, 7, 1 and 6 together carry 73% of the exam.

## Target Audience

- Developers and data modelers who design, implement or maintain MongoDB data models and application queries in development or production
- People with some experience in applying schema design principles, using indexing strategies and building aggregation pipelines
- Candidates who completed the Data Modeler learning path (strongly encouraged by the guide)

## Domain 1: Advanced Schema Design Patterns and Anti-patterns (15%)

**[📖 Schema Design Patterns](https://www.mongodb.com/docs/manual/data-modeling/design-patterns/)** - the pattern families
**[📖 Document and Schema Versioning](https://www.mongodb.com/docs/manual/data-modeling/design-patterns/data-versioning/)** - versioning use cases
**[📖 Use the Approximation Pattern](https://www.mongodb.com/docs/manual/data-modeling/design-patterns/computed-values/approximation-schema-pattern)** - fewer writes for approximate values

**Key Facts:**
- Approximation updates a stored value only when it moves by a step (the docs use steps of 100)
- Schema Versioning keeps several schema versions in one collection with a `schemaVersion` field
- Document Versioning keeps old versions in a separate collection

## Domain 2: Fundamentals of Data Transformation (6%)

**[📖 Aggregation Pipeline](https://www.mongodb.com/docs/manual/core/aggregation-pipeline/)** - stages feed each other
**[📖 $unwind](https://www.mongodb.com/docs/manual/reference/operator/aggregation/unwind/)** - arrays to documents

**Key Facts:**
- Only `$merge` and `$out` make `aggregate()` write
- `$unwind` drops null, missing or empty arrays unless `preserveNullAndEmptyArrays` is true
- `$set` is an alias for `$addFields`; `$group` does not order output

## Domain 3: CRUD Operations (3%)

**[📖 MongoDB CRUD Operations](https://www.mongodb.com/docs/manual/crud/)** - insert, find, update, delete

**Key Facts:**
- Writes are atomic at the level of a single document
- `updateOne`, `updateMany`, `replaceOne`, `deleteOne`, `deleteMany`

## Domain 4: Relational to Document Model (12%)

**[📖 Embedded Data](https://www.mongodb.com/docs/manual/data-modeling/embedding/)** - when to embed
**[📖 Referenced Data](https://www.mongodb.com/docs/manual/data-modeling/referencing/)** - when to reference
**[📖 Schema Validation](https://www.mongodb.com/docs/manual/core/schema-validation/)** - validation rules

**Key Facts:**
- Documents must be under 16 mebibytes
- `validationLevel`: strict (default), moderate, constraint (constraint is new in 9.0); `validationAction`: error (default), warn, errorAndLog (8.1)
- Workload table: action, type, information, frequency, priority

## Domain 5: Performance Tools and Techniques (6%)

**[📖 Database Profiler](https://www.mongodb.com/docs/manual/tutorial/manage-the-database-profiler/)** - levels and `slowms`
**[📖 Analyze Query Plan](https://www.mongodb.com/docs/manual/tutorial/analyze-query-plan/)** - reading explain output

**Key Facts:**
- Profiler is off by default; default slow threshold is 100 ms; output goes to `system.profile`
- `COLLSCAN` plus `totalKeysExamined: 0` means no index was used

## Domain 6: Indexing Design Fundamentals (15%)

**[📖 Index Types](https://www.mongodb.com/docs/manual/core/indexes/index-types/)** - the types
**[📖 The ESR Guideline](https://www.mongodb.com/docs/manual/tutorial/equality-sort-range-guideline/)** - key order
**[📖 Compound Indexes](https://www.mongodb.com/docs/manual/core/indexes/index-types/index-compound/)** - prefixes and limits

**Key Facts:**
- ESR: Equality, then Sort, then Range; ERS if the range is very selective
- A compound index can hold up to 32 fields and one hashed field
- Index prefixes: leading fields only

## Domain 7: Schema Design Optimization (16%)

**[📖 Single Collection Pattern](https://www.mongodb.com/docs/manual/data-modeling/design-patterns/single-collection/)** - one collection, many types
**[📖 Group Data](https://www.mongodb.com/docs/manual/data-modeling/design-patterns/group-data/)** - bucket and outlier
**[📖 Archive Pattern](https://www.mongodb.com/docs/manual/data-modeling/design-patterns/archive/)** - move old data
**[📖 Choose a Shard Key](https://www.mongodb.com/docs/manual/core/sharding-choose-a-shard-key/)** - cardinality, frequency, monotonic

**Key Facts:**
- Time series collections apply the bucket pattern automatically
- Archive: external storage such as Amazon S3 is usually best
- A monotonic shard key sends all inserts to one chunk

## Domain 8: Monitoring and Evolving Data Models (27%)

**[📖 Polymorphic Data](https://www.mongodb.com/docs/manual/data-modeling/design-patterns/polymorphic-data/)** - inheritance
**[📖 Store Computed Data](https://www.mongodb.com/docs/manual/data-modeling/design-patterns/computed-values/computed-schema-pattern/)** - computed pattern
**[📖 Schema Design Anti-Patterns](https://www.mongodb.com/docs/manual/data-modeling/design-antipatterns/)** - the five anti-patterns

**Key Facts:**
- Performance Advisor needs an M10 or higher Atlas cluster; Compass Performance Insights also flags anti-patterns
- Unbounded arrays and bloated documents are fixed by Subset and referencing
- Extended Reference copies a few rarely changing hot fields

## Exam Tips

1. **Domains 8, 7, 1 and 6 are 73% of the exam.** Study patterns and indexing first.
2. **Symptom to pattern.** Most questions describe a problem; name the pattern or anti-pattern.
3. **Know the price of each pattern.** The right answer often depends on the trade-off.
4. **Read the ESR sentence twice.** The ERS exception is a likely trap.
5. **Time:** 110 minutes for 75 questions is about 1.4 minutes each.

## Quick Reference

```javascript
// Run on MongoDB 8.3.13 (mongo:8 Docker image) on 2026-11-07 where shown in the notes.
db.createCollection("members", {validator: {$jsonSchema: {bsonType: "object", required: ["email"], properties: {email: {bsonType: "string"}}}}});
db.runCommand({collMod: "members", validationLevel: "moderate", validationAction: "warn"});
db.events.createIndex({status: 1, user: 1, ts: 1});   // Equality, Sort, Range
db.events.find({status: "open", ts: {$gte: 5000}}).sort({user: 1}).explain("executionStats");
db.setProfilingLevel(1, {slowms: 100});
```
