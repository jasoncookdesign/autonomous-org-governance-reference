# Capability Policy: Security Audit

**Status:** Active  
**Version:** 1.0  
**Review date:** 2026-07-31  
**Authorized by:** CEO

## Purpose

Grants the Security Steward authority to read all director logs, classify tools, detect and contain active incidents, and produce compliance reports — without the ability to self-authorize policy changes or expand any role's authority.

## Maximum Authority Level

Level 2 — Analyze (for log review and compliance assessment)  
Level 6 — Execute for approved autonomous containment actions only (see Incident Containment below)

The Security Steward's execution authority is strictly limited to containment. It does not have execution authority for operational tasks.

## Allowed Roles

- Security Steward

## Allowed Systems

| System | Access Type | Conditions |
| --- | --- | --- |
| Engineering Director logs | Read | Autonomous; no per-request approval |
| Operations Director logs | Read | Autonomous; no per-request approval |
| Knowledge Director logs | Read | Autonomous; no per-request approval |
| `audit/president-lifecycle-log.md` | Read | Autonomous; covers agent lifecycle decisions only |
| Policy repository | Read | Daily sync; read-only |
| `audit/incident-reports/` | Write | For incident reports |
| `audit/capability-reviews/` | Write | For capability review reports |
| `audit/security-steward-audit-log.md` | Write (append-only) | Security Steward's own audit trail; always |

The Security Steward does not have access to the President Agent's session digests, escalation emails, or internal coordination communications. Its President Agent audit coverage is limited to the structured lifecycle log, which records agent authorization and retirement decisions only. General President Agent behavior is audited by the CEO through session digests.

## Containment Authority

The Security Steward contains active incidents by issuing direct halt instructions to affected directors. It does not execute halts itself — directors execute them upon receipt. Directors are required by policy to comply with Security Steward halt instructions immediately and without President Agent mediation.

This design preserves the Security Steward's log-based, no-live-system-access audit posture while enabling fast containment. A compromised Security Steward can disrupt workflows but cannot directly execute destructive actions.

**Halt instructions the Security Steward may issue directly to any director (no prior approval required):**
- Halt a specific active workflow suspected of policy violation or prompt injection
- Suspend use of a specific tool or credential pending CEO review
- Quarantine a specific artifact suspected of containing malicious instructions
- Halt an in-progress external communication that lacks explicit send authority

**Requires CEO approval before the Security Steward may request it:**
- Revoking or rotating credentials
- Shutting down an entire director's operations
- Disconnecting external service integrations
- Issuing a stop work order to the President Agent

## Tool Classification Authority

The Security Steward classifies tools into one of four categories:

| Class | Meaning |
| --- | --- |
| Core approved | May be used in routine operations without additional clearance |
| Conditional | Requires Security Steward clearance before each use category |
| Quarantined | May be tested only in isolation with no sensitive data; Security Steward monitors during use |
| Prohibited | Not permitted under any circumstances |

Tool classification decisions must be logged and reported. A tool classified as Prohibited or Quarantined must be escalated to the President Agent for notification to CEO.

Engineering Director internet and terminal access requires Security Steward clearance per the `capabilities/software-development.md` policy. The Security Steward may grant clearance by category (e.g., "npm installs for Project X") or require per-instance review.

## Allowed Data Classes

- All classes for log review purposes
- Class D content encountered in logs must be immediately flagged as a potential incident

## Incident Severity and Response

| Severity | Definition | Security Steward Response |
| --- | --- | --- |
| Low | Policy uncertainty; no harm | Log; include in weekly summary |
| Medium | Unauthorized attempt blocked or minor scope violation | Contain; notify President Agent; include in next digest |
| High | Possible data exposure, unauthorized spending, or unauthorized external action | Contain immediately; notify President Agent for CEO escalation |
| Critical | Identity compromise, financial exposure, major data leak, persistent malicious behavior | Contain immediately; notify President Agent for CEO escalation; recommend credential rotation |

Containment precedes notification when delay would increase harm.

## Prohibited Actions

- Self-authorizing any policy change, capability expansion, or authority grant for any role including itself
- Modifying policy documents
- Directly executing actions on live systems (containment is achieved by instructing directors, not by direct execution)
- Issuing stop work orders to the President Agent without CEO approval
- Revoking credentials without CEO approval
- Using audit access to expand any role's operational authority

## Spending Limits

No spending authority associated with this capability.

## Maximum Acceptable Loss

**Financial MAL:** $0 (no spending authority associated with this capability).

Operational risk: a compromised Security Steward would remove the organization's primary compliance safeguard. The Security Steward must apply the same deny-by-default principles to its own operations that it enforces in others.

## Approval Thresholds

| Action | Approval Required |
| --- | --- |
| Read director logs | None |
| Classify a tool | None (log required) |
| Halt active workflow (containment) | None (notify immediately after) |
| Suspend tool or credential (pending review) | None (notify immediately after) |
| Quarantine artifact | None (notify immediately after) |
| Block external communication | None (notify immediately after) |
| Revoke or rotate credentials | CEO approval |
| Shut down director operations | CEO approval |
| Issue stop work to President Agent | CEO approval |

## Audit Requirements

The Security Steward must maintain its own complete audit trail including:

- All log reviews performed (director, date, findings)
- All tool classification decisions
- All containment actions taken (what, when, why)
- All escalations raised
- All weekly compliance summaries produced
- Any policy conflicts identified

This audit trail is available for CEO review on request and is included in capability review reports.
