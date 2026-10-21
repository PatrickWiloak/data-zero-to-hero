# 📚 Study Hub - Data Zero to Hero

> **The navigation hub for every data certification on this site: data engineering, analytics, and database administration across AWS, Azure, GCP, Databricks, Snowflake, Confluent (Kafka) and MongoDB.**
>
> Not chasing a cert? Start with **[Data from Scratch](./learn/data-from-scratch.md)**, the plain-English path from zero to a data role.

<!-- markdown="1" lets the documentation site render the markdown inside this
     centered block. GitHub ignores the attribute, so rendering there is unchanged. -->
<div align="center" markdown="1">

**[Browse it as a searchable site](https://data.noblerworks.com/)** - full-text search across every page, which is faster than this index once you know what you are looking for.

</div>

---

## 🗺️ Table of Contents

- [Quick Navigation](#-quick-navigation)
- [Not chasing a certification?](#-not-chasing-a-certification)
- [Decision Tree: Find Your Cert](#-decision-tree-find-your-cert)
- [Study Tracks by Career Path](#-study-tracks-by-career-path)
- [Certifications by Provider](#-certifications-by-provider)
- [Study Timeline Estimates](#-study-timeline-estimates)
- [Resource Library](#-resource-library)
- [Cost Planning](#-cost-planning)

---

## 🎯 Quick Navigation

| I Want To... | Go To... |
|--------------|----------|
| 🛤️ Go from zero to a data job | [Data from Scratch](./learn/data-from-scratch.md) |
| 💡 Understand one data concept | [Concepts](./learn/concepts/) |
| 🗂️ Browse by topic across pillars | [Topic indexes](./topics/) |
| 🎯 Choose the right cert | [Decision Tree](#-decision-tree-find-your-cert) |
| 🚀 Follow a career path | [Study Tracks](#-study-tracks-by-career-path) |
| 🧪 Find the lab for my cert | [Labs by certification](./resources/hands-on-projects/labs-by-cert.md) |
| 📝 Practice for an exam | [Practice question banks](./resources/practice-questions/) |
| 📅 Per-cert last-verified dates | [Freshness ledger](./docs/freshness.md) |
| 🃏 Flashcards for a cert | `flashcards.csv` in each cert dir that has one (Anki-importable) |
| ☁️ Learn cloud basics first | [Cloud Zero to Hero](https://cloud.noblerworks.com/) |
| 🤖 Move on to machine learning | [AI Zero to Hero](https://ai.noblerworks.com/) |

---

## 🎓 Not chasing a certification?

The **[Learn](./learn/)** pillar is for anyone who wants to understand data work without an exam date:

- **[Data from Scratch](./learn/data-from-scratch.md)** - eight phases from "what is a table?" to data engineer, analytics engineer or DBA.
- **[Concepts](./learn/concepts/)** - bite-size pages (5-10 min each) on SQL vs NoSQL, ETL vs ELT, batch vs streaming, lakehouses, modeling and more.
- **[Topic indexes](./topics/)** - databases and data engineering, each tying concepts, comparisons, builds and certs together.

Come back when you decide to certify. Many do, after the Learn pillar helps them pick a direction.

---

## 🌳 Decision Tree: Find Your Cert

```
What's your starting point?
│
├─ New to data (0-6 months)
│  └─> Azure DP-900 (data fundamentals) | SnowPro Core | MongoDB Associate Developer
│
├─ Building pipelines and platforms (data engineer)
│  ├─ On AWS        → AWS DEA-C01
│  ├─ On Azure      → Azure DP-700 (Fabric Data Engineer)
│  ├─ On GCP        → GCP Professional Data Engineer
│  ├─ On Databricks → Databricks Data Engineer Associate → Professional
│  ├─ On Snowflake  → SnowPro Core → SnowPro Advanced Data Engineer / Architect
│  └─ Streaming     → Confluent Certified Developer / Administrator (Kafka)
│
├─ Models, dashboards and BI (analytics engineer / analyst)
│  └─> Azure PL-300 (Power BI) → Azure DP-600 (Fabric Analytics Engineer) | SnowPro Core
│
└─ Running databases (DBA / database engineer)
   ├─ Relational    → Azure DP-300 | GCP Professional Cloud Database Engineer
   ├─ NoSQL         → MongoDB Associate DBA / Atlas Administrator | Azure DP-420 (Cosmos DB)
   └─ Platform admin → SnowPro Advanced Administrator | Databricks Lakehouse Platform Administrator
```

Retired exams (AWS DAS-C01 and DBS-C01, Azure DP-203) keep their guides for reference but are not recommended for new candidates.

---

## 🚀 Study Tracks by Career Path

### Data Engineering Track
1. A foundation in your primary cloud (see [Cloud Zero to Hero](https://cloud.noblerworks.com/)) or Azure DP-900
2. AWS Data Engineer Associate (DEA-C01) *or* Azure DP-700 *or* GCP Professional Data Engineer
3. Databricks Data Engineer Associate → Professional, *or* SnowPro Core → Advanced Data Engineer
4. Streaming specialization: Confluent Certified Developer (Kafka)

Full roadmap: [Data engineer certification roadmap](./resources/certification-roadmap-data-engineer.md)

### Analytics Engineering Track
1. Azure DP-900 for the vocabulary
2. Azure PL-300 (Power BI Data Analyst)
3. Azure DP-600 (Fabric Analytics Engineer) *or* SnowPro Core
4. Add: SnowPro Advanced Architect for warehouse design depth

### Database Administration Track
1. Azure DP-900 for the vocabulary
2. Azure DP-300 *or* GCP Professional Cloud Database Engineer
3. NoSQL: MongoDB Associate DBA → Atlas Administrator, *or* Azure DP-420 (Cosmos DB)
4. Platform admin: SnowPro Advanced Administrator, Databricks Lakehouse Platform Administrator

Full roadmap: [Database specialist certification roadmap](./resources/certification-roadmap-database-specialist.md)

---

## 📋 Certifications by Provider

<!-- BEGIN GENERATED: provider-table - edit .github/scripts/build-provider-indexes.py, not this block -->

| Provider | Certs | Highlights | Browse |
|----------|------:|------------|--------|
| **AWS** | 3 | **Data Engineer Associate (DEA-C01)**; retired Data Analytics (DAS-C01) and Database (DBS-C01) specialties retained for reference | [exams/aws/](./exams/aws/) |
| **Azure** | 7 | DP-900, DP-300, DP-420 (Cosmos DB), DP-600 and DP-700 (Fabric), PL-300 (Power BI); retired DP-203 retained | [exams/azure/](./exams/azure/) |
| **GCP** | 2 | Professional Data Engineer, Professional Cloud Database Engineer | [exams/gcp/](./exams/gcp/) |
| **Databricks** | 3 | Data Engineer (Associate + Professional), Lakehouse Platform Administrator | [exams/databricks/](./exams/databricks/) |
| **Snowflake** | 4 | SnowPro Core + 3 Advanced (Architect, Data Eng, Admin) | [exams/snowflake/](./exams/snowflake/) |
| **Confluent/Kafka** | 2 | Certified Developer, Certified Administrator | [exams/confluent/](./exams/confluent/) |
| **MongoDB** | 3 | Associate Developer, DBA, Atlas Administrator | [exams/mongodb/](./exams/mongodb/) |
| **CERTIFICATIONS TOTAL** | **24** | across 7 providers | |

The Certs column counts real exams, retired ones included.

<!-- END GENERATED: provider-table -->

---

## ⏱️ Study Timeline Estimates

| Cert Tier | Typical Prep Time | Daily Study | Examples |
|-----------|-------------------|-------------|----------|
| **Fundamentals** | 2-4 weeks | 1-2 hr | DP-900 |
| **Associate** | 6-10 weeks | 2-3 hr | DEA-C01, DP-700, DP-300, PL-300, Databricks DE Associate |
| **Professional** | 10-16 weeks | 2-4 hr | GCP Data Engineer, Databricks DE Professional, SnowPro Advanced |
| **Specialty** | 8-14 weeks | 2-4 hr | GCP Cloud Database Engineer, DP-420 |

---

## 📚 Resource Library

- **Service comparisons:** [Databases](./resources/service-comparison-databases.md) · [Messaging and queues](./resources/service-comparison-messaging-queues.md)
- **Architecture patterns:** [Data pipeline / ETL](./resources/architecture-patterns/data-pipeline-etl.md) · [Lakehouse](./resources/architecture-patterns/lakehouse-architecture.md) · [Data mesh](./resources/architecture-patterns/data-mesh.md)
- **Career roadmaps:** [Data Engineer](./resources/certification-roadmap-data-engineer.md) · [Database Specialist](./resources/certification-roadmap-database-specialist.md)
- **Hands-on:** [Build a data pipeline](./resources/hands-on-projects/build-data-pipeline.md)
- **Migration:** [Database migration](./resources/migration-guides/database-migration.md)
- **Interview prep:** [Data engineer](./resources/interview-prep/data-engineer-interview.md)
- **Persona playlist:** [Data engineer in 1 hour](./resources/playlist-data-engineer-1hour.md)

### Practice & Strategy
- [Practice Question Banks](./resources/practice-questions/) - scenario questions with explained answers, one bank per certification
- [Practice Resources Guide](./resources/practice-resources.md)
- [Study Strategies](./resources/study-strategies.md)
- [Exam Day Checklist](./resources/exam-day-checklist.md)
- [Budget Study Plan](./resources/budget-study-plan.md)
- [Community Resources](./resources/community-resources.md)
- [Recommended Courses](./resources/recommended-courses.md)

---

## 💰 Cost Planning

| Item | Typical Cost |
|------|-------------:|
| **All study materials on this site** | Free |
| **Official documentation** | Free |
| **Free tier and trial accounts** | Free |
| **Video course (optional)** | $15-50 |
| **Third-party practice tests (optional)** | $15-30 |
| **Exam fee (per cert)** | Varies by vendor; see each cert's fact sheet |

See the [budget study plan](./resources/budget-study-plan.md) for cost-minimization strategies.

---

## 📝 How to Use This Hub

1. Start at the [Decision Tree](#-decision-tree-find-your-cert) to identify your cert
2. Open that cert's directory: `exams/<provider>/<cert>/`
3. Read in order: `README.md` → `fact-sheet.md` → `practice-plan.md` → `notes/` → `scenarios.md` → `strategy.md`
4. Cross-reference with the [Resource Library](#-resource-library) and the [concept pages](./learn/concepts/)
5. Take practice exams when consistently scoring 80%+ on practice questions
6. Schedule the real exam when you hit 85%+ twice in a row

---

<div align="center" markdown="1">

**Ready to start? [Data from Scratch](./learn/data-from-scratch.md) · [Browse AWS](./exams/aws/) · [Browse Azure](./exams/azure/) · [Browse GCP](./exams/gcp/) · [Browse Databricks](./exams/databricks/) · [Browse Snowflake](./exams/snowflake/)**

*Maintained by [Patrick Wiloak](https://patricklukewilson.com) - one of the four free [Zero to Hero](https://noblerworks.com/) sites by Nobler Works.*

</div>
