---
last-updated: 2026-11-07
applies-to: Databricks Associate Developer for Apache Spark exam guide (published 2026-10-08, read 2026-11-07)
difficulty: intermediate
reading-time: 30 min
---

# Section 5: Structured Streaming

Weighted **10%**, about four or five questions. Four objectives: explain the engine (programming model, micro-batches, exactly-once, fault tolerance); create and write streaming DataFrames with output modes and sinks; perform basic operations including window and aggregation; and deduplicate streams with and without a watermark.

Streaming examples ran on PySpark 4.3.0 locally with the built-in `rate` source and the `memory` and `parquet` sinks on 2026-11-07.

## Table of Contents

1. [The programming model](#the-programming-model)
2. [Reading, writing, triggers and checkpoints](#reading-writing-triggers-and-checkpoints)
3. [Output modes](#output-modes)
4. [Windows and watermarks](#windows-and-watermarks)
5. [Streaming deduplication](#streaming-deduplication)
6. [How the exam tests this section](#how-the-exam-tests-this-section)
7. [Key terms](#key-terms)

---

## The programming model

Structured Streaming treats a live stream as a table that keeps growing. Each arriving item is a new row in an unbounded **input table**; your query runs incrementally over it and updates a **result table**; changed rows go to a **sink**. The same DataFrame code you write for a static dataset works on the stream, which is the whole appeal. By default the engine processes in **micro-batches**: it collects what has arrived, runs the query on that slice, writes output and records progress, then repeats.

**[📖 Structured Streaming basics](https://spark.apache.org/docs/latest/streaming/getting-started.html)** - the stream-as-table model, output modes and exactly-once semantics

**Fault tolerance and exactly-once.** Three pieces combine: sources are **replayable** with offsets (for example Kafka offsets), the engine writes the offsets and progress it has processed to a **checkpoint** (write-ahead log), and sinks are **idempotent**, so reprocessing after a failure does not duplicate output. Together this gives end-to-end exactly-once semantics, so you do not reason about at-least-once or at-most-once yourself. The consequence for your code: always set a `checkpointLocation`, and keep it stable across restarts so the query resumes where it stopped.

## Reading, writing, triggers and checkpoints

```python
# Ran with the built-in rate source.
rate = spark.readStream.format("rate").option("rowsPerSecond", 20).load()   # columns: timestamp, value

q = (rate.writeStream
        .outputMode("append")
        .format("parquet")                        # a file sink; also console, memory, kafka, foreachBatch, delta
        .option("path", out_dir)
        .option("checkpointLocation", ck_dir)
        .trigger(availableNow=True)               # process what is available, then stop
        .start())
q.awaitTermination()
```

A streaming DataFrame has `isStreaming = True`; defining it runs nothing until `start()` is called on the writer (lazy, like batch). **Triggers** control how often micro-batches run: the default starts the next batch as soon as the previous finishes; `processingTime="1 second"` sets an interval; `availableNow=True` processes all available data in batches and stops, which is how you run streaming logic as an incremental batch job. The **sinks** to know are file formats (Parquet and others), Kafka, console and memory (for debugging), and `foreachBatch` (run your own code per batch). In my run, the checkpoint directory afterwards held `offsets`, `commits`, `metadata` and `sources`: that is the progress record that makes recovery possible.

## Output modes

| Mode | Writes to the sink |
|---|---|
| **Append** | Only new rows added to the result since the last trigger; only valid when existing rows will not change |
| **Update** | Only rows that changed since the last trigger; same as append if there is no aggregation |
| **Complete** | The entire result table every time; needs aggregation and keeps all state |

Pick by query shape: a plain filter or map is append; a running count per key can be update or complete; a windowed aggregate with a watermark can be append (each window emitted once when final) or update.

## Windows and watermarks

Aggregations over time group on an event-time window, where **event time** is the timestamp inside the data, not the time Spark receives it:

```python
# Ran (update mode, memory sink).
ev = rate.withColumn("key", (F.col("value") % 5).cast("int")).withWatermark("timestamp", "10 seconds")
counts = ev.groupBy(F.window("timestamp", "2 seconds"), "key").count()
```

`F.window(col, "10 minutes", "5 minutes")` gives 10-minute windows sliding every 5 minutes. With no slide argument the windows are **tumbling**: fixed size, non-overlapping. Sliding windows overlap, so one row can fall into several windows. **Session windows** (`F.session_window`) have dynamic length: a session closes when no input arrives within the gap.

A **watermark** tells the engine how late data may be, so it can finish old windows and drop their state: `withWatermark("timestamp", "10 minutes")`. The docs' rule: for a window ending at T, state is kept and late data accepted until the maximum event time seen minus the threshold passes T; later data is dropped. Data delayed by less than the threshold is always aggregated. Requirements for cleanup: the output mode must be append or update (complete keeps all state), `withWatermark` must be called on the same column used in the aggregation, and it must come before the aggregation.

**[📖 Window operations and watermarking](https://spark.apache.org/docs/latest/streaming/apis-on-dataframes-and-datasets.html)** - tumbling, sliding and session windows, the watermark rule

Other limits (per the Structured Streaming guide): streaming Datasets do not support `distinct`, `limit`/`take`, or `count()` directly (use `groupBy().count()`), and sorting is supported only after an aggregation in complete mode.

## Streaming deduplication

Without a watermark, `dropDuplicates(["guid"])` must remember every key ever seen, so state grows without bound. With a watermark you include the event-time column in the key so old state can be dropped:

```python
# Ran together with the window query above.
ev.dropDuplicates(["key", "timestamp"])                                   # watermark already set on timestamp
streamingDf.withWatermark("eventTime", "10 seconds").dropDuplicates(["guid", "eventTime"])    # per the docs; not run
```

When duplicates carry different event times (for example a producer that stamps time at write), the docs offer `dropDuplicatesWithinWatermark(["guid"])`, which removes duplicates arriving within the watermark delay; it requires a watermark, and the delay should exceed the largest time gap between duplicates.

## How the exam tests this section

- **Why exactly-once works:** replayable sources, checkpoints and idempotent sinks.
- **Output mode for the query:** append for non-aggregating; update or complete for running aggregates; complete keeps all state.
- **Watermark purpose and placement:** bound state, drop very late data, before the aggregation on the same column.
- **Dedup:** unbounded state without a watermark.
- **availableNow** for incremental batch.

## Key terms

- **Structured Streaming** - Spark's stream processing engine that treats a stream as an unbounded table queried with DataFrame code.
- **Micro-batch** - a small slice of arriving data that the engine processes as one incremental query run.
- **Checkpoint** - stored progress information, including offsets, that lets a streaming query resume after failure.
- **Trigger** - the setting that controls when each micro-batch runs, such as a fixed interval or available-now.
- **Output mode** - the setting that decides which rows of the result table are written to the sink: append, update or complete.
- **Sink** - the destination a streaming query writes to, such as files, Kafka or a console.
- **Event time** - the timestamp carried inside the data, as opposed to the time Spark processes it.
- **Watermark** - a threshold on how late data may arrive, used to finalize windows and drop old state.
- **Tumbling window** - a fixed-size, non-overlapping time window for aggregation.
- **Sliding window** - an overlapping time window defined by a window length and a smaller slide interval.
- **Session window** - a window whose length is dynamic and closes when no input arrives within a gap.
- **dropDuplicatesWithinWatermark** - a method that removes duplicate records arriving within the watermark delay, even with different event times.
