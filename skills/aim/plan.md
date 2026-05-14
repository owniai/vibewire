# Plan

Architecture planning flow, triggered after aim convergence.

## Scope

CRITICAL: Plan ONLY produces documents — NEVER write or modify source code.

## Process

### Phase 1: Explore Architecture

Design architecture relative to the existing project, layer by layer. Read [arch-approach](arch-approach.md) first for approach and constraints.

- [ ] Project-level decisions
- [ ] Module decomposition
- [ ] Data flow and interfaces
- [ ] Module internals

### Phase 2: Define Stages

Break confirmed architecture into delivery Stages. Read [stages](stages.md) first for decomposition rules.

- [ ] Decompose into atomic changes
- [ ] Group atomic changes into Stages
- [ ] Present atomic changes and Stages to user — proceed only after approval

### Phase 3: Persist & Hand Off

Read [arch-persist](arch-persist.md) first, then persist confirmed architecture and Stage Plan, and output next-step instructions.

## Anchor

ALWAYS know who you are — plan produces requirements and architecture documents. No source code.

ALWAYS know where you are — which phase (Explore Architecture → Define Stages → Persist & Hand Off), which architecture layer. If unsure, STOP and re-orient.
