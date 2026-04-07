# Margin and Shipping Exception Monitor (MVP)

## 1) Project Summary
This project is a lightweight analytics and workflow automation MVP designed to monitor **margin risk** and **shipping exceptions**.

The goal is simple:
- detect low-margin or loss-making orders early
- flag delayed or high-risk shipments automatically
- make those exceptions visible in a dashboard
- reduce manual checking by sending alerts when important issues appear

This project is inspired by real business problems in operations, supply chain, and industrial sourcing environments, where teams need quick visibility into order health, margin pressure, and fulfillment issues.

---

## 2) Business Problem
In many companies, order and shipping data sit in CSV files, spreadsheets, or disconnected systems.

That creates 3 common problems:
1. **Low-margin orders are noticed too late**
2. **Shipping delays are tracked manually**
3. **Teams do not have one clear view of exceptions that need action**

This MVP solves that by creating a small automated flow that checks order and shipment data on a schedule and highlights risky cases.

---

## 3) MVP Goal
Build a small end-to-end workflow that:
- ingests order and shipment CSV files
- loads them into **BigQuery**
- runs SQL checks for margin and delivery exceptions
- writes flagged records into an exceptions table
- updates a **Looker Studio** dashboard
- optionally sends an alert using **n8n**

This is not a full production system.
It is a fast, practical MVP to prove automation, analytics, and business thinking.

---

## 4) What is being automated?
Before automation, someone would have to:
- open CSV files manually
- compare selling price vs cost vs shipping cost
- check whether promised delivery dates were missed
- identify risky orders one by one
- build or refresh reports manually

This project automates that repetitive review process.

### Automated steps
1. **CSV ingestion**
2. **Data cleaning and loading into BigQuery**
3. **Margin calculation**
4. **Delay calculation**
5. **Rule-based anomaly flagging**
6. **Exception table creation**
7. **Dashboard refresh**
8. **Optional email / Slack-style alert**

---

## 5) Where AI fits in
AI is **optional** in the MVP.
The first version should mainly use **rule-based logic** because it is faster, easier to explain, and more reliable for a portfolio project.

### Good AI use in this project
A small AI layer can be added in one of these ways:
- classify exception notes into categories such as `margin_issue`, `shipping_delay`, `cost_spike`, `manual_review`
- generate a short natural-language summary of the top daily exceptions for leadership
- suggest priority levels based on exception context

### Important note
For the MVP, **AI is not the core engine**.
The core engine is:
- SQL checks
- automation workflow
- business alerts
- dashboard visibility

That is still very relevant for automation roles.

---

## 6) Suggested Tech Stack
### Core stack
- **BigQuery** for storage and SQL checks
- **Looker Studio** for dashboarding
- **n8n** for workflow automation
- **Python** (optional) for CSV preparation or synthetic data generation

### Why this stack?
- fast to build
- light architecture
- close to real analytics work
- strong enough for a GitHub portfolio project

---

## 7) Data Options
### Option A: Use open data
Use a public e-commerce or logistics dataset and adapt it.

### Option B: Use synthetic CSVs (recommended for speed)
Create 3 CSV files:

#### `orders.csv`
- order_id
- order_date
- customer_id
- product_type
- country
- selling_price
- quantity
- quoted_margin_pct

#### `costs.csv`
- order_id
- material_cost
- production_cost
- packaging_cost
- shipping_cost
- total_cost

#### `shipments.csv`
- order_id
- carrier
- promised_delivery_date
- actual_delivery_date
- shipment_status
- tracking_event_count

Synthetic data is fine here because the value of the project comes from the workflow and business logic.

---

## 8) Core Calculations
### Margin
```sql
margin_value = selling_price - total_cost
margin_pct = SAFE_DIVIDE((selling_price - total_cost), selling_price)
```

### Shipping delay
```sql
delay_days = DATE_DIFF(actual_delivery_date, promised_delivery_date, DAY)
```

---

## 9) Exception Rules
The MVP should use simple, easy-to-explain rules.

### Margin exception flags
- `negative_margin_flag` -> margin_value < 0
- `low_margin_flag` -> margin_pct < 0.10
- `cost_spike_flag` -> shipping_cost or total_cost much higher than expected

### Shipping exception flags
- `late_shipment_flag` -> delay_days > 0
- `critical_delay_flag` -> delay_days >= 3
- `stalled_shipment_flag` -> status unchanged for too long

### Overall priority
- **High** -> negative margin OR critical delay
- **Medium** -> low margin OR late shipment
- **Low** -> informational exception only

---

## 10) BigQuery Tables
### `stg_orders`
cleaned order data

### `stg_costs`
cleaned cost data

### `stg_shipments`
cleaned shipment data

### `fct_order_health`
joined order + cost + shipment table with calculations

### `fct_exceptions`
filtered table that only contains flagged records

Suggested columns for `fct_exceptions`:
- order_id
- order_date
- country
- selling_price
- total_cost
- margin_value
- margin_pct
- delay_days
- exception_type
- priority_level
- action_status

---

## 11) Workflow Design (n8n)
### Trigger
- manual trigger for demo
- later: scheduled trigger once per day

### Flow
1. Read CSV files
2. Validate file structure
3. Load data into BigQuery staging tables
4. Run SQL transformation query
5. Create / refresh exception table
6. Count high-priority exceptions
7. If count > threshold, send alert
8. Dashboard reads refreshed exception table

### Example alert message
> 7 high-priority exceptions detected today: 3 negative-margin orders and 4 critical shipment delays.

---

## 12) Dashboard KPIs
### KPI cards
- total orders
- total exception orders
- negative margin orders
- delayed shipments
- high-priority exceptions
- average margin %

### Charts / tables
- exceptions by country
- exceptions by product type
- negative margin trend over time
- delayed shipments by carrier
- detail table of flagged orders for manual review

---

## 13) MVP Build Plan
### Phase 1: Data setup
- create or download CSVs
- load into BigQuery
- validate column types

### Phase 2: SQL logic
- join datasets
- calculate margin and delay
- create exception flags
- build final exception table

### Phase 3: Dashboard
- connect BigQuery to Looker Studio
- add KPI cards and exception views

### Phase 4: Automation
- create n8n workflow
- trigger data load and SQL refresh
- send simple alert

---

## 14) Estimated Effort
### Bare-bones MVP
**6 to 10 focused hours**

### Cleaner portfolio version
**1 to 2 extra days** for:
- README polish
- screenshots
- nicer dashboard layout
- optional AI summary step

---

## 15) Project Deliverables
For GitHub, include:
- `README.md`
- sample CSV files
- SQL scripts
- n8n workflow export
- dashboard screenshots
- short architecture diagram

Suggested repo structure:
```text
margin-shipping-exception-monitor/
│
├── data/
│   ├── orders.csv
│   ├── costs.csv
│   └── shipments.csv
│
├── sql/
│   ├── staging_queries.sql
│   ├── order_health.sql
│   └── exceptions.sql
│
├── workflows/
│   └── n8n_workflow.json
│
├── screenshots/
│   └── dashboard.png
│
└── README.md
```

---

## 16) Portfolio Positioning
### What this project demonstrates
- workflow automation
- SQL-based exception monitoring
- margin and shipping analytics
- dashboarding for operations visibility
- translating business pain points into a practical MVP

### Good interview positioning
> I built a lightweight exception-monitoring workflow that automatically flagged low-margin and delayed orders, refreshed a dashboard, and reduced the need for manual review.

---

## 17) Honest Scope Control
To keep this fast and realistic, avoid these in version 1:
- no complex machine learning model
- no full ERP integration
- no real-time streaming architecture
- no overengineered infrastructure

The first version should be simple, useful, and easy to explain.

---

## 18) Optional Version 2 Ideas
Later, extend the MVP with:
- supplier scorecards
- threshold tuning by country or product type
- AI-generated daily exception summaries
- root-cause tagging for exception categories
- alert acknowledgement workflow for ops teams

---

## 19) Final One-Line Summary
A lightweight business automation project that monitors margin risk and shipping delays, flags exception orders automatically, and gives teams a clear dashboard for faster action.
