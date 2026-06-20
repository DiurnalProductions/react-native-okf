---
id: react-native.native-modules
type: concept
title: Native Modules
description: Exposing custom native code and platform-specific APIs to JavaScript
tags: [react-native, native-modules, ios, android, turbo-modules]
prerequisites:
  - react-native.bridges
related:
  - react-native.bridges
  - react-native.performance
resource: https://reactnative.dev/docs/native-modules-intro
timestamp: 2026-01-01
---

# Summary

Native modules let JavaScript call into platform code written in Objective-C, Swift, Java, or Kotlin. They wrap OS APIs, hardware sensors, and performance-critical logic unavailable in pure JS. Expo modules and community packages follow the same conceptual model with varying boilerplate.

# Mental model

A native module is a **registered native class** with methods exposed to JavaScript:

```
JavaScript:  import { NativeModules } from 'react-native'
             NativeModules.Biometrics.authenticate(options, callback)

Native iOS:  @objc(Biometrics) class Biometrics: NSObject { … }
Native Android: class BiometricsModule extends ReactContextBaseJavaModule { … }
```

**TurboModules** (New Architecture) generate typed C++ specs from TypeScript/Flow definitions, enabling lazy loading and JSI direct calls.

**Platform splits** are common:

- One JS API surface (`biometrics.ts`).
- Separate iOS and Android implementations behind the module.
- JS checks `Platform.OS` or the module returns `unsupported` on missing capability.

Lifecycle: modules are singletons registered at app startup (legacy) or on first access (TurboModules). They receive the React application context for accessing activities, bundles, and threads.

# Example usage

A conceptual `Brightness` module:

- `getBrightness(): Promise<number>` — reads system screen brightness from native APIs.
- `setBrightness(level: number): void` — dispatches to main thread on Android, UIScreen on iOS.
- TypeScript wrapper validates `level` is 0–1 before calling native.
- Errors reject the Promise with a structured code (`PERMISSION_DENIED`).

Community pattern: many apps use **config plugins** (Expo) or **autolinking** (CLI) so native code registers without manual `MainApplication` edits.

# Common mistakes

- **Duplicating logic on both platforms inconsistently** — Same method name but different behavior confuses JS consumers; document platform deltas explicitly.
- **Main-thread violations** — Updating UI or calling UIKit/Android UI from a background native thread crashes the app.
- **Memory leaks via callbacks** — Holding strong references to JS callbacks in native without invalidating on bridge destroy.
- **Synchronous heavy work on JS invocation** — Blocks the native module queue; offload to background threads and callback when done.
- **Skipping TypeScript specs** — Untyped modules lead to silent argument mismatches across the bridge.

# Related concepts

- [Bridges](/concepts/bridges.md) — Transport layer for module invocations
- [Architecture](/concepts/architecture.md) — Thread model governing module execution
- [Performance](/concepts/performance.md) — When to push work native vs keep in JS
