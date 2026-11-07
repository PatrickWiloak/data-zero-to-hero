---
last-updated: 2026-11-07
applies-to: Databricks Data Analyst Associate exam guide (August 2026 edition, read 2026-11-07)
difficulty: intermediate
reading-time: 30 min
---

# Section 6: Working with Dashboards and Visualizations in Databricks

Weighted **16%**, about seven questions. Seven objectives: build AI/BI dashboards; make visualizations in notebooks and the SQL editor; work with parameters; configure sharing and embedding; schedule refresh; configure alerts; and choose the right visualization type.

## Table of Contents

1. [Building AI/BI dashboards](#building-aibi-dashboards)
2. [Visualizations in notebooks and the SQL editor](#visualizations-in-notebooks-and-the-sql-editor)
3. [Parameters](#parameters)
4. [Sharing, embedding and refresh](#sharing-embedding-and-refresh)
5. [Alerts](#alerts)
6. [Choosing a visualization](#choosing-a-visualization)
7. [How the exam tests this section](#how-the-exam-tests-this-section)
8. [Key terms](#key-terms)

---

## Building AI/BI dashboards

An **AI/BI dashboard** is built from **datasets** (a table, a view or a custom query), **pages** (tabs for multi-page layouts) and **widgets** (visualizations, text and images). Because one dashboard can have many datasets, a single page can mix data sources. Filters work at global, page and widget level with cross-filtering and drill-through, and you can add custom calculations without changing the dataset. AI assistance helps author visualizations.

**[📖 AI/BI dashboards](https://docs.databricks.com/aws/en/dashboards/)** - datasets, filters, sharing, embedding and refresh

## Visualizations in notebooks and the SQL editor

You do not need a dashboard to see a chart. In a notebook, results from a SQL or Python cell can be turned into a chart with the **+** button on the result. In the SQL editor, add a visualization to a query result the same way. These are exploration tools; when the audience is a team, publish a dashboard.

## Parameters

A **parameter** substitutes a value into a dataset query at run time, using the `:keyword` marker. A date-range parameter uses `.min` and `.max`:

```sql
-- Syntax per the Databricks docs; not run (needs a dashboard dataset).
SELECT order_date, SUM(amount) AS revenue
FROM main.gold.orders
WHERE order_date BETWEEN :date_param.min AND :date_param.max
GROUP BY order_date;
```

To define one: type the marker (or **Add parameter**), use the gear icon to set the keyword, display name and type (String, Date, Date and Time, Numeric, Date Range, or multiple selection), set a default and run the query; the default is saved. Then connect it to a **filter widget** on the canvas so viewers can change it. Parameters not exposed on the dashboard use their defaults. Parameters change the query itself; **field filters** are more efficient for large data and cascade across filters, so prefer filters unless you need to alter the query logic.

**[📖 Dashboard parameters](https://docs.databricks.com/aws/en/dashboards/parameters)** - syntax, types, widgets, filters versus parameters

## Sharing, embedding and refresh

- **Publish** the dashboard with either shared data permissions (viewers see data through the publisher's credentials) or individual data permissions (each viewer's own access applies).
- **Share** with workspace users and groups, or at the account level; published dashboards can be shared with anyone in the Databricks account, even without workspace access.
- **Embed** a published dashboard in an external site with an iframe.
- **Refresh and delivery:** set an automated refresh schedule, and send email or Slack subscriptions.

**[📖 AI/BI dashboards](https://docs.databricks.com/aws/en/dashboards/)** - publishing, sharing, embedding, schedules and subscriptions

## Alerts

A **SQL alert** runs a query on a schedule and notifies you when a condition is met. Each evaluation ends as `OK`, `TRIGGERED` or `ERROR`. When you create one you choose a column (the first value or an aggregation across rows such as SUM or AVERAGE), an operator and a threshold, and **Test condition** shows whether it would trigger now. Set the schedule (Quartz cron syntax is available), then choose the users or notification destinations; you can repeat notifications until the status returns to OK, and optionally notify on OK. Workspace admins create the notification destinations, such as webhooks. An alert can also run as a task in Lakeflow Jobs so downstream tasks branch on the result.

**[📖 SQL alerts](https://docs.databricks.com/aws/en/sql/user/alerts/)** - states, query ownership and webhook payloads
**[📖 Create an alert](https://docs.databricks.com/aws/en/sql/user/alerts/create)** - condition, schedule and notifications

Use alerts for "tell me when" (average temperature above a limit in the last 15 minutes); use dashboards for "let me look". Refreshing a dashboard by hand, reading job logs or resizing a cluster are not alerting.

## Choosing a visualization

Pick the chart from the question, not from habit:

| Question | Chart |
|---|---|
| How did it change over time? | Line |
| Which category is biggest? | Bar |
| What share of the total? | Pie or stacked bar (few categories only) |
| How are two measures related? | Scatter |
| Exact values or lookups | Table |
| One headline number | Counter |
| Intensity across two categories | Heatmap |

## How the exam tests this section

- **Parameter mechanics:** the date widget's min and max values flow into the query's `WHERE`; they are not just axis settings.
- **Sharing:** shared versus individual data permissions; embedding through iframes; account-level sharing.
- **Alert versus dashboard versus job:** a threshold with immediate notification is an alert.
- **Chart choice:** trends are lines, comparisons are bars.

## Key terms

- **AI/BI dashboard** - a Databricks dashboard built from datasets, pages and widgets, with AI-assisted authoring.
- **Dataset** - a table, view or custom query that supplies data to a dashboard.
- **Widget** - a dashboard element such as a visualization, filter, text box or image.
- **Parameter** - a named placeholder, written as :keyword, that substitutes a value into a query at run time.
- **Field filter** - a dashboard filter on a dataset column that cascades across filters and performs well on large data.
- **Shared data permissions** - a publishing mode where viewers see data using the publisher's credentials.
- **SQL alert** - a scheduled query with a condition that notifies users or destinations when the condition is met.
- **Notification destination** - a place alerts are sent, such as email or a webhook, created by workspace admins.
