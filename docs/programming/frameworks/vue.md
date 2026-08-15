# Vue

Vue is a JavaScript framework for component-based user interfaces. Single-file components commonly keep a component's template, behaviour, and scoped styles together while the application entry point mounts a root component.

## Core Model

```text
reactive state -> rendered template -> user event -> state update
```

Keep component inputs explicit with props and communicate outward through events or focused shared state. Derived values belong in computed state; watchers are best reserved for side effects that genuinely react to change.

## Application Structure

- Use small components with clear ownership rather than one page-sized state container.
- Keep server calls and domain transformations outside presentation-heavy components.
- Use Vue Router for route-to-view mapping and define navigation failure and not-found behaviour.
- Preserve semantic HTML, keyboard access, labels, focus handling, and reduced-motion preferences.

Vue CLI projects use a webpack-based toolchain. Newer projects may use Vite, but the source component model is independent of the selected build tool. Understand the actual repository before applying migration advice.

## Testing

Test pure functions without mounting UI. Use component tests for rendered behaviour and a small number of end-to-end tests for critical routed journeys. Prefer assertions on user-visible behaviour over component internals.

## Project Connections

The `shtormscsgo` project uses Vue 3, Vue Router, Vue CLI, Babel, ESLint, and BootstrapVue-style components.

## Related Guides

- [JavaScript and TypeScript](../languages/javascript-typescript.md)
- [HTML](../web/html.md)
- [CSS](../web/css.md)
- [Vite and Frontend Tooling](../tooling/vite.md)

Return to [Frameworks and Libraries](./README.md).
