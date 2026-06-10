# Mac Mini Director Invocation Runbook

**Owner:** President Agent (defines protocol); Operations Director (executes policy sync via this protocol)  
**Version:** 3.0  
**Status:** Active — Automated Bridge (Phase 3) + Manual Fallback  
**Last updated:** 2026-06-06 (INI-028/INI-029 — Phase 3 active, canonical fetch bridge added, duplicate section removed)

---

## Purpose

Defines the approved protocol for invoking Claude Code director subagents on the Mac mini. The primary mechanism is the Agent Invocation Bridge (Phase 3 — all directors enabled). The manual protocol remains valid as a fallback during bridge outages.

---

## Current Architecture

Directors are installed as Claude Code subagents at `~/.claude/agents/` on the Mac mini ({{MAC_MINI_LOCAL_IP}} / {{MAC_MINI_HOSTNAME}}). Cowork sessions run in a sandboxed environment and cannot invoke Mac mini subagents directly.

**Phase 3 (active as of 2026-06-06):** The Agent Invocation Bridge supports all director subagents. The Engineering Director was the Phase 2 proof-of-concept; Phase 3 extended the registry to all six remaining directors following Security Steward review (CAP-REV-2026-06-06-002) and CEO approval.

---

## Automated Bridge Protocol (Phase 3 — Primary)

**Policy reference:** `operations/agent-invocation-policy.md`  
**Proposal reference:** `proposals/agent-invocation-bridge-proposal.md`

### How It Works

Cowork writes a task file atomically to `{{SANDBOX_DATA_ROOT}}/.agent-invoke`. A launchd WatchPaths agent (`com.jasonos.agent-dispatcher`) fires within ~1 second and runs the dispatcher script on the Mac mini. The dispatcher validates the task, acquires a per-director advisory lock, invokes `claude --agent <director>`, captures the output, and writes a result file that Cowork polls.

### Task File Format

Write to `{{SANDBOX_DATA_ROOT}}/.agent-invoke.tmp`, then atomically rename to `{{SANDBOX_DATA_ROOT}}/.agent-invoke`:

```
director:   <director-name>
task:       <PREFIX>: <natural-language instruction>
request-id: <caller-generated ID>    ← recommended; auto-generated if absent
timeout:    <seconds>                ← optional override; hard cap 30 minutes
push-git:   true|false              ← optional; task must include explicit instruction
requester:  cowork|scheduler        ← optional; for audit log
```

**Required task prefixes:** `READ:`, `DRAFT:`, `ANALYZE:`, `EXECUTE:`, `REVIEW:`, `UPDATE:`  
The prefix is stripped before the task is passed to the director.

**Field trust:** `requester:` is caller-declared and is not authenticated by the dispatcher. See `operations/agent-invocation-policy.md` § Field Trust Levels.

### Result File

The dispatcher writes to `{{SANDBOX_DATA_ROOT}}/.agent-invoke-result`:

```
status=<success|error|timeout|rejected|running|lock-timeout>
timestamp=<ISO8601>
request-id=<ID>
director=<name>
duration=<seconds>
output-log=<path>         ← present on success/error/timeout
error=<description>       ← present on error/timeout/rejected/lock-timeout
exit-code=<integer>       ← present on error
```

### Cowork Usage Pattern

```zsh
# 1. Write task file atomically
printf 'director: engineering-director\ntask: READ: Review workload at workloads/active/foo.md and summarize.\nrequest-id: req-001\nrequester: cowork\n' \
  > {{SANDBOX_DATA_ROOT}}/.agent-invoke.tmp
mv {{SANDBOX_DATA_ROOT}}/.agent-invoke.tmp {{SANDBOX_DATA_ROOT}}/.agent-invoke

# 2. Poll for result (dispatcher claims within ~1s; execution takes minutes)
for i in $(seq 1 240); do
  sleep 5
  [ -f {{SANDBOX_DATA_ROOT}}/.agent-invoke-result ] || continue
  STATUS=$(grep '^status=' {{SANDBOX_DATA_ROOT}}/.agent-invoke-result | cut -d= -f2)
  [ "$STATUS" = "running" ] && continue
  break
done
cat {{SANDBOX_DATA_ROOT}}/.agent-invoke-result

# 3. Read output log
OUTPUT_LOG=$(grep '^output-log=' {{SANDBOX_DATA_ROOT}}/.agent-invoke-result | cut -d= -f2)
[ -n "$OUTPUT_LOG" ] && cat "$OUTPUT_LOG"
```

### Lock-Timeout Retry Procedure

**What `status=lock-timeout` means:** The dispatcher attempted to acquire the per-director advisory lock for the requested director but could not do so within 60 seconds. This occurs when another invocation of the same director is already in progress. The task was **not executed** — no director agent was spawned.

**How to detect lock-timeout from Cowork:** Poll `{{SANDBOX_DATA_ROOT}}/.agent-invoke-result` as usual. When the `status=` field reads `lock-timeout`, the dispatcher has released the task without executing it.

**Task preservation:** When a lock-timeout occurs, the submitted task instruction is NOT preserved by the dispatcher — it was in the trigger file, which was consumed. The caller (President Agent or Cowork session) **must retain the original task instruction** in memory or in a working document before submitting it. Do not discard a task instruction until `status=success` is confirmed.

**Approved retry pattern:**

1. Read the result file and confirm `status=lock-timeout`.
2. Check whether the prior invocation is still running:
   ```zsh
   STATUS=$(grep '^status=' {{SANDBOX_DATA_ROOT}}/.agent-invoke-result | cut -d= -f2)
   echo "Current status: $STATUS"
   ```
   If the prior invocation completed (status is `success`, `error`, or `timeout`), it is safe to retry immediately.
3. If `status=running`, wait for the current invocation to complete before retrying. Poll every 30 seconds.
4. Once the prior invocation completes (or if status was already terminal), re-write the task file:
   ```zsh
   printf 'director: engineering-director\ntask: READ: <your original task here>\nrequest-id: req-001-retry1\nrequester: cowork\n' \
     > {{SANDBOX_DATA_ROOT}}/.agent-invoke.tmp
   mv {{SANDBOX_DATA_ROOT}}/.agent-invoke.tmp {{SANDBOX_DATA_ROOT}}/.agent-invoke
   ```
5. Poll the result file again as usual.

**Maximum retry attempts:** 3 retries after the initial lock-timeout.  
- After 3 retries without `status=success`, escalate to the President Agent.
- The President Agent determines whether to continue retrying, investigate the blocked director, or invoke manually.

**No work orphaning:** A lock-timeout means the task was not executed. The task instruction must always be preserved and re-submitted — never silently discarded. If the President Agent cannot confirm that a task was either successfully executed or explicitly abandoned, it must escalate to the CEO.

### Enabled Directors (Phase 3)

All directors are enabled in Phase 3. The Security Steward is permanently excluded.

| Director | Agent name | Bridge status |
|---|---|---|
| Engineering Director | `engineering-director` | Enabled (Phase 2 original + Phase 3) |
| Operations Director | `operations-director` | Enabled (Phase 3) |
| Knowledge Director | `knowledge-director` | Enabled (Phase 3) |
| Research Director | `research-director` | Enabled (Phase 3) |
| Creative Director | `creative-director` | Enabled (Phase 3) |
| Financial Analysis Director | `financial-analysis-director` | Enabled (Phase 3) |
| Venture Director | `venture-director` | Enabled (Phase 3) |
| Security Steward | `security-steward` | **Permanently excluded** — manual invocation only |

### Bridge Key Files

| File | Purpose |
|---|---|
| Worker script (source) | `{{SANDBOX_DATA_ROOT}}/bin/jasonos-agent-dispatcher.sh` |
| Worker script (installed) | `~/bin/jasonos-agent-dispatcher.sh` |
| Plist | `~/Library/LaunchAgents/com.jasonos.agent-dispatcher.plist` |
| Activation | `{{SANDBOX_DATA_ROOT}}/bin/jasonos-agent-dispatcher-activate.command` |
| Smoke test | `{{SANDBOX_DATA_ROOT}}/bin/jasonos-agent-dispatcher-test.sh` |
| Master log | `{{SANDBOX_DATA_ROOT}}/Logs/director-logs/agent-invocation-master.log` |
| Dispatcher log | `{{SANDBOX_DATA_ROOT}}/Logs/director-logs/agent-dispatcher.log` |
| Policy | `operations/agent-invocation-policy.md` |

---

## Canonical Fetch Bridge

*Added INI-028. Enables Cowork-side policy sync without outbound network calls.*

The Cowork sandbox cannot reach GitHub directly (HTTPS proxy-blocked, confirmed 2026-06-05). To allow the daily policy sync task to compare the working tree against canonical upstream, the Mac mini runs a launchd agent that fetches the `upstream/main` ref nightly and makes it available on the shared volume.

### How It Works

A launchd agent (`com.jasonos.canonical-fetch`) runs `git fetch upstream main` at 00:30 daily on the Mac mini. After the fetch, `upstream/main` is a locally resolvable ref on SandboxData. The Cowork policy sync task reads this ref with `git diff upstream/main --name-only` — zero outbound network calls from Cowork.

### Installation

1. Copy the plist to the LaunchAgents directory on the Mac mini:
   ```zsh
   cp {{SANDBOX_DATA_ROOT}}/bin/com.jasonos.canonical-fetch.plist \
      ~/Library/LaunchAgents/com.jasonos.canonical-fetch.plist
   ```
2. Load the agent:
   ```zsh
   launchctl bootstrap gui/$(id -u) ~/Library/LaunchAgents/com.jasonos.canonical-fetch.plist
   ```
3. Verify it registered:
   ```zsh
   launchctl list | grep canonical-fetch
   ```
4. Trigger an immediate fetch to seed the cache (optional but recommended):
   ```zsh
   launchctl kickstart gui/$(id -u)/com.jasonos.canonical-fetch
   ```

### Prerequisites

- `upstream` remote must point to `git@github.com:{{CANONICAL_GITHUB_USER}}/{{REPO_NAME}}` (SSH key required; not HTTPS — HTTPS may be proxy-blocked)
- `/bin/zsh` must have Full Disk Access (shared with git relay and dispatcher)

### Key Files

| File | Purpose |
|---|---|
| Plist (source) | `{{SANDBOX_DATA_ROOT}}/bin/com.jasonos.canonical-fetch.plist` |
| Plist (installed) | `~/Library/LaunchAgents/com.jasonos.canonical-fetch.plist` |
| Fetch log | `{{SANDBOX_DATA_ROOT}}/Logs/director-logs/engineering/canonical-fetch.log` |

### Troubleshooting

| Symptom | Likely cause | Resolution |
|---|---|---|
| `upstream/main` not found after install | First fetch not run yet | `launchctl kickstart gui/$(id -u)/com.jasonos.canonical-fetch` then check log |
| Fetch fails daily | SSH key not available to launchd user context | Verify key is in keychain and agent has FDA; check canonical-fetch.log |
| Policy sync shows stale comparison | Agent not loaded after reboot | `launchctl bootstrap gui/$(id -u) ~/Library/LaunchAgents/com.jasonos.canonical-fetch.plist` |

---

## Manual Invocation Protocol (Fallback)

The manual protocol remains valid when the bridge is unavailable (e.g., SandboxData not mounted, launchd agent not running, or during debugging).

### Prerequisites

- Mac mini terminal access (local or via Tailscale + SSH)
- Claude Code installed and authenticated on Mac mini
- Governance repo synced at `{{SANDBOX_DATA_ROOT}}/Governance/{{REPO_NAME}}/`

### Invocation Steps

1. Open terminal on Mac mini (or SSH: `ssh {{SYSTEM_USER}}@{{MAC_MINI_HOSTNAME}}` or via Tailscale)
2. Navigate to the governance repo:
   ```bash
   cd {{SANDBOX_DATA_ROOT}}/Governance/{{REPO_NAME}}
   ```
3. Invoke the desired director subagent with Claude Code:
   ```bash
   claude --agent <agent-name> "<task instruction>"
   ```
   Example:
   ```bash
   claude --agent engineering-director "Read the session log and summarize findings"
   ```
4. Monitor output; director will log results to `{{SANDBOX_DATA_ROOT}}/Logs/director-logs/<director>/`
5. President Agent reviews logs after invocation

### Available Agent Names

| Director | Agent name |
|---|---|
| Engineering Director | `engineering-director` |
| Operations Director | `operations-director` |
| Knowledge Director | `knowledge-director` |
| Research Director | `research-director` |
| Creative Director | `creative-director` |
| Financial Analysis Director | `financial-analysis-director` |
| Venture Director | `venture-director` |
| Security Steward | `security-steward` |

---

## Security Notes

- Never pass credentials or secrets as CLI arguments or task fields
- All invocations must be logged manually by the invoking party if the director's own logging fails
- If a director produces unexpected output or appears to exceed its policy bounds, halt invocation and escalate to President Agent
- Task fields submitted via the bridge are logged verbatim in the master invocation log — Security Steward-readable

---

## Git Operations Boundary

All git operations on the governance repo must originate from the Mac mini — not from Cowork.

**Why:** The Cowork sandbox creates `index.lock` files on the APFS-over-USB volume that it lacks permission to remove. The sandbox also cannot delete files on `{{SANDBOX_DATA_ROOT}}/` generally (`rm` returns Operation not permitted). Git credentials (SSH key) live in the Mac mini's environment and are not available to the Cowork sandbox. GitHub remote access is also proxy-blocked from the sandbox.

**Division of responsibility:**
- Cowork: file editing only (Read, Write, Edit tools)
- Mac mini: git stage, commit, push, fetch, diff

## Git Commit Relay

To avoid requiring the CEO to manually run git commands after each Cowork editing session, the Engineering Director built a drop-file relay with a launchd WatchPaths agent.

**How it works:** Cowork writes a trigger file to `{{SANDBOX_DATA_ROOT}}/.git-commit-trigger`. The launchd agent fires within ~1 second, runs `git add -A`, commits, and pushes from the Mac mini's native environment. Cowork polls `{{SANDBOX_DATA_ROOT}}/.git-commit-result` for the outcome.

**Cowork usage:**
```zsh
printf 'message: fix: describe the change\n' > {{SANDBOX_DATA_ROOT}}/.git-commit-trigger.tmp
mv {{SANDBOX_DATA_ROOT}}/.git-commit-trigger.tmp {{SANDBOX_DATA_ROOT}}/.git-commit-trigger
```

**Key files:**
- Worker: `~/bin/jasonos-git-relay.sh`
- Plist: `~/Library/LaunchAgents/com.jasonos.git-relay.plist`
- Activation: `{{SANDBOX_DATA_ROOT}}/bin/jasonos-git-relay-activate.command`
- Smoke test: `{{SANDBOX_DATA_ROOT}}/bin/jasonos-git-relay-test.sh`
- Workload policy: `workloads/active/git-commit-relay.md`

**Prerequisite:** `/bin/zsh` must have Full Disk Access in System Settings → Privacy & Security → Full Disk Access. Without this, the launchd-spawned process cannot access the external volume.

## Policy Sync (Special Case)

The daily policy sync runs as a Cowork scheduled task (`jasonos-policy-sync`). It performs a local git status check against the working tree and compares against `upstream/main` using the canonical fetch bridge (see above).

**v1.1 behavior (current):** The policy sync reads the locally cached `upstream/main` ref — no outbound network calls from Cowork. The canonical-fetch bridge must be running on the Mac mini for remote comparison to work.

```bash
# Compare working tree against canonical upstream (runs on Mac mini via canonical-fetch)
git -C {{SANDBOX_DATA_ROOT}}/Governance/{{REPO_NAME}} diff upstream/main --name-only
```

If `upstream/main` is not found, the policy sync reports "Remote comparison unavailable — canonical-fetch may not have run yet" and links to `canonical-fetch.log`.

**Canonical source for policy sync task prompt:** `operations/policy-sync-task.md`
