---
tags:
  - programming/frameworks
---

# Vue

Vue is a JavaScript framework for component-based user interfaces. Single-file components commonly keep a component's template, behaviour, and scoped styles together while the application entry point mounts a root component.

## Core Model

```text
reactive state -> rendered template -> user event -> state update
```

Keep component inputs explicit with props and communicate outward through events or focused shared state. Derived values belong in computed state; watchers are best reserved for side effects that genuinely react to change.

## Worked Component

```vue
<script setup>
import { computed, ref } from "vue";

const props = defineProps({
  results: {
    type: Array,
    required: true
  }
});

const query = ref("");
const visibleResults = computed(() => {
  const term = query.value.trim().toLowerCase();
  return props.results.filter(result =>
    result.name.toLowerCase().includes(term)
  );
});
</script>

<template>
  <section aria-labelledby="results-heading">
    <h2 id="results-heading">Results</h2>

    <label for="result-search">Search</label>
    <input id="result-search" v-model="query">

    <p v-if="visibleResults.length === 0">No matching results.</p>
    <ul v-else>
      <li v-for="result in visibleResults" :key="result.id">
        {{ result.name }}
      </li>
    </ul>
  </section>
</template>
```

`query` is local mutable state, while `visibleResults` is derived and therefore computed rather than synchronised through a watcher. Stable domain IDs preserve item identity as the filtered list changes.

## Application Structure

Use small components with clear ownership, keeping server calls and domain transformations outside presentation-heavy components. Avoid making one page-sized state container responsible for everything.

Use Vue Router for route-to-view mapping, including navigation failures and not-found behaviour. Preserve semantic HTML, keyboard access, labels, focus handling and reduced-motion preferences as you assemble the interface.

Vue CLI projects use a webpack-based toolchain. Newer projects may use Vite, but the source component model is independent of the selected build tool. Understand the actual repository before applying migration advice.

## Common Failure Modes

- mutating a prop instead of emitting an event or owning local state;
- using a watcher to maintain a value that can be computed;
- using array indexes as keys for reorderable stateful items;
- putting server state and unrelated page state in one global store;
- forgetting to remove timers, listeners, or subscriptions on unmount;
- relying on a client-side route guard as server authorisation.

## Testing

Test pure functions without mounting UI. Use component tests for rendered behaviour and a small number of end-to-end tests for critical routed journeys. Prefer assertions on user-visible behaviour over component internals.

## Project Connections

The `shtormscsgo` project uses Vue 3, Vue Router, Vue CLI, Babel, ESLint, and BootstrapVue-style components.

## Interview Questions

> [!question] Interview Questions
> - Why should a derived value like a filtered list be `computed` rather than maintained by a watcher?
> - Why is mutating a prop directly a mistake, and what should a component do instead?
> - Why do array indexes make unstable keys for a reorderable list?
> - Why isn't a client-side route guard a substitute for server-side authorisation?

## Answer Notes

1. A computed value expresses a derivation from reactive dependencies and is cached until those dependencies change. A watcher-maintained copy creates duplicate state and update-order risks; watchers are better suited to side effects.

2. Props are owned by the parent, so direct mutation breaks the intended data flow and may be overwritten. Emit an event or use the component's agreed model contract so the parent updates its state.

3. An index describes position rather than identity. Reordering can make Vue reuse a component instance for a different item, attaching state or input values to the wrong row; use a stable item key.

4. Route guards only affect the browser UI and can be bypassed by calling the API directly. The server must authenticate requests and enforce resource and action permissions independently.

## Related Guides

- [JavaScript and TypeScript](../languages/javascript-typescript.md)
- [HTML](../web/html.md)
- [CSS](../web/css.md)
- [Vite and Frontend Tooling](../tooling/vite.md)

Return to [Frameworks and Libraries](./README.md).
