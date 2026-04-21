---
id: docs_plans_phase1_headless_runtime_core_000
kind: implementation-plan
status: active
authority: canonical
owner: limerc
created_at: 2026-04-21
updated_at: 2026-04-21
review_after: 2026-07-21
canonical_for:
  - phase1-headless-runtime-core-implementation-plan
related_docs:
  - docs/PLANS.md
  - docs/product/IMPLEMENTATION-STATUS.md
  - docs/product/START-HERE.md
  - docs/product/ARCHITECTURE.md
  - docs/product/ROADMAP.md
supersedes: []
superseded_by: null
---

# 000 Phase 1 Headless Runtime Core

## Status

- Workstream: Phase 1 headless runtime core
- Workstream status: in_progress
- Current repo state: no Go implementation checked in yet
- Current slice: none started yet
- Next slice: `010` repository skeleton and build discipline
- Last verified implementation commit: none

## Goal

Implement the first working version of `tau` as a Go-native headless terminal agent harness.

Phase 1 should establish this loop:

```text
prompt -> model call -> tool call(s) -> tool execution -> tool results -> model continues -> final answer
```

Sessions must persist locally, remain inspectable, be append-only, and be tree-ready from day one.

## How agents should determine progress

Do not infer progress from prose, file dates, or partial code state.

Use these checked-in surfaces in order:

1. `docs/PLANS.md` - which workstream is active and which plan file owns it
2. `docs/product/IMPLEMENTATION-STATUS.md` - what is currently done, in progress, and next
3. the `Slice tracker` table in this file - the authoritative slice-by-slice state for this workstream
4. `.signum/contracts/<contractId>/...` only when deeper execution evidence is needed

For this plan, the `Slice tracker` table is the primary answer to:

- what is already done
- what is currently active
- what is blocked
- what comes next
- what was verified and by which commit

## Required reading before implementation

Read only the smallest useful set first:

```text
AGENTS.md
docs/README.md
docs/product/START-HERE.md
docs/product/ARCHITECTURE.md
docs/product/ROADMAP.md
docs/DOCS.md
```

## Non-negotiable constraints

1. Core before surfaces.
2. Contracts before UI.
3. Internal-first Go packages.
4. One real provider at most in Phase 1, with a fake provider first for deterministic tests.
5. No extension host in Phase 1.
6. No compaction in Phase 1.
7. No permission prompts in Phase 1.
8. Sequential tool execution in Phase 1.
9. If one assistant message emits multiple tool calls, durable tool results still commit in assistant source order.
10. Retry only transient model/provider failures.
11. Do not retry aborts, context overflow, or tool execution failures.
12. Every implementation slice must keep `go test ./...` green.

## Target package layout

```text
go.mod
cmd/tau/main.go

internal/cli/
internal/runtime/
internal/agent/
internal/llm/
internal/events/
internal/session/
internal/tools/
internal/tools/std/
internal/workspace/
internal/config/
```

Recommended dependency shape:

```text
cmd/tau
  -> internal/cli
      -> internal/runtime

internal/runtime
  -> internal/config
  -> internal/session
  -> internal/agent
  -> internal/tools
  -> internal/tools/std
  -> internal/workspace
  -> internal/events
  -> internal/llm

internal/agent
  -> internal/llm
  -> internal/tools
  -> internal/events

internal/tools/std
  -> internal/tools
  -> internal/workspace

internal/session
  -> internal/llm
```

Avoid these reverse dependencies:

```text
internal/session -> internal/runtime
internal/tools   -> internal/agent
internal/llm     -> internal/runtime
```

Boundary summary:

- `runtime` owns orchestration
- `agent` owns the loop
- `session` owns persistence
- `tools` owns the contract and registry
- `tools/std` owns bundled built-ins
- `workspace` owns root, cwd, path resolution, and file-mutation coordination
- `llm` owns provider abstraction and canonical model-facing types
- `cli` owns flags and output only

## Slice tracker

| ID | Slice | Status | Commit | Verified | Evidence | Notes |
|---|---|---|---|---|---|---|
| 010 | Repository skeleton and build discipline | planned | - | - | - | next slice |
| 020 | Canonical LLM types, events, fake provider | planned | - | - | - | depends on 010 |
| 030 | Session v1 append-only tree-ready JSONL | planned | - | - | - | depends on 010 and 020 |
| 040 | Workspace and standard tool registry | planned | - | - | - | depends on 010 |
| 050 | Agent loop with fake provider | planned | - | - | - | depends on 020, 030, 040 |
| 060 | Runtime orchestration, config, retry | planned | - | - | - | depends on 020, 030, 040, 050 |
| 070 | CLI print/json modes | planned | - | - | - | depends on 060 |
| 080 | One real provider adapter | planned | - | - | - | depends on 070 |
| 090 | Docs and ADRs after working core | planned | - | - | - | depends on earlier slices |

## Detailed slices

### 010 - Repository skeleton and build discipline

Goal:

- create the minimal Go project skeleton that builds and tests cleanly

Deliverables:

- `go.mod` with `module github.com/11me/tau`
- `cmd/tau/main.go`
- initial package files under the target `internal/` layout
- `tau --help`
- `tau --version`
- CI workflow running `go test ./...` and `go vet ./...`
- README updated from `No implementation yet` to a minimal build/test entrypoint

Acceptance:

```bash
go test ./...
go vet ./...
go run ./cmd/tau --help
go run ./cmd/tau --version
```

### 020 - Canonical LLM types, runtime events, fake provider

Goal:

- define provider-independent message and stream types
- add a deterministic fake provider
- add a stable-enough event model for JSON mode and later RPC

Core deliverables:

- `internal/llm/types.go`
- `internal/llm/provider.go`
- `internal/llm/fake_provider.go`
- `internal/events/events.go`
- `internal/events/writer.go`

Key requirements:

- canonical roles include `system`, `user`, `assistant`, `tool_result`
- assistant content can carry tool calls as content blocks
- provider interface is stream-oriented
- define `ErrTransient`, `ErrContextOverflow`, `ErrAborted`
- add `IsTransient(err error)` helper
- fake provider supports scripted assistant responses, streamed deltas, and transient failures

Required tests:

- `TestMessageJSONRoundTrip`
- `TestToolCallArgumentsPreserved`
- `TestFakeProviderReturnsScriptedMessages`
- `TestFakeProviderCanReturnTransientError`
- `TestEventJSONRoundTrip`

### 030 - Session v1 append-only tree-ready JSONL

Goal:

- implement durable session storage before the real loop exists

Core deliverables:

- `internal/session/types.go`
- `internal/session/store.go`
- `internal/session/id.go`
- `internal/session/context.go`

Requirements:

- JSONL session file
- header first line
- append-only entries after that
- every entry has `id`, `parent_id`, `seq`, and timestamp
- `tool_result` persists as a message entry
- active path and branching are supported from v1

Required API:

- `Create(path string, header Header) (*Store, error)`
- `Open(path string) (*Store, error)`
- `Header() Header`
- `Entries() []Entry`
- `Append(parentID *string, msg llm.Message) (Entry, error)`
- `ActivePath(leafID string) ([]Entry, error)`
- `LatestLeaf() (*Entry, error)`
- `MessagesForContext(leafID string) ([]llm.Message, error)`

Required tests:

- `TestCreateSessionWritesHeader`
- `TestAppendEntryWritesJSONL`
- `TestOpenSessionReadsHeaderAndEntries`
- `TestActivePathLinear`
- `TestActivePathBranching`
- `TestMessagesForContext`
- `TestAppendPreservesSeq`
- `TestMalformedJSONLReturnsHelpfulError`

### 040 - Workspace and standard tool registry

Goal:

- define the tool contract and registry
- implement built-ins `read`, `write`, `edit`, `bash`

Core deliverables:

- `internal/tools/types.go`
- `internal/tools/registry.go`
- `internal/workspace/workspace.go`
- `internal/workspace/path.go`
- `internal/workspace/mutation.go`
- `internal/tools/std/{read,write,edit,bash,register}.go`

Requirements:

- duplicate tool registration fails
- workspace path resolution stays inside the workspace root
- no permission prompts
- `read` supports bounded text reads
- `write` supports exact content writes with overwrite control
- `edit` supports exact single replacement only
- `bash` captures stdout, stderr, exit code, timeout, and truncation
- tool-level failures become user-visible tool results, not infrastructure retries

Required tests:

- `TestRegistryRejectsDuplicateTool`
- `TestRegistryReturnsToolSpecs`
- `TestWorkspaceRejectsPathEscape`
- `TestReadTextFile`
- `TestReadMissingFileReturnsToolError`
- `TestWriteCreatesParentDirs`
- `TestWriteRespectsOverwriteFalse`
- `TestEditExactSingleReplacement`
- `TestEditNoMatchReturnsToolError`
- `TestEditMultipleMatchesReturnsToolError`
- `TestBashCapturesStdoutStderrExitCode`
- `TestBashTimeout`
- `TestBashOutputTruncation`

### 050 - Agent loop with fake provider

Goal:

- build the deterministic core loop over fake provider, tools, and session

Core deliverables:

- `internal/agent/agent.go`
- `internal/agent/loop.go`
- `internal/agent/context.go`
- `internal/agent/queue.go`
- `internal/agent/errors.go`

Loop rules:

1. runtime appends the user message first
2. agent builds request from active session context
3. agent calls provider stream
4. agent aggregates one assistant message
5. assistant message is committed
6. if there are no tool calls, stop
7. if there are tool calls, execute them sequentially in content order
8. each tool result is converted into a `tool_result` message and committed
9. repeat until final assistant answer or `MaxTurns`

Internal queue primitives may exist, but no CLI or RPC surface should expose them yet.

Required tests:

- `TestAgentFinalAnswerNoTools`
- `TestAgentExecutesSingleToolThenContinues`
- `TestAgentExecutesMultipleToolsInSourceOrder`
- `TestAgentCommitsToolResultsInSourceOrder`
- `TestAgentUnknownToolBecomesToolErrorResult`
- `TestAgentToolErrorIsSentBackToModel`
- `TestAgentStopsAtMaxTurns`
- `TestAgentAbortStopsLoop`

### 060 - Runtime orchestration, config, retry

Goal:

- make `runtime` the application host that wires config, sessions, tools, agent, provider, and events together

Core deliverables:

- `internal/runtime/runtime.go`
- `internal/runtime/run_prompt.go`
- `internal/runtime/retry.go`
- `internal/runtime/session_manager.go`
- `internal/config/config.go`
- `internal/config/load.go`

Config layering order:

```text
defaults
< user config
< project config
< environment variables
< CLI flags
```

Suggested config paths:

```text
~/.tau/config.json
.tau/config.json
```

Retry policy:

- retry only transient provider/model failures before a response is committed
- emit `auto_retry_start` and `auto_retry_end`
- never retry tool errors, aborts, context overflow, malformed tool arguments, unknown tools, or session write failures

Required tests:

- `TestRuntimeCreatesSessionAndAppendsUserMessage`
- `TestRuntimeNoSessionUsesInMemorySession`
- `TestRuntimeUsesExistingSession`
- `TestRuntimeRetriesTransientProviderError`
- `TestRuntimeDoesNotRetryToolError`
- `TestRuntimeDoesNotRetryAbort`
- `TestRuntimeEmitsRetryEvents`
- `TestConfigLayering`

### 070 - CLI print/json modes

Goal:

- ship the first operator surface for the headless runtime

Core deliverables:

- `internal/cli/cli.go`
- `internal/cli/flags.go`
- `internal/cli/output.go`
- `internal/cli/json_mode.go`
- `internal/cli/print_mode.go`

Minimum CLI flags:

```text
-p, --print
--mode json
--provider
--model
--api-key
--session
--session-dir
--no-session
--tools
--no-tools
--cwd
--workspace-root
--fake-script
--help
--version
```

Mode rules:

- print mode writes final assistant text to `stdout`
- print mode writes diagnostics to `stderr`
- JSON mode writes one JSON object per line to `stdout`
- JSON mode writes only fatal CLI errors to `stderr`
- if stdin is not a TTY, append stdin content to the prompt with a separator

Required tests:

- `TestCLIHelp`
- `TestCLIPrintModeWithFakeProvider`
- `TestCLIJSONModeEmitsJSONLines`
- `TestCLINoSessionDoesNotCreateFile`
- `TestCLISessionCreatesJSONL`
- `TestCLIToolsFlagRestrictsTools`

### 080 - One real provider adapter

Goal:

- add exactly one real provider after the fake-provider flow is stable

Requirements:

- keep fake provider as the default path in normal tests
- add one real provider under `internal/llm/providers/`
- map `tau` request and stream types to provider-specific requests and responses
- preserve provider and model metadata
- translate transient upstream failures into `ErrTransient`-compatible errors
- keep provider-specific structs out of agent, runtime, and session packages

Required tests:

- `TestProviderRequestMapping`
- `TestProviderToolCallMapping`
- `TestProviderFinalTextMapping`
- `TestProviderTransientErrorMapping`

Optional live tests must stay opt-in behind an environment variable.

### 090 - Docs and ADRs after working core

Goal:

- update owner docs after the first working runtime exists

Expected deliverables:

- ADRs for headless-core-before-TUI, session v1, tool registry, sequential Phase 1 tool execution, narrow retry policy, and internal-first packages
- product docs updated to describe what is true after implementation lands
- if needed, add narrowly scoped product truth docs for session format, tool contract, events, and CLI behavior

## Priority order

### Priority 1

Make this pass before any real provider work:

```bash
tau --mode json \
  --fake-script testdata/read_then_answer.jsonl \
  -p "inspect README.md"
```

Expected behavior:

1. create a session
2. append the user message
3. fake provider emits assistant tool call for `read README.md`
4. runtime executes `read`
5. append a `tool_result`
6. fake provider emits final assistant text
7. print JSONL events
8. reopening the session returns the same active path

### Priority 2

Make print mode work:

```bash
tau \
  --fake-script testdata/read_then_answer.jsonl \
  -p "inspect README.md"
```

Expected stdout:

```text
I read README.md.
```

### Priority 3

Only then add one real provider.

## Suggested fake-script format

Use JSONL test fixtures under `testdata/`.

Suggested files:

```text
testdata/final_answer.jsonl
testdata/read_then_answer.jsonl
testdata/two_tools_then_answer.jsonl
testdata/transient_then_answer.jsonl
```

Example:

```jsonl
{"type":"assistant","content":[{"type":"tool_call","tool_call":{"id":"call_1","name":"read","arguments":{"path":"README.md"}}}]}
{"type":"assistant","content":[{"type":"text","text":"I read README.md."}]}
```

## Explicit non-goals for Phase 1

Do not pull these into the first build:

- TUI
- interactive editor
- slash commands
- extensions
- skills
- packages
- themes
- MCP
- sub-agents
- plan mode
- permission prompts
- background bash
- compaction
- multi-provider matrix
- session browser
- HTML export
- share or upload surfaces
- model selector UI

## Definition of done for Phase 1

Phase 1 is done when all of this is true:

- `tau` builds as one CLI binary
- print mode works
- JSON mode works
- fake-provider deterministic tests pass
- one real provider can complete a prompt
- `read`, `write`, `edit`, `bash` are available through the registry
- the agent loop supports tool calling
- multiple tool calls are committed in assistant source order
- sessions are append-only JSONL
- sessions carry stable entry ids and parent links
- sessions can be resumed from a file
- runtime retries transient provider/model failures only
- runtime events are inspectable
- docs describe current behavior
- deferred Phase 2/3/4 surfaces were not pulled into Phase 1

The strongest first milestone is not a live model call.

It is this:

```bash
tau --mode json --fake-script testdata/read_then_answer.jsonl -p "inspect README"
```

Once that works, `tau` has a real runtime architecture. The first real provider is then an adapter, not the center of the system.

## Execution evidence ledger

| Slice | Date | Signum Contract | Decision | Commit | Notes |
|---|---|---|---|---|---|
| 010 repository skeleton and build discipline | - | - | planned | - | next slice |
| 020 canonical llm types, events, fake provider | - | - | planned | - | blocked on 010 |
| 030 session v1 append-only tree-ready JSONL | - | - | planned | - | blocked on 010 and 020 |
| 040 workspace and standard tool registry | - | - | planned | - | blocked on 010 |
| 050 agent loop with fake provider | - | - | planned | - | blocked on 020, 030, 040 |
| 060 runtime orchestration, config, retry | - | - | planned | - | blocked on 020, 030, 040, 050 |
| 070 CLI print/json modes | - | - | planned | - | blocked on 060 |
| 080 one real provider adapter | - | - | planned | - | blocked on 070 |
| 090 docs and ADRs after working core | - | - | planned | - | blocked on earlier slices |
