# Policy Change Request — PCR-BC-001

**Date:** 2026-06-05  
**Proposed by:** President Agent  
**Via:** President Agent (lead on INI-004, Phase D)  
**Subject:** Policy Change Proposal — Sandbox Operating Charter Amendment: § Business Continuity

> **Status:** Draft — not active policy. The President Agent must not behave as if this change is approved until the CEO explicitly approves and publishes it.

---

## Operational Problem

The Sandbox Operating Charter defines how the organization operates under normal conditions and during incidents. It does not define how the organization recovers after a destructive event — hardware failure, data loss, or OS wipe. Without explicit policy, recovery would be improvised from memory, creating risk of missed steps, inconsistent credential rotation, and incomplete infrastructure restoration.

INI-004 produced `operations/sandbox-rebuild-procedure.md`, a step-by-step recovery guide. This PCR formalizes that procedure as a Charter-referenced policy rather than a standalone operational document.

---

## Current Policy Limitation

The Sandbox Operating Charter § 21 (Incident Response) covers detection, containment, and notification but stops before recovery. It does not address:

- Criteria for declaring a rebuild scenario
- The rebuild sequence and its dependencies
- What constitutes "restored normal operations"
- Who authorizes each rebuild step

---

## Proposed Change

Add a new section to the Sandbox Operating Charter after the Emergency Operations section (§ 22, if PCR-SHUT-002 is approved, or after § 21 if not):

---

**§ 23 — Business Continuity**

**23.1 — Purpose**

The organization must be rebuildable from documented procedures without relying on memory, verbal instruction, or institutional knowledge held only in active sessions.

**23.2 — Rebuild trigger**

A rebuild is required when any of the following occur:

1. Mac mini hardware failure requiring OS reinstall or replacement
2. SandboxData external drive failure or data corruption
3. Both Mac mini and SandboxData drive are lost simultaneously
4. CEO determines that a controlled wipe and rebuild is appropriate following a security incident

**23.3 — Rebuild authority**

Only the CEO may authorize a rebuild. The President Agent may recommend a rebuild but may not initiate one.

**23.4 — Rebuild procedure**

The rebuild sequence is defined in `operations/sandbox-rebuild-procedure.md`. That document is the authoritative step-by-step reference. This Charter section does not duplicate it.

**23.5 — Backup infrastructure requirement**

The organization must maintain backup infrastructure sufficient to recover the artifacts identified as Tier 2 in `operations/sandbox-rebuild-procedure.md § Phase A`. The Engineering Director is responsible for maintaining backup infrastructure once the CEO approves a backup approach. The President Agent is responsible for flagging if backup infrastructure lapses or becomes stale.

**23.6 — Rebuild completion**

A rebuild is complete when:

1. The governance repo is cloned and verified against GitHub
2. The git relay is operational (smoke test passes)
3. All scheduled tasks are active
4. All active tool connections are verified
5. The President Agent reads `memory.md` and `session-handoff.md` and reports that organizational state is consistent
6. The CEO has explicitly declared the rebuild complete

**23.7 — Recovery documentation**

Every rebuild event — even a partial one — must produce a reconstruction log filed in `audit/incident-reports/`. The log must record what was lost, what was recovered, what was recreated, and what was permanently lost.

---

## Risk Created by Change

- **§ 23.5 backup infrastructure requirement** creates an ongoing maintenance obligation. Mitigated by: Engineering Director owns it; President Agent flags lapses; backup infrastructure is simple (primarily git commits and a Drive sync).
- **§ 23.6 rebuild completion criteria** adds a gate that could delay declaring operations restored. Benefit outweighs risk: a declared "complete" rebuild that is actually incomplete is worse than a briefly delayed declaration.

---

## Risk Reduced by Change

- **Ad hoc recovery:** Without this section, a rebuild would be improvised. Critical steps (credential rotation, relay re-activation, task recreation) could be missed.
- **No defined completion state:** Without § 23.6, there is no shared definition of "restored." The CEO and President Agent might disagree about whether operations have resumed.

---

## Recommended Authority Level

This amendment creates obligations and procedures, not new execution authority. No new Level 6 grants. The backup infrastructure tasks are within existing Engineering Director and Operations Director capabilities.

---

## Recommended Maximum Acceptable Loss

N/A — this PCR creates constraints and procedures, not new capabilities.

---

## Dependencies

- `operations/sandbox-rebuild-procedure.md` must be published before this Charter section references it
- PCR-SHUT-002 should be approved first (to establish the § numbering convention), or this PCR should be renumbered on publication
- Backup infrastructure approach selected: Google Drive sync (CEO decision 2026-06-06); § 23.5 is actionable upon this PCR's approval

---

*This is a proposal only. It is not active policy. The President Agent must not behave as if this change has been approved until the CEO explicitly approves and publishes it.*
