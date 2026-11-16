> **Superseded — pre-spec draft, kept for reference. Do not build from this file.**
>
> The authoritative document is [`docs/MASTER_SPEC.md`](../MASTER_SPEC.md). Where they disagree, the
> master spec is correct. Specific conflicts in this file:
>
> | This file says | Master spec says |
> | --- | --- |
> | First slice: sign-in + HPI + balance sheet + transactions + Rounds | First vertical slice is **registration, email verification, login and MFA** alone (§30 step 7, §26 Phase 1) |
> | Plaid and AI are non-goals | Both are **planned phases** — Plaid is Phase 3, AI-assisted Rounds is Phase 5 (§26). They are out of scope *now*, not out of scope. |
> | "Build 001" | Phases **0–8** (§26); there is no Build 001 in the spec |
> | No payments | Subscriptions and entitlements are designed early, billing enabled later (§13) |
>
> **Worth carrying forward:** the product promise in one sentence, and the standing rule that only
> fictional data is used until security, privacy and operational reviews are complete (master spec §24).

# Finternist MVP v0.1

## Product promise
A physician can complete a financial HPI, record a balance sheet and transactions, review financial position, and generate an explainable SOAP-inspired financial Rounds report.

## First vertical slice
- [ ] Register and sign in
- [ ] Create and edit Financial HPI: specialty, career stage, gross income estimate, concern, goal
- [ ] Record assets and liabilities with balances in integer cents
- [ ] Record income and expense transactions with positive amounts and explicit types
- [ ] Show current net worth and month-to-date cash flow
- [ ] Generate and persist a Rounds report with assessment and action items
- [ ] Protect every financial record by ownership
- [ ] Test calculations and missing-data states

## Non-goals
No real banking connections, Plaid, AI financial advice, trade execution, tax filing, investment clubs, advisor marketplace, or payments in Build 001.

**Development boundary:** Use fictional data only until security, privacy, and operational reviews are complete.
