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

- `internal/llm` - message types, model metadata, stream events, and the first model-backend boundary
- `internal/tools` - built-in tools and registry
- `internal/agent` - loop, tool execution, in-memory state, abort handling
- `internal/session` - append-only session log and restoration
- `internal/runtime` - application orchestration over agent + session + tools
- `internal/cli` - print/json operator modes

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

## Deferred surfaces

These are expected later, but they do not shape the phase-1 runtime:

- TUI host
- plugin/extension system
- broad provider matrix
- additional integrations and side binaries
