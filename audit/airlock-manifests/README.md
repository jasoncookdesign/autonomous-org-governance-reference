# Airlock Manifests

Transfer manifests produced by the Operations Director for every non-trivial airlock transfer. Each manifest documents the source, classification, sanitization, destination, and retention expectation for an inbound artifact.

## Filing Convention

File manifests as: `YYYY-MM-DD-NNN.md` (sequential within the day)

Example: `2026-06-15-001.md`

## Template

Use `templates/airlock-manifest-template.md` for all manifests.

## Retention

Manifests are retained indefinitely. They are the audit record of what entered the sandbox and why.
