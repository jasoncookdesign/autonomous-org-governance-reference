# Temporary Agent Framework Policy

**Version:** 1.0  
**Status:** Active  
**Edit authority:** CEO only

## Purpose

Defines the rules under which the President Agent may authorize temporary task agents on behalf of directors. This framework is the CEO-approved standing authority for temporary agent creation. Individual temporary agents are authorized by the President Agent within this framework without per-agent CEO approval, provided all conditions below are met.

## Definition

A temporary task agent is a bounded, time-limited agent instantiated to perform a specific task on behalf of a director. It is not a permanent role. It does not appear in the organizational role catalog. It ceases to exist at task completion or duration expiry, whichever comes first.

A temporary agent is not a new role. It is a scoped delegation of an existing director's authority to a specific task.

## Naming Convention

Every temporary agent must be assigned a name at authorization using the following format:

```
tmp-[director-prefix]-[task-slug]-[YYYYMMDD]
```

Examples:
- `tmp-eng-api-integration-20260615`
- `tmp-ops-manifest-cleanup-20260615`
- `tmp-know-inbox-triage-20260615`

Director prefixes: `eng` (Engineering), `ops` (Operations), `know` (Knowledge), `res` (Research), `cre` (Creative), `fin` (Financial Analysis), `ven` (Venture)

Names must be unique within a session. If two temporary agents are authorized on the same day for the same director and task type, append a sequential suffix (`-001`, `-002`).

## Authorization Conditions

The President Agent may authorize a temporary task agent only when all of the following are true:

1. A director has submitted a written request identifying:
   - The task to be performed
   - The specific capabilities required (must be a subset of the requesting director's existing capability set)
   - The expected duration
   - Why the task cannot be performed by the director directly

2. The requested scope does not include any authority, tool, system, account, or capability not already held by the requesting director

3. The President Agent has assessed the impact on other directors' work and found it acceptable

4. The President Agent has produced a scoping declaration (see below) before the agent begins any work

## Scoping Declaration

The President Agent must produce a scoping declaration for every temporary agent at the time of authorization. The declaration must be recorded in `audit/president-lifecycle-log.md` and communicated to the requesting director and the Security Steward.

Required declaration fields:

```
Agent name: [tmp-prefix-task-date]
Requesting director: [role name]
Task description: [specific task]
Authorized capabilities: [list — subset of requesting director's capability set]
Explicitly excluded capabilities: [any director capabilities not granted to this agent]
Authorized systems: [specific systems within the director's approved scope]
Authorized data classes: [A / B / C as applicable]
Duration: [expected completion date or session count]
Escalation trigger: [what happens if duration is exceeded]
Log destination: [requesting director's log]
Termination authority: [requesting director confirms; President Agent logs retirement]
```

## Capability Inheritance

A temporary agent inherits a subset of the requesting director's capability set, as defined in the scoping declaration. It does not inherit the full director capability set by default.

The scoping declaration must list authorized capabilities explicitly. Any capability not listed is denied. The deny-by-default principle applies within the temporary agent's scope just as it applies organization-wide.

A temporary agent may not:
- Access any system not listed in its scoping declaration
- Spend money unless the requesting director's wallet is explicitly authorized in the declaration and the director remains accountable for all charges
- Create sub-agents of its own
- Request authority expansions — expansions require a new President Agent authorization
- Persist beyond task completion or duration expiry

## Log Routing

Temporary agent logs route to the requesting director's operational log. The director is fully accountable for the behavior of any temporary agent it requested. The Security Steward audits temporary agent activity through the director's log, not through a separate log file.

The scoping declaration in `audit/president-lifecycle-log.md` is the Security Steward's reference for what the temporary agent was authorized to do.

## Duration and Termination

**Normal termination:**
1. Temporary agent completes the task
2. Director confirms task completion to the President Agent in writing
3. President Agent logs retirement in `audit/president-lifecycle-log.md`
4. Agent ceases operation

**Duration expiry:**
If a temporary agent is still active past its declared duration, the President Agent must:
1. Flag the overrun to the requesting director immediately
2. Require the director to either confirm completion and terminate, or submit a written extension request
3. Log the overrun in the lifecycle log
4. Escalate to CEO if the director does not respond within 24 hours or if the extension would require new authority

**Immediate termination:**
The Security Steward may direct the immediate halt of a temporary agent under the same containment authority it holds over permanent directors. The requesting director executes the halt. The President Agent logs the event.

## Prohibitions

- A temporary agent may not be used to perform work that requires CEO approval as a permanent role
- A temporary agent may not be authorized as a workaround for a missing or unapproved capability
- A temporary agent may not persist indefinitely through repeated extensions without CEO review — three extensions of the same agent require a CEO decision on whether the work warrants a permanent role
- The President Agent may not pre-authorize a pool of temporary agents speculatively — each must be tied to a specific, identified task

## Security Steward Oversight

The Security Steward reviews temporary agent lifecycle entries in `audit/president-lifecycle-log.md` as part of its routine audit. It must escalate to the President Agent and CEO if:

- A temporary agent's scoping declaration exceeds the requesting director's capability set
- A temporary agent is still active past its declared duration without a logged extension
- A temporary agent has received three or more extensions without CEO review
- A temporary agent appears to be performing work that should require a permanent role

## Policy Change

Changes to this framework require CEO approval. The President Agent may recommend changes through the policy change process but may not enact them.
