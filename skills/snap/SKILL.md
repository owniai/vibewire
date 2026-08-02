---
name: snap
description: "Use ONLY when the user explicitly invokes /vibewire:snap. Implement atomic changes — TDD for behavior, direct for non-behavioral — with self-review and optional external reviewers."
---

# Snap

Implement atomic changes — TDD for behavior, direct for the rest.

## Scope

ALWAYS test through public interfaces — NEVER implementation details.

File references: ALWAYS read on phase entry or stated condition — NEVER pre-read.

## Before Start

Load project context — proceed to Investigate immediately after:

- `.vibewire/project.md` — if missing, prompt `/vibewire:intro`.
- `.vibewire/CHANGELOG.md` and `.vibewire/evolve.md` — grep titles ONLY, read body on-demand. Skip if absent.
- [plan-execution](plan-execution.md) — read ONLY when the prompt contains `PLAN-{N}-{name}`, for PLAN execution.

## Process

### Phase 1: Investigate

**Clarify** — Resolve task ambiguity and confirm scope BEFORE exploring. Investigate by codebase evidence; ask ONLY when evidence cannot answer, the moment uncertainty arises — one question per turn, ALWAYS attach a recommendation; user confirms or corrects, NEVER constructs from zero. Silence is NOT confirmation; NEVER accumulate questions.

**Explore** — Map the codebase in one fluid pass. EVERY finding needs provenance — file paths; distinguish fact from inference.
- **Location** — Trace from entry points through call chains — find ALL relevant locations, not just the first.
- **Baseline** — Read existing code, trace execution paths through public interfaces — NEVER assume how things work without reading.
- **Idiom** — Extract patterns from existing similar features — NEVER invent patterns that conflict.
- **Impact** — Trace dependencies upstream and downstream — NEVER proceed without knowing the blast radius.

Prefer dispatching ONE Explore agent for this pass — context coherence over a parallel fleet. If you delegate, ALWAYS review its key findings against the code yourself — NEVER trust a subagent wholesale; verify what Break Down will lean on, re-investigate anything unsupported.

NEVER proceed to Break Down without scope clarified, the codebase explored, and explicit user approval.

### Phase 2: Break Down

On entry, if the confirmed scope involves interface design — read [interface-design](interface-design.md).

Decompose confirmed scope into atomic changes by track:
- `TDD: {name}` — behavioral: one WHAT per atomic change, a verifiable input→output pair, not a HOW. NEVER split a behavior into implementation steps: verify outcome, not process. Each MUST stand alone.
- `Direct: {name}` — non-behavioral: one coherent transformation per atomic change — rename, format, reorganize, config. Each MUST stand alone.

NEVER proceed to Implement without ALL atomic changes listed and explicit user approval.

### Phase 3: Implement

ALWAYS on entry: create one task per atomic change — if any TDD track exists, read [testing-guide](testing.md). 

Process strictly one at a time — NEVER skip, batch, or parallelize. NEVER proceed to next until an atomic change is complete.

**TDD** — strict RED-GREEN cycle:
- RED: write ONE failing test — MUST see it fail
- GREEN: minimal code to pass — NEVER over-implement

**Direct** — implement directly, then run related tests.

NEVER proceed to Verify with unprocessed or failing atomic changes.

### Phase 4: Verify

Run full test suite — all tests MUST pass.

### Phase 5: Polish

ALWAYS read [polish-guide](polish.md) — self-review, optional external review, and fix.

## Finalize

ALWAYS read [finalize-guide](finalize.md) for documentation and commit — after Phase 5 (ad-hoc snap), or after Acceptance in PLAN execution (see [plan-execution](plan-execution.md)).

## Anchor

ALWAYS know who you are — snap delivers atomic changes through TDD or direct tracks, with optional external review.

ALWAYS know where you are — which phase (Investigate → Break Down → Implement → Verify → Polish → then Finalize), which atomic change. If unsure, STOP and re-orient.
