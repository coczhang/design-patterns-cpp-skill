# Pattern Selection Guide

Before selecting a pattern, answer:

1. What changes frequently?
2. What should remain stable?
3. Is the current code already hard to maintain?
4. How many implementations exist now?
5. How many implementations are likely later?
6. Is runtime switching required?
7. Is object creation logic complicated?
8. Are external SDK interfaces inconsistent?
9. Is undo/redo, replay, or state recovery required?
10. Would a simpler solution be enough?

For nontrivial choices, also load `pattern-decision-rubric.md` and score the decision before recommending a pattern.

When more than one pattern seems useful, load `pattern-combination-guide.md` and verify that each pattern addresses a distinct variation point.

## Selection Table

| Problem feature | Prefer |
| --- | --- |
| Multiple object types are created and new types will be added | Simple Factory or Factory Method |
| A family of related objects must be created together | Abstract Factory |
| Construction has many parameters or ordered steps | Builder |
| Existing objects need to be copied as templates | Prototype |
| Third-party interfaces are incompatible | Adapter |
| Abstraction and implementation vary independently | Bridge |
| Tree nodes and leaves need uniform handling | Composite |
| Responsibilities need to be stacked dynamically | Decorator |
| A complex subsystem needs a simple entry point | Facade |
| Access needs lazy loading, caching, permission checks, or remote indirection | Proxy |
| A request passes through ordered handlers | Chain of Responsibility |
| Operations need queuing, logging, shortcuts, menu binding, or undo/redo | Command |
| Many UI/business objects interact in tangled ways | Mediator |
| State must be captured and restored | Memento |
| One event must notify multiple receivers | Observer or Qt signals/slots |
| Object behavior changes by state | State |
| Algorithms, protocols, or rules are interchangeable | Strategy |
| Workflow skeleton is fixed but steps vary | Template Method |
| New operations are needed over a stable object structure | Visitor |

## Practical Decision Rules

- Use Strategy when the user can name interchangeable algorithms or protocols.
- Use State when the same public operation behaves differently depending on lifecycle state.
- Use Adapter when third-party APIs differ but the application needs one internal interface.
- Use Facade when callers should not know a subsystem's orchestration details.
- Use Factory when callers should not know concrete construction rules.
- Use Command when an action should be invoked uniformly by UI, shortcuts, queues, logs, macros, or undo stacks.
- Use Qt signals/slots before hand-written Observer for QObject-based notifications.
- Use Bridge when both platform/backend implementation and high-level abstraction vary.

If the current code has only one stable implementation and no clear extension pressure, recommend a simple direct design and name the future signal that would justify adding a pattern.
