# Qt Module Boundaries

Use this guide when the user asks how to split a Qt project into modules, CMake targets, include directories, or architecture boundaries.

## Target-Level Rule

Prefer CMake targets that reflect architecture boundaries:

```text
app
ui
presentation
application
domain
infrastructure_sdk
infrastructure_storage
infrastructure_comm
```

Dependencies should point inward or sideways through interfaces, not from domain/application into concrete infrastructure.

## Include Direction

Good:

```text
ui -> application interfaces
application -> domain interfaces
infrastructure -> domain/application interfaces
```

Avoid:

```text
domain -> ui
domain -> QSqlQuery
ui -> vendor SDK headers
application -> concrete plugin implementation
```

## Header Organization

```text
domain/include/domain/...
application/include/application/...
infrastructure/sdk/include/infrastructure/sdk/...
ui/include/ui/...
```

Use private source directories for implementation details and SDK headers.

## CMake Sketch

```cmake
add_library(domain ...)
target_include_directories(domain PUBLIC domain/include)

add_library(application ...)
target_link_libraries(application PUBLIC domain Qt::Core)

add_library(infrastructure_sdk ...)
target_link_libraries(infrastructure_sdk PUBLIC application PRIVATE VendorSdk Qt::Core)

add_executable(app ...)
target_link_libraries(app PRIVATE ui application infrastructure_sdk)
```

## Boundary Smells

- `MainWindow` includes SDK headers.
- Domain model includes `QWidget`, `QSqlQuery`, or socket classes.
- One target links everything.
- Circular includes between UI and services.
- Infrastructure callbacks call widgets directly.
- Tests must start the full UI to test business behavior.

## Pattern Mapping

- Adapter belongs at infrastructure boundaries.
- Facade/application service belongs at use-case boundaries.
- Factory belongs at composition roots or plugin registries.
- Repository belongs at storage boundaries.
- Strategy belongs inside domain/application when behavior varies.

## Migration Plan

1. Add interfaces without moving everything.
2. Move SDK/database includes behind infrastructure files.
3. Introduce application service APIs for UI calls.
4. Split CMake targets after include direction is clear.
5. Add tests for application services before broad moves.
