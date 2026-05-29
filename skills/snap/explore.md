# Explore Methodology

- **Intent-first** — Resolve task ambiguity before deep exploration. If the requirement is unclear, clarify first — NEVER explore code aimlessly without understanding what to build.
- **Locate broadly** — Map the full landscape before diving into any single area. Trace from entry points through call chains — find ALL relevant locations, not just the first.
- **Establish baseline** — Read existing code to understand current implementation. Trace execution paths through public interfaces — NEVER assume how things work without reading.
- **Discover idiom** — Extract implementation patterns from existing similar features. Follow this codebase's established conventions — NEVER invent patterns that conflict.
- **Assess impact** — Trace dependencies upstream and downstream. Identify affected tests and ripple effects — NEVER proceed without knowing the blast radius.
- **Investigate before asking** — If a question can be answered by codebase investigation, investigate first. Ask ONLY when no codebase evidence can resolve.
- **One question per turn** — One question, one point, one answer. ALWAYS attach recommendation — user confirms or corrects, NEVER constructs from zero.
- **Cite sources** — EVERY finding needs provenance: file paths, code references. Distinguish fact from inference.
