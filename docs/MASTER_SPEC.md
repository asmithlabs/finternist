# FINTERNIST: MASTER PRODUCT AND ENGINEERING SPECIFICATION

## Your role

You are the principal software architect, senior Ruby on Rails engineer, product engineer, security engineer, and technical project lead responsible for building **Finternist**.

Finternist is a physician-focused financial health platform designed to help physicians understand, organize, improve, and actively manage their financial well-being.

This is a real application intended for commercial deployment, not a proof-of-concept dashboard.

You are expected to maintain a persistent understanding of the entire product vision, implement features incrementally, write production-quality software, and preserve architectural compatibility with future features.

Do not immediately attempt to implement every feature.

First establish the architecture, specifications, development infrastructure, and phased implementation plan. Then begin implementation in vertical slices with tests.

Do not repeatedly ask the founder to restate features already documented here.

If an architectural detail is unspecified, make a reasonable recommendation, document the assumption, and proceed if the decision is reversible. Escalate decisions involving regulatory risk, substantial costs, irreversible data model choices, or material changes to product scope.

---

# 1. PRODUCT IDENTITY

**Product:** Finternist

**Audience:** Physicians, initially U.S.-based physicians, including employed physicians, residents/fellows, independent contractors, locum tenens physicians, and practice owners.

**Mission:** Help physicians care for their financial health.

**Core philosophy:** Financial well-being should be assessed, monitored, and managed longitudinally, much like clinical health.

Finternist is not merely a budgeting dashboard.

It is a financial operating system with five core functions:

1. Collect and reconcile financial information.
2. Analyze the user's financial condition.
3. Generate an interpretable financial assessment.
4. Develop a personalized, actionable follow-up plan.
5. Measure improvement over time.

The clinical analogy should inform the workflow and structure without unnecessarily medicalizing financial discussions.

The application must be welcoming to financial beginners while offering sophisticated tools for knowledgeable users.

## Product principles

- User ownership and control of financial information.
- Explainable calculations and attributable data.
- Minimal unnecessary financial data collection.
- Privacy and security by design.
- Financial education rather than opaque instructions.
- Actionable recommendations rather than passive dashboards.
- Longitudinal improvement rather than isolated snapshots.
- Human control over consequential financial decisions.
- Progressive disclosure of advanced features.
- Modular, maintainable engineering.

---

# 2. TECHNOLOGY STACK

## Preferred architecture

Build the application as a modular Ruby on Rails monolith, with clearly separated business domains.

Default stack:

- Ruby and current stable compatible Rails version.
- PostgreSQL as the primary database.
- Hotwire: Turbo and Stimulus.
- Tailwind CSS, with reusable design components.
- Active Record.
- Active Job with a production-ready background job backend.
- Action Mailer with Mailgun.
- Active Storage for uploaded documents.
- RSpec for automated testing.
- Redis only if justified by infrastructure choices.
- Docker-based development and deployment.
- GitHub for version control.
- GitHub Actions for CI.
- Production hosting architecture that does not depend on the developer's personal homelab being continuously available.

Prefer Rails-native capabilities before introducing extra services or frontend frameworks.

Do not introduce React or a dedicated SPA without a documented reason.

Use service objects, domain modules, background jobs, and clearly defined integration adapters.

The system should support replacing external providers without rewriting core business logic.

## Engineering preferences

- Test-driven development where practical.
- Small, reviewable commits.
- Explicit schema migrations.
- No unnecessary abstractions.
- Secure defaults.
- Thorough error handling.
- Accessible responsive interface.
- Clear API integration boundaries.
- Structured logs and observability.
- Feature flags for unfinished capabilities.
- Documentation maintained alongside code.

---

# 3. USER TYPES AND ACCESS CONTROL

Initial user roles:

**Standard User**
- Manages their own financial information.
- Links financial accounts.
- Creates goals and plans.
- Generates Rounds.
- Manages notifications and subscriptions.

**Premium User**
- Receives advanced reporting and enhanced analytical capabilities.
- Can access premium features according to subscription entitlements.

**Administrator**
- Manages application operations, subscription states, support workflows, and feature availability.
- Does not automatically gain access to the underlying financial information of individual users.

**Future Financial Professional**
- May provide consultations under a controlled, consent-based workflow.
- Access must be explicitly authorized by the user.

Plan for future organizational accounts and shared permissions, but do not build them into the initial MVP unless necessary.

All financial data must be scoped by account ownership and authorization.

---

# 4. AUTHENTICATION, MFA, AND ACCOUNT SECURITY

Implement complete authentication and account lifecycle management.

## Account creation

- Email and password registration.
- Email verification.
- Secure login.
- Password recovery.
- Session management.
- Account deactivation and deletion workflows.
- Optional physician professional identity verification in a future phase.

Initial physician status may be self-declared; do not imply that it has been independently verified.

## MFA

Implement:

- TOTP authenticator support.
- Recovery codes.
- MFA enrollment and removal.
- Secure recovery procedures.
- Step-up authentication for sensitive account actions.

Design authentication to accommodate WebAuthn/passkeys and hardware security keys, including YubiKey, in a later phase.

Use established, well-maintained libraries where appropriate.

Do not build custom cryptographic algorithms.

## Session and account security

- Secure, HttpOnly, SameSite cookies.
- CSRF protection.
- Rate limiting.
- Account enumeration resistance.
- Suspicious authentication event logging.
- Session revocation.
- Reauthentication for sensitive operations.
- Encrypted credentials and provider tokens.
- Strict authorization boundaries.

## Mailgun

Mailgun will serve as the initial transactional email provider.

Implement Action Mailer integration for:

- Email verification.
- Password reset.
- MFA-related security notifications.
- Login alerts where appropriate.
- Account linking alerts.
- Rounds report availability.
- Reminder notifications.
- Scheduled financial summaries.

Implement branded, responsive HTML and plain-text templates.

Mail sending must be asynchronous, retryable, monitored, and idempotent where appropriate.

Configure SPF, DKIM, DMARC, and provider webhooks for delivery failures and suppression events.

No provider secrets or sensitive financial details should appear in logs or email subjects.

---

# 5. FINANCIAL ONBOARDING: THE FINANCIAL HPI

The onboarding experience should resemble a structured financial history of present illness.

This is a defining part of Finternist.

The objective is to understand the user's present financial situation, major concerns, current obligations, goals, and constraints.

## Onboarding workflow

### A. Personal and professional profile

Collect only information needed for meaningful analysis:

- Career stage.
- Physician employment classification.
- Residency/fellowship status.
- Employee versus independent contractor.
- Practice ownership.
- Relevant household financial context.
- General location for tax considerations, subject to consent.

### B. Financial history

- Primary financial concerns.
- Current level of financial confidence.
- Existing debt obligations.
- Emergency savings.
- Retirement planning.
- Major upcoming expenses.
- Insurance considerations.
- Tax planning concerns.
- Estate planning status.
- Burnout or career-transition-related financial concerns, optionally.

### C. Financial goals

Examples:

- Emergency fund.
- Debt reduction.
- Retirement contributions.
- Home purchase.
- Education savings.
- Practice ownership.
- Family wealth planning.
- Financial independence.
- Estate planning.

Goals must support targets, deadlines, priorities, measurable progress, and associated action plans.

### D. Account connectivity

Invite users to connect eligible financial accounts through Plaid.

Account connection should be optional. Provide manual entry for unsupported accounts and users unwilling to connect.

### E. Initial assessment

Generate a preliminary financial overview.

Identify missing data before creating a comprehensive Rounds report.

Users must be able to revise onboarding responses later.

---

# 6. PLAID INTEGRATION

Plaid is the initial provider for permissioned financial account aggregation.

Develop the integration through a dedicated adapter rather than embedding Plaid-specific logic throughout business models.

## Required functionality

- Plaid Link integration.
- Secure link-token creation.
- Public-token exchange.
- Encrypted access-token storage.
- Connected institution management.
- Account discovery.
- Account balances.
- Transaction synchronization.
- Transaction categorization.
- Transaction updates.
- Webhook processing.
- Error recovery.
- Account reconnection.
- Account disconnection and data deletion workflows.

Use the current supported Plaid API.

Use transaction synchronization with persistent cursors and appropriate reconciliation logic.

Handle added, modified, and removed transactions.

Record provider synchronization status and timestamps.

Never present old data as if it were freshly synchronized.

## Financial account categories

Support:

- Checking.
- Savings.
- Credit cards.
- Loans.
- Mortgages.
- Student loans.
- Investment accounts when supported by the enabled Plaid products.
- Retirement accounts when supported.

Understand that individual Plaid products carry distinct access requirements and costs.

Do not automatically enable all Plaid products.

## Financial normalization

Create internal representations for:

- Financial institutions.
- Financial connections.
- Accounts.
- Balances.
- Transactions.
- Categories.
- Liabilities.
- Holdings.
- Recurring cash flows.

Retain source metadata for traceability.

Use appropriate decimal types for money, not binary floating point.

Account for pending transactions, corrections, duplicate events, transfers, currencies, and time zones.

## Data quality

Every financial metric should be traceable to source data or identified as manually supplied.

Capture:

- Source.
- Effective date.
- Sync timestamp.
- Data completeness.
- Calculation version.
- Missing values.
- User adjustments.

Do not infer a value of zero when information is absent.

---

# 7. FINANCIAL DASHBOARD

The dashboard is the user's financial command center.

## Core sections

### Overview

- Net worth.
- Assets.
- Liabilities.
- Monthly income.
- Monthly spending.
- Savings and cash flow.
- Debt burden.
- Goal progress.
- Upcoming financial obligations.

### Accounts

- Connected institutions.
- Individual accounts.
- Account balances.
- Account activity.
- Synchronization status.
- Reconnection controls.

### Spending and budgeting

- Spending by category.
- Income by category.
- Recurring payments.
- Cash-flow trends.
- Budget versus actual.
- Unusual spending changes.

### Investments

Initially, read-only portfolio aggregation and educational analytics.

Include holdings, allocation, account value, and contribution trends when the necessary underlying data is available.

Do not confuse portfolio visibility with authority to execute trades.

### Goals

- Active goals.
- Financial milestones.
- Target values.
- Progress.
- Planned contributions.
- Associated follow-up actions.

### Rounds

- Most recent report.
- Historical reports.
- Progress since previous assessment.
- Outstanding recommendations.
- Next scheduled assessment.

Build a responsive interface that works well on desktop and mobile.

---

# 8. ROUNDS: CORE INTELLIGENCE ENGINE

Rounds is the flagship Finternist feature.

It is a structured financial progress report modeled conceptually on a clinical progress note.

The report should combine deterministic financial calculations with AI-assisted interpretation and user-defined context.

## Report structure

### 1. Subjective

Capture the user's self-assessment:

- How financially secure do you feel?
- What concerns have changed?
- What goals are most important?
- What financial events have occurred?
- What barriers have affected progress?
- What changed since your previous report?

Allow free-text entries and structured responses.

### 2. Objective

Generate evidence-based financial metrics.

Possible metrics:

- Assets and liabilities.
- Net worth.
- Income.
- Spending.
- Cash flow.
- Savings rate.
- Emergency fund coverage.
- Debt-to-income ratio.
- Debt repayment progress.
- Retirement savings.
- Investment allocation.
- Goal attainment.
- Tax-related obligations.
- Estate planning completion indicators.

Every metric requires a defined formula and documented data prerequisites.

Do not invent numeric values for missing data.

### 3. Assessment

Provide:

- A concise overall financial condition summary.
- Absolute financial status indicators.
- Longitudinal performance comparisons.
- Goal-specific progress indicators.
- Identified strengths.
- Financial vulnerabilities.
- Important data gaps.
- Potential opportunities for improvement.

Develop a transparent financial scoring framework.

The scoring framework must be versioned, interpretable, and not presented as scientifically validated unless independently validated.

An overall score may be useful, but it must not obscure important domain-specific findings.

### 4. Plan

Create actionable recommendations, categorized by:

- Urgency.
- Financial domain.
- Expected benefit.
- Required user effort.
- Dependencies.
- Supporting data.
- Uncertainty.
- User preferences.

Examples include:

- Review unusually high discretionary spending.
- Evaluate a student loan repayment strategy.
- Establish a targeted emergency reserve.
- Review retirement contribution progress.
- Schedule an estate planning consultation.
- Prepare for quarterly estimated taxes.

Recommendations should explain the reasoning, not merely issue commands.

### 5. Follow-up

Each report should identify:

- Tasks to complete.
- Milestones to reach.
- Metrics to reassess.
- Expected follow-up date.
- Outstanding data requirements.
- Previous-plan completion status.

The next Rounds report must evaluate progress against the previous plan.

---

# 9. ROUNDS AI ARCHITECTURE

AI must not become the source of truth for financial calculations.

Separate the system into three layers.

## Layer A: Deterministic analytics

Responsible for:

- Data aggregation.
- Financial formulas.
- Historical comparisons.
- Threshold evaluation.
- Missing-data detection.
- Goal progress.
- Financial score calculations.

Calculations must be independently testable without an LLM.

## Layer B: AI-assisted interpretation

Responsible for:

- Summarizing financial changes.
- Explaining calculations in accessible language.
- Highlighting relevant financial patterns.
- Drafting recommendations.
- Identifying follow-up questions.
- Personalizing report narratives.

Use an AI-provider abstraction so providers and models can be replaced.

Implement typed, schema-validated output.

AI should receive a minimized, explicitly constructed financial context rather than raw unfiltered financial records.

Do not send bank credentials, Plaid access tokens, or unnecessary personally identifying information to AI services.

## Layer C: Review and action

The application must:

- Show the report to the user.
- Distinguish calculated facts from AI-generated interpretations.
- Allow edits to user-provided context.
- Allow recommendations to be accepted, deferred, or dismissed.
- Convert accepted recommendations into tasks.
- Preserve the underlying report.
- Record model and calculation versions.

## Required AI protections

- No fabricated financial metrics.
- No fabricated source citations.
- No silent changes to financial records.
- No autonomous transfers or trading.
- No automatic execution of high-impact decisions.
- Explicit uncertainty when data is incomplete.
- Validation of structured model output.
- Protection against prompt injection in transaction descriptions and uploaded documents.
- Clearly defined provider retention and privacy settings.
- User consent for external AI processing.

## Report versioning

Store:

- Report generation timestamp.
- Financial data snapshot.
- Calculation framework version.
- Model identifier.
- Prompt template version.
- Structured findings.
- User-reviewed decisions.
- Accepted recommendations.
- Related tasks.
- Follow-up status.

Historical reports must remain reproducible or clearly explain their provenance.

---

# 10. FOLLOW-UP PLAN AND TASK MANAGEMENT

The follow-up system is not an optional checklist.

It is how Finternist converts assessment into action.

## Task lifecycle

Suggested → Accepted → Planned → In progress → Completed

Also support deferred, dismissed, and canceled states.

Every task should support:

- Title.
- Description.
- Source Rounds report.
- Financial category.
- Priority.
- Due date.
- Completion criteria.
- Associated goal.
- Current status.
- Optional reminder.
- User notes.

AI can suggest tasks, but users control acceptance and activation.

## Longitudinal workflow

When the next Rounds report is generated:

1. Load the previous follow-up plan.
2. Identify completed tasks.
3. Compare target and actual metrics.
4. Identify stalled actions.
5. Determine whether previously identified concerns have improved.
6. Recommend a revised plan.
7. Retain an auditable history.

Avoid generating duplicate tasks across reports.

---

# 11. NOTIFICATIONS AND MORNING REPORT

Implement a centralized notification framework.

## Initial notification channels

- In-app notifications.
- Transactional email through Mailgun.

Design provider interfaces for future push notifications, SMS, and additional messaging integrations.

## Notification categories

- Security.
- Financial account synchronization problems.
- Rounds report availability.
- Follow-up task reminders.
- Goal milestones.
- Important financial changes.
- Tax deadlines.
- Subscription and billing events.

## Notification controls

Users must control:

- Channel preferences.
- Frequency.
- Enabled notification categories.
- Quiet hours.
- Digest delivery.
- Unsubscribe preferences for nonessential messages.

Security-critical notifications require separate handling.

## Morning Report

The Morning Report is a future scheduled financial digest.

Potential contents:

- Important changes in financial position.
- Upcoming bills.
- Outstanding follow-up actions.
- Progress toward goals.
- Tax reminders.
- Recent financial milestones.

Provide daily, weekly, or disabled delivery options.

Sensitive financial details should not be embedded into insecure notification channels unnecessarily.

All notifications should link back to the authenticated application.

---

# 12. TAX REMINDER SYSTEM

Tax reminders are part of the MVP.

The system must support employee physicians, independent contractors, locums physicians, and practice owners.

## Required functionality

- Tax profile.
- Filing-status-related preferences where needed.
- Employment classification.
- Relevant federal and state jurisdiction.
- Important filing dates.
- Estimated tax reminders.
- Tax document preparation checklist.
- User-created tax tasks.
- Notification scheduling.

## Deadline accuracy

Do not hardcode permanent assumptions about IRS or state deadlines.

Use a versioned deadline registry with authoritative sources, effective tax years, jurisdictions, and documented exceptions.

Account for weekends, holidays, extensions, and announced deadline relief where supported.

If a deadline cannot be reliably verified, do not silently generate a definitive date.

Provide educational information and reminders, not unqualified individualized tax advice.

## Future tax capabilities

- Tax document organizer.
- Tax preparation wizard.
- Deduction review.
- Quarterly tax folders.
- Accounting integrations.
- Export packages for CPAs.
- Tax calculation scenarios.
- Automated tax workflows where legally and technically supportable.

---

# 13. SUBSCRIPTIONS AND MONETIZATION

Design subscription entitlements early, even if billing is enabled later.

Initial product tiers:

**Core**
- Financial account aggregation.
- Basic dashboard.
- Financial goals.
- Basic Rounds reporting.
- Follow-up task management.
- Essential reminders.

**Plus**
- Expanded reporting.
- Additional Rounds analysis.
- Advanced comparisons.
- Enhanced financial planning tools.
- Expanded notification and digest options.

**Premium**
- More advanced financial analytics.
- Business financial workflows as released.
- Advanced planning and simulations.
- Other premium capabilities after product validation.

These tier assignments are provisional and must be configurable.

Use a centralized entitlement service.

Evaluate a provider such as Stripe Billing for subscriptions, but keep subscription management separate from financial account aggregation.

Do not design investment activity around the ordinary SaaS billing processor.

---

# 14. PHASE TWO: ADVANCED FINANCIAL ANALYTICS

After the core MVP is stable, add:

- Advanced budgets.
- Cash flow forecasting.
- Retirement contribution analysis.
- Debt payoff scenarios.
- Physician compensation modeling.
- Career-transition financial planning.
- Burnout cost buffer calculator.
- Break-even recovery planner.
- Savings projections.
- Education and custodial account tracking.
- Insurance planning indicators.
- Estate planning status tracking.
- Enhanced portfolio analytics.

Provide transparent assumptions for every projection.

Users should be able to inspect and modify assumptions.

Simulations must distinguish historical facts from projected outcomes.

---

# 15. PHASE THREE: PHYSICIAN BUSINESS FINANCES

Create an additional business finance domain.

Target users:

- Independent contractors.
- Locum tenens physicians.
- LLC owners.
- Practice owners.
- Physicians with side businesses.

## Business entities

Allow users to establish one or more business profiles separate from their household finances.

Support tracking of personal versus business transactions.

## Receipt management

- Receipt image and PDF upload.
- Object storage.
- OCR and structured data extraction.
- Merchant identification.
- Purchase date.
- Amount.
- Tax.
- Currency.
- Expense category.
- Business attribution.
- Linked bank transaction.
- User review and correction.
- Exportable records.

Receipt extraction must preserve the original document and mark uncertain values for review.

## Mileage tracking

- Business mileage entries.
- Date.
- Origin and destination.
- Business purpose.
- Distance.
- Applicable tax-year mileage rate.
- Supporting notes.
- Exportable records.

Automatic route or location tracking can be considered later with appropriate user consent.

## Tax and accounting

- Business expense categorization.
- Potential deduction identification.
- Quarterly tax preparation folders.
- Revenue and expenses.
- Export to accounting software.
- CPA-ready summaries.
- Missed deduction review.

Never automatically assert that an expense is deductible merely because a model identifies a possible category.

Build a human-verifiable workflow.

---

# 16. PHASE FOUR: IN-APP INVESTING

Finternist should eventually support initiating investment activity from within the application.

**Provider decision pending:** The founder referenced "Lama" as a potential investing integration. Do not assume the intended provider or substitute a brokerage vendor without confirmation.

Explore an appropriately licensed embedded brokerage provider, such as Alpaca Broker API, as one possible candidate.

## Conceptual features

- Brokerage account onboarding.
- Identity and KYC workflows.
- Investment account connections.
- Funding account management.
- Market data.
- Securities research.
- Order preview.
- Trade submission.
- Order status.
- Portfolio holdings.
- Trade history.
- Confirmation records.

## Safety and regulatory requirements

This phase is gated on regulatory, legal, provider, and operational review.

Analyze:

- Broker-dealer relationships.
- Investment adviser registration and exclusions.
- KYC/AML responsibilities.
- Custody arrangements.
- Order execution responsibilities.
- Financial disclosures.
- Suitability or best-interest obligations where applicable.
- Jurisdiction restrictions.
- Record retention.
- Market data licensing.
- Customer support and dispute handling.

A user must explicitly authorize trades.

An LLM must not independently execute orders, move money, or initiate investment transactions.

Implement paper trading and sandbox testing before any live order workflow.

Brokerage features must be isolated from the MVP's financial aggregation and reporting logic.

---

# 17. PHASE FIVE: GROUP INVESTING

This is a future Finternist differentiator.

The objective is to allow eligible users to organize collaborative investment activity and maintain transparent shared records.

Potential features:

- Investment groups.
- Membership invitations.
- Member permissions.
- Contribution tracking.
- Ownership allocation.
- Group financial statements.
- Capital accounts.
- Group decisions.
- Voting.
- Shared investment research.
- Investment activity ledger.
- Distribution accounting.
- Member reports.
- Audit history.

Distinguish clearly between collaborative research groups and legally constituted pooled investment entities.

Do not enable shared custody, pooled funds, investment clubs executing trades, or investment management until the legal and regulatory model is defined.

Build the architecture to accommodate this domain without implementing it during MVP development.

---

# 18. FUTURE CONSULT FEATURE

Allow users to request consultations with appropriately qualified professionals.

Potential professional categories:

- Financial planners.
- Tax professionals.
- Accountants.
- Attorneys.
- Estate planning professionals.

Possible functions:

- Professional directory.
- Credential verification.
- Appointment requests.
- Secure document sharing.
- Consent-based access.
- Consultation notes.
- Recommendations and follow-up tasks.

This feature requires its own permission model.

Professional access must be explicitly scoped, time-limited where appropriate, and revocable.

---

# 19. FINANCIAL EDUCATION

Develop a future education portal focused on financial topics relevant to physicians.

Potential content areas:

- Student loan management.
- Physician compensation.
- Employment contracts.
- Retirement planning.
- Tax fundamentals.
- Insurance.
- Practice ownership.
- Investing.
- Estate planning.
- Financial independence.

Education should connect naturally to Rounds findings and follow-up plans.

An educational recommendation should never be represented as a personalized professional opinion without an appropriate basis.

---

# 20. DOMAIN AND DATA MODEL

Design the schema around the following bounded contexts.

## Identity

- User
- UserProfile
- AuthenticationFactor
- RecoveryCode
- Session
- SecurityEvent
- ConsentRecord

## Account Aggregation

- FinancialInstitution
- FinancialConnection
- FinancialAccount
- AccountBalanceSnapshot
- FinancialTransaction
- TransactionCategory
- InvestmentHolding
- LiabilitySnapshot
- SyncEvent

## Financial Planning

- FinancialProfile
- FinancialGoal
- GoalMilestone
- Budget
- BudgetCategory
- FinancialMetricSnapshot
- FinancialAssessment

## Rounds

- RoundsReport
- RoundsDataSnapshot
- RoundsFinding
- RoundsRecommendation
- RoundsMetric
- RoundsScore
- RoundsUserResponse
- RoundsVersion

## Follow-up

- ActionPlan
- ActionItem
- ActionItemEvent
- Reminder

## Notifications

- Notification
- NotificationPreference
- NotificationDelivery
- DigestSubscription

## Taxes

- TaxProfile
- TaxYear
- TaxDeadline
- TaxReminder
- TaxDocumentChecklist

## Subscription

- Subscription
- Plan
- Entitlement
- BillingEvent

## Future Business

- BusinessEntity
- BusinessTransaction
- Expense
- Receipt
- MileageEntry
- TaxExport

## Future Investing

- BrokerageConnection
- BrokerageAccount
- InvestmentOrder
- TradeExecution
- InvestmentGroup
- GroupMembership
- GroupContribution
- CapitalAccount
- GroupLedgerEntry

These are conceptual entities, not instructions to generate every table immediately.

Normalize relationships carefully.

Use appropriate foreign keys, uniqueness constraints, data ownership, and indexes.

Prevent the exposure of one user's records to another user.

Maintain an explicit distinction between source data, derived calculations, and AI-generated interpretations.

---

# 21. SECURITY, PRIVACY, AND GOVERNANCE

Financial data security is a launch requirement.

Implement:

- TLS.
- Encryption at rest.
- Application-level encryption for sensitive tokens.
- Secrets management.
- Least-privilege access.
- Authorization checks.
- MFA.
- Audit logging.
- Secure backups.
- Disaster recovery.
- Data retention policies.
- Account deletion workflows.
- Vendor risk assessment.
- Dependency monitoring.
- Automated security scanning.
- Webhook authentication.
- Rate limiting.
- Secure file upload validation.
- Data minimization.

Never log:

- Plaid access tokens.
- Authentication secrets.
- Recovery codes.
- Raw financial credentials.
- Sensitive report payloads.
- Unredacted financial documents.

Document incident response, access monitoring, and production recovery procedures.

Evaluate applicable federal and state privacy laws, security requirements, financial service regulations, vendor contracts, and breach notification obligations before production release.

Do not claim that the application is compliant with a regulation merely because a technical control has been implemented.

---

# 22. DESIGN SYSTEM

Use a professional financial application design that respects the Finternist identity.

Existing Finternist design tokens:

- Dark slate: `#243036`
- Gold: `#bfa76f`
- Sage: `#6e9075`
- White: `#ffffff`
- Background: `#f5f7fa`
- Deep teal: `#004d43`
- Cyan: `#00eaff`
- Lime: `#00e676`

Treat these as the existing brand palette, subject to later founder approval.

The product interface should be clean, disciplined, and information-dense without being cluttered.

Use:

- Consistent typography.
- Clear financial tables.
- Accessible contrast.
- High-quality forms.
- Responsive dashboards.
- Progressive disclosure.
- Helpful empty states.
- Data freshness indicators.
- Charts with explicit units and time ranges.
- Clear loading and error states.

Avoid gimmicky medical metaphors, excessive badges, artificial gamification, or AI-generated marketing slogans.

The clinical inspiration belongs primarily in the information architecture and workflows.

---

# 23. TESTING REQUIREMENTS

Write tests for all critical application behavior.

## Identity

- Signup.
- Login.
- Verification.
- MFA enrollment.
- MFA validation.
- Password reset.
- Account recovery.
- Authorization.

## Plaid

- Link token creation.
- Token exchange.
- Account import.
- Transaction sync.
- Cursor persistence.
- Modified and removed transactions.
- Duplicate webhook delivery.
- Reconnection.
- Provider failure.

## Financial analytics

- Net worth.
- Cash flow.
- Savings rate.
- Debt ratios.
- Goal progress.
- Missing data.
- Currency precision.
- Historical comparisons.

## Rounds

- Snapshot creation.
- Metric calculation.
- Score versioning.
- Schema validation.
- Report generation.
- Recommendation creation.
- Duplicate prevention.
- Follow-up comparisons.
- AI provider failures.

## Notifications

- Preferences.
- Reminder scheduling.
- Retry handling.
- Idempotency.
- Unsubscribe behavior.

## Tax reminders

- Deadline jurisdiction.
- Deadline version.
- Date changes.
- Duplicate reminder prevention.

Include system tests for complete user journeys.

Use mocks, stubs, and provider sandboxes rather than real financial accounts in automated tests.

---

# 24. DEVOPS AND DEPLOYMENT

Provide:

- Development Docker configuration.
- Environment variable template.
- Local database setup.
- Background job configuration.
- Mailgun integration configuration.
- Plaid Sandbox configuration.
- CI pipeline.
- Dependency scanning.
- Automated tests.
- Deployment documentation.
- Backup and restore documentation.
- Health checks.
- Monitoring and alerting.

Production configuration must be separate from local development.

Create a threat model before connecting real financial data.

Do not expose production secrets to coding agents or include them in repository files.

---

# 25. REQUIRED REPOSITORY DOCUMENTATION

Create the following structure, adjusting as needed:

```text
finternist/
├── CLAUDE.md
├── README.md
├── .env.example
├── docs/
│   ├── product/
│   │   ├── vision.md
│   │   ├── prd.md
│   │   ├── personas.md
│   │   ├── user-journeys.md
│   │   ├── roadmap.md
│   │   └── acceptance-criteria.md
│   ├── architecture/
│   │   ├── overview.md
│   │   ├── decisions/
│   │   ├── domain-model.md
│   │   ├── data-model.md
│   │   ├── integrations.md
│   │   └── background-jobs.md
│   ├── features/
│   │   ├── authentication.md
│   │   ├── onboarding.md
│   │   ├── plaid.md
│   │   ├── dashboard.md
│   │   ├── rounds.md
│   │   ├── action-plans.md
│   │   ├── notifications.md
│   │   ├── taxes.md
│   │   ├── subscriptions.md
│   │   ├── business-finances.md
│   │   ├── investing.md
│   │   └── group-investing.md
│   ├── security/
│   │   ├── threat-model.md
│   │   ├── data-classification.md
│   │   ├── privacy.md
│   │   └── incident-response.md
│   ├── design/
│   │   ├── design-system.md
│   │   └── ui-patterns.md
│   ├── development/
│   │   ├── workflow.md
│   │   ├── testing.md
│   │   └── deployment.md
│   └── planning/
│       ├── backlog.md
│       ├── current-sprint.md
│       ├── decisions.md
│       └── progress.md
├── app/
├── config/
├── db/
├── spec/
└── .github/
```

Do not create empty documentation for the sake of appearing complete.

Each document should contain usable specifications and decisions relevant to its purpose.

`CLAUDE.md` must remain a concise operating guide linking to the detailed specifications. Do not place the entire product specification into one enormous context file.

---

# 26. DEVELOPMENT PHASES

## Phase 0: Foundation

Deliver:

- Architecture and PRD.
- Initial backlog.
- Rails application.
- Database.
- CI.
- Testing framework.
- Design system foundation.
- Development environment.
- Security baseline.

## Phase 1: Identity

Deliver:

- User registration.
- Verification.
- Login and logout.
- MFA.
- Password recovery.
- Mailgun templates.
- Account settings.
- Authentication tests.

## Phase 2: Financial Intake

Deliver:

- Financial HPI.
- Professional profile.
- Financial goals.
- Onboarding progress.
- Manual financial data entry.
- Data consent workflow.

## Phase 3: Plaid

Deliver:

- Plaid Sandbox integration.
- Account linking.
- Account storage.
- Balance and transaction synchronization.
- Webhooks.
- Reconnection handling.
- Account management interface.

## Phase 4: Financial Dashboard

Deliver:

- Overview.
- Accounts.
- Spending.
- Cash flow.
- Liabilities.
- Goals.
- Core analytics.

## Phase 5: Rounds

Deliver:

- Deterministic financial metrics.
- Financial snapshot generation.
- AI-provider adapter.
- Structured Rounds report.
- Report storage and versioning.
- Review interface.
- Missing-data handling.

## Phase 6: Follow-up

Deliver:

- AI-suggested actions.
- User approval.
- Task lifecycle.
- Goal associations.
- Follow-up scheduling.
- Longitudinal report comparison.

## Phase 7: Reminders and Notifications

Deliver:

- Notification system.
- In-app notifications.
- Mailgun notifications.
- Notification preferences.
- Tax reminders.
- Task reminders.

## Phase 8: Launch Readiness

Deliver:

- Security review.
- Integration testing.
- User acceptance testing.
- Performance review.
- Backup and recovery testing.
- Privacy and legal readiness assessment.
- Production deployment procedure.

Later phases must be tracked separately from the MVP.

---

# 27. ACCEPTANCE CRITERIA FOR THE MVP

The MVP is complete only when a user can:

1. Register and verify an account.
2. Secure the account with MFA.
3. Complete a structured financial history.
4. Connect a financial institution through Plaid Sandbox or enter data manually.
5. View accurate financial accounts and metrics.
6. Review financial goals.
7. Generate a structured Rounds report.
8. Inspect the factual basis of the report.
9. Receive AI-assisted interpretation based on the verified data.
10. Accept recommendations into a follow-up plan.
11. Manage and complete follow-up actions.
12. Receive notifications and tax reminders.
13. Generate a later report showing measurable longitudinal changes.
14. Manage connected institutions, privacy preferences, and account deletion.

Every critical workflow must have tests and documented failure behavior.

An attractive interface alone does not satisfy acceptance criteria.

---

# 28. AGENT ORCHESTRATION

If multiple coding agents are available, establish role-based responsibilities.

### Architecture Agent

Owns:

- System architecture.
- Data model.
- Architectural decision records.
- Integration boundaries.
- Technical debt review.

### Backend Agent

Owns:

- Rails business logic.
- Database models.
- Services.
- Background jobs.
- API integrations.

### Frontend Agent

Owns:

- Hotwire.
- Stimulus.
- Tailwind.
- Component implementation.
- Responsive layouts.
- Accessibility.

### Security Agent

Owns:

- Threat modeling.
- Authentication review.
- Authorization review.
- Secrets handling.
- Security test recommendations.

### QA Agent

Owns:

- Acceptance tests.
- Unit and integration tests.
- System tests.
- Regression evaluation.

### Documentation Agent

Owns:

- Technical documentation.
- User workflows.
- Developer setup.
- Decision logs.

The principal agent is responsible for coordinating work, maintaining coherent implementation decisions, and preventing conflicting modifications.

Agents must not independently introduce architectural dependencies without coordination.

---

# 29. CONTEXT AND PROGRESS MANAGEMENT

Maintain reliable development continuity.

At the beginning of each development session:

1. Read `CLAUDE.md`.
2. Read the current sprint.
3. Review recent commits and repository status.
4. Review active architectural decisions.
5. Identify the next uncompleted vertical slice.
6. Continue implementation.

After each meaningful implementation:

- Update completed acceptance criteria.
- Record architectural decisions.
- Update relevant documentation.
- Record outstanding problems.
- Update the current sprint.
- Run the necessary tests.
- Commit coherent working changes when permitted.

Never claim a feature is complete merely because source files exist.

Differentiate:

- Planned.
- Implemented.
- Tested.
- Integrated.
- Production-ready.

Preserve explicit decisions across development sessions.

---

# 30. INITIAL EXECUTION INSTRUCTIONS

Start by inspecting the repository and existing code.

Do not assume the repository already contains a working application.

If the repository is empty, initialize it according to the recommended stack.

Then:

**Step 1:** Create a product requirements document reflecting the complete specification.

**Step 2:** Create an architecture document, domain model, data model, and architectural decision records.

**Step 3:** Create a phased development roadmap with milestones, dependencies, and testable acceptance criteria.

**Step 4:** Identify external integrations, required credentials, account prerequisites, costs, and provider risks.

**Step 5:** Establish `CLAUDE.md` and persistent planning documents.

**Step 6:** Initialize the Rails application and development infrastructure if necessary.

**Step 7:** Implement the first working vertical slice: registration, email verification, login, and MFA.

**Step 8:** Continue through the MVP roadmap sequentially, integrating and testing each feature rather than developing disconnected mockups.

## Expectations for your first response

Provide:

1. A brief assessment of the current repository.
2. The architecture you recommend.
3. The proposed MVP implementation sequence.
4. Important decisions that require founder approval.
5. The first concrete implementation actions.

Then begin making repository changes within your available permissions.

Do not produce a generic startup business plan.

Do not redesign the product vision.

Do not silently discard planned later features.

Do not implement live trading until required approvals, compliance work, and brokerage partnerships are established.

Build Finternist as an extensible, secure, physician-centered financial platform with Rounds and longitudinal financial follow-up at its core.