# Domain Modeling

Actively build and sharpen the project's domain model during grilling. Challenge terms, invent edge-case scenarios, and write the glossary and decisions down the moment they crystallise.

Merely *reading* `.vibewire/CONTEXT.md` for vocabulary is not this discipline — any skill can do that. This file is for when you're **changing** the model.

## Paths (VibeWire)

- Glossary: `.vibewire/CONTEXT.md` — format in [CONTEXT-FORMAT](CONTEXT-FORMAT.md)
- ADRs: `.vibewire/adr/NNNN-slug.md` — format and gates in [ADR-FORMAT](ADR-FORMAT.md)

Create files lazily — only when you have something to write. If no `CONTEXT.md` exists, create it when the first term is resolved. If no `.vibewire/adr/` exists, create it when the first ADR is needed.

If `project.md` **Vibewire artifacts** lists additional locations (e.g. a root `CONTEXT.md`), read those too; **write** new VibeWire terms/ADRs under `.vibewire/` unless the user explicitly directs otherwise.

## During the session

### Challenge against the glossary

When the user uses a term that conflicts with `.vibewire/CONTEXT.md`, call it out immediately.

### Sharpen fuzzy language

When the user uses vague or overloaded terms, propose a precise canonical term.

### Discuss concrete scenarios

When domain relationships are discussed, stress-test them with specific scenarios that force precise boundaries.

### Cross-reference with code

When the user states how something works, check whether the code agrees. Surface contradictions.

### Update CONTEXT.md inline

When a term is resolved, update `.vibewire/CONTEXT.md` immediately — don't batch. Use [CONTEXT-FORMAT](CONTEXT-FORMAT.md).

`CONTEXT.md` must be devoid of implementation details. It is a glossary and nothing else — not a spec, scratch pad, or implementation log.

### Offer ADRs sparingly

Only offer an ADR when all three gates in [ADR-FORMAT](ADR-FORMAT.md) are true. Most sessions produce few or zero ADRs — that is correct.
