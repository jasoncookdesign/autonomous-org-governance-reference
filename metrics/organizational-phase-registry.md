---
document: INI-034-SCHEMA-004
title: "Organizational Phase Registry"
initiative: INI-034
phase: 1
status: Proposed
author: Engineering Director
created: 2026-06-06
updated: 2026-06-06
---

# Organizational Phase Registry

## Purpose

The organizational phase registry provides temporal context for workload and resource consumption analysis. The same consumption pattern — an executive-class resource executing classification work — means something different during Formation than during Mature Operations. Without phase context, the President Agent cannot distinguish transitional constraints from structural ones.

Phase context is an analytical overlay, not operational metadata. It is never attached to individual invocation records. The President Agent applies it at query time by joining the invocation timestamp against the phase registry. This preserves the integrity of historical records — a task logged during Formation stays Formation-contextualized forever, without requiring any flag on the record itself.

---

## Design Principles

**Phases are analytical overlays.** Phase boundaries are stored here, not in invocation records. The President Agent applies phase context when synthesizing analysis, not when recording data.

**Phase boundaries are date-bounded.** Each phase has a defined start date. The end date is either explicit (when the next phase begins) or open (the current phase). Phases do not overlap.

**Transitions are significant signals.** A consumption pattern that was acceptable in Formation may indicate an architectural constraint in Early Operations. The President Agent must be able to compare across phases as well as within them.

**Phase changes require CEO authorization.** Phases reflect the CEO's assessment of organizational maturity. The President Agent may recommend a phase transition; the CEO approves it.

---

## Phase Definitions

### Formation

**Description:** Initial organizational design and governance construction. Directors are being defined. Governance documents are being authored. Core policies are being established. Technical systems may not yet exist. Work is concentrated in the President Agent and CEO, with directors being onboarded as they are defined.

**Characteristics:** High proportion of governance and strategy work types. Executive-class resources doing everything, including work that will eventually be worker-class. Low total invocation volume. No routing layer active.

**Constraint interpretation:** Architectural constraints observed during Formation are transitional by definition — the organization has not yet built the worker-class infrastructure to absorb worker-class work. These patterns should be logged but not treated as structural problems requiring immediate investment.

---

### Infrastructure Buildout

**Description:** Core technical systems are being established. Engineering initiatives are active. Directors are operational. The routing layer, metrics framework, and distributed compute architecture are being built. Work composition is implementation-heavy.

**Characteristics:** High proportion of implementation and governance work types. Executive-class resources are still primary. Infrastructure investment is active. First worker-class resources may be coming online.

**Constraint interpretation:** Implementation work on executive-class resources is expected and appropriate — this is complex architectural work. Subscription pressure during this phase may reflect temporary demand concentration rather than sustained capacity need.

---

### Early Operations

**Description:** First operational workloads are active. Routing systems are live. The organization is executing against its portfolio rather than primarily building itself. Directors are running regular workloads.

**Characteristics:** Work type distribution begins diversifying across all top-level types. Routing layer is active and producing Resource Assigned vs. Resource Used divergence data. First architectural constraint signals become meaningful.

**Constraint interpretation:** Architectural constraints detected during Early Operations may be structural — the organization is operating its intended model, and persistent resource mismatches warrant investigation. Capacity constraints may still be transitional as worker-class resources come online.

---

### Growth

**Description:** Director pool and worker capacity are actively expanding. New resources are being added to the registry. Workload volume is increasing. The organization is scaling.

**Characteristics:** Increasing invocation volume. New resource types appearing in registry. Routing decisions distributing across a broader capability pool. Subscription pressure signals become investment-decision inputs.

**Constraint interpretation:** Capacity constraints during Growth are genuine signals for subscription or hardware investment decisions. Architectural constraints that persist into Growth indicate routing or classification problems requiring Engineering Director attention.

---

### Mature Operations

**Description:** Steady-state organizational execution. Portfolio and director structure are stable. Workload patterns are predictable. Investment decisions are driven by sustained evidence rather than temporary concentration.

**Characteristics:** Stable invocation volume with predictable growth. Well-distributed work types. Resource utilization is interpretable against historical baselines.

**Constraint interpretation:** All constraint signals during Mature Operations are structural. Capacity constraints justify subscription or hardware investment. Architectural constraints require routing design changes. No transitional allowances apply.

---

## Current Phase Registry

| Phase | Start Date | End Date | Status | Authorized By |
|---|---|---|---|---|
| Formation | 2026-06-02 | 2026-06-05 | Completed | CEO |
| Infrastructure Buildout | 2026-06-06 | (open) | Current | CEO |

**Formation start date — verified 2026-06-06 by Knowledge Director (INI-034 Phase 3):** The Formation start date is confirmed as 2026-06-02, the date of the first commit to the governance repository (`chore: initialize governance repository`, SHA `c18e33a`). This is the earliest datable evidence of formal organizational construction. Pre-formalization conceptual work by the CEO is not captured in the governance record and therefore not attributed to any phase. The gap between any pre-repo planning and 2026-06-02 is acknowledged but not analytically significant — Formation-phase interpretation applies to governance-repo-verifiable work from 2026-06-02 forward.

---

## Phase Transition Procedure

1. President Agent identifies that organizational characteristics match the next phase definition.
2. President Agent drafts a phase transition recommendation documenting evidence.
3. CEO reviews and approves.
4. Engineering Director updates this registry with the transition date.
5. Commit to governance repo.

Phase transitions are irreversible in the registry — they may not be backdated or undone without a formal governance record.

---

## Using Phase Context in Analysis

When the President Agent performs workload analysis, the following procedure applies:

1. Determine the time range of the analysis query.
2. Join the time range against the phase registry to identify which phases are covered.
3. Apply the constraint interpretation guidance for each relevant phase.
4. If the analysis spans multiple phases, compare patterns across phases explicitly — highlight what changed and what remained constant across the transition.
5. Surface phase context in the recommendation to the CEO: "During Infrastructure Buildout, this pattern was transitional. If it persists into Early Operations, it becomes a structural signal."

---

## Notes

**2026-06-06:** Registry produced by Engineering Director as INI-034 Phase 1 deliverable. Phase definitions and transitional constraint interpretation derived from CEO-approved initiative definition. Current phase set to Infrastructure Buildout effective 2026-06-06 based on organizational state (governance repo complete, engineering initiatives active, routing and metrics systems in buildout). Formation start date requires Knowledge Director verification.

**2026-06-06:** Formation start date updated to 2026-06-02 by Knowledge Director as INI-034 Phase 3 deliverable. Date verified from earliest governance repository commit (`c18e33a`, "chore: initialize governance repository"). Prior estimated date (2026-01-01) replaced with evidence-based date.
