# Design

## Approach

- **Global-first** — Start with the big picture: current architecture, module boundaries, and where the change fits.
- **One layer at a time** — Present one layer, recommend, confirm, next. When trade-offs exist, present 2–3 options and recommend the best. NEVER proceed without confirmation.
- **Cite evidence** — EVERY architecture decision MUST cite supporting evidence: experiment results, tech research, or code analysis. Distinguish facts from inferences.

## Constraints

- **Modular** — Single purpose, clear boundaries, independently testable.
- **Proportionate** — Simple: sentences. Complex: up to 300 words per component. DO NOT over-document.
- **Architecture only** — No implementation details or code. Cross-module type definitions are the exception — MUST be confirmed here.

## Layers

1. **Project-level decisions** — New dependencies or tech stack changes. Propose, justify, confirm. Skip if no changes.
2. **Module decomposition** — Where the change fits. New or modified modules, single responsibilities, paths, dependencies, dependents.
3. **Data flow and interfaces** — Direction, communication patterns, shared cross-module types (annotated with producer/consumer), interfaces to modify (annotated with location/impact).
4. **Module internals** — Per-module internal design, one module at a time.

## Interface Design & Deep Modules

### Designing for Testability

Good interfaces make testing natural:

1. **Accept dependencies, don't create them**

   ```typescript
   // Testable
   function processOrder(order, paymentGateway) {}

   // Hard to test
   function processOrder(order) {
     const gateway = new StripeGateway();
   }
   ```

2. **Return results, don't produce side effects**

   ```typescript
   // Testable
   function calculateDiscount(cart): Discount {}

   // Hard to test
   function applyDiscount(cart): void {
     cart.total -= discount;
   }
   ```

3. **Small surface area**
   - Fewer methods = fewer tests needed
   - Fewer params = simpler test setup

### Deep Modules

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
