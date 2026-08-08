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

- **Empty project guard** — if no source code files exist (only config, docs, or empty directories), skip remaining exploration and proceed to Phase 3 to create placeholder docs with minimal content (project name, creation date, "empty project — awaiting source code"). Do NOT fabricate architecture or tech stack details.

Scan and understand the codebase from root structure through each dimension：
- **Overview** — extract from existing docs first (README, CLAUDE.md, `package.json` description)
- **Tech stack** — identify via feature files (`package.json`, `Cargo.toml`, `go.mod`, `pyproject.toml`, `pom.xml`)
- **Module architecture** — identify module responsibilities via directory layout and import/require dependencies
- **Coding conventions** — naming style, error handling patterns, test organization, lint config

### Phase 3: Write Docs

Write the following files to `.vibewire/`.

**.vibewire/project.md** structure — prefer markdown headings (`###`, `####`) over deeply nested lists for sub-section hierarchy:

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

## Vibewire artifacts
Paths under `.vibewire/`. After loading this file, follow these pointers:

- **Language**: `.vibewire/CONTEXT.md` — project glossary (created lazily by `/vibewire:aim` when terms crystallise; do not create a placeholder). If the file exists, read it for vocabulary.
- **Decisions**: `.vibewire/adr/` — Architecture Decision Records (created lazily by `/vibewire:aim` when a decision passes the ADR gates; do not create an empty directory). If present, skim titles; read bodies that touch the current work.
- **Changelog**: `.vibewire/CHANGELOG.md` — grep titles ONLY; read body on demand.
- **Evolve**: `.vibewire/evolve.md` — reusable lessons (maintained by `/vibewire:go`). Grep titles ONLY; read body on demand. Skip if absent.
```

If exploration finds an existing glossary or ADR tree elsewhere (e.g. root `CONTEXT.md`, `docs/adr/`), note those paths under Vibewire artifacts as additional locations — still do not copy or scaffold empty VibeWire placeholders.

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
