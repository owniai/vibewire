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

ALWAYS read [explore](explore.md) for methodology.

Explore codebase — investigate first, align on scope and impact through questions.

- [ ] Intent clear
- [ ] Location identified
- [ ] Baseline understood
- [ ] Idiom discovered
- [ ] Impact assessed

NEVER proceed to Break Down without all checklist items resolved and explicit user approval.

### Phase 2: Break Down

From confirmed scope, identify behavioral and non-behavioral units — each unit is one atomic change, one verifiable outcome. Needs "and" → refine behavior, not split implementation.

- `TDD: {name}` — behavioral (logic, API, bug fix)
- `Direct: {name}` — non-behavioral (rename, format, reorganize, config)

ALWAYS read [interface-design](interface-design.md) when an atomic change involves interface design.

Present list to user — ALL atomic changes detailed. NEVER proceed without explicit user approval.

### Phase 3: Implement

ALWAYS create one task per atomic change on entry. Process strictly one at a time — NEVER skip, batch, or parallelize.

**TDD** — strict RED-GREEN cycle:
- RED: write ONE failing test — MUST see it fail
- GREEN: minimal code to pass — NEVER over-implement

**Direct** — implement directly.

After each atomic change — run related tests, NEVER proceed to next until they pass.

NEVER proceed to Verify with unprocessed or failing atomic changes.

### Phase 4: Verify

Run full test suite — all tests MUST pass.

### Phase 5: Review

ALWAYS read [review](review.md) for review and fix.

### Phase 6: Persist

ALWAYS read [persist](persist.md) for documentation and commit.

## Anchor

ALWAYS know who you are — snap delivers atomic changes through TDD or direct tracks, with optional external review.

ALWAYS know where you are — which phase (Explore → Break Down → Implement → Verify → Review → Persist), which atomic change. If unsure, STOP and re-orient.
