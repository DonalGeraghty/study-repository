# Selenium

Selenium is a browser-automation ecosystem built around the W3C WebDriver standard. Language bindings send commands through a browser driver to a local or remote browser. Selenium provides automation primitives; the project must supply its test runner, assertions, data management, reporting, and framework conventions.

## WebDriver Architecture

```text
test code -> Selenium binding -> WebDriver endpoint -> browser
```

The endpoint may be a local driver or a remote Grid. Capabilities describe the requested browser session. Keep browser and driver management reproducible through Selenium Manager, containers, or a controlled grid image rather than relying on undocumented machine state.

## Sessions, Windows, and Frames

A WebDriver instance owns a browser session. Quit it after the test or suite scope that created it. Distinguish:

- browser windows and tabs, addressed by window handles;
- frames, which change the current browsing context;
- alerts, which require the alert interface;
- cookies and storage, which belong to browser state.

Return to the expected context after interacting with a frame or new window. Hidden context changes create misleading element failures later.

## Locators

Prefer stable, meaningful attributes and narrow locators. Accessible semantics and dedicated test attributes can be stronger contracts than styling classes or deep DOM paths.

```java
driver.findElement(By.id("email")).sendKeys("user@example.com");
driver.findElement(By.cssSelector("[data-testid='sign-in']")).click();
```

XPath is useful for relationships not expressed cleanly by another locator, but long absolute XPath expressions are fragile. A locator cannot compensate for an application without stable testability hooks.

## Waiting and Synchronisation

Web applications change asynchronously. An explicit wait polls for a meaningful condition:

```java
WebDriverWait wait = new WebDriverWait(driver, Duration.ofSeconds(10));
WebElement heading = wait.until(
        ExpectedConditions.visibilityOfElementLocated(By.cssSelector("h1")));
```

Avoid fixed sleeps: they are always longer than necessary when the system is fast and still fail when it is slower. Use implicit waits cautiously and do not mix wait strategies without understanding their combined timing. Re-find elements after DOM replacement rather than retaining stale references.

## Page and Component Objects

Page objects model services offered by a page; component objects model repeated regions. They should hide locator mechanics while keeping the test’s business intent visible.

```java
public final class LoginPage {
    private final WebDriver driver;

    public LoginPage(WebDriver driver) {
        this.driver = driver;
    }

    public void signIn(String email, String password) {
        driver.findElement(By.id("email")).sendKeys(email);
        driver.findElement(By.id("password")).sendKeys(password);
        driver.findElement(By.cssSelector("[data-testid='sign-in']")).click();
    }
}
```

Avoid inheritance-heavy base pages, wrappers for every WebDriver call, and objects that combine navigation, test data, assertions, and unrelated pages.

## Test Isolation and Data

A new browser session per test provides strong isolation but costs time. Reusing a session requires rigorous reset of cookies, storage, tabs, and application state. Whichever lifecycle is chosen, tests must remain independently executable.

Prepare data through controlled APIs or fixtures when setup through the UI is not under test. Use unique records for parallel workers and avoid one shared account whose state changes.

## Grid and Parallel Execution

Selenium Grid routes sessions to remote browser nodes. It supports distribution and browser diversity but introduces capacity, networking, versioning, artifact, and observability concerns.

The test runner—not WebDriver—usually controls test parallelism. Never share one driver instance concurrently between tests. Bound concurrency to available browser and backend capacity.

## Diagnostics

On failure, retain:

- the exception and complete stack trace;
- a screenshot and current URL;
- browser console logs where supported;
- relevant page source or DOM snapshot, with secrets removed;
- browser, driver, platform, test data, and build identifiers;
- Grid session and node details for remote execution.

Retries should classify flakes, not hide them. Distinguish product defects, test defects, environment failures, and capacity problems.

## Readiness Checklist

You should be able to:

- explain the WebDriver client, endpoint, session, and browser relationship;
- switch windows, frames, and alerts without losing context;
- select stable locators and explicit wait conditions;
- design cohesive page and component objects;
- isolate drivers, accounts, and server data under parallel execution;
- operate local and Grid-based browser sessions;
- diagnose failures with browser, driver, and environment evidence.

## Official References

- [Selenium documentation](https://www.selenium.dev/documentation/)
- [WebDriver](https://www.selenium.dev/documentation/webdriver/)
- [Waiting strategies](https://www.selenium.dev/documentation/webdriver/waits/)
- [Selenium Grid](https://www.selenium.dev/documentation/grid/)

Return to [Test Automation Tools](./README.md).
