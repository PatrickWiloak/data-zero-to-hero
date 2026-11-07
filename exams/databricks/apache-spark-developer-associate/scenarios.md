---
last-updated: 2026-11-07
applies-to: Databricks exam guide (published 2026-10-08), read 2026-11-07
---

# Databricks Associate Developer for Apache Spark - Exam-Style Scenarios

Original scenarios for practice. They are not vendor exam questions.

## Scenario 1: The Silent Union

### Scenario
`a` has columns (id INT, amount INT) and `b` has columns (amount INT, id INT). An engineer runs `a.union(b)` and the totals look wrong.

**Question:** What happened and what is the fix?

**Options:**
A. union stacks by position, so b's amount values landed in id; use `unionByName`
B. union removes duplicates, so use `unionAll`
C. union runs lazily, so call `collect()` first
D. union requires identical row counts, so repartition both

**Correct Answer:** A

**Explanation:**
- `union` matches columns by position; the column order differs, so values end up under the wrong names
- `unionByName` matches by name

**Why other options are wrong:**
- **B:** `union` already keeps duplicates in the DataFrame API
- **C:** Laziness does not change values
- **D:** Row counts do not matter

---

## Scenario 2: One Slow Task

### Scenario
A join stage has 200 tasks. 199 finish in 5 seconds; one runs for 6 minutes and reads ten times more shuffle data than the median.

**Question:** What is the right response?

**Options:**
A. Add more executors
B. Treat it as skew: confirm AQE skew handling is on, handle hot or null keys, broadcast the small side or salt the key
C. Increase the driver memory
D. Call `collect()` to see the data

**Correct Answer:** B

**Explanation:**
- One task with far more data than the median is data skew
- AQE splits skewed partitions; broadcasting avoids shuffling the large side; salting spreads a hot key

**Why other options are wrong:**
- **A:** One task is still one task
- **C:** The driver is not the bottleneck
- **D:** Collecting pulls data to the driver and does not fix the stage

---

## Scenario 3: Accumulator Reads Zero

### Scenario
A job increments an accumulator inside a `map` to count bad records, then prints the accumulator before calling any action.

**Question:** What is printed, and where should the update be made for exactly-once counting?

**Options:**
A. The bad-record count; transformations are fine
B. Zero; do the update in an action such as `foreach`, where updates are applied exactly once
C. Null; accumulators cannot be read on the driver
D. An error; accumulators must be created in actions

**Correct Answer:** B

**Explanation:**
- Transformations are lazy, so the update has not run yet
- Updates inside actions are applied exactly once; inside transformations they may be applied more than once if tasks re-execute

**Why other options are wrong:**
- **A:** Nothing has run
- **C:** The driver can read `.value`
- **D:** Accumulators are created on the driver at any time

---

## Scenario 4: Late Events

### Scenario
A streaming job counts clicks per 5-minute window by event time. Some events arrive up to 20 minutes late and the state keeps growing.

**Question:** What should be configured?

**Options:**
A. `outputMode("complete")`
B. `withWatermark("event_time", "20 minutes")` on the event-time column before the groupBy, with append or update mode
C. `trigger(availableNow=True)`
D. `dropDuplicates()` after the aggregation

**Correct Answer:** B

**Explanation:**
- A watermark lets Spark finalize windows and drop old state
- It must be on the aggregated event-time column and placed before the aggregation

**Why other options are wrong:**
- **A:** Complete mode keeps all state
- **C:** Changes when batches run, not state
- **D:** Does not bound state in the aggregation

---

## Scenario 5: Too Many Small Files

### Scenario
After filtering 90% of rows from a 400-partition DataFrame, a job writes 400 tiny Parquet files.

**Question:** What is the cheapest way to cut the file count?

**Options:**
A. `repartition(400)`
B. `coalesce(10)` before the write
C. Increase `spark.sql.shuffle.partitions`
D. `cache()` the DataFrame

**Correct Answer:** B

**Explanation:**
- `coalesce` merges existing partitions without a full shuffle
- `repartition(10)` would also work but adds a shuffle

**Why other options are wrong:**
- **A:** Keeps 400 partitions and shuffles
- **C:** Raises partitions after shuffles, the opposite of the goal
- **D:** Caching does not change file counts
