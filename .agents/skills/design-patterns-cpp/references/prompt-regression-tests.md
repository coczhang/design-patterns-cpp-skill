# Prompt Regression Tests

Use these prompts to forward-test whether the skill produces stable, practical C++/Qt architecture advice. Do not treat these as expected answers; inspect whether the answer scores necessity, avoids overengineering, handles Qt ownership, and gives usable code.

## Multi-Vendor SDK

```text
Use $design-patterns-cpp to design a C++17/Qt abstraction for NVR SDK access.
We support Hikvision and Dahua today and will add TP-LINK later.
Each vendor supports login, preview, playback, download, and error callbacks.
Show class design, C++ interfaces, ownership rules, and overengineering check.
```

Expected qualities:

- Adapter + Factory likely recommended.
- Vendor SDK types should not leak upward.
- Error normalization and callback ownership should be mentioned.
- Decision rubric should justify the pattern.

## Communication Branch Refactor

```text
Use $design-patterns-cpp to refactor a communication module with if/else branches for TCP, UDP, HTTP, WebSocket, and future serial.
The module is QObject-based and emits dataReceived/error signals.
Give migration steps and Qt ownership guidance.
```

Expected qualities:

- Strategy + Factory likely recommended.
- Parent-owned QObject return or clear ownership should be stated.
- Signal compatibility and CMake impact should be checked.

## Undo/Redo

```text
Use $design-patterns-cpp to design undo/redo for image annotation boxes.
Operations include add, delete, move, resize, and label edit.
The image can have thousands of boxes.
```

Expected qualities:

- Command + Memento likely recommended.
- Deltas should be preferred over full snapshots.
- Memory growth and sequence tests should be mentioned.

## Connection State

```text
Use $design-patterns-cpp to decide whether a Qt network client needs State pattern.
It has disconnected, connecting, connected, reconnecting, and error states.
connect(), disconnect(), send(), and retry() behave differently by state.
```

Expected qualities:

- State likely recommended if branch complexity is real.
- Invalid transitions and logs should be included.
- Simpler enum alternative should be discussed if code is still small.

## Overengineering Refusal

```text
Use $design-patterns-cpp to review whether I should introduce Strategy for a class with one compression algorithm.
There is no plan to add another algorithm this quarter.
```

Expected qualities:

- Pattern should be rejected or deferred.
- Simple design and upgrade trigger should be provided.
- Decision score should be low.
