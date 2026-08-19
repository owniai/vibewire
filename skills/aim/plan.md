# Plan

Explore architecture, define checkpoints, and route.

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

After all layers confirmed — write `.vibewire/plans/PLAN-{N}-{name}/architecture.md` (N = next 3-digit sequence from `.vibewire/plans/`, name = kebab-case task id): the four layers, all confirmed decisions, tech decisions citing evidence sources. Architecture only.

### Phase 2: Define Checkpoints

ALWAYS read [checkpoints](checkpoints.md) for decomposition rules.

Break confirmed architecture into delivery Checkpoints:

- [ ] Group into delivery Checkpoints (runnable after each)
- [ ] Present to user — ALL checkpoints detailed. NEVER proceed without explicit approval.

After approval — write `checkpoints.md` in that directory: a status header (one `- [ ]` per Checkpoint, all unchecked) above a `---` divider, then each Checkpoint's definition per the [format](checkpoints.md).

### Phase 3: Route

ALWAYS state the recommendation — execute the checkpoints. User confirms this session or a new one.

- This session — invoke `/vibewire:go PLAN-{N}-{name}`
- New session — output copy-ready prompt: `/vibewire:go PLAN-{N}-{name}`, AIM path if persisted, otherwise a tight digest of the settled spec. Never the exploration process.

ALWAYS ask whether to commit, with a recommendation — user decides. Recommend yes when this session created or modified files. Commit ONLY those files.

## Anchor

ALWAYS know who you are — plan produces architecture documents.

ALWAYS know where you are — which phase, which architecture layer. If unsure, STOP and re-orient.
