---
type: Concept
title: State Management
description: "Local state, context, and external stores in React Native applications"
tags: [react-native, state, hooks, context]
prerequisites:
  - concepts/components
related:
  - concepts/navigation
  - concepts/performance
resource: "https://reactnative.dev/docs/state"
timestamp: 2026-01-01
---

# Summary

React Native uses the same React state model as the web: `useState`, `useReducer`, `useContext`, and external stores. State changes trigger re-renders on the JavaScript thread, which propagate style and structure updates across the bridge to native views.

# Mental model

State lives in **React component trees on the JS thread**. Native views are a projection of the latest render output — they do not hold application business state.

Layers of state scope:

| Layer | Mechanism | Best for |
|-------|-----------|----------|
| Local | `useState`, `useReducer` | UI toggles, form fields, component-specific data |
| Shared tree | `useContext` | Theme, auth session, locale — moderate fan-out |
| Global store | Redux, Zustand, Jotai, MobX | Cross-screen data, caching, complex updates |
| Server/async | React Query, SWR, custom hooks | Remote data with loading/error lifecycle |

When state updates:

1. React schedules a re-render of affected components on the JS thread.
2. Reconciliation produces an updated shadow tree.
3. Native renderer applies diffs — unchanged subtrees skip native work if memoized correctly.

Navigation libraries often maintain their own navigation state (current route, params) separate from your app store. Coordinating both is a common integration point (see [Navigation](/concepts/navigation.md)).

# Example usage

A profile screen conceptually:

- Uses a data hook (`useQuery` or custom) to fetch user data on mount.
- Stores ephemeral UI state (`isEditing`) in local `useState`.
- Reads auth token from a context provider wrapping the app root.
- Dispatches cart updates to a global store when the user taps "Add to cart."
- Passes derived props to presentational child components.

Persisted state (AsyncStorage, MMKV, SQLite) sits outside React; hydration loads into store or local state on app launch.

# Common mistakes

- **Storing everything globally** — Unnecessary subscriptions cause wide re-renders and hurt performance.
- **Mutating state directly** — `state.items.push(x)` without a new reference skips re-renders.
- **Context for high-frequency updates** — Every context consumer re-renders on any context change; split contexts or use selectors.
- **Ignoring async storage race conditions** — Reading persisted state after first render causes flicker; gate UI behind a hydration flag.
- **Duplicating server state in global store** — Prefer a single source of truth with cache libraries rather than manual sync.

# Related concepts

- [Components](/concepts/components.md) — Where state hooks live and propagate
- [Navigation](/concepts/navigation.md) — Route params and navigation state interplay
- [Performance](/concepts/performance.md) — Re-render cost from state architecture
