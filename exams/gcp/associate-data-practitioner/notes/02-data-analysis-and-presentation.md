---
last-updated: 2026-11-07
applies-to: Google Cloud Associate Data Practitioner exam guide v1.0 (read 2026-11-07)
difficulty: beginner
reading-time: 30 min
---

# Section 2: Data Analysis and Presentation

About **27%** of the exam, so roughly 13 to 16 questions. Three objectives: 2.1 find trends and insights with BigQuery and Jupyter notebooks, 2.2 build dashboards in Looker, 2.3 define, train, evaluate and use ML models (BigQuery ML, AutoML, remote LLMs, Model Registry).

## Table of Contents

1. [2.1 Trends and insights with BigQuery and notebooks](#21-trends-and-insights-with-bigquery-and-notebooks)
2. [2.2 Dashboards in Looker](#22-dashboards-in-looker)
3. [2.3 Machine learning with BigQuery ML](#23-machine-learning-with-bigquery-ml)
4. [How the exam tests this section](#how-the-exam-tests-this-section)
5. [Key terms](#key-terms)

---

## 2.1 Trends and insights with BigQuery and notebooks

### SQL for reports

The exam does not ask you to write a hundred-line query; it asks you to recognise the right clause for a business question. The core toolkit is the same in every SQL engine: `GROUP BY` for "per something", aggregate functions for the measure, `ORDER BY ... LIMIT` for "top N", window functions for "compared with the previous row", and date functions for trends.

```sql
-- Monthly revenue and month-over-month change. Standard SQL; ran in DuckDB 2.0.
WITH monthly AS (
  SELECT date_trunc('month', order_date) AS month, SUM(amount) AS revenue
  FROM orders
  GROUP BY month
)
SELECT month,
       revenue,
       revenue - LAG(revenue) OVER (ORDER BY month) AS change_vs_prev
FROM monthly
ORDER BY month;
```

In BigQuery the same shape works, with `DATE_TRUNC(order_date, MONTH)` (argument order differs; that BigQuery form was **not run**). Two BigQuery habits are worth knowing because cost questions come up: select only the columns you need (BigQuery stores data by column, so fewer columns means fewer bytes read), and filter on a partitioning column so BigQuery can skip partitions.

### Notebooks

For exploration that SQL alone does not cover, such as charts, statistics or a quick model, you use a **Jupyter notebook**. In Google Cloud the managed notebook experience named in the guide is **Colab Enterprise** (notebooks inside BigQuery Studio and Vertex-family tooling). A typical notebook cell pulls a BigQuery result into a pandas DataFrame, plots it, and annotates the finding. Notebooks answer "what is going on in this data"; a dashboard answers "show the same view to the team every day".

(I did not fetch the Colab Enterprise documentation for this guide, so treat the product description as general knowledge to confirm on the docs page.)

### Answering business questions

The guide ends objective 2.1 with "analyze data to answer business questions". Translate the question into measure, dimension and time. "Which regions are shrinking?" is revenue (measure) by region (dimension) over months (time), compared across periods. State assumptions: what counts as a customer, which dates, which currency. Exam scenarios reward the answer that uses data already in BigQuery directly rather than exporting it elsewhere.

---

## 2.2 Dashboards in Looker

Google has two products with similar names, and the guide asks you to compare them.

- **Looker Studio** is the free-to-start, self-service reporting tool. You connect a data source, drag charts onto a canvas and share. Good for quick reports and individual teams.
- **Looker** is the enterprise business intelligence platform built around a **semantic model** written in **LookML**. Metrics and joins are defined once in code (versioned in Git), and every dashboard and explore reuses the same definitions, so "revenue" means the same thing everywhere. Good for governed, organisation-wide metrics.

The decision rule: quick, flexible, ad hoc reporting favours Looker Studio; consistent governed metrics across many users favours Looker.

**[📖 Looker documentation](https://docs.cloud.google.com/looker/docs/intro)** - the intro describes LookML as Looker's language for describing your SQL data and the options you want to offer users, and dashboards as a main content type alongside Looks. (The same page does not mention Looker Studio; the Studio-versus-Looker comparison above is general knowledge I could not confirm from a docs page.)

LookML parameters the guide mentions are small edits to the model: change a dimension's SQL, rename a label, add a filter. You do not need to write a full model, but you should recognise that a LookML change alters the data model for every dashboard that uses it, while a dashboard change affects only that dashboard.

Creating, modifying and sharing dashboards is a product skill: pick a chart type that fits the question (line for trend, bar for comparison, table for exact values), add filters, set who can view.

---

## 2.3 Machine learning with BigQuery ML

### Why BigQuery ML

BigQuery ML lets you create and run ML models with GoogleSQL, where the data already lives, so you do not export data or write training code. Models are stored in BigQuery datasets just like tables.

**[📖 Introduction to BigQuery ML](https://docs.cloud.google.com/bigquery/docs/bqml-introduction)** - supported model types: linear and logistic regression, K-means, matrix factorization, PCA and time series trained inside BigQuery; boosted trees, random forest, DNN and AutoML trained externally; remote and imported models.

Match the model to the question:

| Business question | Kind of problem | Model type |
|---|---|---|
| Will this customer buy? (yes/no) | Classification | Logistic regression, boosted trees |
| What will this order total be? | Regression | Linear regression |
| Which customers behave alike? | Clustering | K-means |
| What should we recommend? | Recommendation | Matrix factorization |
| What will sales be next month? | Forecasting | ARIMA_PLUS |

**[📖 BigQuery ML workflow and model types](https://docs.cloud.google.com/bigquery/docs/e2e-journey)** - the create, inspect, evaluate, predict, explain and monitor steps and the model types per use case

### A standard ML project

Plan it in four steps: collect and prepare data, train, evaluate, predict. Splitting data into a training set and a held-out evaluation set matters: you must evaluate on data the model has not seen, or the score flatters the model.

```sql
-- BigQuery ML. Syntax follows the reference pages; NOT run (needs a BigQuery project).
CREATE OR REPLACE MODEL `myproject.mydataset.purchase_model`
  OPTIONS (model_type = 'LOGISTIC_REG',
           input_label_cols = ['will_purchase'])
AS SELECT * FROM `myproject.mydataset.customers_train`;

SELECT * FROM ML.EVALUATE(MODEL `myproject.mydataset.purchase_model`,
  (SELECT * FROM `myproject.mydataset.customers_test`));

SELECT * FROM ML.PREDICT(MODEL `myproject.mydataset.purchase_model`,
  (SELECT * FROM `myproject.mydataset.new_customers`));
```

`MODEL_TYPE` is required in `CREATE MODEL`; `INPUT_LABEL_COLS` names the label column. `ML.EVALUATE` returns, for classification, precision, recall, accuracy, F1, log loss and ROC AUC. `ML.PREDICT` returns `predicted_<label>` (and `predicted_<label>_probs` for classification) and passes unused input columns through.

**[📖 CREATE MODEL reference](https://docs.cloud.google.com/bigquery/docs/reference/standard-sql/bigqueryml-syntax-create)** - `CREATE MODEL`, `IF NOT EXISTS`, `OR REPLACE`, `MODEL_TYPE`, `INPUT_LABEL_COLS`
**[📖 ML.EVALUATE](https://docs.cloud.google.com/bigquery/docs/reference/standard-sql/bigqueryml-syntax-evaluate)** - syntax, default 0.5 threshold and classification metrics
**[📖 ML.PREDICT](https://docs.cloud.google.com/bigquery/docs/reference/standard-sql/bigqueryml-syntax-predict)** - syntax and output columns

### AutoML, pretrained LLMs and the Model Registry

- **AutoML** is among the externally trained model types: you supply data and a label, and the service selects features and models for you. Choose it when you want good results without hand-tuning.
- **Pretrained LLMs** such as Gemini are reached from BigQuery through a **remote model**: a model object that points at a hosted endpoint through a connection. You then call AI functions over table columns (for example `AI.GENERATE_TEXT`, `AI.GENERATE_EMBEDDING`) to summarise, classify or extract. Queries against remote models also incur charges on the model-hosting side.
- **Model Registry** is where you organise and version models; the docs say BigQuery ML models can be registered there and deployed to endpoints for online prediction.

**[📖 Generative AI in BigQuery](https://docs.cloud.google.com/bigquery/docs/generative-ai-overview)** - remote models over Gemini and partner models, and the AI function family

---

## How the exam tests this section

- **Pick the model for the question.** Yes/no: classification. A number: regression. Groups without labels: clustering. Future values over time: time series.
- **BigQuery ML vs exporting.** If the data is in BigQuery and the model type is supported, BigQuery ML wins on effort.
- **Looker or Looker Studio.** Governed shared definitions: Looker. Quick self-service: Looker Studio.
- **Evaluate before predicting.** `ML.EVALUATE` on held-out data comes before `ML.PREDICT` on new data.
- **LLMs.** Through a remote model and AI functions, not by exporting text to another system.

## Key terms

- **Colab Enterprise** - the managed Jupyter-style notebook experience used with BigQuery for exploration and visualization.
- **LookML** - Looker's language for describing your SQL data model, so metrics are defined once and reused.
- **Looker Studio** - a self-service reporting tool for quick charts and dashboards over many data sources.
- **BigQuery ML** - lets you create, train, evaluate and run ML models with GoogleSQL inside BigQuery.
- **CREATE MODEL** - the BigQuery ML statement that trains a model; MODEL_TYPE is a required option.
- **ML.EVALUATE** - a BigQuery ML function that returns evaluation metrics such as precision, recall and ROC AUC.
- **ML.PREDICT** - a BigQuery ML function that runs a trained model over new rows to produce predictions.
- **AutoML** - a model option where the service selects features and algorithms automatically from labelled data.
- **Remote model** - a BigQuery model object that calls a hosted model, such as Gemini, through a connection.
- **Model Registry** - the place to organize and version models and deploy them to endpoints for online prediction.
- **Logistic regression** - a classification model type, MODEL_TYPE LOGISTIC_REG, for binary and multi-class labels.
- **K-means** - a clustering model type that groups similar rows without labels.
