# Route

ALWAYS present all routes via AskUserQuestion with a recommendation — fast routing, no deep deliberation, user decides. ALWAYS include the persisted AIM document path if one exists.

- **go** — clear, no design needed. Invoke `/vibewire:go`.
- **plan** — directional, needs architectural planning. Follow [plan](plan.md).
- **delegate: go** — session exhausted or scope demands fresh start. Output copy-ready prompt: `/vibewire:go`, objective, conclusions (or AIM path), further exploration. Conclusions only — never the exploration process.
- **close** — end session without routing to implementation.

## Commit

ALWAYS confirm commit before exiting if any of these were created or modified in this session:

- `.vibewire/aims/AIM-*.md`
- `.vibewire/CONTEXT.md`
- `.vibewire/adr/*`

Stage ONLY those changed files (not unrelated `.vibewire/` noise). Suggested formats:

- AIM: `[AIM-{N}-{name}] docs: {one-line description}`
- CONTEXT / ADR only: `[vibewire/domain] docs: {one-line description}`
- Mixed: prefer the AIM format when an AIM exists; otherwise the domain format.

AIM's responsibility ends here.
