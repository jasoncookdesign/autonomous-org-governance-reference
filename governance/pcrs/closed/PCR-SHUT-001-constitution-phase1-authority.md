# Policy Change Request — PCR-SHUT-001

**Date:** 2026-06-05  
**Proposed by:** President Agent  
**Via:** President Agent (self-authored as lead on INI-003)  
**Subject:** Policy Change Proposal — Constitution Amendment: President Agent Phase 1 Emergency Shutdown Authority

> **Status:** Approved and published — 2026-06-06. Constitution Article XIII is active policy. `capabilities/president-coordination.md` updated concurrently.

---

## Operational Problem

The Emergency Shutdown Procedure (`operations/emergency-shutdown-procedure.md`) defines a four-phase shutdown architecture. Phase 1 (Contain) requires halting all scheduled tasks and directing all directors to stop work. This must happen immediately — ideally within minutes of a triggering condition, before the CEO can be reached.

Under current policy, the President Agent has no authority to initiate Phase 1 unilaterally. All shutdown initiation requires the CEO. This creates a gap: if a Critical incident occurs outside of an active CEO session, no containment can occur until the CEO responds. Scheduled tasks could continue to run, directors could continue executing, and a compromised workflow could propagate for hours before the CEO is notified.

---

## Current Policy Limitation

**Constitution, Article III — Deny By Default:**
> "Authority is denied unless explicitly granted."

**Constitution, Article VIII — Capability-Scoped Autonomy:**
> "Autonomy exists only inside approved capabilities. Default mode: Observe → Analyze → Recommend → Draft. Execution authority requires explicit capability grants."

No current policy grants the President Agent authority to stop scheduled tasks or halt director work unilaterally. The `capabilities/president-coordination.md` capability grants Level 6 only for agent lifecycle decisions and direct CEO email.

---

## Proposed Change

Add a new Article XIII to the Constitution:

---

**Article XIII — Emergency Operations Authority**

The President Agent may initiate Phase 1 Containment of the emergency shutdown procedure unilaterally, without prior CEO authorization, when any of the following conditions is observed:

1. A director reports a Critical-severity incident
2. The Security Steward issues a Model C halt directive
3. Any agent is actively executing in excess of its authority ceiling with no apparent way to halt through normal direction
4. Active prompt injection with apparent behavioral effect on any agent is detected

Phase 1 Containment is defined in `operations/emergency-shutdown-procedure.md` and is limited to: disabling active scheduled tasks, halting in-progress director assignments, and notifying the CEO and Security Steward. Phase 1 does not include credential revocation, network isolation, or any action not enumerated in the Phase 1 procedure.

Phases 2 through 4 of the Emergency Shutdown Procedure require explicit CEO authorization under all circumstances and are not delegated by this amendment.

The President Agent must notify the CEO immediately upon Phase 1 initiation. If the triggering condition is later determined to have been a false positive, the President Agent must document this in the Shutdown Manifest with a full account of the observation that prompted initiation.

---

## Companion Change Required

`capabilities/president-coordination.md` must be updated to add Phase 1 emergency initiation as a Level 6 execution authority, scoped to the four conditions above and the enumerated Phase 1 actions only. This should be done concurrently with approval of this PCR.

---

## Risk Created by Change

- **False positive risk:** The President Agent initiates Phase 1 based on a misread observation, disrupting all operations unnecessarily. Mitigated by: objective triggering conditions, immediate CEO notification requirement, and Shutdown Manifest documentation. False positives are reversible.
- **Authority creep risk:** This amendment could be cited to justify broader unilateral action. Mitigated by: explicit enumeration of Phase 1 actions; Phases 2–4 named as requiring CEO authorization regardless.

---

## Risk Reduced by Change

- **Unconstrained incident propagation:** Without Phase 1 authority, a Critical incident during an unattended session continues unchecked until the CEO responds.
- **CEO notification delay during active incident:** Under current policy, the President Agent cannot stop anything — only notify. If notification is missed, no containment occurs.

---

## Recommended Authority Level

Level 6 (Execute) for Phase 1 Containment only, scoped to: scheduled task disabling and director halt notification. No financial, credential, network, or policy-modification authority.

---

## Recommended Maximum Acceptable Loss

Phase 1 actions are reversible. Maximum acceptable loss from false positive: operational disruption of up to several hours. This is significantly lower than the loss from an uncontained true positive.

---

## Dependencies

Companion update to `capabilities/president-coordination.md` must be made concurrently.

---

*This is a proposal only. It is not active policy. The President Agent must not behave as if this change has been approved until the CEO explicitly approves and publishes it.*
