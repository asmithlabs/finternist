# Finternist — operating guide for coding agents

Finternist is a physician-focused financial health platform: a modular Ruby on Rails monolith whose
flagship feature, **Rounds**, is a structured financial progress report modeled on a clinical note.

This file is deliberately short. The specification lives in **[docs/MASTER_SPEC.md](docs/MASTER_SPEC.md)**.

## Source of truth, in order

1. `docs/MASTER_SPEC.md` — the founder's master product and engineering specification. **It wins every conflict.**
2. `docs/` planning and architecture documents you create under it (see MASTER_SPEC §25 for the required tree).
3. `README.md`, `docs/ARCHITECTURE.md`, `docs/MVP_SCOPE.md`, `docs/BUILD_BACKLOG.md` — earlier starter notes.
   Where these disagree with the master spec, **the master spec is correct** and the starter note is stale.

Known conflicts already settled by the spec: tests are **RSpec**, not Minitest (§2); MFA belongs to the
first identity slice (§4, §26 Phase 1); transactional email is **Mailgun** (§4).

## Non-negotiable rules

- **Synthetic data only.** No production financial data, no real banking connections, no live trading.
- **Money is never a binary float.** Use decimal types (§6).
- **Every financial record is scoped to its owner.** One user must never see another's data (§3, §20).
- **No AI-driven money movement.** An LLM never executes orders, moves funds, or silently edits financial
  records, and never invents a metric or a citation (§9).
- **Never infer zero for missing data.** Absent means absent; say so (§6, §8).
- **No secrets in the repo, in logs, or in anything sent to a model** (§21, §24).
- **Never claim regulatory compliance** because a control exists (§21).
- Distinguish calculated facts from AI interpretation everywhere they appear together (§9, §20).

## Each session (MASTER_SPEC §29)

1. Read this file.
2. Read `docs/planning/current-sprint.md` and `docs/planning/decisions.md`.
3. Review recent commits and repository status.
4. Review active architectural decisions (`docs/architecture/decisions/`).
5. Identify the next uncompleted vertical slice.
6. Continue implementation, with tests.

Afterwards update acceptance criteria, decisions, docs, the sprint, and outstanding problems.
Never call a feature complete because files exist — distinguish planned, implemented, tested,
integrated, and production-ready.

## Current state

The repository is **documentation only**. There is no Rails application yet. The next work is
**MASTER_SPEC §26 Phase 0 (Foundation)** via §30 steps 1–6: PRD, architecture and data model, phased
roadmap with acceptance criteria, integrations and credentials inventory, planning documents, then
initialize the Rails application.

Phase 0 explicitly excludes registration, sign-in, identity models, and financial workflows.

## What agents must not change

- `docs/MASTER_SPEC.md` and this file — founder-owned; change them only through a pull request the founder writes.
- `.devorch.yml` — build configuration; same rule.

Unresolved founder decisions go in `docs/planning/decisions.md`. Do not guess them, and do not escalate
decisions the spec already answers. Work stops at an open pull request; nothing is merged or deployed
without explicit human approval.
