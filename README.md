# FlexBank — Finance Analytics Dashboard

An end-to-end Power BI dashboard built for a financial organization to monitor transaction performance, customer behavior, and operational fees/taxes across 50,000+ bank transaction records.

**[🔗 View on GitHub](https://github.com/ayushi-gajendra/FlexBank_Finance_Analytics_Dashboard)** | Power BI · DAX · Power Query

## Dashboard Preview

**Overview**
![FlexBank Overview page — KPI cards, monthly trend, segment/state/status/gender breakdowns, and a transaction-type heatmap](imgs/overview.png)

**Transaction Details (drill-through)**
![FlexBank Transaction Details page — filtered KPI summary plus a row-level transaction grid](imgs/transaction_details.png)

---

## Key Findings

- **81.58% of transactions succeed** company-wide, with 12.63% failing and 5.79% pending — a useful baseline for tracking operational reliability over time.
- **Retail is the dominant segment by volume** (114 of ~210 transactions in the filtered view), more than 3x the next-largest segment (Premium, 35), suggesting retail customer experience and uptime have outsized impact on overall metrics.
- **Loan EMI and Transfer drive the most amount and fee volume** among transaction types, ahead of Deposit, Investment, and Card Payment — useful for prioritizing where fee-structure changes would have the biggest revenue impact.
- **Maharashtra and Tamil Nadu lead state-wise transaction volume**, with a fairly even spread across Karnataka, Madhya Pradesh, and Uttar Pradesh — indicating the customer base is geographically diversified rather than concentrated in one region.
- The **drill-through view makes segment-level patterns traceable to row level** — e.g., filtering to "Business Owner" occupation surfaces a cluster of failed transactions, a natural next investigation into possible payment-method or risk-flag patterns.

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

---

## Ideas for Further Enhancement

- **Investigate the "Business Owner" failed-transaction cluster** seen in the drill-through view — if it's concentrated in specific transaction types or cities, that's a concrete root-cause finding worth adding here.
- **A short Loom/YouTube walkthrough (60–90 seconds)** linked at the top is one of the highest-converting additions for a BI portfolio repo — it removes all friction for a reviewer to see the interactivity (slicers, drill-through) in action, which static screenshots can't fully convey.
- **One-sentence "who uses this and what decision it drives"** right above the Business Problem section would sharpen the framing from "a dashboard I built" to "a dashboard that solves a business problem," which is what tends to land with analytics hiring managers.
