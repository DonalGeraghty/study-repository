# Test Automation Tools

This section covers browser and application test-automation tools. Tool mechanics matter, but reliability depends equally on test scope, data ownership, environment control, diagnosability, and alignment with product risk.

## Guides

- [Playwright](./playwright.md) — modern browser automation, fixtures, isolation, diagnostics, and cross-browser testing.
- [Selenium](./selenium.md) — WebDriver-based browser automation across languages and browser implementations.
- [Cypress](./cypress.md) — browser-focused testing, command chains, network control, and debugging.

## Selection Dimensions

- Test runner and programming-language support
- Browser coverage and execution model
- Waiting, isolation, and parallel execution
- Network, API, and authentication support
- Diagnostics, reporting, and CI integration
- Appropriate use cases and important limitations

Do not choose solely from syntax preference. Build a short proof using representative authentication, test data, parallel execution, diagnostics, and CI constraints.

## Shared Principles

- Test observable behaviour at the lowest useful level.
- Make every test independent and deterministic.
- Prefer condition-based waiting to fixed delays.
- Treat accessibility semantics and stable test hooks as product interfaces.
- Retain enough failure evidence to diagnose CI without immediate reruns.
- Use retries to expose and classify flakiness, not redefine an unstable suite as healthy.

Return to the [Quality Engineering](../README.md) guide.
