# Finternist — operating guide for agents

Physician-focused financial health platform (Rails monolith). Read before every session.

## Source of truth
1. `docs/MASTER_SPEC.md` — founder's master product and engineering specification. **Wins on any conflict.** Agents may not edit it.
2. Everything else in `docs/` (PRD, architecture, ADRs, roadmap, planning) is derived from it and maintained by agents. The early starter docs (`docs/ARCHITECTURE.md`, `docs/MVP_SCOPE.md`, `docs/BUILD_BACKLOG.md`) predate the master spec; where they differ (e.g. Minitest vs RSpec), follow the master spec and update or retire them.

## Rules
- Branch + PR only; never push to `main`. The founder merges.
- Fictional/sandbox data only. No real financial data, no production secrets in the repo or in agent environments.
- Money as integer cents or decimal, never floats. Never infer zero for missing data.
- Every financial record is scoped to its owner (`Current.user`); test it.
- AI is never the source of truth for calculations; no autonomous money movement or trading.
- Tests (RSpec) must pass before a feature is called done. Distinguish planned / implemented / tested / integrated / production-ready.
- Escalate to the founder: regulatory risk, real costs, irreversible data-model choices, scope changes.

## Session routine
Read this file → `docs/planning/current-sprint.md` → recent commits → active ADRs → next unfinished vertical slice. After meaningful work, update `docs/planning/progress.md` and decisions.
