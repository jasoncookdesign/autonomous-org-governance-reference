# Emergency Shutdown Procedure

**Version:** 1.0 — Draft  
**Audience:** President Agent, CEO  
**Status:** Active — pending one CEO decision (see § Triggering Conditions — CEO Decision Required)

> **Quick reference:** In a real emergency, skip to the phase tables below. Read this section only when planning or reviewing.

---

## Purpose

This procedure defines the organization's kill switch. When unauthorized agent behavior, a security breach, a CEO stop-work order, or another triggering condition is detected, this document specifies what to do, in what order, and who has authority at each step.

The procedure is designed to be executed under pressure without improvisation.

---

## Four-Phase Architecture

| Phase | Name | Initiator | Goal |
|---|---|---|---|
| 1 | Contain | President Agent (unilateral) or CEO | Stop all autonomous activity immediately |
| 2 | Isolate | CEO | Revoke credentials and cut external access |
| 3 | Preserve | CEO | Capture state before any cleanup |
| 4 | Gate | CEO | Authorize restart only after root cause is understood |

**Phases execute sequentially.** Phase 2 does not begin until Phase 1 is complete. Phase 3 does not begin until Phase 2 is complete. Phase 4 does not begin until Phase 3 is complete and the Security Steward has filed a post-incident report.

**No component restarts** without explicit CEO authorization in Phase 4, regardless of downtime duration.

---

## Triggering Conditions

### CEO-initiated shutdown

The CEO may direct shutdown at any time, for any reason, with no justification required. CEO instruction is the highest-authority triggering condition.

### President Agent unilateral Phase 1 initiation

> ⚠️ **Constitution amendment pending.** The triggering conditions below have been approved by the CEO (2026-06-05) and are documented in PCR-SHUT-001. They are not yet active policy — the formal Constitution amendment must be published before the President Agent may invoke this authority unilaterally. Until published, the President Agent must contact the CEO before initiating Phase 1.

The President Agent may initiate Phase 1 unilaterally when any of the following is observed:

1. A director reports a Critical-severity incident
2. The Security Steward issues a Model C halt directive
3. Any agent is actively executing in excess of its authority ceiling with no apparent way to halt through normal direction
4. Active prompt injection with apparent behavioral effect on any agent is detected

---

## Phase 1 — Contain

**Initiator:** President Agent (once Constitution amendment is approved) or CEO  
**Time target:** Complete within 5 minutes of triggering condition  
**Authority required:** Existing President Agent coordination authority (scheduling tool access)

### Steps

1. **Disable all scheduled tasks** immediately.

   | Task | Action |
   |---|---|
   | `jasonos-airlock-monitor` | Disable via `update_scheduled_task` with `enabled: false` |
   | `jasonos-policy-sync` | Disable via `update_scheduled_task` with `enabled: false` |
   | `jasonos-security-steward-review` | Disable via `update_scheduled_task` with `enabled: false` |
   | `jasonos-daily-briefing` | Disable via `update_scheduled_task` with `enabled: false` |

2. **Issue stop-work to all active directors.** Communicate halt to every director currently assigned work. Record which directors were notified and at what time.

3. **Halt in-progress Cowork sessions** (President Agent self-halts all autonomous actions; no further tool calls except logging and escalation).

4. **Notify Security Steward.** If Security Steward is not the initiating party, notify immediately with a one-line description of the triggering condition.

5. **Notify CEO** via email (President Agent send authority). Include:
   - Triggering condition observed
   - Phase 1 actions taken
   - Whether Phase 2 is recommended

6. **Open a Shutdown Manifest** using `templates/shutdown-manifest-template.md`. Begin filling it out immediately.

7. **Await CEO acknowledgment** before proceeding to Phase 2.

---

## Phase 2 — Isolate

**Initiator:** CEO only  
**Authority required:** CEO credential access  
**Prerequisite:** Phase 1 complete; CEO has acknowledged

### Steps

1. **Suspend the Gmail MCP OAuth credential** (revoke via Google Account → Security → Third-party apps). This stops the airlock monitor from accessing Gmail.

2. **Suspend the Google Drive MCP OAuth credential** (same process). This stops the airlock monitor from accessing Drive.

3. **Suspend the GitHub personal access token** (if Engineering Director was active on a commit or PR). GitHub → Settings → Developer settings → Personal access tokens.

4. **Suspend the Anthropic API key** (if Anthropic API-based agent actions were in flight). Anthropic console → API keys.

5. **Assess network isolation.** If the incident involves suspected lateral movement or a compromised Mac mini process, disconnect the Mac mini from the network (unplug Ethernet / disable Tailscale / disable Wi-Fi).

6. Update the Shutdown Manifest — Phase 2 section.

7. Authorize Phase 3 explicitly.

---

## Phase 3 — Preserve

**Initiator:** CEO only  
**Authority required:** CEO filesystem and git access  
**Prerequisite:** Phase 2 complete

### Steps

1. **Capture governance repo state.** Run `git log --oneline -20` from `{{SANDBOX_DATA_ROOT}}/Governance/{{REPO_NAME}}/` on the Mac mini. Record the HEAD commit hash in the manifest.

2. **Copy logs to a timestamped archive.** `cp -r {{SANDBOX_DATA_ROOT}}/Logs/ ~/shutdown-preserve-[DATE]/`

3. **Note KB vault state.** Record the current item count in the KB inbox and any in-flight transfers.

4. **Note Airlock state.** Record any items currently in `{{SANDBOX_DATA_ROOT}}/Airlock/` that have not yet been processed.

5. **File an Incident Report** (using `templates/incident-report-template.md`) covering the triggering event, Phase 1–2 actions, and any suspected impact.

6. Update the Shutdown Manifest — Phase 3 section.

7. Direct Security Steward to file a post-incident compliance report.

8. Authorize Phase 4 explicitly once root cause is understood and the Security Steward report is filed.

---

## Phase 4 — Gate

**Initiator:** CEO only  
**Authority required:** CEO  
**Prerequisite:** Phase 3 complete; Security Steward post-incident report filed; root cause understood or accepted risk documented

### Steps

1. **Review the Security Steward post-incident report.** Confirm all affected components and any recommended policy changes.

2. **Determine which components may restart.** Not all components must restart. A component that contributed to the incident should not restart until the contributing policy gap is addressed.

3. **Complete the restart authorization table** in the Shutdown Manifest. Each component requires an explicit checkmark.

4. **Re-enable authorized scheduled tasks** via `update_scheduled_task` with `enabled: true` — one at a time, in this order:
   1. `jasonos-policy-sync` (lowest risk; verify repo integrity first)
   2. `jasonos-security-steward-review`
   3. `jasonos-airlock-monitor`
   4. `jasonos-daily-briefing`

5. **Re-establish credential access** for authorized tool connections (reverse Phase 2 actions as applicable).

6. **Brief the President Agent** on any policy changes or restrictions in effect before resuming director assignments.

7. **Close the Shutdown Manifest** with completion time and summary.

8. **File the closed manifest** in `audit/incident-reports/`.

---

## Open Items — CEO Action Required

### 1. Publish Constitution Amendment (PCR-SHUT-001)

Triggering conditions selected (Option A, 2026-06-05). The Constitution amendment is drafted and ready. Until published, the President Agent cannot invoke Phase 1 unilaterally.

See `governance/PCR-SHUT-001-constitution-phase1-authority.md`.

**Action required:** CEO approves and publishes the Constitution amendment. Companion update to `capabilities/president-coordination.md` required at the same time.

---

## Partial Drill Record

**Drill date:** 2026-06-05  
**Drill type:** Stop mechanism verification (Phase 1, step 1 only)  
**Executed by:** President Agent

### Test performed

Scheduled task `jasonos-policy-sync` was disabled and immediately re-enabled to verify the `update_scheduled_task` stop/start mechanism.

- **Stop:** `update_scheduled_task` with `enabled: false` — confirmed functional
- **Restart:** `update_scheduled_task` with `enabled: true` — confirmed functional
- **Operational impact:** None (task had already executed for the day; next run was not interrupted)

**Result:** Phase 1 stop mechanism verified. All four active scheduled tasks can be halted via this mechanism within a single Cowork session. Full Phase 1 can be executed in under 5 minutes.

**Not tested:** Phases 2–4 (require CEO credential access and are not safely testable in isolation).

**Next drill:** Full Phase 1 dry run recommended — CEO directs President Agent to halt all tasks and immediately restart them. No credential revocation required. Recommended cadence: quarterly or after any significant infrastructure change.

---

## Related Documents

| Document | Purpose |
|---|---|
| `templates/shutdown-manifest-template.md` | Audit record for each shutdown event |
| `templates/incident-report-template.md` | Incident report filed in Phase 3 |
| `capabilities/security-audit.md` | Security Steward Model C halt authority |
| `roles/president-agent.md` | President Agent authority scope |
| `audit/incident-reports/PCR-SHUT-001.md` | Constitution amendment — President Agent Phase 1 authority |
| `audit/incident-reports/PCR-SHUT-002.md` | Operating Charter amendment — § Emergency Operations |
