# Code Review

Code review is a structured examination of a proposed change by someone other than its author. Its purpose is to improve the codebase and reduce delivery risk through shared technical judgment—not to demonstrate that the reviewer could have written the code differently.

A good review considers the change's behaviour, design, tests, security, operability, and long-term maintenance. Automated tools support this work, but they do not replace understanding the requirement or reasoning about system behaviour.

## Core Principles

### Improve Overall Code Health

The target is not perfect code. A change should be approved when it safely fulfils its purpose and improves or preserves the overall health of the system. Reviewers should distinguish important defects from personal preferences and unrelated improvements.

### Review the Change, Not the Person

- Discuss observable code behaviour and engineering trade-offs.
- Assume positive intent.
- Explain why a concern matters.
- Ask questions when context may be missing.
- Praise effective solutions as well as identifying problems.
- Move prolonged or sensitive disagreement into a direct conversation, then record the conclusion in the review.

### Match Depth to Risk

A configuration typo and an authentication change do not deserve the same level of scrutiny. Increase review depth for changes involving:

- authentication, authorisation, secrets, or personal data;
- money, permissions, safety, or compliance;
- concurrency, distributed transactions, or asynchronous processing;
- database schemas and irreversible migrations;
- shared libraries, public APIs, or widely used interfaces;
- deployment, rollback, or infrastructure behaviour;
- complex algorithms or performance-critical paths.

### Keep Changes Reviewable

Small, focused changes are easier to understand, test, review, merge, and roll back. Avoid mixing functional changes with large formatting passes, dependency upgrades, or unrelated refactoring.

There is no universal maximum line count. If a reviewer cannot explain the purpose and risk of a change with reasonable confidence, the change may need to be split or supported by stronger design documentation.

## Responsibilities

### The Author

The author is responsible for making the change reviewable:

- understand and self-review the complete diff;
- explain the problem, approach, risks, and alternatives;
- link the relevant requirement, incident, or decision;
- keep the scope focused;
- include suitable tests and verification evidence;
- identify migrations, flags, compatibility concerns, and rollback steps;
- remove debugging code, generated noise, and unrelated changes;
- respond to every actionable comment;
- request reviewers with the necessary code ownership or domain expertise.

### The Reviewer

The reviewer is responsible for providing an independent, timely assessment:

- understand the intended outcome before judging implementation details;
- inspect enough surrounding code to understand the change in context;
- prioritise correctness, security, design, and operational risk;
- verify important claims rather than relying only on the description;
- make blocking concerns explicit;
- separate required changes from optional improvements;
- avoid expanding the change with unrelated work;
- approve when remaining concerns are non-blocking and the change improves code health.

Approval means the reviewer is willing to share responsibility for the change entering the codebase. It does not guarantee that the change is defect-free.

## Before Reading the Diff

Start with context:

1. What problem is being solved?
2. What behaviour should change, and what must remain unchanged?
3. Who or what consumes this code?
4. What is the failure impact?
5. Is this the smallest coherent change?
6. Are design decisions or constraints documented?
7. What evidence demonstrates that the change works?
8. How will it be deployed, observed, and rolled back?

If the purpose cannot be understood from the pull-request description and linked material, ask the author to improve the context before spending significant time on line-level review.

## A Practical Review Workflow

### Pass 1: Understand Scope and Shape

- Read the title, description, linked requirements, and test evidence.
- Inspect the file list and overall diff size.
- Identify generated files, dependency changes, migrations, or infrastructure modifications.
- Look at the architecture and data flow before individual expressions.
- Decide whether specialist review is required for security, database, accessibility, or platform concerns.

### Pass 2: Review Behaviour and Design

- Trace the main success path.
- Trace important failure, boundary, retry, cancellation, and recovery paths.
- Check interfaces and dependency direction.
- Evaluate state changes and externally visible behaviour.
- Confirm that the design fits existing conventions or documents a justified departure.

### Pass 3: Review Implementation and Tests

- Examine names, control flow, data structures, resource ownership, and error handling.
- Read tests as specifications and check what they actually prove.
- Compare implementation behaviour with the requirement and test assertions.
- Inspect the surrounding unchanged code where assumptions or contracts matter.

### Pass 4: Consider Delivery and Operations

- Confirm configuration, migrations, compatibility, telemetry, rollout, and rollback.
- Check CI results and any manual verification evidence.
- Re-read the complete diff for accidental files, debug output, secrets, or unrelated edits.
- Summarise blocking concerns and the overall review outcome.

## What to Review

### 1. Correctness and Functional Integrity

Ask whether the code solves the intended problem for all important paths.

Check:

- requirements and acceptance criteria;
- null, empty, zero, maximum, minimum, and malformed inputs;
- ordering, duplicates, locale, time zone, and clock behaviour;
- numeric precision, overflow, and rounding;
- state transitions and invariants;
- retry and idempotency behaviour;
- partial success and partial failure;
- race conditions and timing assumptions;
- off-by-one and incorrect boolean conditions;
- behaviour when dependencies are slow, unavailable, or inconsistent.

Do not limit review to the changed lines. A locally correct change can violate a contract elsewhere in the system.

### 2. Design and Architecture

Ask whether the solution is appropriate for the system and proportional to the problem.

Check:

- clear responsibilities and boundaries;
- appropriate abstractions and dependency direction;
- cohesion within modules and coupling between them;
- ownership of state and resources;
- consistency with established architecture;
- whether a simpler design would meet the requirement;
- whether complexity is justified by a real need;
- whether the change introduces avoidable global or shared mutable state.

SOLID principles and design patterns are reasoning tools, not a scoring system. Do not demand an abstraction or extension point for a hypothetical future requirement. Prefer the simplest design that handles known variation safely.

### 3. Readability and Maintainability

Code is read more often than it is written.

Check:

- names communicate purpose and domain meaning;
- methods and classes have coherent responsibilities;
- control flow is easy to follow;
- nesting and branching are manageable;
- duplication represents actual knowledge duplication rather than superficial similarity;
- constants replace unexplained values where doing so adds meaning;
- comments explain reasons, constraints, or non-obvious behaviour;
- outdated comments and TODOs are updated or removed;
- formatting follows automated and team conventions.

Prefer automated formatters and linters for mechanical style. Human review time is more valuable for behaviour, design, and context.

### 4. Tests and Verification

Test coverage means more than a percentage or the existence of a new test file.

Check that tests:

- demonstrate the requested behaviour;
- exercise meaningful boundaries and failure paths;
- fail for the right reason when the implementation is broken;
- use the lowest practical test level;
- are deterministic and isolated;
- avoid unnecessary sleeps, retries, shared data, and order dependence;
- use readable test data and assertions;
- verify externally observable behaviour rather than implementation details;
- clean up resources they create;
- provide useful failure diagnostics.

Also ask:

- Are existing tests being weakened, skipped, or deleted?
- Does a changed contract require tests in another component?
- Is manual verification justified and recorded?
- Is the untested risk understood and accepted?

Coverage tools can reveal unexecuted code, but a high percentage does not prove that assertions are meaningful or that important risks are covered.

### 5. Security and Privacy

Security review depends on the application's threat model. At minimum, examine:

- server-side input validation and safe output encoding;
- parameterised database access rather than string-built queries;
- authentication and authorisation at every protected operation;
- object-level access control, not only page or route access;
- secret storage and accidental credentials in code, tests, logs, or configuration;
- collection, exposure, retention, and deletion of personal or sensitive data;
- safe cryptographic APIs rather than custom cryptography;
- dependency and supply-chain changes;
- path traversal, unsafe deserialisation, injection, and request-forgery risks;
- rate limits and abuse cases where relevant;
- whether errors expose internals, tokens, or sensitive values.

Client-side validation improves usability but does not replace validation at the trust boundary. Never approve a security-sensitive change solely because automated scanning passed.

### 6. Error Handling and Reliability

Check that the system fails predictably and provides enough information for recovery.

- Catch exceptions only where the code can add context, recover, translate, or clean up.
- Preserve the original cause when wrapping an exception.
- Avoid swallowing exceptions or returning misleading success.
- Distinguish retryable from permanent failures.
- Bound retries with appropriate backoff and timeouts.
- Ensure repeated operations are safe where idempotency is expected.
- Release files, connections, locks, threads, and other resources reliably.
- Handle cancellation and interruption correctly.
- Consider degraded dependencies and partial outages.

Logs should help diagnosis without exposing secrets or generating uncontrolled noise.

### 7. Concurrency and Asynchronous Behaviour

Concurrency bugs are often difficult to reproduce, so review assumptions explicitly.

Check:

- ownership and mutation of shared state;
- atomicity of read-modify-write operations;
- lock ordering and deadlock potential;
- thread-safe collection and API use;
- lifecycle of executors, tasks, subscriptions, and futures;
- timeout, cancellation, and exception propagation;
- duplicate, delayed, missing, or out-of-order events;
- transaction boundaries and eventual consistency;
- tests that synchronise on observable conditions rather than arbitrary sleeps.

### 8. Performance and Resource Use

Optimise based on expected scale and evidence, not instinct alone.

Look for:

- repeated database or network calls inside loops;
- unbounded queries, collections, queues, caches, or retries;
- inappropriate algorithms or data structures;
- unnecessary serial operations where safe concurrency is required;
- repeated parsing, allocation, or conversion on hot paths;
- missing pagination, batching, streaming, or backpressure;
- leaked connections, files, threads, or memory;
- increased latency on critical paths.

Ask for a benchmark, query plan, profile, or load-test result when performance is an important claim. Avoid blocking a change for speculative micro-optimisation.

### 9. Compatibility and Data Changes

Check compatibility across the complete rollout window:

- existing API consumers and persisted data;
- old and new application versions running simultaneously;
- backward- and forward-compatible message or schema changes;
- safe defaults for missing fields;
- database migration duration, locking, and rollback;
- backfills, retries, and restartability;
- feature-flag behaviour in every state;
- removal plans for temporary compatibility code.

An application rollback may not reverse a destructive data migration. Treat data changes as operational changes, not merely code changes.

### 10. Observability and Operations

Ask how the team will know whether the change works after deployment.

Check:

- logs provide useful context and stable identifiers;
- metrics describe success, failure, latency, and saturation where relevant;
- traces preserve context across service boundaries;
- alerts are actionable and avoid obvious noise;
- health checks represent the correct readiness and liveness conditions;
- dashboards or runbooks are updated when operational behaviour changes;
- rollout, feature-flag, and rollback plans are realistic;
- failure can be diagnosed without reproducing it locally.

### 11. Documentation and Dependencies

Check whether the change updates:

- public API documentation;
- README and setup instructions;
- configuration examples;
- architecture decisions and diagrams;
- operational runbooks;
- release or migration notes.

For dependencies, review why the dependency is needed, its source, maintenance status, licence requirements, transitive impact, version constraints, known risks, and whether existing functionality already solves the problem.

## Reviewing Test Automation Code

Test code is production code for the delivery system and deserves equivalent scrutiny.

Check:

- the test has a clear risk and purpose;
- setup does not hide the behaviour under test;
- selectors and API interactions use stable contracts;
- waits observe a condition rather than sleeping for a fixed duration;
- assertions verify the outcome, not merely that execution completed;
- tests remain independent under random or parallel execution;
- data is unique where collisions are possible;
- failure artefacts are useful but do not expose sensitive information;
- retries do not conceal a deterministic defect;
- page objects and helpers express domain operations rather than becoming generic utility collections;
- test-only shortcuts do not create unrealistic production behaviour.

## Automation Versus Human Review

Automate repeatable mechanical checks:

- formatting and linting;
- compilation and type checking;
- unit and integration tests;
- dependency and vulnerability scanning;
- secret scanning;
- static analysis;
- schema validation;
- policy and licence checks.

Human reviewers should concentrate on intent, correctness, threat scenarios, system interactions, trade-offs, maintainability, and whether the automated evidence is sufficient. A green pipeline is evidence, not proof.

## Writing Useful Review Comments

State the severity or intent when it may be unclear.

### Blocking

Use when the change should not merge in its current form:

> **Blocking:** This authorisation check verifies that the caller is signed in but does not verify access to the requested account. Please enforce account-level permission before loading the record; otherwise one authenticated user could read another user's data.

### Suggestion

Use for a worthwhile but non-blocking improvement:

> **Suggestion:** Extracting the retry policy into the existing client configuration would keep timeout and backoff behaviour consistent. I do not think this needs to block the current fix.

### Question

Use when context or intent is unclear:

> **Question:** What guarantees that this event cannot be delivered twice? If delivery is at-least-once, should this write use the event ID as an idempotency key?

### Nit

Use for optional polish that should not delay approval:

> **Nit:** `expiryInstant` may communicate the unit and meaning more clearly than `expiry`.

### Praise

Positive comments reinforce useful practices:

> The boundary tests make the rounding rule especially clear, and the failure messages should make regressions straightforward to diagnose.

Avoid comments such as “This is wrong,” “Why would you do this?”, or “Just use pattern X.” They do not explain the risk or help the author evaluate the proposed alternative.

## Handling Disagreement

1. Clarify whether the issue is blocking, optional, or a question.
2. Refer to requirements, measured evidence, established standards, or documented architectural decisions.
3. Compare concrete trade-offs rather than defending personal preference.
4. Move to a short conversation if written comments stop producing new information.
5. Record the decision and reasoning in the review.
6. Escalate to the code owner, technical lead, security owner, or other responsible decision-maker when consensus cannot be reached.

Do not use approval authority to enforce personal style. Likewise, do not dismiss a concern merely because automated tests pass or the author is more senior.

## Suggested Pull-Request Description

```markdown
## Problem
What user, business, or operational problem does this solve?

## Approach
What changed, and why was this approach selected?

## Verification
Which automated and manual checks were performed?

## Risk
What could fail, and what remains untested?

## Delivery
Are migrations, flags, monitoring, or rollout steps required?

## Rollback
How can the change be disabled or reversed safely?
```

Adapt the template to the risk of the repository. A one-line documentation correction should not require the same ceremony as a payment migration.

## Interview Approach

When asked to review code during an interview, explain your process before listing defects:

1. Clarify the requirement, inputs, outputs, and constraints.
2. Trace the success path and important edge cases.
3. Identify correctness or security failures first.
4. Evaluate design, readability, tests, performance, and operations.
5. Prioritise findings by impact and confidence.
6. Suggest a concrete improvement and explain the trade-off.
7. Mention what you would verify with tests, measurement, or additional context.

A strong answer separates verified defects from hypotheses. For example:

> This query appears inside a loop, so it may produce one database round trip per item. I would confirm the expected collection size and inspect query metrics. If the path is large or frequent, I would batch the lookup or fetch the required records in one query.

## Practice Exercise

Review this method:

```java
public User loadUser(String id) {
    try {
        return repository.findById(id).get();
    } catch (Exception exception) {
        logger.info("Unable to load user " + id + ": " + exception);
        return null;
    }
}
```

Potential findings include:

- the contract for null or blank `id` is unclear;
- `Optional.get()` throws when the user does not exist;
- catching `Exception` also hides unexpected programming or infrastructure failures;
- returning `null` loses the distinction between not found and dependency failure;
- string concatenation and exception formatting produce weak structured logs;
- the identifier may be sensitive depending on the domain;
- the exception cause is swallowed;
- tests should cover found, not-found, invalid-input, and repository-failure behaviour.

The correct redesign depends on the surrounding API contract. The reviewer should ask for or infer that context before prescribing a specific exception or return type.

## Quick Review Checklist

### Context

- Is the problem and intended behaviour clear?
- Is the scope focused and the correct reviewers involved?

### Behaviour

- Are success, boundary, failure, retry, and recovery paths correct?
- Are concurrency and distributed-system assumptions safe?

### Design

- Is the solution proportionate, cohesive, and consistent with the architecture?
- Are dependencies and state ownership clear?

### Tests

- Do the tests prove meaningful behaviour at the right level?
- Are they deterministic, isolated, and diagnosable?

### Security

- Are trust boundaries, access controls, secrets, and sensitive data handled safely?

### Delivery

- Are compatibility, migrations, telemetry, rollout, and rollback covered?

### Communication

- Are blocking issues distinguished from suggestions and nits?
- Does every comment explain its concern clearly and respectfully?

## Further Reading

- [Google Engineering Practices: Code Review](https://google.github.io/eng-practices/review/)
- [Google Engineering Practices: What to Look For](https://google.github.io/eng-practices/review/reviewer/looking-for.html)
- [Google Engineering Practices: The Standard of Code Review](https://google.github.io/eng-practices/review/reviewer/standard.html)
- [Google Engineering Practices: Small Changes](https://google.github.io/eng-practices/review/developer/small-cls.html)
- [GitHub pull-request reviews](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/reviewing-changes-in-pull-requests/about-pull-request-reviews)
- [OWASP Secure Code Review Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Secure_Code_Review_Cheat_Sheet.html)
