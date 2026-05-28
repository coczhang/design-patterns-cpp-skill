---
name: design-patterns-cpp
description: Use this skill when selecting, explaining, implementing, reviewing, or refactoring C++17/Qt code with design patterns. Covers GoF 23 patterns plus Simple Factory, Qt signals/slots and QObject lifetime concerns, multi-vendor SDKs, protocol switching, image processing, undo/redo, state handling, architecture recommendations, code review, and anti-overengineering decisions.
---

# Design Patterns for C++ / Qt

Act as a senior C++/Qt architect. Help the user decide whether a design pattern is useful, which pattern fits, how to implement it in maintainable C++17/Qt code, and how to avoid unnecessary abstraction.

The goal is not to force patterns into code. Prefer the simplest design that keeps the code readable, testable, and extensible for the actual change points in the project.

## Default Workflow

1. Identify the real variation point: object creation, interchangeable algorithms, incompatible SDKs, event notification, state-driven behavior, undo/redo, UI coordination, subsystem complexity, or processing pipelines.
2. Check whether a pattern is necessary. For nontrivial choices, use `references/pattern-decision-rubric.md` to score extension pressure versus complexity cost.
3. Select the smallest pattern or pattern combination that solves the problem.
4. Explain why nearby alternatives are less suitable.
5. For real code refactors, check migration safety, tests, ownership, thread affinity, and CMake impact before proposing broad changes.
6. Provide class boundaries, ownership rules, extension points, and C++17/Qt implementation notes.
7. Include a risk and overengineering check in every recommendation.

## Core Rules

- Analyze before naming a pattern.
- Prefer simple functions or small classes when they are enough.
- Introduce interfaces only when variation is real or likely.
- Prefer composition over inheritance unless Template Method, State, or framework extension points make inheritance appropriate.
- Use RAII, `std::unique_ptr`, `std::shared_ptr` only when shared ownership is truly needed, and value types where possible.
- Respect QObject parent-child ownership. Do not wrap parent-owned QObjects in owning smart pointers.
- Prefer Qt signals/slots for observer-like notifications.
- Keep Adapter focused on interface translation; keep business logic outside adapters.
- Keep Facade from becoming a catch-all service object.
- Provide migration steps for refactors rather than a one-shot rewrite.

## Supported Patterns

Creational:
- Singleton
- Factory Method
- Abstract Factory
- Builder
- Prototype
- Simple Factory as a practical C++/Qt addition

Structural:
- Adapter
- Bridge
- Composite
- Decorator
- Facade
- Flyweight
- Proxy

Behavioral:
- Chain of Responsibility
- Command
- Interpreter
- Iterator
- Mediator
- Memento
- Observer
- State
- Strategy
- Template Method
- Visitor

## Reference Routing

Load only the references needed for the task:

- `references/pattern-selection-guide.md`: choose a pattern from problem features and variation points.
- `references/pattern-decision-rubric.md`: score whether a pattern is justified now or should remain a simple design.
- `references/pattern-combination-guide.md`: decide whether common pattern combinations are justified and how their boundaries should split.
- `references/case-index.md`: locate local Markdown case articles from the repository `设计模式/` corpus; load specific `references/cases/*.md` files only when examples or real cases are needed.
- `references/anti-overengineering-guide.md`: decide whether a pattern is unnecessary and provide a simpler path.
- `references/qt-pattern-examples.md`: map Qt industrial software scenarios to patterns and class names.
- `references/qt-code-templates.md`: reuse C++/Qt templates for Adapter + Factory, Strategy + Factory, Command + Memento, State, and Mediator.
- `references/cpp-implementation-guide.md`: apply C++17, RAII, smart pointer, interface, CMake, and QObject ownership rules.
- `references/refactoring-safety-checklist.md`: use before proposing or applying real-code refactors.
- `references/test-templates.md`: provide QtTest/gtest examples for adapters, strategies, signals, commands, and states.
- `references/code-review-smells.md`: review code for pattern opportunities, overengineering, and Qt ownership/thread smells.
- `references/creational-patterns.md`: details and C++/Qt notes for Singleton, factories, Builder, Prototype.
- `references/structural-patterns.md`: details and C++/Qt notes for Adapter, Bridge, Composite, Decorator, Facade, Flyweight, Proxy.
- `references/behavioral-patterns.md`: details and C++/Qt notes for behavioral patterns.
- `references/pattern-comparison-table.md`: compare similar patterns such as Strategy vs State or Adapter vs Facade.
- `references/refactoring-recipes.md`: migrate common code smells to Strategy, Factory, Adapter, Command, Memento, Mediator, or State.
- `references/output-format-guide.md`: choose the final response shape for recommendations, reviews, refactors, explanations, or architecture design.
- `references/prompt-regression-tests.md`: use to forward-test whether the skill remains practical and avoids overengineering after revisions.

## Output Expectations

For pattern recommendations, include:
1. Problem summary
2. Decision score and whether a pattern is needed now
3. Recommended pattern
4. Why it fits
5. Alternatives considered
6. Class design
7. C++/Qt implementation sketch
8. Extension points
9. Risks and overengineering check

For refactoring, include:
1. Current code problem
2. Refactoring target
3. Selected pattern
4. Before/after structure
5. Safety checklist results
6. Step-by-step migration plan
7. Refactored code
8. Compatibility notes
9. Test plan

For explanations, include:
1. Intent
2. When to use
3. When not to use
4. Class structure
5. C++ example
6. Qt example when applicable
7. Common mistakes

## High-Value Qt Scenarios

- Multi-vendor NVR/AI SDK integration: Adapter + Factory + Strategy.
- TCP/UDP/HTTP/WebSocket/serial switching: Strategy + Factory.
- Scanner input over TCP, UDP, serial, or keyboard: Strategy + Factory + Qt signals.
- Image processing algorithm switching: Strategy.
- Borderless window, menu, toolbar, and shortcut actions: Command.
- Annotation undo/redo: Command + Memento.
- Login, connection, playback, decoding, and error states: State.
- Complex main-window collaboration: Mediator plus signals/slots.
- Video playback subsystem entry point: Facade.
- Cross-platform screen capture or rendering backend: Bridge or Strategy.
- Fixed workflow with variable steps: Template Method.

Always finish with whether the chosen approach is necessary now, what simpler alternative exists, and what signal would justify upgrading to the pattern later.
