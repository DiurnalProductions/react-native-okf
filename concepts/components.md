---
type: Concept
title: React Native Components
description: "Building reusable UI through composition, props, and platform behavior"
tags: [react-native, components, composition, ui]
prerequisites:
  - concepts/core-components
related:
  - concepts/styling
  - concepts/state-management
  - concepts/gestures
  - concepts/performance
resource: "https://reactnative.dev/docs/intro-react-native-components"
timestamp: 2026-01-01
---

# Summary

Beyond core primitives, React Native applications are built from custom components — functions or classes that accept props and return a tree of native-backed elements. Component patterns mirror React on the web, but platform constraints and native rendering affect how you design them.

# Mental model

A React Native component is still a **pure function of props and state** (with hooks) that returns JSX. The difference is what JSX compiles to: native view instructions, not DOM nodes.

Composition hierarchy:

```
App
 └── Screen (custom)
      └── Header (custom)
           └── View + Text + Pressable
      └── List (custom)
           └── FlatList → row components
```

**Platform-specific components** use `.ios.js` / `.android.js` file extensions or `Platform.select()` to branch behavior while sharing a single import path.

**Host components** (`View`, `Text`, etc.) are implemented by React Native. **Composite components** are yours. The reconciler treats both uniformly, but only host components trigger native view creation.

# Example usage

A reusable `PrimaryButton` conceptually:

- Accepts `label`, `onPress`, and optional `disabled` props.
- Renders a `Pressable` wrapping a `View` with background color and a centered `Text`.
- Uses `Platform.OS` to apply slightly different padding on iOS vs Android.
- Memoizes with `React.memo` when passed stable props to avoid unnecessary re-renders in lists.

Screens compose multiple such components and receive navigation or store data via props or hooks.

# Common mistakes

- **Deep prop drilling across screens** — Better addressed with context or external state (see [State Management](/concepts/state-management.md)).
- **Inline anonymous functions in list `renderItem`** — Creates new references every render, defeating memoization and causing child re-renders.
- **Monolithic screen files** — One 800-line component mixing data fetching, layout, and handlers; split by responsibility.
- **Importing web-only libraries** — Packages that depend on `document` or DOM APIs will crash at runtime.
- **Ignoring `key` in lists** — Unstable keys cause incorrect recycling in virtualized lists and subtle UI bugs.

# Related concepts

- [Core Components](/concepts/core-components.md) — Primitives that custom components compose
- [Styling](/concepts/styling.md) — How components receive visual identity
- [State Management](/concepts/state-management.md) — Feeding data into component trees
- [Gestures](/concepts/gestures.md) — Handling touch inside components
- [Performance](/concepts/performance.md) — Re-render behavior in deep trees
