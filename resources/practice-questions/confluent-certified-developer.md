---
last-updated: 2026-08-09
difficulty: intermediate
---

# Confluent Certified Developer for Apache Kafka (CCDAK) - Practice Questions

15 questions weighted toward development (30%), then application design, Kafka Streams, Kafka Connect, and Schema Registry (15% each) and ksqlDB (10%).

> **Cert page:** [exams/confluent/certified-developer/](../../exams/confluent/certified-developer/)

---

### Question 1
**Scenario:** Message ordering must be guaranteed for all events about a single customer.

A. Use a round-robin partitioner so each customer's events spread across all partitions
B. Use the customer ID as the message key, so the default partitioner sends all of that customer's events to one partition
C. Set `acks=all` so every in-sync replica confirms each write, which also orders events across partitions
D. Enable idempotence so the broker sorts each customer's events by timestamp before storing them

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Kafka guarantees order within a partition, not across a topic. Keying gives per-entity ordering while keeping parallelism across entities; a single-partition topic gives global ordering at the price of no parallelism at all.
</details>

---

### Question 2
**Scenario:** A producer must not lose messages if the leader broker fails.

A. `acks=1`, because the leader's acknowledgement is enough once it has written to its own log
B. `acks=0`, with a very large `buffer.memory` so the producer can hold every unsent message until a broker answers
C. More retries and a longer `delivery.timeout.ms`, since resending always recovers the messages the old leader never replicated
D. `acks=all` with `min.insync.replicas` at least 2 and a replication factor of 3

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** `acks=all` alone is not enough: without `min.insync.replicas`, a topic down to one in-sync replica still acknowledges. The pairing of replication factor 3 with `min.insync.replicas` 2 tolerates one broker loss while still refusing to acknowledge unreplicated writes.
</details>

---

### Question 3
**Scenario:** A producer retries and creates duplicate messages.

A. Accept duplicates and let consumers deal with them by storing processed offsets in a local file
B. Use `acks=1`, since waiting only for the leader means fewer ack timeouts and so fewer retries
C. Reduce retries to zero, which prevents duplicates at the cost of silently dropping any message that hits a transient error
D. Enable idempotence with `enable.idempotence=true`, which deduplicates retries per producer session and partition

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** The producer stamps a sequence number that the broker uses to reject duplicates. Idempotence is on by default in current clients, and it covers retries within a session; exactly-once across a read-process-write cycle additionally needs transactions.
</details>

---

### Question 4
**Scenario:** A consumer group has 4 consumers and the topic has 3 partitions.

A. The group fails to start, because the number of consumers must not exceed the number of partitions
B. Three consumers each own one partition and the fourth is idle
C. Partitions are split into byte ranges, so the fourth consumer reads the tail of each partition
D. Each consumer gets a share of every partition, with the load divided evenly across the four

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** A partition is assigned to at most one consumer in a group, so partition count is the ceiling on group parallelism. The idle consumer is not useless: it takes over immediately on a rebalance if another consumer dies.
</details>

---

### Question 5
**Scenario:** A consumer must not reprocess messages after a restart.

A. Use a new group ID on every restart so the consumer starts with a clean committed-offset state
B. Commit offsets, ideally after processing rather than before; use manual commits when at-least-once matters
C. Set `auto.offset.reset=earliest`, so the consumer always resumes from the point where it last stopped, even with no committed offset
D. Disable offsets with `enable.auto.commit=false` and never commit, since the broker tracks progress itself without any help

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Auto-commit on a timer can commit offsets for records not yet processed, which loses messages on a crash. Committing after processing gives at-least-once, so consumers still need to be idempotent. `auto.offset.reset` only applies when there is no committed offset at all.
</details>

---

### Question 6
**Scenario:** Schemas will evolve over time. Consumers are upgraded first, and the upgraded consumers must still read the data already in the topic.

A. Recreate the topic with the new schema each time and restart every client together
B. Schema Registry with a compatibility mode, such as backward so new consumers can read old data
C. Version the topic name, publishing v2 to a new topic and deleting the old one
D. Send JSON without a schema, so each consumer ignores fields it does not recognize and nothing can ever break

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** BACKWARD is the Schema Registry default: consumers using the new schema can read data written with the last schema, so upgrade consumers first. It permits deleting fields and adding optional ones with defaults. Forward compatibility is the mirror image for producer-first upgrades, and full compatibility requires both. Getting the direction wrong is the classic Schema Registry mistake.

**Key Concept:** [Schema Registry compatibility types](https://docs.confluent.io/platform/current/schema-registry/fundamentals/schema-evolution.html)
</details>

---

### Question 7
**Scenario:** A topic must retain only the latest value per key indefinitely.

A. `cleanup.policy=compact`, which keeps the most recent value per key
B. A short retention window of a few minutes, so only the newest record for each key survives
C. A single partition, which stores only the latest message written to it, whatever the key
D. `cleanup.policy=delete` with long retention, which keeps only the latest value for each key

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Compaction is what makes a topic usable as a changelog or state store, and it is how Kafka Streams backs its state. A null value is the tombstone that marks a key for deletion, and keyless messages cannot be compacted at all.
</details>

---

### Question 8
**Scenario:** A stream must be joined to a lookup table of reference data.

A. A window join between the stream and the reference topic, using a 24-hour window on each side of every record
B. Query a database per record from inside the processor, which scales with the stream and adds no latency
C. A KStream-KTable join on a table built from a compacted, co-partitioned topic
D. Two consumers that each read one topic and exchange records through a shared in-memory cache

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Co-partitioning is the requirement people miss: both sides need the same key and the same partition count, or the join silently produces incomplete results. A GlobalKTable avoids co-partitioning by replicating the whole table to every instance, which suits small reference data.
</details>

---

### Question 9
**Scenario:** Which Kafka Streams operation requires a state store?

A. `map`, because it can change record keys and so must remember earlier values for every key it has seen
B. Stateful operations such as `aggregate`, `count`, `reduce`, joins, and windowing
C. `filter`, because it must remember which keys it has already dropped
D. `peek`, because it records each record it sees so later operators can replay the sequence

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** State stores are local RocksDB instances backed by a compacted changelog topic, which is what makes them recoverable after a failure. Stateless operations need no such backing, so scaling them is purely a matter of partitions.
</details>

---

### Question 10
**Scenario:** Data must move from a database into Kafka without writing code.

A. A Streams application that reads the database over JDBC
B. ksqlDB only, since CREATE STREAM connects to any database directly
C. Kafka Connect with a source connector, run in distributed mode for fault tolerance
D. A custom producer that polls the tables and tracks its own last-read offsets

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Connect handles offsets, restarts, and scaling for you, and Single Message Transforms cover light per-record changes without a separate application. Sink connectors move the other direction, and change-data-capture source connectors read the database log rather than polling.
</details>

---

### Question 11
**Scenario:** A consumer takes 10 minutes to process a batch and is repeatedly removed from the group.

A. Increase `max.poll.interval.ms` beyond the processing time, or reduce `max.poll.records` so batches are smaller
B. Increase `session.timeout.ms` only, to a value over ten minutes, because heartbeats are assumed to pause while a batch is processing
C. Disable heartbeats so the coordinator never considers the consumer dead during a long batch
D. Add consumers to the group, which shortens how long each batch takes the original consumer to process

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Heartbeats run on a background thread, so `session.timeout.ms` is not what fails here. `max.poll.interval.ms` bounds the time between poll calls, and exceeding it makes the group assume the consumer is stuck and rebalance.
</details>

---

### Question 12
**Scenario:** Exactly-once semantics are required across a consume-transform-produce cycle.

A. Transactions: a `transactional.id` on the producer, offsets committed in the transaction, and `read_committed` consumers
B. Idempotence alone, since it deduplicates retries across the consume, transform, and produce steps
C. Manual deduplication in the consumer using a table of processed message IDs, with the output records produced to Kafka separately, outside any transaction
D. `acks=all` with `min.insync.replicas` of 2, which makes the whole read-transform-write cycle atomic from the input topic to the output topic

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** All three parts are needed. Committing the consumer's offsets within the same transaction as the output records is what makes the cycle atomic, and `read_committed` is what stops downstream consumers seeing aborted records.
</details>

---

### Question 13
**Scenario:** Messages that repeatedly fail processing must not block the partition.

A. Retry forever with exponential backoff, since every message eventually succeeds once the downstream recovers
B. Skip them silently and commit the offset, so the partition keeps moving and no one is paged
C. Route them to a dead letter topic after bounded retries, with enough context to diagnose and replay
D. Delete the topic and recreate it, which discards the poison messages along with all the other unprocessed records in the topic

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** A poison message with unbounded retries halts the partition, so throughput for every other key behind it goes to zero. The dead letter record needs the original headers and error detail, otherwise it is unactionable.
</details>

---

### Question 14
**Scenario:** Producer throughput must increase without losing durability.

A. `acks=0`, so the producer never waits for a broker response, which removes the main throughput bottleneck
B. Increase `batch.size` and set `linger.ms` greater than zero to fill batches, and enable lz4 or zstd compression
C. More partitions only, with no change to batching, since each partition adds an independent sender thread and the durability settings stay the same
D. Larger messages, since fewer bigger records mean fewer requests and no change to the durability settings

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Batching and compression trade a small amount of latency for a large gain in throughput, and compression applies per batch so bigger batches compress better. Reducing `acks` gains throughput by giving up the durability the question says to keep.
</details>

---

### Question 15
**Scenario:** A stream of events must be aggregated into 5-minute buckets.

A. A global aggregation that rolls every event into one running total per key
B. A join between the event stream and a clock topic, emitting a record every five minutes
C. A filter that drops events outside each five-minute boundary and counts the rest
D. A windowed aggregation with a tumbling window, plus a grace period for late-arriving events and out-of-order data

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Tumbling windows are fixed and non-overlapping; hopping windows overlap; session windows are activity-driven with a gap. Grace period is the key operational setting, since events arriving after it are dropped rather than counted.
</details>

---

## Where to go deeper

- [CCDAK cert page](../../exams/confluent/certified-developer/) - notes, practice plan, strategy
- [Confluent Administrator practice questions](./confluent-certified-administrator.md) - the operations counterpart
- [Queues vs streams](../../learn/concepts/queues-vs-streams.md) - plain-English primer on the model
- [Idempotency explained](https://cloud.noblerworks.com/learn/concepts/idempotency-explained/) - why at-least-once needs idempotent consumers
- **[📖 Confluent certification](https://www.confluent.io/certification/)** - official exam guides
