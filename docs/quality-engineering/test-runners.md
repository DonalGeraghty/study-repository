# Test Runners and Assertion Libraries

A test runner discovers tests, prepares their lifecycle, executes them, records results, and returns a process status to local tooling or CI. Automation libraries drive a system; runners organise and report the tests that use them.

## Runners Used in the Projects

| Ecosystem | Runner or library | Typical role |
| --- | --- | --- |
| Python | `unittest` | standard-library unit and service tests |
| Python | pytest and pytest-playwright | fixtures, parametrisation, browser tests, and plugins |
| Java | JUnit Jupiter | unit tests and Playwright scenarios |
| Java | TestNG | grouped or parameterised API tests |
| JavaScript | Vitest | Vite-aligned unit and component tests |
| JavaScript | Node test runner | dependency-light tests for JavaScript modules |
| React | Testing Library | user-oriented DOM queries and interactions, run by a test runner |

## Selection Principles

Use the runner already established in a repository unless a migration has a clear benefit. Avoid mixing lifecycle models casually: fixtures, parallelism, retries, parameterisation, and extension mechanisms differ.

Assertions should explain the contract under test. Compare complete objects only when every field matters; otherwise make the important fields explicit. Failed assertions need enough expected and actual context to diagnose the problem without exposing secrets.

## Isolation and Fixtures

- Each test must be able to run alone and in any order.
- Keep setup near the scope that owns it: test, class/module, worker, or suite.
- Reset external state explicitly; a fresh browser context does not reset a database.
- Do not hide the scenario inside a large fixture graph.
- Make clocks, randomness, environment, and network dependencies controllable.
- Use parallel execution only when data and resource ownership support it.

## CI Behaviour

Pin runner and plugin versions, emit machine-readable results where the CI platform benefits, and return a failing exit status for test failures or collection errors. Treat retries as diagnostic evidence, not a way to redefine an unreliable test as passing.

## Project Connections

The repositories use every runner in the table: Java puzzle and browser projects use JUnit, an older API suite uses TestNG, the Flask APIs use `unittest`, Python browser suites use pytest, Nyx uses Vitest and Testing Library, and Aether uses Node's test runner.

## Related Guides

- [Software Testing](./testing.md)
- [Playwright](./test-automation/playwright.md)
- [Cypress](./test-automation/cypress.md)
- [GitHub Actions](../platform-engineering/ci-cd/github-actions.md)

Return to [Quality Engineering](./README.md).
