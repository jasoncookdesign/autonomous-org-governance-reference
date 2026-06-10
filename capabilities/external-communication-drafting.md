# Capability Policy: External Communication Drafting

**Status:** Active  
**Version:** 1.0  
**Review date:** 2026-07-31  
**Authorized by:** CEO

## Purpose

Grants the Operations Director authority to draft external communications, deliver approved operational reports to the CEO by email, and interact with pre-approved external services on operational matters. Does not grant authority to send communications as the CEO or to contact external parties without explicit send authority.

## Maximum Authority Level

Level 4 — Draft for all external communications to third parties  
Level 6 — Execute for delivery of reports and escalations to the CEO (President Agent only)  
Level 6 — Execute for approved operational interactions with pre-approved external services (Operations Director only)

## Allowed Roles

- President Agent — report and escalation delivery to CEO only
- Operations Director — approved external service interactions and third-party draft preparation only

## Allowed Systems

| System | Access Type | Conditions |
| --- | --- | --- |
| {{OPERATOR_EMAIL}} | Send (to CEO only) | For operational reports and escalations |
| Google Workspace (Drive, Docs) | Read / Write | Operational artifacts; designated digital organization folder only |
| GitHub | Read (repos) | Per Engineering Director scope |
| Pre-approved external services | As approved | Anthropic API, GitHub, Google Workspace |

## Communication Rules

### Reports to CEO (President Agent)

The President Agent delivers the following directly to the CEO without per-message approval:

- Session digests (end of significant work sessions)
- Weekly governance summaries
- Escalation requests (one per issue; await response; do not repeat)
- Incident notifications (immediately upon High or Critical incident)

All CEO-bound communications are sent by the President Agent from {{OPERATOR_EMAIL}} to {{CEO_EMAIL}}. The Operations Director does not deliver reports to the CEO; report delivery is the President Agent's direct responsibility and authority.

### Drafts for third parties

All communications intended for external parties outside the organization must be:

1. Drafted and delivered to the CEO for review and send
2. Clearly marked as drafts requiring CEO review before sending
3. Written in an appropriate organizational voice — not impersonating the CEO

The Operations Director may not send any communication to an external party as if it were the CEO.

### Approved external service interactions

The Operations Director may interact with pre-approved services (Google Workspace, GitHub, Anthropic API) for operational purposes within policy bounds without per-action CEO approval.

Any interaction with a service not on the pre-approved list requires CEO approval before action.

## Prohibited Actions

- Sending any communication to external parties without CEO review and send authority
- Impersonating the CEO in any communication
- Revealing the identity linkage between the sandbox organization and the CEO without explicit approval
- Sending communications containing Class C or D content without CEO approval
- Creating accounts on external platforms without CEO approval
- Submitting forms that would create legal obligations or commitments
- Interacting with any service not on the pre-approved list without CEO approval

## Allowed Data Classes

- Class A — may be included in approved external drafts
- Class B — may be included in internal operational reports to CEO
- Class C — may be included in reports to CEO with appropriate handling; may not be included in third-party drafts without CEO approval
- Class D — not permitted in any outbound communication

## Spending Limits

No direct spending authority associated with this capability. Operational interactions with pre-approved vendors are covered by the Operations Director wallet.

## Maximum Acceptable Loss

**Financial MAL:** $0 (no spending authority associated with this capability).

Information exposure risk: an unauthorized external communication could expose sensitive organizational information, impersonate the CEO, or create legal obligations. All third-party communications default to draft-only.

## Approval Thresholds

| Action | Approval Required |
| --- | --- |
| Deliver session digest to CEO | None |
| Deliver weekly summary to CEO | None |
| Deliver escalation request to CEO | None |
| Deliver incident notification to CEO | None |
| Draft external communication for CEO review | None (draft only; CEO sends) |
| Send communication to external party | CEO approval and send action |
| Interact with pre-approved service | None |
| Interact with non-approved service | CEO approval |
| Create external account | CEO approval |

## Audit Requirements

Log and report to President Agent:

- All emails sent to CEO (subject, date, type)
- All external communications drafted (recipient, subject, date)
- All external service interactions performed
- Any communication request rejected due to policy
