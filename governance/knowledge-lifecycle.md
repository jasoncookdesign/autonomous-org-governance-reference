# Knowledge Lifecycle Policy

**Version:** 1.0  
**Status:** Active  
**Owner:** Knowledge Director  
**Approved by:** CEO  
**Effective date:** 2026-06-06  
**Review date:** 2026-12-06  
**Governance manual cross-reference:** § Knowledge Base Governance, § Knowledge Lifecycle

---

## Purpose

This policy defines how knowledge enters, is maintained, and exits the JasonOS knowledge base. It establishes a three-tier memory architecture that distinguishes active working knowledge from archived historical context, ensures that agents operate on current and relevant information, and provides the CEO with a clear picture of what the organization knows now versus what it has retained from the past.

---

## Three-Tier Memory Architecture

### Tier 1 — Working Memory

**Scope:** Session-scoped context active within a single Claude session (Cowork, Claude Code, or Claude Chat).

**Characteristics:**
- Ephemeral — exists only for the duration of the session
- No formal governance required
- Never persisted to the KB vault without explicit curation

**Examples:** Intermediate drafts, in-session calculations, conversation history, session-local notes not yet determined to have lasting value.

**Governance action:** None. Content either surfaces to Tier 2 via curation or is discarded at session end.

---

### Tier 2 — Institutional Memory

**Scope:** The active KB vault at `{{SANDBOX_DATA_ROOT}}/knowledge/jasonos-vault/`.

**Characteristics:**
- Actively used by directors and the President Agent for decision-making
- Maintained as current and accurate
- Subject to semi-annual review
- Read/write access per role and capability policy

**Examples:** Architectural primers, domain reference documents, active project knowledge, onboarding materials for new initiatives, CEO-authored strategic context documents.

**Retention rule:** Content remains in Tier 2 as long as it is current, accurate, and actively referenced. Content that is superseded, completed, or no longer relevant moves to Tier 3 or is deleted per the archival criteria below.

**Review cadence:** Semi-annual (every 6 months). Knowledge Director conducts the review, produces a Knowledge Audit Summary, and presents findings to the President Agent. May shift to annual cadence once vault content stabilizes.

---

### Tier 3 — Historical Archive

**Scope:** `{{SANDBOX_DATA_ROOT}}/knowledge/archive/`

**Characteristics:**
- Append-only after archival — content may not be modified once archived
- Retained indefinitely unless CEO explicitly authorizes deletion
- Accessible to directors with read authority but not actively indexed for retrieval
- Each archived item must have an accompanying Archival Record (see template)

**Examples:** Superseded architectural proposals, completed workload inputs, old inbox documents no longer needed for active context, deprecated policy reference docs, historical session context that may have future evidentiary value.

**Governance action:** Knowledge Director files an Archival Record for each item moved to Tier 3. Archival Record is committed to governance repo.

---

## Ingestion Policy

Content enters the KB vault (Tier 2) through two channels:

| Channel | Source | Governing policy |
|---|---|---|
| Airlock inbox | CEO-forwarded emails, documents, attachments | `operations/airlock-monitoring-runbook.md` |
| Director curation | Director-produced artifacts with lasting reference value | Director capability policy + Knowledge Director approval |

**Prohibited ingestion:** Content classified as Class C or D (per § Data Classification in the Governance Manual) may not enter the KB vault without CEO approval. The Knowledge Director is responsible for flagging any ingestion that appears to violate this rule.

---

## Archival Criteria

Content in Tier 2 must be moved to Tier 3 (or deleted) when any of the following conditions are met:

| Criterion | Description |
|---|---|
| Superseded | A newer, authoritative version exists and is active in Tier 2 |
| Initiative complete | Content relates exclusively to a completed initiative; governance repo is the authoritative record |
| No access in review cycle | Content was not referenced by any director in the preceding 6-month review period AND Knowledge Director determines it has no foreseeable active use |
| CEO directive | CEO explicitly directs archival or deletion |
| Data classification change | Content is reclassified to a tier that restricts vault storage |

**Deletion (not archival):** Content may be deleted outright (rather than archived) only if:
- It has no historical or evidentiary value, AND
- CEO approves deletion, AND
- Knowledge Director documents the deletion in the Knowledge Audit Summary

Deletion of content that has been referenced in any committed governance repo document requires CEO approval per instance.

---

## Semi-Annual Review Process

1. **Knowledge Director** conducts a full review of Tier 2 contents against the archival criteria above.
2. Knowledge Director produces a **Knowledge Audit Summary** covering:
   - Items reviewed
   - Items retained (with brief rationale)
   - Items recommended for archival (with archival criteria cited)
   - Items recommended for deletion (CEO approval required)
   - Gaps: knowledge that should exist in Tier 2 but is absent
3. Knowledge Director presents the Summary to the **President Agent**.
4. President Agent reviews and forwards to CEO for acknowledgment. Items requiring CEO action (deletion approvals) are listed explicitly.
5. After CEO acknowledgment, Knowledge Director executes approved archival and deletion actions and files Archival Records for each item archived.

**First review:** Following the initial classification workload (see workload: `workloads/active/knowledge-lifecycle-classification.md`).  
**Subsequent reviews:** Every 6 months from the date of the first completed review.

---

## Roles and Responsibilities

| Role | Responsibility |
|---|---|
| Knowledge Director | Owns KB vault structure, ingestion decisions, semi-annual review, archival execution, Archival Record filing |
| President Agent | Reviews Knowledge Audit Summary before CEO; escalates deletion requests; advises Security Steward on Class C/D content |
| Security Steward | Audits vault classification compliance; may flag content for reclassification |
| CEO | Acknowledges Knowledge Audit Summary; approves deletions; approves Class C/D ingestion |

---

## Vault Directory Structure

```
{{SANDBOX_DATA_ROOT}}/knowledge/jasonos-vault/
  inbox/              ← Airlock-routed content awaiting Knowledge Director triage
  [topic-dirs]/       ← Organized Tier 2 content by domain (Knowledge Director defines)
  index.md            ← Vault index maintained by Knowledge Director

{{SANDBOX_DATA_ROOT}}/knowledge/archive/
  [YYYY-MM]/          ← Archived items organized by archival month
  index.md            ← Archive index maintained by Knowledge Director
```

The inbox is a staging area, not permanent storage. Knowledge Director is responsible for triaging inbox items into topic directories or archiving them within 30 days of arrival.

---

## Archival Record

Each item moved to Tier 3 requires an Archival Record filed using `templates/knowledge-archival-record-template.md`. Records are committed to the governance repo at `audit/knowledge-archival-records/`.

---

## Policy Exceptions

Exceptions to this policy require a Policy Change Request (PCR) filed per § Policy Change Management in the Governance Manual. The Knowledge Director may approve temporary exceptions within their authority ceiling; permanent exceptions require CEO approval.
