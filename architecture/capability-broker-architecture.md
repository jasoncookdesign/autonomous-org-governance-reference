# Capability Broker Architecture

**Document ID:** ARCH-001  
**Version:** 1.0  
**Status:** Approved — Ready for Engineering Execution  
**Authors:** President Agent (synthesis); CEO (architectural direction)  
**Date:** 2026-06-06  
**Initiative:** INI-011 (Distributed Compute Architecture)  
**Authority:** CEO-approved architectural direction. Engineering Director must execute against this document. Deviations require President Agent review and CEO approval.

---

## 1. Purpose

This document defines the architectural model for INI-011's core deliverable: a capability broker that routes organizational work to the most suitable compute resource. It is the output of a structured CEO–President Agent inception session and represents sufficient definition for the Engineering Director to begin the Worker Capability Taxonomy (Phase 2 first deliverable).

This document defines the model, principles, and constraints. It does not define the taxonomy itself — that is Engineering's deliverable. It does not define implementation — that comes after the taxonomy is approved.

---

## 2. The Problem Being Solved

JasonOS is currently bound to a single compute node. That is not the problem INI-011 solves.

The problem INI-011 solves is the absence of an abstraction layer that makes compute resources interchangeable. Without that layer, every new resource requires bespoke routing logic. Work gets routed to "the Mac mini" or "the AWS node" because those nodes exist — not because they are the best fit for the work. As the resource landscape changes, the routing logic has to change with it.

INI-011 establishes the abstraction layer that decouples work from compute. Once that layer exists, adding new resources — GPU workstations, phone clusters, cloud VMs, local inference servers, future hardware — becomes a matter of registering a new worker, not redesigning the routing system.

**The organization is currently architecture-constrained, not capacity-constrained.** The Mac mini is sufficient for current workloads. This initiative is not solving an immediate capacity problem. It is establishing the framework that makes future expansion straightforward when the need arises.

---

## 3. Core Model

The capability broker is a three-sided system:

```
Tasks declare what they need
    ↓
Broker evaluates fit across multiple dimensions
    ↓
Workers declare what they offer
```

Neither tasks nor workers need to know about each other. They both speak to the broker using a shared organizational vocabulary. The broker makes the match.

**The abstraction layer is the product. Compute resources underneath it are implementation details.**

A task is not routed to a worker because that worker exists. It is routed to a worker because that worker satisfies the task's work requirements and constraints better than the alternatives.

---

## 4. Three Vocabularies

The matching system operates across three distinct vocabularies. These must remain separate in the architecture. Flattening them into a single taxonomy would create ambiguity and limit the broker's ability to make intelligent tradeoffs.

### 4.1 Work Taxonomy

Describes the kind of work being performed. This is the most stable layer — these categories map to enduring organizational work types that change slowly regardless of how the resource landscape evolves.

Examples: `document_analysis`, `summarization`, `classification`, `reasoning`, `planning`, `research`, `code_implementation`, `writing`, `review`, `transcription`, `image_generation`, `media_processing`, `data_extraction`

A task declares exactly one primary work type. Secondary work types may be declared where relevant.

### 4.2 Resource Taxonomy

Describes what a worker or compute resource can offer. This layer evolves as the resource landscape changes — capabilities that are rare today may become standard, and new capability types will emerge.

Examples: `gpu_inference`, `local_execution`, `cloud_execution`, `large_context`, `multimodal_input`, `audio_processing`, `video_processing`, `internet_access`, `tool_use`, `batch_processing`, `real_time_processing`

### 4.3 Constraint Taxonomy

Describes the conditions under which work must or should be performed. This is orthogonal to both work type and resource capability — it answers questions about governance, security, cost, privacy, and operational conditions.

**Hard constraints** are non-negotiable requirements. A worker that violates a hard constraint is ineligible, regardless of all other factors.

Examples: `local_only`, `private_data`, `no_cloud`, `no_external_api`, `auditability_required`, `sandbox_restricted`

**Preferences** are optimization targets. They influence ranking among eligible workers but do not eliminate candidates.

Examples: `low_cost`, `low_latency`, `high_reliability`, `background_acceptable`, `premium_quality_preferred`

---

## 5. Task Structure

A task submitted to the broker declares three things:

```yaml
work_type: document_analysis

hard_constraints:
  - local_only
  - private_data

preferences:
  - low_cost
  - low_latency
```

Task authors (directors) declare what they know. The broker validates, enriches, and sometimes overrides.

**Directors are responsible for declaring intent.** They understand the business purpose of the task better than the broker does. They should declare the obvious work type and any known constraints.

**Directors are not responsible for perfect routing metadata.** They will misclassify, forget constraints, use outdated vocabulary, or optimize for task completion over system safety. The broker corrects for this.

**Policy constraints do not rely on task authors.** A director may declare "cloud allowed," but the broker independently checks whether the task content contains private data, secrets, client data, or anything else that makes cloud execution impermissible. The director's declaration is a request. The broker's classification and policy enforcement determine what is actually allowed.

The declaration principle: **explicit when known, inferred when missing, policy-enforced always, logged every time.**

---

## 6. Worker Attributes

Workers declare their attributes using the organizational vocabulary. Workers do not invent their own terms.

Worker attributes are divided into two types:

### 6.1 Intrinsic Attributes

Facts about the worker. Declared by the worker or its administrator. Stable unless the worker's physical capabilities change.

Examples: supported modalities, available hardware, maximum context window, local vs. cloud execution, available tools, supported work types, model identifier

### 6.2 Derived Attributes

Organizational judgments about the worker. Owned and maintained by the organization — not the worker. May change as organizational experience accumulates, pricing changes, or better alternatives emerge.

Examples: cost tier, quality tier, trust level, security classification, preferred use cases, suitability scores per work type

**Workers are authoritative about what they are. The organization is authoritative about how it evaluates and uses them.**

A worker can declare that it has a GPU. It cannot declare that it is "low cost" or "high quality." Those are organizational assessments.

---

## 7. Vocabulary Governance

The organization owns all vocabulary definitions. Workers declare values within that vocabulary.

This is non-negotiable. If workers define their own capability terms, interoperability breaks down as the worker pool diversifies. A shared vocabulary is what makes the broker's matching logic durable across a heterogeneous resource landscape.

**Vocabulary change process:**
- Engineering Director proposes additions or changes to any vocabulary layer
- President Agent reviews for consistency with architectural principles
- CEO approves before any change takes effect
- Changes to hard constraint types require Security Steward review before CEO approval

The work taxonomy is expected to be stable. The resource taxonomy will evolve. The constraint taxonomy changes only when governance or security policy changes.

---

## 8. Three-Phase Routing

The broker evaluates every task against the worker registry in three sequential phases. Each phase has a distinct purpose and failure mode.

### Phase 1 — Eligibility

**Question:** Can this worker legally, safely, and technically perform this work?

The broker applies hard constraints and checks intrinsic worker attributes. A worker that fails any eligibility check is excluded from further consideration. There is no partial credit. This phase is binary.

Eligibility checks include: hard constraint compliance (local_only, private_data, etc.), independent broker policy enforcement (data classification, sandbox restrictions), and minimum technical capability (does the worker support the declared work type at all).

### Phase 2 — Suitability

**Question:** How well does this worker perform this type of work?

Among eligible workers, the broker scores capability fit using the work taxonomy and the worker's declared intrinsic attributes and organizational derived attributes (suitability scores, quality tier). A worker that is technically eligible but has no relevant experience in the declared work type scores low.

### Phase 3 — Optimization

**Question:** Among eligible, capable workers, which should we prefer?

The broker ranks candidates using the task's declared preferences and the worker's derived performance attributes (cost tier, latency profile, current availability, reliability). This is where soft tradeoffs are resolved.

---

## 9. Broker Outcomes

The broker returns exactly one of four outcomes. These are categorically distinct and require different responses.

| Outcome | Meaning | Response |
|---|---|---|
| **Assigned** | An eligible worker was found and selected | Task dispatched; execution begins |
| **Queued** | Eligible workers exist in the registry but none are currently available | Task enters pending state; broker retries when capacity becomes available |
| **Escalated** | Task has been queued beyond the configurable pending threshold | President Agent notified; President decides to wait, reprioritize, modify task, acquire resources, or bring to CEO |
| **Rejected** | No eligible worker exists anywhere in the registry | Task returned with structured explanation; this is an architectural gap requiring organizational action |

**Queued ≠ Rejected.** The distinction matters. A queued task is waiting for a worker that exists. A rejected task is waiting for a worker that does not exist. The organizational response is completely different.

---

## 10. Hard Constraint Enforcement

Hard constraints are never relaxed automatically. Ever.

If the broker cannot find an eligible worker because hard constraints eliminate the entire worker pool, the outcome is Rejected — not a quietly degraded assignment to an ineligible worker. Routing a task that declared `local_only` to a cloud worker because no local worker was available is a security and governance failure, not an acceptable fallback.

Constraint relaxation is an explicit task-level policy, declared at task creation time:

```yaml
constraint_relaxation_permitted: true
relaxable_constraints:
  - low_latency
```

Only preferences may appear in the relaxable list. Hard constraints may never appear in the relaxable list. The broker rejects any task that attempts to mark a hard constraint as relaxable.

Governance and security constraints may only be weakened by explicit CEO authorization — not by broker logic, not by director declaration, not by scheduling pressure.

---

## 11. Escalation

When a task remains in Queued state beyond a configurable threshold, the broker escalates to the President Agent with a structured notification:

- Task identifier and work type
- How long the task has been pending
- Which workers are eligible (by class, not by name)
- Why none are currently available
- Options available to the President Agent

The President Agent may: continue waiting, reprioritize the task, direct the task be modified (with CEO approval if hard constraints need changing), recommend resource acquisition to the CEO, or return the task to the originating director with context.

The President Agent does not automatically resolve escalations. Escalations that require new resources or constraint changes go to the CEO.

---

## 12. Audit Requirements

Every broker decision must be logged. The log record must contain:

- Task identifier, work type, declared hard constraints, declared preferences
- Broker-inferred or broker-overridden metadata (with reason for each override)
- Policy constraints applied (independent of task declaration)
- Phase 1 result: eligible workers (by class), excluded workers and reason for exclusion
- Phase 2 result: suitability scores per eligible worker
- Phase 3 result: ranked candidates with preference scores
- Final outcome: Assigned / Queued / Escalated / Rejected
- If Assigned: selected worker, confidence score, reason for selection
- If Rejected: explanation of which registry gap caused rejection
- Timestamp and request identifier

The Security Steward has autonomous read access to all broker audit logs. No broker decision is opaque.

---

## 13. Engineering Director Deliverables — Phase 2

The first Engineering Director deliverable for INI-011 is the **Worker Capability Taxonomy**. This document must define:

1. **Work taxonomy** — the initial set of work type definitions, with descriptions precise enough to classify real organizational tasks unambiguously. Include: name, description, examples of tasks that qualify, examples of tasks that do not qualify (boundary cases).

2. **Resource taxonomy** — the initial set of intrinsic worker attribute definitions. Include: name, description, what it means for routing (which phase it affects), and how it is declared.

3. **Constraint taxonomy — hard constraints** — the initial set of hard constraint definitions. Include: name, description, what makes a worker ineligible under this constraint, and whether broker policy enforcement is required independently of task declaration.

4. **Constraint taxonomy — preferences** — the initial set of preference definitions. Include: name, description, which derived worker attributes it maps to, and how it affects Phase 3 ranking.

5. **Derived attribute taxonomy** — the initial set of organizational assessment categories. Include: name, description, how it is assessed, who updates it, and update frequency.

6. **Vocabulary governance proposal** — a proposed process for adding, changing, or retiring terms in each vocabulary layer, including authority levels required for each change type.

The taxonomy must be precise enough that a new director, reading it for the first time, can correctly classify any task the organization is likely to generate — and a new worker administrator can correctly declare a worker's attributes — without asking for clarification.

**Definition of done for Phase 2:** CEO reviews and approves the taxonomy. Engineering Director may not begin Phase 3 (routing implementation, node onboarding) until the taxonomy is approved.

---

## 14. What This Architecture Does Not Cover

The following are out of scope for the taxonomy phase and will be addressed in subsequent proposals:

- Broker implementation (where it runs, how it integrates with the dispatcher, what technology it uses)
- Worker onboarding process (how new workers are added to the registry)
- Node selection (which hardware to acquire or provision first)
- Communication channel between broker and remote workers
- HMAC authentication for remote workers (required pre-condition for any remote node — see PROP-002 § 4.4, which remains valid)
- Internal economy, resource allocation, cost tracking

These are Phase 3 and beyond. None of them can be designed well until the taxonomy exists.

---

## 15. Principles Summary

For use as a design checklist during taxonomy development and future implementation:

1. The abstraction layer is the product. Compute resources are implementation details.
2. Route by capability requirement, never by node identity.
3. Three vocabularies, kept separate: work taxonomy, resource taxonomy, constraint taxonomy.
4. Hard constraints gate. Preferences optimize. Never conflate them.
5. Explicit when known, inferred when missing, policy-enforced always, logged every time.
6. Workers are authoritative about what they are. The organization is authoritative about how it evaluates and uses them.
7. The organization owns all vocabulary. Workers declare values within it.
8. Hard constraints are never relaxed automatically.
9. Queued ≠ Rejected. They are different problems requiring different responses.
10. Every broker decision is auditable. No routing choice is opaque.
11. Stable abstractions sit above changing implementations. The work taxonomy outlasts any hardware generation.

---

*President Agent — 2026-06-06 (v1.0)*  
*Architectural direction: CEO, 2026-06-06*  
*Status: Approved for Engineering execution*
