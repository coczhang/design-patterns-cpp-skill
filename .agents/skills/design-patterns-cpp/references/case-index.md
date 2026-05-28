# Local Design Pattern Case Index

This reference maps the local Markdown case corpus from the repository `设计模式/` directory into this skill.

Load this file when the user asks for real examples, case studies, blog-style explanations, or wants the skill to use the author's CSDN design-pattern material.

Do not load every case file by default. Load only the specific case file for the pattern being discussed, or the two to three files needed for a comparison.

## Coverage

The local corpus currently contains 22 pattern articles. It covers all GoF patterns except Interpreter. Simple Factory is still covered by the skill's factory guidance and Qt/C++ references, but there is no standalone local case article for it.

Missing local case article:

- Interpreter

## Case Map

| Pattern | Local case file | Use when |
| --- | --- | --- |
| Singleton | `references/cases/singleton-pattern.md` | Explaining singleton lifetime, eager/lazy variants, thread safety, and why not to overuse global business state. |
| Factory Method | `references/cases/factory-method-pattern.md` | Showing factory method and factory-style object creation examples, including Qt-friendly object creation. |
| Abstract Factory | `references/cases/abstract-factory-pattern.md` | Explaining product families and related object creation. |
| Builder | `references/cases/builder-pattern.md` | Building complex objects or UI/form-like configuration step by step. |
| Prototype | `references/cases/prototype-pattern.md` | Cloning objects, prototype registry, and Qt QWidget-like prototype examples. |
| Adapter | `references/cases/adapter-pattern.md` | Translating incompatible interfaces, especially legacy/third-party/Qt-oriented integration. |
| Bridge | `references/cases/bridge-pattern.md` | Separating abstraction from implementation, including rendering/backend variation examples. |
| Composite | `references/cases/composite-pattern.md` | Tree structures, part-whole hierarchies, product boxes, and Qt UI component trees. |
| Decorator | `references/cases/decorator-pattern.md` | Dynamically layering behavior, notification/data processing, or visual decoration. |
| Facade | `references/cases/facade-pattern.md` | Simplifying access to a complex subsystem through one entry point. |
| Flyweight | `references/cases/flyweight-pattern.md` | Sharing intrinsic state for large numbers of small objects, such as game particles or repeated attributes. |
| Proxy | `references/cases/proxy-pattern.md` | Lazy access, permission, cache proxy, remote proxy, and Qt SQL cache proxy examples. |
| Chain of Responsibility | `references/cases/chain-of-responsibility-pattern.md` | Ordered request handling, validation chains, approval flows, and handler pipelines. |
| Command | `references/cases/command-pattern.md` | Encapsulating UI actions, `QAction`/menu commands, undo/redo, and operation history. |
| Iterator | `references/cases/iterator-pattern.md` | Traversing custom collections while hiding storage details. |
| Mediator | `references/cases/mediator-pattern.md` | Reducing direct UI component coupling and coordinating Qt widgets/forms. |
| Memento | `references/cases/memento-pattern.md` | Snapshot/history design, text editor history, and undo state preservation. |
| Observer | `references/cases/observer-pattern.md` | One-to-many notification, with a reminder to prefer Qt signals/slots for QObject code. |
| State | `references/cases/state-pattern.md` | Lifecycle/state-driven behavior, login state, order state, approval workflow, and state machines. |
| Strategy | `references/cases/strategy-pattern.md` | Interchangeable algorithms or policies such as payment, protocol, export, compression, or processing rules. |
| Template Method | `references/cases/template-method-pattern.md` | Fixed workflow skeleton with variable steps, including Qt process examples. |
| Visitor | `references/cases/visitor-pattern.md` | Adding operations over stable object structures, graph/node traversal, and Qt widget visitor examples. |

## Usage Rules

- Prefer the skill's concise reference files for first-pass selection and anti-overengineering checks.
- Load a local case file when the user asks for a concrete example, a teaching explanation, or code inspired by the author's articles.
- When using a local case, adapt it to the user's actual C++/Qt project rather than copying it blindly.
- Preserve the anti-overengineering workflow even if the case article demonstrates a pattern.
- If a local article uses manual Observer but the target code is QObject-based, prefer signals/slots unless the user needs framework-independent code.
- If a local article demonstrates QObject cloning, explicitly discuss QObject copy limitations and clone only logical state.

## Suggested Output Addition

When a local case is used, include a short line:

```text
Local case used: references/cases/<file-name>.md
```

Then explain how the case maps to the user's concrete problem and where it should be adapted for Qt ownership, threading, testing, or project structure.
