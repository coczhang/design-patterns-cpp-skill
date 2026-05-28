# Qt Plugin Architecture

Use plugin architecture when vendors, algorithms, protocols, device integrations, import/export formats, or feature packs must be loaded without rebuilding the main application.

## When to Use

- Third-party or vendor modules change independently.
- The application must discover capabilities at runtime.
- Optional features should be deployed separately.
- Multiple teams own different integrations.

Avoid plugins when a static factory is simpler and deployment does not require dynamic loading.

## Core Qt Types

- `QPluginLoader`
- `Q_DECLARE_INTERFACE`
- `Q_PLUGIN_METADATA`
- `Q_INTERFACES`
- JSON plugin metadata

## Interface Sketch

```cpp
class IDetectorPlugin {
public:
    virtual ~IDetectorPlugin() = default;
    virtual QString id() const = 0;
    virtual QString displayName() const = 0;
    virtual std::unique_ptr<IDetector> createDetector(const DetectorConfig& config) = 0;
};

#define IDetectorPlugin_iid "com.example.IDetectorPlugin/1.0"
Q_DECLARE_INTERFACE(IDetectorPlugin, IDetectorPlugin_iid)
```

Implementation:

```cpp
class HikDetectorPlugin final : public QObject, public IDetectorPlugin {
    Q_OBJECT
    Q_PLUGIN_METADATA(IID IDetectorPlugin_iid FILE "hik_detector.json")
    Q_INTERFACES(IDetectorPlugin)
public:
    QString id() const override;
    QString displayName() const override;
    std::unique_ptr<IDetector> createDetector(const DetectorConfig& config) override;
};
```

## Architecture Rules

- Keep plugin interfaces small and stable.
- Use versioned interface IDs or metadata.
- Do not expose vendor SDK types in plugin interfaces.
- Validate plugin metadata before creating objects.
- Treat plugin loading errors as first-class diagnostics.
- Keep plugin object lifetime separate from created service/adapter lifetime.

## Pattern Mapping

- Plugin interface is an Abstract Factory boundary.
- Plugin loader is a Factory/Registry.
- Concrete plugins create Strategy or Adapter implementations.
- Application service or Facade orchestrates plugin-created objects.

## Common Mistakes

- Plugin ABI changes without versioning.
- Main app depends on plugin concrete classes.
- Plugin metadata is ignored until runtime failure.
- Plugin owns QObjects used after unloading.
- Exceptions cross plugin boundaries inconsistently.

## Testing

Test metadata parsing, missing plugin files, incompatible versions, failed `qobject_cast`, duplicate IDs, and plugin unload behavior.
