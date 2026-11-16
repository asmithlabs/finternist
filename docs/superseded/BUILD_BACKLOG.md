> **Superseded — pre-spec draft, kept for reference. Do not build from this file.**
>
> The authoritative document is [`docs/MASTER_SPEC.md`](../MASTER_SPEC.md). Where they disagree, the
> master spec is correct.
>
> This file decomposes the work into **Build 001 / 002 / 003**. The master spec uses **Phases 0–8**
> (§26) with a different ordering: Phase 0 Foundation comes first and delivers architecture, PRD,
> backlog, Rails app, database, CI, test framework, design-system foundation, development environment
> and a security baseline — none of which appears in Build 001 here. Build 001 step 1 ("Bootstrap
> Rails, PostgreSQL, auth and registration") conflates spec Phase 0 with Phase 1.
>
> The replacement roadmap is a Phase 0 deliverable (§30 step 3), to be written at
> `docs/product/roadmap.md`. Until it exists, follow §26 directly.
>
> **Worth carrying forward:** the definition-of-done discipline — a named, testable end state per
> stage rather than a list of tasks.

# Execution order

## Build 001: Functional local vertical slice
1. Bootstrap Rails, PostgreSQL, auth and registration
2. Implement financial HPI creation/editing
3. Implement asset/liability entry
4. Implement income/expense entry
5. Verify dashboard math and missing-data state
6. Generate immutable Rounds reports
7. Test access control, validations and ownership scoping

## Build 002: Make it usable
1. Edit existing records
2. Goal tracking and period-over-period Rounds comparisons
3. CSV import with reconciliation and duplicate detection
4. Data export and deletion flows
5. Mobile UX and accessibility review

## Build 003: Financial institution sandbox
1. Security and privacy review
2. Plaid Sandbox linking with explicit consent
3. Transactions sync and webhook processing
4. Sync failure handling, duplicate detection, audit trail

**Definition of done for Build 001:** A test user can register, enter fictional finances, view totals and produce a saved Rounds report, with automated tests passing locally.
