# Plan Execution

Execute a PLAN's Checkpoints, one at a time.

## Read the Plan

From `.vibewire/plans/PLAN-{N}-{name}/`:

- `architecture.md` — authoritative. Read per Checkpoint. NEVER override.
- `checkpoints.md` — first unchecked is current; `[x]` = done. Edit ONLY checkboxes — NEVER definitions.
- `log.md` — per-Checkpoint drift + notes. Read if present; later Checkpoints follow it.

## Branch

Branch `plan/PLAN-{N}-{name}`.

- On it → proceed.
- Exists, not on it → clean tree: checkout; dirty tree: STOP. NEVER auto-stash or discard.
- Absent → create from current HEAD (first Checkpoint). NEVER create from `plan/*` unless it is this PLAN.

## Per Checkpoint

**Execute** — Phases 1-5 (Investigate → Polish), scoped to this Checkpoint. Confirm only Checkpoint-scoped decisions.

**Recommend next** — STOP. Highlight what the user should verify for this Checkpoint; wait for confirmation. NEVER AskUserQuestion.

**Wrap up** — after confirmation: record drift + notes in `log.md` (drift = implementation-level deviation from the plan, e.g. a different solution or an API constraint; notes = reusable observation, e.g. a perf quirk). Flip `[ ]` → `[x]`. Commit code + `checkpoints.md` + `log.md`: `[PLAN-{N}-{name}/CP-{M}-{name}] feat: {one-line}`.

All Checkpoints `[x]` → Acceptance.

## Acceptance

Verify every Checkpoint's acceptance criteria against the system and `checkpoints.md` (primary), `architecture.md`, `log.md`. NEVER docs-only.

- **Clean** → Finalize.
- **Requirement gap** → fix, re-verify; loop until clean. Recurring gaps → STOP.
- **Bug / non-requirement** → fix, re-run the test suite, Finalize.

## Finalize

Supplements go's Finalize for PLAN execution:

- One `.vibewire/CHANGELOG.md` entry for the whole plan, on completion only.
- Distill `log.md` notes into `.vibewire/evolve.md`.
- Merge `plan/PLAN-{N}-{name}` — recommend one, user confirms: **Squash** (collapse to one commit), **Merge** (preserve per-Checkpoint commits), or **Defer** (leave unmerged). After Squash or Merge, ask whether to delete or keep the branch.
