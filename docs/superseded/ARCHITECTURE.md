> **Superseded — pre-spec draft, kept for reference. Do not build from this file.**
>
> The authoritative document is [`docs/MASTER_SPEC.md`](../MASTER_SPEC.md). Where they disagree, the
> master spec is correct. Specific conflicts in this file:
>
> | This file says | Master spec says |
> | --- | --- |
> | Minitest | **RSpec** (§2, §23) |
> | Built-in Rails authentication | Authentication **plus MFA** — TOTP, recovery codes, step-up, designed for WebAuthn later (§4, §26 Phase 1) |
> | "Do not introduce an unvalidated financial health score" | **Develop a transparent, versioned scoring framework** — it simply must not be presented as scientifically validated (§8 Assessment) |
> | `Account`, `Transaction` | `FinancialAccount`, `FinancialTransaction`, within the bounded contexts of §20 |
> | A five-entity domain | Nine bounded contexts (§20), of which this is a small subset |
>
> **Worth carrying forward into the Phase 0 documents:** the net-worth and month-to-date cash-flow
> formulas; the rule that an empty dataset shows a missing-data warning rather than a falsely
> favorable assessment (master spec §6 and §8 agree); ownership scoping through `Current.user`;
> balances held as integer cents — a legitimate reading of §6's "appropriate decimal types for
> money, not binary floating point", but confirm it against §20 before relying on it.

# Finternist MVP architecture

## Stack
- Ruby on Rails 8.1.4.1
- PostgreSQL
- Hotwire, Stimulus, Tailwind
- Built-in Rails authentication
- Minitest
- Rails monolith with business logic in service objects

## Core domain
- `User`: authenticated identity and owner
- `FinancialProfile`: financial HPI (one per user)
- `Account`: asset or liability and nonnegative balance in cents
- `Transaction`: income or expense, positive amount in cents, category, date
- `RoundsReport`: date, saved financial snapshot, assessment, plan

## Math and rules
Net worth = sum(assets) - sum(liabilities).
Month-to-date cash flow = month-to-date income minus expenses.
An empty dataset must display a missing-data warning, not a falsely favorable cash-flow assessment.
Do not introduce an unvalidated financial health score.

## Security design constraints
Authorize reads and mutations through `Current.user` ownership scopes. Do not commit secrets. Prototype with fictional data only. Production requires reviewed security controls, encryption, audit logging, backup recovery, monitoring, and data handling policies.
