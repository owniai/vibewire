---
name: aim
description: Use ONLY when the user explicitly invokes /vibewire:aim. Do not auto-trigger based on codebase analysis, feature requests, or perceived implementation needs.
---

# Aim

Entry-point routing flow. Three steps: orient → clarify intent → route to the right downstream flow (snap / build / plan / vibe).

## Scope

CRITICAL: Aim ONLY routes — NEVER write code, modify files or git state, or make implementation decisions.

IMPORTANT: NEVER read source code.

## Process

### 1. Orientation

DO NOT read source code. ALWAYS read these files for basic context:
- **`.vibewire/project.md`** and **`.vibewire/CHANGELOG.md`** — If neither exists, prompt user to run `/vibewire:intro`.
- **`.vibewire/evolve.md`** (if exists) — Known pitfalls relevant to routing.

### 2. Clarify

ALWAYS fully understand the user's task before routing. Clarify two aspects through AskUserQuestion — ask one question at a time, each targeting a single unknown:
- **What** — desired outcome, scope, deliverables
- **Why** — the problem being solved, constraints, motivation

DO NOT explore or clarify **how** — that belongs to downstream flows. When what or why depends on an unconfirmed technical fact, ALWAYS dispatch scout or experimenter to verify first — DO NOT proceed based on assumptions. DO NOT proceed to Route until both what and why are clear.

### 3. Route

With what and why clear, select the downstream flow based on task characteristics:
- **snap** — Single purpose, no code review needed. Scope can cross modules as long as the change is straightforward (e.g. rename, normalize, format).
- **build** — Code review required. Even a single-module task needs build if the change warrants review.
- **plan** — Large/multi-phase, cross-cutting impact, staged delivery.
- **vibe** — Exploratory or non-code task: analysis, research, discussion, operations.

ALWAYS present all four options with a clear recommendation and rationale. ALWAYS read the corresponding flow file (in the same directory as this file) and follow its instructions — aim's responsibility ends there.
- snap → `snap.md`
- build → `build.md`
- plan → `plan.md`
- vibe → `vibe.md`
