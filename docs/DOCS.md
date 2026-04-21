# DOCS

This file defines how docs in `tau` should be written and maintained.

## Read minimally

Do not read the full docs tree by default.

Start from `docs/README.md`, identify the owner of the truth surface you need, and read only the relevant docs.

## Where to write what

### `docs/product/`

Use for current product truth.

Current product docs:

- `START-HERE.md`
- `ARCHITECTURE.md`
- `ROADMAP.md`
- `IMPLEMENTATION-STATUS.md`

### `docs/PLANS.md` and `docs/plans/`

Use for checked-in implementation planning memory.

- `docs/PLANS.md` owns the planning workflow and active plan index.
- `docs/plans/` holds bounded implementation plans for active workstreams.

### `docs/adr/`

Use for accepted decisions and rationale.

An ADR explains why a decision was taken. It does not replace the product doc that describes the current state.

### `docs/research/`

Use for advisory research.

Research may synthesize sources and surface implications, but it does not own current truth until promoted.

### `docs/ideas/`

Use for speculative notes and backlog ideas.

Ideas are not commitments.

## Promotion path

Keep the layers distinct.

Typical promotion paths:

```text
research -> adr -> product
idea -> research -> adr -> product
idea -> product            (only for small, obvious, already-accepted changes)
plan -> product            (when implementation work changes current truth)
```

When a decision changes current behavior, update the owning product doc as well as the ADR.

When implementation work changes current state, update `docs/product/IMPLEMENTATION-STATUS.md` as well as the relevant plan doc.

## Writing rules

- Prefer direct, technical prose.
- Describe what is true now, not what feels likely.
- Mark future work explicitly as `planned`, `deferred`, or `parked`.
- Keep each doc scoped to one main truth surface.
- Link to owning docs instead of copying their content.

## Change rules

When editing docs:

1. Identify the canonical owner.
2. Update the owner first.
3. Update summaries and links second.
4. Preserve history instead of silently deleting replaced truth.
5. If ownership changes, update `docs/README.md`.

## New docs

If you add a new product truth doc:

- add frontmatter;
- keep it narrowly scoped;
- add it to `docs/README.md` as a canonical owner.

Minimal frontmatter shape:

```yaml
---
id: docs_product_example
kind: architecture-doc
status: active
authority: canonical
owner: limerc
created_at: 2026-04-21
updated_at: 2026-04-21
review_after: 2026-07-21
canonical_for:
  - example-truth-surface
related_docs: []
supersedes: []
superseded_by: null
---
```

## Templates

Use templates for recurring note types when helpful:

- `docs/_templates/research-note.md`
- `docs/_templates/idea-note.md`

Templates are helpers, not authority.

## Root hygiene

Keep documentation-related memory under `docs/` unless a surface becomes active enough to justify its own root.
