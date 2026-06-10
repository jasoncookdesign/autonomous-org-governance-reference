# Knowledge Policy

**Version:** 1.0  
**Status:** Active  
**Edit authority:** CEO only

## Purpose

Governs the ingestion, classification, organization, retention, archival, deletion, and export of content within the sandbox knowledge base. The KB is a sensitive organizational asset and must be treated accordingly.

## KB as an Asset

The knowledge base may contain financial exports, career and project history, creative plans, business strategy, technical work, and personal operational context. As the sandbox becomes more useful, its KB becomes more sensitive. Governance must keep pace with content accumulation.

## Ingestion Rules

1. Only artifacts that have cleared an approved airlock channel may be ingested into the KB.
2. The airlock manifest produced by the Operations Director is the record of approval.
3. An artifact without a corresponding manifest may not be ingested. The Knowledge Director must escalate to the CEO.
4. Class D (Restricted) content may not be ingested under any circumstances. If Class D content is discovered in an artifact already routed to the KB inbox, escalate immediately.

## Classification Standards

All KB content inherits the data classification of the artifact from which it derives. When an artifact contains mixed-class content, the highest applicable class governs the entire artifact.

| Class | Name | Examples | Handling |
| --- | --- | --- | --- |
| A | Public | Public research, public documentation, event listings | No retention limit; freely usable within KB |
| B | Internal | Project notes, non-sensitive drafts, sanitized planning docs | No retention limit; use within approved workflows |
| C | Sensitive | Financial exports, contracts, private strategy, personal logistics | 180-day retention; archive after; deletion requires CEO approval |
| D | Restricted | Banking portals, identity providers, personal credentials | Not permitted in KB; escalate if encountered |

## Retention Policy

| Class | Retention Limit | Action |
| --- | --- | --- |
| A | None | Retain indefinitely |
| B | None | Retain indefinitely |
| C | 180 days from ingestion date | Archive autonomously at limit; deletion requires CEO approval |
| D | N/A | Not permitted; escalate |

The Knowledge Director maintains a retention log for all Class C artifacts including ingestion date, classification, and archival date.

Archival moves content from the active KB to `/knowledge/archive/`. Archived content is retained but not surfaced in routine retrieval. Deletion of archived content requires CEO approval.

## Inbox Management

`/knowledge/inbox/` is the staging zone for all newly arrived airlock artifacts. Content in this directory is not considered part of the KB until classified and routed by the Knowledge Director.

- Inbox items must be classified and routed within 48 hours of arrival
- Items unclassified after 48 hours must be escalated to the CEO
- The Knowledge Director may not use inbox content in analysis or recommendations until it has been classified and routed

## Organization and Cross-Referencing

- The KB master index (`knowledge/index.md`) must accurately reflect all KB content
- `[[wiki link]]` format is the approved cross-referencing convention
- Tags may be applied using a consistent taxonomy documented in `knowledge/index.md`
- The Knowledge Director updates the index whenever content is added, moved, or archived

## Export Rules

KB content may not be transmitted to external services, accounts, or recipients without explicit CEO approval. This includes:

- Pasting KB content into external tools
- Uploading KB content to external platforms
- Including KB content in external communications
- Syncing KB content to unauthorized cloud storage

The approved Google Drive mirror (CEO read access) is the only approved outbound sync for KB content and is limited to the designated digital organization folder.

## Access Rules

| Role | KB Access |
| --- | --- |
| Knowledge Director | Full read/write within scope of this policy |
| Engineering Director | Read access to `/knowledge/technology/` and `/knowledge/projects/` within workflow scope |
| Operations Director | Read access to `/knowledge/operations/` within workflow scope |
| Security Steward | Read access to KB for audit purposes |
| President Agent | Read access to KB index; directed reads on request |
| Inactive roles | No access until activated |

Cross-section access beyond the above requires CEO approval.

## Backup

KB backup is handled at the operating system level and is not the organization's direct responsibility. The Google Drive mirror provides a secondary copy for CEO access. The organization must not create additional copies of KB content in unapproved locations.

## Policy Change

Changes to classification standards, retention limits, ingestion rules, or access rules require CEO approval and a policy update. The Knowledge Director may recommend changes through the policy change process.
