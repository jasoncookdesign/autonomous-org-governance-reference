---
document: INI-034-SCHEMA-002
title: "Work Taxonomy"
initiative: INI-034
phase: 1
status: Proposed
author: Engineering Director
created: 2026-06-06
updated: 2026-06-06
---

# Work Taxonomy

## Purpose

The work taxonomy provides a stable classification system for organizational work. Every invocation record carries a `work_type` field drawn from this taxonomy. Accurate, consistent classification is the foundation for all constraint analysis — without it, the distinction between capacity constraints, architectural constraints, and transitional constraints becomes unresolvable.

This taxonomy is consumed by the metrics framework. It is populated by directors at task creation and normalized by the routing layer (INI-011). The metrics framework records what it receives — classification correctness is an upstream responsibility.

---

## Design Principles

**Stable top level.** Top-level work types are small in number, organizationally meaningful, and expected to survive for years without modification. They anchor investment and resource decisions. They must not be proliferated for analytical convenience.

**Evolving subcategories.** Subcategories provide analytical richness when needed. They may be added, renamed, or retired as the organization matures — but only when a demonstrated analytical need exists. Anticipated value is not sufficient justification.

**Work type is independent of resource class.** Technical Research is Technical Research whether it is performed by an executive-class or worker-class resource. Conflating work type with resource class would permanently distort the architectural constraint signal. These dimensions are always recorded and analyzed separately.

**Governance test for new categories:** "If this category disappeared tomorrow, what decision would become harder to make?" If there is no clear answer, the category should not be added.

---

## Top-Level Work Types (Stable)

These eight categories are the stable foundation of the taxonomy. They are not expected to change. Additions require President Agent review and CEO approval.

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

---

## Initial Subcategories

Subcategories are illustrative, not exhaustive. They are provided to demonstrate the intended level of granularity and to seed the taxonomy. Additional subcategories should be proposed when analysis reveals a decision need that the top-level category cannot satisfy alone.

### Research

| Subcategory | Description |
|---|---|
| `research.market` | Market landscape analysis, customer research, competitive positioning |
| `research.technical` | Technical investigation, feasibility assessment, architecture research |
| `research.competitive` | Competitive intelligence, product benchmarking, threat analysis |
| `research.regulatory` | Legal, regulatory, and compliance research |

### Analysis

| Subcategory | Description |
|---|---|
| `analysis.financial` | Financial modeling, investment analysis, cost-benefit evaluation |
| `analysis.data` | Statistical analysis, pattern identification, quantitative evaluation |
| `analysis.decision` | Structured decision support, option comparison, tradeoff analysis |

### Implementation

| Subcategory | Description |
|---|---|
| `implementation.coding` | Writing, modifying, or reviewing code |
| `implementation.automation` | Building scripts, workflows, pipelines, or automated processes |
| `implementation.infrastructure` | Configuring systems, provisioning resources, setting up environments |
| `implementation.integration` | Connecting systems, building APIs, implementing data flows |

### Content

| Subcategory | Description |
|---|---|
| `content.writing` | Long-form writing — documents, reports, proposals, articles |
| `content.editing` | Reviewing and improving existing written content |
| `content.communication` | Email drafting, messaging, briefings, presentations |
| `content.media` | Audio, video, image, or design production |

### Operations

| Subcategory | Description |
|---|---|
| `operations.monitoring` | Observing system state, reviewing logs, watching for anomalies |
| `operations.maintenance` | Routine upkeep, dependency updates, configuration hygiene |
| `operations.support` | Responding to incidents, resolving errors, handling requests |

---

## Work Type vs. Resource Class — Formal Separation

Work type classification and resource capability classification are independent axes. The following table illustrates correct classification behavior:

| Work Being Done | Correct Work Type | Resource Used | Analysis Interpretation |
|---|---|---|---|
| Drafting governance policy | `governance` | Executive-class LLM | Normal — governance work requires executive reasoning |
| Classifying task outcomes | `operations.monitoring` | Executive-class LLM | Potential architectural constraint — classification is worker-class work |
| Writing production code | `implementation.coding` | Executive-class LLM | Normal — complex implementation requires executive reasoning |
| Running a static analysis script | `implementation.automation` | Local execution worker | Normal — automation work is well-matched to local execution |
| Summarizing a market report | `research.market` | Executive-class LLM | Normal during formation; potential architectural constraint at scale |

The divergence between declared work type and actual resource consumption profile is the primary signal for architectural constraint detection. This signal only exists if work type is recorded accurately and independently of resource selection.

---

## Classification Responsibilities

| Role | Responsibility |
|---|---|
| Director | Declares work type at task creation. Uses the taxonomy defined here. Responsible for accuracy. |
| Routing Layer (INI-011) | Normalizes and validates the declared work type. Emits `work_type` to the metrics framework as the normalized value. |
| Metrics Framework | Records the normalized work type. Does not modify or override classifications. |
| President Agent | Applies work type distributions in analysis. Identifies classification anomalies. Surfaces architectural constraint signals. |

---

## Governance Procedure for Taxonomy Changes

**Adding a top-level work type:**
1. Engineering Director drafts a proposal documenting the decision need that cannot be served by existing types.
2. President Agent reviews.
3. CEO approval required.

**Adding a subcategory:**
1. Engineering Director drafts a brief proposal applying the governance test.
2. President Agent reviews.
3. CEO approval required.

**Deprecating or merging categories:**
1. Engineering Director proposes with impact analysis on historical records.
2. President Agent reviews.
3. CEO approval required.

No category may be added, changed, or removed without completing this procedure. Historical invocation records must not be retroactively reclassified except via a formally approved reconciliation process.

---

## Notes

**2026-06-06:** Taxonomy produced by Engineering Director as INI-034 Phase 1 deliverable. Top-level categories and governance test derived from CEO-approved initiative definition. Subcategories are initial seed set; expansion requires demonstrated need.
