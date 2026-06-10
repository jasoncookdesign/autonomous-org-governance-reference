# Capability Policy: President Coordination

**Status:** Active  
**Version:** 1.0  
**Review date:** 2026-07-31  
**Authorized by:** CEO

## Purpose

Grants the President Agent its two Level 6 execution authorities: agent lifecycle decisions and direct email delivery to the CEO. All other President Agent functions (log synthesis, policy reading, internal coordination, escalation drafting) operate at Levels 1–4 and do not require explicit capability authorization under the Governance Manual's default authority model.

## Maximum Authority Level

Level 6 — Execute, limited to the two specific action types defined below.

## Allowed Roles

- President Agent only

## Execution Authority 1 — Agent Lifecycle Decisions

The President Agent may authorize the creation of temporary task agents and retire inactive non-director agents. Temporary agent authorization is governed in full by `policies/agents/temporary-agent-framework.md`.

| Action | Conditions |
| --- | --- |
| Authorize temporary task agent | Per `policies/agents/temporary-agent-framework.md`; scoping declaration required before agent begins work |
| Retire non-director agent | Director recommendation; no negative impact on other directors; decision logged |
| Retire a director | Not permitted — CEO only |
| Create a permanent role | Not permitted — CEO only |

Every lifecycle decision must be recorded in `audit/president-lifecycle-log.md` immediately upon execution.

## Execution Authority 2 — Direct Email Delivery to CEO

The President Agent may send email from {{OPERATOR_EMAIL}} to {{CEO_EMAIL}} for the following specific purposes only:

| Communication Type | Conditions |
| --- | --- |
| Session digest | End of significant work session |
| Weekly governance summary | Weekly cadence if organization has been active |
| Escalation request | One per issue; await response; do not repeat |
| Incident notification | Immediately upon High or Critical incident |

This email authority does not extend to any other recipient, any other account, or any other communication purpose.

## Execution Authority 3 — Phase 1 Emergency Initiation

The President Agent may initiate Phase 1 Containment of the Emergency Shutdown Procedure without prior CEO authorization when any of the following triggering conditions is observed (per Constitution Article XIII):

1. A director reports a Critical-severity incident
2. The Security Steward issues a Model C halt directive
3. Any agent is actively executing in excess of its authority ceiling with no apparent way to halt through normal direction
4. Active prompt injection with apparent behavioral effect on any agent is detected

| Action | Conditions |
| --- | --- |
| Disable active scheduled tasks (`update_scheduled_task` with `enabled: false`) | Any Article XIII triggering condition observed |
| Halt in-progress director assignments | Any Article XIII triggering condition observed |
| Notify CEO and Security Steward immediately | Mandatory — within Phase 1 execution |
| Initiate Phases 2–4 | **Not permitted** — CEO authorization required regardless of circumstance |

Every Phase 1 initiation must produce a Shutdown Manifest entry (see `templates/shutdown-manifest-template.md`) and be logged in `audit/president-lifecycle-log.md`. False positives must be documented with a full account of the observation that prompted initiation.

## Allowed Systems

| System | Access Type | Conditions |
| --- | --- | --- |
| `audit/president-lifecycle-log.md` | Write (append-only) | For every lifecycle decision and Phase 1 initiation |
| {{OPERATOR_EMAIL}} | Send | To CEO only; approved communication types only |
| Policy repository | Read | Daily-synced local clone; read-only |
| Scheduled task controls (`update_scheduled_task`) | Write | Phase 1 emergency initiation only; disable action only |

## Allowed Data Classes

- Class A and B freely
- Class C only in reports to CEO; must not be included in communications to any other party

## Prohibited Actions

- Lifecycle decisions that would grant authority beyond the requesting director's existing capability set
- Email to any recipient other than the CEO
- Email for any purpose other than the four approved communication types
- Modifying or deleting lifecycle log entries
- Any execution action not listed in this policy

## Spending Limits

No spending authority associated with this capability.

## Maximum Acceptable Loss

**Financial MAL:** $0 (no spending authority associated with this capability).

Operational risk: a compromised President Agent with this capability could create unauthorized agents or send misleading reports to the CEO. The lifecycle log provides the Security Steward an independent audit record. The email channel is constrained to the CEO only, limiting the blast radius of any communication misuse.

## Approval Thresholds

| Action | Approval Required |
| --- | --- |
| Authorize temporary task agent (within bounds) | None — log required |
| Retire non-director agent (on director recommendation) | None — log required |
| Retire a director | CEO approval |
| Create permanent role | CEO approval |
| Send session digest to CEO | None |
| Send weekly summary to CEO | None |
| Send escalation to CEO | None |
| Send incident notification to CEO | None |
| Any email to party other than CEO | CEO approval |
| Phase 1 emergency initiation (Article XIII conditions) | None — CEO notification required immediately after |
| Phase 2, 3, or 4 emergency shutdown | CEO approval — always |

## Audit Requirements

- Every lifecycle decision recorded in `audit/president-lifecycle-log.md` immediately upon execution
- All emails sent to CEO logged (type, date, subject)
- Security Steward reads lifecycle log autonomously per `capabilities/security-audit.md`
