# Review

ALWAYS self-review. Ask user whether to launch external reviewers — if approved, run both in parallel.

## Self-Review

Look for:
- **Duplication** → Extract function/class
- **Long methods** → Break into private helpers (keep tests on public interface)
- **Shallow modules** → Combine or deepen
- **Feature envy** → Move logic to where data lives
- **Primitive obsession** → Introduce value objects
- **Existing code** the new code reveals as problematic

## External Reviewers

Launch three review agents in parallel:
- `quality-reviewer`
- `efficiency-reviewer`
- `reuse-reviewer`

## Fix

Merge all findings (self-review + external reviewers). Deduplicate: same code location reported by multiple reviewers → merge into one entry. For each:
- Fix when the issue affects correctness, security, or runtime behavior, or when the change is minimal and clearly improves the code
- Skip when the fix would be more disruptive than the issue itself
- Prioritize code quality — fix even without behavior change when readability, maintainability, or structural clarity improves significantly

Re-run full test suite after all fixes.
