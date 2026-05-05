---
name: aim
description: Use ONLY when the user explicitly invokes /vibewire:aim. Do not auto-trigger based on codebase analysis, feature requests, or perceived implementation needs.
---

# Aim

Lightweight intent triage — focused on clarifying what and why, then route to the right downstream flow (snap / build / plan / vibe).

## Scope

CRITICAL: Aim ONLY routes — NEVER write code, modify files or git state, or make implementation decisions.

IMPORTANT: Stay shallow, ask often — quick iterative clarification over deep analysis. Depth belongs downstream.

IMPORTANT: NEVER read source code.

IMPORTANT: NEVER read downstream flow files (`snap.md`, `build.md`, `plan.md`, `vibe.md`) before routing is decided.

## Process

### Phase 1: Orient

DO NOT read source code. ALWAYS read these files for basic context:
- **`.vibewire/project.md`** and **`.vibewire/CHANGELOG.md`** — If neither exists, prompt user to run `/vibewire:intro`. For CHANGELOG, grep `^##` for title lines only; read full entries on-demand when relevant to the task.
- **`.vibewire/evolve.md`** (if exists) — Known pitfalls relevant to routing.

### Phase 2: Clarify Intent

ALWAYS fully understand the user's task before routing. Clarify two aspects through AskUserQuestion — ask one question at a time, each targeting a single unknown:
- **What** — the outcome: what should be true when done
- **Why** — the context: what drives this and what constrains it

DO NOT explore or clarify **how** — that belongs to downstream flows. DO NOT proceed to Route until both what and why are clear.

### Phase 3: Route to Flow

With what and why clear, quickly identify the recommended flow — DO NOT overthink, the user decides:
- **snap** — HOW clear. Single purpose, no code review needed. Straightforward change (e.g. rename, normalize, format, fix bug).
- **build** — HOW clear. Code review required — even single-module tasks if the change warrants review.
- **plan** — Multi-phase, complex implementation. HOW may or may not be clear — plan handles both.
- **vibe** — Non-code tasks (analysis, research, discussion, operations, documentation), or code tasks where HOW is unclear. Needs discussion or interactive clarification before execution. Vibe clarifies HOW, then transitions to the appropriate execution flow.

ALWAYS present the recommendation via AskUserQuestion with a brief rationale and all four options. Route based on the user's choice.
ALWAYS read the corresponding flow file (in the same directory as this file) and follow its instructions. Aim stops here — the downstream flow owns everything.
- snap → `snap.md`
- build → `build.md`
- plan → `plan.md`
- vibe → `vibe.md`
