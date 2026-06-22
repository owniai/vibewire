# Record

## Directory

`.vibewire/plans/PLAN-{N}-{name}/`

- `{N}` — scan `.vibewire/plans/` for existing PLAN directories, take highest N, increment by 1. First is `001`.
- `{name}` — task's English identifier in kebab-case (e.g., `user-auth`)

## Architecture Document

Write to `.vibewire/plans/PLAN-{N}-{name}/architecture.md`. Consolidate all confirmed architecture decisions. Tech decisions MUST cite evidence sources.

**Stage Plan section** — list the atomic change summary, then list each Stage.

## Commit

Stage ONLY files produced by this task. Commit message format: `[PLAN-{N}-{name}] docs: {one-line description}`.

## Next Steps

Output the following for the user:

```
Planning complete. Documents saved to .vibewire/plans/PLAN-{N}-{name}/.
Architecture and Stage Plan are ready for implementation.
```
