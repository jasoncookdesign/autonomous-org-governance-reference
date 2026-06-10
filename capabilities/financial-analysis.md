# Capability Policy: Financial Analysis

**Status:** Active  
**Version:** 1.0  
**Review date:** 2026-07-31  
**Authorized by:** CEO

## Purpose

Grants the Financial Analysis Director authority to analyze CEO-provided financial exports, build budget models, produce forecasts, and prepare financial summaries for internal use and the CEO's consulting clients. Grants no access to live financial systems of any kind. All financial data must arrive via an approved airlock channel.

## Maximum Authority Level

Level 4 — Draft for all financial outputs  
Level 6 — Execute for: reading airlock-cleared financial artifacts, writing analysis artifacts to the sandbox workspace, and routing artifacts to the KB inbox

No Level 6 authority for any live financial system access, external transmission, or spending.

## Allowed Roles

- Financial Analysis Director
- Temporary task agents authorized by the President Agent operating within Financial Analysis Director scope

## Allowed Systems

| System | Access Type | Conditions |
| --- | --- | --- |
| Sandbox workspace filesystem | Read / Write | Financial analysis artifacts only |
| KB inbox (`/knowledge/inbox/`) | Write (route artifacts) | Freely permitted |
| Anthropic API | Execute | Analysis and synthesis tasks; within wallet limits if applicable |

No external financial system access is permitted under this capability under any circumstances.

## Allowed Data Classes

- Class C (Sensitive) — all financial artifacts, without exception
- Class B (Internal) — non-sensitive budget models and forecasts not derived from personal financial data

Class D (Restricted) financial artifacts — e.g., unredacted full account credentials, government-issued identification numbers — may not be ingested. Escalate immediately if encountered.

## Approved Financial Artifact Types

The following export types are permitted once cleared through an approved airlock channel:

| Artifact Type | Notes |
| --- | --- |
| Bank account export (CSV, PDF) | CEO-exported from banking portal; not direct bank access |
| Credit / debit card statement export | CEO-exported; not direct portal access |
| Org wallet transaction logs | From provisioned privacy.com virtual cards |
| Budget spreadsheets | CEO-prepared or airlock-cleared |
| Invoice and receipt exports | Airlock-cleared |
| Payroll or contractor payment records | Airlock-cleared; handle with care |
| Venture revenue and expense reports | Airlock-cleared; assigned by President Agent |

Any artifact type not listed requires CEO approval before ingestion.

## Prohibited Systems

All live financial systems are prohibited without exception:

- Banking portals
- Brokerage and investment portals
- Tax filing systems
- Payment processors (direct API access)
- Accounting platform live connections (QuickBooks, Wave, etc.)
- CEO personal credit card accounts
- Any financial system requiring login or API credentials

## Retention Rules

All financial artifacts are Class C. The 180-day retention rule applies from the date of ingestion.

| Action | Requirement |
| --- | --- |
| Ingest financial artifact | Log ingestion date and classification immediately |
| Approach 180-day limit | Flag to Knowledge Director via President Agent before expiry |
| Retain beyond 180 days | CEO approval required |
| Delete any artifact | CEO approval required |

The Financial Analysis Director must maintain a retention log for all financial artifacts, recording ingestion date, artifact type, classification, and archival or deletion date.

## Venture Financial Analysis

Venture financial analysis is within scope. The Financial Analysis Director analyzes venture revenue, expense, and performance data on assignment from the President Agent. The Venture Director interprets outputs and makes portfolio decisions; analysis is performed here. All venture financial artifacts must arrive via an approved airlock channel and are classified Class C.

## Spending Limits

No spending authority associated with this capability at launch.

## Maximum Acceptable Loss

**Financial MAL:** $0 (no spending authority associated with this capability).

Information exposure risk: financial exports are Class C and must not be transmitted externally by the Financial Analysis Director. The CEO delivers all client-facing financial work personally. Unauthorized export or transmission of financial data is a High incident.

## Approval Thresholds

| Action | Approval Required |
| --- | --- |
| Analyze airlock-cleared financial artifact | None |
| Write analysis artifacts to sandbox workspace | None |
| Route artifacts to KB inbox | None |
| Ingest artifact type not on approved list | CEO approval |
| Retain financial artifact beyond 180 days | CEO approval |
| Delete any financial artifact | CEO approval |
| Transmit financial output externally | CEO approval — CEO performs transmission |
| Access any live financial system | Prohibited — not approvable under this capability |

## Audit Requirements

The Financial Analysis Director must log and report to President Agent:

- Financial artifacts ingested (artifact type, airlock manifest reference, ingestion date, classification)
- Analysis artifacts produced (title, type, client or purpose, classification)
- Retention log updates (ingestion dates, approaching limits, archival actions)
- Client-facing outputs completed and delivered to President Agent for CEO transmission
- Escalations raised
- Any artifact identified as potential Class D after ingestion
