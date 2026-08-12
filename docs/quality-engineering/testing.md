# Software Testing

Software testing evaluates software and related work products to discover defects, reduce risk, and provide information for decisions. It includes more than executing test cases: requirements analysis, design review, exploratory work, automation, observability, and production learning all contribute to confidence.

Testing can show evidence about quality, but it cannot guarantee that a system is defect-free. The goal is to understand whether the product is sufficiently fit for its intended use and what risks remain.

## Quality Is a Shared Responsibility

Quality is created throughout product development rather than inspected in at the end. Developers, testers, product specialists, designers, security specialists, operations teams, and users contribute different knowledge.

An SDET or tester provides specialist testing and engineering skills, including:

- identifying product risks and testability problems;
- selecting effective test approaches;
- building fast and trustworthy feedback systems;
- investigating unexpected behaviour;
- making quality and residual risk visible;
- helping the team prevent defects.

Testing independence can provide a valuable alternative perspective, but handing all responsibility to an independent test team usually delays feedback and weakens ownership.

## Verification and Validation

- **Verification** asks whether the work product meets its specified requirements or design: “Did we build it correctly?”
- **Validation** asks whether the resulting product meets user and stakeholder needs in its intended context: “Did we build the right thing?”

A system can conform perfectly to an incorrect requirement. Effective testing considers both.

## The Seven Testing Principles

The following principles are based on the ISTQB Foundation Level syllabus.

### 1. Testing Shows the Presence, Not the Absence, of Defects

Testing can reveal failures and increase confidence, but passing tests do not prove that no undiscovered defect exists. Conclusions must remain proportional to the scope, environments, data, and risks actually evaluated.

### 2. Exhaustive Testing Is Impossible

Except for trivial cases, it is not feasible to test every input, path, state, timing, environment, and interaction. Use risk analysis and systematic test techniques to select a valuable sample.

### 3. Early Testing Saves Time and Money

Review requirements, examples, architecture, interfaces, and acceptance criteria before implementation. Preventing an ambiguous requirement or untestable design is usually more effective than detecting its consequences later.

Early testing does not mean testing only on the left side of delivery. Continue evaluating deployed behaviour and feeding production learning back into design.

### 4. Defects Cluster Together

Defects and failures are often unevenly distributed. Complex, frequently changed, poorly understood, highly coupled, or historically unreliable areas may deserve more attention.

The Pareto 80/20 ratio is a heuristic, not a universal law. Use repository history, incidents, complexity, and observed failure data rather than assuming an exact distribution.

### 5. Tests Wear Out

Repeating the same tests tends to reveal fewer new defects over time. Existing regression tests still protect known behaviour, but the test model must evolve as the product, risks, users, and failure history change.

Refresh coverage through exploratory testing, new data, mutation testing, changed combinations, production insights, and periodic removal or refactoring of low-value tests.

This principle was historically called the **pesticide paradox**. “Tests wear out” communicates the idea without implying that useful regression checks should simply be discarded.

### 6. Testing Is Context Dependent

The appropriate approach depends on the domain, users, architecture, regulation, delivery model, and failure impact. A medical device, payment system, internal reporting tool, and mobile game require different evidence and controls.

### 7. Absence-of-Defects Is a Fallacy

Finding and fixing many defects does not make a product successful if it solves the wrong problem, is inaccessible, is too slow, or cannot be operated safely. Quality must be evaluated against actual user and stakeholder needs.

## Static and Dynamic Testing

### Static Testing

Static testing evaluates work products without executing the software.

Examples include:

- requirement and acceptance-criteria reviews;
- architecture and threat-model reviews;
- code review;
- static analysis and linting;
- schema and configuration validation;
- reviewing test cases and operational runbooks.

Static testing can find ambiguity, inconsistency, unreachable code, unsafe dependencies, and design defects early.

### Dynamic Testing

Dynamic testing executes software and compares observed behaviour with expectations or investigates it for unexpected behaviour.

Examples include unit tests, API tests, exploratory sessions, performance experiments, accessibility evaluation, and production smoke tests.

Static and dynamic techniques complement each other; neither replaces the other.

## Testing and Debugging

Testing and debugging are related but distinct:

```text
Testing:   observe a failure and provide evidence
Debugging: locate its cause, correct it, and verify the correction
```

After a defect is fixed:

- **confirmation testing** checks that the original failure is corrected;
- **regression testing** checks that the change did not damage previously working behaviour.

## Test Levels

Test levels group testing by scope and the object under test. Names vary between organisations, so define boundaries rather than relying only on labels.

### Unit Testing

Evaluates a small unit of behaviour, usually in process and without real external infrastructure.

Strengths:

- very fast feedback;
- precise failure localisation;
- broad boundary and branch exploration;
- easy execution during development.

Risks:

- excessive mocking can prove interactions with doubles rather than real behaviour;
- implementation-coupled tests make refactoring expensive;
- isolated correctness does not prove integration correctness.

### Component or Service Testing

Evaluates a deployable or meaningful subsystem through a stable interface. Some dependencies may be real, embedded, containerised, or replaced deliberately.

This level often provides strong behavioural confidence without the cost of a complete end-to-end environment.

### Integration Testing

Evaluates interfaces and interactions between components, services, databases, queues, or external systems.

Focus on contracts, serialisation, authentication, timeouts, transactions, ordering, retries, and failure propagation—not merely whether two happy paths connect.

### System or End-to-End Testing

Evaluates a complete integrated system through user-visible interfaces.

Use it for a limited set of critical journeys and cross-system risks. End-to-end tests are valuable but commonly slower, more expensive, and harder to diagnose than lower-level tests.

### Acceptance Testing

Evaluates whether a system is acceptable to stakeholders or users. Acceptance may cover business rules, operational readiness, contracts, regulation, usability, or production rollout—not only automated UI scenarios.

## Test Types

### Functional Testing

Evaluates what the system does:

- business rules and calculations;
- state transitions and workflows;
- API and UI behaviour;
- validation and error handling;
- persistence and side effects;
- permissions and domain constraints.

### Non-Functional Testing

Evaluates how well the system operates under relevant conditions:

- performance and capacity;
- reliability, recovery, and resilience;
- security and privacy;
- accessibility and usability;
- compatibility and portability;
- maintainability and operability;
- installation, upgrade, and migration behaviour.

### Change-Related Testing

- **Confirmation testing** verifies a specific correction.
- **Regression testing** searches for unintended impact elsewhere.

Regression scope should follow dependency and risk analysis. “Run everything” may be appropriate sometimes, but it is not a substitute for understanding change impact.

## A Continuous Testing Process

Testing activities overlap and repeat throughout iterative, agile, and DevOps delivery. They should not be treated as a one-way hand-off owned only by a QA team.

```text
Plan and monitor
       |
Analyse risk and test basis
       |
Design tests and required coverage
       |
Implement tests, data, tools, and environments
       |
Execute, explore, investigate, and report
       |
Evaluate results and complete the test activity
       |
Learn and adjust continuously
```

### Test Planning

Define:

- objectives and quality risks;
- scope and exclusions;
- test approach and levels;
- people, skills, tools, environments, and data;
- schedule and dependencies;
- entry, exit, suspension, and resumption criteria where useful;
- reporting, escalation, and decision responsibilities.

Plans can be concise and evolve with the product. Their value is shared understanding, not document volume.

### Test Monitoring and Control

Compare actual progress and evidence with the plan, then adapt.

Monitor items such as:

- high-risk coverage completed;
- unresolved defects and their impact;
- environment or data blockers;
- automation reliability and duration;
- changes in scope or architecture;
- confidence in release objectives.

Control actions might include changing priorities, adding expertise, reducing scope, improving an environment, or delaying a decision until critical evidence exists.

### Test Analysis

Analyse the **test basis**—requirements, examples, designs, code, risks, standards, incident history, and user research—to identify testable features and test conditions.

Ask:

- What could fail?
- Who would be affected?
- What states and transitions exist?
- Which interfaces and dependencies matter?
- What must never happen?
- How will behaviour be observed?
- Is the requirement testable and unambiguous?

### Test Design

Transform test conditions into concrete coverage using appropriate techniques. Define expected results, data needs, environment needs, and traceability where it adds value.

Expected results should come from an independent oracle such as a rule, invariant, model, specification, trusted calculation, or observable user outcome—not from copying the implementation's logic into the test.

### Test Implementation

Prepare:

- executable tests or session charters;
- test data and accounts;
- fixtures, drivers, simulators, and service doubles;
- suites and execution order where order is genuinely required;
- environment configuration;
- logging and failure artefacts;
- CI/CD integration.

Review test code and test cases like other engineering artefacts.

### Test Execution

- Verify the environment and test data.
- Execute planned checks and exploratory sessions.
- Compare actual and expected results.
- Investigate anomalies before classifying them.
- Record useful evidence and defects.
- Re-test corrections and run proportionate regression coverage.

A failed automated test is an observation, not automatically a product defect. The cause may be the product, test, environment, data, dependency, or expectation.

### Test Completion

At a useful completion point:

- evaluate objectives and residual risks;
- communicate evidence and important limitations;
- close, defer, or transfer unresolved issues;
- preserve useful testware and environments;
- remove or sanitise temporary data;
- record lessons and improvement actions.

Completion does not imply zero defects. It means stakeholders have enough information to make the next decision.

## Risk-Based Testing

Product risk is the possibility that a product failure causes harm or loss. A simple prioritisation model is:

```text
Risk exposure = likelihood of failure x impact of failure
```

This formula is a conversation aid, not precise mathematics. Consider detectability, exposure frequency, recovery difficulty, affected users, legal obligations, and uncertainty as well.

### Example Risk Table

| Risk | Likelihood | Impact | Response |
| :--- | :---: | :---: | :--- |
| Customer charged twice | Medium | Critical | Idempotency, concurrency, recovery, and production monitoring tests |
| Search result colour differs | Medium | Low | Visual check in representative browsers |
| Admin data visible to customer | Low | Critical | Object- and role-level authorisation tests plus security review |
| Confirmation email delayed | Medium | Medium | Queue, retry, eventual-consistency, and observability tests |

Use risk to decide what to test first, how deeply to test it, which level to use, and which residual risks require explicit acceptance.

## Black-Box Test Techniques

Black-box techniques derive tests from externally visible behaviour without requiring knowledge of implementation structure.

### Equivalence Partitioning

Divide inputs into groups expected to behave similarly, then select representatives.

For an allowed age of 18 through 120:

```text
Invalid: age < 18
Valid:   18 <= age <= 120
Invalid: age > 120
```

Partitions reduce redundant cases but do not guarantee that one representative exposes every defect in a group.

### Boundary-Value Analysis

Defects often occur at boundaries. For the same range, useful values include:

```text
17, 18, 19, 119, 120, 121
```

Also consider missing values, wrong types, numeric overflow, and domain-specific boundaries.

### Decision-Table Testing

Use a table when combinations of conditions determine outcomes.

| Valid account | Sufficient funds | Fraud hold | Expected result |
| :---: | :---: | :---: | :--- |
| Yes | Yes | No | Approve payment |
| Yes | No | No | Decline for funds |
| Yes | Yes | Yes | Block for review |
| No | Any | Any | Reject account |

Mark impossible or irrelevant combinations explicitly.

### State-Transition Testing

Model valid and invalid events from each state.

```text
Pending -> Paid -> Shipped -> Delivered
   |         |
   v         v
Cancelled  Refunded
```

Test valid transitions, forbidden transitions, repeated events, and behaviour after terminal states.

### Use-Case and Scenario Testing

Exercise user or system journeys including alternate and exception flows. Avoid limiting scenarios to ideal happy paths.

### Pairwise and Combinatorial Testing

When many factors interact, pairwise or higher-strength combinations can reduce the test set while covering selected interactions. This is sampling, not proof that higher-order combinations cannot fail.

## White-Box Test Techniques

White-box techniques use knowledge of implementation structure.

### Statement Coverage

Measures which executable statements ran. Full statement coverage does not prove that every decision outcome or requirement was tested.

### Branch Coverage

Measures whether decision outcomes were exercised. It is stronger than statement coverage for control flow but still does not prove meaningful assertions, correct requirements, or all combinations.

### Data-Flow and Path-Oriented Analysis

Examine variable definition and use, resource lifecycles, error paths, and selected execution paths. Complete path coverage is normally impossible for non-trivial loops and conditions.

Coverage metrics reveal gaps; they are not release goals in isolation.

## Experience-Based Techniques

### Exploratory Testing

Exploratory testing combines learning, test design, and execution. It is deliberate investigation, not random clicking.

A useful charter contains:

```text
Explore: checkout recovery
With: interrupted payments, retries, refreshes, and duplicate callbacks
To discover: duplicate orders, inconsistent status, or unrecoverable sessions
```

Time-box sessions, take useful notes, capture evidence, and debrief findings and new risks.

### Error Guessing

Use experience with previous defects, technologies, integrations, and common developer mistakes to target likely failures.

### Checklist-Based Testing

Use a maintained checklist for recurring risks such as accessibility, security headers, migrations, or mobile interruption. Avoid allowing a checklist to replace thinking about new context.

## Test Strategy and the Test Pyramid

A common strategy uses many fast, focused tests and fewer broad, expensive tests:

```text
            End-to-end
          /             \
        Integration and contract
      /                           \
    Unit and component-level checks
```

This is an economic guideline, not a required ratio. The right shape depends on architecture and risk. A UI-heavy product, data pipeline, embedded system, and API platform need different portfolios.

Optimise for:

- fast feedback;
- meaningful confidence;
- deterministic execution;
- useful failure localisation;
- realistic integration evidence;
- sustainable maintenance cost.

Do not count tests as if every test has equal value.

## Automation Strategy

Automation is valuable when a check is repeatable, objective, sufficiently stable, and worth executing often.

Good candidates include:

- unit and component regression checks;
- API contracts and business rules;
- deterministic critical journeys;
- compatibility checks across supported configurations;
- build, migration, and deployment validation;
- performance baselines and security scanning.

Human-led testing remains important for:

- exploration and learning;
- usability and nuanced accessibility evaluation;
- rapidly changing behaviour;
- ambiguous requirements;
- rare scenarios whose automation cost exceeds their value.

Before automating, ask:

1. What risk does this check cover?
2. Is this the lowest reliable level?
3. Can the result be determined objectively?
4. Can state and data be controlled?
5. Will failure be diagnosable?
6. How often will it run and how long will it remain useful?
7. What maintenance cost does it create?

Automation supports testing; it is not synonymous with testing.

## Test Data

Test data must be representative, controlled, secure, and diagnosable.

- Generate unique data when tests run in parallel.
- Use builders or factories to make intent readable.
- Avoid dependencies on mutable shared records.
- Keep data creation close to the test where practical.
- Clean up safely or design disposable environments.
- Record generated identifiers in failure output.
- Cover realistic boundaries and relationships, not only random strings.
- Never copy sensitive production data without an approved and protected process.
- Test retention, masking, deletion, and access policies where they are product requirements.

Random data can broaden exploration but harms reproducibility unless the seed and generated values are captured.

## Test Environments and Dependencies

Environment design affects what a test result means.

Consider:

- version parity with production;
- configuration and feature flags;
- network, identity, certificates, and time;
- database schemas and seed data;
- external service availability and quotas;
- observability and access to evidence;
- isolation between users and parallel runs;
- ownership, reset, and maintenance.

Use real dependencies when their behaviour is the risk under test. Use fakes, stubs, simulators, or service virtualisation when control, speed, cost, or failure injection is more important. Contract tests can reduce—but not eliminate—the risk that a substitute diverges from reality.

## Flaky Tests

A flaky test produces inconsistent outcomes without a relevant product change. Common causes include:

- uncontrolled time or randomness;
- fixed sleeps and timing races;
- shared mutable state;
- order dependence;
- leaking resources;
- unstable selectors;
- unreliable external services;
- eventual consistency without condition-based polling;
- environment capacity or configuration drift.

Investigate flakes as defects in the feedback system. Quarantine may protect delivery temporarily, but it needs an owner, reason, and removal deadline. Blind retries hide information and can make a broken suite appear healthy.

Track flake rate, affected tests, lost engineering time, and recurrence by cause. Delete tests that no longer provide enough value to justify repair.

## Defect Investigation and Reporting

Before reporting a defect, establish what was observed and gather enough evidence to support investigation.

A useful report includes:

- concise summary and impact;
- environment, build, commit, and configuration;
- preconditions and relevant data;
- minimal reproducible steps;
- expected and actual behaviour;
- frequency and timing;
- logs, screenshots, video, traces, or requests where useful;
- suspected scope without presenting speculation as fact;
- security-sensitive handling when applicable.

Severity describes impact; priority describes when the issue should be addressed. Teams may use different scales, so define them.

Avoid automatically treating every test failure as a new product defect. First determine whether the failure comes from the product, test, environment, data, dependency, or incorrect expectation.

## Security Testing

Security testing should follow the threat model and trust boundaries.

### Authentication: Who or What Are You?

Authentication establishes an identity using one or more authenticators.

Test:

- missing, malformed, expired, revoked, and replayed credentials;
- password, recovery, enrolment, logout, and session lifecycle;
- multi-factor flows and fallback paths;
- rate limiting and account-enumeration behaviour;
- token issuer, audience, signature, scope, and expiry where relevant;
- secure transport and storage of credentials.

Authentication factors are commonly grouped as something known, possessed, or inherent. A mechanism's real security depends on implementation and recovery paths, not merely the category count.

### Authorisation: What May You Do?

Authorisation evaluates whether the established identity may perform an operation on a resource.

Models include role-based access control, attribute-based access control, relationship-based access control, and policy-based combinations.

Test:

- every role and permission boundary;
- horizontal access to another user's object;
- vertical access to privileged operations;
- tenant isolation;
- field- and function-level restrictions;
- identifiers changed in paths, queries, and content;
- access after role changes, logout, or revocation;
- default-deny behaviour for missing policy decisions.

A user can be correctly authenticated and still be unauthorised. Test both independently.

### Additional Security Areas

- input validation and output encoding;
- injection and unsafe deserialisation;
- secrets and sensitive logging;
- dependency and supply-chain risk;
- encryption and key handling;
- security headers and CORS configuration;
- abuse of sensitive business workflows;
- server-side request forgery;
- audit trails and tamper resistance.

Automated scanners provide useful coverage but do not replace threat-informed manual analysis.

## Performance Testing

Performance is multi-dimensional:

- **latency**: time taken for an operation, including percentiles;
- **throughput**: completed work per unit of time;
- **concurrency**: simultaneous active work;
- **utilisation**: consumption of CPU, memory, storage, connections, and other resources;
- **saturation**: the point where demand exceeds capacity;
- **error rate**: failed or timed-out work under load.

Common experiments include load, stress, spike, endurance, volume, and scalability testing.

Define a workload model from expected user behaviour and data. Warm up where appropriate, control the generator, monitor every relevant tier, and report distributions rather than averages alone. A test result from an unrealistic environment must state its limitations.

## Accessibility Testing

Accessibility testing combines automated checks, manual evaluation, standards knowledge, and feedback from users with disabilities.

Include:

- keyboard-only operation and visible focus;
- semantic structure, labels, names, roles, and states;
- screen-reader interaction on representative journeys;
- colour contrast and non-colour cues;
- zoom, reflow, orientation, and text spacing;
- error identification and recovery;
- captions, transcripts, and alternative text;
- timing, motion, and input alternatives.

Automated tools detect only some accessibility problems. They cannot determine complete conformance or usability on their own.

## Reliability and Resilience Testing

Evaluate behaviour when dependencies or infrastructure fail:

- timeouts, dropped connections, and malformed responses;
- retry, backoff, and circuit-breaker behaviour;
- process or node restart;
- duplicate, delayed, missing, or reordered events;
- partial writes and transaction failure;
- backup restoration and disaster recovery;
- capacity exhaustion and degraded modes;
- safe rollback and recovery after deployment failure.

Verify observability alongside behaviour: the system should expose enough logs, metrics, traces, and alerts to detect and diagnose the failure.

## Testing in CI/CD

Organise feedback by speed, reliability, and risk:

```text
Developer loop
-> formatting, static analysis, unit and focused component tests

Pull request
-> deterministic component, contract, integration, and selected journey tests

Post-merge or deployed environment
-> broader integration, migration, smoke, and compatibility tests

Scheduled or dedicated environment
-> expensive end-to-end, performance, resilience, and security activities
```

This is an example, not a fixed rule. Critical checks belong at the earliest stage where they are reliable. Keep total feedback time visible, execute independent tests in parallel, and do not allow slow suites to become ignored background noise.

## Metrics and Reporting

Metrics should support decisions and improvement rather than reward activity.

Potentially useful measures include:

- coverage of identified high risks;
- escaped defects and where earlier detection was possible;
- change failure and rollback rates;
- suite duration and time to feedback;
- flake rate and failure-diagnosis time;
- unresolved defects by impact and age;
- production reliability and user-impact signals;
- mutation score or branch coverage when used diagnostically.

Potentially misleading measures include raw test-case counts, pass percentages without context, defect counts used to compare individuals, and code coverage treated as proof of correctness.

A test report should state:

- what was evaluated and why;
- environment, build, and data scope;
- important evidence and findings;
- blocked, skipped, or unreliable coverage;
- known limitations and residual risks;
- the decision being supported.

## Release Decisions

Testing informs a release decision; it does not make the business decision alone.

Useful release criteria are risk-based and observable, such as:

- critical journeys verified in the release candidate;
- no unresolved defect above an agreed impact threshold without explicit acceptance;
- migrations and rollback rehearsed;
- required security, performance, and accessibility evidence available;
- monitoring and operational ownership ready;
- known limitations communicated to the decision-makers.

A failed criterion may lead to a delay, reduced scope, feature flag, additional control, or explicit risk acceptance. Avoid presenting a simplistic “QA sign-off” as a guarantee of quality.

## Example Test Strategy: Checkout

Assume an online checkout accepts a basket, reserves stock, authorises payment, creates an order, and sends confirmation.

### Primary Risks

- customer charged without an order;
- customer charged more than once;
- oversold stock;
- incorrect total, tax, currency, or rounding;
- another customer accesses the order;
- success displayed before durable completion;
- confirmation failure causes order rollback or duplicate processing.

### Layered Coverage

| Level | Example coverage |
| :--- | :--- |
| Unit | totals, discounts, tax, currency rounding, state rules |
| Component | checkout orchestration with controlled payment and stock substitutes |
| Contract | payment, stock, and notification request/response compatibility |
| Integration | real database transactions, queue publishing, idempotency storage |
| End-to-end | a small number of critical customer journeys through the deployed system |
| Exploratory | interruptions, refreshes, duplicate submission, unusual baskets, recovery |
| Non-functional | load, authorisation, accessibility, resilience, and observability |

### Important Oracles

- exactly one durable order for one accepted checkout request;
- charged amount equals the independently calculated order total;
- stock and payment state reconcile after failure recovery;
- only authorised users can access the resulting order;
- asynchronous work reaches a documented terminal state;
- logs and traces connect the customer request to downstream operations without exposing payment secrets.

This risk-to-evidence mapping is more useful than listing tools before understanding the system.

## Common Testing Mistakes

- Automating every scenario through the UI.
- Treating acceptance criteria as complete coverage.
- Asserting only that no exception occurred.
- Copying production logic into expected-result calculations.
- Using fixed sleeps instead of observable conditions.
- Sharing mutable accounts or records across parallel tests.
- Retrying failures without investigating their cause.
- Measuring quality by test count or pass percentage alone.
- Ignoring accessibility, security, performance, and operational behaviour.
- Testing only before release and discarding production learning.
- Reporting findings without impact, context, or reproduction evidence.
- Keeping obsolete tests because they once found a defect.

## Interview Approach

When asked how you would test a feature:

1. Clarify users, objectives, architecture, constraints, and failure impact.
2. Identify product risks and rank them.
3. Model inputs, boundaries, states, rules, dependencies, and trust boundaries.
4. Select test techniques and the lowest useful test levels.
5. Cover functional, security, performance, accessibility, and reliability concerns proportionately.
6. Explain data, environments, isolation, and observability.
7. Decide what to automate and where it runs in delivery.
8. State limitations, residual risks, and the evidence needed for a decision.

Strong answers show prioritisation. Do not respond with an unbounded list of test cases before understanding the risk.

## Practice Exercises

1. Build an equivalence and boundary table for a password-reset token with a 15-minute lifetime.
2. Create a decision table for discounts based on customer type, basket value, and promotion eligibility.
3. Draw a state model for an order including cancellation, payment failure, and refund.
4. Write an exploratory charter for a flaky asynchronous notification workflow.
5. Design a test portfolio for an API without defaulting every case to end-to-end tests.
6. Diagnose a suite that fails only during parallel CI execution.
7. Create a risk-based release report from incomplete and conflicting test evidence.

## Readiness Checklist

You should be able to:

- explain the seven testing principles without treating them as slogans;
- distinguish verification, validation, testing, and debugging;
- select useful test levels and types from system risk;
- apply equivalence, boundary, decision-table, and state-transition techniques;
- plan and execute an exploratory session;
- design a maintainable automation portfolio;
- control data, environments, time, and dependencies;
- investigate flaky tests systematically;
- test authentication separately from authorisation;
- reason about security, performance, accessibility, and resilience;
- design layered CI/CD feedback;
- communicate evidence, limitations, and residual risk clearly.

## Related Guides

- [Code Review](../engineering-foundations/code-review.md)
- [REST APIs and HTTP Testing](./rest-api.md)
- [Java](../programming/languages/java/README.md)
- [Software Design](../software-design/README.md)
- [Docker](../platform-engineering/docker.md)
- [Kubernetes](../platform-engineering/kubernetes.md)

## Further Reading

- [ISTQB Certified Tester Foundation Level](https://istqb.org/help/ctfl-v40/)
- [Google Testing Blog: Test Sizes](https://testing.googleblog.com/2010/12/test-sizes.html)
- [OWASP Web Security Testing Guide](https://owasp.org/www-project-web-security-testing-guide/)
- [W3C: Evaluating Web Accessibility](https://www.w3.org/WAI/test-evaluate/)
- [W3C: WCAG 2 Overview](https://www.w3.org/WAI/standards-guidelines/wcag/)
