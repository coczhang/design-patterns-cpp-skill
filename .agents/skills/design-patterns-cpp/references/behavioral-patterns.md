# Behavioral Patterns

## Chain of Responsibility

Use when a request passes through ordered handlers: message filters, permission checks, scan pipelines, validation, or exception handling.

Avoid long opaque chains. Make order visible and test each handler.

## Command

Use when operations need uniform invocation, shortcut/menu binding, queues, logging, macros, or undo/redo.

Qt note: `QAction` can trigger commands, but explicit command objects are most useful when actions exist beyond UI clicks.

## Interpreter

Use for small rule expressions, filter expressions, or tiny DSLs.

Avoid for complex grammars; use a parser library instead.

## Iterator

Use for custom collections or tree traversal.

Avoid rebuilding what standard containers and Qt containers already provide.

## Mediator

Use when UI modules or business objects have tangled many-to-many interaction.

Keep mediator focused. Split by feature if it starts becoming a god object.

## Memento

Use to capture and restore state for undo, form drafts, annotation history, or editor checkpoints.

Watch memory usage for large images or large object graphs. Store deltas when snapshots are too heavy.

## Observer

Use for one-to-many notification.

Qt default: use signals/slots for QObject-based code. Hand-written Observer is mainly useful for non-QObject domain code or framework-independent libraries.

## State

Use when behavior depends on lifecycle state: disconnected, connecting, connected, error, logging in, playing, decoding, reconnecting.

Avoid when there are only two simple states. An enum with clear branches may be enough.

## Strategy

Use for interchangeable algorithms, protocols, compressors, exporters, image processors, or business rules.

Avoid when there are only one or two stable branches and no runtime switching.

## Template Method

Use when the workflow skeleton is fixed but steps vary: import/export, device initialization, data-processing tasks.

Avoid deep inheritance. If steps vary independently at runtime, Strategy may be better.

## Visitor

Use when a stable object structure needs new operations such as export, statistics, validation, or inspection.

Avoid if the object structure changes often; Visitor makes adding new element types more expensive.
