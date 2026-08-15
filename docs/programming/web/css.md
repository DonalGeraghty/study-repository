# CSS

CSS describes how structured documents are presented. It is a rule system influenced by the cascade, inheritance, layout algorithms, content, viewport, user preferences, and browser support—not a sequence of drawing commands.

## Selectors and the Cascade

A rule selects elements and assigns property values:

```css
.notice {
  border-inline-start: 0.25rem solid var(--accent-color);
  padding: 1rem;
}
```

When declarations conflict, the cascade considers origin and importance, cascade layers, selector specificity, scoping proximity where applicable, and source order. Prefer low-specificity classes and an intentional layer structure over escalating selectors or routine `!important`.

Inheritance passes selected computed values, such as text colour, from parent to child. Inspect the browser’s computed styles to learn which rule won and why.

## Box Model and Sizing

Every rendered element has content, padding, border, and margin areas. A common foundation makes declared width include padding and borders:

```css
*,
*::before,
*::after {
  box-sizing: border-box;
}
```

Avoid fixed heights for text containers unless overflow is handled deliberately. Use relative units for scalable typography and spacing, and understand the containing block before relying on percentages or positioned offsets.

## Layout

Normal flow should do most of the work. Add a layout mode according to the relationship:

| Need | Typical choice |
| --- | --- |
| One-dimensional row or column | Flexbox |
| Two-dimensional rows and columns | Grid |
| Inline text flow | Inline layout |
| Content independent of normal position | Positioned layout, used deliberately |

```css
.card-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(min(18rem, 100%), 1fr));
  gap: 1rem;
}
```

Do not reorder meaningful content visually in a way that disagrees with DOM and keyboard order.

## Responsive Design

Design from content constraints rather than a catalogue of device sizes. Flexible tracks, wrapping, intrinsic sizing, and `min()`/`max()`/`clamp()` can reduce breakpoint count. Media and container queries should respond to a real layout transition.

```css
.page-title {
  font-size: clamp(1.75rem, 1.25rem + 2vw, 3rem);
}
```

Test zoom, narrow widths, long translations, large text, and user font settings—not only common phone dimensions.

## Custom Properties and Architecture

Custom properties define reusable values that participate in the cascade:

```css
:root {
  --space-2: 0.5rem;
  --space-4: 1rem;
  --surface: #ffffff;
  --text: #1a1a1a;
}
```

Use meaningful design tokens and component boundaries. Keep global rules limited and predictable. A naming convention is useful when it communicates ownership and state; it should not compensate for unclear HTML structure.

## Accessibility and User Preferences

Ensure sufficient contrast, visible keyboard focus, usable target sizes, and content that survives text resizing. Do not use colour as the only signal.

Respect user preferences:

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    scroll-behavior: auto;
    animation-duration: 0.01ms;
    animation-iteration-count: 1;
  }
}
```

Animation should explain change or provide feedback, not obstruct access. Hiding content visually can also remove it from assistive technology depending on the technique.

## Performance and Compatibility

Prefer progressive enhancement: deliver a usable base and enhance where supported. Check compatibility for features that affect required behaviour. Avoid premature micro-optimisation; large unused stylesheets, blocking resources, expensive visual effects, and layout instability usually matter more than selector folklore.

## Testing and Debugging

Use browser developer tools to inspect the cascade, box model, grid and flex overlays, responsive behaviour, accessibility, and rendering performance. Add visual-regression tests for stable, high-value layouts while keeping assertions tolerant of intentional rendering differences.

Test:

- supported browsers and viewport ranges;
- keyboard focus and hover-independent interaction;
- light, dark, contrast, and reduced-motion preferences where supported;
- long content, empty states, errors, and loading states;
- zoom and text scaling.

## Readiness Checklist

You should be able to:

- explain why a declaration wins in the cascade;
- reason about box sizing, normal flow, containing blocks, and overflow;
- choose Flexbox or Grid from layout requirements;
- build responsive components around content constraints;
- structure tokens and component styles without specificity escalation;
- preserve focus, contrast, zoom, and motion accessibility;
- diagnose rendering with computed styles and layout tools.

## Official References

- [CSS reference](https://developer.mozilla.org/docs/Web/CSS)
- [CSS specifications](https://www.w3.org/Style/CSS/specs.en.html)
- [Cascade and inheritance](https://developer.mozilla.org/docs/Web/CSS/Guides/Cascade)
- [CSS Grid layout](https://developer.mozilla.org/docs/Web/CSS/Guides/Grid_layout)

Return to [Web Foundations](./README.md).
