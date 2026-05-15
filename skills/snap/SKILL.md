---
name: snap
description: "Use ONLY when the user explicitly invokes /vibewire:snap. Implement atomic changes — TDD for behavior, direct for non-behavioral — with self-review and optional external reviewers."
---

# Snap

Implement atomic changes — TDD for behavior, direct for the rest.

## Scope

ALWAYS test through public interfaces — NEVER implementation details.

ALWAYS read [tests](tests.md) and [mocking](mocking.md) before writing any test.

File references: ALWAYS read on phase entry or stated condition — NEVER pre-read.

## Before Start

Load project context — proceed to Explore immediately after:

- `.vibewire/project.md` — if missing, prompt `/vibewire:intro`.
- `.vibewire/CHANGELOG.md` and `.vibewire/evolve.md` — grep titles ONLY, read body on-demand. Skip if absent.

## Process

### Phase 1: Explore

Explore codebase deeply — existing patterns, conventions, and architectural decisions. When uncertain and unable to resolve — ask immediately, NEVER assume. One question per turn — NEVER batch or accumulate.

ALWAYS read [interface-design](interface-design.md) when designing interfaces.

Interactively confirm with user — interface changes and behaviors to test, critical paths only. Proceed to Break Down only after alignment.

### Phase 2: Break Down

Break confirmed scope into atomic changes — one verifiable outcome each — needs "and" → split:

- `TDD: {name}` — behavioral (logic, API, bug fix)
- `Direct: {name}` — non-behavioral (rename, format, reorganize, config)

Present list to user — proceed only after approval — create one task per atomic change.

### Phase 3: Implement

Process atomic changes strictly one at a time — NEVER skip, batch, or parallelize.

**TDD** — strict RED-GREEN cycle:
- RED: write ONE failing test — MUST see it fail
- GREEN: minimal code to pass — NEVER over-implement

**Direct** — implement directly.

After each atomic change — run related tests, NEVER proceed to next until they pass.

### Phase 4: Verify

All atomic changes done — run full test suite, all tests MUST pass.

### Phase 5: Review

ALWAYS read [review](review.md) for review and fix.

### Phase 6: Persist

ALWAYS read [persist](persist.md) for documentation and commit.

## Anchor

ALWAYS know who you are — snap delivers atomic changes through TDD or direct tracks, with optional external review.

ALWAYS know where you are — which phase (Explore → Break Down → Implement → Verify → Review → Persist), which atomic change. If unsure, STOP and re-orient.
