# Qt MVP / MVVM Guide

Use this guide when the user asks how to structure Qt UI logic, keep `MainWindow` thin, split UI from business code, or choose between MVC/MVP/MVVM.

## Quick Choice

| UI style | Prefer |
| --- | --- |
| Qt Widgets with forms, dialogs, buttons, and imperative UI | MVP |
| Qt Quick/QML with bindings and observable state | MVVM |
| Simple widget with little logic | Direct widget + service |
| Data-heavy list/table/tree UI | Model/View plus presenter/service |

## MVP for Qt Widgets

Presenter owns screen behavior and calls services. View exposes signals and small UI update methods.

```cpp
class ILoginView {
public:
    virtual ~ILoginView() = default;
    virtual QString userName() const = 0;
    virtual QString password() const = 0;
    virtual void showError(QString message) = 0;
    virtual void showBusy(bool busy) = 0;
};

class LoginPresenter : public QObject {
    Q_OBJECT
public:
    LoginPresenter(ILoginView& view, AuthService& auth, QObject* parent = nullptr);

public slots:
    void onLoginClicked();

private:
    ILoginView& view_;
    AuthService& auth_;
};
```

Use MVP when:

- widgets are becoming controllers,
- tests need to verify screen logic,
- business services should not know widgets.

## MVVM for QML / Binding-Heavy UI

ViewModel exposes properties, commands, and signals.

```cpp
class DeviceViewModel : public QObject {
    Q_OBJECT
    Q_PROPERTY(QString status READ status NOTIFY statusChanged)
    Q_PROPERTY(bool busy READ busy NOTIFY busyChanged)
public:
    QString status() const;
    bool busy() const;

    Q_INVOKABLE void connectDevice();

signals:
    void statusChanged();
    void busyChanged();
};
```

Use MVVM when:

- QML bindings consume observable state,
- state must update automatically,
- UI actions map to commands/invokable methods.

## Boundary Rules

- Views should not include SDK/database/protocol headers.
- Presenters/ViewModels should not do heavy I/O directly.
- Services should not know widget classes.
- Use signals/slots for async results.
- Keep validation close to the presenter/view model when it is screen-specific; keep business rules in domain/application services.

## Common Mistakes

- MainWindow becomes the application service.
- Presenter owns widgets it does not create.
- ViewModel exposes raw infrastructure objects.
- QML calls SDK adapters directly.
- UI tests are the only tests for business workflows.
