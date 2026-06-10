# Session Handoff — President Agent

**Last updated:** [YYYY-MM-DD] (President Agent — [one-line session summary])
**Session summary:** [Brief description of what was accomplished this session]

---

## Current Organizational State

| Role | Status |
|---|---|
| President Agent | [Active / Inactive] |
| Engineering Director | [Status] |
| Operations Director | [Status] |
| Knowledge Director | [Status] |
| Research Director | [Status] |
| Creative Director | [Status] |
| Financial Analysis Director | [Status] |
| Venture Director | [Status] |
| Security Steward | [Status] |

---

## Infrastructure in Place

[Brief summary of operational infrastructure — relay, scheduled tasks, key paths]

**Scheduled tasks:**

| Task ID | Schedule | Purpose |
|---|---|---|
| [task-id] | [cron / description] | [Purpose] |

---

## Source Control Status

> **Required check before any new commit cycle.** Resolve all open items before staging new changes.

### Open PRs

| Branch | Status | Files in Flight |
|---|---|---|
| [branch-name] | [Open / Merged / Closed] | [Affected files] |

*If any PRs are open, do not stage changes to their affected files until resolved.*

### Post-Merge Checklist

After each PR merge, confirm:

- [ ] Source branch deleted from {{SYSTEM_USER}} fork
- [ ] Fork main synced with canonical (GitHub "Sync fork")
- [ ] Local main pulled (`git fetch upstream && git merge upstream/main`)

### Next Available Initiative ID

Highest ID on `{{CANONICAL_GITHUB_USER}}/{{REPO_NAME}}` main: **INI-[XXX]**
Next assignable ID: **INI-[XXX+1]**

*Always verify against canonical main before assigning — do not use local fork or session memory.*

---

## Open Items (CEO Action Required)

[List any items requiring CEO decision or action before the organization can proceed]

---

## Resolved Gaps

| Gap | Resolution |
|---|---|
| [Description] | [How it was resolved] |

---

## Future Functionality

[Deferred items and their blockers]

---

## Recommended Next Actions

1. [Most important next action]
2. [Second action]
3. [Additional actions as needed]

---

## Key File Locations

| Resource | Path |
|---|---|
| Governance repo (local) | `{{SANDBOX_DATA_ROOT}}/Governance/{{REPO_NAME}}/` |
| Governance repo (fork — JasonOS origin) | `https://github.com/{{FORK_GITHUB_USER}}/{{REPO_NAME}}` |
| Governance repo (canonical — CEO upstream) | `https://github.com/{{CANONICAL_GITHUB_USER}}/{{REPO_NAME}}` |
| Session memory | `governance/memory.md` |
| President lifecycle log | `{{SANDBOX_DATA_ROOT}}/Logs/president-lifecycle-log.md` |
