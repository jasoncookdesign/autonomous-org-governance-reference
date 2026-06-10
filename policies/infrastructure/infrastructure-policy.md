# Infrastructure Policy

**Version:** 1.0  
**Status:** Active  
**Edit authority:** CEO only

## Purpose

Defines the sandbox environment boundaries, approved hardware, network identity, account separation rules, and remote access configuration. Infrastructure is an implementation detail — governance is the root of trust — but infrastructure decisions affect the security of every capability.

## Sandbox Host

The sandbox organization operates on a dedicated Mac mini. This machine is the primary compute, storage, and execution environment for all sandbox agents and tools.

The Mac mini is a single-tenant sandbox host. It is not shared with personal workloads unless the CEO explicitly approves a cross-use case.

## Network Identity

| Parameter | Value |
| --- | --- |
| Hostname | `{{MAC_MINI_HOSTNAME}}` |
| IP Address | `{{MAC_MINI_LOCAL_IP}}` (DHCP reservation on router) |
| VPN | Tailscale (approved for secure remote access) |

A fixed DHCP reservation is required. The sandbox must not operate with a dynamic IP address that changes between sessions.

## Remote Access

| Method | Status | Notes |
| --- | --- | --- |
| macOS Screen Sharing | Approved | System Settings → General → Sharing |
| Remote Login (SSH) | Approved | System Settings → General → Sharing → Remote Login |
| Tailscale | Approved | For access outside local network |

Remote access credentials must not be shared with or accessible from within the sandbox itself. Remote access is a clean-side control mechanism, not a sandbox capability.

## Account Separation

| Account Type | Rule |
| --- | --- |
| CEO personal accounts | Must not be signed in on the Mac mini sandbox host (except as explicitly noted below) |
| CEO personal cloud drive | Must not be synced to the sandbox host |
| CEO personal password manager | Must not be installed or accessible on the sandbox host |
| Organizational accounts | {{OPERATOR_EMAIL}} and purpose-built organizational accounts only |
| GitHub | CEO personal account — explicit Article VI exception (see below) |

If a CEO personal account must temporarily be used on the sandbox host for setup purposes not covered by an existing exception, it must be signed out and credentials removed before sandbox agents begin operating.

## Article VI Identity Firewall Exception — GitHub

The CEO has authorized a bounded exception to the Article VI Identity Firewall permitting sandbox agents to use the CEO's personal GitHub account for approved repository operations.

**Scope of exception:**
- The Engineering Director may clone approved repositories and, with per-operation CEO approval, push to approved repositories
- Access is limited strictly to approved repository operations — the Engineering Director may not view, modify, or access account settings, billing, profile, followers, other repositories, or any GitHub feature beyond repository read/write operations on approved repos
- This exception does not authorize creating new repositories, starring, forking, or any social or account-management action without explicit CEO approval
- This exception does not extend to any other CEO personal account

**Risk acknowledgment:** Engineering Director activity appears in the CEO's personal GitHub account history. The per-operation CEO push approval and the prohibition on unapproved repository access are the primary controls on blast radius.

## Approved Software Environment

The sandbox host runs the following at launch:

| Category | Approved Software |
| --- | --- |
| Operating System | macOS (current supported version) |
| IDE | VS Code |
| AI CLI | Claude Code |
| Version Control | Git CLI, GitHub (clone; push requires CEO approval) |
| Runtime | Node.js, Python |
| Package Managers | npm, Homebrew |
| Shell | zsh |
| Containers | Docker |
| Network | Tailscale |
| Communication | Google Drive (via {{OPERATOR_EMAIL}}) — approved |
| | Gmail (via {{OPERATOR_EMAIL}}) — approved |
| | All other Google Workspace tools — unapproved until CEO approval |
| LLM API | Anthropic API (Claude only at launch) |

Additional software requires CEO approval before installation. Containerization tools beyond Docker require CEO approval. Local model execution requires CEO approval. Additional LLM providers require CEO approval.

**Google Workspace scope:** Only Google Drive and Gmail (via {{OPERATOR_EMAIL}}) are approved at launch. Accessibility of other Google tools within the same authenticated session does not constitute approval. Google Docs, Sheets, Slides, Calendar, Meet, Forms, Sites, Chat, and all other Google Workspace products require CEO approval before use.

## Backup

Backup is handled at the operating system level (macOS Time Machine or equivalent) and is not the sandbox organization's direct responsibility. The organization must not create independent backup mechanisms that copy KB content or policy documents to unapproved locations.

The Google Drive mirror of the KB provides a secondary copy for CEO read access. This is not a backup system — it is a CEO access mechanism.

## Policy Repository

The authoritative policy repository is hosted on a private GitHub repository. The sandbox maintains a read-only local clone.

| Parameter | Rule |
| --- | --- |
| Sync cadence | Daily (on session start or scheduled) |
| On-demand sync | Triggered by CEO instruction |
| Local modification detection | Treat as security incident; generate diff; overwrite with GitHub version; log; notify Security Steward and President Agent |
| Edit authority | CEO only (via GitHub) |

The local clone is read-only. No sandbox agent may push to the policy repository.

## Containerization

Docker is approved for local service operation and isolation of untrusted workloads. Quarantined tools must run in containers with no access to sensitive data or the sandbox filesystem outside the container.

Additional containerization tooling requires CEO approval before use.

## Compute and Resource Limits

The sandbox host is a dedicated 2025 Mac mini with an M4 Pro chip (12-core CPU, 16-core GPU), 24 GB unified memory, and 512 GB internal SSD. A 5 TB external USB3 drive is attached for document storage and Time Machine backup. No personal or external workloads share this host.

**Concurrent agent processes:** Maximum 4 concurrent agent processes at any time. Additional process requests must queue or be deferred until a slot is available. The President Agent is always counted against this limit when active.

**Per-agent resource limits:**

| Resource | Limit per agent |
| --- | --- |
| Memory | 4 GB |
| CPU cores | 4 of 12 available |

**Host-level ceiling:** Total agent memory must not exceed 18 GB, leaving approximately 6 GB for macOS and core tools. If the ceiling would be breached by a new process, the requesting agent must wait or escalate to the CEO.

**GPU access:** The M4 Pro GPU is available for approved workloads. GPU-intensive workloads (e.g., local model inference) require CEO approval before execution. No GPU-intensive workloads are approved at launch.

**Local model compute:** Not applicable until local model execution is approved by the CEO.

**Enforcement:** The Security Steward monitors resource usage. Any agent observed exceeding its per-agent limits must be halted by the Security Steward per the incident response policy.

## Prohibited Infrastructure Actions

- Installing software outside the approved stack without CEO approval
- Modifying system network configuration without CEO approval
- Modifying cron jobs or launchd without CEO approval
- Using sudo except as explicitly approved per use case
- Connecting the sandbox host to the CEO's personal accounts
- Disabling or bypassing macOS security features (SIP, Gatekeeper, etc.)
- Creating outbound network tunnels or proxies outside Tailscale without CEO approval
