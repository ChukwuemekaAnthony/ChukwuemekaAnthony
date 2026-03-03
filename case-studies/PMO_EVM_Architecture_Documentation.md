# PMO Performance & EVM Architecture (BQE Core + Smartsheet + Power BI)

## Purpose
A reusable reference architecture for a governed PMO performance system that:
- Ingests financial actuals/spend from BQE Core (Actual Cost inputs)
- Collects PM-owned plan dates and execution progress through governed Smartsheet Update Requests
- Calculates EVM-style metrics in Smartsheet and Power BI (Power Query + DAX)
- Delivers operational Smartsheet dashboards and executive Power BI dashboards

## End-to-end flow
BQE Core (Actuals/Spend)
-> Integration/Sync (load + refresh)
-> Smartsheet PMO Tracker (System of Record; 1 row per project)

PM Update Requests (Start/End Dates; governed) -> Smartsheet
Progress Update Requests (Phase/Deployment) -> Smartsheet

Smartsheet -> Smartsheet formula KPIs (operational)
Smartsheet -> Power Query (M) -> DAX -> Power BI dashboards (executive)

Smartsheet -> Reports (action queues) -> Smartsheet dashboards (operational)

## Mermaid diagram
```mermaid
flowchart TD
  A[BQE Core\nActuals/Spend (AC)] --> B[Integration / Sync\n(load + refresh)]
  B --> C[Smartsheet PMO Tracker\nSystem of Record (1 row per project)]

  D[PMs / Owners] -->|Update Requests\n(Start/End Dates; governed)| C
  D -->|Update Requests\n(Phase + Deployment progress)| C

  C --> E[Smartsheet Formula Layer\nOperational KPIs]
  C --> F[Power Query (M)\nData shaping + joins]
  F --> G[DAX Measures\nEVM KPIs (CPI/SPI/EAC/ETC)]
  G --> H[Power BI Dashboards\nExecutive / Portfolio Views]

  C --> I[Smartsheet Reports\nAction queues + exceptions]
  I --> J[Smartsheet Dashboards\nOperational visibility]

```

## Implementation checklist
- Define PMO tracker schema (Project ID key, owners, plan fields, progress fields, financial fields)
- Stand up integration/sync from BQE Core to Smartsheet for actuals/spend refresh
- Configure governance: restricted plan fields, update request cadence, required-field rules
- Implement progress update workflows for phase completion and deployment tracking
- Build EVM metrics: PV/EV/AC inputs, CPI/SPI, variance, and forecasting measures (EAC/ETC)
- Publish Smartsheet operational reports + dashboards and Power BI executive dashboards
- Document definitions and SOPs; establish operating cadence for data quality and exception management
