# Technology Stack

A technology stack is the collection of languages, runtimes, frameworks, libraries, data stores, test tools, delivery systems, and operational services used to build and run a product.

A stack should be selected from product and engineering needs rather than popularity. The important question is not “Which tool is best?” but “Which option fits this system, team, and risk profile?”

## Stack Layers

| Layer | Purpose | Examples |
| --- | --- | --- |
| Language and runtime | Expresses and executes code | Java and the JVM, C# and .NET, TypeScript and Node.js |
| Application framework | Provides application conventions and infrastructure | Spring Boot, ASP.NET Core, React, Angular |
| Build and dependencies | Compiles, tests, packages, and resolves libraries | Maven, Gradle, npm, pnpm |
| Data | Persists and retrieves state | PostgreSQL, document stores, caches, object storage |
| Testing | Supplies runners, automation APIs, assertions, and environments | JUnit, Selenium, Playwright, Cypress, Testcontainers |
| Delivery | Builds and promotes changes | GitHub Actions, Jenkins, artifact and image registries |
| Runtime platform | Packages, deploys, and manages workloads | Docker, Kubernetes, cloud services |
| Observability | Exposes system behaviour and health | Logs, metrics, traces, OpenTelemetry |

These layers are related but not interchangeable. SQL is a language, PostgreSQL is a database, JDBC is a Java database API, and Hibernate is an object-relational mapping tool.

## Choosing a Stack

Evaluate the complete lifecycle, not only how quickly a tutorial starts.

### Product and Domain

- What behaviour, throughput, latency, and availability are required?
- Is the product web, mobile, embedded, data-intensive, or event-driven?
- Are there regulatory, privacy, accessibility, or data-residency constraints?
- Which failures have the highest business impact?

### Team and Organisation

- Which technologies can the team operate as well as develop?
- Is expertise available for debugging production failures?
- How easily can people be hired, trained, and supported?
- Does the organisation already provide secure, maintained platform capabilities?

### Engineering and Operation

- Is the ecosystem mature for the required integrations?
- Are release cadence and compatibility policies clear?
- Can components be tested without fragile shared environments?
- Are diagnostics, profiling, and security tools available?
- What are the infrastructure, licensing, migration, and exit costs?
- What must be patched, backed up, scaled, and monitored?

Build a small proof of concept for the riskiest unknowns. A feature checklist rarely reveals debugging quality, upgrade difficulty, or behaviour under failure.

## Languages

### Java

Java is common in backend services, enterprise systems, Android-related ecosystems, and test automation. Its JVM ecosystem includes mature build, testing, profiling, and observability support.

Relevant tools include Maven or Gradle, JUnit Jupiter or TestNG, Spring Boot, Selenium, Playwright for Java, REST Assured, and Testcontainers. Use the repository's [Java guide](../programming/languages/java/README.md) for language and JVM fundamentals.

### C# and .NET

C# and .NET are used for web services, enterprise systems, desktop software, cloud workloads, and test automation. Typical tools include ASP.NET Core, xUnit.net, NUnit, MSTest, Playwright for .NET, and Selenium.

### JavaScript and TypeScript

JavaScript runs in browsers and in server-side runtimes such as Node.js. TypeScript adds static type checking and compiles to JavaScript.

TypeScript can improve contracts and refactoring in a growing application or automation suite. It does not replace runtime validation for network responses, configuration, or user input.

Typical tools include React or Angular for web interfaces, Playwright Test or Cypress for browser testing, and npm or pnpm for dependency management.

### SQL

Engineers working with relational data should understand joins, aggregation, constraints, normalization, transactions, isolation, indexes, query plans, migrations, and safe test-data cleanup.

An ORM reduces repetitive mapping but does not remove the need to understand SQL or database behaviour.

## Application Frameworks

### Backend

Frameworks such as Spring Boot and ASP.NET Core provide routing, configuration, dependency injection, security integration, and production diagnostics.

Evaluate their resource requirements, update policy, integrations, test support, deployment model, observability, and how much framework code leaks into business logic.

### Frontend

React is a UI library, while Angular is a broader application framework. Both can support production web applications but make different choices about routing, state, forms, testing, and project structure.

Choose using application complexity, team experience, accessibility requirements, ecosystem health, rendering needs, and maintenance cost—not a benchmark for a trivial component.

## Test Automation Stack

An automation stack should support the test strategy. It should not force every risk through a browser.

| Need | Possible tools | Notes |
| --- | --- | --- |
| Java unit and component tests | JUnit Jupiter, AssertJ, Mockito | Keep most tests fast and deterministic |
| .NET unit and component tests | xUnit.net, NUnit, MSTest | Follow the product codebase's conventions |
| API tests | REST Assured, Playwright API testing, HTTP clients | Assert status, headers, schema, and behaviour |
| Browser tests | Selenium, Playwright, Cypress | Select from language, browser, debugging, and execution needs |
| Integration dependencies | Testcontainers, Docker Compose | Prefer isolated, reproducible dependencies |
| Service virtualization | WireMock, MockServer | Model relevant provider behaviour and failures |
| Contract tests | Pact or provider verification | Complements rather than replaces integration testing |
| Performance tests | k6, Gatling, JMeter | Derive workloads and thresholds from real needs |
| Mobile tests | Appium and platform-native tools | Include devices, permissions, networks, and lifecycle risks |
| Accessibility checks | axe-core plus manual evaluation | Automation finds only part of accessibility risk |

Avoid overlapping tools without a reason. Two browser frameworks double training, infrastructure, and maintenance unless they serve distinct needs.

## Selenium, Playwright, or Cypress?

There is no universal winner.

| Tool | Strong fit | Important considerations |
| --- | --- | --- |
| Selenium WebDriver | Teams needing the WebDriver standard, broad language choice, or an established Selenium ecosystem | Runner, assertions, reporting, and framework structure are selected separately |
| Playwright Test | TypeScript or JavaScript teams wanting an integrated runner, isolated browser contexts, tracing, and web-first assertions | Playwright Test is the Node.js runner; other languages use the Playwright library with their own runner |
| Cypress | JavaScript or TypeScript web teams valuing its interactive workflow, command log, retry model, and component integrations | Its queued command model differs from promises and must be learned explicitly |

Evaluate each option with a representative workflow:

1. Authenticate without repeating slow UI setup.
2. Interact with a dynamic page.
3. Capture diagnostics for a CI-only failure.
4. Execute the required browser matrix.
5. Run tests in parallel with isolated data.
6. Upgrade the tool and browser versions.

Built-in waiting reduces timing problems but cannot repair shared test data, order dependence, ambiguous assertions, or an unobservable application.

## A Practical Java SDET Stack

This is a coherent example, not a mandatory template:

| Concern | Selection |
| --- | --- |
| Language | Java |
| Build | Maven or Gradle |
| Test runner | JUnit Jupiter |
| Assertions | JUnit assertions or AssertJ |
| API automation | REST Assured or a standard HTTP client |
| Browser automation | Selenium or Playwright for Java |
| Test doubles | Mockito at narrow code boundaries; WireMock for HTTP |
| Integration environment | Testcontainers |
| Reporting | JUnit XML plus CI-native reports; richer reporting when users need it |
| Delivery | GitHub Actions or the organisation's existing CI system |
| Packaging | Docker where reproducible services or dependencies justify it |

The product and automation languages do not have to match. Matching can improve code sharing and developer contribution; a different language may offer a stronger specialist tool or better team fit.

## A Practical TypeScript Browser Stack

| Concern | Selection |
| --- | --- |
| Language | TypeScript |
| Package management | npm or pnpm with a committed lockfile |
| Browser runner | Playwright Test or Cypress |
| API setup and checks | The selected runner's HTTP facilities or a focused client |
| Static checks | TypeScript compiler, ESLint, and a formatter |
| Reporting | Built-in HTML and JUnit-compatible CI output |
| Delivery | CI jobs using a pinned Node.js release line |
| Runtime dependencies | Docker Compose or Testcontainers for Node.js where useful |

Keep browser abstractions expressed as user-facing tasks and reusable components. See [Design Patterns](../software-design/design-patterns.md) for Page Object guidance.

## Reference Delivery Flow

```text
developer change
      |
      v
format + static analysis + unit tests
      |
      v
component and focused integration tests
      |
      v
build immutable artifact or container image
      |
      v
deploy to a production-like environment
      |
      v
targeted end-to-end, contract, and non-functional checks
      |
      v
progressive release + production observation
```

The exact gates should reflect risk and feedback time.

### Pull Request

- formatting, linting, and static analysis;
- unit and fast component tests;
- focused integration and contract checks;
- build and dependency-policy validation.

### Main Branch or Release Candidate

- broader integration suites;
- selected browser and platform coverage;
- packaging and deployment verification;
- security and infrastructure checks.

### Scheduled or Pre-release

- expensive compatibility matrices;
- performance and resilience experiments;
- long-running or specialised security tests;
- maintenance checks that do not need to block every commit.

Do not hide a failing test behind unlimited retries. A retry can collect evidence for intermittent behaviour, but repeated instability should be owned, investigated, and fixed.

## Example Repository Layout

```text
project/
|-- src/
|   |-- main/
|   +-- test/
|-- ui-tests/
|   |-- pages/
|   |-- components/
|   |-- fixtures/
|   +-- specs/
|-- docker-compose.yml
|-- pom.xml or build.gradle
|-- package.json
+-- .github/workflows/
```

Use only the directories relevant to the project. A single-language service should not acquire a second build system merely to match this example.

## Dependency and Version Management

- Commit reproducible dependency manifests and lockfiles.
- Pin CI actions and container bases according to the organisation's security policy.
- Use supported runtime release lines and document the upgrade policy.
- Automate dependency-update proposals, but review release notes and test the result.
- Remove unused dependencies and plugins.
- Keep credentials outside source control.
- Generate a software bill of materials when supply-chain or compliance needs justify it.
- Rebuild artifacts regularly so operating-system and runtime fixes are incorporated.

Avoid `latest` tags and unbounded version ranges in reproducible delivery paths.

## Observability for Testing

Testability includes being able to understand failures. A useful stack provides:

- structured logs with correlation identifiers;
- metrics for traffic, errors, latency, and saturation;
- distributed traces across service boundaries;
- health and readiness signals;
- build artifacts such as screenshots, traces, logs, and reports;
- safe mechanisms to inspect test data and asynchronous processing.

Observability assertions can be valuable, but avoid tests coupled to incidental log wording.

## Common Stack Mistakes

- Selecting tools before understanding product risks.
- Treating a long tool list as architecture.
- Introducing several tools that solve the same problem.
- Automating all checks at the slowest system level.
- Sharing mutable environments and test accounts across parallel runs.
- Allowing framework details to dominate domain design.
- Choosing Kubernetes for a deployment that does not need cluster orchestration.
- Ignoring upgrades until a runtime or library is unsupported.
- Buying a reporting platform before deciding who needs which information.
- Optimising developer convenience while ignoring production operation.

## Explaining a Stack in an Interview

1. State the product, users, architecture, and main risks.
2. Describe the selected stack by layer.
3. Explain two or three important choices and their constraints.
4. Describe the test feedback provided before and after deployment.
5. Explain diagnostics, test data, and environment management.
6. Name an alternative and why it was not selected.
7. Share evidence such as execution time, defect detection, stability, or operational cost.

“We used Playwright because it is modern” is weak. “We selected it after a proof of concept showed reliable multi-browser execution, useful traces for CI failures, and good TypeScript alignment” is a defensible engineering explanation.

## Review Checklist

- [ ] Every tool has a clear responsibility and owner.
- [ ] The stack supports the product's highest risks.
- [ ] Local and CI execution are reproducible.
- [ ] Fast feedback is separated from expensive coverage.
- [ ] Test data and state are isolated.
- [ ] Failure diagnostics are retained and accessible.
- [ ] Dependencies, runtimes, and images have upgrade policies.
- [ ] Security and secret management are built into delivery.
- [ ] Operational complexity is justified.
- [ ] Important choices and rejected alternatives are documented.

## Official Starting Points

- [Java](https://dev.java/)
- [.NET](https://learn.microsoft.com/dotnet/)
- [TypeScript](https://www.typescriptlang.org/docs/)
- [Spring Boot](https://docs.spring.io/spring-boot/)
- [ASP.NET Core](https://learn.microsoft.com/aspnet/core/)
- [JUnit](https://docs.junit.org/)
- [Selenium](https://www.selenium.dev/documentation/)
- [Playwright](https://playwright.dev/docs/intro)
- [Cypress](https://docs.cypress.io/)
- [Testcontainers for Java](https://java.testcontainers.org/)
- [OpenTelemetry](https://opentelemetry.io/docs/)

## Related Guides

- [Encoding](./encoding.md)
- [Encryption](./encryption.md)
- [Hashing](./hashing.md)
- [Testing](../quality-engineering/testing.md)
- [Java](../programming/languages/java/README.md)
- [Software Design](../software-design/README.md)
- [Docker](../platform-engineering/docker.md)
- [Kubernetes](../platform-engineering/kubernetes.md)

Return to [Engineering Foundations](./README.md).
