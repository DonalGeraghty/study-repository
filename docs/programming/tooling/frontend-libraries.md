# Frontend Libraries

Frontend projects often combine a UI framework with focused libraries. Select each library for a real capability, keep ownership boundaries clear, and distinguish a dependency that is actively imported from one that is merely present in a manifest.

## Routing

React Router and Vue Router map URLs to application views and navigation state. Define not-found behaviour, protected-route handling, deep-link server fallback, focus changes, and browser back/forward behaviour. Client-side route guards improve experience but never replace server authorisation.

## Charts

Recharts builds charts from React components. Prepare and validate chart data separately from rendering, label axes and units, provide non-visual equivalents for important values, and test empty, single-point, large, and invalid datasets.

## Motion and Graphics

Motion libraries coordinate interface animation. Animation should reinforce state changes without delaying essential actions; respect reduced-motion preferences and test interrupted transitions.

OGL and Three.js wrap WebGL at different abstraction levels. Canvas and WebGL output needs explicit sizing, lifecycle cleanup, performance budgets, and an accessible alternative when the visual communicates essential information.

## Component and CSS Libraries

BootstrapVue-style components provide reusable layout and interaction patterns. Verify framework-version compatibility, keyboard behaviour, semantics, custom styling boundaries, and bundle cost instead of assuming a component library supplies accessibility automatically.

Vue CLI projects may use Babel to transform modern JavaScript syntax for configured browser targets. The browser support list, transforms, and polyfills must agree; transpiling syntax does not automatically provide every missing runtime API.

## Dependency Hygiene

- Remove packages that are no longer imported or configured.
- Keep runtime and development dependencies in the appropriate manifest section.
- Review update notes and generated bundle changes.
- Avoid exposing multiple libraries for the same capability without a migration plan.

## Project Connections

Nyx uses React Router, Recharts, Motion, and OGL. The Vue project uses Vue Router and BootstrapVue-related components. The browser games use the Canvas API directly rather than a rendering framework.

## Related Guides

- [React](../frameworks/react.md)
- [Vue](../frameworks/vue.md)
- [Browser Storage, Canvas, and Push](../web/browser-platform-apis.md)

Return to [Development Tooling](./README.md).
