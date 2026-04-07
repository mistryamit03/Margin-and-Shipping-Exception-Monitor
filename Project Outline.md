# AI-Assisted Margin and Shipping Exception Monitor (MVP)

## 1) Project Summary
This project is a lightweight business automation MVP that monitors **margin risk** and **shipping exceptions**.

It combines:
- **workflow automation** with **n8n**
- **SQL-based exception detection** in **BigQuery**
- **dashboard visibility** in **Looker Studio**
- an **AI layer** that generates plain-English summaries of the most important exceptions

The aim is to turn a manual review process into an AI-assisted workflow that is faster, clearer, and easier for business teams to act on.

---

## 2) Business Problem
In many companies, order and shipping data sit in CSV files, spreadsheets, or disconnected systems.

That creates 3 common problems:
1. **Low-margin orders are noticed too late**
2. **Shipping delays are tracked manually**
3. **Teams do not get a fast explanation of which issues need action first**

This MVP solves that by checking order and shipment data automatically, flagging risky cases, and generating an AI summary of the most important exceptions.

---

## 3) MVP Goal
Build a small end-to-end workflow that:
- ingests order and shipment CSV files
- loads them into **BigQuery**
- runs SQL checks for margin and delivery exceptions
- writes flagged records into an exceptions table
- updates a **Looker Studio** dashboard
- uses **n8n** to trigger the workflow and send alerts
- generates an **AI summary** of the highest-priority exceptions in plain English

This is not a full production system.
It is a practical MVP to prove automation, analytics, and AI-assisted business support.

---

## 4) What is being automated?
Before automation, someone would have to:
- open CSV files manually
- compare selling price, cost, and shipping cost row by row
- check whether promised delivery dates were missed
- identify risky orders one by one
- refresh reports manually
- explain the most important issues to stakeholders

This project automates that repetitive review process.

### Automated steps
1. **CSV ingestion**
2. **Data cleaning and loading into BigQuery**
3. **Margin and delay calculation**
4. **Rule-based exception flagging**
5. **Exception table refresh**
6. **Dashboard refresh**
7. **AI-generated summary of top exceptions**
8. **Optional email or Slack-style alert**

---

## 5) How AI is used
The core detection logic still comes from **SQL checks** because they are fast, clear, and easy to validate.

### Option A - AI-generated daily exception summary
After SQL flags low-margin and delayed orders, the workflow sends the highest-priority exceptions to an LLM step that:
- summarizes the top issues in plain English
- explains likely business drivers such as shipping cost spikes or delivery bottlenecks
- highlights which cases should be reviewed first

### Example AI output
> Today’s highest-risk exceptions include 3 negative-margin orders driven by elevated shipping costs and 4 critical shipment delays concentrated in one carrier lane. Immediate review is recommended for orders 1045, 1052, and 1061.

### Why this matters
This turns raw exception rows into fast decision support for leadership and operations teams.

---

## 6) Suggested Tech Stack
### Core stack
- **BigQuery** for storage and SQL checks
- **Looker Studio** for dashboarding
- **n8n** for workflow automation and AI orchestration
- **Python** (optional) for CSV preparation or synthetic data generation

### Why this stack?
- fast to build
- light architecture
- close to real analytics work
- relevant for workflow automation roles
- practical for a GitHub MVP

---

## 7) Exception Logic
The monitor flags orders when:
- margin is below threshold
- margin is negative
- shipment is delayed
- delay is critical
- an order needs manual review

Simple examples:
- **negative margin**: `margin_value < 0`
- **low margin**: `margin_pct < 10%`
- **late shipment**: `delay_days > 0`
- **critical delay**: `delay_days >= 3`

This keeps the logic simple, explainable, and useful for an MVP.

---

## 8) Workflow Design (n8n)
### Trigger
- manual trigger for demo
- later: scheduled trigger once per day

### Flow
1. Read CSV files
2. Validate file structure
3. Load data into BigQuery staging tables
4. Run SQL transformation query
5. Create or refresh the exception table
6. Count high-priority exceptions
7. Send top exception rows to an AI step for summary generation
8. If count is above threshold, send alert with AI summary
9. Dashboard reads the refreshed exception table

### Example alert message
> 7 high-priority exceptions detected today. AI summary: 3 negative-margin orders are linked to rising shipping costs, and 4 critical delivery delays are concentrated in one carrier route.

---

## 9) MVP Build Plan
### Phase 1: Data setup
- create or download CSVs
- load them into BigQuery
- validate column types

### Phase 2: SQL logic
- join datasets
- calculate margin and delay
- create exception flags
- build final exception table

### Phase 3: Dashboard
- connect BigQuery to Looker Studio
- add KPI cards and exception views

### Phase 4: Automation + AI
- create n8n workflow
- trigger data load and SQL refresh
- add AI summary step for high-priority exceptions
- send simple alert

---

## 10) Final One-Line Summary
A lightweight AI-assisted workflow that flags margin risk and shipping delays, refreshes exception dashboards, and generates plain-English summaries for faster business action.
