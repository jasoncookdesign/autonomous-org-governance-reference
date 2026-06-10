# Capability Policy: Airlock Operations

**Status:** Active  
**Version:** 1.0  
**Review date:** 2026-07-31  
**Authorized by:** CEO

## Purpose

Grants the Operations Director authority to execute approved airlock transfers, generate transfer manifests, classify incoming artifacts, and route them to the appropriate KB inbox or director workspace.

## Maximum Authority Level

Level 6 — Execute, within the constraints defined in this policy.

## Allowed Roles

- Operations Director
- Temporary task agents authorized by President Agent operating within Operations Director scope

## Approved Airlock Channels

| Channel | Source | Direction | Trigger |
| --- | --- | --- | --- |
| Google Drive folder | CEO's designated digital organization folder | Inbound only | CEO places file; Operations Director retrieves |
| Mac mini inbox folder | Designated filesystem path on the Mac mini | Inbound only | CEO places file; Operations Director retrieves |
| Email | From {{CEO_EMAIL}} to {{OPERATOR_EMAIL}} | Inbound only | Operations Director monitors {{OPERATOR_EMAIL}} inbox |

No other channel is an approved airlock source. Outbound airlock (sandbox to clean side) is not defined at launch and requires CEO approval before any such flow is established.

## Transfer Execution Rules

1. The Operations Director may execute transfers from approved channels without per-transfer CEO approval, provided the artifact source and category match pre-approved patterns.
2. A manifest must be generated for every non-trivial transfer (see manifest requirements below).
3. Artifacts must be routed to the KB inbox (`knowledge/inbox/`) or the appropriate director's workspace, not stored in ad-hoc locations.
4. Artifacts containing apparent Class C or D content must be flagged before routing, not ingested silently.
5. Artifacts arriving via any unapproved channel must be rejected and reported to President Agent and CEO.

## Manifest Requirements

Every non-trivial airlock transfer must produce a manifest documenting:

- Source channel
- Source file name and type
- Transfer date and time
- Apparent data classification (A, B, C, or ambiguous/escalate)
- Sanitization performed (if any)
- Destination (KB inbox, director workspace)
- Retention expectation based on classification
- Notes on any anomalies

Manifests are stored in `audit/airlock-manifests/` (directory to be created by Operations Director on first use).

## Artifact Classification on Receipt

The Operations Director performs initial classification:

| If artifact appears to be... | Action |
| --- | --- |
| Class A or B | Route to KB inbox with manifest |
| Class C | Route to KB inbox with manifest; flag to Knowledge Director for careful handling |
| Class D | Do not ingest; escalate to CEO immediately |
| Classification ambiguous | Classify upward; escalate to CEO before routing |

## Allowed Systems

- Google Drive (designated digital organization folder — read access for inbound transfers)
- Mac mini inbox filesystem folder
- {{OPERATOR_EMAIL}} email inbox (monitoring for CEO-sourced transfers)
- KB inbox folder (`knowledge/inbox/`)
- Director workspace folders (for routing)
- `audit/airlock-manifests/` (manifest storage)

## Prohibited Systems

- CEO personal email ({{CEO_EMAIL}}) — inbound detection only via {{SYSTEM_USER}} inbox
- CEO personal Google Drive (only the designated digital organization folder is approved)
- Any system not listed above
- Outbound transfers to any external destination

## Allowed Data Classes

- Class A, B, C inbound (with classification-appropriate handling)
- Class D: no ingest; escalate only

## Spending Limits

No spending authority associated with this capability.

## Maximum Acceptable Loss

**Financial MAL:** $0 (no spending authority associated with this capability).

Information exposure risk: an airlock failure could result in Class C content being misrouted or Class D content entering the sandbox. Both are incidents requiring immediate escalation. The Operations Director must design transfer workflows to minimize this risk.

## Approval Thresholds

| Action | Approval Required |
| --- | --- |
| Transfer from approved channel, approved artifact type | None (manifest required) |
| Transfer of apparent Class C artifact | None, but flag to Knowledge Director |
| Transfer of apparent Class D artifact | CEO approval required; do not transfer |
| Establishing a new airlock channel | CEO approval required |
| Outbound transfer (sandbox to clean side) | CEO approval required; no such flow defined at launch |

## Audit Requirements

The Operations Director must log and report to President Agent:

- All transfers executed (manifest reference)
- All artifacts rejected (source, reason)
- All escalations raised for classification uncertainty
- Policy sync execution results (daily)
- Any channel anomalies (unexpected sender, unexpected format, suspected manipulation)

## Policy Repository Sync

This capability also covers the Operations Director's responsibility for the daily policy sync:

1. Pull policy repository from GitHub daily
2. Compare local files to GitHub source
3. If unauthorized local modifications are detected:
   - Treat as security incident
   - Generate diff
   - Overwrite with GitHub version
   - Log and notify Security Steward and President Agent immediately
4. Pull on demand when ordered by CEO
