# Persist

`{name}` derives from the task's objective, in kebab-case (e.g., `fix-login-bug`).

## Documentation

### CHANGELOG.md

Prepend entry:
```
## YYYY-MM-DD | SNAP-{name}
Objective: {1-2 sentences}
What changed: {bullet points}
```

### evolve.md

Prepend ONLY when a genuinely reusable lesson emerged. Must be a transferable insight that helps future development (e.g., "API rate limits apply per-header, not per-token"), not a task execution record. Keep each entry abstract, concise, precise, and generalized — avoid verbose explanation. Skip when no such lesson exists.
```
## {Pattern Title} | SNAP-{name}
- Lesson: {what was learned}
- Action: {what to do differently}
```

### project.md

Update if this task affected project structure or conventions. Skip if no impact.

## Commit

Stage ONLY files changed or produced by this task — source files and updated `.vibewire/` files. Commit message format: `[SNAP-{name}] feat: {one-line description}`.
