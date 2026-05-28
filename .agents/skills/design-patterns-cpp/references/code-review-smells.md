# Code Review Smells

Use this reference when reviewing C++/Qt code for design-pattern fit. Lead with concrete risks and minimal fixes.

## Pattern Opportunity Smells

| Smell | Possible pattern | Review focus |
| --- | --- | --- |
| Large `switch` or `if-else` over protocol, algorithm, or type | Strategy + Factory | Is the branch set growing or runtime-configurable? |
| Vendor SDK types appear in UI/business code | Adapter | Is there a stable internal interface? |
| Many call sites construct concrete subclasses differently | Factory | Is creation logic duplicated or config-driven? |
| UI widgets call each other directly across modules | Mediator + signals/slots | Can intent signals replace direct coupling? |
| Same method checks many lifecycle states | State | Are invalid transitions hidden in branches? |
| Actions need shortcuts, queues, logs, or undo | Command | Is the operation a first-class object? |
| Undo stores huge full copies for every small edit | Command + delta Memento | Can the command store before/after deltas? |
| Complex subsystem setup repeated across callers | Facade | Can callers depend on a use-case-level entry point? |

## Overengineering Smells

| Smell | Risk | Minimal recommendation |
| --- | --- | --- |
| Interface has one implementation and no named future variant | Noise | Keep concrete class, revisit after extension appears |
| Singleton used for mutable business state | Hidden coupling | Inject dependencies through owner objects |
| Adapter contains workflow and retry policy | Boundary leak | Move policy to service/facade |
| Facade has dozens of unrelated methods | God object | Split by use case or collaborator |
| Mediator handles local widget rendering details | Centralized UI tangle | Keep local behavior inside widgets |
| Factory mirrors constructors without selection logic | Useless indirection | Construct directly |
| State classes duplicate most behavior | Class explosion | Use enum/table-driven transitions |
| Visitor used while element types change often | High maintenance | Prefer direct traversal or virtual operations |

## Qt-Specific Review Smells

- Parent-owned QObject stored in `std::unique_ptr`.
- QObject copied or used in Prototype-style clone without defining logical-state copy.
- Cross-thread QObject calls made directly instead of queued signals.
- `delete` used where `deleteLater()` is safer.
- Long-lived raw QObject pointer held without parent ownership, lifecycle comment, or `QPointer`.
- Signal emitted before object reaches a valid state.
- Factory creates QObject without assigning a parent or documenting ownership transfer.
- Worker object created in one thread but used from another without `moveToThread()`.

## Review Output Shape

For code review, provide:

1. Findings with file/line references when available.
2. Whether a pattern is necessary.
3. Minimal local fix.
4. Larger pattern-based refactor only if justified.
5. Tests or logs needed before changing behavior.
