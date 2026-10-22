# Microsoft Power BI Data Analyst (PL-300) - Practice Questions

30 questions for PL-300 prep. Most modeling and DAX questions assume a star schema and turn on understanding filter context.

> **Cert page:** [exams/azure/pl-300/](../../exams/azure/pl-300/)

---

### Question 1
**Scenario:** A 4-billion-row fact table lives in Azure Synapse. Current-day data must be accurate to the minute; five years of history is queried far more often and performance is a priority.

A. Full DirectQuery on the whole table, so that every visual always shows the latest minute of data from the source
B. Import with incremental refresh only, set to refresh the most recent partition every minute through the gateway
C. A composite model: history in Import, today in DirectQuery, shared dimensions in Dual
D. Full Import with hourly refresh through the gateway, plus a larger capacity so that the refresh finishes quickly

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** A composite model satisfies both requirements at once. Dual mode on the shared dimensions is what prevents an unnecessary DirectQuery round trip for the imported queries. Full DirectQuery meets currency and fails performance. Import with any refresh schedule cannot be minute-accurate.
</details>

---

### Question 2
**Scenario:** A refresh over a 200-million-row SQL table takes four hours. An index column is added as the second Power Query step, with filters and group-by afterwards.

A. Schedule the refresh overnight so the four hours fall outside business hours
B. Switch the table to DirectQuery so that nothing needs to be refreshed
C. Increase gateway memory so the mashup engine can hold more of the 200 million rows in RAM while it processes them
D. Move or remove the index step so filters and grouping fold to the source, and check with View Native Query

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Adding an index column breaks query folding, so every subsequent step processes locally on the full dataset. Reordering so folding-breaking steps come last restores source-side filtering. More memory treats the symptom. DirectQuery changes the interaction model to solve a refresh problem. Scheduling overnight leaves the four hours in place.
</details>

---

### Question 3
**Scenario:** `Profit Margin` is created as a calculated column with `(Revenue - Cost) / Revenue`, then placed in a card with average aggregation. Finance reports the value is wrong at every level except individual rows.

A. Change the visual aggregation to sum
B. Use `AVERAGEX` over the fact table to average the row-level margin
C. Replace it with a measure: `DIVIDE( SUM(Revenue) - SUM(Cost), SUM(Revenue) )`
D. Add a second calculated column for the weighted average

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Averaging a row-level ratio gives the average of ratios, not the ratio of totals. Ratios must be measures so aggregation happens before division. `DIVIDE` also handles division by zero. Summing percentages is worse. `AVERAGEX` computes an average of ratios, which is a different, usually unwanted, number here.
</details>

---

### Question 4
**Scenario:** 200 regional managers must each see only their own region. An employee table maps each manager's email to their region.

A. Object-level security on the region and fact tables, with a role per manager that hides the other regions' rows from that manager
B. A slicer defaulted to the user's region and saved in the report
C. One dynamic RLS role filtering `[Email] = USERPRINCIPALNAME()`, with the security table related through to the facts
D. 200 static row-level security roles, one for each manager, each with a hard-coded filter on that manager's own region and maintained by hand

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Dynamic RLS with `USERPRINCIPALNAME()` scales to any number of users with one role. Static roles are unmanageable at 200. A slicer is not security, because users can change it. Object-level security hides tables and columns, not rows.
</details>

---

### Question 5
**Scenario:** A `SAMEPERIODLASTYEAR` measure returns blank for every row. The model has a date column on the fact table and no separate date table.

A. Rely on Auto Date/Time, which Microsoft recommends for models with several date columns
B. Create a contiguous date table, mark it as a date table, relate it to the fact, and use its date column
C. Wrap the measure in `CALCULATE` with additional filters on the fact date column, to force the prior-year range for every row in the visual
D. Change the fact date column's data type from Date/Time to Text

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Time intelligence functions require a marked date table with a complete, gap-free date range. Blank results almost always mean the date table is missing, unmarked, or has gaps. Auto Date/Time creates hidden per-column tables that bloat the model and do not provide the marked-date-table behavior these functions expect.
</details>

---

### Question 6
**Scenario:** A finance report must reach 3,000 employees across four departments, each seeing only their department's pages, while five authors keep editing without disrupting consumers.

A. Create four separate workspaces, one per department
B. Share the report directly with all 3,000 users
C. Publish an app with four audiences, keeping authors in the workspace
D. Give all consumers Viewer access to the workspace and use RLS roles so that each department only sees its pages

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Apps with audiences are the governed distribution mechanism, and publishing separates what consumers see from work in progress. Direct sharing is unmanageable at that scale and exposes edits immediately. Workspace Viewer access shows unpublished changes and offers no audience segmentation. Four workspaces duplicate the model and its maintenance.
</details>

---

### Question 7
**Scenario:** Which workspace role can create and edit content and schedule refresh, but cannot publish an app?

A. Contributor
B. Member, who can edit content and schedule refresh but is restricted from publishing the workspace app
C. Viewer, who can edit content and schedule refresh in the workspace but is not allowed to publish an app
D. Admin, who can edit content and schedule refresh but needs a Member to publish the workspace app for them

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Contributor is exactly this boundary and it is directly testable. Admin and Member can both publish apps. Viewer can only consume, and is the only role to which row-level security applies.
</details>

---

### Question 8
**Scenario:** An author tests row-level security in the workspace and sees all rows despite being assigned to a role. Why?

A. The role's DAX expression is wrong, for example a filter on the wrong column, so it returns every row to the tester
B. Workspace Contributors or higher bypass RLS; test with View As or a real Viewer
C. RLS requires a Premium capacity, so roles assigned on Pro workspaces are ignored when the report is opened
D. The relationship direction is wrong, so the security table's filter never reaches the fact table in the model

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** RLS applies to the Viewer role. Anyone with Contributor or above sees all rows in the workspace regardless of role membership, so validation must use View As in Desktop and then a genuine Viewer or app consumer in the service.
</details>

---

### Question 9
**Scenario:** A model built from one 60-column flat spreadsheet is 800 MB, slow, and filters inconsistently. What is the correct fix?

A. Split into fact and dimension tables, add a date table, and drop unused high-cardinality columns
B. Add calculated columns for the common measures so results are precomputed
C. Switch to DirectQuery so that the 800 MB file is no longer loaded into the model and the filters are evaluated at the source
D. Reduce the number of visuals per page and turn off interactions between them, so that fewer queries run on each click, hoping the page then feels faster

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Star schema resolves the ambiguous filtering and the repetition driving size. The largest size win usually comes from removing high-cardinality columns such as transaction IDs and timestamps. Calculated columns increase size. DirectQuery moves the problem to the source. Fewer visuals does not fix a model problem.
</details>

---

### Question 10
**Scenario:** Which relationship configuration should generally be avoided because it introduces ambiguity?

A. Single-direction one-to-many from dimension to fact, with the dimension on the one side and filters flowing down to the fact
B. Bidirectional cross-filtering between two tables
C. An inactive relationship activated with `USERELATIONSHIP` inside the measure that needs the alternative date
D. A one-to-many relationship from a date table marked as a date table to the fact table's date column

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Bidirectional filtering creates ambiguous filter paths and can produce wrong results elsewhere in the model. The usual alternative is single direction plus `CROSSFILTER` inside specific measures where both directions are genuinely needed.
</details>

---

### Question 11
**Scenario:** Incremental refresh cannot be configured on a table. What is the most likely cause?

A. The report is published to a workspace that has no deployment pipeline assigned
B. The parameters are not named exactly `RangeStart` and `RangeEnd`, or folding does not reach the source
C. The dataset is not on a Premium capacity, because incremental refresh is only offered to Premium workspaces
D. There is no date table in the model, so the service cannot work out which partitions to create for the refresh

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Incremental refresh requires two date/time parameters named exactly `RangeStart` and `RangeEnd`, a filter using them, and query folding so the source can partition. A model date table is unrelated to the parameter requirement.
</details>

---

### Question 12
**Scenario:** One report page renders in 25 seconds while others are instant. The model is Import mode and reasonably sized. What is the first step?

A. Switch the model to DirectQuery so the slow page queries the source
B. Increase the capacity size to the next SKU
C. Split the page into three pages with fewer visuals each
D. Run Performance Analyzer to see whether the time is in the DAX query, visual display, or other

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** The exam expects the diagnostic step before the fix. Performance Analyzer tells you whether the cost is in the query, the visual, or elsewhere, which determines whether you rewrite a measure, change the model, or reduce visual cardinality. Splitting pages may help, but only once you know why.
</details>

---

### Question 13
**Scenario:** Which storage mode allows a dimension table to serve both an imported fact table and a DirectQuery fact table without forcing a source round trip for the imported queries?

A. Dual, which behaves as Import or DirectQuery depending on the query
B. Direct Lake
C. Import, which holds the dimension in memory but forces a source query when it serves the DirectQuery fact table
D. DirectQuery, which always queries the source and so serves the two fact tables with no imported copy of the dimension

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Dual lets the engine decide per query, acting as Import when serving imported facts and as DirectQuery when serving DirectQuery facts. That is precisely the problem Dual exists to solve, and it is why composite models depend on it.
</details>

---

### Question 14
**Scenario:** What does a deployment pipeline rule do?

A. Restricts who can deploy between stages, using workspace roles
B. Schedules deployments so each stage is promoted automatically
C. Validates the semantic model and blocks promotion when any DAX measure fails
D. Swaps data source parameters and connection details so each stage points at the right data

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Rules are the mechanism ensuring production content points at production data after promotion. Access control between stages is governed separately by workspace permissions, and rules do not schedule or validate.
</details>

---

### Question 15
**Scenario:** A report must be accessible to keyboard and screen reader users. Which combination is required?

A. A mobile layout, which reorders visuals for phone screens so that the report is readable for every kind of user, whatever device they open it on
B. Alt text on visuals, a logical tab order from the Selection pane, enough contrast, and not relying on color alone
C. Alt text on visuals and a high-contrast theme
D. Exporting the report to PDF each week for assistive technology users

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Accessibility on this exam consistently covers all four: alt text, tab order, contrast, and not conveying meaning through color alone. A mobile layout addresses screen size, not accessibility. PDF export is a different output, not an accessibility control.
</details>

---

### Question 16 - Domain 1: Prepare the data
**Scenario:** An analyst merges an internal SQL Server customer table with a public geocoding web API in Power Query. Desktop asks for privacy levels, and a colleague suggests choosing "Ignore privacy levels" to make refresh faster.

A. Ignore privacy levels, because both sources are reached with the analyst's own credentials anyway
B. Set the SQL source to Organizational and the web API to Public, and leave privacy levels enforced
C. Set both sources to Public so the engine can fold the merge into a single query against the API
D. Set both sources to Private so that each source is isolated and the merge always folds to SQL

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Privacy levels stop the mashup engine from sending data from one source to another. With SQL as Organizational and the API as Public, customer values are never pushed into the API's query strings. Ignoring privacy levels (A) removes exactly that protection and can leak customer data to a third party. Marking internal data Public (C) says it is safe to share with anyone, which it is not. Private on both (D) blocks combining at all and does not make anything fold.
</details>

---

### Question 17 - Domain 1: Prepare the data
**Scenario:** A budget file has columns Product, Region, then one column per month (Jan, Feb, and so on). A new month column appears every month. The model needs one row per product, region and month.

A. Select the twelve month columns and use Unpivot only selected columns to turn them into rows
B. Transpose the table, promote the first row to headers, then transpose it back again afterwards
C. Select the month columns and use Pivot column with Sum, so each month becomes a value per row
D. Select Product and Region, then use Unpivot other columns so new month columns are picked up

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Unpivot other columns names only the columns to keep, so any month column added later is unpivoted automatically. Unpivot only selected columns (A) hard-codes the month names, and the next new month is silently left out. Transposing (B) swaps rows and columns but never produces the long attribute and value shape. Pivot (C) does the opposite of what is needed, turning rows into columns.
</details>

---

### Question 18 - Domain 1: Prepare the data
**Scenario:** A staging query cleans a customer extract. Two downstream queries need it: one aggregates customers by region, the other keeps the detail list. Any future fix to the cleaning steps must flow to both.

A. Create both downstream queries as references to the staging query, and disable load on the staging query
B. Create both downstream queries as duplicates of the staging query, and disable load on the staging query
C. Copy the cleaning steps into each downstream query, then delete the staging query from the model
D. Load the staging query into the model and build the two outputs as calculated tables in DAX

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** A reference starts from the output of the original query, so a fix to the staging steps reaches both outputs. Disabling load keeps the staging table out of the model. Duplicate (B) copies the steps at that moment, so later fixes must be made three times. Copying steps by hand (C) has the same drift problem. Loading staging and using calculated tables (D) stores the data twice and moves shaping work into the model.
</details>

---

### Question 19 - Domain 1: Prepare the data
**Scenario:** Column quality in Power Query shows 0% errors and 0% empty for an OrderDate column. After loading, a visual shows hundreds of blank dates.

A. The data type must be wrong, so change OrderDate from Date to Date/Time and load the table again
B. The visual is filtering blanks in, so add a visual-level filter that excludes blank OrderDate values
C. Profiling ran on the top 1,000 rows only, so switch to profiling based on the entire data set
D. The relationship to the date table is inactive, so activate it before looking at the column again

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** By default Power Query profiles only the first 1,000 rows, so problems further down are invisible. Switching the status bar to the entire data set shows the true error and empty counts. Changing the type (A) guesses at a fix before the data has been evaluated. Hiding blanks in one visual (B) masks a data quality problem. Relationship state (D) does not change what Power Query reports about a column.
</details>

---

### Question 20 - Domain 2: Model the data
**Scenario:** A daily inventory snapshot table holds the on-hand quantity per warehouse per day. A SUM measure is right for a single day but wildly too high at month and year level.

A. Change the measure to AVERAGE so each month shows the typical daily on-hand quantity instead
B. Set the column's Summarize by property to None so that it is never added up across dates
C. Use CALCULATE over SUM, filtered with LASTNONBLANK on the date column, to return the closing balance
D. Wrap the SUM in TOTALYTD so that each month accumulates correctly from the start of the year

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Stock balances are semi-additive: they add across warehouses but not across time. Taking the last date that has data gives the closing balance for any period, and LASTNONBLANK avoids a blank when the final calendar day has no snapshot yet. An average (A) answers a different question. Summarize by (B) only affects implicit aggregation and does nothing for a measure. TOTALYTD (D) adds up balances, which makes the error worse.
</details>

---

### Question 21 - Domain 2: Model the data
**Scenario:** A model has 25 base measures. Finance wants YTD, prior year and year-over-year % for every one of them, and expects more base measures next quarter.

A. Create a calculation group with three calculation items that use SELECTEDMEASURE()
B. Use quick measures to generate the three time variants for each of the base measures
C. Create a field parameter that lets users switch between the base measures on visuals
D. Build a calculated table that stores each base measure's variants as precomputed columns

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** A calculation group applies each time logic to whatever measure is in context, so three items cover all 25 measures and any added later. Quick measures (B) still produce 75 separate measures to maintain. A field parameter (C) switches which measure appears but does not compute time variants. A calculated table (D) is static, inflates the model and loses filter context.
</details>

---

### Question 22 - Domain 2: Model the data
**Scenario:** A matrix shows Category, then Product, on rows. Each product needs its share of its own category's sales, and the Region slicer must still apply.

A. `DIVIDE( [Sales], CALCULATE( [Sales], ALL( 'Product' ) ) )`
B. `DIVIDE( [Sales], CALCULATE( [Sales], REMOVEFILTERS( Sales ) ) )`
C. `DIVIDE( [Sales], SUMX( 'Product', [Sales] ) )`
D. `DIVIDE( [Sales], CALCULATE( [Sales], REMOVEFILTERS( 'Product'[Product] ) ) )`

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Removing the filter on the product column alone keeps the category filter from the row above and the region filter from the slicer, so the denominator is the category total. ALL on the whole Product table (A) also removes the category filter, giving share of all products. REMOVEFILTERS on the fact table (B) can wipe the slicer's effect and other filters. SUMX over the visible products (C) returns the same product's sales at product level, so every row shows 100%.
</details>

---

### Question 23 - Domain 2: Model the data
**Scenario:** A clickstream fact table has 1.2 billion rows at one-second grain. Every report slices by day, page and country only, and refresh is hitting capacity limits.

A. Split the timestamp into separate date and time columns and keep every row at the same grain
B. Group by date, page and country in Power Query, and load a count of events at that daily grain
C. Hide the timestamp column in report view so report authors stop building visuals on top of it
D. Switch the fact table to DirectQuery so that the 1.2 billion rows no longer need a refresh

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** When no report needs detail below the day, reducing granularity to that level removes most rows and shrinks both the model and the refresh. Splitting date and time (A) lowers column cardinality, which helps, but still loads 1.2 billion rows. Hiding a column (C) does not remove it from memory. DirectQuery (D) moves the cost to every visual query at the source instead of solving it.
</details>

---

### Question 24 - Domain 3: Visualize and analyze the data
**Scenario:** A summary page lists customers. Users should be able to right-click any customer and land on a detail page that shows only that customer, with the summary page's other filters still applied.

A. Add Customer to the Drillthrough well on the detail page and turn on Keep all filters
B. Create one bookmark per customer and add a button that applies the matching bookmark
C. Add a page navigation button on the summary page that opens the detail page directly
D. Build a report page tooltip for the detail page and assign it to the customer table

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Drillthrough passes the selected customer as a filter to the target page, and Keep all filters carries the other filters across. Power BI also adds a back button. One bookmark per customer (B) does not scale and breaks when customers change. A plain navigation button (C) opens the page without the customer filter. A tooltip page (D) shows on hover and is not a full detail page.
</details>

---

### Question 25 - Domain 3: Visualize and analyze the data
**Scenario:** Operations needs monthly customer statements that run to 40 printed pages, with every row shown, headers repeated on each page and an exact layout when exported to PDF.

A. A standard Power BI report page with a table visual, exported to PDF from the service each month
B. A dashboard with the table pinned as a tile and a subscription that emails it to the team
C. A paginated report built in Power BI Report Builder, with the semantic model as its source
D. An Analyze in Excel workbook connected to the semantic model and printed from Excel monthly

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Paginated reports are built for pixel-exact, print-ready output that expands across as many pages as the data needs. A report page export (A) captures only what fits on the canvas, so long tables are cut off. A dashboard tile (B) is a snapshot, not a multi-page document. Excel (D) can print but gives no controlled, repeatable layout and needs manual work each month.
</details>

---

### Question 26 - Domain 3: Visualize and analyze the data
**Scenario:** A report has a Region slicer on five of its eight pages. Users complain that choosing a region on one page does not carry to the others, and two of the eight pages should ignore region.

A. Capture a bookmark of the slicer state on each page and add buttons that apply them
B. Use the Sync slicers pane to sync the Region slicer across the five pages that need it
C. Edit interactions on each page so that the Region slicer filters every visual there
D. Set up drillthrough on Region so the selection is passed to each page that is opened

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Sync slicers keeps one selection across the chosen pages, and you pick per page whether it syncs and whether it is visible, so the two other pages can be left out. Bookmarks (A) store fixed states, not the user's live choice. Edit interactions (C) controls visuals within one page only. Drillthrough (D) needs a right-click on a data point and is not a cross-page slicer.
</details>

---

### Question 27 - Domain 3: Visualize and analyze the data
**Scenario:** The retention lead asks which customer attributes, such as plan type, tenure and support tickets, most increase the likelihood that a customer churns. She wants them ranked.

A. A decomposition tree on churn rate, expanded by whichever attribute she wants to look at
B. A line chart of churn rate over time, with the forecasting option turned on in Analytics
C. A Q&A visual where she types each attribute name and compares the answers by hand
D. A key influencers visual with Churned as the field to analyze and the attributes as factors

<details>
<summary>Answer</summary>

**Correct: D**

**Why:** Key influencers is the AI visual that ranks factors by how much they change the likelihood of an outcome. A decomposition tree (A) breaks a measure down by dimensions in turn but does not rank drivers of a yes or no outcome. Forecasting (B) projects a trend and says nothing about causes. Q&A (C) answers one question at a time and leaves the ranking to her.
</details>

---

### Question 28 - Domain 4: Manage and secure Power BI
**Scenario:** A semantic model imports from Azure SQL Database, an on-premises SQL Server, and a SharePoint Online list. It must refresh on a schedule in the service and be maintained by a team of three.

A. Install an on-premises data gateway in standard mode and route all three sources through it
B. Install a gateway in personal mode on the analyst's laptop, since it covers all import sources
C. Install a standard mode gateway and use it only for the on-premises SQL Server connection
D. No gateway is needed, because the service can reach every source once the model is published

<details>
<summary>Answer</summary>

**Correct: C**

**Why:** Only the on-premises source needs a gateway. Azure SQL Database and SharePoint Online are cloud sources the service reaches directly. Standard mode supports shared administration by a team. Routing cloud sources through the gateway (A) is unnecessary and adds a dependency. Personal mode (B) is tied to one user and one machine, which does not suit a team. With no gateway (D), the on-premises source cannot refresh.
</details>

---

### Question 29 - Domain 4: Manage and secure Power BI
**Scenario:** An HR report holds salary data. Compliance requires that it is visibly classified as confidential and that the protection travels with data exported to Excel or PDF.

A. Apply a Microsoft Purview sensitivity label such as Confidential to the semantic model and report
B. Add row-level security so each user only sees salary rows for their own department and grade
C. Ask the tenant admin to turn off export to Excel and PDF for every user in the organization
D. Certify the semantic model so users know it is the trusted and approved source of salary data

<details>
<summary>Answer</summary>

**Correct: A**

**Why:** Sensitivity labels show the classification in Power BI and are applied to supported exports such as Excel, PowerPoint and PDF, carrying any encryption with them. Labels also flow to downstream items. Row-level security (B) limits which rows people see but does not classify or protect exported files. A tenant-wide export ban (C) blocks legitimate work everywhere to fix one report. Certification (D) signals trust, not confidentiality.
</details>

---

### Question 30 - Domain 4: Manage and secure Power BI
**Scenario:** Finance wants its sales semantic model flagged across the organization as the official, reviewed source, with a stronger signal than the owner can give it on their own.

A. Promote the semantic model, which any owner or contributor can do from its settings page
B. Certify the semantic model through a reviewer that the Fabric admin has authorized to do so
C. Feature the semantic model on the Power BI home page so that everyone sees it when they log in
D. Move the semantic model to a Premium capacity workspace so that it is marked as production

<details>
<summary>Answer</summary>

**Correct: B**

**Why:** Certification is the stronger endorsement. Only reviewers the admin authorizes can grant it, which is what makes it a governed signal. Promotion (A) is self-service by people with write access, so it carries less weight. Featuring on home (C) improves discovery but is not an endorsement. Capacity type (D) changes performance and features, not trust status.
</details>

---

## Scoring guide

- **26-30 correct (85%+):** Ready. Spend remaining time predicting DAX results before running them.
- **20-25 correct (65-84%):** Review filter context and the star schema material, which underpin most wrong answers.
- **Below 20:** Build the models listed in the [practice plan](../../exams/azure/pl-300/practice-plan.md). PL-300 rewards hands-on work more than any other exam in the Microsoft data track.
