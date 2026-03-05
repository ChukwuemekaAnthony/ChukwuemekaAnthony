# Workday Bonus Accrual — Process Architecture

**Incentive Compensation Analytics & Bonus Accrual Architecture**  
From Performance Platform Design to Workday-Native Implementation

---

## Overview

This document describes the end-to-end architecture of a bonus accrual system designed and built to automate monthly incentive compensation calculations at a mid-size professional services consulting firm. The platform replaced manual, spreadsheet-based accrual estimates with a governed, automated calculation engine — eliminating year-end P&L corrections caused by inaccurate in-year estimates.

The architecture is organized into five modules that mirror Workday's native compensation and financial accounting configuration model.

---

## The Composite Productivity Metric

The accrual model combines two consulting performance signals into a single productivity score:

| Metric | Formula | Target |
|---|---|---|
| **Utilization Rate** | Billable Hours ÷ Available Hours | 75–80% |
| **Realization Rate** | Client-Approved Hours ÷ Submitted Billable Hours | Firm-defined |
| **Composite Score** | Utilization Index × Realization Index | Performance tier input |

**Example:** 0.80 utilization × 0.875 realization = 0.70 composite score — meaning 70% of available time is effectively converting to revenue.

### Performance Tier Mapping

| Composite Score | Performance Tier | Bonus Multiplier | % of Target Paid |
|---|---|---|---|
| 0.90 and above | Exceeds | 1.20x | 120% of target |
| 0.75 – 0.89 | At Plan | 1.00x | 100% of target |
| 0.60 – 0.74 | Below Plan | 0.75x | 75% of target |
| Below 0.60 | Not Eligible | 0x | No accrual |

---

## Module 1 — Time Tracking

**Purpose:** Capture and validate billable time before it enters the billing and utilization pipeline.

| Step | System | Action |
|---|---|---|
| Consultant Time Entry | Time Tracking | Consultant logs hours against project and worktag hierarchy |
| Time Entry Type Tagging | Time Tracking | Hours tagged Billable vs Non-Billable |
| Manager / Supervisor Approval | Time Tracking | Approval workflow validates and locks time blocks |
| **Approved Billable Time Blocks** | Output | Tagged to project and worktag hierarchy — feeds Module 2 |

---

## Module 2 — Project Billing & Resource Management

**Purpose:** Derive Realization Rate and Utilization Rate from approved time data.

### Realization Rate (Left Branch)

| Step | System | Action |
|---|---|---|
| Billing Rules Applied | Project Billing | Rate caps, RTE ceilings, client approval logic applied |
| Billing Transactions Generated | Project Billing | Write-offs processed → recognized revenue determined |
| **Realization Rate** | Output | Client-Approved Hours ÷ Submitted Billable Hours |

### Utilization Rate (Right Branch)

| Step | System | Action |
|---|---|---|
| Scheduled Hours from Position Record | Resource Management | Gross available hours from position, minus approved Absence/PTO |
| Available Hours Denominator | Time Tracking / HCM | PTO exclusion governance logic applied |
| **Utilization Rate** | Output | Billable Hours ÷ Available Hours |

---

## Module 3 — Composite Productivity Scoring

**Purpose:** Combine Realization Rate and Utilization Rate into a single composite score and assign a performance tier.

| Step | Logic |
|---|---|
| **Composite Productivity Score** | Utilization Index × Realization Index |
| **Performance Tier Assignment** | Score mapped to tier thresholds defined by Finance & HR |

The composite score feeds directly into Module 4 as the input variable for eligibility and multiplier determination.

---

## Module 4 — HCM Compensation Plan

**Purpose:** Apply eligibility rules and payout structure to calculate estimated bonus liability per consultant.

| Component | System | Description |
|---|---|---|
| Compensation Plan | HCM Compensation | Bonus Plan with eligibility rules, compensation basis, target % |
| Compensation Grade Profile | HCM Compensation | Bonus tier / payout level per grade |
| Eligibility Rule Evaluation | HCM | Evaluates grade, tenure, performance rating, productivity tier |
| Payout Structure Applied | HCM | Flat / Tiered / Formula-based multiplier applied |
| **Estimated Bonus Liability Per Consultant** | Output | Calculated monthly per performance period |

### Workday Configuration Mapping

| What I Built (Power Platform) | Workday Native Equivalent |
|---|---|
| Dataverse target bonus % field | Compensation Plan → Compensation Basis |
| Power Automate eligibility routing | Eligibility Rules (declarative, dynamic evaluation) |
| Dataverse tier lookup table | Compensation Grade Profiles |
| Scheduled Power Automate flow | Compensation Review (period process) |

---

## Module 5 — Financial Accounting & Adaptive Planning

**Purpose:** Post period-end accrual journal entries and model forward-looking bonus liability.

### Financial Accounting (Left Branch)

| Step | System | Action |
|---|---|---|
| Period End Accrual Journal | Financial Accounting | Debit: Bonus Compensation Expense / Credit: Accrued Compensation |
| Liability Account Updated | Financial Accounting | Accrued Compensation balance sheet account updated |

#### Journal Entry Structure

| Entry Type | Account | Direction | Impact |
|---|---|---|---|
| Period Accrual | Bonus Compensation Expense | Debit | Reduces operating income (P&L) |
| Period Accrual | Accrued Compensation Liability | Credit | Builds on Balance Sheet until paid |

Worktags applied: Cost Center, Supervisory Org, Practice Area — ensuring expense allocates correctly for management reporting.

### Adaptive Planning (Right Branch)

| Step | System | Action |
|---|---|---|
| Workday Adaptive Planning | Adaptive Planning | Forecasts full-year bonus liability from actuals |
| Budget vs Actual Comparison | Adaptive Planning | Actuals from Financials Ledger vs Adaptive budget |

### Period Close

Both branches feed into **Period Accrual Adjusted or Confirmed by Finance** — the control point where Finance either posts, adjusts, or carries forward the period liability.

---

## Output — Finance & HR Reporting

| Report | Consumer | Content |
|---|---|---|
| Finance Report | CFO / Controller | Accrual liability + variance vs budget |
| HR Compensation Tracking | HR Leadership | Running payout obligation by employee |
| Executive Dashboard | Leadership | Portfolio productivity trends + comp liability |

---

## Technology Stack

| Platform | Component |
|---|---|
| Microsoft Power Apps | Front-end forms and workflow UI |
| Microsoft Power Automate | Routing logic, scheduling, notifications |
| Microsoft Dataverse | Structured data model — employees, tiers, outputs |
| Power BI | Executive reporting dashboard |
| Excel / Power Query | Finance reconciliation and accrual reporting |
| BQE Core (ODBC) | Utilization and realization source data |

---

## Data Pipeline Summary

```
Timesheets → Time Tracking → Project Billing → Composite Score
→ Compensation Plan → Accrual Journal → Adaptive Forecast
→ Finance & HR Reporting
```

---

*Anthony Chukwuemeka Orji | emekaorji101@gmail.com | (312) 934-7121*  
*https://chukwuemekaanthony.github.io/ChukwuemekaAnthony/*
