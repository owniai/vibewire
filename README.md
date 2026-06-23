# VibeWire

[中文](README.zh-CN.md) | **English**

An autonomous development workflow plugin for Claude Code. From a task description to tested, reviewed code — without human intervention.

VibeWire orchestrates a pipeline of specialized agents that plan, implement, review, and iterate on your behalf. You describe what you want. The agents figure out how.

---

## How It Works

It starts with your project. Run `/vibewire:intro` once to scan the codebase and establish a documentation baseline.

Choose the right skill for your task:

- **Aim** — For exploration, research, discussion, and architecture planning. Aim clarifies requirements, investigates unknowns, and when needed, designs architecture with a checkpoint-based delivery plan. When a task involves new technologies or unverified assumptions, aim dispatches **scout** to investigate and **experimenter** to run real-world experiments — grounding architecture decisions in verified data. If code is needed, aim transitions to snap or produces a PLAN document (architecture + checkpoint plan).
- **Snap** — For well-defined implementation tasks. Snap handles the entire cycle: break down → TDD → verify → optional review → record → commit. Review is recommended for changes spanning 3+ files or affecting public APIs.

All process artifacts live in `.vibewire/` inside your project — architecture, checkpoint plans, implementation records, review reports, and experience logs. Nothing is hidden. You can trace every decision.

---

## Installation

### Claude Code (via Plugin Marketplace)

Register the marketplace first, then install the plugin:

```bash
claude plugins marketplace add https://github.com/owniai/vibewire
claude plugins install vibewire@vibewire
```

---

## Quick Start

```bash
# Step 1: Scan your project (run once)
/vibewire:intro

# Step 2: Choose the right skill for your task
/vibewire:aim   # Explore, clarify, plan architecture if needed
/vibewire:snap  # Direct implementation: break down → TDD → verify → optional review → commit
```

---

## The Workflow

| Command / Skill | Purpose | When to Use |
|-----------------|---------|-------------|
| `/vibewire:intro` | Scan project, establish documentation baseline | Once per project, or when the codebase has changed significantly |
| `/vibewire:aim` | Exploration, clarification, and architecture planning | When you need to explore, analyze, decide, or plan before coding |
| `/vibewire:snap` | TDD implementation with optional review | When you know what to build and want to code it |

```
/vibewire:intro → .vibewire/project.md, .vibewire/CHANGELOG.md

/vibewire:aim (exploration and planning):
  → orient (read project context)
  → clarify (what, why, how)
  → research / discussion / operations / documentation
  → (optional) .vibewire/aims/AIM-{N}-{name}.md
  → if architecture planning needed:
      → explore architecture (layer by layer)
      → scout (if tech unknowns) → .vibewire/tech-research/{task-id}.md + .vibewire/tech-research/knowledge.md
      → experimenter (if unverified assumptions) → .vibewire/experiments/{task-id}/
      → .vibewire/plans/PLAN-{N}-{name}/ (architecture.md + checkpoints.md)
      → (user reviews and approves)
      → hand off to snap: /vibewire:snap PLAN-{N}-{name} (execute checkpoints)
  → or transition to snap directly if the change is simple

/vibewire:snap (implementation):
  → break down → confirm
  → TDD (red → green) per atomic change
  → verify (full test suite)
  → review decision (self-review always; optional 3 reviewers in parallel)
  → fix (deduplicate findings, fix or skip)
  → .vibewire/CHANGELOG.md + .vibewire/evolve.md
  → commit
```

---

## What's Inside

### Commands (1)

| Command | Description |
|---------|-------------|
| **intro** | Scans the codebase, establishes documentation baseline (`.vibewire/project.md`, `.vibewire/CHANGELOG.md`). Five steps: confirm scope → explore → write docs → review → commit |

### Skills (2)

| Skill | Description |
|-------|-------------|
| **aim** | Exploration, clarification, and architecture planning: orient → clarify → research / discussion / architecture design. Produces PLAN documents (architecture + checkpoint plan) |
| **snap** | TDD implementation with optional review: break down → implement → verify → review decision → record → commit |

### Agents (5)

| Agent | Role | Used By |
|-------|------|---------|
| **scout** | Investigates specified technologies and dependencies with factual findings — versions, compatibility, constraints. Receives research targets, no decision-making | aim |
| **experimenter** | Runs specified experiments to obtain real structures, API behaviors, or performance data. Receives experiment targets, no decision-making | aim |
| **efficiency-reviewer** | Reviews for performance issues — unnecessary work, missed concurrency, memory leaks, algorithmic inefficiency | snap |
| **quality-reviewer** | Reviews for anti-patterns — redundant state, parameter creep, copy-paste variants, over-abstraction, code smells | snap |
| **reuse-reviewer** | Reviews for duplication — searches existing utilities and patterns to identify reusable code opportunities | snap |

---

## Architecture

### Process Artifacts

All process artifacts are stored in `.vibewire/` within the target project:

```
.vibewire/
├── project.md                          # Project overview (created by intro)
├── CHANGELOG.md                        # Change log (created by intro)
├── evolve.md                           # Reusable lessons and experience records (maintained by snap)
├── tech-research/                      # Tech research artifacts (created by scout)
│   ├── knowledge.md                    # Global research knowledge base
│   └── {task-id}.md                    # Detailed research per task
├── experiments/
│   ├── framework.md                    # Global experiment framework (created by experimenter)
│   └── {task-id}/                      # Experiment results (created by experimenter)
│       └── result.md
├── plans/                              # Plan records
│   └── PLAN-{N}-{name}/               # Planning directory per plan task
│       ├── architecture.md             # Architecture design (created by aim)
│       └── checkpoints.md              # Delivery checkpoints + status header (created by aim)
└── aims/                               # Aim records (created by aim)
    └── AIM-{N}-{name}.md             # Analysis/research conclusions
```

### Agent Pipeline

```mermaid
flowchart TD
    subgraph aim["/vibewire:aim"]
        direction TB
        A0[User describes task] --> A1[Orient + Clarify]
        A1 --> A2{Code needed?}
        A2 -- No --> A3["Research / Discussion / Operations / Documentation"]
        A2 -- Yes --> A4{Complex or simple?}
        A4 -- Complex, needs planning --> A5{Tech unknowns?}
        A4 -- Simple, HOW clear --> snapflow

        A5 -- Yes --> A6["scout
        Tech investigation"]
        A6 --> A7{Unverified assumptions?}
        A5 -- No --> A7
        A7 -- Yes --> A8["experimenter
        Run real-world experiments"]
        A7 -- No --> A9[Architecture Design]
        A8 --> A9
    end

    A9 --> snapflow

    subgraph snapflow["/vibewire:snap"]
        direction TB
        S0[User confirms scope] --> S1["Break Down → TDD → Verify"]
        S1 --> S2{Review recommended?}
        S2 -- Yes --> S3["3 reviewers (parallel)
        → fix if needed"]
        S2 -- No --> S4[Record → Commit]
        S3 --> S4
    end
```

---

## Philosophy

- **Autonomous by default** — You approve the design. The agents handle the rest.
- **Review before merge** — Three independent reviewers catch different classes of issues. Nothing ships without review.
- **Traceable process** — Every decision, every change, every review is recorded in `.vibewire/`.
- **Fix, don't skip** — Review findings are fixed, not deferred.
- **Scope discipline** — The aim skill pushes back on oversized tasks and helps you ship the smallest useful unit first.

---

## Updating

```bash
claude plugins update vibewire@vibewire
```

---

## Acknowledgments

VibeWire was inspired by [Superpowers](https://github.com/obra/superpowers) by Jesse Vincent. The following patterns and concepts were adapted from Superpowers:

- **Skill format** — Markdown with YAML frontmatter for metadata and structured sections for process, principles, and anti-patterns
- **Design-first workflow** — Requiring user-approved design documents before any implementation begins

## License

MIT License - see LICENSE file for details.
