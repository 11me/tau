---
id: docs_product_architecture
kind: architecture-doc
status: active
authority: canonical
owner: limerc
created_at: 2026-04-21
updated_at: 2026-04-21
review_after: 2026-07-21
canonical_for:
  - architecture-boundaries
  - runtime-shape
related_docs:
  - docs/README.md
  - docs/product/ROADMAP.md
supersedes: []
superseded_by: null
---

# Architecture

## Intent

`tau` is an early-stage Go-native terminal agent harness focused on a smaller, faster, easier-to-reason-about runtime.

This document describes the target architecture, not an existing implementation.

## Active architecture target

Phase 1 target:

```text
cmd/tau
  -> internal/cli
    -> internal/runtime
      -> internal/agent
      -> internal/session
      -> internal/tools
      -> internal/config

internal/agent
  -> internal/llm
  -> internal/tools
```

## First runtime boundary

The first useful runtime consists of:

- `internal/llm` - canonical message types, model metadata, stream events, and the first model-backend boundary
- `internal/tools` - tool registry, host-side tool services, and bundled standard-library tools; first built-ins are `read`, `write`, `edit`, and `bash`
- `internal/agent` - loop, tool execution, in-memory state, abort handling, and optional steering/follow-up queue primitives
- `internal/session` - tree-ready append-only session log and restoration
- `internal/runtime` - application orchestration over agent + session + tools, including retry policy
- `internal/cli` - print/json operator modes

## Phase 1 kernel and bundled standard library

Phase 1 should be read as a minimal host kernel plus a bundled standard library of tools.

Host-kernel responsibilities in the first build:

- model/backend boundary
- agent loop and turn execution
- session persistence and restore
- retry policy
- tool scheduling and execution ordering
- file-mutation coordination
- config loading and operator mode selection

Bundled standard-library responsibilities in the first build:

- `read`
- `write`
- `edit`
- `bash`

The first binary ships both layers together, but the agent loop should depend on the tool registry/contract rather than on individual built-in implementations.

## Early runtime commitments

- Session persistence starts tree-ready. The stored format should carry stable entry ids and parent links from the beginning even if early flows remain linear.
- Steering/follow-up queue primitives may remain in the core contract, but they do not define the first operator surface.
- Automatic retry for transient model/provider failures is part of the first headless core. Compaction remains deferred until later.
- Phase-1 retry stays narrow: it retries transient upstream failures at the model/request layer and does not retry user aborts, context-overflow handling, or tool-level execution failures.
- If one assistant message produces multiple tool calls, durable runtime artifacts stay in assistant source order. This applies to committed tool-result messages, turn summaries, and session persistence.
- Runtime progress may stream while tools are executing, but wall-clock completion must not reorder committed transcript state.

## Principles

- core before surfaces
- contracts before source shape
- local-first and inspectable runtime state
- `internal`-first until a real external SDK surface exists
- compatibility is selective, not absolute

## Compatibility boundary

Compatibility should be preserved first at:

- session format
- runtime event/rpc shape
- tool behavior contracts

Compatibility should not be preserved blindly at:

- legacy source structure
- internal implementation details
- UI rendering behavior

## Public API stance

Start `internal`-first.

Public Go packages can be extracted later only if they stabilize as real SDK surfaces.

## Deferred extension strategy, preserved seams

A real plugin/extension system is deferred until after the headless core and rpc surfaces exist.

Phase 1 should preserve clean seams for later work without promising extension compatibility yet:

- Built-in tools execute through `internal/tools` registry/boundary logic rather than agent-loop special cases.
- Phase-1 built-ins are bundled with the binary as privileged standard-library tools, not external plugins.
- Future non-core tools should fit the same general tool contract where practical, but the external extension host is a later design problem.
- Runtime lifecycle boundaries stay explicit around prompt preparation, model calls, retry, tool execution, and session load/save. Any event surface may remain internal for now.
- Session storage stays append-only, tree-ready, and forward-extensible enough to add future non-core entry kinds without forcing a format reset.
- Configuration remains layered across CLI, user, and project scope.
- Project-scoped behavior should resolve through a dedicated project-root decision instead of ad hoc per-surface discovery rules.
- File mutation coordination stays centralized in the host runtime so future non-core tools cannot bypass ordering and safety guarantees.
- TUI, rpc, and later operator affordances layer on top of the headless runtime contract; they do not define the future extension model.

## Deferred surfaces

These are expected later, but they do not shape the phase-1 runtime:

- TUI host
- plugin/extension system
- broad provider matrix
- additional integrations and side binaries
