---
id: docs_plans_index
kind: planning-index
status: active
authority: canonical
owner: limerc
created_at: 2026-04-21
updated_at: 2026-04-21
review_after: 2026-07-21
canonical_for:
  - implementation-planning-process
  - active-plan-index
related_docs:
  - docs/README.md
  - docs/product/IMPLEMENTATION-STATUS.md
  - docs/product/ROADMAP.md
supersedes: []
superseded_by: null
---

# Plans

This file owns the checked-in implementation planning workflow for `tau`.

Use plans to break product direction into bounded implementation slices.

## Read order for implementation work

When resuming active work, read in this order:

1. `docs/PLANS.md`
2. `docs/product/IMPLEMENTATION-STATUS.md`
3. the relevant file in `docs/plans/`
4. `.signum/contracts/<contractId>/...` only if deeper execution evidence is needed

Checked-in docs are the primary onboarding memory.

`.signum/` is evidence-on-demand, not the first place a new session should start.

## Plan file rules

- Put active implementation plans in `docs/plans/`.
- Use stable ordered filenames like `000-phase1-headless-runtime-core.md`.
- Reserve numeric gaps so later insertions can use `005`, `015`, or similar without renaming the full plan set.
- Keep one main workstream per plan.
- Break work into bounded slices with clear acceptance criteria.
- Prefer slices that can land as one PR or one tightly related stack.
- Every plan must include a `Slice tracker` table so a new agent can tell what is `done`, `in_progress`, `blocked`, `planned`, or `deferred` without inferring status from prose.
- Record status in the plan as work changes.
- If a plan introduces a decision that becomes current product truth, update the owning product doc too.

## Status vocabulary

Use these states consistently:

- `planned` - accepted as future work, not started
- `in_progress` - active implementation or verification work
- `blocked` - cannot proceed without an external decision or missing prerequisite
- `done` - implemented and verified to the level claimed in the plan
- `deferred` - intentionally not part of the current phase or slice

## Signum workflow

Use `signum` for bounded execution slices, not for whole roadmaps.

Recommended flow:

1. choose the next slice from the active plan
2. run a bounded `signum` contract for that slice
3. keep raw execution evidence under `.signum/contracts/<contractId>/`
4. update the plan doc with the result
5. update `docs/product/IMPLEMENTATION-STATUS.md` with what is now true

## Slice tracker

Each implementation plan should carry a `Slice tracker` table with at least:

- Slice ID
- Slice name
- Status
- Commit
- Verified
- Evidence
- Notes

This table is the primary checked-in answer to "what is already done and what is next?".

## Execution evidence ledger

Each implementation plan should also carry an evidence ledger table with at least:

- Slice
- Date
- Signum Contract
- Decision
- Commit
- Notes

## Active plans

| Workstream | Status | Plan |
|---|---|---|
| Phase 1 headless runtime core | in_progress | `docs/plans/000-phase1-headless-runtime-core.md` |
