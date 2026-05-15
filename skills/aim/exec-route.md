# Exec Route

- **snap** — requirement is clear, atomic, no design needed. Invoke `/vibewire:snap`.
- **plan** — requirement is directional but needs architectural planning. Follow [plan](plan.md) flow.
- **delegate: snap** — session context exhausted or scope demands fresh start. Output a copy-ready prompt: `/vibewire:snap` first, then objective, conclusions, blocking open questions. Conclusions only — never the exploration process.

ALWAYS recommend the narrowest option. When snap suffices, do not suggest plan.

ALWAYS include the persisted AIM document path if one exists.

## Commit

If AIM file was created or modified during this session — ask user to confirm commit before routing. Stage ONLY the AIM file. Message format: `[AIM-{N}-{name}] docs: {one-line description}`.

AIM's responsibility ends here — execution is delegated to the next flow.
