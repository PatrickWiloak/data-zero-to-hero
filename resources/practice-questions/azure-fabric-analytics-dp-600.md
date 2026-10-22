---
last-updated: 2026-10-22
difficulty: intermediate
---

# Microsoft Fabric Analytics Engineer Associate (DP-600) - Practice Questions

30 questions for DP-600 prep, weighted toward preparing data (45-50%), then maintaining a data analytics solution (25-30%) and implementing and managing semantic models (25-30%). Questions 16-30 are tagged with their domain from the skills outline measured as of 2026-10-19.

> **Cert page:** [exams/azure/dp-600/](../../exams/azure/dp-600/)

---

### Question 1
**Scenario:** What storage layer underpins every Fabric workload?

A. Azure SQL Database, provisioned separately for each workspace to hold all of its tables
B. Cosmos DB
C. Azure Blob Storage with CSV files, created as a separate storage account inside each workspace for its own items
D. OneLake, a single logical data lake per tenant storing data in Delta Parquet

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** OneLake is provisioned once per tenant and every workload writes Delta Parquet into it, which is what lets a lakehouse table be queried by Spark, the SQL analytics endpoint, and Direct Lake without copying. "One copy of the data" is the central Fabric idea.
</details>

---

### Question 2
**Scenario:** A team needs to query existing data in an ADLS Gen2 account from Fabric without copying it.

A. Dataflow Gen2 that reads the ADLS Gen2 account on a schedule and loads it into a lakehouse table
B. A pipeline copy activity
C. A OneLake shortcut to the external location
D. Export to CSV

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Shortcuts create a reference to data in ADLS Gen2, Amazon S3, or another Fabric item, so it appears in your lakehouse without duplication or a refresh schedule. Copy activities and dataflows both move data, which adds cost, latency, and a second copy to keep consistent.
</details>

---

### Question 3
**Scenario:** A semantic model must query lakehouse Delta tables directly at import-like speed without scheduled refresh.

A. DirectQuery to the SQL endpoint
B. Direct Lake mode
C. Live connection to a dataset
D. Import mode with scheduled refresh

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Direct Lake reads Delta Parquet from OneLake into the engine's memory on demand, giving import-like performance with no refresh and no per-query trip to a source system. When a query cannot be served that way it falls back to DirectQuery, which is why watching for fallback is part of tuning a Direct Lake model.
</details>

---

### Question 4
**Scenario:** Which item type gives you both a Spark-writable table area and a read-only T-SQL query surface?

A. Warehouse with T-SQL writes
B. Lakehouse
C. KQL database (Eventhouse)
D. Power BI semantic model

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** A lakehouse holds files and Delta tables written by Spark, and it exposes a SQL analytics endpoint that is read-only. A warehouse is the opposite arrangement: full T-SQL read and write, including multi-table transactions. Choosing between them usually comes down to whether your team writes Spark or T-SQL.
</details>

---

### Question 5
**Scenario:** A dimensional model should perform well and be easy to write DAX against.

A. Many-to-many relationships throughout, so each table can filter every other table in whichever direction the report needs
B. A single wide flat table
C. A fully normalized snowflake
D. A star schema: one fact table joined to dimension tables by single-direction one-to-many relationships

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** The VertiPaq engine and DAX filter propagation are both built around star schemas, so this is not just tidiness: it changes performance and makes measures behave predictably. Bidirectional and many-to-many relationships introduce ambiguity and are the usual root cause of "the number is wrong depending on which slicer I use."
</details>

---

### Question 6
**Scenario:** A measure must ignore all filters from slicers on the report page.

A. `SUM(Table[Sales])` with no CALCULATE wrapper
B. `CALCULATE([Sales], KEEPFILTERS(Table))`
C. `CALCULATE([Sales], ALL(Table))`
D. `RELATED(Table[Sales])` on the dimension side

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** `ALL()` removes filters from the specified table or columns inside the `CALCULATE` modification. `KEEPFILTERS` does the opposite, intersecting rather than replacing. A bare `SUM` respects the filter context, and `RELATED` looks up a value across a relationship.
</details>

---

### Question 7
**Scenario:** A capacity is throttled and interactive reports are slow while a large Spark job runs.

A. Review capacity metrics, then scale the SKU, move the workload to another capacity, or reschedule the job
B. Switch to DirectQuery so the semantic model stops using capacity memory and queries run against the source instead
C. Buy more Power BI Pro licenses
D. Delete the semantic model

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Fabric capacity units are shared across all workloads in a workspace's capacity, so a heavy background job can crowd out interactive queries, and smoothing plus bursting only absorbs so much. The Capacity Metrics app shows which items consumed the units, which is the prerequisite for any of the three fixes.
</details>

---

### Question 8
**Scenario:** Sensitive columns in a warehouse must be hidden from a group of analysts.

A. Column-level security granting SELECT only on permitted columns
B. A separate copy of the table
C. Hiding the column in the report
D. Renaming the column

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Column-level security is enforced by the engine, so it holds regardless of how the user connects. Hiding a field in a report is cosmetic and is bypassed by anyone who builds their own report or connects with another tool. Row-level and object-level security cover the other two shapes of this requirement.
</details>

---

### Question 9
**Scenario:** A medallion architecture is being adopted. What belongs in the bronze layer?

A. Aggregated business metrics prepared per department, with deduplicated and conformed dimension keys
B. Raw ingested data kept as it arrived from the source
C. The star schema of conformed dimensions and facts that reports consume
D. Report visuals and the dashboards that business users build on top of them

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Bronze is raw and append-only so you can always reprocess. Silver is cleaned, conformed, and deduplicated. Gold is the business-ready dimensional model that reports consume. The value of keeping bronze intact is that a logic bug in silver is recoverable without re-ingesting from source.
</details>

---

### Question 10
**Scenario:** Analysts need near-real-time streaming telemetry with sub-second query over high-volume logs.

A. Warehouse
B. Eventhouse with a KQL database
C. Dataflow Gen2 on an hourly refresh schedule
D. Lakehouse batch pipeline that loads logs every minute into Delta tables queried from the SQL analytics endpoint

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** KQL databases are built for high-ingest, time-series and log data with fast text and time-range queries, which is a different engine profile from a warehouse. Batch pipelines and dataflows introduce latency measured in minutes at best.
</details>

---

### Question 11
**Scenario:** A workspace must promote content from development to production with review.

A. Git integration for source control plus deployment pipelines with dev, test, and production stages
B. Share the workspace
C. Export and import PBIX files between workspaces, with a reviewer comparing the files before each manual import into production
D. Copy items by hand

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Git integration versions the item definitions so changes are reviewable, and deployment pipelines move content between workspace stages with rules that rebind data sources per stage. Manual copying loses history and reliably drifts.
</details>

---

### Question 12
**Scenario:** A DAX measure calculates year-to-date sales.

A. `SUM(Sales[Amount])`
B. `COUNTROWS('Date')`
C. `TOTALYTD([Sales], 'Date'[Date])` with a marked date table
D. `RELATED(Sales[Amount])`

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Time intelligence functions require a proper date table marked as a date table, with a contiguous set of dates covering the full range. Without that, functions such as `TOTALYTD` and `SAMEPERIODLASTYEAR` return wrong or blank results, which is the most common time-intelligence bug.
</details>

---

### Question 13
**Scenario:** A large fact table's Direct Lake model keeps falling back to DirectQuery.

A. Nothing can be done
B. Increase the refresh frequency of the semantic model to every five minutes and move the workspace to the next SKU
C. Switch every model to import mode and schedule a refresh every hour so fallback to DirectQuery never happens
D. Investigate the cause: unsupported features, SKU guardrail limits, or views instead of Delta tables

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Fallback is a signal, not a setting. Common causes are querying a SQL view rather than a Delta table, row counts beyond the SKU guardrail, or unsupported model constructs. You can also set the fallback behavior to error so the problem is visible instead of silently slow.
</details>

---

### Question 14
**Scenario:** Data lineage must be traceable from a report back to its source tables.

A. The audit log
B. The activity log
C. The lineage view in the workspace, plus item-level impact analysis
D. Manual documentation

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Lineage view renders the dependency graph across pipelines, lakehouses, semantic models, and reports, and impact analysis shows what breaks downstream if you change an item. Activity and audit logs record user actions rather than data dependencies.
</details>

---

### Question 15
**Scenario:** A Delta table accumulates thousands of small files and queries slow down.

A. Run `OPTIMIZE` (with V-Order) and `VACUUM` to compact small files and remove old ones
B. Convert the table to CSV files, which can be read in parallel without the overhead of transaction log checks
C. Rewrite the queries
D. Increase capacity to the next SKU so that the extra compute reads the many small files faster

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** The small file problem is the classic lakehouse performance issue: each file adds read overhead. `OPTIMIZE` compacts them into fewer, larger files, V-Order improves compression for the Fabric engines, and `VACUUM` removes files no longer referenced after the retention window.
</details>

---

### Question 16 - Domain 1: Maintain a data analytics solution
**Scenario:** An external auditor needs to open one finance report in a workspace that also holds draft reports, notebooks and a lakehouse. The auditor must not see or browse any other item in that workspace. How should you grant access?

A. Add the auditor to the workspace with the Viewer role
B. Share only that report with the auditor, with no build or reshare rights
C. Add the auditor to the Contributor role and hide the other items
D. Publish the report to the web and send the auditor the public link

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Item-level sharing grants access to a single item without a workspace role, so the auditor sees only that report. A is wrong because any workspace role, even Viewer, lets the user see every item in the workspace. C is wrong because Contributor can edit and delete content, and hiding items is not a security boundary. D is wrong because publish to web makes the report public to anyone with the link, which is unacceptable for finance data.
</details>

---

### Question 17 - Domain 1: Maintain a data analytics solution
**Scenario:** A semantic model holds salary data. Compliance requires that the data stays protected when users export it from reports to Excel or PDF. What should you do?

A. Mark the semantic model as Certified so users know it is sensitive
B. Add a text box to each report page warning users not to export
C. Turn off the Export data setting for every user in the tenant
D. Apply a Microsoft Purview sensitivity label such as Highly Confidential to the model

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Sensitivity labels flow downstream to reports built on the model and travel with supported exports, so the exported Excel or PDF file keeps its label and protection. A is wrong because endorsement signals quality and trust, not confidentiality, and adds no protection. B is wrong because a warning is not a control. C is wrong because a tenant-wide block stops legitimate export for every other workload, which is far broader than the requirement.
</details>

---

### Question 18 - Domain 1: Maintain a data analytics solution
**Scenario:** A center of excellence wants analysts to find the one authoritative sales semantic model instead of building their own. Only a small review team should be able to grant that status. Which approach fits?

A. Promote the model, which any contributor can do from the item settings
B. Add "OFFICIAL" to the model name and pin it to the workspace top
C. Certify the model, with certification restricted to the review team by the admin
D. Move the model into its own workspace with a dedicated capacity

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Certification is the endorsement level a Fabric admin limits to named users or groups, so only the review team can grant it, and certified items rank higher in the OneLake catalog. A is wrong because anyone with write permission can promote, so promotion does not prove review. B is wrong because a naming convention is not enforced and anyone can copy it. D is wrong because a separate workspace and capacity change isolation and cost, not discoverability or trust.
</details>

---

### Question 19 - Domain 1: Maintain a data analytics solution
**Scenario:** Two developers edit the same Power BI report and model in Power BI Desktop. They want to commit changes to Git and review them as readable line-by-line diffs in pull requests. What should they use?

A. Save the work as a Power BI Desktop project (.pbip) with TMDL model files
B. Save the work as a .pbix file and commit it to the repository
C. Save a Power BI template (.pbit) file after every change
D. Export a .pbids data source file and commit that instead

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** A .pbip project saves the report and model as a folder of text files, with the model in TMDL, so Git can diff and merge them line by line. B is wrong because a .pbix is a binary package, so a pull request shows only that the file changed. C is wrong because a .pbit is a reusable template with no data, meant for distribution rather than source control. D is wrong because a .pbids file only holds connection details and contains none of the model or report.
</details>

---

### Question 20 - Domain 2: Prepare data
**Scenario:** A warehouse staging table receives the same customer several times, each with a different `ModifiedDate`. The silver table must keep only the latest row per `CustomerID`. Which T-SQL pattern should you use?

A. `SELECT DISTINCT *` from the staging table into the silver table
B. `ROW_NUMBER() OVER (PARTITION BY CustomerID ORDER BY ModifiedDate DESC)` and keep row 1
C. `GROUP BY CustomerID` with `MAX()` applied separately to every column
D. `UNION` the staging table with itself to collapse duplicate rows

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Numbering rows within each customer by newest date and keeping row 1 returns exactly one complete, latest record per key. A is wrong because the rows differ in `ModifiedDate`, so they are not true duplicates and `DISTINCT` keeps them all. C is wrong because taking `MAX()` per column can mix values from different rows into a record that never existed. D is wrong because `UNION` only removes rows that are identical in every column, which these are not.
</details>

---

### Question 21 - Domain 2: Prepare data
**Scenario:** An Eventhouse table `DeviceTelemetry` has `Timestamp`, `DeviceId` and `Temperature`. An analyst needs the average temperature per device in 5-minute buckets for the last hour. Which KQL query fits?

A. `DeviceTelemetry | where Timestamp > ago(1h) | top 5 by Temperature`
B. `DeviceTelemetry | summarize avg(Temperature) by DeviceId | where Timestamp > ago(1h)`
C. `DeviceTelemetry | where Timestamp > ago(1h) | summarize avg(Temperature) by DeviceId, bin(Timestamp, 5m)`
D. `DeviceTelemetry | extend Bucket = 5m | summarize avg(Temperature) by Bucket`

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Filtering first with `where` and `ago(1h)`, then grouping with `summarize` by device and `bin(Timestamp, 5m)`, gives one average per device per 5-minute bucket. A is wrong because `top` returns the five hottest rows, not averages per bucket. B is wrong because after `summarize` the `Timestamp` column no longer exists, so the late filter fails. D is wrong because it sets a constant, so every row lands in the same bucket and devices are not separated.
</details>

---

### Question 22 - Domain 2: Prepare data
**Scenario:** IoT events land in a KQL database in an Eventhouse. Data engineers want to read the same data from Spark notebooks and a Direct Lake model without building a copy pipeline. What should you do?

A. Turn on OneLake availability for the KQL database or its tables
B. Schedule a Dataflow Gen2 to copy the KQL tables into a lakehouse every hour
C. Export the tables to CSV in the lakehouse Files area each night
D. Rebuild the ingestion so events land in a warehouse instead

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** OneLake availability writes the Eventhouse data to OneLake in Delta format, so lakehouses can shortcut to it and Spark and Direct Lake read it with no extra copy. B is wrong because it adds a second copy, refresh cost and up to an hour of lag. C is wrong because CSV files lose the Delta table format that Direct Lake needs, and nightly export adds latency. D is wrong because it throws away the Eventhouse, which is the right engine for high-volume streaming data.
</details>

---

### Question 23 - Domain 2: Prepare data
**Scenario:** A new analyst is asked to build a margin report. Another team may already have a curated product lakehouse somewhere in the tenant. What is the best first step?

A. Ingest the product data again from the ERP system into a new lakehouse
B. Email every workspace admin and ask whether they own product data
C. Open the Real-Time hub and search the event streams for product tables
D. Search the OneLake catalog, filtering for endorsed items with product data

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** The OneLake catalog lists the Fabric items the analyst can access, with filters for item type and endorsement, so a certified product lakehouse can be found and reused. A is wrong because re-ingesting creates a second copy that drifts from the curated one. B is wrong because it is slow and misses items whose owners do not reply. C is wrong because the Real-Time hub is for discovering streaming data such as eventstreams, not batch lakehouse tables.
</details>

---

### Question 24 - Domain 2: Prepare data
**Scenario:** The silver layer stores Product, Subcategory and Category as three normalized tables. The gold layer feeds a star schema, and report authors keep asking which table holds the category name. What should you build?

A. Keep the three tables and add bidirectional relationships between them in the model
B. Join all three tables onto the fact table so every sales row carries product text
C. Create a separate Category fact table that holds one row for each category
D. Denormalize them into one Product dimension with subcategory and category columns

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Flattening the snowflake into a single Product dimension, for example with a view or a Spark join, gives one table per business entity, which is what a star schema needs. A is wrong because it keeps the snowflake and adds ambiguous filter paths. B is wrong because putting descriptive text on every fact row bloats the fact table and breaks the fact and dimension split. C is wrong because category is a descriptive attribute, not a measurable event, so it does not belong in a fact table.
</details>

---

### Question 25 - Domain 2: Prepare data
**Scenario:** A staging column `OrderDateText` holds dates as strings, and a few rows contain values such as "N/A". The load into a `date` column fails on those rows. The business wants bad values stored as NULL and the load to finish. Which T-SQL fits in a Fabric warehouse?

A. `TRY_CAST(OrderDateText AS date)` in the insert query
B. `CAST(OrderDateText AS date)` wrapped in a TRY...CATCH block
C. `CONVERT(varchar(10), OrderDateText)` before the insert
D. `ISNULL(OrderDateText, '1900-01-01')` in the insert query

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** `TRY_CAST` returns NULL for any value it cannot convert instead of raising an error, so the load completes and bad dates become NULL. B is wrong because the first bad row still makes the whole statement fail, and the CATCH block only handles the error after the insert stops. C is wrong because converting to a string does not produce a `date` value at all. D is wrong because "N/A" is not NULL, so `ISNULL` leaves it in place, and a fake 1900 date hides missing data.
</details>

---

### Question 26 - Domain 2: Prepare data
**Scenario:** A business analyst who does not write SQL needs to filter warehouse orders to one region, total them by month, and share the result as a reusable object others can query. What should the analyst use?

A. A Spark notebook that writes a new aggregated Delta table to a lakehouse
B. A KQL queryset connected to the warehouse SQL analytics endpoint
C. The Visual query editor in the warehouse, then save the query as a view
D. A paginated report with the filter and totals built into the report layout

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** The Visual query editor gives a Power Query style, no-code canvas for selecting, filtering and grouping warehouse data, and the result can be saved as a view that others query. A is wrong because notebooks need code, which the analyst does not write. B is wrong because KQL querysets target KQL databases, and the analyst would still need to write KQL. D is wrong because a report presents data but does not create a queryable object for others to reuse.
</details>

---

### Question 27 - Domain 3: Implement and manage semantic models
**Scenario:** Bank accounts can have several owners, and a customer can own several accounts. Balances live in an Account fact table. Report users must filter balances by customer. How should you model this?

A. Merge Customer and Account into one wide table with a row for each owner pair
B. Link Customer directly to the Account fact with a many-to-many relationship on name
C. Duplicate each balance once per owner so the fact table joins to Customer
D. Add a CustomerAccount bridge table between the Customer and Account dimensions

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** A bridge table holds one row per customer and account pair. Customer filters the bridge, the bridge filters Account through a relationship that filters in both directions, and Account filters the fact, so balances are not double-counted in the account rows. A is wrong because one wide table repeats data and makes totals hard to get right. B is wrong because matching on name is unreliable, and a direct many-to-many link skips the ownership mapping. C is wrong because duplicating balances inflates every total across customers.
</details>

---

### Question 28 - Domain 3: Implement and manage semantic models
**Scenario:** A model has 25 base measures. Finance wants year-to-date, prior year and year-over-year versions of each, and the measure list is already hard to maintain. What should you implement?

A. A calculation group with calculation items that use `SELECTEDMEASURE()`
B. 75 new measures, one for each combination of base measure and time view
C. A field parameter that lists the 25 base measures for the user to pick
D. Calculated columns on the fact table for each time view of each measure

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** A calculation group applies each time pattern to whatever measure is in context through `SELECTEDMEASURE()`, so three items replace 75 measures and new base measures get the patterns for free. B is wrong because it triples the measure count and the maintenance burden. C is wrong because a field parameter lets users swap which field a visual shows, but it does not create time calculations. D is wrong because calculated columns are computed at refresh row by row and cannot respond to the report filter context the way YTD needs.
</details>

---

### Question 29 - Domain 3: Implement and manage semantic models
**Scenario:** An import model holds five years of sales, and a full refresh now takes three hours. Only the last few days of data ever change. What should you configure?

A. Switch the fact table to DirectQuery so it never needs to be refreshed again
B. Incremental refresh using `RangeStart` and `RangeEnd`, refreshing only recent partitions
C. A more frequent scheduled refresh so each run has less data to process
D. Large semantic model storage format, which makes full refreshes run faster

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Incremental refresh partitions the table by date through the `RangeStart` and `RangeEnd` parameters, keeps the five-year archive, and refreshes only the recent window, so each run handles days instead of years. A is wrong because DirectQuery gives up import performance and pushes every visual query to the source. C is wrong because every scheduled refresh is still a full refresh of all five years. D is wrong because large model format lifts the size limit and enables XMLA partition work, but it does not by itself shrink what a refresh processes.
</details>

---

### Question 30 - Domain 3: Implement and manage semantic models
**Scenario:** A Direct Lake model must include several T-SQL views from a warehouse, and it must honor row-level security that is defined in the warehouse with T-SQL. Which Direct Lake option fits?

A. Direct Lake on OneLake, which reads the Delta files directly
B. Import mode, with the warehouse security policy copied into the model
C. Direct Lake on SQL analytics endpoint, which can fall back to DirectQuery
D. Direct Lake on OneLake, with fallback set to Automatic for the views

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Direct Lake on the SQL analytics endpoint goes through the SQL endpoint, so views and SQL-defined security are supported by falling back to DirectQuery for those queries. A is wrong because Direct Lake on OneLake reads Delta tables directly and bypasses the SQL engine, so views and T-SQL row-level security do not apply. B is wrong because import mode does not carry warehouse security, so you would maintain a second copy of the rules. D is wrong because Direct Lake on OneLake has no DirectQuery fallback, so the fallback setting cannot add views.
</details>

---

## Where to go deeper

- [DP-600 cert page](../../exams/azure/dp-600/) - notes, practice plan, strategy
- [DP-700 practice questions](./azure-fabric-data-engineer-dp-700.md) - the engineering sibling
- [PL-300 practice questions](./azure-power-bi-pl-300.md) - the Power BI depth this assumes
- [Databases topic index](../../topics/databases.md) - lakehouse in context
- **[📖 DP-600 study guide](https://learn.microsoft.com/en-us/credentials/certifications/resources/study-guides/dp-600)** - official skills outline
