# Portfolio — Included Initiatives (Reference Subset)

**Note:** This is a curated subset of the full initiative portfolio. It includes the initiatives that best demonstrate how the system governs its own infrastructure and development. Client work, operational backlog, and non-illustrative initiatives are excluded from this reference repo.

See the top-level README for context on what is included and why.

---

## Included Initiatives

| ID | Initiative | Status | Category |
|---|---|---|---|
| [INI-001](INI-001.md) | Git Commit Relay & Fork Model Infrastructure | Done | Infrastructure |
| [INI-002](INI-002.md) | Governance Repo v1.0 Upgrade | Done | Governance |
| [INI-003](INI-003.md) | Emergency Shutdown Procedure | Done | Operations |
| [INI-004](INI-004.md) | Sandbox Rebuild Procedure | Done | Operations |
| [INI-005](INI-005.md) | Policy Gap Register | Done | Governance |
| [INI-007](INI-007.md) | Capability Review Program | Done | Governance |
| [INI-010](INI-010.md) | Agent Invocation Bridge | Done | Infrastructure |
| [INI-021](INI-021.md) | Portfolio ID Reservation and Canonical-First Assignment Policy | Done | Governance |
| [INI-022](INI-022.md) | Deliberate Commit Gate Policy | Done | Governance |
| [INI-027](INI-027.md) | Operationalize 'Edge of Authority Ceiling' Advisory Trigger | Done | Governance |
| [INI-028](INI-028.md) | Canonical Main Fetch Bridge | Done | Infrastructure |
| [INI-030](INI-030.md) | Direct-to-Main Git Relay via SSH | Done | Infrastructure |
| [INI-031](INI-031.md) | autonomous-org-governance-reference Public Repository | Done | Governance |

---

## Why These Initiatives?

These initiatives were selected because they collectively demonstrate:

- **Bootstrap sequence** (INI-001, INI-002): How the organization stood up its own infrastructure from scratch using the template
- **Core governance mechanics** (INI-005, INI-007, INI-021, INI-022, INI-027): How policy gaps are tracked, capabilities are audited, IDs are assigned, and authority ceilings work in practice
- **Operational foundations** (INI-003, INI-004): How the organization handles emergencies and rebuilds from scratch
- **Infrastructure maturation** (INI-010, INI-028, INI-030): How the agent invocation bridge, canonical fetch, and SSH relay evolved
- **Boundary marker** (INI-031): The initiative that marks the transition from infrastructure build-out to active organizational use

Client work initiatives, backlog initiatives not yet started, and operational initiatives specific to this organization's particular workloads are excluded.

---

## Adding Your Own Initiatives

When you instantiate this template, replace this portfolio with your own. The initiative file format is defined in `templates/` and the ID assignment policy is in `portfolio/INI-021.md`.
