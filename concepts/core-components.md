---
type: Concept
title: Core Components
description: "Primitive native-backed UI elements — View, Text, Image, ScrollView"
tags: [react-native, components, ui, primitives]
prerequisites:
  - concepts/architecture
related:
  - concepts/components
  - concepts/styling
  - concepts/performance
resource: "https://reactnative.dev/docs/components-and-apis"
timestamp: 2026-01-01
---

# Summary

Core components are the built-in React Native primitives that map directly to native UI elements. They are not HTML tags. Every screen is built from a small set of these native-backed building blocks.

# Mental model

Each core component is a **thin React wrapper around a native view**:

| Component | Role | Not equivalent to |
|-----------|------|-------------------|
| `View` | Generic layout container | `<div>` (no semantic HTML, different layout defaults) |
| `Text` | All text must live inside Text | Bare text nodes in JSX (invalid in RN) |
| `Image` | Bitmap and network images | `<img>` (different loading, caching, resize modes) |
| `ScrollView` | Scrollable container for bounded content | Overflow scroll on a div (different performance profile) |

`View` uses Flexbox (via Yoga) as its default layout engine. Unlike the web, `display: block` and document flow do not exist — everything is flex-oriented unless overridden.

`Text` is special: raw strings cannot appear as direct children of `View`. Text nesting creates nested native text views with inherited styles.

`Image` resolves `source` as a local `require()` asset or a `{ uri }` remote URL. Sizing often requires explicit dimensions or `resizeMode`.

`ScrollView` renders **all children at once**. It is appropriate for short, static content — not long lists (see [Performance](/concepts/performance.md)).

# Example usage

A simple card layout conceptually combines:

- An outer `View` with padding and border radius for the card shell.
- A nested `Image` with fixed height and `resizeMode: 'cover'`.
- A `Text` child for the title and another `Text` for the subtitle with different `style` props.
- A `ScrollView` wrapping the card when content may exceed screen height.

Props like `accessible`, `testID`, and `onLayout` are available on most core components for accessibility and measurement.

# Common mistakes

- **Putting text directly inside View** — Causes runtime errors or missing text; always wrap strings in `Text`.
- **Using web elements** — `div`, `span`, `p`, and `img` do not exist in React Native.
- **ScrollView for long lists** — Mounting hundreds of rows inside ScrollView exhausts memory and slows initial render; use virtualized lists instead.
- **Missing Image dimensions** — Remote images without width/height may render at 0×0 until loaded.
- **Assuming CSS inheritance** — Only `Text` descendants inherit text styles; `View` styles do not cascade to children like CSS.

# Related concepts

- [Architecture](/concepts/architecture.md) — How these components become native views
- [Components](/concepts/components.md) — Composing primitives into reusable UI
- [Styling](/concepts/styling.md) — Applying layout and appearance to core components
