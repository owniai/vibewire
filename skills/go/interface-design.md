# Interface Design & Deep Modules

## Designing for Testability

Good interfaces make testing natural:

1. **Accept dependencies, don't create them** — Pass dependencies as parameters rather than instantiating them internally.
2. **Return results, don't produce side effects** — Pure functions returning values are easier to test than procedures mutating state.
3. **Small surface area** — Fewer methods and fewer parameters mean fewer tests and simpler setup.

## Deep Modules

From "A Philosophy of Software Design":

**Deep module** = small interface + lots of implementation

```
┌─────────────────────┐
│   Small Interface   │  ← Few methods, simple params
├─────────────────────┤
│                     │
│                     │
│  Deep Implementation│  ← Complex logic hidden
│                     │
│                     │
└─────────────────────┘
```

**Shallow module** = large interface + little implementation (avoid)

```
┌─────────────────────────────────┐
│       Large Interface           │  ← Many methods, complex params
├─────────────────────────────────┤
│  Thin Implementation            │  ← Just passes through
└─────────────────────────────────┘
```

When designing interfaces, ask:

- Can I reduce the number of methods?
- Can I simplify the parameters?
- Can I hide more complexity inside?
