---
id: react-native.gestures
type: concept
title: Gestures
description: Touch handling, gesture responders, and interaction primitives in React Native
tags: [react-native, gestures, touch, interaction]
prerequisites:
  - react-native.components
related:
  - react-native.performance
resource: https://reactnative.dev/docs/gesture-responder-system
timestamp: 2026-01-01
---

# Summary

React Native handles touch through a gesture responder system built into core components, supplemented by `Pressable`, `TouchableOpacity`, and higher-level libraries like React Native Gesture Handler. Touch events originate on the native thread and dispatch to JS handlers asynchronously.

# Mental model

Touch flow:

```
Native: finger down → hit testing on native view hierarchy
         ↓
Bridge: touch start/move/end events
         ↓
JS: gesture responder negotiation (bubbling/capture)
         ↓
JS: winning component's onResponderMove / onPress handlers run
```

**Responder negotiation** decides which component owns a gesture when multiple overlapping views could respond (e.g., scroll view vs button inside it). ScrollViews typically become responders to claim pan gestures.

**Pressable** (modern) vs legacy Touchables:

- `Pressable` exposes `pressed` state for styling and supports android ripple via `android_ripple`.
- Legacy `TouchableOpacity` animates opacity on press — simple but less flexible.

**React Native Gesture Handler** runs recognizers on the native thread (UI thread on Android, main on iOS), enabling simultaneous gestures and smoother interaction than JS-only responders — especially for swipes, pinches, and drag-to-dismiss.

# Example usage

A swipeable list row conceptually:

- Row content wrapped in `Pressable` for tap.
- Outer `GestureDetector` (RNGH) with `Pan` gesture for horizontal drag.
- On drag past threshold, animate row off-screen via Reanimated shared values (native-driven).
- Parent `FlatList` uses `simultaneousHandlers` or `waitFor` to coordinate scroll vs swipe.

Simple button: `Pressable` with `onPress`, `disabled`, and style function receiving `{ pressed }`.

# Common mistakes

- **Nested touchables** — Parent and child both capture presses; unpredictable behavior without `pointerEvents` or responder props.
- **Using TouchableWithoutFeedback for accessibility** — Missing default accessibility roles; prefer `Pressable` with proper labels.
- **JS-thread pan tracking for complex gestures** — Laggy compared to native gesture handler + Reanimated.
- **Ignoring `pointerEvents: 'box-none'`** — Transparent overlay Views block touches to children beneath.
- **Assuming hover states** — Mobile has no mouse hover; web-oriented interaction patterns do not translate.

# Related concepts

- [Components](/concepts/components.md) — Where touch targets are defined
- [Core Components](/concepts/core-components.md) — ScrollView responder behavior
- [Performance](/concepts/performance.md) — JS-driven gesture handlers vs native-driven
