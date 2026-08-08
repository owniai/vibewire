# ADR Format

ADRs live in `.vibewire/adr/` and use sequential numbering: `0001-slug.md`, `0002-slug.md`, etc.

Create the directory lazily — only when the first ADR is needed.

## Template

```md
# {Short title of the decision}

{1-3 sentences: what's the context, what did we decide, and why.}
```

An ADR can be a single paragraph. The value is recording *that* a decision was made and *why*.

## Optional sections

Only when they add genuine value:

- **Status** (`proposed | accepted | deprecated | superseded by ADR-NNNN`)
- **Considered Options** — when rejected alternatives are worth remembering
- **Consequences** — when non-obvious downstream effects need calling out

## Numbering

Scan `.vibewire/adr/` for the highest existing number and increment by one.

## When to offer an ADR

All three must be true:

1. **Hard to reverse** — changing your mind later has meaningful cost
2. **Surprising without context** — a future reader will wonder why it was done this way
3. **Real trade-off** — genuine alternatives existed and one was picked for specific reasons

If any gate fails, skip the ADR.

### What qualifies (examples)

- Architectural shape; integration patterns between contexts
- Technology choices with lock-in (not every library)
- Boundary and ownership decisions; deliberate deviations from the obvious path
- Constraints not visible in the code; non-obvious rejected alternatives
