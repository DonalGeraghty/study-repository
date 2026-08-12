# React

React is a library for building user interfaces from components. A component describes UI for its current props and state; React coordinates rendering and commits the necessary changes to the host environment, commonly the browser DOM.

## Components and Rendering

Components are JavaScript functions that return UI descriptions. Keep rendering pure: the same props and state should produce the same result without modifying external state.

```tsx
type ResultProps = {
  name: string;
  passed: boolean;
};

function Result({ name, passed }: ResultProps) {
  return <li>{name}: {passed ? "Passed" : "Failed"}</li>;
}
```

Composition is the primary reuse mechanism. Split components around cohesive responsibilities and meaningful data flow, not arbitrary line counts.

## Props, State, and Identity

Props are inputs from a parent. State is a component’s memory between renders. Treat both as immutable snapshots.

```tsx
const [query, setQuery] = useState("");

const visibleResults = results.filter(result =>
  result.name.toLowerCase().includes(query.toLowerCase())
);
```

Do not store derived data in state when it can be calculated during rendering. Each independent piece of state should have one clear owner. Lift state to the nearest shared owner when multiple components must coordinate.

Keys give list items stable identity. Use a durable domain identifier, not an array index when items can move, be inserted, or be removed.

## Events and Forms

Event handlers update state or invoke application actions. Keep the user interaction explicit:

```tsx
function SearchForm({ onSearch }: { onSearch: (query: string) => void }) {
  const [query, setQuery] = useState("");

  return (
    <form onSubmit={event => { event.preventDefault(); onSearch(query); }}>
      <label htmlFor="query">Search</label>
      <input id="query" value={query} onChange={e => setQuery(e.target.value)} />
      <button type="submit">Search</button>
    </form>
  );
}
```

Preserve native HTML semantics rather than recreating buttons, links, labels, and validation from generic elements.

## Effects

Effects synchronise a component with an external system such as a subscription, browser API, or network connection. They are not the default place for ordinary data transformation or event handling.

```tsx
useEffect(() => {
  const controller = new AbortController();
  loadResults(controller.signal).then(setResults, handleError);
  return () => controller.abort();
}, []);
```

Dependencies describe which reactive values the effect reads. Do not suppress dependency analysis to force a lifecycle shape. Ensure cleanup mirrors setup and guard against stale or out-of-order asynchronous results.

## Context and State Management

Context makes a value available through a subtree and suits cross-cutting values such as themes or authenticated identity. Frequently changing large context values can rerender many consumers. Local state, composition, a reducer, server-state tooling, or an external store may be more appropriate depending on ownership and update patterns.

Choose state tooling after identifying:

- who owns the state;
- whether it is local UI, URL, server, or durable client state;
- how it is loaded, invalidated, and updated;
- which components need to observe each change.

## Accessibility and Performance

Use semantic HTML, logical focus movement, visible focus, accessible names, and announced validation errors. Test keyboard interaction and real screen-reader workflows for critical features.

Before memoising, measure. `memo`, `useMemo`, and `useCallback` add complexity and do not correct an impure render or poor state placement. Improve data ownership, component boundaries, network behaviour, and bundle delivery before applying speculative memoisation.

## Testing

Test components as users encounter them: query by role and accessible name, perform realistic interactions, and assert visible outcomes. Avoid asserting internal state, hook calls, or component implementation details.

A balanced strategy includes:

- unit tests for pure transformations and reducers;
- component tests for rendering and interaction;
- integration tests around routing, data, and major boundaries;
- a small set of end-to-end tests for critical journeys.

## Common Failure Modes

- mutating props or state;
- mirroring derived values into state through effects;
- unstable keys causing state to attach to the wrong item;
- stale closures or missing effect cleanup;
- a single global store for unrelated state;
- inaccessible custom controls;
- premature memoisation hiding a data-flow problem.

## Readiness Checklist

You should be able to:

- explain component purity, render, commit, props, state, and identity;
- place state with one clear owner and avoid redundant state;
- distinguish event handling from external-system synchronisation;
- design effects with correct dependencies and cleanup;
- build accessible forms and interaction patterns;
- select a state-management approach from ownership and lifecycle;
- test observable behaviour and profile before optimising.

## Official References

- [React documentation](https://react.dev/)
- [Thinking in React](https://react.dev/learn/thinking-in-react)
- [Managing state](https://react.dev/learn/managing-state)
- [Synchronizing with effects](https://react.dev/learn/synchronizing-with-effects)

Return to the [JavaScript and TypeScript](../languages/javascript-typescript.md) guide.
