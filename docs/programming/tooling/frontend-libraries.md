---
tags:
  - programming/tooling
---

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

## Worked Scenario: A Routed Chart Page

A report page uses a client router, chart library, and animated loading panel. Design checks for a bookmarked URL, no data, a failed API request, keyboard navigation, and reduced motion before choosing another package.

**Check your reasoning:** The server must deliver the application at the deep link; the router selects the view and handles unknown paths. The data boundary validates units and values before the chart sees them. Empty data needs an explicit explanation rather than an invented zero. Errors need a recoverable state; important chart values need a text or tabular equivalent. Animation must not delay access, and leaving the page must release listeners and graphics resources.

A useful test observes the report title, values, error recovery, and focus behaviour. Asserting that a particular library component was instantiated proves little about the user's task. Remove a library only after checking source imports, configuration, and build-time use.

## Interview Questions

> [!question] Interview Questions
> - Why doesn't a client-side route guard ever replace server-side authorisation?
> - Why should chart data be prepared and validated separately from the rendering code that displays it?
> - Why can transpiling modern syntax with Babel still leave a missing runtime API unpolyfilled?
> - Why is a component library not proof of accessibility by itself?

## Answer Notes

1. The browser is controlled by the caller and its navigation rules can be bypassed with direct requests. Server-side checks must independently enforce permissions for each protected operation and resource.

2. Separate calculations expose errors in grouping, missing values and units before rendering and make them straightforward to test. The chart component can then focus on presenting a well-defined dataset accurately.

3. Transpilation rewrites language syntax, but an absent browser API still needs a runtime implementation. Check the supported browser targets and deliberately include a suitable polyfill or alternative for required APIs.

4. Accessibility depends on how components are assembled, labelled and operated in the actual app. Verify keyboard use, focus, contrast and assistive-technology behaviour; incorrect configuration can undermine accessible library defaults.

## Related Guides

- [React](../frameworks/react.md)
- [Vue](../frameworks/vue.md)
- [Browser Storage, Canvas, and Push](../web/browser-platform-apis.md)

Return to [Development Tooling](./README.md).
