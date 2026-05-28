# Qt Architecture Patterns

Use this reference when the user asks for Qt application architecture, module design, UI/business separation, plugin systems, threading architecture, model/view structure, or how GoF patterns fit into a larger Qt project.

## Architecture Decision Workflow

1. Identify the application type: Qt Widgets, Qt Quick/QML, service/daemon, SDK wrapper, industrial desktop client, visualization tool, or hybrid.
2. Identify major change axes: UI screens, device vendors, communication protocols, algorithms, data models, storage, deployment plugins, or async tasks.
3. Select the architectural style before selecting individual GoF patterns.
4. Define ownership and thread boundaries early.
5. Keep MainWindow/QML pages thin; move orchestration into services, presenters, view models, facades, or mediators.
6. Keep infrastructure details behind adapters/repositories.
7. Add tests around application services, models, adapters, and state transitions.

## Common Qt Architecture Styles

| Style | Use when | Main Qt types |
| --- | --- | --- |
| Layered architecture | Business logic, SDKs, UI, and storage need clear boundaries | QObject services, adapters, repositories, CMake targets |
| Model/View | Lists, tables, trees, device hierarchy, logs, alarms, result grids | `QAbstractItemModel`, `QSortFilterProxyModel`, delegates, views |
| MVP | Qt Widgets UI should stay thin while presenter owns screen logic | QWidget, Presenter QObject, service interfaces |
| MVVM | QML/Qt Quick or binding-heavy UI needs observable state | QML, QObject ViewModel, properties, signals |
| Plugin architecture | Vendors, algorithms, protocols, or feature packs load dynamically | `QPluginLoader`, interface classes, metadata |
| Event-driven architecture | Loose notification between modules is needed | signals/slots, domain events, event bus only when justified |
| State-machine architecture | Lifecycle transitions are central | `QStateMachine`, State pattern, transition tables |
| Worker/task architecture | Work must not block the UI thread | worker-object, `QThreadPool`, `QtConcurrent`, task queue |
| Repository/DAO | Storage access needs isolation and testability | `QSqlDatabase`, repositories, query objects |
| Service + Facade | UI should call use-case-level APIs instead of SDK/network/database details | application services, facades, adapters |

## Reference Routing

- Use `qt-layered-architecture.md` for UI/application/domain/infrastructure boundaries.
- Use `qt-model-view-guide.md` for `QAbstractItemModel`, proxy models, delegates, and table/tree UI.
- Use `qt-mvp-mvvm-guide.md` for Qt Widgets MVP and QML MVVM decisions.
- Use `qt-plugin-architecture.md` for `QPluginLoader`, plugin interfaces, metadata, and versioning.
- Use `qt-threading-architecture.md` for worker-object, `QThreadPool`, async boundaries, and signal safety.
- Use `qt-module-boundaries.md` for CMake target boundaries, include direction, and module split.

## Pattern Mapping

| Architecture concern | Useful patterns |
| --- | --- |
| SDK boundary | Adapter, Factory, Facade |
| Algorithm selection | Strategy, Factory |
| UI page coordination | Mediator, Observer via signals/slots |
| Model transformations | Adapter, Proxy, Composite |
| Dynamic feature loading | Factory, Abstract Factory, Plugin architecture |
| Background work | Command, Strategy, Observer via signals/slots |
| Storage isolation | Repository, Proxy, Adapter |
| Lifecycle behavior | State, Template Method |

Always combine this architecture guidance with the anti-overengineering and decision-rubric references.
