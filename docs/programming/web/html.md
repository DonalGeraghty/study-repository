# HTML

HTML gives web content structure and meaning. Browsers parse markup into the Document Object Model (DOM), then combine it with CSS and JavaScript. Good HTML begins with semantics and progressive enhancement rather than treating every element as a generic container.

## Document Structure

A minimal document declares its syntax, language, character encoding, viewport, and title:

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <title>Study notes</title>
  </head>
  <body>
    <main>
      <h1>Study notes</h1>
    </main>
  </body>
</html>
```

The title identifies the page in browser tabs and assistive technology. The `lang` attribute helps pronunciation, translation, and language-aware processing.

## Semantic Elements

Choose an element for what the content means:

| Content | Typical element |
| --- | --- |
| Primary page content | `main` |
| Self-contained item | `article` |
| Thematic grouping | `section` with a heading |
| Navigation links | `nav` |
| Supporting content | `aside` |
| Action | `button` |
| Destination | `a` with `href` |

Semantic elements provide browser behaviour, keyboard interaction, and accessibility information. A styled `div` does not automatically become a button, heading, or landmark.

Use headings to describe a logical hierarchy. Do not choose a heading level merely for its default font size; use CSS for appearance.

## Links, Images, and Media

Link text should describe the destination without relying on nearby context. Use buttons for actions and links for navigation.

Images need an `alt` decision:

- describe meaningful content concisely;
- use `alt=""` for an image that is purely decorative;
- avoid repeating adjacent text;
- provide a text equivalent for complex charts or diagrams.

Specify intrinsic image dimensions when known to reduce layout movement. Use responsive image features when different sizes or crops are genuinely needed.

## Forms

Associate every control with an accessible name, usually an explicit label:

```html
<form method="post" action="/subscriptions">
  <label for="email">Email address</label>
  <input id="email" name="email" type="email" autocomplete="email" required />

  <button type="submit">Subscribe</button>
</form>
```

The `name` participates in submission; `id` connects the label. Use `fieldset` and `legend` for related controls. Native input types and autocomplete tokens improve mobile keyboards and browser assistance.

Browser validation improves usability but is not a security boundary. The server must validate, authorise, and safely process every submission.

## Tables

Use tables for genuinely tabular relationships, not page layout. Mark header cells with `th` and appropriate `scope`, include a caption when it helps identify the table, and ensure the reading order remains meaningful.

```html
<table>
  <caption>Test results</caption>
  <thead>
    <tr><th scope="col">Suite</th><th scope="col">Status</th></tr>
  </thead>
  <tbody>
    <tr><th scope="row">Checkout</th><td>Passed</td></tr>
  </tbody>
</table>
```

## Accessibility

Start with native HTML before adding ARIA. Native controls already include semantics and interaction behaviour that custom widgets must reproduce.

Check that:

- all functionality works with a keyboard;
- focus order follows the visual and DOM order;
- controls have names and errors are associated with them;
- landmarks and headings make the page navigable;
- zoom and text resizing do not hide content;
- dynamic changes are communicated when necessary.

## Security and External Content

Escape untrusted content before inserting it into HTML. Do not build markup through unsafe string concatenation. Apply restrictive content-security policy and iframe permissions where appropriate. Use `rel="noopener"` when required by the link behaviour and avoid exposing sensitive data in URLs or markup.

## Testing and Validation

Validate markup, inspect the accessibility tree, and test with keyboard navigation, multiple viewport sizes, and at least one screen reader workflow for critical pages. Automated accessibility rules catch useful classes of problems but cannot determine whether content and interaction make sense.

## Readiness Checklist

You should be able to:

- build a valid document with meaningful landmarks and headings;
- choose native semantic elements before custom roles;
- create labelled, keyboard-usable forms with server-side validation;
- provide appropriate alternatives for images and media;
- mark up data tables accurately;
- explain DOM order, accessible names, and focus behaviour;
- test structure with validators, developer tools, and assistive technology.

## Official References

- [HTML reference](https://developer.mozilla.org/docs/Web/HTML)
- [HTML Living Standard](https://html.spec.whatwg.org/)
- [HTML accessibility](https://developer.mozilla.org/docs/Learn_web_development/Core/Accessibility/HTML)
- [Web Content Accessibility Guidelines](https://www.w3.org/WAI/standards-guidelines/wcag/)

Return to [Web Foundations](./README.md).
