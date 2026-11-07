---
last-updated: 2026-11-07
applies-to: MongoDB Associate Data Modeler exam guide v2.0, read 2026-11-07
---

# MongoDB Associate Data Modeler - Exam-Style Scenarios

Original scenarios for practice. They are not vendor exam questions.

## Scenario 1: The Query That Sorts in Memory

### Scenario
An events collection is queried with `{status: "open", ts: {$gte: 5000}}` and sorted by `user`. `explain("executionStats")` shows the right documents but a sort stage in memory. The team has an index on `{status: 1, ts: 1, user: 1}`.

**Question:** Which index change removes the in-memory sort?

**Options:**
A. `{ts: 1, status: 1, user: 1}`
B. `{status: 1, user: 1, ts: 1}`
C. `{user: 1, status: 1, ts: 1}`
D. A text index on `user`

**Correct Answer:** B

**Explanation:**
- ESR puts equality (`status`), then sort (`user`), then range (`ts`)
- With the sort field before the range field the index returns rows already ordered

**Why other options are wrong:**
- **A:** Range first breaks the ordering of everything after it
- **C:** The sort field cannot lead when the query has an equality filter that should come first
- **D:** Text indexes serve `$text` queries, not sorts

## Scenario 2: The Growing Review List

### Scenario
A book document embeds every review. The product page shows the latest three. Some bestsellers have tens of thousands of reviews, pages load slowly, and the largest documents approach the size limit.

**Question:** Name the anti-pattern and a fix.

**Options:**
A. Unnecessary indexes; remove indexes
B. Unbounded array; keep the newest few in the book and store the rest in a `reviews` collection
C. Too many collections; merge collections
D. Excess `$lookup`; add another `$lookup`

**Correct Answer:** B

**Explanation:**
- An array with no limit is an unbounded array and can hit the 16 MiB document limit
- The Subset pattern keeps what is read often (three newest) in the main document, and references the rest

**Why other options are wrong:**
- **A, C, D:** The symptom is array growth, not indexes, collection count or joins

## Scenario 3: The Order List That Joins Every Time

### Scenario
Orders reference customers. The order list screen shows order total and customer name. Every request runs `$lookup` against customers. Names almost never change.

**Question:** Which design change fits best?

**Options:**
A. Embed the full customer record in every order, including addresses and history
B. Add a small `customer: {name}` copy to each order and keep the reference
C. Remove the reference
D. Use `$lookup` with a bigger batch size

**Correct Answer:** B

**Explanation:**
- Extended Reference copies only the few hot, stable fields
- The reference stays for the rare cases that need more

**Why other options are wrong:**
- **A:** Embedding everything bloats orders and duplicates changing data
- **C:** You would lose the link to the customer
- **D:** Does not remove the join

## Scenario 4: Tightening Rules Without Breaking Updates

### Scenario
A `members` collection has 2 million documents, some missing the new required `email` field. The team wants validation, but nightly update jobs on old documents must not start failing.

**Question:** Which setup lets them add the rule safely?

**Options:**
A. `validationLevel: "strict"`, `validationAction: "error"`
B. `validationLevel: "moderate"`, `validationAction: "warn"` first, then tighten later
C. Disable validation permanently
D. `validationLevel: "constraint"` immediately

**Correct Answer:** B

**Explanation:**
- `moderate` does not require updates to already-invalid documents to pass
- `warn` logs violations instead of rejecting writes, so you can find and fix data
- Tighten later once old documents are clean

**Why other options are wrong:**
- **A:** At `strict`, updates to invalid existing documents fail even when the update does not cause the violation
- **C:** Gives up the integrity goal
- **D:** `constraint` guarantees every document satisfies the rules, so it cannot sit alongside known-invalid documents

## Scenario 5: Choosing a Shard Key

### Scenario
A logging collection will be sharded. The proposed key is the event timestamp, which grows steadily. Writes dominate.

**Question:** What is the likely problem?

**Options:**
A. Low cardinality splits the data into too few chunks
B. A monotonically increasing key sends all new inserts to one chunk, making one shard the write bottleneck
C. Timestamps cannot be indexed
D. It will force a hashed index on every field

**Correct Answer:** B

**Explanation:**
- With an always-increasing key, inserts route to the chunk whose upper bound is `MaxKey`
- Consider a hashed key or a compound key that adds a well-distributed field

**Why other options are wrong:**
- **A:** Timestamps have high cardinality
- **C:** They can be indexed
- **D:** Nothing forces that
