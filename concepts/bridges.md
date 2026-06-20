---
id: react-native.bridges
type: concept
title: Bridges
description: The JavaScript ↔ native communication layer and async messaging model
tags: [react-native, bridge, native, jsi]
prerequisites:
  - react-native.architecture
related:
  - react-native.native-modules
  - react-native.performance
resource: https://reactnative.dev/docs/the-new-architecture/landing-page
timestamp: 2026-01-01
---

# Summary

The bridge is the historical mechanism that serializes messages between the JavaScript runtime and native iOS/Android code. UI commands, layout results, and module invocations cross this boundary asynchronously. The New Architecture replaces much of the old bridge with JSI (JavaScript Interface) for synchronous, lower-overhead native access.

# Mental model

**Legacy bridge (async, batched):**

```
JS thread                          Native thread
    │                                    │
    │  JSON message: createView(…)       │
    ├──────────────────────────────────► │
    │                                    │ mount UIView / View
    │  JSON message: setProperty(…)      │
    ├──────────────────────────────────► │
    │                                    │
    │ ◄──────────────────────────────────┤  JSON: onTouch event
    │  invoke onPress handler            │
```

Characteristics:

- **Asynchronous** — JS sends a message and continues; native processes on its thread.
- **Serialized** — Data is JSON-like; complex objects pay conversion cost.
- **Batched** — Multiple operations may queue before flushing each frame.

**JSI / New Architecture:**

- JavaScript holds direct references to C++ host objects (TurboModules, Fabric).
- Synchronous calls possible where safe; fewer round-trips.
- Fabric renderer creates shadow trees in C++ shared between JS and native.

Both models preserve the rule: **never block the JS thread** waiting on slow native work without yielding.

# Example usage

When a component mounts:

1. React commits a new host node on the JS thread.
2. The renderer emits a create-node instruction with tag, class name (`RCTView`), and props.
3. Native allocates the view, applies initial layout via Yoga.
4. After layout, `onLayout` fires on JS with `{ width, height, x, y }`.

When calling a native module method (legacy):

- JS invokes `NativeModules.MyModule.doWork(args)`.
- Args serialize across the bridge.
- Native executes on a background or main queue.
- Result or callback serializes back to JS.

# Common mistakes

- **Assuming synchronous native returns (legacy)** — Bridge calls are async; reading a return value immediately is undefined for many APIs.
- **Flooding the bridge** — Sending thousands of layout updates or animation steps per second from JS causes jank.
- **Large payload serialization** — Passing huge arrays or binary blobs over the legacy bridge is slow; use shared buffers or file URIs.
- **Calling UI APIs from wrong thread** — Native modules must dispatch UI mutations to the main thread.
- **Ignoring New Architecture migration** — TurboModules and Fabric change performance profiles; test both during upgrades.

# Related concepts

- [Architecture](/concepts/architecture.md) — Where the bridge sits in the overall runtime
- [Native Modules](/concepts/native-modules.md) — Custom code exposed through the bridge or JSI
- [Performance](/concepts/performance.md) — Bridge traffic as a primary bottleneck
