# Reporting Policy

**Version:** 1.0  
**Status:** Active  
**Edit authority:** CEO only

## Purpose

Defines the required reports, their contents, cadence, delivery mechanism, and format. Reporting is how the CEO maintains visibility into the organization without reading raw logs.

## Report Delivery

All reports are delivered by email from {{OPERATOR_EMAIL}} to {{CEO_EMAIL}}. Reports must be clearly labeled with type and date in the subject line. No report may be sent to any external party.

## Report Types

### Session Digest

**Trigger:** End of any significant work session  
**Prepared by:** President Agent (synthesized from director summaries)  
**Delivered by:** President Agent (via {{OPERATOR_EMAIL}})

**Required contents:**
- Summary of goals addressed in the session
- Work completed by each active director
- Files created or significantly modified
- External sites or services accessed
- Money spent (wallet, vendor, amount, purpose)
- External communications drafted or sent
- Escalations raised during the session
- Policy gaps identified
- Temporary agent authorizations or retirements
- Items pending CEO decision

**Format:** Concise. Organized by section. No raw logs. CEO should be able to read and act in under five minutes.

---

### Weekly Governance Summary

**Trigger:** Weekly, if the organization has been active  
**Prepared by:** President Agent (synthesized from director summaries and Security Steward compliance summary)  
**Delivered by:** President Agent (via {{OPERATOR_EMAIL}})

**Required contents:**
- Open policy gaps and their age
- Open escalations awaiting CEO response
- Incidents from the past week (summary with links to full reports)
- New capability requests or role change proposals
- Capability review dates approaching
- Wallet spending summary for the week
- Compliance findings from Security Steward
- Recommended CEO decisions (grouped, with clear options)

**Format:** Structured. Actionable. CEO should be able to process approvals as a batch.

---

### Capability Audit Report

**Trigger:** Per capability review date, or monthly if no review date is set  
**Prepared by:** Security Steward  
**Delivered via:** President Agent weekly summary or standalone email if time-sensitive

**Required contents (per capability):**
- Capability name and version
- Period covered
- Actions taken under this capability
- Spending (if applicable)
- Exceptions or policy deviations
- Estimated loss exposure
- Compliance observations
- Recommendations (changes, renewals, retirements)

---

### Incident Report

**Trigger:** Immediately upon High or Critical incident; within 24 hours for Medium  
**Prepared by:** Security Steward  
**Delivered by:** President Agent to CEO (immediately for High/Critical)

See `templates/incident-report-template.md` for required format.

Low-severity items are logged and included in the next Weekly Governance Summary rather than triggering a standalone report.

## Escalation Protocol

When an agent cannot proceed due to missing authority, policy conflict, or required CEO decision:

1. Prepare an escalation request using `templates/escalation-request-template.md`
2. President Agent delivers via email ({{OPERATOR_EMAIL}} → {{CEO_EMAIL}})
3. Email once per issue; await response
4. Do not repeat the escalation unless CEO requests it or a new material development occurs
5. If the CEO does not respond, the agent holds in a safe state — it does not proceed or improvise

## Reporting Cadence Summary

| Report | Cadence | Trigger | Prepared By |
| --- | --- | --- | --- |
| Session Digest | Per significant session | Session end | President Agent |
| Weekly Governance Summary | Weekly (if active) | Calendar | President Agent |
| Capability Audit | Monthly or per review date | Schedule | Security Steward |
| Incident Report (High/Critical) | Immediately | Incident detection | Security Steward |
| Incident Report (Medium) | Within 24 hours | Incident detection | Security Steward |
| Escalation Request | As needed | Authority gap | Requesting role |
