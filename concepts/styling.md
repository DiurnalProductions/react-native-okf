---
type: Concept
title: Styling
description: "StyleSheet, Flexbox layout, and platform-specific appearance in React Native"
tags: [react-native, styling, layout, flexbox]
prerequisites:
  - concepts/components
related:
  - concepts/core-components
  - concepts/performance
resource: "https://reactnative.dev/docs/style"
timestamp: 2026-01-01
---

# Summary

React Native styling uses JavaScript objects (often via `StyleSheet.create`) rather than CSS files. Layout is Flexbox-first through Yoga. Many CSS properties exist with different defaults, missing shorthands, or platform-specific behavior.

# Mental model

Styles are **plain objects** passed to the `style` prop. Arrays merge left-to-right: `style={[base, active && activeStyle]}`.

```
StyleSheet.create({ ... })  →  registered IDs + validation at dev time
         ↓
Component style prop  →  serialized across bridge  →  native view attributes
```

Key layout differences from web:

- Every `View` defaults to `flexDirection: 'column'` (web default is `row` for flex containers in some contexts; RN is consistently column).
- There is no `display: grid`, no floats, no collapsing margins.
- `position: 'absolute'` is relative to the parent, similar to web.
- Units are unitless numbers (density-independent pixels), not `px`, `rem`, or `em`.
- Shadow and elevation differ: iOS uses `shadow*` props; Android uses `elevation`.

`StyleSheet.create` does not enable CSS-like selectors or pseudo-classes. Conditional styling is explicit in JSX.

# Example usage

A styled row conceptually:

- Outer `View` with `flexDirection: 'row'`, `alignItems: 'center'`, `padding: 16`.
- Fixed-width `Image` on the left.
- Flexible `View` with `flex: 1` containing stacked `Text` elements.
- Separator via `borderBottomWidth: StyleSheet.hairlineWidth` for crisp 1px lines on retina displays.

Theme tokens (colors, spacing scale) are typically plain JS objects merged into component styles — not CSS variables, though dynamic values work at runtime.

# Common mistakes

- **Using CSS strings or className** — React Native does not support CSS syntax unless using a third-party compiler (e.g., NativeWind); standard RN expects style objects.
- **Expecting margin collapse** — Vertical margins between siblings do not collapse.
- **Percentage heights without parent flex** — Percentage dimensions require explicit parent constraints.
- **Web-only properties** — `hover`, `cursor`, `vw/vh`, and many pseudo-selectors have no native equivalent.
- **Creating styles inline in hot paths** — `style={{ margin: 8 }}` allocates a new object every render; prefer `StyleSheet.create` or stable references.

# Related concepts

- [Core Components](/concepts/core-components.md) — Elements that accept `style` props
- [Components](/concepts/components.md) — Where styles are composed and themed
- [Performance](/concepts/performance.md) — Style recalculation and bridge traffic
