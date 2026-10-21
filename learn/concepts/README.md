# 💡 Concepts - Bite-Size Topic Pages

> **Single-topic explanations. 5-10 minute reads. No exam scaffolding.**
>
> Read one when you keep seeing a term and want to actually understand it.

---

## How these are organized

Each page covers one concept:
- What it is, in plain English
- Why it exists (the problem it solves)
- A small concrete example
- What to look at next

These are not exam-objective summaries. They're "explain it like I'm a smart engineer who just hasn't been exposed to this yet."

If you want exam coverage, head back to the [Study Hub](../../STUDY-HUB.md).

**Brand new to all of this?** Start with **[Day One](https://cloud.noblerworks.com/learn/day-one/)** - terminal, git, HTTP, and servers - then come back here once that vocabulary feels familiar.

**Want a curated path through these?** Try **[Data from Scratch](../data-from-scratch.md)**, which orders these concepts into an eight-phase path to a data role. For cloud and AI, see [Cloud from Scratch](https://cloud.noblerworks.com/learn/cloud-from-scratch/) and [AI from Scratch](https://ai.noblerworks.com/learn/start-here/zero-to-hero-path/).

**Want them grouped by subject across the whole repo?** See the **[topic indexes](../../topics/)**.

---

## 🗄️ SQL and databases

| Topic | Read when... |
|-------|--------------|
| [SQL vs NoSQL](./sql-vs-nosql.md) | You're choosing a database and the options blur together |
| [SQL joins explained](./sql-joins-explained.md) | A join dropped rows or doubled a total and nothing errored |
| [Window functions explained](./window-functions-explained.md) | You need "latest row per key", running totals or rankings |
| [Indexes explained](./indexes-explained.md) | A query got slow as the table grew |
| [Transactions and ACID](./transactions-and-acid.md) | You want to know what isolation levels actually allow |
| [OLTP vs OLAP](./oltp-vs-olap.md) | You're asking why analytics doesn't run on the app database |
| [Replication and sharding](./replication-and-sharding.md) | One database server is no longer enough |
| [Eventual consistency](./eventual-consistency.md) | A read returned stale data and you want to know why |
| [Vector databases for data engineers](./vector-databases-for-data-engineers.md) | Someone asked you to run the embedding store |

## 📊 Analytics and modeling

| Topic | Read when... |
|-------|--------------|
| [Warehouses, lakes, and lakehouses](./warehouses-lakes-lakehouses.md) | Someone said "lakehouse" and you nodded |
| [Data modeling for analytics](./data-modeling-for-analytics.md) | A report double-counted and you need to know how |
| [Slowly changing dimensions](./slowly-changing-dimensions.md) | Last year's report changed when a customer moved |
| [Analytics engineering and dbt](./analytics-engineering-and-dbt.md) | Your transformation SQL needs tests and code review |
| [File formats and partitioning](./file-formats-and-partitioning.md) | A query is slow or a warehouse bill is high |

## 🔧 Pipelines and integration

| Topic | Read when... |
|-------|--------------|
| [ETL vs ELT](./etl-vs-elt.md) | You want to know why the transform moved after the load |
| [Data pipelines and orchestration](./data-pipelines-and-orchestration.md) | Your cron jobs have started depending on each other |
| [Change data capture](./change-data-capture.md) | Nightly full copies are too slow and miss deletes |

## 🌊 Streaming

| Topic | Read when... |
|-------|--------------|
| [Batch vs streaming](./batch-vs-streaming.md) | You're deciding whether a pipeline needs to be real-time |
| [Queues vs streams](./queues-vs-streams.md) | You can't pick between SQS and Kinesis |
| [Stream processing: windows and watermarks](./stream-processing-windows-and-watermarks.md) | You need to know when a streaming window is "done" |

## 🛡️ Quality, governance and contracts

| Topic | Read when... |
|-------|--------------|
| [Data quality and lineage](./data-quality-and-lineage.md) | A dashboard showed a wrong number and nothing errored |
| [Data governance and catalogs](./data-governance-and-catalogs.md) | Nobody knows which `customers` table is the real one |
| [Data contracts](./data-contracts.md) | An upstream rename broke your pipeline again |

---

## 🔗 On the sibling sites

Concepts outside data live on the other Zero to Hero sites and are linked here because data work leans on them.

### ☁️ Cloud Computing

| Topic | Read when... |
|-------|--------------|
| [What is cloud computing?](https://cloud.noblerworks.com/learn/concepts/what-is-cloud-computing/) | You want the foundational mental model |
| [IaaS vs PaaS vs SaaS](https://cloud.noblerworks.com/learn/concepts/iaas-paas-saas/) | The three terms keep coming up |
| [Regions and availability zones](https://cloud.noblerworks.com/learn/concepts/regions-and-availability-zones/) | You're asking "where does my code actually run?" |
| [Shared responsibility model](https://security.noblerworks.com/learn/concepts/shared-responsibility-model/) | You're wondering "what's AWS's job vs mine?" |
| [VPC explained](https://cloud.noblerworks.com/learn/concepts/vpc-explained/) | Networking inside the cloud confuses you |
| [IAM explained](https://security.noblerworks.com/learn/concepts/iam-explained/) | "Roles vs policies vs federation" is fuzzy |
| [Serverless explained](https://cloud.noblerworks.com/learn/concepts/serverless-explained/) | You keep hearing "serverless" without a clear definition |
| [CDN explained](https://cloud.noblerworks.com/learn/concepts/cdn-explained/) | You see CloudFront/CloudFlare/Akamai and wonder what they do |
| [Caching explained](https://cloud.noblerworks.com/learn/concepts/caching-explained/) | You want to know why caches are fast and why they lie |
| [Load balancing explained](https://cloud.noblerworks.com/learn/concepts/load-balancing-explained/) | "Layer 4 vs layer 7" keeps coming up and you want specifics |
| [Autoscaling explained](https://cloud.noblerworks.com/learn/concepts/autoscaling-explained/) | You want capacity to follow demand without an outage |
| [Cloud cost basics](https://cloud.noblerworks.com/learn/concepts/cloud-cost-basics/) | The bill went up and nobody decided to spend more |

### ☸️ Containers and Orchestration

| Topic | Read when... |
|-------|--------------|
| [Containers vs VMs](https://cloud.noblerworks.com/learn/concepts/containers-vs-vms/) | You don't know why containers replaced VMs |
| [Kubernetes in 10 minutes](https://cloud.noblerworks.com/learn/concepts/kubernetes-in-10-minutes/) | Everyone uses K8s; you want to know why |

### 🛠️ DevOps / Infrastructure

| Topic | Read when... |
|-------|--------------|
| [Terraform explained](https://cloud.noblerworks.com/learn/concepts/terraform-explained/) | You want to understand IaC without diving into HCL |
| [CI/CD explained](https://cloud.noblerworks.com/learn/concepts/cicd-explained/) | "Pipelines" is hand-waved everywhere; you want specifics |
| [Observability basics](https://cloud.noblerworks.com/learn/concepts/observability-basics/) | "Logs vs metrics vs traces" needs a one-page answer |
| [Idempotency explained](https://cloud.noblerworks.com/learn/concepts/idempotency-explained/) | You're worried about retries doing things twice |
| [Deployment strategies](https://cloud.noblerworks.com/learn/concepts/deployment-strategies/) | Rolling, blue-green, canary: which and when |

### 🌐 Networking and Security

| Topic | Read when... |
|-------|--------------|
| [DNS explained](https://cloud.noblerworks.com/learn/concepts/dns-explained/) | You want to understand how the internet finds things |
| [TLS and HTTPS](https://security.noblerworks.com/learn/concepts/tls-and-https/) | You want to know what the lock icon actually means |
| [Secrets management](https://security.noblerworks.com/learn/concepts/secrets-management/) | You need credentials in an app without leaving copies everywhere |

### 🤖 AI / Machine Learning - foundations

| Topic | Read when... |
|-------|--------------|
| [LLM basics](https://ai.noblerworks.com/learn/concepts/llm-basics/) | You want to understand what a language model actually does |
| [Transformer architecture](https://ai.noblerworks.com/learn/concepts/transformer-architecture/) | You're ready to look inside the model |
| [Embeddings and vector search](https://ai.noblerworks.com/learn/concepts/embeddings-and-vector-search/) | You want to understand semantic search |
| [Context windows and management](https://ai.noblerworks.com/learn/concepts/context-windows-and-management/) | You're hitting token limits in production |
| [Multimodal models](https://ai.noblerworks.com/learn/concepts/multimodal-models/) | You want to use images / audio / video in prompts |

### 🤖 AI / Machine Learning - building with LLMs

| Topic | Read when... |
|-------|--------------|
| [Prompt engineering](https://ai.noblerworks.com/learn/concepts/prompt-engineering/) | You want to write prompts that actually work |
| [Tool use and function calling](https://ai.noblerworks.com/learn/concepts/tool-use-and-function-calling/) | You want the model to call your code |
| [MCP explained](https://ai.noblerworks.com/learn/concepts/mcp-explained/) | "Model Context Protocol" keeps coming up |
| [Structured outputs](https://ai.noblerworks.com/learn/concepts/structured-outputs/) | You want guaranteed-valid JSON from the model |
| [RAG explained](https://ai.noblerworks.com/learn/concepts/rag-explained/) | "Retrieval-augmented generation" is everywhere |
| [Fine-tuning vs RAG](https://ai.noblerworks.com/learn/concepts/fine-tuning-vs-rag/) | You want to know which one to reach for |
| [AI agents explained](https://ai.noblerworks.com/learn/concepts/agents-explained/) | You're trying to figure out what "agent" really means |
| [Agentic loops](https://ai.noblerworks.com/learn/concepts/agentic-loops/) | You want the deep-dive on the loop, not just the concept |
| [Prompt caching](https://ai.noblerworks.com/learn/concepts/prompt-caching/) | Your input costs are higher than they should be |

### 🤖 AI / Machine Learning - operations

| Topic | Read when... |
|-------|--------------|
| [Evals for LLMs](https://ai.noblerworks.com/learn/concepts/evals-for-llms/) | You're shipping LLM features and wondering how to test them |
| [Guardrails and safety](https://ai.noblerworks.com/learn/concepts/guardrails-and-safety/) | You're worried about prompt injection or unsafe output |
| [Prompt injection explained](https://ai.noblerworks.com/safety/security/prompt-injection-explained/) | You want to know why prompt injection has no clean fix |
| [AI threat modeling](https://ai.noblerworks.com/safety/security/ai-threat-modeling/) | You're designing an AI feature and want to find the risk early |
| [Inference servers](https://ai.noblerworks.com/learn/concepts/inference-servers/) | You're considering self-hosting an open-weights model |
| [Quantization and distillation](https://ai.noblerworks.com/learn/concepts/quantization-and-distillation/) | You want a smaller, faster model |
| [GPUs for AI](https://ai.noblerworks.com/learn/concepts/gpus-for-ai/) | You want to know what VRAM, FLOPS, and bandwidth actually decide |

---

## 🔁 Companion docs

- **[Glossary](../glossary.md)** - 200+ data terms, one-line definitions
- **[Cloud from Scratch](https://cloud.noblerworks.com/learn/cloud-from-scratch/)** - structured 8-phase cloud path
- **[AI from Scratch](https://ai.noblerworks.com/learn/start-here/zero-to-hero-path/)** - structured AI/LLM learning path
- **[YouTube curation](../youtube.md)** - videos for visual learners
- **[Topic indexes](../../topics/)** - browse Learn + Build + Compare + Certify by subject

---

## 🤝 Suggest a topic

Missing a concept you keep getting asked about? Open an issue or PR. Criteria for new pages:
- One topic, focused
- 5-10 minute read
- Plain English, no exam framing
- Concrete example included

[Contributing Guide](../../CONTRIBUTING.md)
