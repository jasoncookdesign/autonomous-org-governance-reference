---
document: INI-034-SCHEMA-003
title: "Resource Registry Schema"
initiative: INI-034
phase: 1
status: Proposed
author: Engineering Director
created: 2026-06-06
updated: 2026-06-06
---

# Resource Registry Schema

## Purpose

The resource registry is a structured catalog of all compute resources available to JasonOS. It provides the President Agent and routing layer with the dimensional data needed to make routing decisions, analyze resource utilization, and support infrastructure investment decisions.

Resources are described across five independent dimensions rather than as monolithic identities. Investment decisions are attribute-level questions — "should I add more local inference capacity?" not "should I buy resource X?" The registry enables this by making each dimension independently queryable.

---

## Design Principles

**Five dimensions, stored independently.** Each dimension describes a distinct aspect of the resource. They are stored and queryable independently so that analysis can slice across dimensions — e.g., "which resources have a fixed-subscription cost model and a rate-limit constraint model?"

**Resources are attribute collections, not monolithic identities.** A resource does not have a single "type" — it has a type, a cost model, a constraint model, a capability profile, and a location. Adding a new resource means populating all five dimensions. Changing a dimension does not create a new resource.

**Registry is maintained by Operations Director.** Engineering Director owns the schema and initial population. Operations Director owns ongoing maintenance — adding new resources, updating versions, retiring deprecated resources.

---

## Resource Identity Fields

Every resource in the registry has identity fields plus values for each of the five dimensions.

| Field | Type | Description |
|---|---|---|
| `resource_id` | string (slug) | Unique stable identifier, e.g., `claude-opus-4-6`, `mac-mini-m4`, `local-ollama` |
| `display_name` | string | Human-readable name |
| `version` | string | Model version, firmware version, or software version (as applicable) |
| `description` | string | Brief description of the resource and its primary use case |
| `status` | enum | `active`, `deprecated`, `unavailable` |
| `registered` | ISO 8601 date | When this resource was added to the registry |
| `updated` | ISO 8601 date | When this resource record was last modified |

---

## Five Dimensions

### Dimension 1 — Resource Type

What the resource fundamentally is.

| Value | Description |
|---|---|
| `subscription_llm` | A language model accessed through a subscription service (e.g., Claude Pro, ChatGPT Plus) |
| `api_llm` | A language model accessed through a usage-billed API |
| `local_llm` | A language model running on local hardware, accessed via a local inference server |
| `local_hardware` | Physical compute hardware used for script execution, automation, or agent orchestration |
| `cloud_api` | A cloud-hosted API service (non-LLM) |
| `gpu_server` | A server with dedicated GPU resources for inference or compute |
| `phone_worker` | A mobile device running local inference or automation (e.g., Termux) |
| `external_service` | A third-party service accessed programmatically (e.g., web search API) |

### Dimension 2 — Cost Model

How resource consumption translates to organizational spending.

| Value | Description |
|---|---|
| `fixed_subscription` | Flat periodic fee regardless of consumption volume |
| `usage_based` | Billed per unit of consumption (tokens, API calls, compute time) |
| `capital_asset` | One-time acquisition cost; ongoing costs are maintenance and power only |
| `hybrid` | Combination of fixed subscription with usage-based overage charges |
| `free` | No direct monetary cost to the organization |

**Cost model drives subscription pressure analysis.** When a `fixed_subscription` resource approaches its rate or context limits, the signal is capacity-constrained — more usage of the same tier is unavailable. When a `usage_based` resource is under pressure, the signal is cost-pressure — more capacity is available but at direct financial cost.

### Dimension 3 — Constraint Model

What kind of bottleneck occurs when this resource is saturated.

| Value | Description |
|---|---|
| `rate_limit` | Maximum number of requests per unit time; excess requests are rejected or queued |
| `context_limit` | Maximum input/output size per invocation; tasks exceeding this cannot be routed here |
| `throughput_limit` | Maximum concurrent or parallel invocations |
| `compute_limit` | Total compute capacity; additional load degrades performance |
| `queue_limit` | Requests queue when busy; latency increases under load but capacity is not lost |
| `availability_limit` | Resource is available only during certain windows (e.g., phone worker when device is charging) |

A resource may have multiple constraint model values. The constraint model determines which analysis questions are relevant — "Am I hitting rate limits?" is only meaningful for `rate_limit` resources.

### Dimension 4 — Capability Profile

The classes of work this resource can perform, drawn from a defined capability vocabulary.

| Capability | Description |
|---|---|
| `executive_reasoning` | Complex multi-step reasoning, judgment under ambiguity, strategic analysis |
| `large_context` | Processing inputs or maintaining context windows larger than 100K tokens |
| `gpu_inference` | GPU-accelerated model inference |
| `local_execution` | Running scripts, automation, and processes on local hardware |
| `low_cost_classification` | Fast, cheap classification, extraction, and simple generation tasks |
| `real_time` | Sub-second response latency suitable for interactive or time-sensitive workloads |
| `media_generation` | Generating images, audio, or video |
| `web_access` | Retrieving and processing content from the web |
| `code_execution` | Executing code in a sandboxed or controlled environment |

A resource may advertise multiple capabilities. The capability profile is the primary input to routing decisions — tasks are routed to resources that advertise the required capability.

### Dimension 5 — Location

Where the resource runs relative to the organizational boundary.

| Value | Description |
|---|---|
| `local` | Runs on hardware owned and operated by the organization (on-premises) |
| `cloud_hosted` | Runs in the organization's cloud environment (managed by the org, hosted externally) |
| `third_party_hosted` | Runs on infrastructure owned and operated by a third party |

Location affects data sensitivity analysis, latency characteristics, dependency risk, and cost structure.

---

## Initial Registry Population

The following resources are registered at Phase 2 launch. Operations Director is responsible for maintaining accuracy.

| Resource ID | Display Name | Type | Cost Model | Constraint Model | Capabilities | Location |
|---|---|---|---|---|---|---|
| `claude-sonnet-4-6` | Claude Sonnet 4.6 | `subscription_llm` | `fixed_subscription` | `rate_limit`, `context_limit` | `executive_reasoning`, `large_context`, `web_access` | `third_party_hosted` |
| `claude-opus-4-6` | Claude Opus 4.6 | `subscription_llm` | `fixed_subscription` | `rate_limit`, `context_limit` | `executive_reasoning`, `large_context`, `web_access` | `third_party_hosted` |
| `claude-haiku-4-5` | Claude Haiku 4.5 | `subscription_llm` | `fixed_subscription` | `rate_limit`, `context_limit` | `low_cost_classification`, `real_time` | `third_party_hosted` |
| `mac-mini-m4` | Mac mini M4 | `local_hardware` | `capital_asset` | `compute_limit` | `local_execution`, `code_execution` | `local` |

**Note:** This table is a placeholder for Phase 2 initial population. Specific model versions, subscription tiers, and local hardware specifications should be confirmed by Operations Director before Phase 2 launch.

---

## Registry Operations

### Adding a Resource

1. Assign a stable `resource_id` slug.
2. Populate all identity fields.
3. Assign values for all five dimensions.
4. Set `status: active`.
5. Commit to governance repo.

### Updating a Resource

Minor updates (version bump, status change): update in place, update `updated` date.  
Significant changes (capability profile change, cost model change): create a new resource entry with a new `resource_id` and set the old entry to `deprecated`. This preserves historical accuracy of invocation records that reference the old resource.

### Retiring a Resource

Set `status: deprecated` or `unavailable`. Do not delete — historical invocation records reference this resource by `resource_id`. Deletion would break aggregation queries.

---

## Relationship to Routing Layer (INI-011)

The routing layer (INI-011) reads the resource registry to determine which resources are available and what capabilities they advertise. The routing layer uses the `capability_profile` dimension as the primary routing input.

When a resource is added to the registry with `status: active`, it becomes eligible for routing. When set to `deprecated` or `unavailable`, it is removed from routing consideration but retained for historical analysis.

---

## Notes

**2026-06-06:** Schema produced by Engineering Director as INI-034 Phase 1 deliverable. Five dimensions, initial capability vocabulary, and initial registry population derived from CEO-approved initiative definition. Operations Director assumes registry maintenance responsibility at Phase 2 launch.
