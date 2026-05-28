# Pattern Comparison Table

| Compare | Choose first when | Choose second when |
| --- | --- | --- |
| Strategy vs State | Algorithm/rule can be swapped by caller or config | Object lifecycle state controls behavior and transitions |
| Adapter vs Facade | Existing interface is incompatible and needs translation | Subsystem is complex and needs a simple entry point |
| Factory vs Strategy | Variation is in object creation | Variation is in behavior after creation |
| Factory Method vs Abstract Factory | One product family member varies | Multiple related products must be created consistently |
| Simple Factory vs Factory Method | A central switch is acceptable and compact | Subclasses/plugins should own creation |
| Bridge vs Strategy | Abstraction and implementation both vary | Only behavior/algorithm varies |
| Decorator vs Proxy | Add responsibilities to an object | Control access to an object |
| Command vs Strategy | Operation itself must be queued, undone, logged, or invoked uniformly | Algorithm varies behind the same operation |
| Mediator vs Observer | Many components need coordinated interaction | One publisher notifies many subscribers |
| Memento vs Command | Need state snapshots | Need operation objects; combine both for undo/redo |
| Template Method vs Strategy | Workflow skeleton fixed by base class | Steps should be replaceable by composition/runtime config |
| Composite vs Decorator | Need tree part-whole structure | Need layered responsibilities around one object |

## Common Pitfalls

- Strategy is not State: Strategy usually has no internal transition graph.
- Adapter is not Facade: Adapter changes interface shape; Facade simplifies orchestration.
- Factory is not always needed: direct construction is fine until creation varies or becomes complex.
- Observer is not always custom code in Qt: signals/slots are often the right implementation.
- Singleton is not dependency injection: global access makes tests and lifetimes harder.
