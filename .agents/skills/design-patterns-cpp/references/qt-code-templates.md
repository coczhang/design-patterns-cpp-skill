# Qt Code Templates

Use these templates as starting points. Adapt names, ownership, error types, and async behavior to the existing project.

## Adapter + Factory for Multi-Vendor SDKs

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

class HikvisionNvrAdapter final : public INvrAdapter {
public:
    explicit HikvisionNvrAdapter(HikvisionSdkConfig config);

    bool login(const NvrLoginInfo& info) override;
    bool startPreview(int channel) override;
    bool stopPreview(int channel) override;
    bool startPlayback(const PlaybackRequest& request) override;
    bool download(const DownloadRequest& request) override;

private:
    HikvisionSdkConfig config_;
    long userId_ = -1;
};

class NvrAdapterFactory {
public:
    static std::unique_ptr<INvrAdapter> create(NvrVendor vendor, const NvrSdkConfig& config);
};
```

Guidance:

- Keep vendor SDK headers out of application-level headers when possible.
- Normalize SDK error codes at the adapter boundary.
- Keep retry, permission, UI, and workflow orchestration outside the adapter.
- Return `std::unique_ptr<INvrAdapter>` for non-QObject adapters.

## Strategy + Factory for Communication Clients

```cpp
class ICommClient : public QObject {
    Q_OBJECT
public:
    explicit ICommClient(QObject* parent = nullptr) : QObject(parent) {}
    ~ICommClient() override = default;

    virtual void connectToHost(const CommEndpoint& endpoint) = 0;
    virtual void disconnectFromHost() = 0;
    virtual void send(QByteArray payload) = 0;

signals:
    void connected();
    void disconnected();
    void dataReceived(QByteArray payload);
    void errorOccurred(QString message);
};

class TcpCommClient final : public ICommClient {
    Q_OBJECT
public:
    explicit TcpCommClient(QObject* parent = nullptr);
    void connectToHost(const CommEndpoint& endpoint) override;
    void disconnectFromHost() override;
    void send(QByteArray payload) override;
};

class CommClientFactory {
public:
    static ICommClient* create(CommProtocol protocol, QObject* parent);
};
```

Guidance:

- For parent-owned QObjects, return raw pointers with a parent or use `QPointer` for non-owning observation.
- Document whether calls are async.
- Use queued connections for cross-thread signals.
- Avoid wrapping parent-owned QObjects in `std::unique_ptr`.

## Command + Memento for Annotation Undo/Redo

```cpp
class IAnnotationCommand {
public:
    virtual ~IAnnotationCommand() = default;
    virtual void execute() = 0;
    virtual void undo() = 0;
};

struct AnnotationSnapshot {
    QVector<AnnotationItem> items;
};

class AddBoxCommand final : public IAnnotationCommand {
public:
    AddBoxCommand(AnnotationModel& model, AnnotationItem item)
        : model_(model), item_(std::move(item)) {}

    void execute() override { id_ = model_.add(item_); }
    void undo() override { model_.remove(id_); }

private:
    AnnotationModel& model_;
    AnnotationItem item_;
    AnnotationId id_{};
};
```

Guidance:

- Prefer small deltas over full snapshots for frequent operations.
- Use snapshots for operations where reconstructing prior state is expensive or error-prone.
- Test command sequences: execute, undo, redo, mixed operations, and edge cases.

## State for Connection Lifecycle

```cpp
class ConnectionContext;

class IConnectionState {
public:
    virtual ~IConnectionState() = default;
    virtual void enter(ConnectionContext& context) {}
    virtual void connect(ConnectionContext& context) = 0;
    virtual void disconnect(ConnectionContext& context) = 0;
    virtual QString name() const = 0;
};

class DisconnectedState final : public IConnectionState {
public:
    void connect(ConnectionContext& context) override;
    void disconnect(ConnectionContext& context) override;
    QString name() const override { return "Disconnected"; }
};

class ConnectionContext {
public:
    void setState(std::unique_ptr<IConnectionState> state);
    void connect();
    void disconnect();

private:
    std::unique_ptr<IConnectionState> state_;
};
```

Guidance:

- Draw valid transitions before adding state classes.
- Keep transition ownership explicit in the context.
- Log invalid transitions.
- For simple two-state logic, prefer an enum and clear branches.

## Mediator for Main Window Coordination

```cpp
class MainWindowMediator : public QObject {
    Q_OBJECT
public:
    explicit MainWindowMediator(QObject* parent = nullptr);

    void bindNavigation(NavigationBar* navigation);
    void bindPages(QStackedWidget* pages);
    void bindStatusPanel(StatusPanel* statusPanel);

public slots:
    void switchPage(PageType page);
    void showStatus(QString message);

private:
    QPointer<NavigationBar> navigation_;
    QPointer<QStackedWidget> pages_;
    QPointer<StatusPanel> statusPanel_;
};
```

Guidance:

- Use `QPointer` for QObject collaborators that are not owned by the mediator.
- Keep local UI behavior inside widgets.
- Split mediator responsibilities by feature when it grows.
