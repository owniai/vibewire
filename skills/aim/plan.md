# Plan

Architecture planning flow, triggered after aim convergence.

## Scope

CRITICAL: Plan ONLY produces documents — NEVER write or modify source code.

## Process

### Phase 1: Explore Architecture

Design architecture relative to the existing project, layer by layer.

- [ ] Project-level decisions
- [ ] Module decomposition
- [ ] Data flow and interfaces
- [ ] Module internals

See [arch-approach](arch-approach.md) for approach and constraints.

### Phase 2: Define Stages

Break confirmed architecture into delivery Stages.

- [ ] Decompose into atomic changes
- [ ] Group atomic changes into Stages
- [ ] Present atomic changes and Stages to user — proceed only after approval

See [stages](stages.md) for decomposition rules.

### Phase 3: Persist & Hand Off

Persist confirmed architecture, Stage Plan, and output next-step instructions. See [arch-persist](arch-persist.md).

## Anchor

ALWAYS know who you are — plan produces requirements and architecture documents. No source code.

ALWAYS know where you are — which phase (Explore Architecture → Define Stages → Persist & Hand Off), which architecture layer. If unsure, STOP and re-orient.
