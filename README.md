# VibeWire

An autonomous development workflow plugin for Claude Code. From a task description to tested, reviewed code — without human intervention.

VibeWire orchestrates a pipeline of specialized agents that plan, implement, review, and iterate on your behalf. You describe what you want. The agents figure out how.

---

## How It Works

It starts with your project. Run `/vibewire:intro` once to scan the codebase and establish a documentation baseline.

When you have a task, run `/vibewire:aim`. Through a structured conversation, the aim skill clarifies requirements with you, narrows scope if needed, and produces a requirements document and architecture design. When the task involves new technologies or unverified assumptions, aim dispatches **scout** to investigate tech facts and **experimenter** to run real-world experiments — grounding architecture decisions in verified data. You review and approve both before any code is written.

Then run `/vibewire:go`. The go skill dispatches agents through a stage-by-stage pipeline:

1. For each stage, **implementer** reads the architecture, breaks down tasks, and executes with strict TDD
2. Three reviewers — **efficiency**, **quality**, **reuse** — inspect the result in parallel
3. If Critical/Major issues are found, **resolver** consolidates findings and applies minimal fixes
4. After all stages, **acceptor** performs full acceptance verification against requirements
5. If issues are found, **fixer** enters a repair loop (up to 2 rounds)
6. **Evolver** distills lessons learned and records design drift

Each stage is a self-contained loop: implement → review → fix. If an implementer gets blocked, it retries automatically (up to twice before escalating to you). After all stages, acceptance verification ensures requirements are met before merge.

All process artifacts live in `.vibewire/` inside your project — requirements, architecture, stage designs, implementation records, review reports, and experience logs. Nothing is hidden. You can trace every decision.

---

## Installation

### Claude Code (via Plugin Marketplace)

Register the marketplace first, then install the plugin:

```bash
/plugin marketplace add owniai/vibewire
/plugin install vibewire
```

---

## Quick Start

```bash
# Step 1: Scan your project (run once)
/vibewire:intro

# Step 2: Define a task through collaborative dialogue
/vibewire:aim

# Step 3: Execute the plan autonomously
/vibewire:go 001-task-name
```

---

## The Workflow

| Skill | Purpose | When to Use |
|-------|---------|-------------|
| `/vibewire:intro` | Scan project, establish documentation baseline and shadow API files | Once per project, or when the codebase has changed significantly |
| `/vibewire:aim` | Collaborative requirements clarification and architecture design | Before each new feature or change |
| `/vibewire:go` | Autonomous stage-by-stage implementation with review loops | After aim produces requirements and architecture |

```
/vibewire:intro → .vibewire/project.md, .vibewire/CHANGELOG.md, .shadow/

/vibewire:aim → .vibewire/{N}-{name}/requirements.md
              → scout (if tech unknowns) → .vibewire/tech-research.md
              → experimenter (if unverified assumptions) → .vibewire/experiments/{N}-{name}/
              → .vibewire/{N}-{name}/architecture.md
              → (user reviews and approves)

/vibewire:go {N}-{name}
  → for each stage:
      implementer → code + tests (TDD)
      3 reviewers → findings
      resolver → fixes (if Critical/Major)
  → acceptor → acceptance verification
  → fixer → fix loop (if CONDITIONAL, max 2 rounds)
  → evolver → experience log
  → (user chooses how to merge)
```

---

## What's Inside

### Skills (3)

| Skill | Description |
|-------|-------------|
| **intro** | Scans the project, establishes documentation baseline (`.vibewire/project.md`, `.vibewire/CHANGELOG.md`), generates shadow API files |
| **aim** | Collaborative requirement clarification and architecture design. Dispatches scout for tech investigation and experimenter for real-world validation when needed. Produces `requirements.md` and `architecture.md` |
| **go** | Execution orchestrator. Dispatches agents in sequence through planning, implementation, and review stages |

### Agents (11)

| Agent | Role | Used By |
|-------|------|---------|
| **shadow-writer** | Extracts declarations from source files — all definitions without function bodies | intro |
| **scout** | Investigates specified technologies and dependencies with factual findings — versions, compatibility, constraints | aim |
| **experimenter** | Runs specified experiments to obtain real structures, API behaviors, or performance data | aim |
| **implementer** | Reads architecture context, breaks down tasks, executes per-task TDD — writes tests first, then minimal implementation | go |
| **efficiency-reviewer** | Reviews for performance issues — unnecessary work, missed concurrency, memory leaks, algorithmic inefficiency | go |
| **quality-reviewer** | Reviews for anti-patterns — redundant state, parameter creep, copy-paste variants, over-abstraction, code smells | go |
| **reuse-reviewer** | Reviews for duplication — searches existing utilities and patterns to identify reusable code opportunities | go |
| **resolver** | Consolidates review reports from all three reviewers, deduplicates findings, cross-validates issues, executes minimal fixes | go |
| **acceptor** | Post-implementation acceptance agent — verifies requirements traceability and hunts for hidden bugs through adversarial analysis | go |
| **fixer** | Fixes bugs and partial requirements identified during acceptance verification, with TDD approach | go |
| **evolver** | Distills execution experience and design drift from stage outputs, updates project-level documentation | go |

---

## Architecture

### Directory Structure

```
vibewire/
├── .claude-plugin/
│   └── plugin.json           # Plugin metadata
├── agents/                   # 11 specialized agents
│   ├── acceptor.md
│   ├── efficiency-reviewer.md
│   ├── evolver.md
│   ├── experimenter.md
│   ├── fixer.md
│   ├── implementer.md
│   ├── quality-reviewer.md
│   ├── resolver.md
│   ├── reuse-reviewer.md
│   ├── scout.md
│   └── shadow-writer.md
├── skills/                   # 3 workflow skills
│   ├── intro/SKILL.md
│   ├── aim/SKILL.md
│   └── go/SKILL.md
```

### Process Artifacts

All process artifacts are stored in `.vibewire/` within the target project. Shadow API files are stored in `.shadow/` at the project root:

```
.vibewire/
├── project.md                          # Project overview (created by intro)
├── CHANGELOG.md                        # Change log (created by intro)
├── tech-research.md                    # Tech investigation results (created by scout)
├── experiments/{N}-{name}/             # Experiment reports (created by experimenter)
│   └── experiment-report.md
├── evolve.md                           # Cross-milestone experience (created by evolver)
└── {N}-{name}/                         # Planning directory per task
    ├── requirements.md                 # Requirements document (created by aim)
    ├── architecture.md                 # Architecture design with Stage Plan (created by aim)
    ├── log.md                          # Execution log (created by implementer)
    ├── lessons.md                      # Accumulated lessons (created by implementer, resolver, fixer)
    ├── review-efficiency.md            # Efficiency review report
    ├── review-quality.md               # Quality review report
    ├── review-reuse.md                 # Reuse review report
    ├── resolve.md                      # Review adjudication record (created by resolver)
    ├── acceptance.md                   # Acceptance report (created by acceptor)
    └── acceptance-{round}.md           # Archived acceptance reports (created by fixer)

.shadow/                                # API declaration mirrors (created by intro, at project root)
    └── {path/to/source}.{ext}
```

### Agent Pipeline

```mermaid
flowchart TD
    subgraph aim["/vibewire:aim"]
        direction TB
        A1[Requirements Clarification] --> A2{Tech unknowns?}
        A2 -- Yes --> A3["scout
        Tech investigation"]
        A3 --> A4{Unverified assumptions?}
        A2 -- No --> A4
        A4 -- Yes --> A5["experimenter
        Run real-world experiments"]
        A4 -- No --> A6[Architecture Design]
        A5 --> A6
    end

    subgraph go["/vibewire:go"]
        direction TB
        G0[User approves] --> G1["implementer
        TDD implementation"]
        G1 --> G2{Review}

        G2 --> G3["efficiency-reviewer"]
        G2 --> G4["quality-reviewer"]
        G2 --> G5["reuse-reviewer"]

        G3 --> G6{Critical/Major
        issues?}
        G4 --> G6
        G5 --> G6
        G6 -- Yes --> G7["resolver
        Consolidate and fix"]
        G6 -- No --> G8{More stages?}
        G7 --> G8
        G8 -- Yes --> G1
        G8 -- No --> G9["acceptor
        Acceptance verification"]
        G9 -- PASS --> G10["evolver
        Experience synthesis"]
        G9 -- CONDITIONAL --> G11["fixer
        Fix loop"]
        G11 --> G9
        G9 -- FAIL --> G12[User intervention]
        G10 --> G13[User chooses merge strategy]
    end

    aim --> go
```

---

## Philosophy

- **Autonomous by default** — You approve the design. The agents handle the rest.
- **Review before merge** — Three independent reviewers catch different classes of issues. Acceptor verifies every requirement before merge. Nothing ships without review and acceptance.
- **Traceable process** — Every decision, every change, every review is recorded in `.vibewire/`.
- **Fix, don't skip** — Blocked agents trigger automatic rework. Issues are escalated, not ignored.
- **Scope discipline** — The aim skill pushes back on oversized tasks and helps you ship the smallest useful unit first.

---

## Updating

Run `/plugin` in Claude Code, switch to the `Marketplaces` tab, select `vibewire`, then choose `Update marketplace`.

---

## Acknowledgments

VibeWire was inspired by [Superpowers](https://github.com/obra/superpowers) by Jesse Vincent. The following patterns and concepts were adapted from Superpowers:

- **Skill format** — Markdown with YAML frontmatter for metadata and structured sections for process, principles, and anti-patterns
- **Design-first workflow** — Requiring user-approved design documents before any implementation begins

## License

MIT License - see LICENSE file for details.
