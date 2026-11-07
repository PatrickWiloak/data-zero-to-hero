---
last-updated: 2026-11-07
applies-to: Databricks exam guide (August 2026 edition), read 2026-11-07
---

# Databricks Data Analyst Associate - Exam-Style Scenarios

Original scenarios for practice. They are not vendor exam questions.

## Scenario 1: Customers Without Orders

### Scenario
A sales manager wants a list of customers who have never placed an order, from tables `customers` and `orders`.

**Question:** Which query returns that list?

**Options:**
A. `SELECT c.name FROM customers c INNER JOIN orders o ON c.id = o.customer_id`
B. `SELECT c.name FROM customers c LEFT ANTI JOIN orders o ON c.id = o.customer_id`
C. `SELECT name FROM customers UNION SELECT customer_id FROM orders`
D. `SELECT c.name FROM customers c CROSS JOIN orders o`

**Correct Answer:** B

**Explanation:**
- LEFT ANTI JOIN returns left-side rows with no match on the right
- The same result comes from a LEFT JOIN filtered with `WHERE o.customer_id IS NULL`

**Why other options are wrong:**
- **A:** Returns only customers who do have orders
- **C:** Stacks two lists and mixes names with ids
- **D:** Produces every customer-order combination

---

## Scenario 2: Slow Dashboard

### Scenario
A dashboard runs the same heavy aggregation over a large, slowly changing table every time someone opens it.

**Question:** What is the best improvement?

**Options:**
A. Replace the dataset source with a materialized view that refreshes on a schedule
B. Convert the table to a streaming table
C. Disable the result cache
D. Ask every viewer to refresh less often

**Correct Answer:** A

**Explanation:**
- A materialized view stores precomputed results and refreshes them, so reads are fast
- Suitable for repeated, expensive queries on data that changes slowly

**Why other options are wrong:**
- **B:** Streaming tables are for append-only incremental ingestion
- **C:** Disabling caching makes things slower
- **D:** Does not fix the cost of each run

---

## Scenario 3: Immediate Notification

### Scenario
A logistics team must receive an email as soon as the number of delayed shipments in the last hour passes 50.

**Question:** What should the analyst build?

**Options:**
A. A dashboard with a counter
B. A SQL alert on a query that counts delayed shipments, with a threshold of 50 and an email destination
C. A Genie agent
D. A larger SQL warehouse

**Correct Answer:** B

**Explanation:**
- Alerts run a query on a schedule, test a condition and notify users or destinations
- Test condition shows whether it would trigger on current data

**Why other options are wrong:**
- **A:** Someone must open it
- **C:** Genie answers questions; it does not notify
- **D:** Compute size has no bearing on notification

---

## Scenario 4: Wrong Genie Answers

### Scenario
Executives ask a Genie agent for monthly active customers and get different numbers each week because the agent guesses the definition.

**Question:** What is the best fix?

**Options:**
A. Ask executives to rephrase until the number looks right
B. Add an instruction that defines monthly active customer, add an example query with that logic, and add a benchmark question
C. Grant executives CAN MANAGE
D. Switch to a smaller warehouse

**Correct Answer:** B

**Explanation:**
- Instructions and example queries provide proven definitions
- Benchmarks measure whether accuracy improved

**Why other options are wrong:**
- **A:** Does not fix the cause
- **C:** Permissions do not change definitions
- **D:** Compute size does not affect answer quality

---

## Scenario 5: Old Version Missing

### Scenario
An analyst runs `SELECT * FROM sales VERSION AS OF 40` for a version created three weeks ago and gets an error. `DESCRIBE HISTORY` still lists the version.

**Question:** What explains the error?

**Options:**
A. The warehouse is stopped
B. VACUUM has removed the data files that version needs
C. The table was renamed
D. Photon is disabled

**Correct Answer:** B

**Explanation:**
- History metadata can outlast the data files
- Deleted-file retention defaults to 7 days, and VACUUM removes older files

**Why other options are wrong:**
- **A:** A stopped warehouse restarts when queried
- **C:** Renaming does not remove versions
- **D:** Photon does not affect time travel
