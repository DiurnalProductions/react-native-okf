---
id: react-native.navigation
type: concept
title: Navigation
description: Screen stacks, route parameters, and deep linking in React Native apps
tags: [react-native, navigation, routing, screens]
prerequisites:
  - react-native.state-management
related:
  - react-native.state-management
  - react-native.performance
resource: https://reactnavigation.org
timestamp: 2026-01-01
---

# Summary

React Native has no built-in router. Navigation libraries (primarily React Navigation) manage a stack, tab, or drawer of screens backed by native navigation primitives. Screens are components; navigation state determines which screen is mounted and what params it receives.

# Mental model

Navigation is **state-driven screen switching**:

```
Navigation container holds route state
         ↓
Navigator (Stack / Tab / Drawer) maps routes to screen components
         ↓
Active screen renders its component tree
         ↓
navigate('Details', { id: 42 }) pushes or switches routes and passes params
```

Each screen is a normal React component. Unmounted screens in a stack may be retained in memory (configurable) or destroyed. Params are serializable data attached to a route — not callbacks (callbacks break persistence and deep linking).

Native stack navigators use platform-native transition animations (`UINavigationController` on iOS, `Fragment` transactions on Android). JavaScript-based stack navigators run transitions on the JS thread with different performance characteristics.

Deep linking maps URLs (`myapp://product/42`) to route names and params, requiring a configured linking schema at the app root.

# Example usage

An e-commerce app conceptually:

- Root `NavigationContainer` with a linking config for `product/:id` paths.
- Auth gate: unauthenticated users see a stack with Login; authenticated users see a tab navigator (Home, Cart, Profile).
- Product list screen calls `navigation.navigate('ProductDetail', { productId })`.
- Product detail reads `route.params.productId` and fetches data.
- Header back button pops the stack via `navigation.goBack()`.

Screen options (title, header buttons) are set via `options` prop or `navigation.setOptions` for dynamic headers.

# Common mistakes

- **Passing functions in route params** — Breaks state restoration and deep links; use event emitters or shared store instead.
- **Nesting navigators without understanding state shape** — Deep `navigation.navigate` calls may target wrong navigator; use nested navigator refs or fully qualified names.
- **Fetching data in wrong lifecycle** — Relying on mount-only effects when returning to a screen may show stale data; use `useFocusEffect` for refresh-on-focus.
- **Treating web routers identically** — No URL bar by default; back button is hardware/gesture on Android; iOS swipe-back is expected.
- **Huge param payloads** — Large objects in params bloat navigation state serialization; pass IDs and look up data from store or cache.

# Related concepts

- [State Management](/concepts/state-management.md) — Coordinating navigation state with app state
- [Components](/concepts/components.md) — Screens as composed component trees
- [Performance](/concepts/performance.md) — Cost of retaining many screens in memory
