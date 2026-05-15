# Persist

`{name}` derives from the task's objective, in kebab-case (e.g., `fix-login-bug`).

## Documentation

- `.vibewire/actions/{name}.md` — sections: Objective (1-2 sentences), Solution (1-3 sentences: approach and why).
- `.vibewire/evolve.md` — append only when a genuinely reusable lesson emerged: short title + root cause + recommendation. Must be a transferable insight that helps future development (e.g., "API rate limits apply per-header, not per-token"), not a task execution record. Keep each entry abstract, concise, and precise — one sentence per field maximum. Skip when no such lesson exists.
- `.vibewire/CHANGELOG.md` — prepend entry: `## YYYY-MM-DD | SNAP-{name}` + what changed.
- `.vibewire/project.md` — update if this task affected project structure or conventions. Skip if no impact.

## Commit

Stage ONLY files changed or produced by this task — source files and updated `.vibewire/` files. Commit message format: `[SNAP-{name}] feat: {one-line description}`.
