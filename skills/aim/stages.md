# Stages

## Atomic Changes

One verifiable outcome per change. Number and classify each:

- `TDD: {name}` — behavioral: logic, API, bug fix, new feature
- `Direct: {name}` — non-behavioral: rename, format, reorganize, config

Split when:
- Description needs "and" for unrelated outcomes
- Change mixes behavioral and non-behavioral work

Keep together when:
- "Because" explains motivation, not a second outcome
- Multiple facets of the same behavior

Cross-cutting concerns (error handling, logging, auth) are attributes of their relevant atomic changes — NEVER standalone.

## Stages

Group atomic changes into delivery Stages. Each Stage is a functional checkpoint — not an arbitrary bundle, not an implementation step.

- **Runnable** — System MUST be functional and testable after each Stage, independent of later Stages
- **Cohesive** — Group by functional relationship; related changes form one checkpoint
- **Proportionate** — Too granular → merge; too monolithic → split

CRITICAL: Implementation mechanics (install, code, test, docs) are execution flow WITHIN a Stage — NEVER model them as Stages.

## Stage Template

```
### Stage {M}-{name}

[one-line description]

- Includes: #{number}, #{number}, ...
- Acceptance criteria: [state the system should reach after completion]
- Module changes:
  - Add `module-name` — [responsibility]
  - Modify `module-name` — [reason]
```
