# Anti-Overengineering Guide

Do not recommend a pattern automatically. Every answer should include whether the pattern is necessary now.

## Avoid Patterns When

- There is only one implementation and no clear extension point.
- A simple function or small class solves the problem.
- The abstraction adds more classes than clarity.
- The team will not maintain the extra structure.
- Requirements are unclear.
- The current code is simple and unlikely to change.
- There are only two stable branches.
- Tests, logs, or reproducible behavior are missing for a risky refactor.
- Qt signals/slots already solve notification cleanly.

## Prefer Simpler Alternatives

| Situation | Simpler path |
| --- | --- |
| One implementation | Concrete class with a clear public API |
| Two stable branches | Keep `if`/`switch`, extract helper functions if needed |
| Small object creation logic | Static factory function or direct constructor |
| Simple event notification in Qt | `signals` and `slots` |
| One-off workflow | Plain function sequence |
| Shared utility behavior | Free function or composition helper |

## Upgrade Signals

Introduce a pattern when:

- A third or fourth implementation appears.
- The branching logic changes often.
- Tests repeatedly need to substitute implementations.
- Different modules need a stable interface over changing implementations.
- Runtime switching becomes a real feature.
- Undo/redo, logging, replay, or command queues become required.
- State transitions grow enough that branch-based logic hides invalid transitions.

## Common Pattern Misuses

| Misuse | Why it hurts | Prefer |
| --- | --- | --- |
| Singleton for normal business services | Hides dependencies and complicates tests | Constructor injection or explicit service owner |
| Strategy with only one implementation | Adds interface noise without flexibility | Concrete class with clear methods |
| Factory for trivial construction | Centralizes code that does not vary | Direct constructor or small helper function |
| Adapter with business policy inside | Vendor translation and business workflow become tangled | Thin adapter plus separate service/facade |
| Facade that owns all logic | Turns into a god object | Facade orchestrates, collaborators implement |
| Mediator that handles every UI decision | Recreates tight coupling in one class | Split mediator by feature or keep local widget logic local |
| Custom Observer in QObject code | Duplicates Qt's event model | Signals/slots |
| Deep inheritance just for reuse | Creates brittle hierarchy | Composition helper or Strategy |
| State for two simple states | Too many classes for little gain | Enum with explicit checks |
| Visitor over changing element types | Adding element types becomes expensive | Virtual methods, Strategy, or direct traversal |

## Required Answer Shape

When a pattern may be excessive, provide:

1. Simple solution for today.
2. Pattern-based solution for later.
3. Trigger for upgrading.
4. Migration path that avoids a disruptive rewrite.
