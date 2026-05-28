# Qt Layered Architecture

Use layered architecture when UI, business workflows, SDK integrations, communication, storage, and algorithms need clear ownership and testable seams.

## Recommended Layers

| Layer | Responsibility | Should depend on |
| --- | --- | --- |
| UI | Widgets/QML pages, user interaction, rendering state | Application services, presenters/view models |
| Presentation | Screen logic, validation, UI state mapping | Application services, domain DTOs |
| Application service | Use-case orchestration, transactions, workflow decisions | Domain interfaces, repositories, adapters |
| Domain | Business concepts, rules, value types, pure interfaces | Nothing Qt-heavy when practical |
| Infrastructure | SDKs, network, database, file system, OS APIs | Domain/application interfaces |
| Cross-cutting | logging, config, metrics, errors | Narrow shared interfaces |

## Dependency Rule

Dependencies point inward:

```text
UI -> Presentation -> Application -> Domain
                         |
                         v
                   Infrastructure implements interfaces
```

Do not let UI include vendor SDK headers, SQL query details, socket protocol parsing, or file-format specifics.

## Qt-Specific Boundary Rules

- UI can use QWidget/QML types freely.
- Application services may be QObject when signals, thread affinity, or Qt lifetime management are useful.
- Domain value types should prefer simple C++ or lightweight Qt value types (`QString`, `QDateTime`, `QByteArray`) when the whole project is Qt-based.
- Infrastructure adapters may own SDK handles, sockets, SQL connections, and worker objects.
- Long-running infrastructure work should expose async signals or futures instead of blocking UI.

## Typical Module Split

```text
app/
  main.cpp
ui/
  main_window/
  widgets/
presentation/
  presenters/
  viewmodels/
application/
  services/
domain/
  models/
  interfaces/
infrastructure/
  sdk_adapters/
  comm/
  storage/
```

## Service + Facade Guidance

Use application services as use-case APIs:

```cpp
class DeviceApplicationService : public QObject {
    Q_OBJECT
public:
    explicit DeviceApplicationService(std::unique_ptr<IDeviceAdapter> adapter,
                                      QObject* parent = nullptr);

public slots:
    void login(DeviceLoginRequest request);
    void startPreview(int channel);

signals:
    void loginSucceeded();
    void loginFailed(QString message);
    void previewStarted(int channel);
};
```

Keep services focused on a use case or bounded context. If one service begins managing every workflow, split it.

## Overengineering Check

Use fewer layers when the project is small:

- UI + service is often enough for simple tools.
- Add repositories only when storage logic grows or tests need substitution.
- Add domain layer when business rules deserve isolation.
- Add plugin/infrastructure modules when implementations vary.
