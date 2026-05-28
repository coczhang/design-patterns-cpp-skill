# Creational Patterns

## Singleton

Intent: ensure one instance and provide a global access point.

Use for logging, immutable configuration, or process-wide resources with clear lifetime.

Avoid for normal business objects, UI objects, test-hostile dependencies, and anything that needs multiple instances later.

Qt note: prefer dependency injection or QObject ownership for services in most applications. If using singleton, make initialization and shutdown explicit.

## Simple Factory

Intent: centralize creation by type or configuration.

Use when concrete types vary but creation is simple.

Good for protocols, scanner implementations, vendor adapters, exporters, and processors.

```cpp
class ScannerFactory {
public:
    static std::unique_ptr<IScanner> create(ScannerType type);
};
```

Upgrade to Factory Method or Abstract Factory when creation rules become polymorphic or object families matter.

## Factory Method

Intent: define a creation interface and let subclasses decide concrete products.

Use for plugin-like frameworks, base tasks that create specialized collaborators, and extensible SDK object creation.

Avoid when a simple switch in one factory is stable and easier to read.

## Abstract Factory

Intent: create related object families without exposing concrete classes.

Use for platform families, vendor SDK object sets, UI themes, or device families where products must be consistent.

Avoid when there is no clear product family.

## Builder

Intent: construct complex objects step by step.

Use for complex request objects, report generation, long UI configuration, and objects with optional groups of parameters.

Avoid when the constructor has a few clear parameters.

## Prototype

Intent: create objects by copying an existing object.

Use for annotation templates, graphics items, configuration templates, and repeated object presets.

Qt caution: QObject copy is disabled. For QObject-derived types, prefer `clone()` that creates a new object and copies logical state, not QObject identity or parent pointers.
