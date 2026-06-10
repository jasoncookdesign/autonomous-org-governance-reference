# Audit

All organizational audit records and logs. This folder contains filed records only — policy documents governing audit behavior live in `policies/audit/`.

Records here are append-only in practice and retained indefinitely unless a specific retention rule applies.

## Contents

| File / Folder | Filed By | Description |
| --- | --- | --- |
| `airlock-manifests/` | Operations Director | Transfer manifests for all non-trivial airlock transfers |
| `capability-reviews/` | Security Steward | Periodic capability audit reports |
| `incident-reports/` | Security Steward | Incident reports by severity |
| `monthly-reports/` | President Agent | Weekly governance summaries and monthly reports |
| `venture-reviews/` | Security Steward | Compliance audit records for ventures at Stage 5 review |
| `president-lifecycle-log.md` | President Agent | Log of all temporary agent authorizations and non-director agent retirements |
| `security-steward-audit-log.md` | Security Steward | Security Steward's own action audit trail |

## Governing Policies

- **Reporting:** `policies/audit/reporting-policy.md`
- **Incident response:** `policies/audit/incident-response-policy.md`
