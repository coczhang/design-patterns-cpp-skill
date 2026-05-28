# Qt Threading Architecture

Use this guide when design patterns touch background work, network I/O, SDK callbacks, image processing, decoding, database operations, or UI responsiveness.

## Choose the Right Async Shape

| Need | Prefer |
| --- | --- |
| Long-lived QObject with signals/slots | Worker-object moved to `QThread` |
| Many short CPU tasks | `QThreadPool` / `QRunnable` |
| Simple parallel computation | `QtConcurrent` |
| Ordered background commands | Task queue / Command objects |
| Async network I/O | Qt network classes in their owning thread |
| SDK callbacks from foreign threads | marshal into Qt thread with queued connection |

## Worker-Object Pattern

```cpp
class CaptureWorker : public QObject {
    Q_OBJECT
public slots:
    void start();
    void stop();

signals:
    void frameReady(QImage frame);
    void errorOccurred(QString message);
    void finished();
};
```

Setup:

```cpp
auto* thread = new QThread(parent);
auto* worker = new CaptureWorker();
worker->moveToThread(thread);

QObject::connect(thread, &QThread::started, worker, &CaptureWorker::start);
QObject::connect(worker, &CaptureWorker::finished, thread, &QThread::quit);
QObject::connect(worker, &CaptureWorker::finished, worker, &QObject::deleteLater);
QObject::connect(thread, &QThread::finished, thread, &QObject::deleteLater);
```

## Design Rules

- Never update widgets from non-UI threads.
- Do not subclass `QThread` just to put work in `run()` unless you are intentionally managing an event loop boundary.
- Use queued signals for cross-thread communication.
- State who owns the worker and thread.
- Ensure stop/cancel paths are deterministic.
- Keep SDK callback lifetime and thread context explicit.
- Prefer immutable or value payloads across threads.

## Pattern Mapping

- Command can represent queued background work.
- Strategy can select processing algorithms executed by workers.
- State can model worker lifecycle.
- Observer is usually Qt signals/slots.
- Facade can expose async use-case methods while hiding worker details.

## Common Mistakes

- Direct method calls across thread boundaries.
- Worker deleted from the wrong thread.
- Blocking UI thread with `wait()` or synchronous SDK calls.
- Shared mutable state without synchronization.
- Missing cancellation path.
- Signal payload references objects that die before delivery.

## Review Checklist

Mention thread affinity, connection type, cancellation, ownership, and UI-thread handoff whenever proposing threaded architecture.
