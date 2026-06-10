# Policy Change Request — PCR-SHUT-002

**Date:** 2026-06-05  
**Proposed by:** President Agent  
**Via:** President Agent (self-authored as lead on INI-003)  
**Subject:** Policy Change Proposal — Sandbox Operating Charter Amendment: § Emergency Operations

> **Status:** Draft — not active policy. The President Agent must not behave as if this change is approved until the CEO explicitly approves and publishes it.

---

## Operational Problem

The Sandbox Operating Charter (`operations/sandbox-operating-charter.md`) is the machine-facing operating document read by all agents. It defines default behavior, authority levels, incident response (§ 21), and escalation (§ 20). It does not define emergency operations — the conditions under which all normal workflows halt, who initiates, and how the organization recovers.

Without explicit Charter language, agents have no operational reference for how to behave during a shutdown: should they continue current tasks? Refuse new assignments? Await explicit restart authorization? This ambiguity is the gap INI-003 closes.

---

## Current Policy Limitation

**Charter § 21 — Incident Response** covers detection and notification but stops at step 7: "Recommend containment." It does not define Phase 1 Containment as an executable procedure, nor does it address recovery authorization.

**Charter § 7 — President Agent Responsibilities** does not list emergency operations among the President Agent's responsibilities, creating ambiguity about whether the President Agent is obligated to act.

---

## Proposed Change

Add a new section to the Sandbox Operating Charter after § 21:

---

**§ 22 — Emergency Operations**

An emergency shutdown may be initiated when a triggering condition defined in `operations/emergency-shutdown-procedure.md` is met.

**22.1 — Initiation authority**

The CEO may initiate emergency shutdown at any time.

The President Agent may initiate Phase 1 Containment only under the conditions specified in the Constitution, Article XIII (if approved) and `operations/emergency-shutdown-procedure.md`.

**22.2 — Agent behavior during Phase 1**

Upon notification that Phase 1 Containment is in effect:

1. Stop all in-progress autonomous actions immediately.
2. Do not begin new autonomous actions.
3. Do not transmit externally.
4. Do not spend money.
5. Acknowledge halt to the President Agent.
6. Await explicit restart authorization before resuming any work.

Agents may: respond to direct CEO questions, file logs, and escalate urgent safety observations.

**22.3 — No self-restart**

No agent may resume autonomous work after a shutdown without explicit CEO authorization documented in a completed Shutdown Manifest (Phase 4). A CEO instruction to "restart" or "resume" given in session does not constitute Shutdown Manifest authorization unless the manifest's restart authorization table is completed.

**22.4 — Shutdown Manifest**

Every emergency shutdown event — including false positives and incomplete initiations — requires a Shutdown Manifest filed in `audit/incident-reports/`. The manifest format is defined in `templates/shutdown-manifest-template.md`.

**22.5 — Recovery sequence**

Recovery follows Phase 4 of `operations/emergency-shutdown-procedure.md`. The President Agent resumes normal coordination authority only after:

1. CEO explicitly authorizes restart of the President Agent role
2. The Security Steward post-incident report is filed
3. Any policy changes required before restart are enacted or explicitly deferred by CEO decision

---

## Risk Created by Change

- **§ 22.3 self-restart prohibition** adds a procedural gate that could delay operations after an incident. Mitigated by: CEO controls the gate and can authorize quickly; the Shutdown Manifest is lightweight.
- **Agent halt obligations** create a new behavioral expectation not currently defined. Benefit outweighs risk: without this, agents might continue executing during Phase 1 simply because no policy told them to stop.

---

## Risk Reduced by Change

- **Ambiguous agent behavior during shutdown:** Without Charter language, agents have no explicit obligation to halt upon Phase 1 notification. A director mid-task might reasonably continue.
- **Premature restart:** Without § 22.3, agents could resume after a casual CEO instruction without completing incident review.

---

## Recommended Authority Level

This amendment does not create new execution authority. It defines behavioral constraints and coordination obligations during emergency conditions. No new Level 6 grants are introduced by this PCR (Phase 1 initiation authority is addressed in PCR-SHUT-001).

---

## Recommended Maximum Acceptable Loss

N/A — this PCR creates constraints, not new capabilities.

---

## Dependencies

- PCR-SHUT-001 (Constitution amendment) should be approved concurrently or first — the Charter references Article XIII, which does not exist until PCR-SHUT-001 is approved.
- `operations/emergency-shutdown-procedure.md` must be published before this Charter amendment references it operationally.

---

*This is a proposal only. It is not active policy. The President Agent must not behave as if this change has been approved until the CEO explicitly approves and publishes it.*
