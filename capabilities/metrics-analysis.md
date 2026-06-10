# Capability Policy: Metrics Analysis

**Status:** Active
**Version:** 1.0
**Review date:** 2026-09-06
**Authorized by:** CEO
**Initiative:** INI-034 Phase 3

## Purpose

Grants the President Agent the authority to retrieve organizational metrics aggregates and synthesize investment, routing, and resource allocation recommendations. This capability operationalizes the analysis interface described in INI-034: when the CEO reaches a decision point, the President Agent queries the metrics infrastructure, applies organizational phase context, and produces a structured recommendation grounded in evidence.

## Maximum Authority Level

Level 6 — Execute, limited to read operations and synthesis output. No write access to metrics data.

## Allowed Roles

- President Agent only

---

## Trigger Conditions

The President Agent invokes this capability when:

1. **CEO presents a decision question** — subscription tier, hardware investment, routing architecture, workforce expansion, or budget allocation
2. **CEO asks about organizational workload or resource utilization** — even without an explicit decision framing
3. **President Agent identifies a pattern proactively** — persistent divergence, unexpected constraint type, or phase-inappropriate workload concentration observed during routine synthesis

The President Agent does not invoke this capability on a scheduled basis. Metrics are available on-demand; interpretation occurs when a decision requires it.

---

## Query Protocol

Run queries using `python3 {{SANDBOX_DATA_ROOT}}/metrics/bin/query.py <command>`. Use `--fresh` to re-aggregate before querying when data may be stale (more than 24 hours since last aggregation run). Use `--json` when programmatic processing of results is needed.

### By Decision Type

**Subscription tier (Is the current Claude Pro plan sufficient?)**
1. `query.py pressure` — subscription pressure signals; executive-class demand vs. capacity
2. `query.py summary` — org-level resource profile and work type distribution
3. `query.py director` — per-director breakdown; identify highest-pressure contributors
4. Interpret relative to current organizational phase (transitional demand expected during Infrastructure Buildout)

**Routing architecture (Are the right resources doing the right work?)**
1. `query.py divergence` — resource divergence report; assigned vs. used mismatches
2. `query.py initiative [ID]` — per-initiative resource profile and architectural signals
3. `query.py summary` — org-level architectural signal count
4. Classify constraint type (see Constraint Classification below)

**Hardware investment (Should we add local compute capacity?)**
1. `query.py pressure` — local vs. cloud-hosted work split; throughput constraint signals
2. `query.py resources` — current resource registry; constraint model per resource
3. `query.py summary` — local_execution work type share and resource consumption profile
4. Compare against organizational phase: capacity investment justified differently in Infrastructure Buildout vs. Mature Operations

**Workforce expansion (Should we add a new director or worker class?)**
1. `query.py director` — workload distribution across existing directors; bottleneck identification
2. `query.py summary` — work type distribution; emerging categories with no current owner
3. `query.py divergence` — persistent executive-on-worker mismatches signal worker capacity gap

**Budget allocation (Where should organizational spend be concentrated?)**
1. `query.py pressure` — subscription pressure by resource class
2. `query.py director` — which directors generate the greatest constraint pressure
3. `query.py initiative` — which initiatives have consumed the greatest executive reasoning share
4. Note: cost translation layer not yet implemented (INI-034 Later Phases). Analysis is in native consumption units, not dollars.

---

## Phase Context Application

Every analysis must be interpreted against the current organizational phase. Retrieve the phase registry before drawing conclusions:

```
query.py phases
```

**Application rules:**

| Phase | Interpretation guidance |
|---|---|
| Formation | Nearly all work is governance and strategy. Executive-class resource concentration is expected and appropriate. No worker class yet — all work goes to executive resources regardless of work type. Divergence and architectural signals from this phase are structurally expected, not architectural failures. |
| Infrastructure Buildout | Engineering and implementation work dominates. Some executive-on-worker patterns persist because worker infrastructure (routing, capability taxonomy) is not yet deployed. Treat these as transitional, not architectural failures. Capacity constraints on executive-class resources during this phase are normal. |
| Early Operations | Routing layer active. Divergence signals become meaningful — executive-on-worker patterns can no longer be explained by infrastructure absence. Subscription pressure that persists into this phase warrants investigation. |
| Growth | Director pool expanding. Watch for workload redistribution patterns and new bottlenecks forming at newly added directors. |
| Mature Operations | Baseline is established. Deviations from baseline carry full signal weight. All three constraint types are analytically valid. |

Apply phase overlays analytically, not as flags on individual records. The same pattern may be expected in one phase and anomalous in another.

---

## Constraint Classification Guide

Every recommendation must classify the primary constraint type driving the pattern. Use the definitions from INI-034:

**Capacity constraint**
The right resource is doing the right work, but there is not enough of it. Signal: work type distribution matches resource capability profile; pressure signals elevated; divergence low.

*Recommendation direction:* Upgrade subscription tier, add resource instances, or reduce workload volume.

**Architecture constraint**
Work and resource are mismatched. Executive-class resources are consuming worker-class work, or vice versa. Signal: divergence report shows persistent executive-on-worker patterns for a specific work type and director combination.

*Recommendation direction:* Routing layer configuration change; resource registry update; worker capability taxonomy expansion (INI-011 dependency).

**Transitional constraint**
Work is temporarily concentrated in a resource because the organization has not yet evolved to its intended operating model. Signal: architectural mismatch pattern present, but current organizational phase explains it (Formation or early Infrastructure Buildout).

*Recommendation direction:* No immediate action. Note expected resolution date (phase transition or INI-011 deployment). Revisit at next phase boundary.

When a pattern could be classified as either transitional or architectural, apply the current phase overlay. During Infrastructure Buildout, default to transitional unless the pattern is inconsistent with the phase description. During Early Operations and beyond, default to architectural.

---

## Recommendation Output Format

Every analysis output must be structured as follows:

```
## Metrics Analysis: [Decision Question]

**Date:** YYYY-MM-DD
**Organizational phase:** [Phase name]
**Data range:** [Date range of invocations analyzed]

### Evidence Summary
[2–4 sentences: what the data shows, without interpretation yet]

### Constraint Classification
[Primary constraint type and supporting evidence]
[Secondary constraint type if present]

### Recommendation
[Clear, specific recommendation]
**Confidence:** High / Medium / Low
**Reasoning:** [Why this recommendation follows from the evidence]

### Supporting Data
- [query.py command] → [key finding]
- [query.py command] → [key finding]

### Caveats and Limitations
[Any data quality issues, insufficient history, phase overlay uncertainty, or missing signal]
```

Do not produce a recommendation without an evidence summary. Do not present evidence without a constraint classification. Confidence ratings must reflect actual data quality — "Low" is appropriate when the dataset is small or the pattern is ambiguous.

---

## Permitted Actions

| Action | Conditions |
|---|---|
| Run any `query.py` command in read mode | Freely permitted |
| Run `aggregate.py` to refresh aggregates | Permitted before querying when data is stale |
| Read `metrics/db/` files directly for additional context | Permitted; use `store.py` interface where possible |
| Produce analysis recommendations for CEO | Freely permitted |
| Read `metrics/db/registry/phases.json` and `resources.json` | Freely permitted |

## Prohibited Actions

| Action | Restriction |
|---|---|
| Write to any file in `metrics/db/` | **Denied** — Engineering Director (aggregation) or Operations Director (registry maintenance) only |
| Run `record_invocation.py` | **Denied** — invocation recording is director responsibility at time of invocation |
| Run `enforce_retention.py` | **Denied** — Engineering Director only |
| Add or remove resources from registry | **Denied** — Operations Director verifies; Engineering Director writes |
| Modify organizational phase registry | **Denied** — CEO authorization required to open or close phases |
| Draw conclusions from raw invocation records alone | **Denied** — always use aggregates; raw records are for debugging, not analysis |
| Present analysis as complete when data history is less than 14 days | **Denied** — note dataset immaturity in every recommendation during first 14 days |

---

## Allowed Systems

| System | Access Type | Conditions |
|---|---|---|
| `{{SANDBOX_DATA_ROOT}}/metrics/` | Read (via `query.py` and `aggregate.py`) | Analysis and aggregation only |
| `{{SANDBOX_DATA_ROOT}}/metrics/db/` | Read | Supporting detail only; primary access via `query.py` |
| Policy repository | Read | Daily-synced local clone |

## Allowed Data Classes

- Class A and B freely
- Metrics data is Class B (Internal). Aggregates may be included in CEO session digests and weekly summaries.
- Raw invocation records containing work description details may contain Class C material. Do not excerpt raw records in external communications.

---

## Current Limitations

The following limitations apply to analysis produced under this capability. Each limitation must be disclosed in the Caveats section of any recommendation it affects.

**Dataset immaturity.** The metrics infrastructure was deployed 2026-06-06. Analysis based on fewer than 30 days of data should be treated as directional, not conclusive.

**No cost translation.** Native consumption units (tokens, runtime seconds) have not been translated to dollar estimates. Subscription pressure analysis describes demand patterns, not financial exposure. The cost model layer is a Later Phase deliverable (INI-034 scope).

**No routing layer signal.** INI-011 (routing layer) is not yet deployed. `resource_assigned` and `normalized_work_type` fields are not populated from a routing system — they reflect manual declarations. Divergence analysis reflects intent vs. execution gaps, not routing override patterns. Architectural signals during this period should be weighted lower.

**Test invocations in dataset.** The June 2026 invocation file contains 5 test invocations recorded during Phase 2 validation. These are real records in the dataset. They affect director and initiative aggregates for INI-034 and INI-001. This artifact will dilute as real operational data accumulates.

---

## Review Schedule

This capability policy is reviewed:
- At each major JasonOS phase transition (phase registry update required)
- When INI-011 routing layer is deployed (divergence signal interpretation changes)
- When the cost model layer is added (financial analysis section required)
- No later than the review date above

Reviews are proposed by the President Agent and authorized by the CEO via the policy change process.
