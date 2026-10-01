---
tags:
  - quality-engineering
---

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

## Worked Parameterised Test

```python
import pytest


def shipping_cost(order_total):
    if order_total < 0:
        raise ValueError("order total cannot be negative")
    return 0 if order_total >= 50 else 5


@pytest.mark.parametrize(
    ("order_total", "expected_cost"),
    [
        (0, 5),
        (49.99, 5),
        (50, 0),
        (100, 0),
    ],
    ids=["zero", "below-boundary", "at-boundary", "above-boundary"],
)
def test_shipping_cost_boundaries(order_total, expected_cost):
    assert shipping_cost(order_total) == expected_cost


def test_shipping_cost_rejects_negative_total():
    with pytest.raises(ValueError, match="cannot be negative"):
        shipping_cost(-0.01)
```

Parameterisation makes the boundary cases visible without hiding the assertion in a loop. Descriptive IDs improve CI output. The separate exception test verifies both the failure type and the useful part of its contract.

## Isolation and Fixtures

- Each test must be able to run alone and in any order.
- Keep setup near the scope that owns it: test, class/module, worker, or suite.
- Reset external state explicitly; a fresh browser context does not reset a database.
- Do not hide the scenario inside a large fixture graph.
- Make clocks, randomness, environment, and network dependencies controllable.
- Use parallel execution only when data and resource ownership support it.

## CI Behaviour

Pin runner and plugin versions, emit machine-readable results where the CI platform benefits, and return a failing exit status for test failures or collection errors. Treat retries as diagnostic evidence, not a way to redefine an unreliable test as passing.

## Common Failure Modes

- a shared fixture that makes tests pass only in one order;
- asserting every field of a response when only a stable subset is the contract;
- catching an exception inside the test and forgetting to fail when none is raised;
- using sleeps to coordinate asynchronous behaviour;
- enabling parallelism before isolating data and ports;
- marking tests as retriable without retaining first-attempt evidence;
- returning a zero process status after collection or setup failed.

## Project Connections

The repositories use every runner in the table: Java puzzle and browser projects use JUnit, an older API suite uses TestNG, the Flask APIs use `unittest`, Python browser suites use pytest, Nyx uses Vitest and Testing Library, and Aether uses Node's test runner.

## Worked Prediction: Would This Test Catch a Bug?

Change the shipping implementation from `order_total >= 50` to `order_total > 50`. Predict which existing case fails before running the suite.

**Check your reasoning:** The `at-boundary` case must fail because exactly 50 now costs 5. If every test passes, investigate whether the changed code was imported, the test was discovered, or the expected value copied the same faulty condition. Deliberately perturbing a rule in a scratch copy can reveal a weak test without treating line coverage as proof.

Next, imagine a session-scoped fixture returns one mutable list. Test A appends to it and test B expects it empty. Fresh assertion objects do not isolate the shared fixture; use appropriate fixture scope or reset owned state. Confirm each test independently and in a different order before enabling parallel execution. Check discovered test counts as well as the process exit status.

## Interview Questions

> [!question] Interview Questions
> - Why shouldn't you casually mix lifecycle models between test runners in one suite?
> - How would you design a parameterised test so boundary cases are visible instead of hidden inside a loop?
> - How would you make a suite's setup safe for parallel execution when tests currently depend on running in a fixed order?
> - What's wrong with treating retries as a way to make an unreliable test "pass," and how would you tell a flaky test from a real regression?

## Answer Notes

1. Runners own discovery, fixtures, hooks, scheduling and reporting. Mixing assumptions about those lifecycles can leave setup unexecuted, state shared incorrectly or tests undiscovered; use one clear execution model per suite.

2. Make each boundary input and expected outcome a separately named parameterised case. Independent reporting shows exactly which case failed and allows the runner to execute and manage cases individually.

3. Remove dependencies on earlier tests, create uniquely scoped data, and make setup and cleanup own their resources. Use per-test or per-worker fixtures according to mutation needs, and verify tests work alone and in different orders.

4. A successful retry does not explain the first failure and can conceal timing or isolation problems. Preserve failure evidence, look for reproducibility and environmental patterns, and fix the cause rather than counting retries as reliability.

## Related Guides

- [Software Testing](./testing.md)
- [Playwright](./test-automation/playwright.md)
- [Cypress](./test-automation/cypress.md)
- [GitHub Actions](../platform-engineering/ci-cd/github-actions.md)

Return to [Quality Engineering](./README.md).
