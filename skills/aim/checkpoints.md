# Checkpoints

## Checkpoints

Break the confirmed architecture into delivery Checkpoints. Each Checkpoint leaves the system functional and testable — not an arbitrary bundle, not an implementation step.

- **Runnable** — System MUST be functional and testable after each Checkpoint, independent of later Checkpoints
- **Cohesive** — Group by functional relationship; related work forms one checkpoint
- **Proportionate** — Too granular → merge; too monolithic → split

CRITICAL: Implementation mechanics (install, code, test, docs) are execution flow WITHIN a Checkpoint — NEVER model them as Checkpoints.

## File Format

`checkpoints.md` = a status header, a `---` divider, then each Checkpoint's definition. All Checkpoints start unchecked.

```
- [ ] Checkpoint 1-{name}
- [ ] Checkpoint 2-{name}

---

### Checkpoint {M}-{name}

[one-line description]

- Acceptance criteria: [state the system should reach after completion]
- Module changes:
  - Add `module-name` — [responsibility]
  - Modify `module-name` — [reason]
```
