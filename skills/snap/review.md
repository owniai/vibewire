# Review

ALWAYS ask user whether to launch external reviewers on entry. Self-review is mandatory — external reviewers run in parallel if approved.

Self-review looks for:
- **Duplication** → Extract function/class
- **Long methods** → Break into private helpers (keep tests on public interface)
- **Shallow modules** → Combine or deepen
- **Feature envy** → Move logic to where data lives
- **Primitive obsession** → Introduce value objects
- **Existing code** the new code reveals as problematic

External reviewers (if approved) — launch three agents in parallel:
- `quality-reviewer`
- `efficiency-reviewer`
- `reuse-reviewer`

## Fix

Merge all findings. Deduplicate by location. For each:
- **Fix** — correctness, security, runtime behavior, or clear code improvement
- **Skip** — fix more disruptive than issue
- **Prioritize** — readability, maintainability, structural clarity, even without behavior change

ALWAYS re-run full test suite after all fixes.
