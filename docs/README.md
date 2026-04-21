# Docs

Start here for repository guidance.

Use the smallest useful set of docs for the task.

## Task router

| Task | Read first | Then if needed |
|---|---|---|
| General project orientation | `docs/product/START-HERE.md` | `docs/product/ARCHITECTURE.md`, `docs/product/ROADMAP.md` |
| Change docs | `docs/DOCS.md` | the canonical owner in `docs/product/`, `docs/adr/`, `docs/research/`, or `docs/ideas/` |
| Change architecture or runtime boundaries | `docs/product/ARCHITECTURE.md` | `docs/product/ROADMAP.md`, `docs/adr/` |
| Change roadmap or current phase | `docs/product/ROADMAP.md` | `docs/product/START-HERE.md`, `docs/product/ARCHITECTURE.md` |
| Review accepted decision history | `docs/adr/` | relevant product docs |
| Gather advisory background or backlog | `docs/research/`, `docs/ideas/` | relevant product docs |
| Create a research or idea note | `docs/research/README.md` or `docs/ideas/README.md` | `docs/_templates/` |

## Canonical owners

| Truth surface | Canonical owner |
|---|---|
| Project orientation and current target | `docs/product/START-HERE.md` |
| Target runtime architecture | `docs/product/ARCHITECTURE.md` |
| Phase order and exit criteria | `docs/product/ROADMAP.md` |
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

Promote insight forward instead of duplicating it across layers.

## Layout

```text
docs/
  README.md            routing index and owner map
  DOCS.md              documentation process and writing rules
  product/             canonical product truth
  adr/                 accepted architectural decisions
  research/            advisory research notes
  ideas/               speculative backlog
  _templates/          templates for recurring doc artifacts
```

## Rules

- Current product truth lives in `docs/product/`.
- `docs/README.md` routes; it does not replace the owning docs.
- ADRs explain accepted decisions, but do not replace product docs.
- Research and ideas are advisory until promoted.
- Do not duplicate truth. Update the owner and link elsewhere.
- Do not silently delete current truth. Supersede, archive, or retire it.
