---
last-updated: 2026-11-07
applies-to: MongoDB Associate Data Modeler exam guide v2.0, read 2026-11-07
---

# MongoDB Associate Data Modeler - Study Strategy

Weights: Monitoring and evolving 27%, Schema optimization 16%, Advanced patterns 15%, Indexing 15%, Relational to document 12%, Data transformation 6%, Performance tools 6%, CRUD 3%. Spend hours roughly in that proportion.

## Study Approach

### Phase 1: Foundations (Week 1)
- Read notes 03, 04 and 02. Make sure you can say, in one sentence each, when to embed, when to reference, and what `validationLevel` versus `validationAction` does.

### Phase 2: Patterns and indexing (Weeks 2-3)
- Read notes 08, 07 and 01 slowly. For every pattern, write down the symptom it fixes and the price it charges.
- Read note 06 and be able to order a compound index from a query description.

### Phase 3: Verify and review (Week 4)
- Read note 05, then take the practice bank in two sittings and the scenarios once.

## Exam Tactics

1. **Read the last sentence first:** is it asking for a pattern, an index, a setting or a diagnosis?
2. **Find the symptom word:** "unbounded", "rarely read", "every request does a lookup", "slow writes".
3. **Eliminate by trade-off:** a choice that fixes the problem but breaks a stated requirement (exact values, downtime) is wrong.
4. **For index questions,** list equality, sort and range fields, then apply ESR before looking at the options.
5. **Time:** 75 questions in 110 minutes; flag and return.

## Common Traps

- Using Approximation for values that must be exact.
- Choosing a `$lookup`-heavy design when the question says every read needs the related fields.
- Confusing Document Versioning (history in another collection) with Schema Versioning (`schemaVersion` field).
- Forgetting that a monotonically increasing shard key makes a write hotspot.
- Placing the range field before the sort field when the question wants no in-memory sort.
- Treating Bucket as a solution for an unbounded series; the bucket itself must be bounded.
