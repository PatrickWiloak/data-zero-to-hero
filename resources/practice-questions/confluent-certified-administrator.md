---
last-updated: 2026-08-09
difficulty: intermediate
---

# Confluent Certified Administrator for Apache Kafka (CCAAK) - Practice Questions

15 questions weighted toward managing and operating (30%), monitoring and troubleshooting and Confluent Platform (20% each), then Kafka fundamentals and security (15% each).

> **Cert page:** [exams/confluent/certified-administrator/](../../exams/confluent/certified-administrator/)

---

### Question 1
**Scenario:** A broker fails in a cluster with replication factor 3 and `min.insync.replicas` 2.

A. The cluster stops accepting writes, because with one of three replicas gone the partition no longer meets its configured replication factor
B. Data is lost, because the failed broker held the only copy of the records that had not yet been flushed to its disk
C. Leadership moves to an in-sync replica and producing continues, since two in-sync replicas remain
D. Consumers stop until an operator restarts the failed broker, because they were fetching from the leader that was on it

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Leader election among in-sync replicas is what makes the failure survivable. Losing a second broker would drop the partition below `min.insync.replicas`, at which point `acks=all` producers receive `NotEnoughReplicas` rather than silently accepting unreplicated writes.
</details>

---

### Question 2
**Scenario:** Which component replaced ZooKeeper for cluster metadata?

A. Nothing; ZooKeeper is still required, since the controller stores topic and ACL metadata in znodes that only ZooKeeper can host
B. KRaft mode, where a quorum of controllers manages metadata in an internal Kafka log
C. Schema Registry, which stores schemas for the topics in an internal Kafka topic and validates compatibility but not cluster metadata
D. Kafka Connect, which runs connector tasks that move data in and out of Kafka and keeps cluster state in its own config topics

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** KRaft removes a separate system to operate and improves metadata scalability and failover time. Controllers can run on dedicated nodes or combined with brokers, and combined mode is discouraged for production clusters.
</details>

---

### Question 3
**Scenario:** Partitions are unevenly distributed after adding brokers.

A. New brokers take load automatically as soon as they join, because the controller rebalances existing partition replicas across them
B. Restart the whole cluster so that the controller recalculates replica placement from scratch across every broker
C. Create new topics only, since partitions on the new topics will be placed on the new brokers and the old ones can stay
D. Run a partition reassignment to move replicas onto the new brokers, throttling the reassignment to protect live traffic, and verify completion with the reassignment tool

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Adding a broker only affects new partitions, so existing load stays where it is. The throttle matters: an unthrottled reassignment saturates the network and degrades production traffic while it copies data.
</details>

---

### Question 4
**Scenario:** Consumer lag is growing steadily on one consumer group.

A. Diagnose first (slow or failing consumers, rebalances, too few consumers), then scale consumers up to at most the partition count
B. Restart the brokers one at a time so that the partition leaders are re-elected and consumer fetch sessions are rebuilt
C. Increase topic retention so that records are kept longer on disk and the group has more time to catch up on the backlog
D. Add partitions to the topic, because every extra partition lets the consumer group process more records in parallel straight away, whatever the cause of the lag

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Lag is a symptom with several causes, and adding partitions to an already-underconsumed topic does nothing. Adding consumers beyond the partition count also does nothing, which is why partition count is a capacity planning decision made early.
</details>

---

### Question 5
**Scenario:** Which metric indicates replicas falling behind their leader?

A. `UnderReplicatedPartitions`, along with `IsrShrinksPerSec` and replica lag
B. `BytesInPerSec`, which tracks the rate of data produced to a broker but says nothing about whether replicas are keeping pace
C. `MessagesInPerSec`, which measures how many records arrive at a broker per second and so reflects load rather than replica health
D. `RequestQueueSize`, which shows requests waiting for the broker's I/O threads and signals saturation, not replication lag

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** A sustained non-zero under-replicated count is one of the highest-signal broker alerts, because it means durability is degraded before anything has visibly failed. `OfflinePartitionsCount` above zero is the more severe version: those partitions have no leader at all.
</details>

---

### Question 6
**Scenario:** Disk usage on brokers is growing without bound.

A. Add disks only, since the brokers are simply out of space and growing the volumes removes the pressure without touching any topic
B. Review per-topic retention by time and size, plus compaction settings, and confirm segment rolling and deletion are actually occurring
C. Reduce the replication factor on the largest topics, which cuts the copies stored but does nothing to stop log growth
D. Delete topics that look inactive, since removing a topic frees its segments from every broker at once and is the quickest way to stop disk growth

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Retention applies per topic and overrides the broker default, so one misconfigured topic can consume a cluster. Deletion only happens on closed segments, so a large `segment.bytes` with low throughput can hold data far beyond the intended retention window.
</details>

---

### Question 7
**Scenario:** Clients must authenticate and traffic must be encrypted.

A. Network isolation only, placing the brokers on a private subnet so that only trusted hosts can reach the listeners, even from other networks
B. TLS for encryption plus SASL for authentication (SCRAM, GSSAPI, or OAUTHBEARER), with ACLs or RBAC for authorization
C. A firewall in front of the brokers that restricts source addresses and ports
D. ACLs alone, which identify principals and control what each may do and are enough to prove identity and also to encrypt traffic on the wire

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** The three concerns are distinct and each needs configuring: encryption in transit, identity, and what that identity may do. Listeners are configured per security protocol, which is how a cluster serves internal and external clients differently.
</details>

---

### Question 8
**Scenario:** A specific application must be allowed to read one topic only.

A. A network policy that limits which pods can reach the brokers, which controls connectivity but not which topics a principal reads or writes
B. An ACL granting Read on that topic and on its consumer group to that principal, with `allow.everyone.if.no.acl.found` disabled
C. A separate cluster for this application with its own brokers, which isolates the topic but is far heavier than an ACL
D. Give it cluster admin so that its consumer can always read the topic, and rely on the application code to read only that one topic and nothing else

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Consumers need the group resource permission as well as the topic, which is the pairing most often missed. Leaving the allow-everyone fallback enabled means an unmatched resource is wide open, which defeats the point of ACLs.
</details>

---

### Question 9
**Scenario:** A cluster must be replicated to a second data center for disaster recovery.

A. Copy the log directories to the remote site with a scheduled file sync, then start brokers there when the primary fails
B. Backups only, taken nightly to object storage and restored into a new cluster in the second data center after a failure
C. Cluster Linking or MirrorMaker 2 replicating topics, consumer groups, and offsets to the remote cluster
D. A shared filesystem mounted by brokers in two data centers

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Replicating offsets is what makes consumer failover viable rather than a full reprocess. Cluster Linking preserves offsets natively; MirrorMaker 2 translates them, which is why the two behave differently on cutover.
</details>

---

### Question 10
**Scenario:** A rolling broker upgrade must proceed without downtime.

A. Upgrade one broker at a time, wait for under-replicated partitions to reach zero, and set the inter-broker protocol version deliberately
B. Upgrade all brokers at once during a quiet period, then watch for under-replicated partitions as they restart together
C. Stop all brokers, upgrade them, and start them together, which is quicker and avoids mixed-version operation entirely
D. Upgrade clients first, since newer clients are always compatible with older brokers and so the broker roll can follow later without any risk of downtime

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Waiting for replicas to catch up is what preserves durability during the roll; moving on early can leave partitions with too few in-sync replicas. Pinning the inter-broker protocol version until every broker is upgraded is what keeps a rollback possible.
</details>

---

### Question 11
**Scenario:** Frequent consumer group rebalances are disrupting processing.

A. Ignore them, since rebalances are a normal part of consumer groups and the group recovers without any operator action
B. Disable consumer groups by assigning partitions manually in every consumer, which removes rebalances but also failover
C. Reduce the number of partitions on the topic, so there are fewer assignments to recompute each time the group changes
D. Find the cause (`max.poll.interval.ms` overruns, unstable instances, scaling churn); use cooperative sticky assignment and static membership

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Cooperative rebalancing avoids the stop-the-world pause of the eager protocol, and static membership with `group.instance.id` stops a rolling restart from triggering a rebalance per instance. Both reduce impact rather than removing the cause.
</details>

---

### Question 12
**Scenario:** Which Confluent Platform component enforces schema compatibility?

A. Schema Registry, storing schemas and validating new versions against the subject's compatibility mode
B. Control Center, which monitors clusters, topics and consumer lag through a web UI but does not validate schemas on write
C. REST Proxy, which exposes produce and consume operations over HTTP for clients that cannot use the native protocol
D. ksqlDB, which runs streaming SQL queries and reads the registered schemas but does not decide whether a new version is compatible

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Schema Registry stores its state in a compacted Kafka topic, so it is itself a Kafka client rather than an external database. Broker-side schema validation can additionally reject records whose schema is not registered.
</details>

---

### Question 13
**Scenario:** Broker request latency is high and the request queue is filling.

A. Add brokers immediately, since request queueing always means the cluster has run out of capacity and more nodes will spread the request load evenly
B. Restart the cluster so that the thread pools and request queues are emptied and the brokers begin from a clean state
C. Check thread counts against utilization, disk performance, and whether a few clients produce disproportionate load, before adding capacity
D. Reduce replication so that followers fetch less data from the leaders and so release network and I/O threads

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Saturated `num.network.threads` or `num.io.threads` produce queueing that looks like a capacity problem but is a configuration one. Client quotas are the other lever, bounding a single misbehaving application's effect on everyone else.
</details>

---

### Question 14
**Scenario:** A topic's partition count must increase.

A. Partitions can be reduced too, so the count can be changed in either direction with the topic tools at any time
B. Recreate the topic only, since partition counts are fixed at creation and the existing data must be copied into the new topic with a replay job
C. Partitions can be added but not removed, and adding them changes key-to-partition mapping, so ordering for existing keys breaks
D. It cannot change once the topic has been created, so the only way to get more parallelism is to add consumers to the group and keep the existing partitions

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Existing data is not redistributed, so a key that used to land on partition 2 may now land on partition 5 while its history stays behind. This is why over-provisioning partitions modestly at creation is easier than growing them later.
</details>

---

### Question 15
**Scenario:** An unclean leader election setting must be decided.

A. Enable it for availability on every topic, so that a partition with no in-sync replica can always elect a new leader and serve clients
B. Enable it everywhere, because the risk of losing a few unreplicated messages is smaller than the cost of an unavailable partition
C. It does not matter, because acknowledged messages are always replicated to every replica before a producer receives the acknowledgement
D. Leave `unclean.leader.election.enable` false by default, accepting unavailability over data loss; enable it only where availability outranks correctness

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** An unclean election promotes an out-of-sync replica, so acknowledged messages it never received are lost. That is a legitimate trade for some telemetry topics and unacceptable for a financial ledger, which is why it is a per-topic decision.
</details>

---

## Where to go deeper

- [CCAAK cert page](../../exams/confluent/certified-administrator/) - notes, practice plan, strategy
- [Confluent Developer practice questions](./confluent-certified-developer.md) - the application counterpart
- [Queues vs streams](../../learn/concepts/queues-vs-streams.md) - plain-English primer on the model
- [Observability basics](https://cloud.noblerworks.com/learn/concepts/observability-basics/) - what to monitor and why
- **[📖 Confluent certification](https://www.confluent.io/certification/)** - official exam guides
