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

- Use small components with clear ownership rather than one page-sized state container.
- Keep server calls and domain transformations outside presentation-heavy components.
- Use Vue Router for route-to-view mapping and define navigation failure and not-found behaviour.
- Preserve semantic HTML, keyboard access, labels, focus handling, and reduced-motion preferences.

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

## Related Guides

- [JavaScript and TypeScript](../languages/javascript-typescript.md)
- [HTML](../web/html.md)
- [CSS](../web/css.md)
- [Vite and Frontend Tooling](../tooling/vite.md)

Return to [Frameworks and Libraries](./README.md).
