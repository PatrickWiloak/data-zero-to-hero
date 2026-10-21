---
last-updated: 2026-10-21
---

# 🎥 YouTube - Channels and Videos Worth Your Time

> **A filtered list of YouTube content for learning SQL, databases, data engineering and analytics engineering. No algorithmic slop, no engagement-bait thumbnails - just channels and individual videos that actually teach.**
>
> Curated by [Patrick Wiloak](https://patricklukewilson.com). If you find dead links or want to suggest additions, see [CONTRIBUTING.md](../CONTRIBUTING.md).

---

## How to use this page

YouTube is one of the best free resources for learning data work - if you can find the signal in the noise. This page is the signal.

- **Channels** are sorted by what they're best for, not subscriber count.
- **Single-video deep dives** are linked when one video is worth more than a whole playlist elsewhere. Where a video gets re-uploaded or renamed, the link is a YouTube search so it does not rot.
- Tools change fast (dbt, Fabric, Databricks rename things yearly). The fundamentals - SQL, modeling, how databases work - do not, so this page leans toward channels that teach those.

If you want vendor-specific exam content, that lives in the [Study Hub](../STUDY-HUB.md). This page is for learning the **why** and **how**, not memorizing exam objectives.

Cloud, security and AI channels moved to their own sites: **[Cloud YouTube picks](https://cloud.noblerworks.com/learn/youtube/)**, **[Security YouTube picks](https://security.noblerworks.com/learn/youtube/)**, and the AI channels on **[AI Zero to Hero](https://ai.noblerworks.com/)**.

---

## 🏠 Start here: my channel

**[@patrickwiloak](https://youtube.com/@patrickwiloak)** - my channel.

Cloud certification strategy, Claude Code workflows, agent architecture, and "what I learned passing 16 cloud certs while working at AWS." Practical, no fluff.

If you're using this repo, subscribe - new content lands here first.

---

## 🧮 SQL - Best Channels

**[techTFQ](https://youtube.com/@techTFQ)**
- Best for: SQL from beginner to interview level
- Why: Each video takes one real problem (window functions, recursive CTEs, tricky joins) and works it through slowly, with the dataset on screen.
- Use when: You are in [phase 1 of Data from Scratch](./data-from-scratch.md) or preparing for a SQL interview.

**[Ankit Bansal](https://youtube.com/@ankitbansal6)**
- Best for: SQL interview problems, one per video
- Why: Hundreds of real interview questions solved several ways, which teaches you to see the shape of a problem.
- Use when: You can write SQL and want to get fast and fluent.

**[Alex The Analyst](https://youtube.com/@AlexTheAnalyst)**
- Best for: SQL, Excel and BI for aspiring data analysts
- Why: Friendly, structured bootcamp-style series with portfolio projects.
- Use when: You are coming from a non-technical background.

**[freeCodeCamp.org](https://youtube.com/@freecodecamp)**
- Best for: Long-form full courses (SQL, PostgreSQL, Python for data, Spark)
- Why: Free, comprehensive, frequently refreshed.
- Use when: You want a whole course in one sitting or one weekend.

---

## 🗄️ Databases - Best Channels

**[CMU Database Group](https://youtube.com/@CMUDatabaseGroup)**
- Best for: How databases actually work inside - storage, indexes, query execution, concurrency control, recovery
- Why: Andy Pavlo's Carnegie Mellon courses, recorded in full and free. The best university database material online.
- Use when: You want to understand [indexes](./concepts/indexes-explained.md) and [transactions](./concepts/transactions-and-acid.md) properly, or you are going down the DBA lane.

**[Hussein Nasser](https://youtube.com/@hnasr)**
- Best for: Database and backend internals explained by an engineer who clearly enjoys them
- Why: Long, thoughtful videos on isolation levels, replication, sharding, connection pooling, and why PostgreSQL does what it does.
- Use when: A concept page here made you want to go one level deeper.

**[Martin Kleppmann](https://youtube.com/@kleppmann)**
- Best for: Distributed systems, from the author of *Designing Data-Intensive Applications*
- Why: His Cambridge distributed systems lecture series is free and the clearest treatment of replication, consensus and consistency anywhere.
- Use when: You are reading [Replication and sharding](./concepts/replication-and-sharding.md) or [Eventual consistency](./concepts/eventual-consistency.md) and want the theory.

**[ByteByteGo](https://youtube.com/@ByteByteGo)**
- Best for: Short animated system-design explainers, many on databases, caching and messaging
- Why: Good diagrams, 5-10 minutes each. Breadth, not depth.
- Use when: You need a quick mental model before a deeper read.

**[MongoDB](https://youtube.com/@MongoDB)**
- Best for: Document modeling, Atlas, the MongoDB certifications
- Use when: You are preparing for a [MongoDB exam](../exams/mongodb/).

---

## 🔧 Data Engineering - Best Channels

**[DataTalksClub](https://youtube.com/@DataTalksClub)**
- Best for: The free **Data Engineering Zoomcamp** - Docker, orchestration, warehousing, dbt, Spark, Kafka - as a full course with homework
- Why: The most complete free data engineering course on YouTube, refreshed every year, with a community behind it.
- Use when: You want a structured course alongside [Data from Scratch](./data-from-scratch.md).

**[Seattle Data Guy](https://youtube.com/@SeattleDataGuy)**
- Best for: What data engineering actually looks like at work - careers, architecture, tool choices, consulting stories
- Why: Ben Rogojan has done the job for years and talks about trade-offs, not hype.
- Use when: You are deciding whether this career is for you, or preparing for a design interview.

**[Darshil Parmar](https://youtube.com/@DarshilParmar)**
- Best for: End-to-end project walkthroughs on AWS, Azure and GCP
- Why: Each project is a complete pipeline you can rebuild for a portfolio.
- Use when: You finished [Build a data pipeline](../resources/hands-on-projects/build-data-pipeline.md) and want more builds.

**[Kahan Data Solutions](https://youtube.com/@KahanDataSolutions)**
- Best for: Modern data stack tools - dbt, Snowflake, Airflow, Fivetran - in short, practical videos
- Use when: You are setting up one specific tool and want to see it done.

**[Andreas Kretz - Plumbers of Data Science](https://youtube.com/@andreaskayy)**
- Best for: Data engineering fundamentals and the platform-and-pipeline view of the job
- Use when: You want the "how does it all fit together" picture.

---

## 🌊 Streaming and Kafka

**[Confluent](https://youtube.com/@Confluent)**
- Best for: Apache Kafka, Kafka Streams, Flink and stream processing concepts, from the company founded by Kafka's creators
- Why: The "Apache Kafka 101" and stream processing course series are free, short, and accurate.
- Use when: You are in [phase 4 of Data from Scratch](./data-from-scratch.md) or preparing for a [Confluent certification](../exams/confluent/).

**[Stephane Maarek](https://youtube.com/@StephaneMaarek)**
- Best for: Kafka for beginners, and AWS data certifications
- Why: Tight, accurate, exam-aligned. His Kafka material is how many people first learned it.
- Use when: You have a Kafka or AWS exam date.

---

## 🏛️ Platforms: lakehouse, warehouse, BI

**[Databricks](https://youtube.com/@Databricks)**
- Best for: Lakehouse, Delta Lake, Unity Catalog, Spark - plus every Data + AI Summit talk
- Use when: You use Databricks at work or are preparing for a [Databricks exam](../exams/databricks/).

**[Advancing Analytics](https://youtube.com/@AdvancingAnalytics)**
- Best for: Independent, opinionated takes on Databricks, Fabric and lakehouse design
- Why: Practitioners who will tell you when a feature is not ready yet.
- Use when: You want a second opinion beyond the vendor's own channel.

**[Bryan Cafferky](https://youtube.com/@BryanCafferky)**
- Best for: Spark, Databricks and Python for data, taught carefully from the basics
- Use when: Spark's mental model has not clicked yet.

**[Guy in a Cube](https://youtube.com/@GuyInACube)**
- Best for: Power BI and Microsoft Fabric
- Why: Two Microsoft people answering real questions every week, for years.
- Use when: You are on the analytics engineer lane, or preparing for [PL-300](../exams/azure/pl-300/) or [DP-600](../exams/azure/dp-600/).

**[AWS Events](https://youtube.com/@AWSEventsChannel)**
- Best for: re:Invent sessions on Redshift, Glue, Kinesis, Lake Formation and data architecture
- Use when: You want the service team's own deep dive. Search the channel for the service plus "deep dive".

---

## 🎯 Single-Video Deep Dives

### Databases
- **[CMU Intro to Database Systems - full course](https://www.youtube.com/results?search_query=CMU+15-445+intro+to+database+systems)** - the whole semester; the lectures on indexes, joins and concurrency control are worth watching on their own.
- **[Martin Kleppmann - Distributed Systems lecture series](https://www.youtube.com/results?search_query=kleppmann+distributed+systems+lecture)** - eight short lectures; watch the replication and consistency ones first.
- **[Martin Kleppmann - "Turning the database inside-out"](https://www.youtube.com/results?search_query=kleppmann+turning+the+database+inside+out)** - the talk that explains why logs, CDC and stream processing belong together.

### Data engineering
- **[Data Engineering Zoomcamp](https://www.youtube.com/results?search_query=data+engineering+zoomcamp)** - the full course playlist from DataTalksClub.
- **[Jay Kreps - "I heart logs"](https://www.youtube.com/results?search_query=jay+kreps+i+heart+logs)** - the creator of Kafka on why the log is the core abstraction; the thinking behind the Kappa architecture.
- **[Tyler Akidau - "The evolution of massive-scale data processing"](https://www.youtube.com/results?search_query=tyler+akidau+evolution+of+massive+scale+data+processing)** - from MapReduce to the Dataflow model, by the author of *Streaming Systems*. Watermarks finally make sense.

### SQL
- **[techTFQ - window functions](https://www.youtube.com/results?search_query=techTFQ+window+functions)** - pairs with [Window functions explained](./concepts/window-functions-explained.md).

---

## 🚫 Channels to be cautious of

I'm not naming names, but be skeptical of:

- **Cert farms** - channels that pump out new "Pass the exam in 2 weeks!" videos with engagement-bait thumbnails. Often outdated, sometimes wrong.
- **"Become a data engineer in 30 days"** - the job takes months of SQL and modeling practice. Videos that skip to tools skip the part that gets you hired.
- **Tool-of-the-week channels** - a new framework every video, no fundamentals. The tools will change; joins and grain will not.
- **AI-narrated faceless channels** - if the voice is synthetic and the script reads like a Wikipedia summary, it probably is one. Quality is hit-or-miss; sources usually missing.

Heuristic: if the thumbnail has a shocked face and red arrows, the actual content is probably thin.

---

## 📺 Recommended watch order for total beginners

If you have never written SQL, here is a starter sequence of about 12 hours:

1. **freeCodeCamp - a full SQL or PostgreSQL course** (4 hours, watch at 1.5x) - basic SQL literacy
2. **techTFQ - joins and window functions videos** (2 hours) - the two things every SQL interview tests
3. **ByteByteGo - database and caching explainers** (1 hour) - the mental models around the database
4. **Seattle Data Guy - "what does a data engineer do" videos** (1 hour) - what the job actually is
5. **Data Engineering Zoomcamp - module 1** (3 hours) - Docker, PostgreSQL, your first pipeline
6. **Confluent - Apache Kafka 101** (1 hour) - what streaming is for

After this, follow [Data from Scratch](./data-from-scratch.md) and come back here when a phase wants a video.

---

## 🔁 Companion docs

- **[Data from Scratch](./data-from-scratch.md)** - the structured path from SQL to a data role
- **[Glossary](./glossary.md)** - look up any term you encounter in these videos
- **[Concepts](./concepts/)** - bite-size written explanations of single topics
- **[Cloud YouTube picks](https://cloud.noblerworks.com/learn/youtube/)** - cloud, Kubernetes and networking channels

---

## 🤝 Suggest an addition

Found a channel or video that should be here? Open a PR or issue. Criteria:
- Still accurate today (tool names rot fast; SQL does not)
- Teaches you something, not just hype
- Not a sponsorship-disguised-as-content video
- Bonus: small or undiscovered creators teaching well

[Contributing Guide](../CONTRIBUTING.md)
