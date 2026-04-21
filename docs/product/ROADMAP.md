---
id: docs_product_roadmap
kind: roadmap-doc
status: active
authority: canonical
owner: limerc
created_at: 2026-04-21
updated_at: 2026-04-21
review_after: 2026-07-21
canonical_for:
  - roadmap
  - phase-order
related_docs:
  - docs/README.md
  - docs/product/ARCHITECTURE.md
supersedes: []
superseded_by: null
---

# Roadmap

Current state: the repo is still in Phase 0. Later phases are planning targets only.

## Phase 0 - documentation and repo skeleton

Exit criteria:

- canonical product docs exist
- repo memory boundaries are in place
- package/runtime direction is explicit

## Phase 1 - headless runtime core

Target:

- one CLI binary
- one provider first
- basic tools: `read`, `write`, `bash`
- agent loop with tool calling
- append-only sessions
- print/json modes

Exit criteria:

- usable end-to-end prompt -> tool -> continue loop
- sessions can be resumed locally
- runtime events are inspectable

## Phase 2 - rpc and compatibility surfaces

Target:

- rpc/json event surface
- more stable runtime contracts
- fixture-based compatibility checks

## Phase 3 - operator UX

Target:

- initial TUI
- model/session selection
- better operator controls

## Phase 4 - breadth

Target:

- more providers
- more tools
- extension/plugin strategy
- additional integrations if they are still justified

## Discipline

Do not pull later phases forward just because a surface is documented.
