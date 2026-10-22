---
last-updated: 2026-10-22
difficulty: intermediate
reading-time: 25 min
---

# SQL Interview Guide: Worked Problems

The SQL round shows up in almost every data engineer, analytics engineer, data analyst and DBA interview. This guide works through the problem types that come up again and again, explaining not just the answer but how to think your way to it, and the follow-up questions interviewers use to probe deeper.

All examples use standard SQL that runs on PostgreSQL, Snowflake, BigQuery, Databricks SQL and SQL Server with at most small changes (date functions are the usual difference). For the concepts underneath, see [SQL joins explained](../../learn/concepts/sql-joins-explained.md) and [Window functions explained](../../learn/concepts/window-functions-explained.md).

---

## How to approach any SQL question

Interviewers care about your process as much as your final query. A reliable routine:

1. **Restate the grain of the answer.** "So you want one row per customer, with their most recent order date?" Most wrong answers start with a misunderstood grain.
2. **Ask about edge cases.** Can there be ties? Nulls? Customers with no orders? Duplicate rows? Which time zone are dates in?
3. **Sketch the data.** Write three or four sample rows on the whiteboard, including an awkward one, and say what the output should be.
4. **Build in steps with CTEs.** One CTE per idea (filter, then rank, then pick) is easier to explain and debug than one nested query.
5. **Check your answer against the sample rows**, out loud, before you say you are done.
6. **Mention performance** briefly at the end: which index or partitioning would help, and whether the query scans the whole table.

Here are the sample tables used throughout:

```sql
customers(customer_id, name, signup_date, country)
orders(order_id, customer_id, order_date, amount, status)
logins(user_id, login_date)
employees(employee_id, name, department_id, salary, manager_id)
```

---

## 1. Joins: who has never ordered?

**Question:** List customers who have never placed an order.

The tempting answer is an inner join, but an inner join only returns customers who *do* have orders. You need the rows that fail to match. Two standard patterns:

```sql
-- Anti-join with LEFT JOIN
SELECT c.customer_id, c.name
FROM customers c
LEFT JOIN orders o ON o.customer_id = c.customer_id
WHERE o.order_id IS NULL;

-- Anti-join with NOT EXISTS
SELECT c.customer_id, c.name
FROM customers c
WHERE NOT EXISTS (
  SELECT 1 FROM orders o WHERE o.customer_id = c.customer_id
);
```

**Why it works:** a left join keeps every customer; where there is no matching order, every `o.` column is null. Filtering on a column that can never be null in a real match (the primary key `o.order_id`) isolates the non-matches.

**The trap interviewers look for:** `NOT IN` with a subquery.

```sql
SELECT * FROM customers
WHERE customer_id NOT IN (SELECT customer_id FROM orders);  -- dangerous
```

If any `orders.customer_id` is null, `NOT IN` returns **no rows at all**, because `x NOT IN (1, 2, NULL)` evaluates to unknown, never true. Say this out loud; it shows you know three-valued logic. `NOT EXISTS` does not have the problem.

**Follow-up:** "Customers who ordered in 2025 but not in 2026." Same pattern, with the date filter inside the join condition or subquery, not in the outer `WHERE`. Putting `o.order_date >= '2026-01-01'` in the outer `WHERE` of a left join turns it back into an inner join, a classic bug.

---

## 2. Aggregation with conditions

**Question:** For each country, show the number of customers, the number who have ordered, and the percentage who have ordered.

```sql
WITH customer_flags AS (
  SELECT c.customer_id,
         c.country,
         CASE WHEN EXISTS (
           SELECT 1 FROM orders o WHERE o.customer_id = c.customer_id
         ) THEN 1 ELSE 0 END AS has_ordered
  FROM customers c
)
SELECT country,
       COUNT(*)                                   AS customers,
       SUM(has_ordered)                           AS ordering_customers,
       ROUND(100.0 * SUM(has_ordered) / COUNT(*), 1) AS pct_ordered
FROM customer_flags
GROUP BY country
ORDER BY pct_ordered DESC;
```

**Points to make:**

- Build the flag at customer grain first. Joining customers to orders and then counting would count a customer once per order unless you use `COUNT(DISTINCT ...)`.
- `100.0 *` forces decimal division. In many engines `integer / integer` truncates, so `3 / 4` is `0`.
- Conditional aggregation (`SUM(CASE WHEN ... THEN 1 ELSE 0 END)`, or `COUNT_IF` / `FILTER (WHERE ...)` where supported) is the general tool for "count the ones that meet a condition" in one pass.

**`WHERE` versus `HAVING`:** `WHERE` filters rows before grouping; `HAVING` filters groups after. "Countries with more than 1,000 customers" needs `HAVING COUNT(*) > 1000`.

---

## 3. Top-N per group

**Question:** For each department, return the three highest-paid employees.

This is the single most common window function question. `GROUP BY` cannot do it, because you want rows, not aggregates.

```sql
WITH ranked AS (
  SELECT e.*,
         DENSE_RANK() OVER (
           PARTITION BY department_id
           ORDER BY salary DESC
         ) AS rnk
  FROM employees e
)
SELECT department_id, name, salary, rnk
FROM ranked
WHERE rnk <= 3
ORDER BY department_id, rnk;
```

**The real question is ties.** Ask which the interviewer wants:

| Function | Salaries 100, 90, 90, 80 | Behaviour |
|---|---|---|
| `ROW_NUMBER()` | 1, 2, 3, 4 | Exactly N rows per group; ties broken arbitrarily |
| `RANK()` | 1, 2, 2, 4 | Ties share a rank, then a gap |
| `DENSE_RANK()` | 1, 2, 2, 3 | Ties share a rank, no gap: "top 3 distinct salaries" |

With `ROW_NUMBER()`, add a tie-breaker column (`ORDER BY salary DESC, employee_id`) so results are deterministic between runs. Non-deterministic queries are a real production bug, not just an interview nicety.

**Why you cannot filter on `rnk` directly:** window functions are evaluated after `WHERE`, so `WHERE rnk <= 3` in the same query block fails. Use a CTE or subquery, or `QUALIFY` in Snowflake, BigQuery and Databricks:

```sql
SELECT * FROM employees
QUALIFY DENSE_RANK() OVER (PARTITION BY department_id ORDER BY salary DESC) <= 3;
```

**Follow-up: "the second-highest salary overall."** `DENSE_RANK() = 2` handles ties and the case where everyone earns the same (returns nothing, which you should mention). The older `MAX(salary) WHERE salary < (SELECT MAX(salary) ...)` also works for exactly the second.

---

## 4. Deduplication

**Question:** The `orders` table was loaded twice for some days, so there are duplicate rows. Return one row per `order_id`, keeping the most recently loaded one. Assume a `loaded_at` column.

```sql
WITH ranked AS (
  SELECT o.*,
         ROW_NUMBER() OVER (
           PARTITION BY order_id
           ORDER BY loaded_at DESC
         ) AS rn
  FROM orders o
)
SELECT * FROM ranked WHERE rn = 1;
```

**Points to make:**

- `ROW_NUMBER` (not `RANK`) because you want exactly one survivor even if two copies share a `loaded_at`. Add another tie-breaker if one exists.
- `SELECT DISTINCT` only removes rows that are identical in every column. If the duplicates differ in `loaded_at` or anything else, it removes nothing.
- To actually delete duplicates in place, most engines let you delete where the row's identifier is in the set with `rn > 1`. In a lakehouse you would more often rewrite the table or fix the pipeline so it is idempotent. See [Failing and late pipelines](../troubleshooting/failing-and-late-pipelines.md).

**Follow-up: "how would you find the duplicates first?"**

```sql
SELECT order_id, COUNT(*) AS copies
FROM orders
GROUP BY order_id
HAVING COUNT(*) > 1
ORDER BY copies DESC;
```

---

## 5. Running totals and moving averages

**Question:** Daily revenue, a running total for the month, and a 7-day moving average.

```sql
WITH daily AS (
  SELECT order_date, SUM(amount) AS revenue
  FROM orders
  WHERE status = 'completed'
  GROUP BY order_date
)
SELECT order_date,
       revenue,
       SUM(revenue) OVER (
         PARTITION BY DATE_TRUNC('month', order_date)
         ORDER BY order_date
         ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
       ) AS month_to_date,
       AVG(revenue) OVER (
         ORDER BY order_date
         ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
       ) AS revenue_7d_avg
FROM daily
ORDER BY order_date;
```

**Points to make:**

- **Aggregate to the day first**, then apply windows. Windows over raw orders would produce one running total per order.
- **`ROWS` versus `RANGE`.** With `ORDER BY` and no frame clause, the default frame is `RANGE BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW`, which treats rows with equal sort keys as peers and includes all of them. That gives surprising results when dates repeat. State the frame explicitly.
- **Missing days.** "6 preceding rows" is only "the last 7 days" if every day has a row. If some days have no orders, join to a date spine (a calendar table or `generate_series`) first, filling revenue with zero.

---

## 6. Comparing rows: LAG and LEAD

**Question:** For each customer, show each order and the number of days since their previous order.

```sql
SELECT customer_id,
       order_id,
       order_date,
       order_date - LAG(order_date) OVER (
         PARTITION BY customer_id ORDER BY order_date, order_id
       ) AS days_since_prev
FROM orders;
```

The first order per customer has a null gap. Date subtraction syntax varies (`DATEDIFF(day, a, b)` in SQL Server and Snowflake, `DATE_DIFF(b, a, DAY)` in BigQuery); say which dialect you are using.

**Follow-up: month-over-month growth.**

```sql
WITH monthly AS (
  SELECT DATE_TRUNC('month', order_date) AS month, SUM(amount) AS revenue
  FROM orders GROUP BY 1
)
SELECT month,
       revenue,
       LAG(revenue) OVER (ORDER BY month) AS prev_revenue,
       ROUND(100.0 * (revenue - LAG(revenue) OVER (ORDER BY month))
             / NULLIF(LAG(revenue) OVER (ORDER BY month), 0), 1) AS pct_change
FROM monthly;
```

`NULLIF(x, 0)` avoids division by zero by turning a zero denominator into null. Interviewers notice when you guard against it unprompted.

---

## 7. Gaps and islands

**Question:** Find each user's streaks of consecutive login days: start date, end date and length.

This is the classic "hard" SQL question. The trick is worth understanding rather than memorizing.

**Step 1: number each user's login days in order.**

| user_id | login_date | rn |
|---|---|---|
| 7 | 2026-03-01 | 1 |
| 7 | 2026-03-02 | 2 |
| 7 | 2026-03-03 | 3 |
| 7 | 2026-03-06 | 4 |
| 7 | 2026-03-07 | 5 |

**Step 2: subtract the row number (in days) from the date.** Inside a run of consecutive days, the date and the row number both go up by one each row, so their difference stays constant. When there is a gap, the date jumps but the row number does not, so the difference changes.

| login_date | rn | login_date - rn days |
|---|---|---|
| 2026-03-01 | 1 | 2026-02-28 |
| 2026-03-02 | 2 | 2026-02-28 |
| 2026-03-03 | 3 | 2026-02-28 |
| 2026-03-06 | 4 | 2026-03-02 |
| 2026-03-07 | 5 | 2026-03-02 |

That constant is an **island key**. Group by it.

```sql
WITH days AS (
  SELECT DISTINCT user_id, login_date      -- one row per user per day
  FROM logins
),
keyed AS (
  SELECT user_id,
         login_date,
         login_date - (ROW_NUMBER() OVER (
           PARTITION BY user_id ORDER BY login_date
         ) * INTERVAL '1 day') AS island
  FROM days
)
SELECT user_id,
       MIN(login_date) AS streak_start,
       MAX(login_date) AS streak_end,
       COUNT(*)        AS streak_days
FROM keyed
GROUP BY user_id, island
ORDER BY user_id, streak_start;
```

**Points to make:**

- The `DISTINCT` matters. Two logins on the same day would give two row numbers for one date and break the arithmetic.
- In engines without interval arithmetic, use `DATEADD(day, -rn, login_date)` (Snowflake, SQL Server) or `DATE_SUB(login_date, INTERVAL rn DAY)` (BigQuery).

**The alternative approach (flag and cumulative sum)** generalizes better to "gaps" defined by rules other than one day, such as sessions separated by 30 minutes of inactivity:

```sql
WITH flagged AS (
  SELECT user_id, login_date,
         CASE WHEN login_date - LAG(login_date) OVER (
                PARTITION BY user_id ORDER BY login_date) = 1
              THEN 0 ELSE 1 END AS new_island
  FROM (SELECT DISTINCT user_id, login_date FROM logins) d
),
numbered AS (
  SELECT *, SUM(new_island) OVER (
           PARTITION BY user_id ORDER BY login_date
           ROWS UNBOUNDED PRECEDING) AS island_id
  FROM flagged
)
SELECT user_id, island_id, MIN(login_date), MAX(login_date), COUNT(*)
FROM numbered GROUP BY user_id, island_id;
```

Mark the first row of each island with 1, then a running sum gives every row in the same island the same number. Change the condition to `event_time - LAG(event_time) > INTERVAL '30 minutes'` and you have sessionization, another favourite.

**Follow-up:** "Longest streak per user" is an aggregate over this result. "Users with a current streak of at least 7 days" filters on `streak_end = CURRENT_DATE`.

---

## 8. Self-joins and hierarchies

**Question:** List employees who earn more than their manager.

```sql
SELECT e.name AS employee, e.salary, m.name AS manager, m.salary AS manager_salary
FROM employees e
JOIN employees m ON m.employee_id = e.manager_id
WHERE e.salary > m.salary;
```

The same table appears twice with different aliases and different roles. Using an inner join correctly excludes the CEO, who has no manager.

**Follow-up: the whole reporting chain.** Use a recursive CTE:

```sql
WITH RECURSIVE chain AS (
  SELECT employee_id, name, manager_id, 0 AS depth
  FROM employees
  WHERE manager_id IS NULL                       -- anchor: the top
  UNION ALL
  SELECT e.employee_id, e.name, e.manager_id, c.depth + 1
  FROM employees e
  JOIN chain c ON e.manager_id = c.employee_id   -- recursive step
)
SELECT * FROM chain ORDER BY depth, name;
```

Mention cycle protection (bad data where A manages B and B manages A loops forever); many engines support a `CYCLE` clause or you can cap depth.

---

## 9. Retention cohorts

**Question:** For each signup month, what percentage of customers placed an order in each of the following months?

```sql
WITH cohorts AS (
  SELECT customer_id, DATE_TRUNC('month', signup_date) AS cohort_month
  FROM customers
),
activity AS (
  SELECT DISTINCT customer_id, DATE_TRUNC('month', order_date) AS active_month
  FROM orders
),
joined AS (
  SELECT c.cohort_month,
         (EXTRACT(YEAR FROM a.active_month) - EXTRACT(YEAR FROM c.cohort_month)) * 12
         + (EXTRACT(MONTH FROM a.active_month) - EXTRACT(MONTH FROM c.cohort_month))
           AS month_number,
         c.customer_id
  FROM cohorts c
  JOIN activity a ON a.customer_id = c.customer_id
                 AND a.active_month >= c.cohort_month
),
sizes AS (
  SELECT cohort_month, COUNT(*) AS cohort_size FROM cohorts GROUP BY 1
)
SELECT j.cohort_month, j.month_number,
       COUNT(DISTINCT j.customer_id) AS active_customers,
       ROUND(100.0 * COUNT(DISTINCT j.customer_id) / s.cohort_size, 1) AS retention_pct
FROM joined j
JOIN sizes s USING (cohort_month)
GROUP BY j.cohort_month, j.month_number, s.cohort_size
ORDER BY 1, 2;
```

This combines most of the earlier ideas: building at the right grain, distinct counts that cannot be summed, and a denominator computed separately so customers with no activity still count in the cohort size.

---

## 10. Performance follow-ups

After a correct answer, many interviewers ask "this table has 5 billion rows; what changes?" Good things to say:

- **Filter early and on partition or cluster keys** so the engine prunes files or partitions instead of scanning everything.
- **Index or cluster on join and filter columns** in a row store (see [Indexes explained](../../learn/concepts/indexes-explained.md)); in a columnar warehouse, cluster or sort on them.
- **Select only needed columns.** Columnar engines read only the columns you name; `SELECT *` defeats that.
- **Window functions sort.** A `PARTITION BY` on a skewed key (one huge customer) puts most of the work on one worker. See [Spark job performance](../troubleshooting/spark-job-performance.md).
- **Read the plan.** Say you would run `EXPLAIN` (or check the query profile) to confirm. See [Slow SQL query diagnosis](../troubleshooting/slow-sql-query-diagnosis.md).

---

## Practice routine

Pick ten problems from this page and write each from a blank editor, against a real database (a local PostgreSQL or DuckDB is enough), with your own sample data including the awkward rows. Then change one requirement (ties, nulls, a different gap rule) and adjust. Interviewers almost always add a twist, and being able to adapt a pattern is what they are testing.

- **[📖 PostgreSQL window functions tutorial](https://www.postgresql.org/docs/current/tutorial-window.html)** - the official introduction to `OVER`, partitions and frames
- **[📖 PostgreSQL WITH queries](https://www.postgresql.org/docs/current/queries-with.html)** - CTEs and recursive queries, including cycle detection
- **[📖 DuckDB documentation](https://duckdb.org/docs/)** - a zero-setup local engine for practising

---

## Related

- [Data engineer interview prep](./data-engineer-interview.md) - the broader interview, including tool and concept questions
- [Data modeling and pipeline design interviews](./data-modeling-system-design-interview.md) - the design round
- [Analytics engineer interview guide](./analytics-engineer-interview.md) and [DBA interview guide](./dba-interview.md) - the role-specific loops
- [SQL joins explained](../../learn/concepts/sql-joins-explained.md)
- [Window functions explained](../../learn/concepts/window-functions-explained.md)
