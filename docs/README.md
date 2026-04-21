# Docs

Start here for repository guidance.

Use the smallest useful set of docs for the task.

## Task router

| Task | Read first | Then if needed |
|---|---|---|
| General project orientation | `docs/product/START-HERE.md` | `docs/product/ARCHITECTURE.md`, `docs/product/ROADMAP.md` |
| Continue implementation work or resume an active build | `docs/PLANS.md` | `docs/product/IMPLEMENTATION-STATUS.md`, relevant `docs/plans/`, `.signum/contracts/<contractId>/...` if needed |
| Change docs | `docs/DOCS.md` | the canonical owner in `docs/product/`, `docs/adr/`, `docs/research/`, `docs/ideas/`, or `docs/plans/` |
| Change architecture or runtime boundaries | `docs/product/ARCHITECTURE.md` | `docs/product/ROADMAP.md`, `docs/adr/` |
| Change roadmap or current phase | `docs/product/ROADMAP.md` | `docs/product/START-HERE.md`, `docs/product/ARCHITECTURE.md` |
| Review accepted decision history | `docs/adr/` | relevant product docs |
| Gather advisory background or backlog | `docs/research/`, `docs/ideas/` | relevant product docs |
| Create or update an implementation plan | `docs/PLANS.md` | `docs/plans/`, `docs/product/IMPLEMENTATION-STATUS.md` |
| Create a research or idea note | `docs/research/README.md` or `docs/ideas/README.md` | `docs/_templates/` |

## Canonical owners

| Truth surface | Canonical owner |
|---|---|
| Project orientation and current target | `docs/product/START-HERE.md` |
| Target runtime architecture | `docs/product/ARCHITECTURE.md` |
| Phase order and exit criteria | `docs/product/ROADMAP.md` |
| Implementation planning process and active plan index | `docs/PLANS.md` |
| Current implementation status | `docs/product/IMPLEMENTATION-STATUS.md` |
| Documentation process and writing rules | `docs/DOCS.md` |
| Accepted decision history | `docs/adr/` |
| Advisory research | `docs/research/` |
| Speculative backlog | `docs/ideas/` |

## Promotion path

Use these surfaces intentionally:

- `docs/research/` - advisory research notes
- `docs/ideas/` - speculative backlog
- `docs/adr/` - accepted decisions and rationale
- `docs/product/` - current product truth
- `docs/PLANS.md` + `docs/plans/` - checked-in implementation plans and workstream memory

Promote insight forward instead of duplicating it across layers.

## Layout

```text
docs/
  README.md            routing index and owner map
  DOCS.md              documentation process and writing rules
  PLANS.md             implementation planning process and active plan index
  product/             canonical product truth
  plans/               checked-in implementation plans
  adr/                 accepted architectural decisions
  research/            advisory research notes
  ideas/               speculative backlog
  _templates/          templates for recurring doc artifacts
```

## Rules

- Current product truth lives in `docs/product/`.
- Checked-in implementation planning memory lives in `docs/PLANS.md` and `docs/plans/`.
- `docs/README.md` routes; it does not replace the owning docs.
- ADRs explain accepted decisions, but do not replace product docs.
- Research and ideas are advisory until promoted.
- Do not duplicate truth. Update the owner and link elsewhere.
- Do not silently delete current truth. Supersede, archive, or retire it.
