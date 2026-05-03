# Build

Implement feature-level changes through TDD and mandatory three-way review.

## Scope

CRITICAL: Build ONLY implements the confirmed scope — NEVER add features, fix unrelated bugs, or refactor adjacent code. Changes directly required by the confirmed task are always in scope.

IMPORTANT: Disregard all markdown lint warnings.

## Tools

- **peek** (`peek-code:peek` skill) — Powerful code file exploration tool. ALWAYS use for locating definitions and declarations and surveying file structure.
- **scout** — External tech research only: dependency versions, API compatibility, library constraints. For codebase questions use Explore agent + peek instead. Dispatch prompt:
  ```
  TASK_ID: {task-id}
  RESEARCH_TARGETS:
  - {target description}
  ```
- **experimenter** — ALWAYS use when a hypothesis can only be verified by writing and running code (e.g., API behavior, runtime constraints). Dispatch prompt:
  ```
  TASK_ID: {task-id}
  EXPERIMENT_TARGETS:
  - {target description}
  ```
- **Explore agent + peek** — ALWAYS use for codebase exploration that only needs results. When spawning, include in its prompt:
  > Load `peek-code:peek` skill first. Use `peek` to locate definitions and declarations, then read only what you need.

## Approach

- **Clarify HOW** — ALWAYS understand the implementation approach through code exploration before implementing. DO NOT skip to coding.
- **Confirm before coding** — ALWAYS present the implementation plan to the user. Proceed only after confirmation.
- **Delegate mechanical work** — ALWAYS delegate context-free tasks (rename, format, simple substitutions) to subagent.
- **Validate before delegating** — For repetitive work requiring understanding, ALWAYS do one sample first to verify feasibility, then delegate the rest to subagent with the validated pattern.
- **Parallelize orthogonal tasks** — ALWAYS dispatch independent agents in parallel for orthogonal work. DO NOT sequence tasks that have no dependencies.

## Process

### Phase 1: Break Down

Break the task into atomic changes. Present the plan for user confirmation.

**Atomic change** — Delivers one verifiable behavior. If a change needs "and" for unrelated outcomes, split.

Categorize and create tasks:
- **Behavioral** (logic, API, bug fix) → `TDD: {name}-Red` + `TDD: {name}-Green`
- **Structural** (rename, format, reorganize, config) → `Structural: {name}`

Present the list to the user. Proceed only after confirmation.

### Phase 2: Implement

Process one atomic change at a time. DO NOT batch.

**TDD cycle** (tasks ending in `-Red` / `-Green`):
1. **Red** — Write a failing test. Assert behavior, not implementation. Cover edge cases. DO NOT write production code before a failing test exists.
2. **Green** — Write minimal implementation to pass the test.

**Direct** (tasks with `Structural:` prefix):
- Implement without TDD.

After each change: run relevant tests. DO NOT proceed until current tests pass.

### Phase 3: Verify

After all atomic changes, run the full test suite. All tests MUST pass. If tests fail 3 times without an explainable cause, stop and report — DO NOT guess.

### Phase 4: Review

Launch three review subagents in parallel. For each, use the Agent tool with `subagent_type: "vibewire:{reviewer}"`, `description: "{reviewer}"`, `prompt: "MODE: inline\nTASK_GOAL: {one-line task objective}"`:
- `quality-reviewer`
- `efficiency-reviewer`
- `reuse-reviewer`

Wait for all three agents to complete. Collect each reviewer's Status Report.

### Phase 5: Fix

Deduplicate findings across all three reviewers' Status Reports: when the same code location is reported by multiple reviewers, merge into a single entry using the most complete description and best fix.

For each finding, judge whether to fix:
- Fix when the issue affects correctness, security, or runtime behavior
- Fix when the change is minimal and clearly improves the code
- Skip when the fix would be more disruptive than the issue itself
- Skip cosmetic or stylistic issues that don't affect behavior

When fixing: make minimal changes — address the reported issue only, do not refactor surrounding code or introduce new abstractions. Re-run the full test suite after all fixes.

### Phase 6: Record

`{name}` is the task's English identifier in kebab-case (e.g., `add-user-auth`).

- Write `.vibewire/actions/{name}.md` with sections: Objective, Solution, Changes (file path + A/M/D + description).
- If the task produced knowledge worth preserving, synthesize lessons and write to `.vibewire/evolve.md` (append new entries or update existing similar entries). Each lesson MUST include root cause and recommendation. Skip if no lessons.
- Prepend a change entry to `.vibewire/CHANGELOG.md`: date, task name, what changed.
- If this task affected project structure or conventions, update `.vibewire/project.md`. Skip if no impact.

### Phase 7: Commit

```git
git add {source files} .vibewire/actions/{name}.md .vibewire/evolve.md .vibewire/CHANGELOG.md .vibewire/project.md
# If produced:
git add .vibewire/tech-research/ .vibewire/experiments/
git commit -m "[BUILD-{name}] feat: {one-line description}"
```

## Anchor

ALWAYS know who you are — build implements feature-level changes through atomic implementation and mandatory three-way review. No skipping.

ALWAYS know where you are — which atomic change, which phase (Break Down → Implement → Verify → Review → Fix → Record → Commit). If unsure, STOP and re-orient.
