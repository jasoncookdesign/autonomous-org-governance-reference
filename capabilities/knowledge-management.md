# Capability Policy: Knowledge Management

**Status:** Active  
**Version:** 1.1  
**Review date:** 2026-07-31  
**Authorized by:** CEO  
**Last updated:** 2026-06-09 (INI-038 — Wiki Knowledge Architecture)

## Purpose

Grants the Knowledge Director authority to ingest airlock-cleared artifacts, classify and organize KB content, apply retention policy, and maintain the knowledge base in a well-structured and retrievable state.

## Maximum Authority Level

Level 5 — Stage for archival operations (preparing content for archival)  
Level 6 — Execute for ingestion, classification, reorganization, and archival per approved retention schedule

Deletion is explicitly excluded from this capability. Deletion requires separate CEO approval per item.

## Allowed Roles

- Knowledge Director
- Temporary task agents authorized by President Agent operating within Knowledge Director scope

## Allowed Systems

| System | Access Type | Conditions |
| --- | --- | --- |
| KB filesystem (`/knowledge/`) | Read / Write / Reorganize | Freely permitted |
| KB inbox (`/knowledge/inbox/`) | Read / Write / Route | Freely permitted |
| KB archive (designated archive folder) | Write (archival) | Per approved retention schedule only |
| Google Drive (designated digital organization folder) | Write (mirror) | For CEO read access; sync only |

## Allowed Data Classes

- Class A (Public) — no retention limit
- Class B (Internal) — no retention limit
- Class C (Sensitive) — 180-day retention; archive after; deletion requires CEO approval
- Class D (Restricted) — NOT permitted in KB under any circumstances; escalate immediately if encountered

## KB Structure

The KB is organized as a **compiled wiki** (per INI-038). Raw artifacts are never stored directly in the vault. Instead, the Knowledge Director reads each artifact, synthesizes its content, and encodes it as one or more interlinked wiki pages. The vault accumulates knowledge; it does not accumulate files.

```
/knowledge/
  SCHEMA.md        ← KD operating protocol; read first each session
  index.md         ← content catalog by page type
  log.md           ← append-only operational record
  /entities/       ← named persistent things (projects, tools, orgs, people)
  /concepts/       ← recurring patterns, principles, architectural abstractions
  /synthesis/      ← cross-cutting analyses, comparisons, theses
  /sources/        ← distilled summaries of ingested raw artifacts
  /decisions/      ← organizational decision records
  /operations/     ← runbooks and procedures
  /inbox/          ← staging area for artifacts pending triage (unchanged)
  /archive/        ← archival destination for retired content and aged Class C content
```

The Knowledge Director reads `SCHEMA.md` and `index.md` at the start of every vault session. Full page templates, ingest/query/lint workflows, cross-reference conventions, and log format are defined in `SCHEMA.md`.

## Ingestion Rules

1. An artifact may be ingested without per-item CEO approval if it has cleared an approved airlock channel (documented by Operations Director manifest).
2. An artifact that has not cleared an approved channel may not be ingested. Escalate to CEO.
3. Class D artifacts may not be ingested under any circumstances. Escalate immediately.
4. Inbox items must be classified and routed within 48 hours of arrival. If classification cannot be determined within 48 hours, escalate to CEO.
5. Ingestion produces wiki pages — source summaries, entity pages, concept pages, or decision records — not raw artifact copies. See `SCHEMA.md` ingest workflow for the full 9-step procedure.

## Retention Enforcement

Retention policy applies to **source artifacts** — the original documents cleared through the airlock. It does not apply to generated wiki pages (source summaries, concept pages, entity pages, synthesis pages, etc.), which are the vault's compiled knowledge and are never archived on a retention schedule.

| Class | Source Artifact Limit | Action at Limit |
| --- | --- | --- |
| A | None | Retain indefinitely |
| B | None | Retain indefinitely |
| C | 180 days from ingestion | Knowledge Director archives source artifact autonomously per schedule; redacts derived wiki pages to Class B (see below) |
| D | Not permitted | Never ingest; escalate |

**When a Class C source artifact's retention window expires:**

1. Archive the source artifact (inbox copy, Drive mirror, or any other copy held within JasonOS systems).
2. Review all wiki pages classified Class C that were derived from that source. Redact any content that reproduces Class C material verbatim or in a way that re-constitutes the original. Once redacted, reclassify the page to Class B and update its YAML frontmatter.
3. Class C wiki pages are never archived — redaction to Class B is the only permitted outcome. This prevents gaps from forming in the wiki's compiled knowledge over time.
4. Log the review and reclassification outcomes in `log.md`.

**Wiki pages are not raw artifacts and do not inherit the source artifact's retention clock.** The classification declared in a wiki page's YAML frontmatter reflects the sensitivity of the synthesized content, not the classification of the source. A concept page distilled from a Class C source may already be Class B at the time of ingestion if the synthesis does not reproduce the sensitive material — in which case no retention action is required when the source expires.

Archival of source artifacts is autonomous per this schedule. Deletion requires explicit CEO approval regardless of age or classification.

The Knowledge Director must maintain a retention log recording ingestion date, classification, and archival date for all Class C source artifacts, and must record the outcome of any derived-page redactions at the time of archival.

## Wiki Maintenance

The Knowledge Director is responsible for three recurring operational modes:

**Ingest** — When a new source arrives, follow the 9-step ingest workflow in `SCHEMA.md`. A single source may produce multiple wiki pages. The ingest is complete when `index.md` and `log.md` are updated.

**Query** — When answering a question requiring vault knowledge, follow the query workflow in `SCHEMA.md`. If the answer required non-trivial synthesis, file it as a `synthesis/` page so it persists for future queries.

**Lint** — Run a lint pass when directed by CEO or President Agent, or when the vault has grown by 10+ pages since the last lint. Lint checks for orphan pages, stale content, contradictions, missing pages, index gaps, broken cross-references, and classification drift. See `SCHEMA.md` lint workflow.

Log each operation in `log.md` using the format defined in `SCHEMA.md`.

## Cross-Referencing Standard

- Use `[[slug]]` wiki link format for all internal cross-references, where the slug is the filename without extension and without directory prefix
- Index (`knowledge/index.md`) must be updated whenever a new page is created, modified, or moved
- Cross-references that point to non-existent pages are valid — they serve as markers for pages to be created on next ingest
- Tags are declared in each page's YAML frontmatter and reflected in `index.md`
- Full cross-reference and tagging conventions are in `SCHEMA.md`

## Prohibited Actions

- Ingesting artifacts that have not cleared an approved airlock channel
- Ingesting or retaining Class D content
- Deleting any KB artifact without CEO approval
- Exporting KB content to external services, accounts, or recipients
- Accessing source systems represented in KB artifacts (KB content is a derivative artifact, not a portal to the source)
- Modifying policy documents stored in the policy repository

## Spending Limits

No spending authority associated with this capability.

## Maximum Acceptable Loss

**Financial MAL:** $0 (no spending authority associated with this capability).

Information exposure risk: unauthorized export of KB content could expose Class C content (financial exports, business strategy, personal context). The Knowledge Director must treat export requests as high-risk and escalate any ambiguous case.

## Approval Thresholds

| Action | Approval Required |
| --- | --- |
| Ingest airlock-cleared artifact | None |
| Classify and route artifact | None |
| Create or update wiki pages | None |
| Archive content per retention schedule | None |
| Run lint pass | None |
| Restructure vault directory schema or rename conventions | CEO approval |
| Ingest artifact not cleared by airlock | CEO approval |
| Delete any artifact (archival is autonomous; deletion is not) | CEO approval |
| Export KB content externally | CEO approval |
| Retain Class C beyond 180 days | CEO approval |
| Reclassify a page from C to a lower class | CEO approval |

## Audit Requirements

The Knowledge Director must log and report to President Agent:

- Artifacts ingested (source manifest reference, classification assigned)
- Routing decisions
- Archival actions (artifact, reason, date)
- Inbox items escalated for classification uncertainty
- Any content identified as potential Class D after ingestion
- Index updates
