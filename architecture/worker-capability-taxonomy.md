---
document: INI-011-ARCH-002
title: "Worker Capability Taxonomy"
initiative: INI-011
phase: 2
status: Approved — CEO 2026-06-07
author: Engineering Director
reviewed_by: President Agent
created: 2026-06-07
updated: 2026-06-07
approved: 2026-06-07
approved_by: CEO
reference_architecture: architecture/capability-broker-architecture.md (ARCH-001)
---

# Worker Capability Taxonomy

## Purpose

This document defines the three organizational vocabularies that govern how the capability broker matches work to compute resources. It is the Phase 2 deliverable for INI-011 (Distributed Compute Architecture), produced per ARCH-001 § 13.

No routing implementation, node onboarding, or Phase 3 work may begin until the CEO approves this taxonomy. This is the architectural foundation — all downstream design depends on the vocabulary defined here.

---

## Scope

This document defines and governs five components:

1. **Work Taxonomy** — the kinds of work the organization performs (consolidated from `metrics/work-taxonomy.md`)
2. **Resource Taxonomy** — the intrinsic attributes a compute worker can declare
3. **Constraint Taxonomy — Hard Constraints** — eligibility gates that cannot be relaxed automatically
4. **Constraint Taxonomy — Preferences** — optimization targets that influence worker ranking
5. **Derived Attribute Taxonomy** — organizational assessments of worker fitness and performance
6. **Vocabulary Governance** — authority levels and process for changing any vocabulary layer

---

## Design Principles

These principles apply across all vocabulary layers.

**Three vocabularies, kept separate.** Work type, resource capability, and routing constraints are independent axes. Conflating them would make the routing logic brittle and the audit logs uninterpretable. Every architectural decision that touches the vocabulary must preserve this separation.

**Workers declare what they are. The organization declares how it evaluates them.** Intrinsic attributes are facts about the worker — declared by the worker or its administrator. Derived attributes are organizational judgments — owned and maintained by the organization, not by the worker.

**The organization owns all vocabulary. Workers declare values within it.** Workers do not invent their own capability terms. Shared vocabulary is what makes broker matching logic durable as the worker pool diversifies.

**Hard constraints gate. Preferences optimize. Never conflate them.** A hard constraint eliminates ineligible workers before any scoring begins. A preference ranks eligible workers against each other. A routing outcome that violated a hard constraint to satisfy a preference is a governance failure.

**Vocabulary is stable at the top level, evolving at the detail level.** Top-level categories in each vocabulary are small in number, organizationally meaningful, and expected to survive for years. Detail-level terms may be added, revised, or retired as the organization matures — but only when a demonstrated need exists.

---

## Section 1 — Work Taxonomy

### 1.1 Purpose

The work taxonomy classifies what kind of work each task represents. Directors declare work type at task creation. The routing layer normalizes it against this taxonomy. Accurate classification is the primary signal for architectural constraint detection.

This taxonomy is consolidated from `metrics/work-taxonomy.md` (INI-034 Phase 1 deliverable). That document remains the authoritative reference for metrics framework use. This section governs routing layer use of the same taxonomy.

### 1.2 Top-Level Work Types

These eight categories are the stable foundation. They are not expected to change. Additions require President Agent review and CEO approval.

| Work Type | Description |
|---|---|
| `governance` | Policy creation, constitutional amendments, role definition, compliance review, organizational rule-making |
| `strategy` | Goal-setting, portfolio planning, initiative prioritization, organizational direction |
| `research` | Information gathering, market analysis, technical investigation, competitive analysis, fact-finding |
| `analysis` | Synthesis and interpretation of existing information — data analysis, financial modeling, decision support, evaluation |
| `implementation` | Building, coding, configuring, deploying, automating, constructing systems or artifacts |
| `operations` | Running, monitoring, maintaining, supporting, and administering ongoing systems and processes |
| `content` | Creating, editing, or producing communications, documents, media, or other output artifacts |
| `administration` | Scheduling, coordination, record-keeping, routing, housekeeping tasks that support the organization's function |

### 1.3 Subcategories

Subcategories provide routing granularity when top-level types are insufficient for differentiation. The routing layer may use subcategory values to score suitability more precisely. The full subcategory set is defined in `metrics/work-taxonomy.md`.

**Routing relevance by subcategory type:**

| Subcategory | Routing Significance |
|---|---|
| `research.technical` | May require web access or large-context capability |
| `analysis.financial` | Financial data sensitivity — may trigger `private_data` hard constraint |
| `implementation.coding` | Generally requires executive reasoning; code execution capability for testing |
| `implementation.automation` | May be satisfied by local execution worker rather than LLM |
| `implementation.infrastructure` | Requires local execution capability; often requires elevated trust |
| `content.media` | Requires media generation capability; LLM routing is inappropriate |
| `operations.monitoring` | Classification-class work; candidate for low-cost or local worker routing |

### 1.4 Work Type Boundary Cases

The following illustrate where classification requires judgment. Included so new directors can classify correctly without asking for clarification.

| Task | Correct Work Type | Not This | Reason |
|---|---|---|---|
| Drafting a governance policy update | `governance` | `content.writing` | The output is a governance artifact, not a communication |
| Summarizing a research report for the CEO | `content.writing` | `research` | Research is complete; the work is writing |
| Running a Python script to aggregate metrics | `implementation.automation` | `operations.monitoring` | The work is building/running automation, not observing state |
| Reviewing a pull request for correctness | `implementation.coding` | `analysis` | Code review is a form of implementation-class work |
| Evaluating three cloud vendors for cost | `analysis.decision` | `research` | The research is complete; the work is evaluating and comparing |
| Classifying incoming airlock items | `operations.monitoring` | `administration` | Classification is a monitoring/triage function within operations |

---

## Section 2 — Resource Taxonomy (Intrinsic Attributes)

### 2.1 Purpose

Intrinsic attributes describe facts about a compute worker. They are stable unless the worker's physical capabilities change. Workers declare intrinsic attributes; the organization validates them. The routing layer uses intrinsic attributes during Phase 1 (Eligibility) and Phase 2 (Suitability) evaluation.

Intrinsic attributes answer "can this worker do this?" Derived attributes (Section 5) answer "how good is this worker at this, and at what cost?" These are never conflated.

### 2.2 Intrinsic Attribute Definitions

#### 2.2.1 `resource_type`

What the worker fundamentally is. Drawn from the Resource Registry Schema (`metrics/resource-registry-schema.md`).

| Value | Routing Implication |
|---|---|
| `subscription_llm` | Fixed-cost; rate-limited; third-party hosted; not suitable for `local_only` tasks |
| `api_llm` | Usage-billed; rate-limited; third-party hosted; not suitable for `local_only` tasks |
| `local_llm` | Runs on org hardware; eligible for `local_only` tasks; context window varies by model |
| `local_hardware` | Physical compute for script execution and orchestration; no LLM inference unless a local model is loaded |
| `gpu_server` | Dedicated GPU resources; suitable for inference acceleration and media generation |
| `phone_worker` | Mobile device; limited context; suitable for lightweight classification and local inference |
| `cloud_api` | Third-party hosted; suitable for specific non-LLM capabilities (e.g., search, OCR) |
| `external_service` | Third-party service; not org-controlled; data leaves the org boundary |

**Routing gate:** `local_only` hard constraint eliminates all `subscription_llm`, `api_llm`, `cloud_api`, `external_service`, and any `cloud_hosted` resource types. Checked during Phase 1 Eligibility.

#### 2.2.2 `capability_profile`

The set of capability classes this worker can perform. A worker may advertise multiple capabilities. The capability profile is the primary routing input.

| Capability | Description | Phase Applied |
|---|---|---|
| `executive_reasoning` | Complex multi-step reasoning, judgment under ambiguity, strategic analysis, nuanced writing | Phase 2 (Suitability) |
| `large_context` | Processing inputs or maintaining context windows larger than 100K tokens | Phase 1 (Eligibility) — tasks requiring large context must route here |
| `gpu_inference` | GPU-accelerated model inference; dramatically faster for large batches | Phase 2 (Suitability) |
| `local_execution` | Running scripts, automation, and processes on org-controlled hardware | Phase 1 (Eligibility) — `local_only` tasks require this |
| `low_cost_classification` | Fast, cheap classification, extraction, tagging, and simple generation | Phase 2 (Suitability) — preferred over executive resources for classification work |
| `real_time` | Sub-second response latency suitable for interactive or time-sensitive workloads | Phase 1 (Eligibility) — `real_time_required` tasks must route here |
| `media_generation` | Generating images, audio, or video | Phase 1 (Eligibility) — media tasks cannot be routed elsewhere |
| `web_access` | Retrieving and processing content from the live web | Phase 1 (Eligibility) — tasks requiring live web access cannot route to workers without it |
| `code_execution` | Executing code in a sandboxed or controlled environment | Phase 1 (Eligibility) — tasks requiring sandboxed execution must route here |
| `batch_processing` | Processing large volumes of items in bulk without interactive oversight | Phase 2 (Suitability) |
| `multimodal_input` | Accepting images, audio, or structured data as input alongside text | Phase 1 (Eligibility) — multimodal tasks require this |

**Routing logic:** During Phase 1, the broker checks that the worker's capability profile includes all capabilities required by the task. A single missing required capability eliminates the worker. During Phase 2, the suitability scorer uses the profile to rank workers by fit against the task's declared work type.

#### 2.2.3 `location`

Where the worker runs relative to the organizational boundary.

| Value | Description | `local_only` Eligible |
|---|---|---|
| `local` | Runs on hardware owned and operated by the org (on-premises) | Yes |
| `cloud_hosted` | Runs in org-managed cloud environment (hosted externally) | No |
| `third_party_hosted` | Runs on infrastructure owned and operated by a third party | No |

#### 2.2.4 `context_window_tokens`

Maximum input + output tokens per invocation, as an integer. Used during Phase 1 to eliminate workers when a task's estimated token requirement exceeds this limit.

Workers with no context limit constraint may declare `unbounded`. Workers with variable limits (API-dependent) should declare the guaranteed minimum.

#### 2.2.5 `max_concurrent_invocations`

Maximum number of simultaneous invocations this worker can serve. If the worker is at capacity, it is not ineligible — it is queued (per ARCH-001 § 9). Declare as integer. Workers with no enforced concurrency limit declare `unbounded`.

#### 2.2.6 `supported_modalities`

The input modalities this worker accepts. At minimum every worker supports `text`. Workers with multimodal capability declare additional modalities.

| Value | Description |
|---|---|
| `text` | Plain text input (required for all workers) |
| `image` | Static image input |
| `audio` | Audio stream or file input |
| `video` | Video stream or file input |
| `structured_data` | Structured input formats (JSON, CSV, tables) |
| `code` | Source code as a first-class input modality |

#### 2.2.7 `trust_level`

The security classification governing what data may be routed to this worker. Declared by the org based on the worker's authentication posture, location, and operator. Validated by the Security Steward at worker onboarding.

| Value | Description |
|---|---|
| `trusted` | Org-controlled hardware; full data access; no external data boundary |
| `vetted` | Third-party operator with established security posture; reviewed by Security Steward |
| `standard` | Default for third-party hosted services meeting org baseline requirements |
| `restricted` | Limited data access; may only receive data explicitly cleared for this worker |

Hard constraint `private_data` eliminates workers below `vetted`. Hard constraint `local_only` effectively requires `trusted`. The Security Steward must assign trust levels at worker onboarding and may downgrade at any time.

---

## Section 3 — Constraint Taxonomy: Hard Constraints

### 3.1 Purpose

Hard constraints define conditions under which a worker is categorically ineligible for a task. Applied during Phase 1 (Eligibility). A worker that violates any hard constraint is excluded from all further consideration — no suitability scoring, no optimization ranking.

Hard constraints are never relaxed automatically. If no eligible worker exists after hard constraint application, the broker outcome is Rejected. Routing a task that declared `local_only` to a cloud worker because no local worker was available is a security and governance failure, not an acceptable fallback.

Constraint relaxation is an explicit task-level policy declared at task creation. Only preferences may appear in the relaxable list. Hard constraints may never be listed as relaxable. The broker rejects any task attempting to mark a hard constraint as relaxable.

Governance and security constraints may only be weakened by explicit CEO authorization.

### 3.2 Hard Constraint Definitions

#### `local_only`

**Description:** The task must execute on org-owned, locally operated hardware. No data may transit to a third-party hosted or cloud-hosted resource.

**Worker ineligibility condition:** Worker `location` is not `local`, OR `resource_type` is `subscription_llm`, `api_llm`, `cloud_api`, or `external_service`.

**Broker policy enforcement:** Applied independently of task declaration. If task content is classified as containing private data, client data, or privileged organizational secrets, the broker enforces `local_only` regardless of whether the director declared it.

**Examples:** Executing scripts containing credentials; processing client-identifiable data; workloads subject to data residency requirements.

---

#### `private_data`

**Description:** Task content includes data that must not leave the org's trust boundary. Less strict than `local_only` — allows `vetted` third-party workers but excludes `standard` and `restricted` workers.

**Worker ineligibility condition:** Worker `trust_level` is below `vetted`.

**Broker policy enforcement:** Applied when task content contains recognizable private data patterns (PII markers, financial identifiers, client names in confidential context). Director declaration is supplemented by independent broker classification.

**Relationship to `local_only`:** `private_data` does not imply `local_only`. A vetted third-party LLM with appropriate data handling agreements satisfies `private_data`. Declare `local_only` explicitly if stricter enforcement is required.

---

#### `no_external_api`

**Description:** Task execution must not make outbound calls to external APIs or services.

**Worker ineligibility condition:** Worker capability profile includes `web_access`, OR `resource_type` is `external_service`.

**Use case:** Air-gapped execution requirements; tasks where external calls introduce data leakage risk; tasks requiring deterministic output.

---

#### `auditability_required`

**Description:** Every step of task execution must be logged with sufficient detail for the Security Steward to reconstruct the decision process.

**Worker ineligibility condition:** Worker does not support structured audit logging for all invocation steps. At current scale, all registered workers satisfy this — this is a forward-looking gate.

---

#### `real_time_required`

**Description:** Task requires sub-second response latency. Background processing is not acceptable.

**Worker ineligibility condition:** Worker does not advertise `real_time` in its capability profile.

---

#### `min_context_window`

**Description:** Task requires a minimum context window size to process all input without truncation. Declared as a token count integer.

**Worker ineligibility condition:** Worker `context_window_tokens` is less than the declared minimum.

**Declaration format:** `min_context_window: 150000`

---

#### `require_capability`

**Description:** Task explicitly requires one or more capabilities from the capability taxonomy. Capabilities listed here are hard requirements, not preferences.

**Worker ineligibility condition:** Worker capability profile does not include all declared required capabilities.

**Declaration format:**
```yaml
require_capability:
  - media_generation
  - multimodal_input
```

Note: Use named constraints (e.g., `local_only`, `no_external_api`) when they exist. `require_capability` is the general-purpose gate for capabilities without a named constraint.

---

#### `sandbox_restricted`

**Description:** Task originates from or operates within a sandboxed execution environment and may not interact with systems outside that sandbox's permission boundary.

**Worker ineligibility condition:** Worker operates outside the declared sandbox boundary.

**Use case:** Tasks generated within the Cowork sandbox that must not trigger operations on external systems; tasks submitted by temporary agents under restricted authority.

---

### 3.3 Hard Constraint Declaration Format

```yaml
hard_constraints:
  - local_only
  - auditability_required
  - min_context_window: 200000
  - require_capability:
      - code_execution
```

Constraints are ANDed — all must be satisfied. There is no OR relationship between hard constraints.

---

## Section 4 — Constraint Taxonomy: Preferences

### 4.1 Purpose

Preferences are optimization targets. They influence Phase 3 (Optimization) ranking among workers that have already passed Phase 1 and Phase 2. A preference that cannot be satisfied does not eliminate a worker — it scores that worker lower.

Preferences may appear in the task's `constraint_relaxation_permitted` list, meaning the broker may deprioritize them if no workers score well on that dimension.

### 4.2 Preference Definitions

#### `low_cost`

**Description:** Prefer workers with lower direct monetary cost per invocation.

**Derived attribute mapped:** `cost_tier`. Workers with `economy` are ranked above `standard` above `premium`.

**Use case:** Bulk operations, routine classification, tasks where quality variance across cost tiers is negligible.

---

#### `low_latency`

**Description:** Prefer workers with lower response latency.

**Derived attribute mapped:** `latency_profile`. Workers classified `fast` are ranked above `moderate` above `slow`.

---

#### `high_reliability`

**Description:** Prefer workers with lower historical failure and error rates.

**Derived attribute mapped:** `reliability_tier`. Workers classified `high` are ranked above `standard` above `degraded`.

**Use case:** Production workflows; tasks where a retry would be expensive or disruptive.

---

#### `premium_quality`

**Description:** Prefer the highest-capability worker available, regardless of cost.

**Derived attribute mapped:** `quality_tier`. Workers classified `premium` are ranked first.

**Use case:** Executive reasoning tasks; high-stakes content; tasks where output quality directly affects external deliverables.

---

#### `background_acceptable`

**Description:** Task can run in the background; queue position and latency are not priorities. Actively de-prioritizes latency-optimized workers in favor of cost-optimized ones.

**Derived attribute mapped:** `cost_tier` (inverted from `low_latency`).

**Use case:** Scheduled tasks, batch processing, non-urgent analysis.

---

#### `prefer_local`

**Description:** Among eligible workers, prefer locally hosted ones over third-party hosted ones, even when the task does not strictly require local execution.

**Derived attribute mapped:** `location`. Workers with `local` are ranked above cloud and third-party hosted workers.

**Use case:** Tasks with moderate data sensitivity; tasks where the director has a governance preference for local execution without making it a hard requirement.

---

### 4.3 Preference Declaration Format

```yaml
preferences:
  - low_cost
  - background_acceptable
```

Preferences are ranked by declaration order — the first preference listed carries the highest weight in Phase 3 scoring. If no preference is declared, the broker applies the org-default ranking defined in the routing configuration.

---

## Section 5 — Derived Attribute Taxonomy

### 5.1 Purpose

Derived attributes are organizational assessments of how the org evaluates and uses each worker. Not declared by the worker — owned and maintained by the organization. They change as operational experience accumulates, pricing changes, or the worker pool evolves.

The Operations Director maintains derived attribute accuracy. The President Agent flags anomalies when metrics data reveals that derived attributes no longer reflect operational reality.

### 5.2 Derived Attribute Definitions

#### `cost_tier`

The organization's assessment of this worker's cost per unit of useful work.

| Value | Description |
|---|---|
| `economy` | Negligible or near-zero marginal cost (e.g., fixed subscription with headroom, local hardware with no usage billing) |
| `standard` | Moderate cost per invocation; within normal operational budget |
| `premium` | High cost per invocation; reserved for tasks where quality justifies it |

**Assessment method:** Engineering Director calculates approximate cost per 1,000 tokens based on subscription tier and observed utilization. Operations Director updates on subscription changes. Reviewed quarterly.

---

#### `quality_tier`

The organization's assessment of this worker's output quality for the work types it serves.

| Value | Description |
|---|---|
| `premium` | Best available quality for complex reasoning, nuanced writing, and executive-class tasks |
| `standard` | Reliable quality for routine tasks; not appropriate for highest-stakes work |
| `specialist` | Best-in-class for a narrow capability (e.g., a model tuned specifically for classification) |
| `experimental` | Newly onboarded worker; quality not yet validated by org experience |

**Assessment method:** President Agent and director feedback following invocations; periodic review of output samples. Reviewed at each 60-day Capability Audit; may be updated immediately on significant quality change.

---

#### `latency_profile`

Empirical assessment of this worker's response time under typical load.

| Value | Description |
|---|---|
| `fast` | Consistent sub-5s response for typical invocations |
| `moderate` | 5–30s typical response time |
| `slow` | 30s+ typical response time; acceptable only for background or batch work |

**Assessment method:** Measured across sampled invocations during normal operations. Operations Director monitors and updates. Flagged immediately if latency degrades significantly.

---

#### `reliability_tier`

Empirical assessment of this worker's error and failure rate.

| Value | Description |
|---|---|
| `high` | Error rate below 1% across recent invocation sample |
| `standard` | Error rate 1–5%; acceptable for non-critical work |
| `degraded` | Error rate above 5%; should not be primary routing target; President Agent notified |

**Assessment method:** Failure rate calculated from invocation outcome metrics (INI-034). Operations Director monitors and escalates to President Agent when a worker enters `degraded` tier.

---

#### `suitability_scores`

Per-work-type suitability scores reflecting organizational experience with this worker. Used during Phase 2 (Suitability) routing.

**Format:** Map of work taxonomy entries to scores from 0.0 (not suitable) to 1.0 (best available).

```yaml
suitability_scores:
  governance: 0.95
  strategy: 0.90
  research.technical: 0.85
  implementation.coding: 0.80
  operations.monitoring: 0.40
  administration: 0.30
```

**Assessment method:** Initially set by Engineering Director at worker onboarding based on published benchmarks and model characteristics. Refined by President Agent analysis of invocation outcomes over time. Reviewed at each 60-day Capability Audit.

---

#### `security_classification`

The Security Steward's formal classification of this worker's security posture. Distinct from `trust_level` (intrinsic attribute, declared at onboarding) — this is the org's ongoing operational assessment.

| Value | Description |
|---|---|
| `cleared` | Security Steward has reviewed and approved; no restrictions beyond standard policy |
| `conditional` | Approved with specific conditions documented in the worker registry entry |
| `under_review` | Security Steward review in progress; treat as `restricted` until cleared |
| `suspended` | Routing suspended pending incident resolution |

**Assessment method:** Security Steward review at onboarding; ongoing audit of invocation logs. Security Steward may change classification at any time without President Agent mediation.

---

## Section 6 — Vocabulary Governance

### 6.1 Authority Levels

| Change Type | Proposer | Reviewer | Approver | Security Steward Required |
|---|---|---|---|---|
| Add top-level work type | Engineering Director | President Agent | CEO | No |
| Add subcategory (work taxonomy) | Engineering Director | President Agent | CEO | No |
| Deprecate or merge work types | Engineering Director | President Agent | CEO | No |
| Add resource capability | Engineering Director | President Agent | CEO | No |
| Add intrinsic attribute definition | Engineering Director | President Agent | CEO | No |
| Add hard constraint definition | Engineering Director | President Agent | CEO | **Yes — before CEO approval** |
| Modify hard constraint eligibility rule | Engineering Director | President Agent | CEO | Yes |
| Deprecate hard constraint | Engineering Director | President Agent | CEO | Yes |
| Add preference definition | Engineering Director | President Agent | CEO | No |
| Modify preference ranking logic | Engineering Director | President Agent | CEO | No |
| Add derived attribute definition | Engineering Director | President Agent | CEO | No |
| Change derived attribute assessment method | Engineering Director | President Agent | CEO | No |

No change takes effect before all required approvals are documented. Engineering Director may not implement a vocabulary change in advance of approval, even provisionally.

### 6.2 Change Process

1. Engineering Director drafts a proposal: new or modified term, definition, routing implications, and organizational need.
2. For hard constraint changes: route to Security Steward for review first.
3. Route to President Agent (with Security Steward review if applicable).
4. President Agent reviews for consistency with the three-vocabulary separation principle, ARCH-001 design principles, and vocabulary coherence.
5. President Agent forwards to CEO with recommendation.
6. CEO approves or rejects.
7. On approval: Engineering Director updates this document and supporting documents in a single commit.

**Governance test for new terms:** *"If this term disappeared tomorrow, what routing decision would become harder or less accurate?"* If there is no clear answer, the term should not be added.

**Historical record:** Deprecated terms are marked `[DEPRECATED as of YYYY-MM-DD]` with the reason. Terms are never deleted — historical invocation records reference them.

### 6.3 Emergency Vocabulary Freeze

The Security Steward may declare a vocabulary freeze if an active security incident involves vocabulary manipulation or misclassification. During a freeze, no vocabulary changes may be proposed, reviewed, or approved. The freeze lifts when the Security Steward files a clearance report. A freeze may not be permanent.

---

## Section 7 — Initial Worker Registry Entries

Workers registered at Phase 2 taxonomy approval. Cross-referenced against the Resource Registry Schema (`metrics/resource-registry-schema.md`).

| Worker ID | Type | Location | Trust Level | Capabilities | Cost Tier | Quality Tier | Security |
|---|---|---|---|---|---|---|---|
| `claude-sonnet-4-6` | `subscription_llm` | `third_party_hosted` | `vetted` | `executive_reasoning`, `large_context`, `web_access`, `multimodal_input`, `code_execution` | `standard` | `premium` | `cleared` |
| `claude-opus-4-6` | `subscription_llm` | `third_party_hosted` | `vetted` | `executive_reasoning`, `large_context`, `web_access`, `multimodal_input` | `premium` | `premium` | `cleared` |
| `claude-haiku-4-5` | `subscription_llm` | `third_party_hosted` | `vetted` | `low_cost_classification`, `real_time`, `web_access` | `economy` | `standard` | `cleared` |
| `mac-mini-m4` | `local_hardware` | `local` | `trusted` | `local_execution`, `code_execution`, `batch_processing` | `economy` | `specialist` | `cleared` |

**Suitability scores — initial values (Engineering Director, 2026-06-07):**

`claude-sonnet-4-6`: governance 0.95, strategy 0.90, research 0.85, analysis 0.85, implementation.coding 0.80, implementation.automation 0.50, content 0.85, operations.monitoring 0.60, administration 0.50.

`claude-opus-4-6`: Same base as Sonnet; scored higher on tasks requiring maximum reasoning depth where Sonnet is insufficient. Use when `premium_quality` preference is declared.

`claude-haiku-4-5`: operations.monitoring 0.85, administration 0.70, all other types 0.30 or below. Primary routing target for classification, triage, and high-volume low-complexity tasks.

`mac-mini-m4`: implementation.automation 0.90, implementation.infrastructure 0.85, all LLM-class work types 0.0 (not applicable — local hardware does not perform LLM inference). Exclusive routing target for `local_only` tasks and shell/script execution.

---

## Section 8 — Definition of Done

Phase 2 is complete when the CEO reviews and approves this taxonomy document. Engineering Director may not begin Phase 3 work (routing implementation, node onboarding, dispatcher extension) until CEO approval is received and recorded in the INI-011 portfolio document.

**Phase 3 gate conditions (for reference):**

1. This taxonomy is CEO-approved and committed to the governance repo
2. Engineering Director has produced a Phase 3 architecture proposal covering broker implementation, dispatcher extension, and worker onboarding process
3. President Agent has reviewed the Phase 3 proposal
4. CEO has approved Phase 3 scope
5. Security Steward has reviewed any authentication mechanisms proposed in Phase 3 before implementation begins

---

## Notes

**2026-06-07:** Worker Capability Taxonomy produced by Engineering Director as INI-011 Phase 2 deliverable. Vocabulary structure and three-taxonomy separation principle derived from CEO-approved ARCH-001. Work taxonomy section consolidated from `metrics/work-taxonomy.md` (INI-034 Phase 1). Submitted to President Agent for routing to CEO.

**Relationship to PROP-002:** PROP-002 (distributed-compute-phase2-proposal.md) addressed second compute node onboarding and HMAC authentication — a Phase 3 concern per the INI-011 portfolio definition. Its authentication and worker registry design remain valid inputs to Phase 3 planning. PROP-002 Q2–Q4 decisions (node selection, communication channel) do not block this taxonomy.

---

*Engineering Director — 2026-06-07 (v1.0)*
*President Agent — reviewed and forwarded to CEO for approval*
