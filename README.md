# autonomous-org-governance-reference

A real-world instantiation of the [`autonomous-org-governance`](https://github.com/{{CANONICAL_GITHUB_USER}}/autonomous-org-governance) template — a policy-governed, AI-operated organizational system built on top of Claude.

This reference implementation is published for transparency and community reference. All private values (hostnames, email addresses, user identifiers, file paths, SSH key names) have been replaced with `{{PLACEHOLDER}}` tokens defined in the template.

---

## What This Is

This repository contains the live governance artifacts of a working autonomous organization:

- **Role definitions** for the AI President Agent and all AI Director roles
- **Capability policies** governing what each AI agent is authorized to do
- **Operational policies** (security, infrastructure, data classification, financial controls)
- **Portfolio tracking** — the initiative backlog, active work, and completion records
- **Audit trails** — capability reviews, incident reports, and monthly reports
- **Architecture documentation** — how the system is structured and how components relate
- **Proposals** — formal change proposals with CEO approval records

---

## Structure

```
autonomous-org-governance-reference/
├── README.md                    # This file
├── architecture/                # System architecture documentation
├── audit/                       # Audit trails, reviews, incident reports
├── capabilities/                # AI agent capability policies
├── operations/                  # Operational runbooks and procedures
├── policies/                    # Organizational policies
├── portfolio/                   # Initiative portfolio and backlog
├── proposals/                   # Formal change proposals
└── roles/                       # Role definitions for AI agents
```

---

## How to Use This Reference

This repository is a companion to [`autonomous-org-governance`](https://github.com/{{CANONICAL_GITHUB_USER}}/autonomous-org-governance). Use it to:

1. **See how the template is applied** — each governance artifact here corresponds to a template in the source repo
2. **Understand operational patterns** — how initiatives are tracked, how proposals work, how audit trails are maintained
3. **Bootstrap your own instance** — fork `autonomous-org-governance`, then use this reference to understand how to populate it

---

## Placeholder Tokens

All private values have been substituted with descriptive tokens:

| Token | Description |
|---|---|
| `{{CANONICAL_GITHUB_USER}}` | GitHub username for the primary/canonical repo |
| `{{FORK_GITHUB_USER}}` | GitHub username for the fork/relay repo |
| `{{SYSTEM_USER}}` | Local system username on the Mac mini |
| `{{MAC_MINI_HOSTNAME}}` | Hostname of the Mac mini compute node |
| `{{CEO_EMAIL}}` | CEO's email address |
| `{{OPERATOR_EMAIL}}` | Operator email used by automated systems |
| `{{SSH_KEY_NAME}}` | Name of the SSH deploy key |
| `{{REPO_NAME}}` | Name of the governance repository |
| `{{SANDBOX_DATA_ROOT}}` | Root path of the sandbox data volume |
| `{{DRIVE_AIRLOCK_FOLDER_ID}}` | Google Drive folder ID for the Airlock |
| `{{DRIVE_BACKUP_ROOT_FOLDER_ID}}` | Google Drive folder ID for backup root |
| `{{DRIVE_PROJECTS_FOLDER_ID}}` | Google Drive folder ID for projects |
| `{{DRIVE_SCHEDULED_FOLDER_ID}}` | Google Drive folder ID for scheduled tasks |

A complete example configuration file is provided at [`.jasonos-config.example`](.jasonos-config.example).

---

## License

Published for reference and community use. See the parent template repository for license terms.
