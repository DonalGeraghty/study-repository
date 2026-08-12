# Software Design

Software design is the work of arranging code and responsibilities so that a system is correct today and can change safely tomorrow. Good design is not measured by the number of patterns it uses. It is measured by how clearly the code expresses its purpose, protects its rules, and supports likely changes.

## What This Section Covers

| Guide | Main question |
| --- | --- |
| [Object-Oriented Programming](./object-oriented-programming.md) | How should state and behaviour be grouped? |
| [SOLID Principles](./solid-principles.md) | How should responsibilities and dependencies be arranged? |
| [Design Patterns](./design-patterns.md) | Which established structures fit a recurring design problem? |
| [Domain-Driven Design](./domain-driven-design.md) | How should software model a complex business domain? |

These subjects overlap, but they operate at different levels:

- OOP supplies modelling mechanisms such as objects, interfaces, encapsulation, and polymorphism.
- SOLID offers heuristics for assigning responsibilities and controlling dependencies.
- Patterns give names to recurring arrangements of collaborating objects.
- DDD aligns the model and its boundaries with business language and rules.

## Design Goals

A useful design balances several qualities:

- **Correctness:** domain rules and technical constraints are enforced.
- **Clarity:** names, boundaries, and control flow reveal intent.
- **Changeability:** likely changes remain local.
- **Testability:** important behaviour can be exercised without excessive setup.
- **Reliability:** failures are contained and resources are managed safely.
- **Security:** trust boundaries and sensitive operations are explicit.
- **Operability:** the system can be observed, diagnosed, and supported.

These qualities can conflict. An abstraction that makes one variation easy may add complexity everywhere else. Design therefore involves trade-offs, not universal rules.

## Core Ideas

### High Cohesion

A cohesive module contains closely related behaviour. A class that calculates prices, sends email, writes SQL, and renders HTML has several unrelated reasons to change.

### Low Coupling

Coupling is the degree to which one part depends on another. Some coupling is unavoidable; the aim is to depend on small, stable contracts and keep volatile details behind boundaries.

### Encapsulation

An object should protect its valid state. Callers ask it to perform meaningful operations instead of changing its fields in arbitrary sequences.

### Dependency Direction

Business policy should not be forced to know about databases, frameworks, or network clients. Define abstractions near the policy that uses them, then supply technical implementations at the application boundary.

### Composition Before Inheritance

Inheritance is appropriate for a genuine substitutable *is-a* relationship with a stable contract. Composition is usually safer when behaviour must vary independently or be assembled at runtime.

### Explicit Boundaries

Boundaries isolate different responsibilities, rates of change, or vocabularies. Examples include an HTTP adapter around an application service, a repository around persistence, and an anti-corruption layer around another system.

## A Practical Design Process

1. Describe the behaviour and constraints before choosing a pattern.
2. Identify the rules that must always remain true.
3. Name responsibilities using the language of the problem.
4. Separate policy from input/output and infrastructure details.
5. List the changes that are likely, not every imaginable change.
6. Compare the simplest viable options and record important trade-offs.
7. Implement a small slice and use tests or operational evidence as feedback.
8. Refactor when repetition or change pressure reveals a useful abstraction.

Do not introduce an interface, layer, or service only because a diagram appears more architectural. Each boundary should protect a rule, separate a concern, enable a real variation, or improve testing and operation.

## Questions for a Design Review

- Does each concept have a clear name and owner?
- Where are invariants enforced?
- Which dependencies point toward business policy, and which point toward tools?
- What happens when an external dependency is slow or unavailable?
- Can invalid states be represented?
- Is shared mutable state controlled?
- Does inheritance preserve the parent contract?
- Is a pattern solving an observed problem or adding ceremony?
- Are module and service boundaries aligned with different responsibilities?
- Can a new reader trace one important use case end to end?

## Common Failure Modes

- **Premature abstraction:** generalising before the repeated shape is understood.
- **Primitive obsession:** passing raw strings and numbers where domain concepts have rules.
- **God objects:** concentrating unrelated decisions in one class.
- **Anemic models:** placing every rule in procedural services while domain objects remain data containers. For simple CRUD, however, a simple model may be entirely appropriate.
- **Deep inheritance:** making behaviour depend on fragile parent-child chains.
- **Pattern collecting:** adding factories, builders, or repositories without a concrete need.
- **Framework-shaped design:** allowing annotations and persistence models to dictate the domain model.
- **Distributed monolith:** splitting deployment while preserving tightly coupled changes and calls.

## Suggested Learning Order

1. Learn encapsulation, composition, interfaces, and polymorphism.
2. Use SOLID to critique responsibilities and dependency direction.
3. Study patterns as trade-off vocabulary, not recipes.
4. Apply DDD when business language, rules, and boundaries are genuinely complex.
5. Practise by improving real code in small, testable steps.

## Practice Exercise

Design an order checkout flow. Include pricing, payment, persistence, and customer notification.

- First, sketch the simplest working design.
- Mark the domain rules and infrastructure details.
- Identify which behaviour is likely to vary.
- Apply only the principles or patterns that solve those pressures.
- Explain one alternative and why you rejected it.

The quality of the explanation matters more than the number of boxes in the diagram.

## Completion Checklist

- [ ] I can distinguish a principle, pattern, and domain model.
- [ ] I can explain cohesion and coupling with examples.
- [ ] I can justify composition or inheritance for a given relationship.
- [ ] I can locate business rules and infrastructure boundaries.
- [ ] I can discuss the cost as well as the benefit of an abstraction.
- [ ] I can evolve a design from evidence instead of guessing every future need.

## Related Guides

- [Java](../programming/languages/java/README.md)
- [Testing](../quality-engineering/testing.md)
- [Code Review](../engineering-foundations/code-review.md)
- [REST API](../quality-engineering/rest-api.md)
- [Documentation Library](../README.md)
