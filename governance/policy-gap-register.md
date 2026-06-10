# Policy Gap Register

**Version:** 1.0  
**Owner:** President Agent  
**Status:** Active  
**Last updated:** 2026-06-06

This register is the President Agent's backlog for policy gaps — situations where the organization is operating without adequate written guidance, where policy is ambiguous, or where a near-miss revealed an unaddressed scenario. Gaps are processed deliberately rather than reactively.

CEOs review this register as part of the weekly governance summary. Approval of any resolution requires the standard policy change process.

---

## How to Use This Register

- **Adding a gap:** President Agent appends a new entry using `templates/policy-gap-entry-template.md`. Any director may surface a gap to the President Agent; the President Agent decides whether to register it.
- **Updating a gap:** Entries are append-only within each gap. Add resolution notes; do not overwrite prior content.
- **Closing a gap:** Change status to Resolved and add resolution notes. Do not delete the entry.
- **Priority calibration:** High = operational risk today; Medium = would cause friction or ambiguity in foreseeable scenarios; Low = worth tracking, not time-sensitive.

---

## Open Gaps

---

### GAP-001 — Near-miss tracking not in incident log format

**ID:** GAP-001  
**Date identified:** 2026-06-05  
**Identified by:** President Agent (session review)  
**Status:** Open  
**Priority:** Medium  
**Domain:** governance / security

#### Description

The incident report template (`templates/incident-report-template.md`) and the Sandbox Operating Charter § 21 define incident response for confirmed policy violations. Neither document addresses near-misses — situations where a policy violation was narrowly avoided, an agent reached the edge of its authority without crossing it, or an ambiguous instruction was handled conservatively but revealed a gap.

#### Observed symptom or trigger

Proactive identification during INI-005 execution. During the 2026-06-05 session, several edge-of-authority situations were navigated without incident, but none were logged as near-misses. This means the CEO and Security Steward have no visibility into how often the organization approaches its policy boundaries.

#### Policy documents affected

| Document | Section | Gap |
|---|---|---|
| `templates/incident-report-template.md` | Entire template | No near-miss category or fields |
| `operations/sandbox-operating-charter.md` | § 21 — Incident Response | Severity table starts at Low; no pre-incident category |
| `governance/governance-manual.md` | § Incident Response | Same gap as Charter § 21 |

#### Risk if unaddressed

Near-misses are the leading indicator of future incidents. Without tracking them, the organization cannot identify systemic policy gaps before they produce harm. The Security Steward's weekly review is less effective without near-miss data.

#### Proposed resolution

Add a "Near-Miss" severity level below Low to the incident severity table. Define it as: an event where policy was nearly exceeded but no actual violation occurred, or where an agent encountered genuine ambiguity and escalated conservatively. Update the incident report template to include a near-miss section.

#### Resolution notes

*(Open — no PCR filed yet)*

---

### GAP-002 — Incident log format not standardized across directors

**ID:** GAP-002  
**Date identified:** 2026-06-05  
**Identified by:** President Agent (session review)  
**Status:** Open  
**Priority:** Medium  
**Domain:** governance / operations

#### Description

Each director maintains an operational log, but no standard format is defined. The President Agent reads director summaries, not raw logs — but the Security Steward does review logs directly. Without a standard format, log quality varies, and Security Steward review is less efficient.

#### Observed symptom or trigger

Proactive identification. Director log files exist (verified 2026-06-05) but were created with implicit formatting conventions rather than a defined template.

#### Policy documents affected

| Document | Section | Gap |
|---|---|---|
| `governance/governance-manual.md` | § Audit, Reporting, and Escalation | Lists report types but not log format per director |
| Each director's capability policy | Audit requirements section | Format is described loosely, not standardized |

#### Risk if unaddressed

Security Steward weekly reviews are slower and less reliable if each director's log requires interpretation. An audit of a specific event across multiple director logs becomes difficult if timestamps, action descriptions, and policy citations use inconsistent formats.

#### Proposed resolution

Define a standard log entry format for all directors. Minimum fields: timestamp, action taken, policy authority cited, artifacts affected, escalation flag (yes/no). Add the format definition to the Governance Manual § Audit section or as a shared template. Each director's capability policy references the standard.

#### Resolution notes

*(Open — no PCR filed yet)*

---

### GAP-003 — Director authority ceilings not fully defined for all directors

**ID:** GAP-003  
**Date identified:** 2026-06-05  
**Identified by:** President Agent (session review)  
**Status:** Open  
**Priority:** Medium  
**Domain:** governance

#### Description

Some directors have detailed capability policy documents with explicit authority levels; others have capability documents with less specificity. The authority ceiling — the maximum authority level (0–6) a director may exercise — should be stated explicitly in every capability document, not implied by the actions described.

#### Observed symptom or trigger

Review of capability documents during governance sessions. The Research Director, Creative Director, Financial Analysis Director, and Venture Director have capability documents, but their authority ceilings are sometimes described narratively rather than as explicit Level N grants for specific action categories.

#### Policy documents affected

| Document | Section | Gap |
|---|---|---|
| `capabilities/research-operations.md` | Authority levels | Needs explicit Level N per action category |
| `capabilities/creative-production.md` | Authority levels | Needs explicit Level N per action category |
| `capabilities/financial-analysis.md` | Authority levels | Needs explicit Level N per action category |
| `capabilities/venture-operations.md` | Authority levels | Needs explicit Level N per action category |

#### Risk if unaddressed

Ambiguous ceilings create ambiguity about what directors may execute autonomously. In practice, directors err conservative — but the Security Steward cannot audit against an unstated ceiling, and the President Agent cannot confidently assign work without knowing whether execution authority exists.

#### Proposed resolution

Conduct a capability audit for the four directors listed above. For each capability document, add an explicit authority table: action category | maximum authority level | conditions. This is an Engineering or Operations Director task (reading and updating documents), not a new capability grant.

#### Resolution notes

*(Open — no PCR filed yet)*

---

### GAP-004 — No dedicated policy change request storage location

**ID:** GAP-004  
**Date identified:** 2026-06-05  
**Identified by:** President Agent (INI-003/004/005 execution)  
**Status:** Open  
**Priority:** Low  
**Domain:** governance

#### Description

The policy change request template (`templates/policy-change-request-template.md`) says to "file as a draft in the policy change proposal area," but no such area is defined in the repository structure. During INI-003/004/005, PCR files were placed in `governance/` as a practical workaround, which is semantically imprecise.

#### Observed symptom or trigger

Encountered during INI-003/004/005 execution when filing draft PCRs. The governance manual lists `/governance` as "CEO-only" edit authority — agents should be proposing PCRs in a designated draft area, not directly in a CEO-edit-only folder.

#### Policy documents affected

| Document | Section | Gap |
|---|---|---|
| `templates/policy-change-request-template.md` | Usage instructions | References "policy change proposal area" that doesn't exist |
| `governance/governance-manual.md` | § Policy Repository | No `/proposals` or `/pcr` folder defined |

#### Risk if unaddressed

PCR drafts have no canonical home. They accumulate in ad hoc locations, making the CEO's review workflow unclear and the Security Steward's audit harder.

#### Proposed resolution

Define a `governance/proposals/` or `policies/proposals/` directory as the staging area for agent-drafted policy change requests. Add this to the Governance Manual § Policy Repository table. Move existing PCR files from `governance/` to the new location upon approval.

#### Resolution notes

*(Open — no PCR filed yet; existing PCRs in `governance/` are a known workaround)*

---

### GAP-005 — Scheduled task prompt backup has no defined mechanism

**ID:** GAP-005  
**Date identified:** 2026-06-05  
**Identified by:** President Agent (INI-004 Phase A audit)  
**Status:** In Progress — initial backup complete; ongoing sync not yet established  
**Priority:** Medium  
**Domain:** operations / infrastructure

#### Description

The four active scheduled tasks (`jasonos-policy-sync`, `jasonos-airlock-monitor`, `jasonos-security-steward-review`, `jasonos-daily-briefing`) have their prompts stored in Claude Desktop's internal storage at `~/Claude/Scheduled/[taskId]/SKILL.md`. These paths are on the Mac mini's local filesystem but are inaccessible from the Cowork sandbox. No backup mechanism exists.

If Claude Desktop data is wiped or the Mac mini is rebuilt, all four task prompts must be manually recreated — which requires either memory of the original prompt content or a backup copy.

#### Observed symptom or trigger

Identified during INI-004 Phase A recoverability audit. Confirmed that scheduled task storage paths are inaccessible from the Cowork sandbox.

#### Policy documents affected

| Document | Section | Gap |
|---|---|---|
| `operations/sandbox-rebuild-procedure.md` | § Phase A — Tier 2 | Identifies gap but does not resolve it |
| `Logs/session-handoff.md` | Scheduled tasks table | Lists task IDs and schedules but not full prompts |

#### Risk if unaddressed

Scheduled task prompts are the operational heart of JasonOS's autonomous functions. Losing them means the airlock monitor, security review, policy sync, and daily briefing all go silent until manually recreated. Recreation from memory risks drift from the original carefully-crafted prompts.

#### Proposed resolution

CEO manually copies the four SKILL.md files to a backed-up location — either the governance repo (`operations/scheduled-task-prompts/`) or the Google Drive airlock folder. Engineering Director establishes a process for keeping these copies current whenever a task prompt is updated.

#### Resolution notes

**In Progress — 2026-06-06:** CEO performed initial backup of all `~/Claude` contents to `Backup/` in Drive (ID: `{{DRIVE_BACKUP_ROOT_FOLDER_ID}}`). Scheduled task SKILL.md files confirmed present at `Backup/Scheduled/[taskId]/SKILL.md`. Drive MCP write access confirmed. Remaining work: Engineering, Knowledge, and Operations Directors establish ongoing sync workflows for agent definitions, KB vault, and logs respectively. See `operations/sandbox-rebuild-procedure.md` § Phase B.

---

### GAP-006 — President Agent Phase 1 emergency authority not yet constitutionalized

**ID:** GAP-006  
**Date identified:** 2026-06-05  
**Identified by:** President Agent (INI-003 execution)  
**Status:** Resolved — 2026-06-06  
**Priority:** High  
**Domain:** governance / security

#### Description

The emergency shutdown procedure (`operations/emergency-shutdown-procedure.md`) requires President Agent unilateral Phase 1 Containment authority. This authority does not exist in current policy. A draft Constitution amendment (PCR-SHUT-001) has been filed, but no decision has been made.

#### Observed symptom or trigger

INI-003 execution. The procedure was written; the authority it relies on is not yet granted.

#### Policy documents affected

| Document | Section | Gap |
|---|---|---|
| `constitution/constitution.md` | All articles | No article grants President Agent unilateral emergency initiation authority |
| `capabilities/president-coordination.md` | Level 6 authorities | Phase 1 initiation not listed |

#### Risk if unaddressed

The emergency shutdown procedure exists but is not fully executable. In a real emergency during an unattended session, the President Agent cannot act without CEO direction — which may not be available.

#### Proposed resolution

CEO reviews PCR-SHUT-001 (`governance/PCR-SHUT-001-constitution-phase1-authority.md`), selects a triggering condition option (A, B, or C), and approves the Constitution amendment. Update `capabilities/president-coordination.md` concurrently.

#### Resolution notes

**In Progress — 2026-06-05:** CEO selected Option A triggering conditions. PCR-SHUT-001 updated to reflect this decision. Constitution amendment drafted and ready for CEO formal approval and publication. Companion update to `capabilities/president-coordination.md` also required.

**Resolved — 2026-06-06:** CEO approved PCR-SHUT-001. Constitution Article XIII published. `capabilities/president-coordination.md` updated with Execution Authority 3. Phase 1 initiation authority is now active policy.

---

### GAP-007 — President Agent initiative execution protocol not written into standing policy

**ID:** GAP-007  
**Date identified:** 2026-06-06  
**Identified by:** President Agent (INI-003/004/005 session review)  
**Status:** Resolved — 2026-06-06  
**Priority:** Medium  
**Domain:** governance / roles

#### Description

The behaviors that make President Agent autonomous initiative execution effective — upfront question batching, non-blocking question handling, question framing with recommendations and consequences, and initiative completion gates — are not in `roles/president-agent.md`. They emerged from the INI-003/004/005 test session and were surfaced as principles by the CEO. Without encoding them as policy, they depend on session-level instruction and are not enforceable across agents or sessions.

#### Observed symptom or trigger

CEO evaluation of the INI-003/004/005 autonomy test (2026-06-06). Five principles were identified as candidates for policy. Two are already covered by existing policy (Constitution Article III, Charter § 3.7 / § 21); three are new.

#### Policy documents affected

| Document | Section | Gap |
|---|---|---|
| `roles/president-agent.md` | § Performance Measures | Escalation framing language is partial; no section for initiative execution protocol |

#### Risk if unaddressed

The execution behaviors that produced a successful INI queue run are not durable. A different session or agent may not apply them, reducing autonomous throughput and increasing CEO interrupt frequency.

#### Proposed resolution

Add `§ Initiative Execution Protocol` to `roles/president-agent.md` covering: upfront question batching, non-blocking question handling with documentation encoding, question framing requirements, and a completion gate for initiatives with unresolved CEO decisions. Also update the Performance Measures escalation bullet. See PCR-EXEC-001.

#### Resolution notes

**In Progress — 2026-06-06:** PCR-EXEC-001 filed at `governance/PCR-EXEC-001-initiative-execution-protocol.md`.

**Resolved — 2026-06-06:** CEO approved PCR-EXEC-001. `roles/president-agent.md` updated with § Initiative Execution Protocol and revised Performance Measures bullet.

---

## Resolved Gaps

---

### GAP-006 — Resolved 2026-06-06

President Agent Phase 1 emergency authority not yet constitutionalized. Resolved by CEO approval of PCR-SHUT-001. Constitution Article XIII published; `capabilities/president-coordination.md` updated. See Open Gaps section for full entry.

---

### GAP-007 — Resolved 2026-06-06

President Agent initiative execution protocol not in standing policy. Resolved by CEO approval of PCR-EXEC-001. `roles/president-agent.md` updated with § Initiative Execution Protocol. See Open Gaps section for full entry.

---

## Deferred Gaps

*(None yet)*
