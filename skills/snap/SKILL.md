---
name: snap
description: "Use ONLY when the user explicitly invokes /vibewire:snap. Deliver atomic changes — TDD for behavior, direct for non-behavioral — with self-review and optional external reviewers."
---

# Snap

One atomic change at a time. TDD for behavior, direct for non-behavioral — with self-review and optional external reviewers.

IMPORTANT: In TDD, test through public interfaces — NEVER implementation details. See [good and bad tests](tests.md) and [when to mock](mocking.md).

## Process

### Phase 1: Orient

Read ONLY these files — all further exploration belongs in Phase 2:

- **`.vibewire/project.md`** — if missing, prompt `/vibewire:intro` first
- **`.vibewire/CHANGELOG.md`** and **`.vibewire/evolve.md`** — scan titles, read on-demand. Skip if absent.

### Phase 2: Break Down

Explore codebase for existing patterns, conventions, test infrastructure, and architectural decisions.

Before writing any code:

- Confirm with user what interface changes are needed
- Confirm with user which behaviors to test — focus on critical paths and complex logic, not every edge case
- Design interfaces for [testability and deep modules](interface-design.md)
- Break confirmed changes into atomic changes — one verifiable outcome each; needs "and" → split
  - `TDD: {name}` — behavioral (e.g. logic, API, bug fix)
  - `Direct: {name}` — non-behavioral (e.g. rename, format, reorganize, config)
- Present list to user — proceed only after approval
- Create one task per atomic change

### Phase 3: Implement

Process one atomic change at a time. For each atomic change, follow the matching track:
- **TDD** (behavioral):
  - RED: write ONE failing test
  - GREEN: write minimal code to pass — NEVER over-implement
- **Direct** (non-behavioral): implement without tests.

After each atomic change: run related tests. DO NOT proceed until they pass.

### Phase 4: Verify

After all atomic changes, run the full test suite. All tests MUST pass.

### Phase 5: Review

Review and fix — see [review guide](review.md).

### Phase 6: Persist

See [persist rules](persist.md) for documentation and commit.

## Anchor

ALWAYS know who you are — snap implements confirmed tasks through atomic changes, with optional external review.

ALWAYS know where you are — which atomic change, which phase (Orient → Break Down → Implement → Verify → Review → Persist). If unsure, STOP and re-orient.
