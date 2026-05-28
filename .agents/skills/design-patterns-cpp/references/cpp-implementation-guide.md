# C++17 / Qt Implementation Guide

## Ownership

- Prefer values for simple data and `std::unique_ptr` for exclusive polymorphic ownership.
- Use `std::shared_ptr` only when ownership is genuinely shared.
- Do not put parent-owned QObjects into owning smart pointers.
- For QObjects created with a parent, rely on the QObject tree for destruction.
- For non-QObject resources, use RAII wrappers.
- Prefer clear lifetime boundaries in factories: return `std::unique_ptr<IInterface>` for non-QObjects, or raw/QPointer references when ownership is handled by a QObject parent.

## Interfaces

- Give interfaces virtual destructors.
- Keep interfaces narrow and business-oriented.
- Avoid leaking vendor SDK types through internal interfaces unless the adapter boundary is intentionally thin.
- Prefer `final` on concrete implementations unless inheritance is intended.
- Keep constructors explicit when they accept one argument.

## Qt Integration

- Use `QString`, `QByteArray`, `QImage`, `QVariant`, and QObject signals at Qt boundaries.
- Convert to standard C++ types only when crossing non-Qt domain or library boundaries.
- Prefer queued connections for cross-thread signals.
- State the thread affinity of QObject-based adapters, workers, and communication clients.
- Use `QPointer<T>` when observing a QObject that may be deleted elsewhere.
- Use `QObject::deleteLater()` when deleting QObjects from another thread or while events may still reference them.
- Remember that QObject disables copying; do not use Prototype by copying QObject identity, parent pointers, or signal connections.
- Prefer raw pointers for parent-owned QObject returns, plus clear parent assignment at construction.
- Do not make UI objects global singletons.

## QObject Ownership Patterns

Use these conventions consistently:

| Situation | Prefer |
| --- | --- |
| Non-QObject polymorphic object created by a factory | `std::unique_ptr<IInterface>` |
| QObject owned by a parent | raw pointer returned from factory after parent assignment |
| Non-owning reference to possibly deleted QObject | `QPointer<T>` |
| Shared service with explicit lifetime | dependency injection through constructor or setter |
| Cross-thread worker | QObject moved to thread, controlled by queued signals, deleted with `deleteLater()` |

## CMake and File Layout

When generating code for an existing Qt/CMake project:

- Respect the repository's current directory layout.
- Add headers and sources near related modules.
- Update `CMakeLists.txt` only when required.
- Keep generated examples minimal and directly integrable.

## Threading Notes

- Do not hide thread ownership inside a design pattern.
- For Strategy or Adapter implementations that do I/O, state whether calls are synchronous or asynchronous.
- For QObject workers, define which thread owns the object and how signals cross threads.

## Testing

Patterns should improve testability. Prefer interfaces that let tests inject:

- fake SDK adapters,
- fake communication clients,
- fake image processors,
- in-memory repositories,
- deterministic clocks or ID generators.

If a proposed pattern does not make testing, extension, or coupling better, reconsider it.
