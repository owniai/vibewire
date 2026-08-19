# Architecture Approach

## Approach

- **Global-first** — Start with the big picture: current architecture, module boundaries, and where the change fits.
- **One layer at a time** — Present one layer, recommend, confirm, next. When trade-offs exist, present 2–3 options and recommend the best. NEVER proceed without confirmation.
- **Cite evidence** — EVERY architecture decision MUST cite supporting evidence: experiment results, tech research, or code analysis. Distinguish facts from inferences.

## Constraints

- **Modular** — Single purpose, clear boundaries, independently testable.
- **Proportionate** — Simple: sentences. Complex: up to 300 words per component. DO NOT over-document.
- **Architecture only** — No implementation code. Module identities, paths, and cross-module types are in scope — MUST be confirmed here.
- **Interface design** — follow [interface-design](../go/interface-design.md).

## Layers

1. **Project-level decisions** — New dependencies or tech stack changes. Propose, justify, confirm. Skip if no changes.
2. **Module decomposition** — Where the change fits. New or modified modules, single responsibilities, paths, dependencies, dependents.
3. **Data flow and interfaces** — Direction, communication patterns, shared cross-module types (annotated with producer/consumer), interfaces to modify (annotated with location/impact).
4. **Module internals** — Per-module internal design, one module at a time.
