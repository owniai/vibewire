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
- Absent → create from current HEAD (first Checkpoint). If on another feature/plan branch (by name), STOP and warn — switch to the intended base first.

## Per Checkpoint

**Execute** — Phases 1-5 (Explore → Polish), scoped to this Checkpoint. Confirm only Checkpoint-scoped decisions. Polish ALWAYS runs external reviewers — no ask-gate in PLAN mode.

**Wrap up** — record drift + notes in `log.md` (drift = implementation-level deviation from the plan, e.g. a different solution or an API constraint; notes = reusable observation, e.g. a perf quirk). Flip `[ ]` → `[x]`. Commit code + `checkpoints.md` + `log.md`: `[PLAN-{N}-{name}/CP-{M}-{name}] feat: {one-line}`.

**Recommend next** — STOP. Highlight what the user should verify for this Checkpoint; wait for confirmation. NEVER AskUserQuestion.

All Checkpoints `[x]` → Acceptance.

## Acceptance

Dispatch three Explore agents in parallel — each independently verifying every Checkpoint's acceptance criteria against `checkpoints.md` (primary), `architecture.md`, `log.md`.

- **Clean** → Finalize.
- **Requirement gap** → fix, re-verify; loop until clean. Recurring gaps → STOP.
- **Bug / non-requirement** → fix, re-run the test suite, Finalize.

## Finalize

Supplements snap's Finalize for PLAN execution:

- One CHANGELOG entry for the whole plan, on completion only.
- Distill `log.md` notes into `evolve.md`.
- Merge `plan/PLAN-{N}-{name}` — recommend one, user confirms: **Squash** (collapse to one commit), **Merge** (preserve per-Checkpoint commits), or **Defer** (leave unmerged). After Squash or Merge, ask whether to delete or keep the branch.
