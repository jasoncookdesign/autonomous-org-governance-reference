# Policy Change Request — PCR-PGM-001

**Date:** 2026-06-05  
**Proposed by:** President Agent  
**Via:** President Agent (lead on INI-005)  
**Subject:** Policy Change Proposal — Governance Manual Amendment: § Policy Gap Management

> **Status:** Draft — not active policy. The President Agent must not behave as if this change is approved until the CEO explicitly approves and publishes it.

---

## Operational Problem

Policy gaps have historically been surfaced reactively — during a session when a specific gap causes friction or ambiguity. The President Agent escalates, the CEO addresses it, and the session continues. This works for individual gaps but creates two problems at scale:

1. **No backlog:** Gaps that don't block a specific task go untracked and accumulate silently.
2. **CEO review under pressure:** Gaps are presented in the middle of active work sessions, when the CEO's attention is on the task, not governance hygiene.

INI-005 introduced a Policy Gap Register (`governance/policy-gap-register.md`) as a systematic alternative. This PCR formalizes the register and the gap management workflow as Governance Manual policy.

---

## Current Policy Limitation

The Governance Manual § Policy Change Management (section 22) defines how changes are processed once identified, but does not define how gaps are identified, tracked, or prioritized before a formal PCR is filed. There is no backlog management policy.

---

## Proposed Change

Add a new section to the Governance Manual between § Policy Change Management and § Implementation Roadmap:

---

**§ Policy Gap Management**

**Purpose**

Policy gaps are situations where the organization operates without adequate written guidance: missing policy, ambiguous authority, unaddressed edge cases, or near-misses that revealed a structural weakness. The Policy Gap Register is the systematic backlog for these situations.

**The Register**

The Policy Gap Register is maintained at `governance/policy-gap-register.md`. It is a living document owned by the President Agent.

- Any director may surface a gap to the President Agent.
- The President Agent decides whether to register it.
- The President Agent is responsible for keeping the register current and presenting open gaps in the weekly governance summary.
- The Security Steward may flag gaps directly if they are security-relevant.

**Entry format**

Each entry follows the format defined in `templates/policy-gap-entry-template.md`. Required fields: ID, date, identified by, status, priority, domain, description, observed trigger, affected policy documents, risk if unaddressed, and proposed resolution.

**Lifecycle**

| Status | Meaning |
|---|---|
| Open | Identified; no active work yet |
| In Progress | A PCR has been filed or the CEO is actively reviewing |
| Resolved | Policy change approved and published; gap closed |
| Deferred | CEO has decided not to address at this time; reason documented |

**Prioritization**

The President Agent assigns priority at intake:

| Priority | Meaning |
|---|---|
| High | Creates immediate operational risk or leaves an active procedure without its required policy underpinning |
| Medium | Would cause friction or ambiguity in foreseeable scenarios within the current operating scope |
| Low | Worth tracking; not time-sensitive; may address in a future governance hygiene session |

**CEO review cadence**

The President Agent includes a gap register summary in every weekly governance summary:
- Count of open gaps by priority
- Any gaps that have changed status
- Any new gaps opened since the last summary
- A recommendation for which gap(s) to address next

This surfaces governance work to the CEO on a predictable schedule rather than reactively.

**Relationship to Policy Change Management**

The gap register is upstream of the PCR process. A gap entry is not a PCR; it is a problem statement. Once the CEO decides to address a gap, the President Agent files a formal PCR using `templates/policy-change-request-template.md`. The gap entry's status changes to In Progress when the PCR is filed.

**Relationship to Incident Reporting**

Near-miss incidents should be registered in both the incident log and the gap register. The incident log captures the event; the gap register captures the structural vulnerability the event revealed. Both are required for gaps surfaced by near-misses.

---

## Risk Created by Change

- **Overhead:** The President Agent must maintain an additional document and include gap summaries in weekly reports. This is modest overhead; the register is a markdown file and the summary is a brief table.
- **Gap inflation:** If every minor ambiguity is registered, the register becomes noise. Mitigated by: President Agent controls intake; Low-priority gaps are clearly separated from operational ones.

---

## Risk Reduced by Change

- **Silent accumulation:** Without a register, gaps that don't block active work go untracked indefinitely. Systematic tracking surfaces them before they cause incidents.
- **Reactive-only governance:** Without a backlog, the CEO addresses gaps only when they cause immediate friction. A register allows deliberate, scheduled governance hygiene rather than reactive policy patching.

---

## Recommended Authority Level

This amendment creates process obligations, not new execution authority. No new Level 6 grants.

---

## Recommended Maximum Acceptable Loss

N/A — this PCR creates process requirements, not new capabilities.

---

## Dependencies

- `governance/policy-gap-register.md` must be published for this section to be operationally meaningful
- `templates/policy-gap-entry-template.md` must be published for the entry format to be actionable

Both documents were created as part of INI-005 (2026-06-05).

---

*This is a proposal only. It is not active policy. The President Agent must not behave as if this change has been approved until the CEO explicitly approves and publishes it.*
