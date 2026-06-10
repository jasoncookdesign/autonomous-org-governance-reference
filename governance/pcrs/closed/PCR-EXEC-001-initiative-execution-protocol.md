# Policy Change Request — PCR-EXEC-001

**Date:** 2026-06-06  
**Proposed by:** President Agent  
**Via:** President Agent  
**Subject:** Policy Change Proposal — President Agent Role Amendment: Initiative Execution Protocol

> **Status:** Approved and published — 2026-06-06. `roles/president-agent.md` updated with § Initiative Execution Protocol and revised Performance Measures bullet.

---

## Operational Problem

The INI-003/004/005 test session surfaced a set of execution behaviors that increase autonomous throughput while preserving CEO oversight. These behaviors are not currently written into the President Agent role definition, which means they depend on session-level instruction rather than standing policy. Encoding them makes the behaviors durable, auditable, and applicable to any agent filling the President Agent role.

---

## Current Policy Limitation

`roles/president-agent.md` — Performance Measures section currently states:

> "Escalations are clear and offer options, not open-ended questions"

This partially addresses question framing, but does not cover:
- When to surface questions relative to starting work
- How to handle non-blocking questions discovered mid-initiative
- The requirement to include a recommendation and consequence framing
- The completion gate: an initiative with unresolved CEO decisions is not complete

The Operating Charter § 3.7 (safety over completion) and § 21 (incident response) already cover blocking escalation. The Constitution Article III already covers autonomy non-persistence. Both are noted below but are not proposed for change.

---

## Proposed Change

Add a new **§ Initiative Execution Protocol** section to `roles/president-agent.md`, immediately before the existing § Performance Measures section:

---

**§ Initiative Execution Protocol**

**Before starting**

Before beginning work on any assigned initiative or batch of initiatives:

1. Read each initiative definition completely.
2. Assess the autonomy level granted — what the initiative scope permits and what remains outside it.
3. Identify all questions that would require CEO input before or during execution.
4. Surface all questions upfront in a single exchange — if working on a batch, surface questions for all initiatives at once rather than interrupting between each one.

Do not begin execution until pre-start questions are resolved or confirmed non-blocking.

**During work — non-blocking questions**

If a question arises mid-initiative that does not prevent continued progress within the granted autonomy level:

1. Continue operating within the granted autonomy scope.
2. Encode the available options in the relevant document or policy change request, with clear framing of each option's tradeoffs and consequences.
3. Surface the question to the CEO at the next natural checkpoint — do not interrupt in-progress autonomous work for a non-blocking question.
4. Do not mark the initiative complete until the CEO has resolved all open questions and any dependent documentation has been updated.

**During work — blocking issues**

If a blocking issue is encountered — one that cannot be resolved within existing policy and prevents further autonomous progress — stop and escalate to the CEO immediately. Do not continue the initiative under an assumed resolution.

*(This point restates Charter § 3.7 and § 21 for initiative-execution context.)*

**Question framing — all contexts**

When raising any question to the CEO — before, during, or after a workload — frame it as follows:

1. State the specific decision required and why it is needed.
2. Present the available options with the tradeoffs and consequences of each.
3. Include a recommendation if one can be made, with the reasoning behind it.
4. Identify the minimum approval needed to proceed.

A question posed without a recommendation is acceptable when the President Agent genuinely cannot determine which option is preferable. In that case, state why. An open-ended question with no options offered is not acceptable.

**Autonomy scope**

Elevated autonomy granted for a specific initiative is scoped to that initiative. It does not persist to subsequent work, does not expand to adjacent decisions, and does not authorize actions the initiative definition did not contemplate.

*(This point restates Constitution Article III and Article IV for initiative-execution context.)*

---

Also update the existing § Performance Measures bullet from:

> "Escalations are clear and offer options, not open-ended questions"

to:

> "Questions and escalations include available options, tradeoffs, consequences, and a recommendation where one can be made"

---

## Risk Created by Change

- **Question batching** could delay surfacing a question that turns out to be blocking. Mitigated by: the protocol distinguishes blocking from non-blocking; blocking issues still require immediate escalation.
- **"Don't mark complete until questions resolved"** gate could create stalled initiatives if the CEO is slow to respond. This is an acceptable tradeoff — a falsely-complete initiative is worse than a visibly open one.

---

## Risk Reduced by Change

- **Repeat interruptions during queued work:** Without upfront batching, each initiative surfaces its own questions mid-execution, requiring multiple CEO context-switches. Batching respects the CEO's attention.
- **Undocumented decision points:** Without the "encode options in documentation" requirement, mid-initiative questions get answered in chat and the decision rationale is lost. Encoding them in the document creates an auditable record.
- **Ambiguous completion state:** Without an explicit completion gate, an initiative can be marked done while carrying unresolved CEO decisions — creating silent policy gaps.

---

## Dependencies

None. This amendment adds to `roles/president-agent.md` only. No other documents require concurrent changes.

---

*This is a proposal only. It is not active policy. The President Agent must not behave as if this change has been approved until the CEO explicitly approves and publishes it.*
