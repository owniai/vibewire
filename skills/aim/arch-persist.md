# Record

## Directory

`.vibewire/actions/PLAN-{N}-{name}/`

- `{N}` — scan `.vibewire/actions/` for existing PLAN directories, take highest N, increment by 1. First is `001`.
- `{name}` — task's English identifier in kebab-case (e.g., `user-auth`)

## Architecture Document

Write to `.vibewire/actions/PLAN-{N}-{name}/architecture.md`. Consolidate all confirmed architecture decisions. Tech decisions MUST cite evidence sources.

**Stage Plan section** — list the atomic change summary, then list each Stage.

## Commit

Stage ONLY files produced by this task. Commit message format: `[PLAN-{N}-{name}] docs: {one-line description}`.

## Next Steps

Output the following prompt for the user to copy and run in a new session:

```
Planning complete. Documents saved to .vibewire/actions/PLAN-{N}-{name}/.

To begin execution, run in a new session:
(code block begin)
/vibewire:go PLAN-{N}-{name}
Stage execution order:
  Stage 1-{name}
  Stage 2-{name}
  ...
(code block end)
```
