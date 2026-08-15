# Documentation Library

This is the canonical table of contents for the handbook. Use the subject indexes for a guided sequence or bookmark a leaf guide for a focused refresh. The repository [landing page](../README.md) explains the available study-session formats and cross-subject learning paths.

## Subject Indexes

| Subject | Focus |
| --- | --- |
| [Engineering Foundations](./engineering-foundations/README.md) | Collaborative engineering practices and foundational data concepts |
| [Programming](./programming/README.md) | Languages, frameworks, platforms, browser APIs, tooling, and integrations |
| [Software Design](./software-design/README.md) | Code structure, dependency design, patterns, and domain modelling |
| [Quality Engineering](./quality-engineering/README.md) | Quality strategy, interfaces, runners, and automation tools |
| [Platform Engineering](./platform-engineering/README.md) | Runtime, data, messaging, cloud, and delivery systems |

## Complete Catalogue

### Engineering Foundations

1. [Git](./engineering-foundations/git.md)
2. [Code Review](./engineering-foundations/code-review.md)
3. [Technology Stack](./engineering-foundations/technology-stack.md)
4. [Project Technology Inventory](./engineering-foundations/project-technology-inventory.md)
5. [Encoding](./engineering-foundations/encoding.md)
6. [Hashing](./engineering-foundations/hashing.md)
7. [Encryption](./engineering-foundations/encryption.md)

### Programming

#### [Languages](./programming/languages/README.md)

- [Java Study Guide](./programming/languages/java/README.md)
  1. [Memory Management](./programming/languages/java/memory-management.md)
  2. [Variables and Data Types](./programming/languages/java/variables.md)
  3. [Numbers and Precision](./programming/languages/java/numbers.md)
  4. [Math and Randomness](./programming/languages/java/math.md)
  5. [Strings and Text](./programming/languages/java/strings.md)
  6. [Collections Framework](./programming/languages/java/collections.md)
  7. [Exceptions and Resources](./programming/languages/java/exceptions-and-resources.md)
  8. [Generics](./programming/languages/java/generics.md)
  9. [Streams and Lambdas](./programming/languages/java/streams-and-lambdas.md)
  10. [Concurrency](./programming/languages/java/concurrency.md)
  11. [Modern Java](./programming/languages/java/modern-java.md)
- [Kotlin](./programming/languages/kotlin.md)
- [Python](./programming/languages/python.md)
- [R](./programming/languages/r.md)
- [C# and .NET](./programming/languages/csharp.md)
- [JavaScript and TypeScript](./programming/languages/javascript-typescript.md)
- [SQL](./programming/languages/sql.md)
- [PowerShell](./programming/languages/powershell.md)

#### Frameworks and Application Platforms

- [Frameworks and Libraries index](./programming/frameworks/README.md)
- [Spring and Spring Boot](./programming/frameworks/spring.md)
- [React](./programming/frameworks/react.md)
- [Flask](./programming/frameworks/flask.md)
- [Vue](./programming/frameworks/vue.md)
- [Express](./programming/frameworks/express.md)
- [Shiny for R](./programming/frameworks/shiny.md)
- [Application Platforms index](./programming/platforms/README.md)
- [Android](./programming/platforms/android.md)

#### [Web Foundations and Browser APIs](./programming/web/README.md)

- [HTML](./programming/web/html.md)
- [CSS](./programming/web/css.md)
- [Browser Storage, Canvas, and Push](./programming/web/browser-platform-apis.md)

#### [Development Tooling](./programming/tooling/README.md)

- [Node.js and npm](./programming/tooling/nodejs-and-npm.md)
- [Vite and Frontend Tooling](./programming/tooling/vite.md)
- [Maven and Gradle](./programming/tooling/jvm-build-tools.md)
- [Frontend Libraries](./programming/tooling/frontend-libraries.md)
- [Data Analysis and Visualisation](./programming/tooling/data-analysis-and-visualisation.md)

#### [External Integrations](./programming/integrations/README.md)

- [Generative AI APIs](./programming/integrations/generative-ai-apis.md)
- [Email and SMTP](./programming/integrations/email-and-smtp.md)

#### Coding Practice

- [Coding Challenges and Entry Template](./programming/coding-challenges/README.md)
- [LeetCode Practice Log](./programming/coding-challenges/leetcode/README.md)
- [Codewars Practice Log](./programming/coding-challenges/codewars/README.md)

### Software Design

1. [Object-Oriented Programming](./software-design/object-oriented-programming.md)
2. [SOLID Principles](./software-design/solid-principles.md)
3. [Design Patterns](./software-design/design-patterns.md)
4. [Domain-Driven Design](./software-design/domain-driven-design.md)

### Quality Engineering

1. [Software Testing](./quality-engineering/testing.md)
2. [REST APIs and HTTP Testing](./quality-engineering/rest-api.md)
3. [Test Runners and Assertion Libraries](./quality-engineering/test-runners.md)
4. [Test Automation Tools index](./quality-engineering/test-automation/README.md)
   - [Playwright](./quality-engineering/test-automation/playwright.md)
   - [Selenium](./quality-engineering/test-automation/selenium.md)
   - [Cypress](./quality-engineering/test-automation/cypress.md)

### Platform Engineering

#### Runtime, Data, Caching, and Messaging

- [Docker](./platform-engineering/docker.md)
- [Kubernetes](./platform-engineering/kubernetes.md)
- [Nginx](./platform-engineering/nginx.md)
- [MySQL](./platform-engineering/mysql.md)
- [Caching](./platform-engineering/caching.md)
- [Redis](./platform-engineering/redis.md)
- [Publish/Subscribe](./platform-engineering/pub-sub.md)
- [RabbitMQ](./platform-engineering/rabbitmq.md)
- [Amazon SNS](./platform-engineering/amazon-sns.md)
- [Amazon SQS](./platform-engineering/amazon-sqs.md)

#### [Cloud Platforms](./platform-engineering/cloud/README.md)

- [Amazon Web Services](./platform-engineering/cloud/aws.md)
- [Google Cloud Platform](./platform-engineering/cloud/gcp/README.md)
  - [Cloud Run](./platform-engineering/cloud/gcp/cloud-run.md)
  - [App Engine](./platform-engineering/cloud/gcp/app-engine.md)
  - [Cloud Build](./platform-engineering/cloud/gcp/cloud-build.md)
  - [Cloud Storage](./platform-engineering/cloud/gcp/cloud-storage.md)
  - [Firestore](./platform-engineering/cloud/gcp/firestore.md)
  - [Identity and Access Management](./platform-engineering/cloud/gcp/iam.md)
  - [Google Cloud Application Services](./platform-engineering/cloud/gcp/application-services.md)

#### [Continuous Integration and Delivery](./platform-engineering/ci-cd/README.md)

- [Jenkins](./platform-engineering/ci-cd/jenkins.md)
- [GitHub Actions](./platform-engineering/ci-cd/github-actions.md)

## Navigation Pattern

- The repository README owns study methods and cross-subject learning paths.
- This page owns the complete bookmarkable catalogue.
- Subject and subsection READMEs own local learning order.
- Leaf guides finish with related guides and a link to their parent index.

When adding a page, give it one conceptual home, link it from that parent index and this catalogue, and add cross-links only where they help explain a genuine relationship.

[Return to the repository home](../README.md).
