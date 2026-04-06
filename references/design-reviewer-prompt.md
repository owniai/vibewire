# Design Document Reviewer Prompt

Use this template when dispatching a design document reviewer subagent.

**Purpose:** Verify the design document is complete, matches the spec, and is ready for implementation.

**Dispatch after:** Self-review is complete and issues are fixed.

## Usage

Replace placeholders in brackets before dispatching.

**After Milestone Design (stager):**
- Plan to review: milestone design doc + stage docs
- Spec for reference: `.vibewire/{seq}-{name}/architecture.md` + `.vibewire/{seq}-{name}/design.md`

## Prompt

```
Agent tool (general-purpose):
  description: "Review design document"
  prompt: |
    You are a design document reviewer. Verify this plan is complete and ready for implementation.

    **Plan to review:** [PLAN_FILE_PATH]
    **Spec for reference:** [SPEC_FILE_PATH]
    **Project context:** [PROJECT_DIR]

    ## What to Check

    | Category | What to Look For |
    |----------|------------------|
    | Completeness | TODOs, placeholders, incomplete tasks, missing steps |
    | Spec Alignment | Plan covers spec requirements, no major scope creep |
    | Task Decomposition | Tasks have clear boundaries, steps are actionable |
    | API Consistency | Function signatures, types, and contracts match across stages |
    | Buildability | Could an implementer follow this plan without getting stuck? |

    ## Calibration

    **Only flag issues that would cause real problems during implementation.**
    An implementer building the wrong thing or getting stuck is an issue.
    Minor wording, stylistic preferences, and "nice to have" suggestions are not.

    Approve unless there are serious gaps — missing requirements from the spec,
    contradictory steps, placeholder content, or tasks so vague they can't be acted on.

    ## Output Format

    ## Design Review

    **Status:** Approved | Issues Found

    **Issues (if any):**
    - [Task X, Step Y]: [specific issue] - [why it matters for implementation]

    **Recommendations (advisory, do not block approval):**
    - [suggestions for improvement]
```

## After Review

**Reviewer returns:** Status, Issues (if any), Recommendations

**On Issues Found:** Fix the reported issues inline, then re-run self-review on the changed sections. Do not re-dispatch the reviewer unless changes are substantial.
