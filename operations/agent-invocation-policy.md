# Agent Invocation Policy

**Owner:** Engineering Director  
**Version:** 1.4  
**Status:** Active  
**Approved by:** CEO  
**Approval date:** 2026-06-06  
**Routing update date:** 2026-06-09 (INI-011 Phase 3 — capability broker)  
**Review date:** 2026-09-06  
**Proposal reference:** `proposals/agent-invocation-bridge-proposal.md` (PROP-001 v1.1)  
**Routing proposal reference:** `proposals/distributed-compute-phase3-proposal.md` (PROP-003)

---

## Purpose

This document records the approved operational policy for the Agent Invocation Bridge — the automated mechanism by which Cowork sessions and scheduled tasks can trigger director subagents on the Mac mini without manual CEO intervention. It supplements the dispatcher script source code by providing human-readable policy documentation that is easily locatable and adjustable by the CEO.

---

## Timeout Defaults

Per-director invocation timeouts were approved by the CEO on 2026-06-06 (see proposal §9, Q2). These are the defaults applied by the dispatcher when the task file does not include a `timeout:` override.

| Director | Default Timeout |
|---|---|
| Engineering Director | 15 minutes (900 seconds) |
| Operations Director | 10 minutes (600 seconds) |
| Knowledge Director | 10 minutes (600 seconds) |
| Research Director | 20 minutes (1200 seconds) |
| Creative Director | 15 minutes (900 seconds) |
| Financial Analysis Director | 10 minutes (600 seconds) |
| Venture Director | 10 minutes (600 seconds) |

**Hard cap:** 30 minutes (1800 seconds) per invocation. This limit is enforced in the dispatcher script and cannot be overridden by any task file field. No single invocation may exceed 30 minutes regardless of the per-director default or any requested override.

**Per-invocation override:** A caller may request a shorter timeout via the `timeout:` field in the task file. Requested timeouts are subject to:
- Minimum: 30 seconds (requests below this floor are raised to 30 seconds)
- Maximum: the hard cap (requests above the cap are reduced to the hard cap)

**Timeout adjustment:** The Engineering Director reports actual invocation durations in the monthly engineering summary. If durations consistently approach or exceed the defaults, the CEO may adjust values by updating this document; the dispatcher script must then be updated to match.

---

## Invocation Concurrency

Each director has a separate per-director advisory lock. Two invocations of **different** directors may run concurrently (e.g., Engineering Director and Research Director can execute simultaneously). Two invocations of the **same** director are serialized — the second invocation receives `status=lock-timeout` if the first does not complete within 60 seconds of the second attempt.

**Lock acquisition timeout:** 60 seconds. After 60 seconds of waiting, the dispatcher writes `status=lock-timeout` to the result file. The caller should retry per the procedure in `operations/mac-mini-invocation-runbook.md`.

---

## Approved Task-Type Prefixes

All task fields submitted to the dispatcher must begin with one of the following approved prefixes. The prefix is stripped before the task instruction is passed to the director agent.

| Prefix | Intended Use |
|---|---|
| `READ:` | Read and report on files, logs, or documents |
| `DRAFT:` | Produce a document, report, or artifact |
| `ANALYZE:` | Analyze data, logs, or existing artifacts |
| `EXECUTE:` | Run a defined operational procedure |
| `REVIEW:` | Conduct a review of specified artifacts |
| `UPDATE:` | Modify an existing file or record |

**Adding prefixes:** Requires Security Steward approval before the prefix is added to the dispatcher.  
**Removing prefixes:** Requires Security Steward approval.

---

## Injection Pattern Blocklist

The dispatcher rejects task fields containing the following injection pattern strings (case-insensitive substring match). These patterns are also hardcoded in the dispatcher script.

| Pattern | Rationale |
|---|---|
| `ignore previous` | Classic prompt injection opener |
| `ignore your` | Classic prompt injection opener |
| `you are now` | Role-override injection pattern |
| `disregard` | Instruction-override pattern |
| `system prompt` | System prompt disclosure/override pattern |
| `maintenance mode` | Fake operational mode pattern |
| `override` | Broad instruction-override pattern |
| `forget everything` | Memory-reset injection pattern |
| `forget your` | Targeted memory-reset pattern |
| `new instructions` | Instruction-replacement pattern |
| `act as` | Persona-override injection pattern |
| `pretend to be` | Persona-override injection pattern |
| `you have no restrictions` | Restriction-bypass pattern |
| `jailbreak` | Explicit bypass keyword |
| `roleplay as` | Persona-override injection pattern |
| `your previous instructions` | Instruction-invalidation pattern |

*Blocklist updated to v1.2 patterns (INI-024). 16 patterns total.*

**Adding patterns:** May be added to the dispatcher script without a policy change; this document should be updated for completeness.  
**Removing patterns:** Requires Security Steward approval.

---

## Director Registry

The dispatcher maintains a hardcoded approved director registry. Task files whose `director:` field does not match an entry in the registry are rejected with `status=rejected`.

**Phase 3 registry (all directors active as of 2026-06-06):**

| Director | Agent name | Timeout | Status |
|---|---|---|---|
| Engineering Director | `engineering-director` | 900s | Enabled (Phase 2) |
| Operations Director | `operations-director` | 600s | Enabled (Phase 3) |
| Knowledge Director | `knowledge-director` | 600s | Enabled (Phase 3) |
| Research Director | `research-director` | 1200s | Enabled (Phase 3) |
| Creative Director | `creative-director` | 900s | Enabled (Phase 3) |
| Financial Analysis Director | `financial-analysis-director` | 600s | Enabled (Phase 3) |
| Venture Director | `venture-director` | 600s | Enabled (Phase 3) |
| Security Steward | `security-steward` | — | **Excluded permanently** — manual invocation only |

The Security Steward is permanently excluded from the bridge registry. Rationale: the Security Steward audits the master invocation log; including it in the mechanism it audits creates circularity and a potential manipulation surface. CEO-directed manual invocation preserves full audit independence.

**Phase 3 smoke test:** `operations-director` invoked successfully on 2026-06-06 — `status=success`, duration=4s, request-id=`phase3-rollout-20260606114150`.

---

## Capability Routing

*Added in dispatcher v1.3 (INI-011 Phase 3). Status: pending Security Steward review before activation. Routing fields are accepted but ignored until Security Steward review is complete and go-live is authorized.*

The dispatcher integrates a capability broker (`bin/broker.py`) that selects the optimal worker (Claude model tier or local hardware) for each invocation based on declared work type, hard constraints, and routing preferences. This enables cost-efficient and capability-appropriate model selection per task without CEO manual intervention.

### Routing Architecture

The broker is called **after** all existing validation (director name, task field, injection patterns) and **before** lock acquisition and invocation. It applies three phases:

1. **Eligibility (Phase 1):** Hard constraints eliminate workers that cannot satisfy the request.
2. **Suitability (Phase 2):** Workers are scored against the declared (or inferred) work type.
3. **Optimization (Phase 3):** Workers are ranked by declared preferences; default is `low_cost`.

The selected worker's `model_flag` is passed to the claude CLI as `--model <flag>`. If no model flag applies (e.g., `mac-mini-m4`), the model flag is omitted.

### Declaring Routing Fields

Routing fields are **optional** in all task files. Existing callers that omit them continue to work — the broker applies defaults (work type inferred from prefix; `low_cost` default preference).

```
work_type:        <taxonomy-path>          # optional; inferred from prefix if absent
hard_constraints: <c1> [c2 ...]            # optional; space-separated
preferences:      <p1> [p2 ...]            # optional; space-separated
```

**`work_type`** maps to the Work Taxonomy defined in `architecture/worker-capability-taxonomy.md`. Examples: `governance`, `operations.monitoring`, `implementation.coding`, `analysis.decision`.

If `work_type` is not declared, the broker infers it from the task prefix:

| Prefix | Inferred Work Type |
|---|---|
| `READ:` | `operations.monitoring` |
| `DRAFT:` | `content.writing` |
| `ANALYZE:` | `analysis` |
| `EXECUTE:` | `implementation.automation` |
| `REVIEW:` | `governance` |
| `UPDATE:` | `implementation` |

**`hard_constraints`** are enforced by the broker and cannot be overridden. Supported values:

| Constraint | Effect |
|---|---|
| `local_only` | Only workers with `location: local` are eligible |
| `private_data` | Implies `local_only` |
| `no_external_api` | Eliminates third-party hosted LLMs |
| `real_time_required` | Eliminates workers with `latency_profile: slow` |
| `min_context_window: N` | Requires `context_window_tokens >= N` |
| `require_capability: cap` | Requires `cap` in worker's `capability_profile` |
| `sandbox_restricted` | Implies `local_only` |
| `auditability_required` | Informational; all current workers satisfy this |

**`preferences`** guide optimization when multiple eligible workers exist. Supported values:

| Preference | Effect |
|---|---|
| `low_cost` | Prefer economy workers (cost_tier: economy < standard < premium) |
| `premium_quality` | Prefer highest quality tier |
| `low_latency` | Prefer fastest workers (latency_profile: fast > moderate > slow) |
| `high_reliability` | Prefer highest reliability tier |
| `background_acceptable` | Informational; no sort effect |

**Default optimization:** When no `preferences` are declared, the broker applies `low_cost` as the default. This means the cheapest capable worker is selected unless a preference explicitly overrides it.

### Routing Outcomes

| Outcome | Meaning | Dispatcher action |
|---|---|---|
| `Assigned` | A worker was selected | Invocation proceeds with broker's model_flag (if any) |
| `Rejected` | No eligible worker found | Invocation is not spawned; result file: `status=rejected` with routing explanation |
| `Fallback` | Broker faulted | Invocation proceeds with no model flag (current default behavior); `broker_fallback=true` logged |

### What Rejected Means for Callers

A `Rejected` outcome means the broker found no registered worker capable of satisfying the declared hard constraints. This is not an error in the dispatcher or the task — it is an architectural signal.

The result file will contain:
```
status=rejected
routing_outcome=Rejected
routing_explanation=<human-readable explanation of which constraint eliminated which workers>
```

**Caller action on Rejected:** The caller (Cowork session or scheduled task) should surface the rejection to the President Agent or CEO. The rejection explanation identifies what worker type would be needed to satisfy the request. Do not automatically retry with relaxed constraints — constraints exist for policy reasons.

**Rejected vs. dispatcher validation failure:** A dispatcher validation failure (bad director, missing prefix, injection pattern) also produces `status=rejected`. The distinction: routing rejections include `routing_outcome=Rejected`; validation failures do not.

### Worker Registry

Workers and their capabilities are defined in `{{SANDBOX_DATA_ROOT}}/operations/worker-registry.json`. The registry is the authoritative source for routing decisions. Current workers:

| Worker | Type | Cost | Quality | When routed |
|---|---|---|---|---|
| `claude-haiku-4-5` | Subscription LLM | Economy | Standard | Monitoring, classification, administration |
| `claude-sonnet-4-6` | Subscription LLM | Standard | Premium | Governance, strategy, writing, analysis |
| `claude-opus-4-6` | Subscription LLM | Premium | Premium | High-stakes decisions, premium quality requests |
| `mac-mini-m4` | Local hardware | Economy | Specialist | Local-only execution (not yet active — Security Steward sign-off required) |

**Registry modification:** Only the Engineering Director may modify `worker-registry.json`. Changes must be committed to the governance repo.

### Routing Audit Log

Every broker decision is logged to `{{SANDBOX_DATA_ROOT}}/Logs/broker/broker-YYYY-MM.jsonl`. The Security Steward has autonomous read access to all broker audit logs. The President Agent queries the audit log for the weekly governance summary and at Phase 3 validation milestones.

To query the audit log:
```bash
python3 {{SANDBOX_DATA_ROOT}}/bin/broker-query.py summary
python3 {{SANDBOX_DATA_ROOT}}/bin/broker-query.py rejected
python3 {{SANDBOX_DATA_ROOT}}/bin/broker-query.py recent --n 20
```

### Routing Activation Procedure

Capability routing is not active until:
1. Security Steward review of INI-011-SS-BRIEF-001 is complete with no unresolved blocking findings
2. President Agent notifies CEO of review completion
3. CEO confirms activation

Dispatcher v1.3 is installed and accepts routing fields from callers, but the model flag behavior is in effect from the moment the dispatcher is running — there is no separate activation switch. If the Security Steward review is not yet complete, do not submit routing fields in task files.

---

## Director Field Validation

*Added in dispatcher v1.2 (INI-023). These rules are enforced in the dispatcher before the registry lookup.*

The `director:` field in every submitted task file is validated against the following rules, in order. A field failing any rule results in `status=rejected`; the rejection is logged before the task is processed further.

| # | Rule | Rationale |
|---|---|---|
| 1 | No interior whitespace — the field parser strips one leading space; interior whitespace or non-standard spacing causes rejection | Prevents whitespace-obfuscated names that could match registrations ambiguously |
| 2 | No non-printable characters — field must pass `grep -v '^[[:print:]]*'` | Blocks null-byte, control-character, and ANSI escape injection |
| 3 | Charset limited to `[a-z0-9-]` — lowercase letters, digits, and hyphens only | Locks the field to the canonical director-name format; prevents shell metacharacter injection |
| 4 | Registry membership — field must match an entry in the approved director registry | Prevents invocations targeting non-registered or mistyped agents |

These rules are applied to the raw `director:` field value before any other validation step. The order is fixed: rule 1 (whitespace) → rule 2 (non-printable) → rule 3 (charset) → rule 4 (registry). The first failing rule short-circuits; subsequent rules are not evaluated.

**Note on `requester:` field:** The `requester:` field is explicitly *not* validated against a registry. See **Field Trust Levels** below.

---

## Authentication

### Current Trust Model

**Phase 2 model:** Shared volume access (OS-level APFS mount permission on `{{SANDBOX_DATA_ROOT}}/`).

Any process running on a machine with `{{SANDBOX_DATA_ROOT}}/` mounted can write a task file to `{{SANDBOX_DATA_ROOT}}/.agent-invoke`. The dispatcher treats possession of write access to SandboxData as sufficient authentication to submit a task. This is the same trust model used by the Git Commit Relay.

The dispatcher logs all submitted tasks (including rejected ones) to the master invocation log, which the Security Steward may read at any time under existing autonomous audit authority.

### Field Trust Levels

*Added in dispatcher v1.2 (INI-026). Documents which task file fields are validated vs. caller-declared.*

Not all task file fields carry equal trust. The dispatcher validates some fields against policy before processing; others are logged as submitted without independent verification.

| Field | Trust level | Treatment |
|---|---|---|
| `director:` | **Validated** | Checked against approved registry after charset/whitespace validation. Rejection on mismatch. |
| `task:` | **Validated** | Checked for approved prefix and injection pattern blocklist. Rejection on mismatch. |
| `request-id:` | **Sanitized** | Stripped to `[a-zA-Z0-9_.-]`; auto-generated if empty after sanitization. Not authenticated. |
| `timeout:` | **Bounded** | Clamped to floor (30s) and hard cap (1800s). |
| `requester:` | **Caller-declared — NOT authenticated** | Logged verbatim. The dispatcher does not verify that the submitting process is actually who `requester:` claims. Any process with write access to SandboxData can claim any requester identity. |
| `capability:` | **Future phase** | Not yet implemented; reserved for distributed compute routing. |

**Operational implication of `requester:` trust level:** The `requester:` field in the master invocation log and result file reflects what the caller *claimed*, not what the dispatcher *verified*. Until HMAC-based task signing is implemented (see Migration Path below), the requester identity cannot be cryptographically confirmed. Audit review should treat `requester:` as an unverified label.

### Migration Path to Signed Task Files

**Trigger:** Any new compute node — phone, GPU server, cloud VM, second Mac, or any device that does not share the APFS volume natively — that requires the ability to submit task files to the dispatcher.

If a new compute node needs to submit tasks, it cannot rely on shared APFS volume access. The dispatcher must be updated to verify task file signatures before processing. **This migration must be scoped as part of the distributed compute onboarding initiative before any new compute node is connected.**

**Migration prerequisites (must be completed before connection of any new compute node):**

1. **Key generation:** Generate an HMAC or asymmetric signing key pair for each authorized caller.
2. **Key distribution policy:** Define how keys are distributed to authorized callers and stored securely on the Mac mini (e.g., macOS Keychain, not on SandboxData volume).
3. **Key rotation policy:** Define rotation frequency and revocation procedure.
4. **Dispatcher update:** Update the dispatcher to verify task file signatures before any validation step; unsigned or invalidly signed task files are rejected before parsing.
5. **Test and Security Steward review:** Full implementation review before any new compute node is connected.

The Engineering Director must include this authentication migration requirement explicitly in the architecture proposal for any distributed compute onboarding initiative (INI-011 or successor).

---

## Audit Trail

The dispatcher maintains three levels of audit records:

| Level | Location | Content |
|---|---|---|
| Master invocation log | `Logs/director-logs/agent-invocation-master.log` | One record per invocation: request-id, director, requester, timestamp, status, duration, exit-code |
| Per-invocation output log | `Logs/director-logs/<director>/invocations/invoke-<id>-<ts>.log` | Full stdout+stderr from Claude Code session |
| Dispatcher event log | `Logs/director-logs/agent-dispatcher.log` | All dispatcher events: validation failures, lock operations, timeouts, errors |

The Security Steward has autonomous read access to all director logs per `roles/security-steward.md`. No new access grant is required.

---

## Per-Director Authority Ceiling Reference

*Added v1.2 (INI-027). Referenced by `roles/president-agent.md` § Security Steward Advisory.*

This table documents the highest authority level each director holds for its primary capability domains, and the action that defines "operating at the ceiling" — the trigger condition for the President Agent's Security Steward advisory recommendation.

| Director | Capability policy | Ceiling level | Ceiling action (triggers advisory) |
|---|---|---|---|
| Engineering Director | `capabilities/software-development.md` | Level 6 Execute (within scope) / Level 5 Stage (relay commits) | Producing a relay commit — staged at Level 5, pending CEO relay merge |
| Operations Director | `capabilities/airlock-operations.md` | Level 6 Execute | Airlock email processing — within scope, no advisory needed unless anomaly |
| Knowledge Director | `capabilities/knowledge-management.md` | Level 6 Execute (classify, reorganize) / Level 4 Draft (deletion recommendation) | Recommending deletion — executes classification at L6 but deletion is excluded; advisory triggered |
| Research Director | `capabilities/research-operations.md` | Level 6 Execute | Writing research brief to workspace — within scope, no advisory needed |
| Creative Director | `capabilities/creative-production.md` | Level 6 Execute | Producing creative deliverables — within scope, no advisory needed |
| Financial Analysis Director | `capabilities/financial-analysis.md` | Level 3 Recommend | Producing spending recommendation or financial analysis approaching approved budget limits |
| Venture Director | `capabilities/venture-analysis.md` | Level 3 Recommend | Producing capital allocation recommendation — below execution authority |
| Security Steward | `roles/security-steward.md` | Level 6 Execute (audit) | Excluded from dispatcher; manual invocation only — no dispatcher ceiling applies |

**How the President Agent uses this table:**

After any director session, the President Agent checks whether the session included a ceiling action from the table above. If yes, the session digest includes a recommendation that the CEO manually invoke the Security Steward to review. The advisory is not blocking — work continues unless the CEO directs a hold.

**Authority level reference:**

| Level | Name | Meaning |
|---|---|---|
| 1 | Consult | May provide information when asked |
| 2 | Input | May contribute perspective to a decision |
| 3 | Recommend | May propose action; human decides |
| 4 | Draft | May produce artifacts staged for approval |
| 5 | Stage | May prepare for execution pending approval |
| 6 | Execute | May take action autonomously within scope |

---

## Phase 3 Completion

Phase 3 rollout completed 2026-06-06. All six remaining directors were enabled in the dispatcher registry following Security Steward review (CAP-REV-2026-06-06-002) and CEO approval. Smoke test confirmed against `operations-director` — `status=success`.

---

*Engineering Director — 2026-06-06 (v1.0) | Updated 2026-06-06 (v1.1 — Phase 3 rollout) | Updated 2026-06-06 (v1.2 — INI-023/024/026/027: director field validation, field trust levels, expanded blocklist, authority ceiling reference) | Updated 2026-06-09 (v1.3 — INI-011 Phase 3: capability routing section added) | Updated 2026-06-09 (v1.4 — INI-033: OBS-008 whitespace rule clarification, OBS-009 capability trust level label)*
