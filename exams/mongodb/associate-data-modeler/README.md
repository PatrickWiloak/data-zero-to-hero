---
last-updated: 2026-11-07
applies-to: MongoDB Associate Data Modeler exam page and Exam Guide v2.0, read 2026-11-07
---

# MongoDB Associate Data Modeler

## Exam Overview

The MongoDB Associate Data Modeler exam checks that you can design, build and evolve data models on MongoDB and bring data governance into that work. It leans on judgment: given a workload and a symptom, which pattern, index or validation rule fixes it. Roughly three quarters of the exam is schema patterns, anti-patterns and indexing.

**Exam Details** (from the **[📖 exam page](https://learn.mongodb.com/pages/mongodb-associate-data-modeler-exam)** and the **[📖 exam study guide](https://learn.mongodb.com/courses/associate-data-modeler-exam-study-guide)**, Version 2.0, read 2026-11-07):
- **Exam Name:** MongoDB Associate Data Modeler (no exam code published)
- **Duration:** 110 minutes
- **Number of Questions:** 75 multiple choice, one correct answer; a handful of unscored questions may be added
- **Passing Score:** Not published
- **Cost:** $150 USD
- **Delivery:** Online proctored; no reference materials
- **Languages:** English
- **Validity:** Not stated on the pages read
- **Prerequisites:** None

## Exam Domains

| # | Domain | Weight | Notes |
|---|--------|--------|-------|
| 1 | Advanced Schema Design Patterns & Anti-patterns | 15% | [01](notes/01-advanced-schema-design-patterns-and-anti-patterns.md) |
| 2 | Fundamentals of Data Transformation | 6% | [02](notes/02-fundamentals-of-data-transformation.md) |
| 3 | CRUD Operations | 3% | [03](notes/03-crud-operations.md) |
| 4 | Relational to Document Model | 12% | [04](notes/04-relational-to-document-model.md) |
| 5 | Performance Tools and Techniques | 6% | [05](notes/05-performance-tools-and-techniques.md) |
| 6 | Indexing Design Fundamentals | 15% | [06](notes/06-indexing-design-fundamentals.md) |
| 7 | Schema Design Optimization | 16% | [07](notes/07-schema-design-optimization.md) |
| 8 | Monitoring and Evolving Data Models | 27% | [08](notes/08-monitoring-and-evolving-data-models.md) |

### What each domain covers

1. **Advanced patterns** - Approximation, Schema Versioning, schema evolution and migration.
2. **Data transformation** - aggregation pipelines, the core stages, explain on a pipeline.
3. **CRUD** - insert, query, update and delete with operators and array constraints.
4. **Relational to document** - embedding versus referencing, relationship types, schema validation.
5. **Performance tools** - the profiler, slow queries, `explain()`, index effectiveness.
6. **Indexing** - index types and costs, ESR, prefixes, sort order, selectivity.
7. **Schema optimization** - Single Collection, Subset, Bucket, Outlier, Archive, sharding impact.
8. **Monitoring and evolving** - Inheritance, Computed, Extended Reference, unbounded arrays, bloated documents.

## Names to know

| Name | Note |
|---|---|
| Approximation, Computed | Both sit under "Handle Computed Values" in the current manual |
| Bucket, Outlier | Both sit under "Group Data" |
| Inheritance | A pattern in the "Polymorphic Data" family |
| Subset, Extended Reference | Described in MongoDB's "Building with Patterns" blog series; the manual does not have standalone pages for them |
| Domain 8 title | "Monitoring and Evolving Data Models", but its objectives are patterns and anti-patterns |

## Study Approach

### Phase 1: Foundations (Week 1)
1. Notes 03, 04 and 02 in that order: CRUD, embed versus reference with validation, and aggregation.

### Phase 2: The heavy domains (Weeks 2-3)
1. Notes 08, 07 and 01: the patterns and anti-patterns that make up 58% of the exam.
2. Note 06: indexing and ESR.

### Phase 3: Verify and review (Week 4)
1. Note 05, then the practice bank and scenarios, then the weakest note's ending.

## Study Resources

- **[📖 Exam page](https://learn.mongodb.com/pages/mongodb-associate-data-modeler-exam)** - format, fee, discounts
- **[📖 Exam study guide](https://learn.mongodb.com/courses/associate-data-modeler-exam-study-guide)** - domains and objectives (free, 30 minutes)
- **[📖 MongoDB Data Modeling Path](https://learn.mongodb.com/learning-paths/mongodb-data-modeling-certification-learning-path)** - free learning path; completing it earns 50% off the exam
- **[📖 Schema design process](https://www.mongodb.com/docs/manual/data-modeling/schema-design-process/)** - the four steps
- **[📖 Schema design patterns](https://www.mongodb.com/docs/manual/data-modeling/design-patterns/)** - pattern reference
- **[📖 Schema design anti-patterns](https://www.mongodb.com/docs/manual/data-modeling/design-antipatterns/)** - anti-pattern reference

## Tips for Success

1. **Name the symptom.** Most questions say what hurts; name the fix.
2. **Memorize trade-offs.** Each pattern costs something: staleness, duplication, extra reads.
3. **Learn ESR cold,** including when ERS is better.
4. **Do not skip small domains** like CRUD; they are cheap marks.

## File Index

| File | Description |
|------|-------------|
| [fact-sheet.md](fact-sheet.md) | Exam facts and key facts per domain, with documentation links |
| [strategy.md](strategy.md) | Study phases and exam tactics |
| [practice-plan.md](practice-plan.md) | Four-week reading schedule |
| [scenarios.md](scenarios.md) | Exam-style scenarios with solutions |
| [notes/01-advanced-schema-design-patterns-and-anti-patterns.md](notes/01-advanced-schema-design-patterns-and-anti-patterns.md) | Approximation, versioning, migration |
| [notes/02-fundamentals-of-data-transformation.md](notes/02-fundamentals-of-data-transformation.md) | Aggregation pipelines |
| [notes/03-crud-operations.md](notes/03-crud-operations.md) | CRUD and array queries |
| [notes/04-relational-to-document-model.md](notes/04-relational-to-document-model.md) | Embedding, referencing, validation |
| [notes/05-performance-tools-and-techniques.md](notes/05-performance-tools-and-techniques.md) | Profiler and explain |
| [notes/06-indexing-design-fundamentals.md](notes/06-indexing-design-fundamentals.md) | Index types, ESR, prefixes |
| [notes/07-schema-design-optimization.md](notes/07-schema-design-optimization.md) | Single Collection, Subset, Bucket, Outlier, Archive, sharding |
| [notes/08-monitoring-and-evolving-data-models.md](notes/08-monitoring-and-evolving-data-models.md) | Inheritance, Computed, Extended Reference, anti-patterns |
| [Practice questions](../../../resources/practice-questions/mongodb-associate-data-modeler.md) | 25 questions tagged by domain |
