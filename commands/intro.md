---
description: Scan codebase and establish documentation baseline for downstream flows. Five steps: confirm scope → explore → write docs → review → commit.
disable-model-invocation: true
---

# Intro

Scan codebase and establish documentation baseline for downstream flows. Five steps: confirm scope → explore → write docs → review → commit.

## Scope

CRITICAL: Intro ONLY reads and documents — NEVER write, refactor, or modify source code.

CRITICAL: Record ONLY facts directly confirmed from the codebase — NEVER speculate or approximate.

IMPORTANT: Maintain architecture-level overview — NEVER perform deep file-by-file module exploration.

IMPORTANT: NEVER skip modules within the confirmed scan scope.

## Approach

- **Broad-first** — ALWAYS start from root structure. Identify directory layout and feature files before diving into any module.
- **Exclude noise** — Skip dependency directories (e.g. `node_modules`, `vendor`), lock files, and binaries.
- **Parallelize exploration** — ALWAYS dispatch independent Explore agents in parallel when scanning multiple modules.

## Process

### Phase 1: Confirm Scope

- **Check existing docs** — verify whether `.vibewire/project.md` and `.vibewire/CHANGELOG.md` exist. If both exist → ask user whether to update them. If absent or user confirms → proceed.
- **Ensure git repo** — verify the project is a git repository. If not → prompt user before running `git init`.
- **Verify gitignore** — confirm `.vibewire/` is not excluded by `.gitignore`.

### Phase 2: Explore Codebase

Scan and understand the codebase from root structure through each dimension：
- **Overview** — extract from existing docs first (README, CLAUDE.md, `package.json` description)
- **Tech stack** — identify via feature files (`package.json`, `Cargo.toml`, `go.mod`, `pyproject.toml`, `pom.xml`)
- **Module architecture** — identify module responsibilities via directory layout and import/require dependencies
- **Coding conventions** — naming style, error handling patterns, test organization, lint config

### Phase 3: Write Docs

Write the following files to `.vibewire/`.

**.vibewire/project.md** structure:

```markdown
# {Project Name}

> Last updated: yyyy-mm-dd | Intro

## Overview
{2-5 sentence project description based on exploration}

## Directory Structure
{Code directory tree — source, tests, config, and dependency files. Exclude docs, IDE config, CI scripts.}

## Architecture
{Module responsibilities, inter-module dependencies, core data flow}

## Tech Stack
{Language, framework, database, key dependencies with versions}

## Conventions
{Coding standards, directory conventions, common patterns. Omit this section if no notable conventions found.}
```

**.vibewire/CHANGELOG.md** structure:

```markdown
# Changelog

## yyyy-mm-dd | Intro
- {Baseline summary — list major modules and tech stack}
```

### Phase 4: Review Docs

Launch three Explore agents simultaneously in a single message to verify `.vibewire/project.md`. Design appropriate prompts for each agent based on their responsibilities below.

- **Accuracy explorer** — verify all recorded facts (tech stack, dependencies, module descriptions) against actual codebase. Flag any inaccuracy.
- **Completeness explorer** — scan for modules, files, or directories within scope that are missing from the docs. Flag any gap.
- **Conventions explorer** — verify coding conventions described in docs match actual patterns in the codebase (naming, error handling, test structure). Flag any mismatch.

Each explorer reads the docs, investigates the corresponding codebase areas, and reports issues. If any explorer flags issues → fix the docs, then re-verify before proceeding.

### Phase 5: Commit

```bash
git add .vibewire/project.md .vibewire/CHANGELOG.md
git commit -m "[vibewire/intro] docs: init project documentation baseline"
```

## Anchor

ALWAYS know who you are — intro scans and documents project baseline. DO NOT implement, refactor, or make code changes.

ALWAYS know where you are — which phase (Confirm Scope → Explore Codebase → Write Docs → Review Docs → Commit). If unsure, STOP and re-orient.
