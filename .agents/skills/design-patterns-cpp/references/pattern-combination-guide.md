# Pattern Combination Guide

Use pattern combinations only when each pattern solves a distinct pressure point. Do not stack patterns because they often appear together in examples.

## Adapter + Factory

Use when external SDKs or legacy modules expose incompatible APIs and the application must choose an implementation by vendor, platform, config, or plugin.

Typical Qt/C++ cases:

- NVR vendors: Hikvision, Dahua, TP-LINK.
- AI detector SDKs.
- Device SDKs with different login, callback, and error models.

Boundary:

- Adapter translates the vendor API into the internal interface.
- Factory selects and constructs the right adapter.
- Business workflow stays in a service or facade outside the adapter.

Avoid when there is only one SDK and no near-term vendor expansion.

## Strategy + Factory

Use when behavior varies by protocol, algorithm, export format, compression type, scanner input, or image-processing method.

Boundary:

- Strategy owns behavior after creation.
- Factory owns selection and construction.
- Context owns orchestration and lifecycle.

Avoid when there are only two stable branches and no runtime/config switching.

## Command + Memento

Use when operations must support undo/redo, replay, macro recording, or history inspection.

Boundary:

- Command stores the operation and how to undo it.
- Memento stores state snapshots that cannot be reconstructed as small deltas.
- History stack owns command ordering and redo invalidation.

Prefer deltas over full snapshots for frequent annotation or editor operations.

## Facade + Adapter

Use when callers need a simple entry point over a subsystem that includes vendor adapters, decoders, renderers, recorders, download services, or AI modules.

Boundary:

- Facade exposes use-case-level methods.
- Adapters hide vendor or platform-specific APIs.
- Domain services hold business policy.

Avoid when the facade starts owning all logic. Split collaborators before it becomes a god object.

## Mediator + Signals/Slots

Use when Qt UI components need coordination but should not directly call each other.

Boundary:

- Widgets emit intent signals and keep local UI behavior.
- Mediator decides cross-component coordination.
- Signals/slots carry notifications and user intent.

Use `QPointer` for mediator references to UI objects it does not own.

## State + Strategy

Use only when lifecycle state and interchangeable behavior are separate axes.

Example:

- Connection state: disconnected, connecting, connected, reconnecting.
- Transport strategy: TCP, WebSocket, serial.

Boundary:

- State decides what operations are valid in the current lifecycle.
- Strategy performs interchangeable work.

Avoid merging protocol behavior into state classes unless protocol and lifecycle are truly inseparable.

## Combination Checklist

Before recommending a combination, confirm:

1. Each pattern addresses a different variation point.
2. The combined class count is justified by extension or testability.
3. Ownership and thread boundaries are explicit.
4. A simpler single-pattern solution is insufficient.
5. Migration can happen in steps.
