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
