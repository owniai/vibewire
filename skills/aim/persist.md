# Persist

`{N}` is the sequence number — scan `.vibewire/aims/AIM-*` filenames, take the highest existing N, increment by 1. First file is `001`. `{name}` is the topic's English identifier in kebab-case (e.g., `auth-strategy`).

## Documentation

- `.vibewire/aims/AIM-{N}-{name}.md` — sections: Objective, Conclusions, Open Questions.

## Commit

Stage ONLY files produced by this task. Commit message format: `[AIM-{N}-{name}] docs: {one-line description}`.
