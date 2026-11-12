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
