# Structural Patterns

## Adapter

Intent: convert an incompatible interface into the internal interface the application expects.

Use for NVR SDKs, AI SDKs, payment/device SDKs, third-party libraries, and legacy modules.

Keep adapters thin. Translate types, normalize errors, and hide SDK calls. Do not place business policy in adapters.

## Bridge

Intent: separate abstraction from implementation so both can vary independently.

Use for cross-platform capture/rendering, storage backends, device backends, or rendering APIs.

Avoid when there is only one variation axis.

## Composite

Intent: treat tree nodes and leaves uniformly.

Use for menu trees, device trees, organization trees, permission trees, and scene graph-like domain structures.

Avoid for non-tree data.

## Decorator

Intent: add responsibilities dynamically without modifying the wrapped object.

Use for logging, caching, permission checks, retry, metrics, or image processing layers.

Avoid deep decorator stacks that make debugging hard. Prefer explicit pipeline configuration when order matters.

## Facade

Intent: provide a simple entry point over a complex subsystem.

Use for video playback, NVR workflows, database services, AI recognition modules, and remote desktop orchestration.

Do not let Facade become a god object. It should orchestrate and delegate, not absorb all logic.

## Flyweight

Intent: share repeated fine-grained objects to reduce memory.

Use for large numbers of icons, styles, glyph-like objects, repeated drawing attributes, or shared configuration.

Clearly separate intrinsic shared state from external per-instance state.

## Proxy

Intent: control access to a real object.

Use for lazy loading, cache proxy, permission proxy, remote proxy, or logging proxy.

Keep proxy behavior consistent with the real object. Surprising differences break substitutability.
