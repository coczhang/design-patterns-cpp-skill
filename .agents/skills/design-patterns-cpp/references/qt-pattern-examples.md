# Qt Pattern Examples

Use these mappings for C++/Qt desktop clients, industrial software, multi-vendor SDKs, network communication, image processing, video, and remote desktop systems.

| Qt / C++ scenario | Recommended pattern | Suggested classes |
| --- | --- | --- |
| Multi-vendor NVR SDK integration | Adapter + Factory | `INvrAdapter`, `DahuaNvrAdapter`, `HikvisionNvrAdapter`, `TpLinkNvrAdapter`, `NvrAdapterFactory` |
| AI recognition SDK integration | Adapter + Strategy + Factory | `IAiDetector`, `VendorDetectorAdapter`, `DetectorFactory` |
| TCP/UDP/HTTP/WebSocket communication | Strategy + Factory | `ICommClient`, `TcpClient`, `UdpClient`, `HttpClient`, `WebSocketClient`, `CommClientFactory` |
| Scanner over TCP/UDP/serial/keyboard | Strategy + Factory + Observer | `IScanner`, `TcpScanner`, `SerialScanner`, `ScannerFactory`, `scanReceived` signal |
| Image processing algorithm switching | Strategy | `IImageProcessor`, `SharpenProcessor`, `DenoiseProcessor`, `RotateProcessor` |
| Borderless window buttons, menus, shortcuts | Command | `IUiCommand`, `MinimizeCommand`, `MaximizeCommand`, `CloseCommand` |
| Annotation undo/redo | Command + Memento | `AnnotationCommand`, `AddBoxCommand`, `MoveBoxCommand`, `AnnotationSnapshot` |
| Connection lifecycle | State | `IConnectionState`, `DisconnectedState`, `ConnectingState`, `ConnectedState`, `ErrorState` |
| Main window page collaboration | Mediator | `MainWindowMediator`, `NavigationBar`, `PageStack`, `StatusPanel` |
| Unified video playback entry | Facade | `VideoPlayerFacade`, `Decoder`, `Renderer`, `Recorder`, `SnapshotService` |
| Cross-platform screen capture | Bridge or Strategy | `IScreenCapture`, `DxgiCapture`, `X11Capture`, `WaylandCapture`, `MacCapture` |
| Fixed import/export process with variable steps | Template Method | `BaseImportTask`, `CsvImportTask`, `JsonImportTask`, `DatabaseImportTask` |

## Qt-Specific Guidance

- Treat signals/slots as the default Observer mechanism for QObject-based code.
- Use `QAction` as a natural command trigger, but create explicit Command classes only when actions need undo/redo, logging, replay, permission checks, or non-UI invocation.
- Use `QStateMachine` when Qt's built-in state tooling is a good fit; otherwise model State with small state classes or a table-driven transition layer.
- Use QObject parent ownership for UI and long-lived object trees. Avoid owning smart pointers for objects whose parent owns them.
- Use `QPluginLoader` with Factory-style boundaries for plugin architectures.
- Use `QAbstractItemModel` as a framework-level adapter between domain data and views.

## Multi-Vendor SDK Adapter Sketch

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

class DahuaNvrAdapter final : public INvrAdapter {
public:
    bool login(const NvrLoginInfo& info) override;
    bool startPreview(int channel) override;
    bool stopPreview(int channel) override;
    bool startPlayback(const PlaybackRequest& request) override;
    bool download(const DownloadRequest& request) override;
};
```

Keep vendor-specific translation inside adapters. Keep application workflow, retry policy, permissions, and UI decisions outside adapters.
