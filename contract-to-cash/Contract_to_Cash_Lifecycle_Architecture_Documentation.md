# Contract-to-Cash Lifecycle Architecture (Salesforce <-> Smartsheet)

## Purpose
A reusable, client-safe reference architecture for a Contract-to-Cash (C2C) / document lifecycle tracker that:
- Ingests opportunity events from Salesforce
- Uses Smartsheet as the system of record (one row per engagement)
- Enforces stage-gated governance with business-day reminder cadence
- Routes update requests by role and provides operational + executive visibility
- Supports scaling via standardization and optional provisioning

## End-to-end flow
Salesforce Opportunity stage change
-> Integration/Handshake (API/middleware)
-> Smartsheet Intake (Form or upsert)
-> Smartsheet System of Record (Master Tracker; one row per engagement)
-> Stage-gated workflow engine
-> Helper dates (business-day scheduling)
-> Automations (role-based routing; optional escalation)
-> Stakeholder updates (Sales/Delivery/Finance)
-> Loop back to System of Record

Outputs from System of Record:
- Dynamic View / WorkApps (role-based access)
- Operational reports (action queues)
- Executive dashboards (pipeline + aging)
Optional:
- Control Center (standardized provisioning)
- Data Shuttle / ODBC / API -> BI model / Power BI

## Mermaid diagram
```mermaid
flowchart TD
  A[Salesforce: Opportunity stage changes] --> B[Integration/Handshake (API/middleware)]
  B --> C[Smartsheet Intake (Form or upsert)]
  C --> D[Smartsheet System of Record (Master Tracker)]

  D --> E[Stage-gated workflow engine]
  E --> F[Helper dates (business-day)]
  F --> G[Automation (role routing)]
  G --> H[Stakeholder updates (Sales/Delivery/Finance)]
  H --> D

  D --> I[Dynamic View / WorkApps (role-based access)]
  D --> J[Operational reports (action queues)]
  J --> K[Executive dashboards (pipeline + aging)]

  D --> L[Control Center (optional provisioning)]
  D --> M[Data Shuttle / ODBC / API -> BI model (optional)]
```
