# Finalize

`{IDENTITY}` identifies the work: `GO-{name}` for ad-hoc go (`{name}` = task objective, kebab-case, e.g., `fix-login-bug`), or `PLAN-{N}-{name}` for PLAN execution.

## Documentation

### CHANGELOG.md

Prepend entry:
```
## YYYY-MM-DD | {IDENTITY}
Objective: {1-2 sentences}
What changed: {bullet points}
```

### evolve.md

Prepend ONLY when a genuinely reusable lesson emerged. Must be a transferable insight that helps future development (e.g., "API rate limits apply per-header, not per-token"), not a task execution record. Keep each entry abstract, concise, precise, and generalized — avoid verbose explanation. Skip when no such lesson exists.
```
## {Pattern Title} | {IDENTITY}
- Lesson: {what was learned}
- Action: {what to do differently}
```

### project.md

Update if this task affected project structure or conventions. Skip if no impact.

## Commit

Stage ONLY what this task changed or produced. Commit message format: `[{IDENTITY}] {type}: {one-line description}`.
