# Infrastructure Policy

Policy governing the sandbox host, network identity, account separation, approved software environment, remote access, and compute limits.

Infrastructure is an implementation detail — governance is the root of trust — but infrastructure decisions affect the security of every capability.

## Contents

| File | Description |
| --- | --- |
| `infrastructure-policy.md` | Sandbox host specs, network configuration, account separation rules, approved software environment, backup rules, and prohibited infrastructure actions |

## Current Sandbox Host (Summary)

- **Hostname:** `{{MAC_MINI_HOSTNAME}}` / **IP:** `{{MAC_MINI_LOCAL_IP}}`
- **Remote access:** Tailscale, macOS SSH
- **Compute limits:** [UNDECIDED — pending CEO input]
