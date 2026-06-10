---
document: INI-034-SCHEMA-005
title: "Measurement Contract — INI-034 / INI-011"
initiative: INI-034
phase: 1
status: Proposed
author: Engineering Director
created: 2026-06-06
updated: 2026-06-06
---

# Measurement Contract

## Purpose

This document is the formal interface contract between the metrics framework (INI-034) and the routing layer (INI-011). It specifies exactly which signals INI-011 must emit for each invocation, in what format, and at what point in the invocation lifecycle. INI-034 is the consumer; INI-011 is the producer.

Neither initiative blocks the other. INI-034 Phase 1 defines the contract before INI-011 routing implementation begins, so that routing signal design is informed by measurement requirements rather than discovered after the fact. This document is the artifact that achieves that sequencing benefit.

---

## Contract Parties

| Party | Role | Initiative |
|---|---|---|
| Metrics Framework | Consumer — receives signals and stores invocation records | INI-034 |
| Routing Layer | Producer — emits signals at task routing time | INI-011 |
| Execution Layer | Producer — emits signals at invocation completion | INI-034 / INI-010 |

---

## Signal Overview

Each invocation produces three categories of signal, emitted by three different owners:

```
Director (at task creation)
  → Declared Work Type

Routing Layer / INI-011 (at routing time, before execution)
  → Normalized Work Type
  → Resource Assigned
  → Constraint Set Applied (secondary tier)
  → Routing Decision Reason (secondary tier)

Execution Layer (at invocation completion)
  → Resource Used
  → Invocation Outcome
  → Resource Consumption
```

The metrics framework assembles these into a single invocation record. Signals from different owners may arrive at different times — the record is considered complete when all MVP signals are present.

---

## Signals Required from the Routing Layer (INI-011)

These are the specific signals INI-011 must emit. Engineering Director is responsible for ensuring the routing layer implementation satisfies this contract.

### MVP Signals (Required)

#### `normalized_work_type`

| Property | Value |
|---|---|
| Type | string |
| Source | Routing layer (INI-011) |
| Timing | Emitted at routing time, before execution begins |
| Description | The validated and normalized work classification for this invocation. Drawn from the Work Taxonomy (`work-taxonomy.md`). |
| Derivation | The routing layer receives the `declared_work_type` from the director's task record and normalizes it against the Work Taxonomy. If the declared type is valid, the normalized type matches. If the declared type is ambiguous or invalid, the routing layer applies the most appropriate taxonomy value and logs the discrepancy. |
| Format | Dot-notation taxonomy path, e.g., `research.technical`, `implementation.coding`, `governance` |
| Required values | Must be a valid top-level or subcategory value from the approved Work Taxonomy |
| Null behavior | Not permitted — routing layer must emit a value for every invocation. If classification cannot be determined, default to the top-level work type and flag for review. |

#### `resource_assigned`

| Property | Value |
|---|---|
| Type | string |
| Source | Routing layer (INI-011) |
| Timing | Emitted at routing time, before execution begins |
| Description | The `resource_id` of the resource the routing layer selected for this invocation. |
| Format | Must match a `resource_id` in the Resource Registry (`resource-registry-schema.md`) with `status: active` |
| Null behavior | Not permitted — if no resource can be selected, the invocation outcome is `failure` and the routing layer emits `resource_assigned: null` with outcome `failure`. |

### Secondary Signals (Valuable, Deferred)

These signals are not required at launch but Engineering Director should design the routing layer to emit them when the secondary field tier is activated in a later phase.

#### `constraint_set_applied`

| Property | Value |
|---|---|
| Type | string |
| Description | The routing constraint set that governed resource selection for this invocation — e.g., a constraint policy name or identifier. Enables analysis of how routing policies affect resource distribution. |
| Timing | Emitted at routing time |

#### `routing_decision_reason`

| Property | Value |
|---|---|
| Type | string |
| Description | Human-readable explanation of why the routing layer selected the assigned resource — e.g., "Only resource advertising `executive_reasoning`." Enables routing transparency and debugging. |
| Timing | Emitted at routing time |

---

## Signals Captured at Execution (Not from INI-011)

These signals are produced by the execution layer, not the routing layer. They are documented here for completeness so INI-011 Engineering can understand the full invocation record structure.

#### `resource_used`

The `resource_id` of the resource that actually executed the invocation. May differ from `resource_assigned` if the execution layer overrode the routing layer's selection or if escalation or rerouting occurred. Divergence between `resource_assigned` and `resource_used` is a primary architectural alignment signal.

#### `invocation_outcome`

One of: `success`, `failure`, `escalated`, `rerouted`. Emitted by the execution layer upon completion.

#### `resource_consumption`

Key-value map of native consumption units appropriate to `resource_used`. Token counts, runtime seconds, GPU minutes, API calls — as applicable. See `invocation-record-schema.md` for per-resource-type examples.

---

## Signal Transmission Protocol

The exact transmission mechanism between INI-011 and INI-034 is deferred to Phase 2 implementation. This contract specifies what must be transmitted, not how. Engineering Director must resolve the following in Phase 2:

1. **Transmission format:** JSON schema for the routing signal payload
2. **Transmission channel:** How routing signals reach the metrics storage layer (direct write, event stream, API call, or shared database)
3. **Timing guarantees:** Whether signals are delivered synchronously (before execution) or asynchronously (after)
4. **Failure handling:** What happens if the routing layer cannot emit signals (does execution proceed? is the invocation unrecorded?)
5. **Idempotency:** How duplicate signal emissions are handled

These decisions must be documented and reviewed before Phase 2 implementation begins.

---

## Architectural Alignment Signals

Two divergence patterns are the primary signals for architectural constraint detection. Both depend on accurate signal emission from INI-011.

### Signal 1 — Resource Divergence

`resource_assigned` ≠ `resource_used`

**What it means:** The routing layer selected one resource but execution used a different one. This may indicate that a resource was unavailable, that an escalation occurred, or that the execution layer bypassed the routing layer.

**Threshold for investigation:** If resource divergence rate for any (work_type, director) combination exceeds 10% over a 30-day window, Engineering Director should investigate.

**INI-011 responsibility:** The routing layer should minimize divergence by accurately representing resource availability at routing time. If a resource is unavailable, the routing layer should select an alternative rather than routing to an unavailable resource.

### Signal 2 — Architectural Constraint Signal

Derived at analysis time: `normalized_work_type` classification vs. actual `resource_used` capability profile

**What it means:** Work that should route to worker-class resources is consistently landing on executive-class resources, or vice versa.

**INI-011 responsibility:** The routing layer's capability-matching logic must correctly route work to resources whose capability profile matches the work type's requirements. If an executive-class resource is repeatedly receiving worker-class work, the routing logic or capability definitions require revision.

---

## Contract Versioning

This document is versioned via the governance repo commit history. Changes to required signals (MVP tier) require:

1. Engineering Director proposes revision with impact analysis.
2. President Agent reviews compatibility with existing storage schema.
3. CEO approves.
4. Both INI-034 and INI-011 implementations are updated in the same release.

Changes to secondary signals (deferred tier) require Engineering Director proposal and President Agent review only.

---

## Acceptance Criteria for INI-011 Phase 2 Routing Implementation

The routing layer satisfies this contract when:

- [ ] Every invocation emits a `normalized_work_type` value that is a valid taxonomy entry
- [ ] Every invocation emits a `resource_assigned` value that is a valid active resource registry entry
- [ ] `resource_assigned` values match resources whose capability profile satisfies the work type requirement
- [ ] Routing signals are emitted before or at execution time (not after completion)
- [ ] Null `resource_assigned` is only emitted when no active resource can satisfy the requirement
- [ ] Signal transmission mechanism is documented and failure handling is defined
- [ ] Resource divergence between `resource_assigned` and `resource_used` is measurable and queryable

---

## Notes

**2026-06-06:** Contract produced by Engineering Director as INI-034 Phase 1 deliverable. This document defines required signals before INI-011 routing implementation begins. Engineering Director should ensure the routing layer design incorporates these signal emission requirements before INI-011 Phase 3 implementation proceeds. Signal transmission protocol deferred to Phase 2.
