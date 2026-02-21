---
title: "Introduction to Metric Views for Power BI Developers"
date: 2026-02-18
draft: false
hero: images/01-metric-views-thumbnail.svg
categories: ["Databricks", "Semantic Modeling"]
tags: ["metric-views", "power-bi", "databricks", "semantic-layer"]
---

## Why This Matters

This blog post is for you: the Power BI Developer. If you're reading this, perhaps you've already mastered Power BI semantic modeling. You know how to create Power BI data models with relationships, and understand how to use DAX to create calculated measures and calculated measures.

I have some good news. **Those semantic modeling skills are transferrable**. The better news: **Databricks Metric Views are simpler and more powerful than Power BI semantic models**. If you haven't taken a look at Metric Views for a while, I do believe now is the time to get ahead of this, because the calculation capabilities. 

The implementation of semantic modeling in Databricks is a little different. In Power BI, your semantic model lives *inside a PBIX file*. That file is then deployed. Its deployed formerly in the Power BI Service, and now presently inside of Fabric. However, once deployed its a separate artifact. That artifact sits separately and connects to its data sources. Security is managed separetely. Verson control has improved with the new PBIR format, but its still a complex file that's a commbination of a dashboards, a semantic model, connections, and queries.

In Databricks, your semantic layer lives *inside the lakehouse*. Its governed by Unity Catalog, and accessible to AI/BI dashboards, AI/BI Genie, and downstream tools as well - even those external to databricks. The core language for querying Metric Views is SQL, which is a welcome relief to anyone that's struggled with DAX queries, despite knowing an industry-standard data query language.

In this series, I'll walk to through Metric Views conceptually so you can understand how your current skills make you well-positioned to develop semantic models in Databricks will Metric Views.

---

## The Core Analogy: From PBIX to Lakehouse

### Power BI Mental Model

```
(Gold) Data → Connections → PBIX Semantic Model (DAX) → Power BI Dashboards
           ↓
           Only accessible inside Power BI Desktop/Fabric
           One file per semantic model
           Version control is messy
           Security can be nexted between source systems and the semantic model
```

### Databricks Mental Model

```
(Gold) Data → Metric Views (YAML) → Dashboards / Genie / Any Tool
                                              ↓
                                              Governed in Unity Catalog
                                              Versioned like code
                                              One source of truth
                                              Accessible everywhere
```

**The key difference:** Your semantic layer isn't a proprietary artifact—it's a governed database object.

---

## Concept Mapping: Everything You Know, Translated

| Power BI Concept | Databricks Equivalent | Key Difference |
|---|---|---|
| **Semantic Model** | **Metric View** | Lives in lakehouse, not PBIX |
| **DAX Calculated Measure** | **Measure Expression** | Defined with SQL syntax |
| **DAX Calculated Column** | **Dimension Expression** | Measure expression defined with SQL syntax |
| **Dimension Table** | **Dimensions (in YAML)** | Defined per expression instead of per table
| **Row-Level Security** | **Unity Catalog RLS** | Governance + filtering, not token-based |

### Why This Matters to You

1. **No procedural DAX** — You write declarative YAML instead. Less "how" logic, more "what" definitions.
2. **Metrics are discoverable** — Genie (Databricks' AI/BI tool) automatically surfaces your metrics for natural-language queries.
3. **Separation of concerns** — Dimension definitions and measure definitions are separate, not tangled in DAX logic.
4. **Reusability is enforced** — Child views can layer metrics without redefining KPIs (hard to do in Power BI).

---

## Why Move to Metric Views?

### Problem 1: The PBIX Bottleneck

In Power BI:
- One developer owns the semantic model
- Changes require republishing the entire PBIX
- Multiple semantic models = multiple definitions of "Revenue"
- Governance is policy-based, not enforced

In Databricks:
- Metric Views are SQL objects, versioned like code
- Child views can layer domain-specific metrics without modifying the base
- One "Source of Truth" for each metric
- Governance is built in (Unity Catalog permissions, lineage tracking)

### Problem 2: The DAX Complexity

DAX measures are powerful but procedural. Figuring out filter context when stacking multiple filter expressions inside a CALCULATE() can be a challenging for SQL experts. Business users - forget about it. Here, we define exactly the behavior we want. We can dig deeper into some of those more complex examples in future posts, but the key point is that you don't need to understand the "how" of DAX filter context to define a metric. You just need to know "what" you want to calculate.

```dax
// Power BI DAX
Revenue YTD =
  CALCULATE(
    SUM(Sales[Amount]),
    DATESYTD(Calendar[Date])
  )
```

YAML measures are declarative:

```yaml
# Databricks Metric View (simpler, clearer intent)
  - name: Revenue YTD
    expr: SUM(o_totalprice)
    window:
      order: Date
      partition_by: OrderYear
      range: cumulative
      semiadditive: last

```

### Problem 3: The Dashboard Consistency Problem

In Power BI:
- Different dashboards may use slightly different definitions of the same metric
- DAX logic lives in the model, but BI developers can create their own calculated columns
- "Revenue" in Dashboard A ≠ "Revenue" in Dashboard B

In Databricks:
- Metric Views enforce one definition
- Dashboards and Genie both query the same metric definition
- Consistency is structural, not policy-based

---

## The Architecture: Metric Views in Context

Here's where Metric Views sit in a Databricks data architecture:

```
Lakehouse (Delta Lake)
├── Bronze Layer (raw data)
├── Silver Layer (cleaned, conformed)
├── Gold Layer (star schema, optimized for analytics)
│   ├── Fact: sales_orders
│   ├── Dimension: dim_customer
│   ├── Dimension: dim_product
│   └── Dimension: dim_date
│
└── Semantic Layer (Metric Views) ← You are here
    ├── mv_sales_orders_metrics (base)
    ├──── mv_sales_orders_finance (child view - adds margin)
    └──── mv_sales_orders_marketing (child view - adds attribution)
         ↓
    Consumed by:
    - AI/BI Dashboards
    - Genie (natural language queries)
    - SQL analysts
    - Downstream tools
```

**Key insight:** Metric Views sit *between* your Gold schema and consumers. They enforce semantics without blocking access.

---

## Queries are Simple, Familiar SQL


```sql
SELECT
  `Order Month`,
  `Region`,
  MEASURE(`Total Revenue`) AS revenue,
  MEASURE(`Order Count`) AS orders,
  MEASURE(`Avg Order Value`) AS aov
FROM analytics.mv_sales_metrics
WHERE EXTRACT(YEAR FROM `Order Month`) = 2024
GROUP BY ALL
ORDER BY ALL
```

And it just works - no new language to learn, just reference the metric view as if it were a table in your catalog and use the MEASURE() function to indicate a calculation.

### The YAML (You'll Learn This Next Post)

```yaml
version: 1.1
comment: "Sales KPIs for orders and customers"
source: gold.sales_orders
dimensions:
  - name: Order Month
    expr: DATE_TRUNC('MONTH', order_date)
  - name: Region
    expr: customer_region
measures:
  - name: Total Revenue
    expr: SUM(amount)
  - name: Order Count
    expr: COUNT(1)
  - name: Avg Order Value
    expr: SUM(amount) / COUNT(DISTINCT order_id)
```

Its concise, easy to read, and easy to manage.

---

## Why Now? The Enterprise Shift

### The Rise of the Lakehouse

Companies are consolidating:
- Data warehousing is moving from legacy platforms to Delta Lake.
- BI tools are expanding: Power BI, Tableau, Looker, and Sigma all integrate with Databricks. In the age of AI, we should design our semantic layers to be tool-agnostic, and Metric Views are built for that.
- Governance: Unity Catalog enforces access control at the data layer. You get full lineage an auditability for your metrics from ingestion to reporting with no additional tooling or configuration.

### Purpose-built for the AI Era

Genie (Databricks' natural-language BI tool) changes the game:
- Analysts ask questions in English
- Genie needs clear metric definitions to answer correctly
- Metric Views are *designed* for Genie integration

### Licensing
If your data is already in Databricks, you dont have to pay per-user licensing fee for AI/BI Dashboards or Genie. That's all included. Moreover, you no longer have to compromise on the reusability and centralization of your calculation logic. You now have Metric Views available - also with no additional licensing.

### The Cost of Manual Consistency

Companies with multiple semantic layers (Power BI + SQL models + downstream definitions) are paying a consistency tax:
- Audit questions: "Is this metric defined the same everywhere? How many Power BI models do we have?"
- Reconciliation work: "Why does Dashboard A show different revenue than Dashboard B?"
- Governance drift: "Who changed the definition of COGS, and when? Where do I define row level security for this metric?"

Metric Views solve this by making the semantic layer a governed database object.

Stay tuned for the next post in this series!

---

### References & Further Reading

- [Databricks Metric Views Documentation](https://docs.databricks.com/en/metric-views/)
- [Unity Catalog Governance](https://docs.databricks.com/en/data-governance/unity-catalog/)
- [Databricks AI/BI Genie](https://docs.databricks.com/en/genie/)
