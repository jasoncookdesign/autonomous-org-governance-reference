# Shutdown Manifest Template

**Version:** 1.0  
**Usage:** Complete one manifest per emergency shutdown event. Filed in `audit/incident-reports/` alongside any associated incident report. Append-only — do not modify entries after filing.

---

**Manifest ID:** SHUT-[YYYY-MM-DD-NNN]  
**Date initiated:** [YYYY-MM-DD]  
**Time initiated (local):** [HH:MM]  
**Initiated by:** [President Agent / CEO]  
**Phase reached:** [1 — Contain / 2 — Isolate / 3 — Preserve / 4 — Gate]  
**Status:** [Active / Resolved — Restart Authorized / Resolved — Retired]

---

## Triggering Event

**Triggering condition met:**

> [Describe the specific condition that triggered shutdown. Reference the triggering condition category from `operations/emergency-shutdown-procedure.md` § Triggering Conditions, and describe the specific observation.]

**Observed by:** [Role]  
**Reported via:** [Session / Scheduled task / Director escalation / Security Steward directive]

---

## Phase 1 — Contain

*President Agent executes. Timestamp each action.*

| Action | Completed | Timestamp | Notes |
|---|---|---|---|
| All scheduled tasks disabled | ☐ | | |
| Active director sessions halted | ☐ | | |
| In-progress autonomous workflows stopped | ☐ | | |
| Security Steward notified | ☐ | | |
| CEO notified | ☐ | | |

**Phase 1 completion time:** [HH:MM or N/A]  
**CEO acknowledgment received:** [Yes / No / Pending]

---

## Phase 2 — Isolate

*CEO executes. Required before Phase 3.*

| Action | Completed | Timestamp | Notes |
|---|---|---|---|
| Gmail MCP credential suspended | ☐ | | |
| Google Drive MCP credential suspended | ☐ | | |
| GitHub access token suspended (if applicable) | ☐ | | |
| Anthropic API key suspended (if applicable) | ☐ | | |
| Other tool credentials suspended | ☐ | | List tools: |
| Mac mini network isolated (if directed) | ☐ | | |

**Phase 2 completion time:** [HH:MM or N/A — if incident did not reach Phase 2, state reason]  
**CEO authorization for Phase 3:** [Yes / Not yet / N/A]

---

## Phase 3 — Preserve

*CEO executes. Required before Phase 4.*

| Action | Completed | Timestamp | Notes |
|---|---|---|---|
| Governance repo state captured (git log HEAD) | ☐ | | Commit: |
| Operational logs snapshot taken | ☐ | | |
| KB vault state noted | ☐ | | |
| Airlock contents documented | ☐ | | |
| Incident report filed (separate document) | ☐ | | Report ID: |
| All artifacts in `audit/incident-reports/` | ☐ | | |

**Phase 3 completion time:** [HH:MM or N/A]  
**CEO authorization for Phase 4:** [Yes / Not yet / N/A]

---

## Phase 4 — Gate

*CEO executes. No restart without explicit per-component authorization.*

**Root cause determination:** [Pending / Complete — summarize]

**Resolution or accepted risk:** [Describe what was resolved or what risk the CEO has accepted before restart]

**Security Steward post-incident report filed:** [Yes / Pending / N/A]

### Restart Authorization

*CEO must explicitly authorize each component. No component restarts without this table completed.*

| Component | Authorized | Authorized by | Date |
|---|---|---|---|
| jasonos-airlock-monitor | ☐ | | |
| jasonos-policy-sync | ☐ | | |
| jasonos-security-steward-review | ☐ | | |
| jasonos-daily-briefing | ☐ | | |
| Active director sessions | ☐ | | |
| Gmail MCP | ☐ | | |
| Google Drive MCP | ☐ | | |
| GitHub access | ☐ | | |
| Anthropic API | ☐ | | |
| Other (list): | ☐ | | |

**Phase 4 completion time:** [HH:MM or N/A]

---

## Post-Shutdown Summary

**Total downtime:** [Duration]  
**Systems affected:** [List]  
**Data potentially exposed or affected:** [None identified / Describe]  
**Policy change recommendations raised:** [Yes — see PCR [ID] / None]  
**Filed by:** President Agent  
**Filed at:** [YYYY-MM-DD HH:MM]
