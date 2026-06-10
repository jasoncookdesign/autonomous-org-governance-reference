# Sandbox Rebuild Procedure

**Version:** 1.0 — Draft  
**Audience:** CEO  
**Status:** Active — Phase B backup proposal pending CEO approval (see § Open Items)

> **Purpose:** If the Mac mini is destroyed, wiped, or irreparably corrupted, this document is the rebuild guide. Read § Phase A first to understand what can be recovered automatically and what requires manual steps.

---

## Phase A — Recoverability Matrix

*Audited 2026-06-05 by President Agent.*

### Tier 1 — Recoverable from GitHub

These artifacts live in version-controlled repositories. Recovery requires only a `git clone`.

| Artifact | Location | Repository | Notes |
|---|---|---|---|
| Governance repo | `{{SANDBOX_DATA_ROOT}}/Governance/{{REPO_NAME}}/` | `{{CANONICAL_GITHUB_USER}}/{{REPO_NAME}}` (canonical) | HEAD as of last PR merge |
| Code projects | `{{SANDBOX_DATA_ROOT}}/code/beatport-continuity/` | Presumed in {{CANONICAL_GITHUB_USER}} GitHub — verify | Not audited |
| Code projects | `{{SANDBOX_DATA_ROOT}}/code/dishnbeats/` | Presumed in {{CANONICAL_GITHUB_USER}} GitHub — verify | Not audited |
| Code projects | `{{SANDBOX_DATA_ROOT}}/code/djbellab/` | Presumed in {{CANONICAL_GITHUB_USER}} GitHub — verify | Not audited |
| Code projects | `{{SANDBOX_DATA_ROOT}}/code/dysonhope/` | Presumed in {{CANONICAL_GITHUB_USER}} GitHub — verify | Not audited |
| Code projects | `{{SANDBOX_DATA_ROOT}}/code/{{CANONICAL_GITHUB_USER}}.github.io/` | Presumed in {{CANONICAL_GITHUB_USER}} GitHub — verify | Not audited |
| Code projects | `{{SANDBOX_DATA_ROOT}}/code/ravecal-cli/` | Presumed in {{CANONICAL_GITHUB_USER}} GitHub — verify | Not audited |
| Code projects | `{{SANDBOX_DATA_ROOT}}/code/sigilzero/` | Presumed in {{CANONICAL_GITHUB_USER}} GitHub — verify | Not audited |
| Code projects | `{{SANDBOX_DATA_ROOT}}/code/sigilzero-ai/` | Presumed in {{CANONICAL_GITHUB_USER}} GitHub — verify | Not audited |
| Code projects | `{{SANDBOX_DATA_ROOT}}/code/tradespec/` | Presumed in {{CANONICAL_GITHUB_USER}} GitHub — verify | Not audited |

> **Gap:** Code project GitHub status is assumed, not confirmed. Engineering Director should verify each repo exists remotely as a follow-on task.

### Tier 2 — Requires Backup Infrastructure (not currently backed up)

These artifacts exist only on SandboxData or the Mac mini local filesystem. **They would be permanently lost in a wipe.**

| Artifact | Location | Size / Maturity | Backup Priority | Recommended Backup |
|---|---|---|---|---|
| Agent definitions | `{{SANDBOX_DATA_ROOT}}/agents/` | 13 files; mature; rarely changes | **High** | Add to governance repo under `agents/` |
| KB vault | `{{SANDBOX_DATA_ROOT}}/knowledge/jasonos-vault/` | 4 .md files now; will grow substantially | **High** | Dedicated private GitHub repo or Google Drive sync |
| Operational logs | `{{SANDBOX_DATA_ROOT}}/Logs/` | Active; grows each session | **Medium** | Google Drive sync (CEO already has read access) |
| Git relay scripts (SandboxData copy) | `{{SANDBOX_DATA_ROOT}}/bin/` | 3 files; stable | **Medium** | Already documented in governance repo workload; re-creatable from docs |
| Setup script | `{{SANDBOX_DATA_ROOT}}/jasonos-setup.command` | 1 file | **Low** | Add to governance repo |

**Mac mini home directory only (not on SandboxData):**

| Artifact | Location | Notes | Backup Priority |
|---|---|---|---|
| Git relay launchd plist | `~/Library/LaunchAgents/com.jasonos.git-relay.plist` | Re-creatable from docs; idempotent activation script exists | **Low** — re-creatable |
| Git relay worker (home copy) | `~/bin/jasonos-git-relay.sh` | Source of truth is SandboxData copy | **Low** — duplicate |
| Global CLAUDE.md | `~/.claude/CLAUDE.md` | Source is `{{SANDBOX_DATA_ROOT}}/agents/global-CLAUDE.md` | **Low** — source is SandboxData |
| Scheduled task configs | Claude Desktop internal storage | No export mechanism exists; would need to be recreated | **High** — see Gap below |

> **Gap:** Scheduled task configurations have no export or backup mechanism. Four tasks are active (see session-handoff.md). If Claude Desktop data is wiped, all four tasks must be manually recreated. Full prompts are stored at `/Users/{{SYSTEM_USER}}/Claude/Scheduled/[taskId]/SKILL.md` — these paths are inaccessible from the sandbox but are on the user's local machine. CEO should verify these files exist and are readable.

### Tier 3 — Must Recreate (never back up raw)

These items cannot be backed up safely. Recovery requires manual rotation and reconnection.

| Artifact | Recovery Action |
|---|---|
| GitHub personal access token | Generate new token at github.com → Settings → Developer settings |
| Google OAuth credential (Gmail MCP) | Reconnect Gmail MCP in Claude Desktop → Connections |
| Google OAuth credential (Google Drive MCP) | Reconnect Google Drive MCP in Claude Desktop → Connections |
| Anthropic API key | Generate new key at console.anthropic.com |
| Privacy.com virtual card numbers | Issue new cards at privacy.com; update any saved merchant references |

---

## Phase B — Backup Infrastructure

> **CEO decision 2026-06-05:** Google Drive sync selected. Drive MCP write access confirmed 2026-06-06. Backup folder confirmed at `Backup/` (Drive ID: `{{DRIVE_BACKUP_ROOT_FOLDER_ID}}`).

### Selected approach — Google Drive sync

**Artifacts and confirmed Drive locations:**

| Artifact | Local path | Drive path | Drive folder ID | Status |
|---|---|---|---|---|
| Agent definitions | `{{SANDBOX_DATA_ROOT}}/agents/` | `Backup/Projects/JasonOS/agents/` | `{{DRIVE_PROJECTS_FOLDER_ID}}` (parent) | ⏳ Sync workflow not yet established |
| KB vault | `{{SANDBOX_DATA_ROOT}}/knowledge/jasonos-vault/` | `Backup/Projects/JasonOS/knowledge/` | `{{DRIVE_PROJECTS_FOLDER_ID}}` (parent) | ⏳ Sync workflow not yet established |
| Operational logs | `{{SANDBOX_DATA_ROOT}}/Logs/` | `Backup/Projects/JasonOS/logs/` | `{{DRIVE_PROJECTS_FOLDER_ID}}` (parent) | ⏳ Sync workflow not yet established |
| Scheduled task prompts | `~/Claude/Scheduled/[taskId]/SKILL.md` (×4) | `Backup/Scheduled/[taskId]/SKILL.md` | `{{DRIVE_SCHEDULED_FOLDER_ID}}` (parent) | ✅ Initial backup complete (2026-06-06) |

**Implementation steps remaining:**

1. Engineering Director establishes sync workflow for `agents/` to `Backup/Projects/JasonOS/agents/`.
2. Knowledge Director updates knowledge session workflow to sync KB vault to `Backup/Projects/JasonOS/knowledge/`.
3. Operations Director updates session workflow to sync logs to `Backup/Projects/JasonOS/logs/` and adds this to the airlock monitoring runbook.
4. When any scheduled task prompt is updated, the updated SKILL.md must be copied to the corresponding `Backup/Scheduled/[taskId]/` folder.

**Risk:** Class B/C content in the Drive backup folder. Acceptable — Drive is an approved channel ({{OPERATOR_EMAIL}}) and the `Backup/` folder is scoped appropriately.

**MAL:** If Drive folder is deleted and local SandboxData is also lost: agent definitions, KB vault, and logs are permanently lost. Low probability; acceptable given no Class D content is included.

---

## Phase C — Rebuild Sequence

*If the Mac mini is lost: execute these steps in order.*

### Pre-conditions

- New Mac mini provisioned (or OS reinstalled)
- Tailscale installed and authenticated (for remote access)
- Git installed (`xcode-select --install`)
- Claude Desktop installed and signed into {{OPERATOR_EMAIL}}
- GitHub account accessible ({{CANONICAL_GITHUB_USER}} personal; {{SYSTEM_USER}} fork)

### Step 0 — Emergency shutdown (if applicable)

If the rebuild follows an incident, confirm the emergency shutdown procedure has completed through Phase 3 and the CEO has authorized rebuild in the Shutdown Manifest.

### Step 1 — External drive and volume

1. Mount the SandboxData external drive (5 TB USB3).
2. Verify `{{SANDBOX_DATA_ROOT}}/` is accessible.
3. If drive was also lost: skip to § Step 1B — Full Loss Rebuild.

### Step 2 — Governance repo

```bash
cd {{SANDBOX_DATA_ROOT}}/Governance/
git clone https://github.com/{{CANONICAL_GITHUB_USER}}/{{REPO_NAME}}.git
cd {{REPO_NAME}}
git remote add origin https://github.com/{{FORK_GITHUB_USER}}/{{REPO_NAME}}.git
git remote set-url upstream https://github.com/{{CANONICAL_GITHUB_USER}}/{{REPO_NAME}}.git
```

### Step 3 — Global CLAUDE.md

```bash
cp {{SANDBOX_DATA_ROOT}}/agents/global-CLAUDE.md ~/.claude/CLAUDE.md
```

### Step 4 — Git relay

```bash
# Install relay worker
mkdir -p ~/bin
cp {{SANDBOX_DATA_ROOT}}/bin/jasonos-git-relay.sh ~/bin/
chmod +x ~/bin/jasonos-git-relay.sh

# Activate launchd agent
open {{SANDBOX_DATA_ROOT}}/bin/jasonos-git-relay-activate.command

# Grant Full Disk Access to /bin/zsh:
# System Settings → Privacy & Security → Full Disk Access → add /bin/zsh

# Smoke test
bash {{SANDBOX_DATA_ROOT}}/bin/jasonos-git-relay-test.sh
```

### Step 5 — Reconnect tool credentials

1. Claude Desktop → Connections → reconnect Gmail MCP ({{OPERATOR_EMAIL}})
2. Claude Desktop → Connections → reconnect Google Drive MCP
3. Set Anthropic API key if required for Claude Code director invocations

### Step 6 — Recreate scheduled tasks

Recreate the four scheduled tasks in Claude Desktop (prompts in `/Users/{{SYSTEM_USER}}/Claude/Scheduled/[taskId]/SKILL.md` — or from backup if Google Drive sync was in place):

| Task ID | Schedule | Notes |
|---|---|---|
| `jasonos-policy-sync` | Daily 01:00 | |
| `jasonos-airlock-monitor` | Daily 02:00 | |
| `jasonos-security-steward-review` | Monday 03:00 | |
| `jasonos-daily-briefing` | Daily 06:00 | |

> If scheduled task backup is in place (Google Drive sync), restore prompts from backup rather than recreating from memory.

### Step 7 — Verify KB vault

If KB vault backup is in place (GitHub or Drive), restore to `{{SANDBOX_DATA_ROOT}}/knowledge/jasonos-vault/`. Verify index.md is intact and tagging taxonomy matches.

### Step 8 — Verify operational logs

If log backup is in place, restore to `{{SANDBOX_DATA_ROOT}}/Logs/`. If not, create empty log structure:

```bash
mkdir -p {{SANDBOX_DATA_ROOT}}/Logs/director-logs/{engineering,operations,knowledge,research,creative,financial-analysis,security-steward,venture}
```

### Step 9 — President Agent brief

Open a new Cowork session. The President Agent reads `memory.md` and `session-handoff.md` and reports current state. Issue any corrective instructions.

---

### Step 1B — Full Loss Rebuild (drive and Mac mini both lost)

If both the Mac mini and the SandboxData drive are lost:

1. New Mac mini: follow Steps 2–6 above (governance repo from GitHub; relay rebuild from docs).
2. New drive: provision a new external drive, mount as `{{SANDBOX_DATA_ROOT}}/`.
3. Re-create `agents/` directory from governance repo (if Option 1 was implemented) or manually from documentation.
4. Accept KB vault loss if no backup was in place. Restart vault from empty structure.
5. Accept log loss for historical sessions. Start fresh logs.
6. New Airlock folder: `mkdir -p {{SANDBOX_DATA_ROOT}}/Airlock/`
7. File an incident report documenting the loss.

**Data permanently lost in a full loss scenario (if no backup infrastructure was implemented):**
- KB vault content
- Historical operational logs
- Scheduled task prompts (if Claude Desktop data also lost)

This is the consequence of not implementing Phase B backup infrastructure.

---

## Phase D — Operating Charter Amendment

See `governance/PCR-BC-001-charter-business-continuity.md` for the formal policy change request.

---

## Open Items — CEO Action Required

### 1. Grant Drive folder access and perform initial scheduled task backup

Backup infrastructure approach selected (Google Drive sync, 2026-06-05). Implementation is blocked until CEO:

1. Creates the backup Drive folder and grants access to the Drive MCP connection
2. Manually copies the four scheduled task SKILL.md files from `~/Claude/Scheduled/` to the new folder — this is a one-time CEO action since that path is inaccessible from the sandbox

### 2. Verify code project GitHub status

Engineering Director should confirm all nine code repos in `{{SANDBOX_DATA_ROOT}}/code/` have remote GitHub counterparts. If any do not, they are Tier 2 untracked.

---

## Related Documents

| Document | Purpose |
|---|---|
| `operations/emergency-shutdown-procedure.md` | Shutdown is Step 0 of a controlled rebuild |
| `governance/PCR-BC-001-charter-business-continuity.md` | Draft Charter amendment — § Business Continuity |
| `workloads/active/git-commit-relay.md` | Git relay implementation details |
| `Logs/session-handoff.md` | Current organizational state |
