---
id: react-native.performance
type: concept
title: Performance
description: Re-render costs, bridge overhead, and list virtualization in React Native
tags: [react-native, performance, optimization, flatlist]
prerequisites:
  - react-native.navigation
  - react-native.native-modules
  - react-native.gestures
related:
  - react-native.architecture
  - react-native.components
  - react-native.bridges
  - react-native.styling
  - react-native.state-management
  - react-native.navigation
  - react-native.native-modules
  - react-native.gestures
resource: https://reactnative.dev/docs/performance
timestamp: 2026-01-01
---

# Summary

React Native performance is governed by JavaScript thread throughput, bridge/JSI traffic, native layout cost, and memory pressure from mounted views. The frame budget is ~16ms per frame at 60fps — work on either thread can drop frames. Lists and animations are the most common production bottlenecks.

# Mental model

**Two-thread budget:**

| Symptom | Likely cause | Thread |
|---------|--------------|--------|
| UI frozen, interactions delayed | Heavy JS in render/effects | JS |
| Animations stutter while JS idle | Layout thrashing, overdraw | Native |
| Scroll jank in long lists | Non-virtualized lists, heavy rows | Both |

**Re-render cost chain:**

```
setState → React reconcile → style diff → bridge/JSI updates → Yoga layout → native paint
```

Memoization (`React.memo`, `useMemo`, `useCallback`) reduces reconcile and child render work but only helps when props/references are stable.

**Bridge overhead:** Every host component prop change may serialize. Batching helps, but thousands of updates per scroll frame still hurt on legacy architecture.

**List virtualization:** `FlatList` and `SectionList` mount only visible rows plus a small window (`windowSize`). `ScrollView` with `.map()` mounts everything — O(n) views.

# Example usage

Optimizing a feed screen conceptually:

- Replace `ScrollView` + `map` with `FlatList`, providing `keyExtractor` and `getItemLayout` when row height is fixed.
- Extract row to `memo`ized component; pass only `item.id` and primitives, not whole parent state.
- Move images to fixed dimensions; use progressive loading and caching.
- Use `InteractionManager.runAfterInteractions` to defer non-critical work after navigation transitions.
- Profile with React DevTools Profiler and native tools (Xcode Instruments, Android Systrace).

Animations: prefer **Reanimated** worklets running on the UI thread over `Animated` callbacks firing on JS each frame.

# Common mistakes

- **ScrollView for hundreds of items** — Memory and mount time grow linearly; always virtualize long lists.
- **Anonymous functions and object literals in `renderItem`** — Defeats `memo` on row components.
- **Synchronous layout reads in loops** — Measuring many views sequentially blocks frames; batch or use known dimensions.
- **Unoptimized images** — Full-resolution decoded bitmaps in lists cause memory spikes and GC pauses on JS thread.
- **Console.log in production** — Logging large objects synchronously over the bridge degrades debug and release builds.
- **Ignoring `removeClippedSubviews` on Android** — Off-screen views still consume layout cost without clipping hints.

# Related concepts

- [Architecture](/concepts/architecture.md) — Two-thread model underlying all bottlenecks
- [Bridges](/concepts/bridges.md) — Serialization cost per update
- [Components](/concepts/components.md) — Memoization and composition patterns
- [Styling](/concepts/styling.md) — Layout recalculation triggers
- [State Management](/concepts/state-management.md) — Store granularity and re-render fan-out
- [Navigation](/concepts/navigation.md) — Screen retention and memory
- [Native Modules](/concepts/native-modules.md) — Offloading hot paths to native
- [Gestures](/concepts/gestures.md) — Native vs JS-driven interaction cost
