# Refactoring Safety Checklist

Use this checklist before proposing or applying design-pattern refactors to real code.

## Pre-Refactor Checks

- Identify the exact behavior that must remain unchanged.
- Locate callers, public APIs, signal/slot connections, and configuration entry points.
- Check whether tests exist. If not, propose characterization tests, logs, or a smaller first step.
- Identify object ownership: value, `std::unique_ptr`, parent-owned QObject, non-owning raw pointer, `QPointer`, or shared ownership.
- Identify thread affinity for QObjects, sockets, workers, callbacks, and timers.
- Check whether CMake, plugin metadata, resource files, or generated MOC files are affected.
- Confirm whether vendor SDK headers leak into business or UI layers.

## Migration Rules

1. Extract interfaces from existing behavior, not from imagined future features.
2. Wrap one concrete implementation first.
3. Keep the old public entry point and delegate internally during migration.
4. Add the factory only after at least two concrete implementations or a real config switch exists.
5. Preserve signal names and emitted payloads unless callers are migrated in the same change.
6. Keep QObject parent assignment explicit.
7. Add logs around newly introduced state transitions, factory selection, or adapter errors.
8. Update CMake only for files actually added.

## Compatibility Notes

Mention these when relevant:

- ABI/API changes.
- Changed include paths.
- Changed object ownership.
- Changed sync/async behavior.
- Changed signal timing or connection type.
- Exception/no-exception policy.
- Error code normalization.
- Configuration key changes.

## Stop Conditions

Stop and recommend a smaller step when:

- Behavior cannot be characterized.
- QObject ownership is unclear.
- Thread ownership is unclear.
- Vendor SDK callback lifetime is unknown.
- The proposed pattern would require broad unrelated rewrites.
- No one can name the next likely implementation or extension.
