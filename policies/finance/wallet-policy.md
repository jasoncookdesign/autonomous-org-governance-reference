# Wallet Policy

**Version:** 1.0  
**Status:** Active  
**Edit authority:** CEO only

## Purpose

Governs the structure, provisioning, spending authority, and audit requirements for all organizational wallets. Wallets are the only approved mechanism for autonomous organizational spending. No agent may spend money outside an approved wallet.

## Wallet Provider

All organizational wallets are provisioned through **privacy.com** as virtual credit cards. Wallets may not exist on day one; the organization must request wallet provisioning from the CEO when a wallet is needed and unavailable.

## Wallet Request Process

When a director identifies a need for a wallet that has not been provisioned:

1. The director notifies the President Agent
2. The President Agent submits a wallet request to the CEO via escalation email including:
   - Purpose of the wallet
   - Requesting director
   - Proposed monthly spending cap
   - Proposed maximum acceptable loss
   - Proposed merchant restrictions (if applicable)
   - Pre-approved vendor list for this wallet
3. The CEO provisions the wallet via privacy.com and communicates the wallet parameters back to the organization
4. The wallet is activated for the requesting director upon CEO confirmation

No spending may occur on a wallet that has not been formally provisioned and confirmed by the CEO.

## Wallet Structure

Wallets are segmented by director. Each active director requiring spending authority has its own wallet. There is no shared organizational wallet.

| Wallet | Assigned Director | Status |
| --- | --- | --- |
| Engineering Wallet | Engineering Director | To be provisioned |
| Operations Wallet | Operations Director | To be provisioned |
| Knowledge Wallet | Knowledge Director | To be provisioned (if needed) |
| Security Wallet | Security Steward | To be provisioned (if needed) |

Inactive directors (Research, Financial Analysis, Creative, Venture) do not have wallets until activated.

## Pre-Approved Vendors

The following vendors are pre-approved for all active director wallets, subject to each wallet's spending limits:

- Anthropic API
- GitHub
- Google Workspace

All other vendors require CEO approval before any spend, regardless of amount. A director wishing to spend with a non-approved vendor must submit an escalation request before making any purchase.

## Spending Rules

1. Spending must occur only through an approved wallet assigned to the spending director.
2. Spending must be with an approved vendor or an explicitly approved non-standard vendor.
3. Each transaction must be logged with: date, amount, vendor, purpose, and capability reference.
4. Monthly spending must not exceed the wallet's monthly cap.
5. Any single unexpected charge must be treated as a potential incident and reported.
6. Wallets may not be shared between directors.
7. The CEO's personal credit cards, bank accounts, or financial accounts may not be used for organizational spending under any circumstances.

## Spending Limits

Individual wallet spending limits are set by the CEO at provisioning time and documented in the wallet's provisioning record.

| Parameter | Value |
| --- | --- |
| Transaction limit | [UNDECIDED — set per wallet at provisioning] |
| Monthly cap | [UNDECIDED — set per wallet at provisioning] |
| Maximum acceptable loss | [UNDECIDED — set per wallet at provisioning] |

## Prohibited Financial Actions

Regardless of wallet status or spending limits, the following are prohibited:

- Accessing the CEO's personal bank accounts, brokerage accounts, or credit cards
- Trading securities of any kind
- Using margin, leverage, options, or futures
- Opening brokerage or financial accounts
- Signing contracts or incurring debt
- Managing the CEO's personal investments
- Committing the organization to financial obligations without CEO approval

## Audit Requirements

Each director with spending authority must log and report:

- All transactions (date, amount, vendor, purpose, wallet reference)
- All declined transactions or failed payment attempts
- Any charge that appears unexpected or does not match an approved purpose
- Monthly spend totals included in the monthly capability audit report

The Security Steward reviews wallet transaction logs as part of its routine compliance audit.

## Wallet Suspension

A wallet may be suspended by the Security Steward pending CEO review if:

- An unexpected charge is detected
- A suspected prompt injection targeting financial systems is detected
- The wallet has been used with an unapproved vendor

Wallet suspension is a containment action. The CEO must be notified immediately. The wallet is reinstated only by CEO decision.
