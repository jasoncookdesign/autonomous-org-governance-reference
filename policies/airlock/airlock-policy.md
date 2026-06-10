# Airlock Policy

**Version:** 1.0  
**Status:** Active  
**Edit authority:** CEO only

## Purpose

Defines the approved transfer procedures, approved channels, manifest requirements, and classification rules for moving information from the clean side to the sandbox. The airlock is the primary control point for preventing unauthorized data from entering the sandbox and for ensuring that sensitive content is handled appropriately from the moment it arrives.

## Approved Inbound Channels

Only the following channels are approved for transferring information into the sandbox. Any artifact arriving via another channel must be rejected and escalated.

| Channel | Source | Direction | Notes |
| --- | --- | --- | --- |
| Google Drive folder | CEO's designated digital organization folder | Inbound only | CEO places file; Operations Director retrieves |
| Mac mini inbox folder | Designated filesystem path on the Mac mini | Inbound only | CEO places file; Operations Director retrieves |
| Email | From {{CEO_EMAIL}} to {{OPERATOR_EMAIL}} | Inbound only | Operations Director monitors {{SYSTEM_USER}} inbox |

No outbound airlock channel is defined at launch. Establishing an outbound channel requires CEO approval and a policy update.

## Approved Transfer Formats

Preferred formats for incoming artifacts:

- Markdown (.md)
- Plain text (.txt)
- CSV (.csv)
- JSON (.json)
- Sanitized PDF (.pdf — comments, revision history, and hidden metadata removed)
- Transcripts (text-based)
- Metadata-stripped images (EXIF and sensitive visual detail removed)
- Sanitized audio derivatives (transcript preferred; raw audio only when sonic qualities are required)
- Repository clone or branch with secrets removed

Formats requiring special handling before use:

- Spreadsheets: must be exported to CSV or JSON before ingestion into the KB; native spreadsheet files may be stored but should not be treated as authoritative without export confirmation
- Design files: rendered PNG or PDF preferred; source files only when editing is required and the CEO has approved

Formats not preferred (require justification):

- Raw HTML with active scripts
- Executable files
- Full-resolution video (use transcript, keyframes, or low-resolution preview)
- Files with unremoved metadata

## Manifest Requirements

A manifest is required for every non-trivial airlock transfer. Trivial transfers (single plain-text file, clearly Class A) may use a simplified manifest. The Operations Director defines and maintains the manifest format in `templates/airlock-manifest-template.md`.

Every manifest must record:

- Transfer date and time
- Source channel
- Source file name, type, and approximate size
- Apparent data classification (A, B, C, or escalate)
- Sanitization steps performed
- Destination (KB inbox, director workspace, or specific subfolder)
- Retention expectation based on classification
- Any anomalies noted during transfer

Manifests are stored in `audit/airlock-manifests/`.

## Classification on Receipt

The Operations Director performs initial classification at the time of transfer:

| If artifact appears to be... | Action |
| --- | --- |
| Class A — Public | Route to KB inbox with manifest |
| Class B — Internal | Route to KB inbox with manifest |
| Class C — Sensitive | Route to KB inbox with manifest; flag to Knowledge Director |
| Class D — Restricted | Do not transfer; escalate to CEO immediately |
| Classification ambiguous | Classify upward; escalate to CEO before routing |

When in doubt, classify upward. An artifact classified too high causes inconvenience. An artifact classified too low could cause a security incident.

## Sanitization Requirements

Before transfer, the CEO should ensure:

- Documents: comments, revision history, and embedded metadata removed
- Images: EXIF data and sensitive visual details removed
- Spreadsheets: formulas documented separately if needed; cell-level data only in CSV/JSON
- Code: secrets, credentials, and environment-specific values removed or replaced with examples
- Email: headers and recipient lists trimmed to the minimum necessary for the task

The Operations Director may not perform sanitization on behalf of the CEO. Sanitization is a clean-side responsibility before transfer.

If an artifact arrives with apparent unsanitized sensitive content (e.g., credentials visible in a document), the Operations Director must flag it to the CEO before routing.

## Rejected Artifacts

Artifacts rejected due to unapproved channel, classification concern, or sanitization issue must:

1. Not be routed into the KB or any director workspace
2. Be logged with the reason for rejection
3. Be reported to the President Agent
4. Result in a CEO notification if the rejection involves apparent Class D content or a suspected policy violation

## Policy Change

Changes to approved channels, formats, or classification rules require CEO approval and a policy update. The Operations Director may recommend changes through the policy change process but may not enact them.
