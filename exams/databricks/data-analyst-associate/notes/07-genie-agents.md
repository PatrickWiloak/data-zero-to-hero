---
last-updated: 2026-11-07
applies-to: Databricks Data Analyst Associate exam guide (August 2026 edition, read 2026-11-07)
difficulty: intermediate
reading-time: 25 min
---

# Section 7: Developing, Sharing, and Maintaining AI/BI Genie Agents (Genie Spaces)

Weighted **12%**, about five or six questions. Four objectives: describe the purpose and components of Genie; create a Genie by defining sample questions and instructions, choosing a warehouse, curating datasets and vetting queries as trusted assets; assign permissions and distribute; and optimize through tracking, feedback and benchmarks. The guide uses both names, "Genie Agents" and "Genie spaces". The docs say a Genie Agent was formerly a Genie space.

## Table of Contents

1. [What Genie is for](#what-genie-is-for)
2. [Creating a Genie Agent](#creating-a-genie-agent)
3. [Permissions and distribution](#permissions-and-distribution)
4. [Optimizing and maintaining](#optimizing-and-maintaining)
5. [How the exam tests this section](#how-the-exam-tests-this-section)
6. [Key terms](#key-terms)

---

## What Genie is for

A Genie Agent lets business users ask data questions in natural language and get answers grounded in governed data. The idea is that data teams set up the trusted context once (which tables, what the business terms mean, the correct SQL for tricky questions) and everyone else asks questions without writing SQL. Answers stay governed by Unity Catalog, so a user sees only what their own permissions allow.

**[📖 Genie](https://docs.databricks.com/aws/en/genie/)** - Genie Agents are domain-specific environments of trusted data, metrics and business rules

## Creating a Genie Agent

From the sidebar choose **Genie Agents**, **New**, pick data sources, and **Create**. The ingredients, per the docs:

- **Data:** tables, views or metric views registered in Unity Catalog, up to 50 per agent. Only the sources you attach are used. Curate carefully: fewer, well-described tables answer better than a whole schema.
- **SQL warehouse:** a pro or serverless warehouse; the author needs CAN USE on it. Compute runs under the embedded warehouse credentials, but data access is checked against each user's own Unity Catalog permissions.
- **Instructions and example queries:** plain-language guidance (definitions such as "active customer means an order in the last 90 days") and example SQL that shows the right answer shape. Add these before sharing.
- **Common questions:** the sample questions shown on the landing page; authors define them and Genie fills remaining slots.
- **Settings:** title, default warehouse, tags, description.

**Trusted assets.** The exam guide says to vet queries as *trusted assets*. In practice this means curating the example SQL and Unity Catalog SQL functions that give Genie proven answers for important questions. The docs page I read for benchmarks does not use the phrase "trusted assets" and treats example queries and SQL functions as the equivalent, so confirm the exact label in the product.

**[📖 Set up a Genie Agent](https://docs.databricks.com/aws/en/genie/set-up)** - data, warehouse, instructions, sample questions and permissions

## Permissions and distribution

In the Share dialog the permission levels are **CAN MANAGE**, **CAN EDIT**, **CAN RUN** and **CAN VIEW**, assignable to users, groups or all account users. A consumer needs at least CAN RUN or CAN VIEW on the agent *and* SELECT on the underlying data. Creators get CAN MANAGE automatically, and an agent inherits permissions from its folder. A copy-link option helps distribution. The guide also mentions embedded links, external app integrations and channel apps; I did not find a docs page covering those in this pass, so learn the idea (publish where people already work) and check the product for the options. To restrict what each user sees, apply Unity Catalog **row filters and column masks** on the underlying tables.

## Optimizing and maintaining

Quality is a loop of measure, fix, re-measure:

1. **Track** questions and feedback. Users mark answers correct, "Fix it" or "Request review". The **Monitor** tab filters by time, rating, user and status. A weekly digest summarizes volume, active users and feedback.
2. **Remember feedback does not retrain it.** The docs state the agent's behaviour does not change from user feedback alone; you act on it by editing instructions and example queries ("Add as instruction" on a corrected SQL).
3. **Benchmark.** Build a set of benchmark questions (up to 500 per agent) with known-good SQL answers; include two to four phrasings of common questions. Chat mode compares Genie's result set with the supplied answer and rates it Good or Bad; agent mode uses an LLM judge.
4. **Refresh metadata.** Better Unity Catalog comments and descriptions on tables and columns improve answers because Genie reads them.
5. **Update from stakeholders.** When business definitions change, change the instructions.

**[📖 Benchmarks, monitoring and improving accuracy](https://docs.databricks.com/aws/en/genie/benchmarks)** - benchmark modes, feedback and tuning

## How the exam tests this section

- **Components:** data (Unity Catalog), warehouse, instructions, example queries, sample questions.
- **Why an answer is wrong:** missing or vague instructions, poor metadata, too many tables; fix those rather than "retrain".
- **Permissions:** viewer needs agent access plus data access.
- **Improving accuracy:** benchmarks, feedback review, instruction updates.

## Key terms

- **Genie Agent** - a domain-specific natural-language environment, formerly called a Genie space, built on trusted data and rules.
- **Instructions** - plain-language guidance and business definitions that tell Genie how to interpret questions.
- **Example query** - proven SQL attached to a Genie Agent to teach it the right answer shape.
- **Common questions** - the sample questions shown on a Genie Agent's landing page.
- **Benchmark** - a question with a known-good SQL answer used to measure a Genie Agent's accuracy.
- **CAN RUN** - a Genie permission that lets a user ask questions of an agent.
- **CAN MANAGE** - the highest Genie permission, granted automatically to the creator.
- **Row filter** - a Unity Catalog rule that restricts which rows a user can see in a table.
- **Column mask** - a Unity Catalog rule that hides or transforms values in a column for some users.
