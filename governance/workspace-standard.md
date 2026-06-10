# JasonOS Workspace Architecture Standard

**Document type:** Governance Standard  
**Version:** 1.0  
**Owner:** Knowledge Director  
**Status:** Ratified — CEO 2026-06-09; pilot complete 2026-06-09  
**Created:** 2026-06-09  
**Initiative:** INI-035  

---

## Purpose

This standard defines the execution-layer workspace architecture for JasonOS directorates. It establishes a consistent model for how work is organized, staged, and archived beneath each initiative, making artifacts discoverable and inter-director handoffs reliable.

This standard adds a structured layer beneath the existing governance hierarchy:

```
CEO → President Agent → Directors → Workspaces → Stages → Artifacts
```

It does not alter authority, roles, or policy. It governs how directors organize their operational work product.

---

## Scope and Applicability

This standard applies to:
- All active and future initiative workspaces created after this document is CEO-approved
- All directorates, including pilots (Knowledge Director, Research Director) and future activations

This standard does not require:
- Retroactive restructuring of existing initiative artifacts
- Migration of completed or archived work
- Any changes to the governance repo structure

---

## 1. Canonical Directory Structure

Every directorate workspace lives under `{{SANDBOX_DATA_ROOT}}/workspaces/`. The path structure is:

```
{{SANDBOX_DATA_ROOT}}/workspaces/<directorate>/<initiative-id>/
```

### Full Tree

```
{{SANDBOX_DATA_ROOT}}/workspaces/
└── <directorate>/                        # e.g., knowledge, research, engineering
    └── <initiative-id>/                  # e.g., INI-035
        ├── workspace-map.md              # Required: index of this workspace
        ├── references/                   # Static reference material (read-only in practice)
        │   └── <descriptor>.<ext>
        ├── active/                       # Artifacts currently in use
        │   ├── 01-intake/
        │   ├── 02-analysis/
        │   ├── 03-execution/
        │   ├── 04-review/
        │   └── 05-output/
        └── archive/                      # Superseded or completed artifacts
            └── <stage>-<descriptor>-<version>.<ext>
```

### Directorate Identifiers

Use the lowercase director role name as the directory name:

| Director Role | Directory Name |
|---|---|
| Knowledge Director | `knowledge` |
| Research Director | `research` |
| Engineering Director | `engineering` |
| Operations Director | `operations` |
| Creative Director | `creative` |
| Financial Analysis Director | `financial-analysis` |
| Venture Director | `venture` |
| Security Steward | `security` |
| President Agent | `president` |

### Notes

- The `references/` directory holds materials that inform the work but are not produced by it (e.g., source documents from the Airlock, policy documents, external research inputs). Do not version reference materials — if a source changes, add the new version alongside the original with a date suffix.
- The `active/` directory holds all work-in-progress artifacts, organized by stage.
- The `archive/` directory holds artifacts that are no longer active — either superseded within the initiative or retained at initiative close.

---

## 2. Stage Naming Conventions

### Default Stage Sequence

The default stage sequence is:

| # | Stage | Purpose |
|---|---|---|
| 01 | `intake` | Receive and orient. Review the initiative definition, gather source materials, establish scope, and confirm that prerequisites are met before substantive work begins. |
| 02 | `analysis` | Understand before deciding. Research, synthesize inputs, identify constraints, map decisions, and produce the analytical foundation for execution. |
| 03 | `execution` | Build the deliverable. This is where the primary work product is created — documents drafted, code written, plans constructed. |
| 04 | `review` | Validate before advancing. Self-review, peer review, or President Agent review depending on the initiative's risk level and scope. |
| 05 | `output` | Finalize and hand off. Produce the canonical output artifact, commit to governance repo if required, and prepare the workspace for archival. |

Stages are not required to contain artifacts at all times. An initiative with minimal analysis may have an empty or near-empty `02-analysis/` directory — that is acceptable.

### Directorate Adaptation

Directors may adapt stage names to their domain. For example:

- The Research Director might use `01-discovery`, `02-collection`, `03-synthesis`, `04-review`, `05-recommendation`
- The Engineering Director might use `01-requirements`, `02-design`, `03-implementation`, `04-review`, `05-release`
- The Creative Director might use `01-brief`, `02-outline`, `03-draft`, `04-review`, `05-production`
- The Security Steward might use `01-scoping`, `02-assessment`, `03-findings`, `04-review`, `05-report`

**Adaptation criteria:** A director may adapt stage names without approval if:

1. There are still five or fewer stages (stage count changes require President Agent approval)
2. The logical sequence remains: orient → understand → build → validate → finalize
3. The adapted names are documented in the directorate's `workspace-map.md`

**What requires President Agent approval:**

- Adding more than five stages
- Skipping the review stage for any initiative with external-facing outputs or multi-director dependencies
- Restructuring the stage sequence in a way that inverts the orient-build-validate order

---

## 3. Reference, Active, and Archive Separation

### Reference Zone (`references/`)

**Contains:** Materials that inform the work but are not produced by it.

Examples:
- Source documents forwarded through the Airlock
- Policy documents, standards, or external specifications
- Prior-initiative outputs referenced as inputs
- CEO-provided context documents

**Movement policy:** Reference materials do not move. They are placed here at intake and remain for the life of the workspace. If a reference is superseded by a newer version, add the new file alongside the original rather than replacing it.

**Archival:** Reference materials are deleted at workspace archival unless they are otherwise unreachable (i.e., not committed to the governance repo and not in the KB vault). In that case, they are retained in the workspace archive.

---

### Active Zone (`active/`)

**Contains:** All work-in-progress artifacts organized by stage.

**Movement policy:**
- Artifacts are created inside the relevant stage directory
- When an artifact is superseded by a newer version within the same stage, the prior version moves to `archive/` with its version suffix preserved
- When a stage is complete, artifacts in that stage remain in place — they are not moved to `archive/` until the initiative closes

**What does not belong here:**
- Final output artifacts already committed to the governance repo (those are governed there)
- Reference materials (those belong in `references/`)
- Artifacts from prior completed initiatives (those are in the prior initiative's workspace)

---

### Archive Zone (`archive/`)

**Contains:** Superseded versions of artifacts that were replaced during an active initiative, plus all non-output artifacts at initiative close.

**Movement triggers:**
1. **Mid-initiative:** An artifact is superseded by a newer version (e.g., `execution-draft-v1.md` is replaced by `execution-draft-v2.md`). Move v1 to `archive/` at that time.
2. **Initiative close:** The initiative reaches Done status. All artifacts in `active/` that are not being committed elsewhere move to `archive/`. Output artifacts that are committed to the governance repo or KB vault may be deleted from the workspace or retained as a local copy — director's discretion.

**Archival does not require a formal Archival Record** unless the artifact meets the criteria in the Knowledge Lifecycle Policy (§ Tier 3 — Historical Archive) for KB vault archival. Workspace-local archival is operational housekeeping, not a governance event.

---

## 4. Workspace Map Format (`workspace-map.md`)

Every workspace must have a `workspace-map.md` at its root. This file is the single point of orientation for anyone entering the workspace cold.

### Required Fields

```markdown
# Workspace Map — <Initiative ID>

**Initiative:** <ID and title>
**Lead director:** <Director role>
**Status:** <Active | Complete | Archived>
**Current stage:** <Stage name>
**Last updated:** <YYYY-MM-DD>

---

## Active Artifacts

List the artifacts currently being worked on, one line each:

- `active/03-execution/<filename>` — <one-line description>
- `active/04-review/<filename>` — <one-line description>

## Key References

List the reference materials that matter for this initiative:

- `references/<filename>` — <one-line description>

## Stage History

Brief log of stage transitions. Add a line when a stage completes.

| Stage | Completed | Notes |
|---|---|---|
| 01-intake | YYYY-MM-DD | |
| 02-analysis | — | |

## Notes

Anything else a director or the President Agent should know to orient quickly.
```

### Maintenance Expectation

The workspace-map.md should be updated:
- When a stage transitions (mark the completed stage and update Current stage)
- When a major artifact is added or superseded
- At initiative close (update Status to Complete)

It does not need to be updated for every minor file change. The goal is that a director or the President Agent can read this file and understand the state of the initiative in under two minutes.

---

## 5. Artifact Naming Conventions

### Standard Pattern

```
<stage>-<descriptor>-<version>.<ext>
```

| Component | Rules | Examples |
|---|---|---|
| `<stage>` | The stage the artifact belongs to, without the numeric prefix | `intake`, `analysis`, `execution`, `review`, `output` |
| `<descriptor>` | Lowercase, hyphen-separated, human-readable description | `scope-notes`, `draft-standard`, `findings-summary` |
| `<version>` | `v` followed by an integer. Start at `v1`. | `v1`, `v2`, `v3` |
| `<ext>` | File extension | `.md`, `.pdf`, `.py`, `.json` |

### Examples

```
intake-source-material-v1.pdf
analysis-constraints-map-v1.md
execution-draft-standard-v1.md
execution-draft-standard-v2.md    ← v1 moved to archive/ when v2 created
review-president-feedback-v1.md
output-workspace-standard-v1.md
```

### Reference File Naming

Reference files do not follow the stage prefix convention. Use a descriptive name with an optional date suffix for versioned source material:

```
icm-proposal-2026-06-09.pdf
governance-manual-v1.md
```

### Special Files

`workspace-map.md` is always named exactly `workspace-map.md` — no stage prefix, no version suffix.

### Sort Order

The `<stage>-` prefix on active artifacts ensures that files sort by stage in directory listings, providing natural orientation when browsing the workspace.

---

## 6. Handoff Criteria

A stage is considered complete when its exit criteria are met. These criteria are intentionally lightweight — the goal is clarity at handoff, not process overhead.

### Default Exit Criteria by Stage

**01-intake complete when:**
- Source materials are in `references/`
- Initiative scope and expected outputs are understood
- Any blockers or dependencies are identified
- `workspace-map.md` exists and is filled in

**02-analysis complete when:**
- The analytical foundation for execution is documented (even if brief)
- Key decisions identified in analysis are recorded (either resolved or flagged for review)

**03-execution complete when:**
- The primary deliverable exists in `active/03-execution/` or `active/04-review/`
- The artifact is substantively complete (not necessarily final — review may require revisions)

**04-review complete when:**
- The reviewing party (self, peer director, or President Agent, as appropriate) has confirmed the deliverable meets the initiative's definition of done
- Any review feedback has been addressed or explicitly deferred
- If the initiative scope requires CEO approval, that approval has been obtained or a handoff to the CEO has been formally made

**05-output complete when:**
- Final artifact is committed to its destination (governance repo, KB vault, or other specified location)
- `workspace-map.md` is updated to reflect Complete status
- Archival criteria have been evaluated (see § 7)

### Determining Who Reviews

| Initiative type | Default reviewer |
|---|---|
| Single-director, internal artifact | Director self-review |
| Multi-director dependency | Lead director + receiving director |
| Governance document (policies, standards) | President Agent, then CEO |
| External-facing or public output | President Agent, then CEO |

---

## 7. Archival Criteria

### When a Workspace Becomes Archival

A workspace transitions from active to archived when the initiative is marked Done in the portfolio. This is the triggering event — not the completion of the output stage.

### What Gets Kept

| Artifact type | Disposition |
|---|---|
| Final output artifact (committed to governance repo) | May be deleted from workspace — canonical copy lives in governance repo |
| Final output artifact (committed to KB vault) | May be deleted from workspace — canonical copy lives in vault |
| Intermediate working artifacts (drafts, analysis notes) | Move to `archive/` within the workspace |
| Reference materials (in `references/`) | Delete unless not available elsewhere |
| `workspace-map.md` | Retain in place; update Status to Archived |

### What Gets Deleted

- Duplicate copies of artifacts already committed to governance repo or KB vault
- Empty stage directories
- Temporary or scratch files clearly not intended as records

### Final Artifact Destination

Final artifacts follow this routing at initiative close:

1. **Governance documents** (standards, policies, charters, procedures) → committed to `{{REPO_NAME}}` repo under the appropriate directory
2. **Reference knowledge** (primers, research outputs, domain knowledge) → KB vault (`{{SANDBOX_DATA_ROOT}}/knowledge/jasonos-vault/`) per the Knowledge Lifecycle Policy
3. **Operational records** (logs, audit trails, incident records) → KB vault or governance repo per the artifact type
4. **Deliverables with no ongoing reference value** → delete after initiative close; no archival required

### Workspace Archive Retention

Archived workspaces at `{{SANDBOX_DATA_ROOT}}/workspaces/<directorate>/<initiative-id>/` are retained indefinitely unless the CEO explicitly authorizes deletion. They are not actively indexed but remain accessible.

---

## 8. Initiative File Pointer

The portfolio initiative file (in `{{REPO_NAME}}/portfolio/<INI-xxx>.md`) must include a `workspace_path:` field in its YAML front matter when a workspace has been created.

### Format

```yaml
---
id: INI-035
title: "ICM Workspace Architecture — Standard and Pilot"
status: Current
...
workspace_path: {{SANDBOX_DATA_ROOT}}/workspaces/knowledge/INI-035
---
```

### Policy

- The field is added when the workspace is created, not when the initiative is first filed
- If an initiative does not yet have a workspace (e.g., it is in Backlog), the field is omitted
- The `workspace_path:` value is always the absolute path to the initiative workspace root
- This field creates a single navigable pointer from governance state to operational state — no other cross-reference is required

---

## 9. Setting Up a Compliant Workspace

A director activating a new workspace should complete these steps:

1. Create the directory structure:
   ```
   mkdir -p {{SANDBOX_DATA_ROOT}}/workspaces/<directorate>/<initiative-id>/{references,active/{01-intake,02-analysis,03-execution,04-review,05-output},archive}
   ```
   *(Adapt stage names per § 2 if using domain-specific stages.)*

2. Create `workspace-map.md` at the workspace root using the template in § 4.

3. Add the `workspace_path:` field to the portfolio initiative file (per § 8).

4. Begin work in `active/01-intake/`.

---

## Appendix: Workspace Map Template

```markdown
# Workspace Map — <INI-XXX>

**Initiative:** <INI-XXX — Title>
**Lead director:** <Director role>
**Status:** Active
**Current stage:** 01-intake
**Last updated:** YYYY-MM-DD

---

## Active Artifacts

- *(none yet)*

## Key References

- *(none yet)*

## Stage History

| Stage | Completed | Notes |
|---|---|---|
| 01-intake | — | |
| 02-analysis | — | |
| 03-execution | — | |
| 04-review | — | |
| 05-output | — | |

## Notes

*(Add anything a new reader needs to orient quickly.)*
```

---

## Flagged Decisions

The following decisions involve two or more reasonable approaches. They are documented here for President Agent and CEO review before this standard is approved.

---

**FLAG-01: Top-level path is `/workspaces/` not a directorate-specific root**

The chosen structure is `{{SANDBOX_DATA_ROOT}}/workspaces/<directorate>/<initiative-id>/`, placing all workspaces under a single `workspaces/` root.

*Alternative considered:* Placing workspaces under directorate-specific roots (e.g., `{{SANDBOX_DATA_ROOT}}/knowledge/workspaces/<ini>/`, `{{SANDBOX_DATA_ROOT}}/research/workspaces/<ini>/`), which would co-locate each directorate's operational data with any other directorate-specific storage.

*Reason for choice:* A single `workspaces/` root makes cross-directorate discovery straightforward for the President Agent and avoids requiring knowledge of where each directorate organizes its data. The directorate subdirectory preserves ownership clarity.

*If the President Agent or CEO prefers directorate-rooted paths, this can be changed before the pilot begins without affecting the rest of the standard.*

---

**FLAG-02: Archive zone is within the workspace, not a separate top-level archive**

Archived artifacts from a workspace remain inside that workspace at `<workspace-root>/archive/`, not moved to a separate organizational archive directory.

*Alternative considered:* A top-level `{{SANDBOX_DATA_ROOT}}/workspaces/archive/<directorate>/<initiative-id>/` path for completed workspaces, mirroring the KB vault's Tier 3 structure.

*Reason for choice:* Keeping the archive co-located with the workspace preserves the relationship between active artifacts and their history. A separate archive directory adds navigation complexity without clear benefit at current organizational scale. If the number of completed workspaces grows substantially, consolidating them under a top-level archive path could become worthwhile — that would be a future amendment to this standard.

---

**FLAG-03: Five-stage default with adaptation allowed without approval (within constraints)**

*Status: Deferred to INI-036 — decision will be grounded in pilot experience before applying to less-structurally-oriented directors.*

The adaptation rule is approved as-is for the Knowledge and Research pilot. Whether the flexibility level (rename without approval, count ≤5, logical sequence preserved) holds for Creative, Financial Analysis, Venture, and Engineering Directors will be evaluated in INI-036 based on observed pilot behavior. Standard may be amended at that time.

---

**FLAG-04: workspace-map.md is not a formal governance event**

Updates to `workspace-map.md` are operational housekeeping — they are not required to be committed to the governance repo or logged as governance events. Only the final output artifacts, if they are governance documents, receive formal governance treatment.

*Alternative considered:* Requiring each workspace-map update to be committed, creating a traceable history of workspace state changes in git.

*Reason for choice:* Git-committing every workspace-map update would add process overhead that is likely to cause directors to stop maintaining the file. The goal is that the file is accurate and current — that goal is better served by keeping maintenance lightweight. If audit traceability of workspace state becomes a requirement, that should be addressed as a separate initiative (potentially with tooling support).

---

**FLAG-05: No maximum workspace size or artifact count defined**

This standard does not set limits on the number of artifacts per stage or per workspace.

*Alternative considered:* Defining soft limits (e.g., no more than 10 active artifacts per stage) to encourage discipline and prevent workspace sprawl.

*Reason for choice:* At current organizational scale, artifact discipline is better served by the workspace-map requirement (which makes sprawl visible) than by hard limits. If workspace sprawl becomes a recurring problem post-pilot, a limit policy could be added as an amendment.

---

*End of document*
