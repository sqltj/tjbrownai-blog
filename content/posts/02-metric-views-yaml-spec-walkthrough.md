---
title: "Metric Views: Basic Syntax"
date: 2026-03-05
draft: false
hero: images/02-metric-views-thumbnail.svg
categories: ["Databricks", "Semantic Modeling"]
tags: ["metric-views", "databricks", "semantic-layer", "yaml", "data-modeling", "power-bi"]
---

In the last post, I introduced Databricks Metric Views and made the case for why Power BI developers should care. This post gets concrete: we're going to write some YAML.

If you've read the docs and felt a little unsure about what each field does or when to use it, this walkthrough is for you. We'll start from the simplest possible definition and build it up step by step — adding a calculated dimension, then a window measure.

---

## The `CREATE` Statement

Before we look at the YAML, it's worth knowing how it fits into SQL. A Metric View is a SQL object created with a `WITH METRICS` clause:

```sql
CREATE OR REPLACE VIEW catalog.schema.view_name
WITH METRICS
LANGUAGE YAML
AS $$
  -- your YAML goes here
$$
```

The YAML lives inside the `$$` delimiters. Everything else is standard DDL. You run this in the Databricks SQL editor, a notebook, or via the CLI.

---

## Step 1: The Minimal Example

Here's the smallest valid Metric View — one source table, one dimension, two measures:

```sql
CREATE OR REPLACE VIEW adventureworks.gold.internet_sales_metrics
WITH METRICS
LANGUAGE YAML
AS $$
  version: 1.1
  comment: "Internet sales KPIs"
  source: adventureworks.gold.fact_internet_sales

  dimensions:
    - name: Order Date
      expr: OrderDate

  measures:
    - name: Total Sales
      expr: SUM(SalesAmount)

    - name: Order Count
      expr: COUNT(1)
$$
```

That's it. Once created, you query it like this:

```sql
SELECT
  `Order Date`,
  MEASURE(`Total Sales`)  AS total_sales,
  MEASURE(`Order Count`)  AS order_count
FROM adventureworks.gold.internet_sales_metrics
GROUP BY ALL
ORDER BY `Order Date`
```

A few things to note right away:

- **`version: 1.1`** — Use this for all standard metric views. It requires Databricks Runtime 17.2+.
- **`source`** — A fully-qualified three-part name (`catalog.schema.table`).
- **Measures use aggregate functions** — `SUM`, `COUNT`, `AVG`, `MIN`, `MAX`. A measure without an aggregation will fail.
- **`MEASURE()` in queries** — All measures must be wrapped in `MEASURE()`. You can't just `SELECT SalesAmount`.

**Your DAX skills transfer directly.** The aggregation logic is identical — only the syntax changes:

| Measure | DAX | YAML |
|---------|-----|------|
| Total Sales | `Total Sales = SUM(FactInternetSales[SalesAmount])` | `expr: SUM(SalesAmount)` |
| Order Count | `Order Count = COUNTROWS(FactInternetSales)` | `expr: COUNT(1)` |

---

## Step 2: Adding a Calculated Text Dimension

Raw column values often aren't query-ready. In Power BI you'd handle this with a calculated column. In Metric Views, you put the transformation directly in the dimension's `expr` field — it's just SQL.

Let's add a human-readable order size category based on `SalesAmount`:

```sql
CREATE OR REPLACE VIEW adventureworks.gold.internet_sales_metrics
WITH METRICS
LANGUAGE YAML
AS $$
  version: 1.1
  comment: "Internet sales KPIs"
  source: adventureworks.gold.fact_internet_sales

  dimensions:
    - name: Order Date
      expr: OrderDate

    - name: Order Size
      expr: CASE
        WHEN SalesAmount >= 2000 THEN 'Large'
        WHEN SalesAmount >= 500  THEN 'Medium'
        ELSE 'Small'
        END
      comment: "Order size bucket based on SalesAmount"

  measures:
    - name: Total Sales
      expr: SUM(SalesAmount)

    - name: Order Count
      expr: COUNT(1)
$$
```

Now you can slice by `Order Size` in any query:

```sql
SELECT
  `Order Size`,
  MEASURE(`Total Sales`)  AS total_sales,
  MEASURE(`Order Count`)  AS order_count
FROM adventureworks.gold.internet_sales_metrics
GROUP BY ALL
ORDER BY total_sales DESC
```

The `expr` field supports any SQL expression — `CASE`, `DATE_TRUNC`, `CAST`, string functions, anything your SQL warehouse understands. 

This is the equivalent of a calculated column in your semantic model. In DAX you'd:

```dax
Order Size =
  SWITCH(
    TRUE(),
    FactInternetSales[SalesAmount] >= 2000, "Large",
    FactInternetSales[SalesAmount] >= 500,  "Medium",
    "Small"
  )
```

Same logic, but in a Metric View. The one thing I really want to hammer home is that you've been working in Power BI, you already know this stuff because you aren't just a Power BI developer, report writer, etc. -- you're a semantic modeler. The skills you have building models in Power BI are directly applicable to building Metric Views. You don't need to learn new concepts, just some new syntax.

---

## Complete Example

Here's the full YAML spec for reference. We'll dig deeper into the different components and review some common use cases in future posts, but this is what a complete Metric View definition looks like:

```yaml
version: 1.1            # "1.1" for standard views, "0.1" required for window measures
comment: "..."          # Optional description

source: catalog.schema.table   # Required — three-part name
filter: column > value         # Optional — global WHERE applied to all queries

dimensions:             # Required — at least one
  - name: Display Name  # What you reference in queries (backtick-quoted if it has spaces)
    expr: sql_expr      # Any SQL expression — column ref, CASE, DATE_TRUNC, etc.
    comment: "..."      # Optional

measures:               # Required — at least one
  - name: Display Name  # Referenced via MEASURE(`name`) in queries
    expr: AGG(column)   # Must use an aggregate function
    comment: "..."      # Optional
    window:             # Optional — makes this a window measure (requires version: 0.1)
      - order: Dim Name # Must match a dimension name
        range: ...      # cumulative | current | trailing N unit | leading N unit | all
        semiadditive: last | first

joins:                  # Optional — star/snowflake schema joins
  - name: alias
    source: catalog.schema.dim_table
    on: source.fk = alias.pk

materialization:        # Optional, experimental — pre-compute aggregations
  schedule: every 6 hours
  mode: relaxed
```

---

## What's Next

Next post we'll dig into **period-to-date calculations** — running totals, YTD, and trailing windows — and how the Metric Views `window` block compares to DAX time intelligence functions.

---

### References & Further Reading

- [Metric Views YAML Syntax Reference](https://docs.databricks.com/en/metric-views/data-modeling/syntax)
- [Window Measures Documentation](https://docs.databricks.com/aws/en/metric-views/data-modeling/window-measures)
- [MEASURE() Function](https://docs.databricks.com/en/sql/language-manual/functions/measure)
