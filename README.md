
# FlexBank — Finance Analytics Dashboard

An end-to-end Power BI dashboard built for a financial organization to monitor transaction performance, customer behavior, and operational fees/taxes across 50,000+ bank transaction records.

---

<img width="762" height="427" alt="image" src="https://github.com/user-attachments/assets/c0b77b45-1ae6-4472-a95a-ee31ad7821cd" />

<img width="1529" height="853" alt="image" src="https://github.com/user-attachments/assets/2395f2a0-079d-4253-beca-02c4b0738f58" />

---
## Business Problem

FlexBank's management team needed a centralized way to track financial performance but lacked visibility into:

- Overall transaction growth and YoY performance changes
- Monthly trends in transaction volume and value
- Successful vs. failed vs. pending transactions
- Which customer segments, states, and transaction types were driving revenue
- Operational fees and tax collected across the business
- Customer demographics (occupation, gender) behind transaction activity

The brief called for a dashboard that lets stakeholders dynamically filter by year, occupation, and merchant category, switch between metrics on the fly, and drill into individual transaction records — all from a single, real-time analytical view.

## Solution Overview

The dashboard is built around a star schema with a `finance_transactions` fact table, a `customers` dimension, a `Calendar_Table` date dimension, and a `_Measures` table holding all DAX logic — connected through a `Dynamic Metrics` field parameter so a single set of visuals can re-render against five different KPIs without duplicating charts.

### Page 1 — Overview
- **KPI cards** for Total Amount, Total Transactions, Average Transaction Value, Total Fees, and Total Tax, each with a built-in YoY % comparison against the prior year
- **Area chart** of transaction volume by month, bound to a Dynamic Metric slicer so the same visual can show Amount, Transactions, Fees, or Tax trends without rebuilding the chart
- **Bar chart** of transactions by customer segment (Retail, Premium, SME, Corporate, Wealth)
- **Bar chart** of transactions by state, surfacing top-performing regions
- **Donut chart** of transaction status (Success / Failed / Pending) for operational efficiency tracking
- **Donut chart** of transaction volume by gender
- **Heatmap pivot table** of Amount, Fees, and Tax by transaction type (Bill Payment, Card Payment, Deposit, Loan EMI, Transfer, Withdrawal, etc.), with a color gradient highlighting top contributors
- **Slicers**: Year, Dynamic Metric, Occupation, Merchant Category — all cross-filtering every visual on the page

### Page 2 — Transaction Details (drill-through)
- KPI summary carried over from the Overview page for context
- A detailed, filterable grid of underlying transaction records (transaction ID, date, customer ID/name, type, status, gender, city, occupation, segment) for analysts who need to trace a KPI back to source rows
- Back navigation button to return to the Overview page

## Data Model

```
customers (dimension)
   ├─ customer_id, customer_name, gender, occupation,
   │  city, state, customer_segment
   └─ relates to → finance_transactions

finance_transactions (fact, 50,000+ rows)
   ├─ transaction_id, transaction_date, transaction_type,
   │  transaction_status, merchant_category, amount, fees, tax
   └─ relates to → Calendar_Table, customers

Calendar_Table (date dimension)
   └─ Year, Month

Dynamic Metrics (field parameter table)
   └─ powers the Dynamic Metric slicer used by the area, bar,
      and donut charts

_Measures (disconnected measure table)
   └─ houses all DAX measures listed below
```

## Key DAX Measures

| Measure | Purpose |
|---|---|
| `Total Amount` | Sum of all transaction amounts |
| `Total Transactions` | Count of transactions |
| `Average Txn Value` | Total Amount ÷ Total Transactions |
| `Total Fees` | Sum of fees collected |
| `Total Tax` | Sum of tax collected |
| `YoY % Amount` | Year-over-year % change in Total Amount |
| `YoY % Transactions` | Year-over-year % change in Total Transactions |
| `YoY % Avg Txn Value` | Year-over-year % change in Average Transaction Value |
| `YoY % Fees` | Year-over-year % change in Total Fees |
| `YoY % Tax` | Year-over-year % change in Total Tax |
| `Selected Year` / `Selected Occupation` / `Selected Merchant Category` | Drive the dynamic title text shown on the report header based on active slicer selections |

YoY comparisons use time-intelligence patterns against `Calendar_Table` so every KPI card automatically recalculates as the Year slicer changes.

## Process

1. **Power Query** — cleaned and shaped raw transaction and customer data, handled data types, and built the relationship-ready table structure.
2. **Data Modeling** — established a star schema with one-to-many relationships between `finance_transactions`, `customers`, and `Calendar_Table`, plus a disconnected `Dynamic Metrics` field parameter for metric-switching visuals.
3. **DAX** — wrote YoY growth measures, dynamic title measures, and aggregate KPI measures in a centralized `_Measures` table.
4. **Visualization** — designed KPI cards, trend charts, categorical breakdowns, and a heatmap-style pivot table, themed around a consistent brand palette.
5. **Interactivity** — added cross-filtering slicers, a dynamic metric selector, and a drill-through page with a detailed transaction grid.

## Tech Stack

- **Power BI Desktop** — data modeling, DAX, report design
- **Power Query (M)** — data cleaning and transformation
- **DAX** — KPI measures, YoY time intelligence, field parameters

## Files in this Repository

- `Flex_Bank_Analysis.pbix` — the full Power BI report file
- `Business_Requirements.docx` — original requirements brief used to scope the dashboard
