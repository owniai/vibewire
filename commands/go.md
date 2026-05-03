---
description: Iterative stage-based delivery flow: dispatch specialized agents to implement each stage from architecture through code, then verify via review and acceptance loops.
disable-model-invocation: true
---

# Go

Iterative stage-based delivery flow: dispatch specialized agents to implement each stage from architecture through code, then verify via review and acceptance loops.

## Scope

CRITICAL: Go ONLY dispatches and coordinates — NEVER write code, modify files, or make implementation decisions. If an agent's output is incomplete, retry or fix the input, not the output.

IMPORTANT: NEVER alter, omit, or extend agent prompt templates. Templates are contracts — if insufficient, fix SKILL.md itself.

IMPORTANT: NEVER merge to shared branches without explicit user confirmation.

## Process

### 1. Initialize

Parse `PLAN-{N}-{name}` from user input, then:

- **Verify prerequisites** — `.vibewire/actions/PLAN-{N}-{name}/` MUST exist with both `requirements.md` and `architecture.md`. Missing → prompt user to run `/vibewire:aim`.
- **Create feature branch** — note the current branch as `{original-branch}` (used in §4 Wrap-Up), then run:
  ```
  git checkout -b feature/PLAN-{N}-{name}
  ```

### 2. Stage Loop

Determine the stage list: parse from user input; ONLY read `architecture.md`'s Stage Plan section when the user did not provide a list. Execute the following steps for each stage in order.

#### 2.1 Implementer

```
subagent_type: "vibewire:implementer"
description: "implementer Stage {M}-{name}"
prompt: |
  PLAN_DIRECTORY: .vibewire/actions/PLAN-{N}-{name}/
  STAGE: {M}-{name}
```

Handle by implementer status:
- **DONE** → proceed to §2.2
- **BLOCKED** → append BLOCKED reason to prompt, retry implementer

If still BLOCKED after 2 retries → pause for user intervention:

```
Stage {M}-{name}: failed after retries. See .vibewire/actions/PLAN-{N}-{name}/log.md
```

#### 2.2 Reviewers

After implementer completes, launch all three reviewers simultaneously in a single message. Each uses the same prompt template:

```
subagent_type: "vibewire:{reviewer}"
description: "{reviewer} Stage {M}-{name}"
prompt: |
  PLAN_DIRECTORY: .vibewire/actions/PLAN-{N}-{name}/
  STAGE: {M}-{name}
```

Where `{reviewer}` is one of: `efficiency-reviewer`, `quality-reviewer`, `reuse-reviewer`.

Once all three complete, evaluate their combined output:
- **Any Critical or Major issue found** → proceed to §2.3
- **No Critical or Major issues** → proceed to next stage (§2.1 if stages remain, §3 if all done)

#### 2.3 Resolver

```
subagent_type: "vibewire:resolver"
description: "resolver Stage {M}-{name}"
prompt: |
  PLAN_DIRECTORY: .vibewire/actions/PLAN-{N}-{name}/
  STAGE: {M}-{name}
```

After resolver completes → proceed to next stage (§2.1 if stages remain, §3 if all done).

### 3. Acceptance

After all stages complete, enter the acceptance-fix loop. Initialize `round = 1`, max 3 fix rounds.

#### 3.1 Accept

```
subagent_type: "vibewire:acceptor"
description: "acceptor PLAN-{N}-{name}"
prompt: |
  PLAN_DIRECTORY: .vibewire/actions/PLAN-{N}-{name}/
```

Handle by acceptor verdict:
- **PASS** → proceed to §4 Wrap-Up
- **FAIL** → proceed to §3.2 Fix

#### 3.2 Fix

```
subagent_type: "vibewire:fixer"
description: "fixer PLAN-{N}-{name} round {round}"
prompt: |
  PLAN_DIRECTORY: .vibewire/actions/PLAN-{N}-{name}/
  ROUND: {round}
```

After fixer completes, `round++` and return to §3.1. If `round > 3` → pause for user intervention:

```
PLAN-{N}-{name}: acceptance loop ended with unresolved issues. See .vibewire/actions/PLAN-{N}-{name}/acceptance.md
```

### 4. Wrap-Up

```
subagent_type: "vibewire:evolver"
description: "evolver PLAN-{N}-{name}"
prompt: |
  PLAN_DIRECTORY: .vibewire/actions/PLAN-{N}-{name}/
```

After evolver completes, report overall status, then ask the user how to merge via AskUserQuestion:
- **Merge** — `git checkout {original-branch} && git merge feature/PLAN-{N}-{name}`
- **Squash merge** — `git checkout {original-branch} && git merge --squash feature/PLAN-{N}-{name} && git commit`
- **Create Pull Request** — `gh pr create` targeting `{original-branch}`
- **Keep branch** — no merge, user handles later

Execute the chosen option.

## Anchor

ALWAYS know who you are — go orchestrates stage delivery through agent dispatch. DO NOT implement, review, or fix anything yourself.

ALWAYS know where you are — which stage in the loop, which step (implementer / reviewers / resolver), or whether in acceptance (accept / fix) or wrap-up. If unsure, STOP and re-orient.
