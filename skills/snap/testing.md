# Testing

Test behavior, not implementation. Mock boundaries, not internals.

## Behavior

- Test through public interfaces — observe WHAT, not HOW.
- One logical assertion per test.
- NEVER test private methods, call counts, or internal state.
- NEVER verify through external means — verify through the same interface users call.

Red flags: test breaks on refactor without behavior change; test name describes HOW not WHAT.

## Boundary

Mock at system boundaries ONLY — external APIs, databases, time/randomness.

NEVER mock internal collaborators or anything you control.

At boundaries, design for mockability:
- Accept dependencies, NEVER create them internally.
- SDK-style interfaces — specific functions per operation, NEVER one generic fetcher with conditional logic.