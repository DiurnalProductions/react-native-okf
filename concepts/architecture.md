---
type: Concept
title: React Native Architecture
description: "JavaScript runtime, native rendering pipeline, and the bridge model"
tags: [react-native, architecture, bridge, rendering]
related:
  - concepts/core-components
  - concepts/bridges
  - concepts/performance
resource: "https://reactnative.dev/docs/architecture-overview"
timestamp: 2026-07-06
---

# Summary

React Native runs JavaScript in a separate runtime (Hermes, the default engine) while UI is rendered by native iOS and Android views. React reconciles a virtual tree in JavaScript; layout and painting happen on the native side. This is fundamentally different from React DOM, where the browser owns both the runtime and the rendering surface.

# Mental model

Think of React Native as **two cooperating processes**:

1. **JavaScript thread** — Runs your React components, hooks, and business logic. Maintains a shadow representation of the UI tree.
2. **Native (UI) thread** — Owns real `UIView` / `android.view.View` instances. Performs layout, drawing, and touch dispatch.

A **bridge** (legacy) or **JSI-based native layer** (New Architecture) serializes commands and events between these threads. JavaScript never directly manipulates DOM nodes; it sends instructions like "create a View with these styles" and receives events like "user tapped this element."

The rendering pipeline:

```
JS: render() → React reconciliation → shadow tree updates
     ↓ (bridge / JSI)
Native: layout (Yoga) → mount/update native views → paint
     ↓
Native: touch/scroll events → serialize → JS event handlers
```

React DOM maps components to HTML elements in the same process. React Native maps components to native widgets across a process boundary. There is no `<div>`, no CSS cascade from the browser, and no synchronous DOM reads.

# Example usage

A screen component conceptually:

- Declares a tree of `View`, `Text`, and other core components in JSX.
- React diffs that tree on the JS thread after state changes.
- The native renderer applies only the delta — new views, updated styles, removed nodes.
- User interaction on the native thread invokes your `onPress` handler asynchronously on the JS thread.

With the **New Architecture** (Fabric + TurboModules) — the default since React Native 0.76 and the only architecture from 0.82 onward — this crosses via JSI with lower serialization overhead, and Fabric can render some updates synchronously (e.g. for `useLayoutEffect` measurements). Hermes remains the engine, with Hermes V1 rolling out. The two-thread mental model still applies.

# Common mistakes

- **Treating RN like the web** — Assuming `div`-like elements, global CSS, or synchronous layout measurement from JS without `onLayout`.
- **Blocking the JS thread** — Heavy computation during render or in synchronous native module calls freezes UI and drops frames.
- **Ignoring the native thread** — Animations driven purely from JS on every frame re-cross the bridge and jank; prefer native-driven animation APIs.
- **Expecting identical behavior on iOS and Android** — The same React component may map to different native implementations with subtle platform differences.
- **Confusing Metro with the runtime** — Metro bundles JavaScript at build time; Hermes/JSC executes it at runtime. They are separate concerns.

# Related concepts

- [Core Components](/concepts/core-components.md) — The native-backed primitives your architecture renders
- [Bridges](/concepts/bridges.md) — How JS and native code communicate
- [Performance](/concepts/performance.md) — Costs introduced by the two-thread model
