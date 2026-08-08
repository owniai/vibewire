# Polish

On entry, ALWAYS ask user whether to launch external reviewers (`quality-reviewer`, `efficiency-reviewer`, `reuse-reviewer`) BEFORE any review activity.

- **Approved** — run self-review AND external reviewers in parallel.
- **Declined** — proceed with self-review only.

## Self-review

Looks for:
- **Duplication** → Extract function/class
- **Long methods** → Break into private helpers (keep tests on public interface)
- **Shallow modules** → Combine or deepen
- **Feature envy** → Move logic to where data lives
- **Primitive obsession** → Introduce value objects
- **Existing code** the new code reveals as problematic

## Fix

Merge all findings (self + external, if any). Deduplicate by location. For each:
- **Fix** — correctness, security, runtime behavior, or clear code improvement
- **Skip** — fix more disruptive than issue
- **Prioritize** — readability, maintainability, structural clarity, even without behavior change

ALWAYS re-run full test suite after all fixes.