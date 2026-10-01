---
tags:
  - moc
---

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
| Review delivery and runtime systems | [Platform Engineering](./docs/platform-engineering/README.md) |

## Choose a Study Session

### Five-minute recall

1. Read the guide's opening definition and mental model.
2. Explain the topic aloud without looking at the page.
3. Answer one interview question before checking its numbered answer note.
4. Write down one fact that did not come back immediately.

### Fifteen-minute refresh

1. Skim the headings to recover the map of the topic.
2. Trace one worked example line by line.
3. Change an input, constraint, or failure condition and predict the result.
4. Answer two practice questions from memory.

### Thirty-to-sixty-minute practice

1. Read one guide in order.
2. Run or re-create an example in a scratch project.
3. Complete a worked prediction or reconstruct an example without copying the solution.
4. Compare the result with the guide and record what changed in your understanding.

Active recall matters more than rereading. A useful session ends with an explanation, prediction, small implementation, or decision—not merely a completed page.

## Learn, Remember, and Test Yourself

Use each guide as a cycle rather than a page to finish:

1. **Learn:** explain the opening mental model, trace an example, and identify the rule that makes it work.
2. **Remember:** close the guide and reconstruct the key distinctions or diagram from memory. Prefer a concrete contrast, such as a queue acknowledgement versus a database commit, to memorising a definition alone.
3. **Test:** predict a worked example's result before reading its explanation, then answer the interview questions aloud. Change one constraint and check whether your answer still holds.
4. **Correct:** compare with the numbered answer notes, record the specific missed rule, and try a different example. Recognising the answer after reading it is not yet independent recall.

Each guide has four interview questions followed by four answer notes in the same order. Attempt the questions before scrolling to the notes. Use the notes as checkpoints: explain the idea in your own words, and return to the main explanation when you need more detail. For design questions, a different answer can be sound if its assumptions and trade-offs fit the problem.

Use this simple self-marking scale for an answer:

| Score | Evidence |
| --- | --- |
| 0 | Could not answer, or the explanation was incorrect |
| 1 | Remembered a definition but needed hints to apply it |
| 2 | Explained the mechanism and solved the example independently |
| 3 | Also explained a failure case, trade-off, and way to verify the claim |

For design questions, compare reasoning and constraints rather than looking for one prescribed architecture. Revisit missed questions in your next session, then after a few days and again after a longer gap; adjust the interval from what you can actually recall. Mix a previous topic into a new session instead of rereading one guide until its wording feels familiar.

A scratch note can be as small as `topic | question missed | corrected rule | next attempt`. No plugin or additional frontmatter is needed. Keep personal answers separate from the guide so the next attempt remains a real test.

## Browse by Subject

| Subject | What it covers |
| --- | --- |
| [Engineering Foundations](./docs/engineering-foundations/README.md) | Git, code review, stack decisions, and core data-protection concepts |
| [Programming](./docs/programming/README.md) | Languages, frameworks, platforms, browser foundations, tooling, integrations, and coding practice |
| [Software Design](./docs/software-design/README.md) | OOP, SOLID, design patterns, and Domain-Driven Design |
| [Quality Engineering](./docs/quality-engineering/README.md) | Testing strategy, REST APIs, test runners, and browser automation |
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
7. [Terraform](./docs/platform-engineering/terraform.md)
8. [Kubernetes](./docs/platform-engineering/kubernetes.md)

## Reading Conventions

- Code blocks are examples to inspect, run, and alter; they are not production-ready templates for every context.
- Diagrams show a mental model, not every implementation detail.
- **Common failure modes** explain where an apparently correct approach breaks down.
- **Worked predictions** provide an expected result and reasoning; attempt them before reading the explanation.
- **Interview Questions** test whether you can explain mechanisms, apply them, and discuss failure cases without hints.
- **Answer Notes** give concise answers or reasoning checkpoints in question order so you can check an attempt without memorising a script.
- **Related guides** connect a topic to its language, design, testing, and operational context.

For the full table of contents, go to the [Documentation Library](./docs/README.md).

---

Created and maintained with assistance from Antigravity AI, ChatGPT, and Mistral Vibe.
