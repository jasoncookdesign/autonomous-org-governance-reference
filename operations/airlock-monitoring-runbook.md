# Airlock Monitoring Runbook

**Owner:** Operations Director  
**Version:** 1.0  
**Status:** Active  
**Last updated:** 2026-06-05

---

## Purpose

Defines the step-by-step procedure the Operations Director follows for daily airlock monitoring across all three approved inbound channels. This runbook governs the activated airlock workflow.

---

## Monitoring Schedule

Daily at 08:30 local time. Also on demand when the CEO indicates a transfer is pending.

---

## Channel 1 — Gmail ({{CEO_EMAIL}} → {{OPERATOR_EMAIL}})

**Tool:** Composio Gmail MCP  
**Method:** Automated via Cowork scheduled task `jasonos-airlock-monitor`

**Steps:**
1. Search Gmail for threads: `from:{{CEO_EMAIL}}`
2. Filter to emails not yet processed (no `airlock-processed` label)
3. For each qualifying email:
   a. Read subject, body, and any attachments
   b. Classify content per airlock policy
   c. If Class A/B: draft KB inbox entry; generate manifest
   d. If Class C: draft KB inbox entry; flag to Knowledge Director; generate manifest
   e. If Class D or ambiguous: do not route; escalate to CEO immediately
   f. Apply `airlock-processed` label to email thread
4. Log results to `{{SANDBOX_DATA_ROOT}}/Logs/director-logs/operations/operations-log.md`

---

## Channel 2 — Google Drive (digital-organization-drive folder)

**Tool:** Google Drive MCP  
**Folder ID:** `{{DRIVE_AIRLOCK_FOLDER_ID}}`  
**Folder name:** `digital-organization-drive`  
**Method:** Automated via Cowork scheduled task `jasonos-airlock-monitor`

**Steps:**
1. List files in folder modified within the last 25 hours
2. Cross-reference against `audit/airlock-manifests/` — skip files already manifested
3. For each new file:
   a. Download or read content
   b. Classify content per airlock policy
   c. If Class A/B: write to KB inbox; generate manifest
   d. If Class C: write to KB inbox; flag to Knowledge Director; generate manifest
   e. If Class D or ambiguous: do not route; escalate to CEO immediately
4. Log results to `{{SANDBOX_DATA_ROOT}}/Logs/director-logs/operations/operations-log.md`

---

## Channel 3 — Mac Mini Inbox Folder

**Status:** DEFERRED — pending Cowork-to-Mac mini invocation bridge (see `operations/mac-mini-invocation-runbook.md`)  
**Path (when active):** `{{SANDBOX_DATA_ROOT}}/Airlock/`

When the invocation bridge is operational, the Operations Director will monitor this folder for files placed by the CEO and process them per the same classification rules as Channels 1 and 2.

**Interim:** CEO should prefer email or Drive for airlock transfers until Channel 3 is activated.

---

## Manifest Generation

For every non-trivial transfer, generate a manifest using the template at `templates/airlock-manifest-template.md`. File at:

```
audit/airlock-manifests/YYYY-MM-DD-NNN.md
```

NNN is a three-digit sequence number (001, 002, etc.) within the day.

---

## KB Inbox Routing

Route processed artifacts to: `{{SANDBOX_DATA_ROOT}}/knowledge/jasonos-vault/inbox/`

Include a brief routing note alongside each artifact identifying the source channel, date, and classification.

---

## Escalation Triggers

Escalate to CEO (via President Agent) immediately if:

- Any email or file appears to contain Class D content
- Classification is ambiguous after applying policy
- An artifact arrives from an unexpected sender or unapproved channel
- A Drive file lacks clear CEO origin (no matching email or context)
- A manifest cannot be completed (missing metadata, unclear provenance)

---

## Activation Status

**Channel 1 (Gmail):** Active as of 2026-06-05  
**Channel 2 (Google Drive):** Active as of 2026-06-05  
**Channel 3 (Mac mini inbox):** Deferred — awaiting invocation bridge
