---
id: docs_product_start_here
kind: entry-doc
status: active
authority: canonical
owner: limerc
created_at: 2026-04-21
updated_at: 2026-04-21
review_after: 2026-07-21
canonical_for:
  - product-entry-path
  - active-reading-order
related_docs:
  - README.md
  - docs/README.md
  - docs/DOCS.md
  - docs/product/ARCHITECTURE.md
  - docs/product/ROADMAP.md
supersedes: []
superseded_by: null
---

# Start Here

This is the internal orientation page for `tau`.

`tau` is an early-stage Go-native terminal agent harness.

No implementation exists yet. These docs describe the intended direction, boundaries, and priorities for the first build.

## Full orientation order

Use this sequence only when you need broad project orientation.

For targeted tasks, start from `docs/README.md` and follow the task-specific route instead of reading everything.

1. `README.md`
2. `docs/README.md`
3. `docs/DOCS.md`
4. `docs/product/START-HERE.md`
5. `docs/product/ARCHITECTURE.md`
6. `docs/product/ROADMAP.md`
7. `docs/adr/`
8. `docs/research/`
9. `docs/ideas/`

## Active target

Current planned build target:

- Go runtime only
- headless operation first
- minimal tool-calling agent loop
- append-only sessions
- print/json/rpc surfaces before TUI

## Do not optimize for yet

- broad feature coverage before the core exists
- extension host compatibility
- all provider adapters
- polished terminal UI
- browser UI ownership

## Repo memory boundaries

- `docs/product/` owns current product truth
- `docs/adr/` owns accepted decision history
- `docs/research/` holds advisory research
- `docs/ideas/` holds speculative backlog

Keep documentation memory under `docs/` until a surface becomes active enough to justify a dedicated root.
