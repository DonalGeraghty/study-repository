---
tags:
  - programming/web
---

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

## Worked Prediction: A Form That Looks Correct

A form has a visible label beside `<input id="email" type="email" required>` and a button labelled Preview with no `type`. Predict two failures before changing the markup.

**Check your reasoning:** An `id` alone does not supply a submitted field name; the input needs `name="email"`. A normal button inside a form defaults to submitting it, so a Preview action needs `type="button"`. The label must wrap the input or use `for="email"`; visual proximity is insufficient.

Test by submitting with the keyboard, inspecting the request's form data, and checking the input's accessible name. Native validation can prevent an ordinary invalid submission but does not protect the server from a direct HTTP request. A complete answer connects document semantics, browser behaviour, and server validation.

## Interview Questions

> [!question] Interview Questions
> - Why would you choose a native semantic element over a `div` with a custom ARIA role?
> - What makes a form control's label actually associated with it, versus just visually nearby?
> - Why does client-side form validation need a server-side check behind it?
> - How does DOM order affect both keyboard focus order and screen-reader reading order?

## Answer Notes

1. Native elements provide built-in semantics, keyboard behaviour and browser integration. Adding an ARIA role changes exposed semantics but does not automatically implement the behaviour a custom widget needs.

2. Associate a label's for attribute with the control's matching id, or nest the control inside its label where appropriate. Nearby text or a placeholder alone is not equivalent to a properly associated label.

3. A caller can bypass the browser and send arbitrary requests. Client validation improves feedback, while the server must independently validate input and enforce business and security rules.

4. The DOM provides the default reading sequence and much of the keyboard focus sequence. Visual reordering can create a mismatch for keyboard and screen-reader users; keep a logical source order and avoid using positive tabindex to patch it.

## Official References

- [HTML reference](https://developer.mozilla.org/docs/Web/HTML)
- [HTML Living Standard](https://html.spec.whatwg.org/)
- [HTML accessibility](https://developer.mozilla.org/docs/Learn_web_development/Core/Accessibility/HTML)
- [Web Content Accessibility Guidelines](https://www.w3.org/WAI/standards-guidelines/wcag/)

Return to [Web Foundations](./README.md).
