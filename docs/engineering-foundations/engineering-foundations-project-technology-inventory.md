---
tags:
  - engineering-foundations
---

# Project Technology Inventory

This inventory maps the coding and development repositories in the parent GitHub workspace to learning guides. It is a point-in-time coverage check based on source files, manifests, imports, build configuration, container files, and CI workflows.

## Scope Rules

Count a technology when executable source or active project configuration demonstrates its use. Do not treat a transitive package, generated artifact, cached IDE data, `node_modules`, or an unused declared dependency as proof by itself.

The inventory excludes `Achilles`, `Cookbook`, `elephant-routine`, and `hindi-learning` because they contain personal plans or written content rather than software implementation. `health-os` is included only for its PowerShell validation script and GitHub Actions workflow; its CSV data and prose are not being reclassified as an application.

## Repository Coverage

| Repository | Technology evidence | Learning coverage |
| --- | --- | --- |
| `CodingPuzzles` | Java, Maven, JUnit Jupiter | [Java](../programming/languages/java/README.md), [Maven and Gradle](../programming/tooling/tooling-jvm-build-tools.md), [Test Runners](../quality-engineering/quality-engineering-test-runners.md), [Coding Challenges](../programming/coding-challenges/README.md) |
| `Janus-Gate` | Python, Flask, Pydantic, JWT, Firestore, Cloud KMS, AI SDKs, Web Push, Docker, GitHub Actions, Cloud Run, Cloud Scheduler | [Python](../programming/languages/languages-python.md), [Flask](../programming/frameworks/frameworks-flask.md), [REST APIs](../quality-engineering/quality-engineering-rest-api.md), [Generative AI APIs](../programming/integrations/integrations-generative-ai-apis.md), [GCP Application Services](../platform-engineering/cloud/gcp/gcp-application-services.md), [Browser Platform APIs](../programming/web/web-browser-platform-apis.md), [Docker](../platform-engineering/platform-engineering-docker.md), [GitHub Actions](../platform-engineering/ci-cd/ci-cd-github-actions.md) |
| `NyxAI` | JavaScript/JSX, React, Vite, npm, React Router, Recharts, Motion, OGL, Vitest, Testing Library, Web Storage, GitHub Actions, Nginx container, Artifact Registry, Cloud Run | [JavaScript](../programming/languages/languages-javascript-typescript.md), [React](../programming/frameworks/frameworks-react.md), [Vite](../programming/tooling/tooling-vite.md), [Frontend Libraries](../programming/tooling/tooling-frontend-libraries.md), [Test Runners](../quality-engineering/quality-engineering-test-runners.md), [Browser Platform APIs](../programming/web/web-browser-platform-apis.md), [Nginx](../platform-engineering/platform-engineering-nginx.md), [Cloud Run](../platform-engineering/cloud/gcp/gcp-cloud-run.md) |
| `Shiny` | R, Shiny, ggplot2, Leaflet and spatial-map packages | [R](../programming/languages/languages-r.md), [Shiny](../programming/frameworks/frameworks-shiny.md), [Data Analysis and Visualisation](../programming/tooling/tooling-data-analysis-and-visualisation.md) |
| `aether` | JavaScript/JSX, React, Vite, npm, ESLint, Node test runner, Web Storage, Docker, Nginx, GitHub Actions, Artifact Registry, Cloud Run | [React](../programming/frameworks/frameworks-react.md), [Vite](../programming/tooling/tooling-vite.md), [Node.js and npm](../programming/tooling/tooling-nodejs-and-npm.md), [Test Runners](../quality-engineering/quality-engineering-test-runners.md), [Browser Platform APIs](../programming/web/web-browser-platform-apis.md), [Docker](../platform-engineering/platform-engineering-docker.md), [Nginx](../platform-engineering/platform-engineering-nginx.md), [GitHub Actions](../platform-engineering/ci-cd/ci-cd-github-actions.md) |
| `aurelius` | Kotlin, Android SDK, XML resources, Gradle Kotlin DSL, Android Gradle Plugin, GitHub Actions, APK/AAB and Google Play internal testing | [Kotlin](../programming/languages/languages-kotlin.md), [Android](../programming/platforms/platforms-android.md), [Maven and Gradle](../programming/tooling/tooling-jvm-build-tools.md), [GitHub Actions](../platform-engineering/ci-cd/ci-cd-github-actions.md) |
| `cypress-testing` | JavaScript, Node.js/npm, Cypress | [JavaScript](../programming/languages/languages-javascript-typescript.md), [Node.js and npm](../programming/tooling/tooling-nodejs-and-npm.md), [Cypress](../quality-engineering/test-automation/test-automation-cypress.md) |
| `flappy-ai` | HTML, CSS, JavaScript, Canvas, Web Audio, pointer events, `localStorage` | [HTML](../programming/web/web-html.md), [CSS](../programming/web/web-css.md), [JavaScript](../programming/languages/languages-javascript-typescript.md), [Browser Platform APIs](../programming/web/web-browser-platform-apis.md) |
| `health-os` | PowerShell CSV validator, GitHub Actions | [PowerShell](../programming/languages/languages-powershell.md), [GitHub Actions](../platform-engineering/ci-cd/ci-cd-github-actions.md), [Testing](../quality-engineering/quality-engineering-testing.md) |
| `irish-life` | Python, pytest, Playwright, Requests | [Python](../programming/languages/languages-python.md), [Test Runners](../quality-engineering/quality-engineering-test-runners.md), [Playwright](../quality-engineering/test-automation/test-automation-playwright.md), [REST APIs](../quality-engineering/quality-engineering-rest-api.md) |
| `janus-api` | Successor of Janus-Gate with the same stack plus workout endpoints | Same coverage as `Janus-Gate`; see the Flask, REST, AI integration, GCP, Web Push, Docker, and GitHub Actions guides above |
| `java-api-testing` | Java, Maven, TestNG, Apache HttpClient, JSON/Jackson, REST API testing | [Java](../programming/languages/java/README.md), [Maven and Gradle](../programming/tooling/tooling-jvm-build-tools.md), [Test Runners](../quality-engineering/quality-engineering-test-runners.md), [REST APIs](../quality-engineering/quality-engineering-rest-api.md) |
| `medusa` | Python, pytest, pytest-playwright, Playwright, page objects | [Python](../programming/languages/languages-python.md), [Test Runners](../quality-engineering/quality-engineering-test-runners.md), [Playwright](../quality-engineering/test-automation/test-automation-playwright.md), [Design Patterns](../software-design/software-design-design-patterns.md) |
| `penguin-spring-api` | Java, Spring Boot, Maven Wrapper, REST, Google Cloud Pub/Sub, App Engine | [Java](../programming/languages/java/README.md), [Spring](../programming/frameworks/frameworks-spring.md), [Maven and Gradle](../programming/tooling/tooling-jvm-build-tools.md), [REST APIs](../quality-engineering/quality-engineering-rest-api.md), [Pub/Sub](../platform-engineering/platform-engineering-pub-sub.md), [App Engine](../platform-engineering/cloud/gcp/gcp-app-engine.md) |
| `playwright-crawler-we-buy` | Python, Playwright, pytest, pandas, CSV, SMTP email, dotenv | [Python](../programming/languages/languages-python.md), [Playwright](../quality-engineering/test-automation/test-automation-playwright.md), [Test Runners](../quality-engineering/quality-engineering-test-runners.md), [Data Analysis and Visualisation](../programming/tooling/tooling-data-analysis-and-visualisation.md), [Email and SMTP](../programming/integrations/integrations-email-and-smtp.md) |
| `playwright-java` | Java, Maven, Playwright, JUnit Jupiter, Lombok, page objects | [Java](../programming/languages/java/README.md), [Maven and Gradle](../programming/tooling/tooling-jvm-build-tools.md), [Playwright](../quality-engineering/test-automation/test-automation-playwright.md), [Test Runners](../quality-engineering/quality-engineering-test-runners.md), [Design Patterns](../software-design/software-design-design-patterns.md) |
| `r-shiny-clustering` | R, Shiny, ggplot2, PCA and clustering | [R](../programming/languages/languages-r.md), [Shiny](../programming/frameworks/frameworks-shiny.md), [Data Analysis and Visualisation](../programming/tooling/tooling-data-analysis-and-visualisation.md) |
| `r-shiny-explore-data` | R, Shiny, ggplot2, CSV/TSV input, CSS | [R](../programming/languages/languages-r.md), [Shiny](../programming/frameworks/frameworks-shiny.md), [Data Analysis and Visualisation](../programming/tooling/tooling-data-analysis-and-visualisation.md), [CSS](../programming/web/web-css.md) |
| `shtormscsgo` | JavaScript, Vue 3, Vue Router, Vue CLI, Babel, ESLint, BootstrapVue | [JavaScript](../programming/languages/languages-javascript-typescript.md), [Vue](../programming/frameworks/frameworks-vue.md), [Node.js and npm](../programming/tooling/tooling-nodejs-and-npm.md), [Frontend Libraries](../programming/tooling/tooling-frontend-libraries.md) |
| `tododos-express-api` | Node.js, Express, CORS, MySQL, Docker | [Node.js and npm](../programming/tooling/tooling-nodejs-and-npm.md), [Express](../programming/frameworks/frameworks-express.md), [REST APIs](../quality-engineering/quality-engineering-rest-api.md), [MySQL](../platform-engineering/platform-engineering-mysql.md), [Docker](../platform-engineering/platform-engineering-docker.md) |
| `weathercraft-farming` | HTML, CSS, JavaScript modules, browser audio, SVG/bitmap assets, Python asset helper with Pillow and NumPy | [HTML](../programming/web/web-html.md), [CSS](../programming/web/web-css.md), [JavaScript](../programming/languages/languages-javascript-typescript.md), [Browser Platform APIs](../programming/web/web-browser-platform-apis.md), [Python](../programming/languages/languages-python.md), [Data Analysis and Visualisation](../programming/tooling/tooling-data-analysis-and-visualisation.md) |

## Maintenance

Repeat this check when projects are added or substantially reworked. Prefer evidence from active imports and configuration, add a focused section to an existing guide when concepts belong together, and create a dedicated guide when a technology has its own development or operational model.

## Interview Questions

> [!question] Interview Questions
> - What counts as evidence that a repository actually uses a technology, versus a false positive like a transitive dependency?
> - Why would you exclude a repository from a technology inventory even though it lives in the same workspace?
> - How would you decide between adding a section to an existing guide and creating a brand-new one for a technology?
> - Why is a technology inventory a point-in-time snapshot rather than a permanent record, and how would you keep it trustworthy over time?

Return to [Engineering Foundations](./README.md).
