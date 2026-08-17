# Technical Study and Interview Handbook

This repository is a personal technical handbook for two kinds of learning:

- **quick refreshes** when a technology has not been used for a while;
- **deliberate practice** for engineering work and technical interviews.

The material is organised by subject rather than by a single fixed curriculum. Each subject has an index, each guide links back to its parent, and the [Documentation Library](./docs/README.md) bookmarks every study page.

## Start Here

| Goal | Best starting point |
| --- | --- |
| Find a particular technology | [Complete documentation catalogue](./docs/README.md#complete-catalogue) |
| Rebuild core engineering knowledge | [Engineering Foundations](./docs/engineering-foundations/README.md) |
| Refresh a language or framework | [Programming](./docs/programming/README.md) |
| Review testing strategy and tools | [Quality Engineering](./docs/quality-engineering/README.md) |
| Learn how scalable systems are designed | [System Design](./docs/system-design/README.md) |
| Review delivery and runtime systems | [Platform Engineering](./docs/platform-engineering/README.md) |

## Choose a Study Session

### Five-minute recall

1. Read the guide's opening definition and mental model.
2. Explain the topic aloud without looking at the page.
3. Check the common failure modes or readiness checklist.
4. Write down one fact that did not come back immediately.

### Fifteen-minute refresh

1. Skim the headings to recover the map of the topic.
2. Trace one worked example line by line.
3. Change an input, constraint, or failure condition and predict the result.
4. Answer two practice questions from memory.

### Thirty-to-sixty-minute practice

1. Read one guide in order.
2. Run or re-create an example in a scratch project.
3. Complete a practice exercise without copying the solution.
4. Compare the result with the guide and record what changed in your understanding.

Active recall matters more than rereading. A useful session ends with an explanation, prediction, small implementation, or decision—not merely a completed page.

## Browse by Subject

| Subject | What it covers |
| --- | --- |
| [Engineering Foundations](./docs/engineering-foundations/README.md) | Git, code review, stack decisions, and core data-protection concepts |
| [Programming](./docs/programming/README.md) | Languages, frameworks, platforms, browser foundations, tooling, integrations, and coding practice |
| [Software Design](./docs/software-design/README.md) | OOP, SOLID, design patterns, and Domain-Driven Design |
| [Quality Engineering](./docs/quality-engineering/README.md) | Testing strategy, REST APIs, test runners, and browser automation |
| [System Design](./docs/system-design/README.md) | Requirements, scale estimation, architecture, reliability, and distributed-system trade-offs |
| [Platform Engineering](./docs/platform-engineering/README.md) | Containers, orchestration, data platforms, messaging, cloud, and CI/CD |

## Suggested Learning Paths

### Core engineering path

1. [Git](./docs/engineering-foundations/git.md)
2. [Programming Languages](./docs/programming/languages/README.md)
3. [Software Design](./docs/software-design/README.md)
4. [Software Testing](./docs/quality-engineering/testing.md)
5. [REST APIs](./docs/quality-engineering/rest-api.md)
6. [Code Review](./docs/engineering-foundations/code-review.md)
7. [Technology Stack](./docs/engineering-foundations/technology-stack.md)

### Delivery and platform path

1. [Node.js and npm](./docs/programming/tooling/nodejs-and-npm.md) or [Maven and Gradle](./docs/programming/tooling/jvm-build-tools.md)
2. [Docker](./docs/platform-engineering/docker.md)
3. [Continuous Integration and Delivery](./docs/platform-engineering/ci-cd/README.md)
4. [Caching](./docs/platform-engineering/caching.md)
5. [Publish/Subscribe](./docs/platform-engineering/pub-sub.md)
6. [Cloud Platforms](./docs/platform-engineering/cloud/README.md)
7. [Kubernetes](./docs/platform-engineering/kubernetes.md)

### System design path

1. [REST APIs](./docs/quality-engineering/rest-api.md)
2. [MySQL](./docs/platform-engineering/mysql.md)
3. [Caching](./docs/platform-engineering/caching.md)
4. [Publish/Subscribe](./docs/platform-engineering/pub-sub.md)
5. [System Design](./docs/system-design/README.md)
6. Practise the URL shortener, rate limiter, notification service, and chat application exercises

## Reading Conventions

- Code blocks are examples to inspect, run, and alter; they are not production-ready templates for every context.
- Diagrams show a mental model, not every implementation detail.
- **Common failure modes** explain where an apparently correct approach breaks down.
- **Practice** and **readiness** sections turn recognition into recall.
- **Related guides** connect a topic to its language, design, testing, and operational context.

For the full table of contents, go to the [Documentation Library](./docs/README.md).

---

Created and maintained with assistance from Antigravity AI, ChatGPT, and Mistral Vibe.
