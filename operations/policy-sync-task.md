# Policy Sync Task — Operational Reference

**Owner:** President Agent
**Version:** 1.1
**Status:** Active
**Last updated:** 2026-06-06 (INI-028 — remote comparison corrected)
**Scheduled task ID:** `jasonos-policy-sync`
**Schedule:** Daily ~01:00

---

## Purpose

The daily policy sync task verifies that the governance repo working tree is consistent with canonical main (`{{CANONICAL_GITHUB_USER}}/{{REPO_NAME}}`) and alerts the President Agent to any divergence. It runs automatically on the Cowork scheduled task runner every morning before the CEO's daily briefing.

---

## Architecture

**v1.0 (deprecated):** The task attempted to compare directly against `origin/main` on GitHub over HTTPS. This was proxy-blocked from the Cowork sandbox (HTTP 403 — confirmed 2026-06-05). Remote comparison produced "Blocked" status every run.

**v1.1 (current — INI-028):** Remote comparison now uses the locally cached `upstream/main` ref, updated by the `com.jasonos.canonical-fetch` launchd agent on the Mac mini. The Cowork sandbox reads the cached ref from the shared volume — zero outbound network calls.

**Invariant:** Cowork never calls GitHub directly. The Mac mini (`com.jasonos.canonical-fetch`) is the sole process that fetches from GitHub. Cowork reads from the shared volume only.

---

## Task Prompt (Current — v1.1)

The following is the prompt used for the `jasonos-policy-sync` scheduled task:

```
You are the President Agent of JasonOS. Perform the daily policy sync.

Steps:
1. Check working tree status:
   Run: git -C {{SANDBOX_DATA_ROOT}}/Governance/{{REPO_NAME}} status --short
   Report: any uncommitted changes (files modified, added, or deleted)

2. Compare against canonical main (local cache — requires com.jasonos.canonical-fetch to be running):
   Run: git -C {{SANDBOX_DATA_ROOT}}/Governance/{{REPO_NAME}} diff upstream/main --name-only 2>/dev/null
   If the command fails or upstream/main is not found, report: "Remote comparison unavailable — canonical-fetch may not have run yet. Check {{SANDBOX_DATA_ROOT}}/Logs/director-logs/engineering/canonical-fetch.log."
   If it succeeds and produces no output, report: "Working tree matches upstream/main — no divergence."
   If it produces file names, list them and flag for CEO review.

3. Check for stale session handoff (last updated > 48 hours ago):
   Run: find {{SANDBOX_DATA_ROOT}}/Logs -name "session-handoff.md" -mtime +2
   If found, report as a reminder that the handoff may be stale.

4. Report all findings in a short summary (≤ 10 lines). If everything is clean, confirm in one sentence.
```

---

## Updating the Task Prompt

To update the policy sync task prompt:
1. Edit this document (the canonical source for the prompt)
2. Open the Cowork scheduled tasks panel
3. Locate `jasonos-policy-sync`
4. Replace the prompt with the updated version from this document
5. Save

The President Agent may update the prompt within existing policy scope. Changes that alter what the task reports or who it alerts require CEO approval.

---

## Prerequisites

- `com.jasonos.canonical-fetch` must be installed and running on the Mac mini (INI-028)
- `/bin/zsh` must have Full Disk Access on the Mac mini (shared with git relay and dispatcher)
- `upstream` remote must point to `git@github.com:{{CANONICAL_GITHUB_USER}}/{{REPO_NAME}}`

---

## Troubleshooting

| Symptom | Likely cause | Resolution |
|---|---|---|
| "Remote comparison unavailable" | canonical-fetch not yet installed or not run | Install plist per INI-028 runbook section; check canonical-fetch.log |
| Working tree always shows divergence | Local commits not pushed | Check for unpushed commits via git relay; verify push succeeded |
| Task not running | Scheduled task misconfigured | Check Cowork scheduled tasks panel |

---

## Canonical Fetch Log

`{{SANDBOX_DATA_ROOT}}/Logs/director-logs/engineering/canonical-fetch.log`

This log records every run of the `com.jasonos.canonical-fetch` agent: fetch success/failure, resulting `upstream/main` SHA, and any errors. If remote comparison is failing, check this log first.

---

## Version History

| Version | Date | Change |
|---|---|---|
| 1.0 | 2026-06-05 | Initial — HTTP remote comparison (proxy-blocked) |
| 1.1 | 2026-06-06 | INI-028 — switch to local cached upstream/main ref |
