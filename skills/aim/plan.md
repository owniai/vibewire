# Plan

Explore architecture, define checkpoints, persist, and route.

## Scope

CRITICAL: ONLY produce documents — NEVER write or modify source code.

Phase references: ALWAYS read on phase entry — NEVER pre-read.

## Process

### Phase 1: Explore Architecture

ALWAYS read [arch-approach](arch-approach.md) for approach and constraints.

Design architecture building on AIM conclusions — strictly one layer at a time, top-down. NEVER skip, batch, or advance before layer confirmed.

- [ ] Project-level decisions
- [ ] Module decomposition
- [ ] Data flow and interfaces
- [ ] Module internals

### Phase 2: Define Checkpoints

ALWAYS read [checkpoints](checkpoints.md) for decomposition rules.

Break confirmed architecture into delivery Checkpoints:

- [ ] Group into delivery Checkpoints (runnable after each)
- [ ] Present to user — ALL checkpoints detailed. NEVER proceed without explicit approval.

### Phase 3: Persist

Persist to `.vibewire/plans/PLAN-{N}-{name}/` (N = next 3-digit sequence from plans/, name = kebab-case task id). Two files:

- **`architecture.md`** — the four architecture layers, all confirmed decisions, tech decisions citing evidence sources. Architecture only.
- **`checkpoints.md`** — a status header (one `- [ ]` per Checkpoint, all unchecked) above a `---` divider, then each Checkpoint's definition per the [format](checkpoints.md).

Commit ONLY these two files: `[PLAN-{N}-{name}] docs: {one-line description}`.

### Phase 4: Route

Recommend executing the checkpoints — output copy-ready `/vibewire:go PLAN-{N}-{name}`.

## Anchor

ALWAYS know who you are — plan produces architecture documents.

ALWAYS know where you are — which phase, which architecture layer. If unsure, STOP and re-orient.
