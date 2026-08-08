# Synthesize

Turn the aligned conversation into a durable spec-style AIM — or skip persistence and proceed to Route.

## Gate

ALWAYS ask on entry (recommendation: persist when the work may span sessions or hand off to go/plan):

- **Summarize** — write `.vibewire/aims/AIM-{N}-{name}.md` (N = next 3-digit sequence from `aims/`, name = kebab-case topic)
- **Skip** — do not write an AIM; proceed to Route. CONTEXT/ADR already written during Grill stay as-is.

There is no "output directly to chat only" option.

Do NOT interview for new decisions here — synthesize what is already known. Use vocabulary from `.vibewire/CONTEXT.md` when present; respect ADRs in the area.

## Before writing (Summarize only)

Sketch the **seams** at which the feature will be tested. Prefer existing seams; take the highest seam possible; fewer is better — ideal is one. Confirm seams with the user before writing the AIM.

## AIM template

```markdown
# AIM-{N}-{name}

## Problem Statement

The problem from the user's perspective.

## Solution

The solution from the user's perspective.

## User Stories

A LONG, numbered list covering all aspects of the feature. Each story:

1. As an <actor>, I want a <feature>, so that <benefit>

## Implementation Decisions

- Modules to build/modify and their interfaces
- Technical clarifications, architectural choices, schema/API contracts, interactions
- Do NOT include specific file paths or code snippets (they go stale fast)
- Exception: a prototype snippet that encodes a decision more precisely than prose — trim to the decision-rich parts and note the source

## Testing Decisions

- What makes a good test here (external behavior through public interfaces, not implementation details)
- Agreed seams under test
- Modules under test; prior art for similar tests in the codebase

## Out of Scope

What this AIM deliberately excludes.

## Further Notes

Open questions, further exploration, cited sources (paths, URLs, experiments). Distinguish fact from inference.
```

Every decision in the AIM must have been settled in Grill (or clearly marked as open under Further Notes). Anything asserted that was never agreed is a defect.

## After writing

If Summarize ran, note the AIM path for Route. Commit of AIM / CONTEXT / ADR is handled in Route's Commit step (user confirms).
