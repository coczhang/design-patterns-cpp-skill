# Refactoring Recipes

Before applying any recipe to real code, load `refactoring-safety-checklist.md` and identify tests or logs that preserve existing behavior.

## `if` / `switch` to Strategy + Factory

Use when protocol, algorithm, or rule branches are growing and new branches are likely.

Before:

```cpp
if (protocol == "tcp") {
    startTcp();
} else if (protocol == "udp") {
    startUdp();
} else if (protocol == "serial") {
    startSerial();
}
```

After:

```cpp
class IScanner {
public:
    virtual ~IScanner() = default;
    virtual void start() = 0;
    virtual void stop() = 0;
};

class TcpScanner final : public IScanner { /* ... */ };
class UdpScanner final : public IScanner { /* ... */ };
class SerialScanner final : public IScanner { /* ... */ };

class ScannerFactory {
public:
    static std::unique_ptr<IScanner> create(const QString& type);
};
```

Migration:

1. Extract a common interface from the duplicated branch behavior.
2. Move each branch into a concrete implementation.
3. Keep the old branch entry point and delegate to the factory.
4. Add tests for each concrete implementation.
5. Remove old branch code once behavior matches.

## Multi-Vendor SDK to Adapter + Factory

Use when vendor SDK APIs differ but the application workflow is the same.

```cpp
class INvrAdapter {
public:
    virtual ~INvrAdapter() = default;
    virtual bool login(const NvrLoginInfo& info) = 0;
    virtual bool startPreview(int channel) = 0;
    virtual bool stopPreview(int channel) = 0;
    virtual bool startPlayback(const PlaybackRequest& request) = 0;
    virtual bool download(const DownloadRequest& request) = 0;
};

class NvrAdapterFactory {
public:
    static std::unique_ptr<INvrAdapter> create(NvrVendor vendor);
};
```

Migration:

1. Define the internal interface using application concepts, not vendor names.
2. Wrap one vendor first and keep behavior identical.
3. Normalize errors and result codes at the adapter boundary.
4. Add more vendors behind the same interface.
5. Move orchestration into services or facades, not into adapters.

## UI Interaction to Mediator

Use when navigation, page stack, status panel, dialogs, and business state call each other directly.

```cpp
class MainWindowMediator : public QObject {
    Q_OBJECT
public:
    explicit MainWindowMediator(QObject* parent = nullptr);
    void bindNavigation(NavigationBar* nav);
    void bindPageStack(QStackedWidget* stack);
    void bindStatusPanel(StatusPanel* status);

public slots:
    void switchPage(PageType page);
    void updateStatus(const QString& message);
};
```

Migration:

1. List current direct dependencies among UI components.
2. Move cross-component decisions into mediator slots.
3. Replace direct calls with signals to the mediator.
4. Keep component-local UI logic inside each component.
5. Split mediator by feature if it grows too large.

## Annotation Undo/Redo to Command + Memento

Use when operations must be reversed and reapplied.

1. Define command interface with `execute()` and `undo()`.
2. Store minimal before/after state for each command.
3. Use Memento snapshots only for state that cannot be represented as small deltas.
4. Keep history memory bounded.
5. Test undo/redo sequences, not only single operations.

## Lifecycle Branches to State

Use when the same method has many state checks:

```cpp
if (state == Connected) { ... }
else if (state == Disconnected) { ... }
else if (state == Reconnecting) { ... }
```

Migration:

1. Draw valid states and transitions.
2. Extract behavior that differs by state.
3. Start with one or two state classes for the most complex states.
4. Keep transition ownership explicit in the context object.
5. Add logs for invalid transitions.
