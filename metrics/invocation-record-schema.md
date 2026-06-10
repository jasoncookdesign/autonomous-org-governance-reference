---
document: INI-034-SCHEMA-001
title: "Invocation Record Schema"
initiative: INI-034
phase: 1
status: Proposed
author: Engineering Director
created: 2026-06-06
updated: 2026-06-06
---

# Invocation Record Schema

## Purpose

The invocation record is the atomic unit of measurement for JasonOS workload and resource consumption. Every interaction between a director-originated task and a compute resource produces one invocation record. All higher-level aggregates — task, initiative, director, organizational — are derived from these records.

This schema defines the MVP field set required at launch. Secondary fields are defined separately and deferred. Excluded fields are explicitly listed and may not be added without a demonstrated decision need.

---

## MVP Fields (Required at Launch)

| Field | Type | Description | Source |
|---|---|---|---|
| `invocation_id` | string (UUID) | Unique identifier for this invocation | Execution layer — generated at invocation time |
| `timestamp` | ISO 8601 datetime | When the invocation began | Execution layer |
| `parent_task_id` | string | The task this invocation belongs to | Director (at task creation) |
| `parent_initiative_id` | string | The initiative the parent task belongs to | Derived from task record |
| `director` | string | The director that originated the work | Director (at task creation) |
| `work_type` | string | Normalized work classification per work taxonomy | Routing layer (INI-011) |
| `resource_assigned` | string | Resource the routing layer selected for this invocation | Routing layer (INI-011) |
| `resource_used` | string | Resource that actually executed the invocation | Execution layer |
| `invocation_outcome` | enum | Result of the invocation | Execution layer |
| `resource_consumption` | object | Native consumption units for the resource type used | Execution layer |

### `invocation_outcome` Enumeration

| Value | Meaning |
|---|---|
| `success` | Invocation completed without error |
| `failure` | Invocation failed; not escalated or rerouted |
| `escalated` | Invocation was passed to a higher-capability resource mid-execution |
| `rerouted` | Invocation was redirected to a different resource before execution began |

### `resource_consumption` Object

The `resource_consumption` field is a key-value map of native consumption units appropriate to the resource type. The specific keys depend on which resource executed the invocation.

**Examples by resource type:**

| Resource Type | Keys | Description |
|---|---|---|
| Subscription LLM (e.g., Claude) | `input_tokens`, `output_tokens` | Token counts in native units |
| Local inference | `runtime_seconds`, `input_tokens`, `output_tokens` | Runtime and tokens |
| GPU inference | `gpu_minutes`, `input_tokens`, `output_tokens` | GPU time and tokens |
| Cloud API (non-LLM) | `api_calls`, `runtime_seconds` | Calls and duration |
| Script/automation | `runtime_seconds` | Execution duration |

Dollar cost is a derived interpretation layer applied at analysis time, not a primary field. It is never stored in the invocation record.

---

## Secondary Fields (Valuable, Deferred)

These fields add analytical richness but are not required at launch. Engineering should design storage to accommodate them without requiring schema migration when they are added.

| Field | Type | Description |
|---|---|---|
| `execution_duration_ms` | integer | Wall-clock time from invocation start to completion |
| `constraint_set_applied` | string | Routing constraints that governed resource selection |
| `worker_capability_used` | string | The specific capability class that satisfied the work requirement |
| `routing_decision_reason` | string | Human-readable reason the routing layer selected this resource |
| `retry_count` | integer | Number of retries before final outcome |
| `escalation_source` | string | Resource from which escalation originated (if outcome = escalated) |
| `escalation_destination` | string | Resource to which escalation was directed (if outcome = escalated) |

---

## Explicitly Excluded Fields

The following fields are excluded from MVP and from all subsequent phases unless a demonstrated decision need is established. Each exclusion is governed by the exclusion test below.

**Exclusion test:** "If this field changed significantly over six months, would it directly influence a subscription, hardware, routing, or investment decision?" If no, the field is excluded.

| Excluded Field | Reason |
|---|---|
| Prompt text | Payload content; no decision value; privacy concern |
| Response text | Payload content; no decision value; privacy concern |
| Internal reasoning traces | Internal execution detail; no decision value |
| Token-by-token statistics | Excessive granularity; aggregated token counts are sufficient |
| CPU utilization | Hardware telemetry; no investment decision linkage |
| Memory utilization | Hardware telemetry; no investment decision linkage |
| Network utilization | Hardware telemetry; no investment decision linkage |
| Hardware performance metrics | Hardware telemetry; no investment decision linkage |
| Model configuration parameters | Execution detail; not an investment signal |

No field from this exclusion list may be added to the schema without a written proposal demonstrating direct linkage to a subscription, hardware, routing, or investment decision. The proposal requires President Agent review and CEO approval.

---

## Primary Architectural Alignment Signals

Two field pairs are architecturally significant and must be captured accurately:

**Resource divergence signal:** `resource_assigned` ≠ `resource_used`  
Indicates the routing layer's selection was overridden at execution time. Persistent divergence for a given work type warrants investigation.

**Constraint classification signal:** Derived from `work_type` + `resource_used` at analysis time  
When normalized work type and actual resource consumption profile are mismatched persistently, this indicates an architectural constraint — executive-class resources consuming worker-class work, or vice versa.

---

## Storage Requirements

- Each invocation record must be individually addressable by `invocation_id`
- Records must be queryable by `parent_task_id`, `parent_initiative_id`, `director`, `work_type`, `resource_used`, `invocation_outcome`, and `timestamp` range
- Raw invocation records are retained for a 90-day rolling window
- Before expiration, records must be aggregated into durable task-level summaries (see INI-034 retention policy)
- Storage format is structured and queryable — not append-only log files

---

## Relationship to Aggregation Hierarchy

```
Invocation Record
    ↓ aggregates to
Task Aggregate (total consumption by outcome type, work type distribution, resource divergence rate)
    ↓ aggregates to
Initiative Aggregate (task rollups, dominant work types, resource profile)
    ↓ aggregates to
Director Aggregate (initiative and task rollups across all work from this director)
    ↓ aggregates to
Organizational Aggregate (director rollups, cross-director patterns, subscription pressure signals)
```

Aggregation logic for each tier is defined in the Phase 2 scope of INI-034.

---

## Notes

**2026-06-06:** Schema produced by Engineering Director as INI-034 Phase 1 deliverable. Field set and exclusion policy derived from CEO-approved initiative definition.
