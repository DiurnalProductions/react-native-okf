# React Native Knowledge Pack

A plug-and-play OKF knowledge bundle for learning React Native development. Install this repository into any OKF-compatible client to traverse concepts as a directed learning graph.

## Start here

Begin with [React Native Architecture](/concepts/architecture.md). It establishes the JavaScript runtime, native rendering pipeline, and bridge model that every other concept builds on. Without this mental model, web React assumptions will mislead you at every step.

## Recommended learning path

Follow this progression for a complete foundation:

1. [Architecture](/concepts/architecture.md) — JS runtime, native UI, rendering pipeline
2. [Core Components](/concepts/core-components.md) — View, Text, Image, ScrollView
3. [Components](/concepts/components.md) — Composition, props, and platform behavior
4. [Styling](/concepts/styling.md) — StyleSheet, layout, and platform differences
5. [State Management](/concepts/state-management.md) — Local state, context, and external stores
6. [Navigation](/concepts/navigation.md) — Screen stacks, params, and deep linking
7. [Performance](/concepts/performance.md) — Re-renders, bridge cost, list virtualization

### Native integration path

After architecture, branch into native code:

1. [Architecture](/concepts/architecture.md)
2. [Bridges](/concepts/bridges.md) — JS ↔ native messaging
3. [Native Modules](/concepts/native-modules.md) — Custom platform APIs
4. [Performance](/concepts/performance.md) — Bridge overhead and optimization

### Interaction path

After components, explore touch handling:

1. [Components](/concepts/components.md)
2. [Gestures](/concepts/gestures.md) — Touch, pan, and gesture responders
3. [Performance](/concepts/performance.md) — Interaction-driven performance

## Concept groups

### Foundations

* [Architecture](/concepts/architecture.md) — How React Native runs JavaScript and renders native UI
* [Bridges](/concepts/bridges.md) — The communication layer between JS and native code

### UI Building

* [Core Components](/concepts/core-components.md) — Primitive native-backed UI elements
* [Components](/concepts/components.md) — Building reusable UI from primitives
* [Styling](/concepts/styling.md) — Layout and appearance on native surfaces
* [Gestures](/concepts/gestures.md) — Touch and gesture handling

### State & Navigation

* [State Management](/concepts/state-management.md) — Managing and sharing application state
* [Navigation](/concepts/navigation.md) — Moving between screens and passing data

### Native Integration

* [Native Modules](/concepts/native-modules.md) — Exposing platform APIs to JavaScript

### Performance Optimization

* [Performance](/concepts/performance.md) — Re-render costs, bridge overhead, and list virtualization

## Progression order

| Order | Concept | Depends on |
|-------|---------|------------|
| 1 | Architecture | — |
| 2 | Core Components | Architecture |
| 3 | Bridges | Architecture |
| 4 | Components | Core Components |
| 5 | Styling | Components |
| 6 | State Management | Components |
| 7 | Gestures | Components |
| 8 | Navigation | State Management |
| 9 | Native Modules | Bridges |
| 10 | Performance | Navigation, Native Modules, Gestures |
