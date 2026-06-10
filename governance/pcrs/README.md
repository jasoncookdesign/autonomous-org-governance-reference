# Policy Change Requests (PCRs)

This directory organizes PCRs by lifecycle status.

## Structure

- `open/` — PCRs pending CEO review and approval
- `addressed/` — PCRs that have been approved, published, or rejected

## Lifecycle

1. President Agent drafts a PCR and places it in `open/`
2. CEO reviews in a governance session
3. On decision (approve/reject/defer), PCR moves to `addressed/` and the status field is updated in the document

## Conventions

- Filenames follow the pattern: `PCR-[CATEGORY]-[NNN]-[slug].md`
- Status values: `Draft`, `Pending CEO Review`, `Approved`, `Published`, `Rejected`, `Deferred`

## Index

See `governance/policy-gap-register.md` for the canonical registry of open policy gaps that may generate PCRs.
