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

Load project context — proceed to Explore immediately after:

- `.vibewire/project.md` — if missing, prompt `/vibewire:intro`.
- `.vibewire/CHANGELOG.md` and `.vibewire/evolve.md` — grep titles ONLY, read body on-demand. Skip if absent.

## Process

### Phase 1: Explore

Explore — investigate first, question on uncertainty, advance on confirmation.
- **Sequential focus** — one checklist item at a time, in order. NEVER touch a later item before the current one is explicitly confirmed and resolved. Silence does NOT count as confirmation.
- **Investigate first, question immediately** — resolve by codebase evidence; ask ONLY when evidence cannot answer, as soon as uncertainty arises. NEVER accumulate questions.
- **One question per turn** — one point, one answer. ALWAYS attach recommendation; user confirms or corrects, NEVER constructs from zero.
- **Cite sources** — EVERY finding needs provenance: file paths, code references. Distinguish fact from inference.

Checklist (strict order):
- [ ] Intent — Resolve task ambiguity before deep exploration.
- [ ] Location — Trace from entry points through call chains — find ALL relevant locations, not just the first.
- [ ] Baseline — Read existing code, trace execution paths through public interfaces — NEVER assume how things work without reading.
- [ ] Idiom — Extract patterns from existing similar features — NEVER invent patterns that conflict.
- [ ] Impact — Trace dependencies upstream and downstream — NEVER proceed without knowing the blast radius.

NEVER proceed to Break Down without all checklist items resolved and explicit user approval.

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

ALWAYS read [polish-guide](polish.md).

### Phase 6: Persist

ALWAYS read [persist-guide](persist.md) for documentation and commit.

## Anchor

ALWAYS know who you are — snap delivers atomic changes through TDD or direct tracks, with optional external review.

ALWAYS know where you are — which phase (Explore → Break Down → Implement → Verify → Polish → Persist), which atomic change. If unsure, STOP and re-orient.
