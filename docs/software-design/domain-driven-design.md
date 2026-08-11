# Domain-Driven Design

Domain-Driven Design (DDD) is an approach for building software around a complex business domain. It combines close collaboration with domain experts, precise language, explicit model boundaries, and implementation patterns that protect business rules.

DDD is most valuable when rules, terminology, and competing business needs create substantial complexity. It is usually unnecessary ceremony for a small CRUD application with little domain behaviour.

## Domain and Model

- **Domain:** the problem area the software supports, such as lending, insurance, or fulfilment.
- **Domain model:** a deliberately simplified representation of relevant concepts and rules.
- **Domain expert:** someone with deep knowledge of the business process; expertise may be distributed across several roles.
- **Invariant:** a rule that must always be true within a consistency boundary.

A model is not a mirror of every real-world detail. It is designed for a specific purpose.

## Ubiquitous Language

Developers and domain experts build a shared language and use it consistently in conversations, examples, tests, diagrams, and code.

If the business distinguishes a **quote**, **order**, and **shipment**, the code should not collapse all three into `TransactionRecord`. Ambiguous words should be challenged and refined.

Ubiquitous language is local to a model boundary. “Customer” may mean a purchasing account in Sales and a recipient at an address in Shipping. Forcing one enterprise-wide class can create a confused model.

## Strategic Design

Strategic design decides where models apply and how different parts of the organisation relate.

### Subdomains

| Type | Meaning | Typical approach |
| --- | --- | --- |
| Core | Creates important business advantage | Invest in deep modelling and domain expertise |
| Supporting | Necessary and business-specific, but not differentiating | Build simply and deliberately |
| Generic | Common capability such as email or identity | Prefer a product or established solution |

The classification is contextual and can change as the business changes.

### Bounded Context

A bounded context is the boundary within which a model and its language are consistent. It may align with a module, service, or team boundary, but it is a modelling boundary first—not automatically a microservice.

Within an Ordering context, `Product` may represent a purchasable item and price. In Inventory, it may represent stock-keeping units and availability. Each model should serve its own decisions.

### Context Map

A context map records important relationships between bounded contexts:

- which context is upstream or downstream;
- how data and meaning are translated;
- which team controls a contract;
- where coordination is required;
- whether an anti-corruption layer protects one model from another.

An **anti-corruption layer** translates an external model into local concepts so that provider terminology and assumptions do not spread through the domain.

## Tactical Building Blocks

### Entity

An entity is defined by continuity and identity rather than all of its current attributes. An order remains the same order as its status and lines change.

```java
public record OrderId(UUID value) {
    public OrderId {
        Objects.requireNonNull(value, "Order ID is required");
    }
}
```

Use stable identity deliberately. Avoid equality based on mutable entity attributes.

### Value Object

A value object is defined by its attributes, has no separate identity, and is normally immutable. It can validate construction and provide domain operations.

```java
public record Money(String currency, long minorUnits) {
    public Money {
        if (currency == null || !currency.matches("[A-Z]{3}")) {
            throw new IllegalArgumentException("Use a three-letter currency code");
        }
    }

    public Money plus(Money other) {
        requireSameCurrency(other);
        return new Money(currency, Math.addExact(minorUnits, other.minorUnits));
    }

    private void requireSameCurrency(Money other) {
        if (!currency.equals(other.currency)) {
            throw new IllegalArgumentException("Currency mismatch");
        }
    }
}
```

The representation here assumes a known minor-unit convention. Real financial systems must define rounding and currencies with unusual decimal rules explicitly.

### Aggregate and Aggregate Root

An aggregate is a cluster of entities and value objects changed as one consistency boundary. Its aggregate root is the only entry point for outside modification.

```java
public final class Order {
    private final OrderId id;
    private final List<OrderLine> lines = new ArrayList<>();
    private OrderStatus status = OrderStatus.DRAFT;

    public Order(OrderId id) {
        this.id = Objects.requireNonNull(id);
    }

    public void addLine(ProductId productId, int quantity, Money unitPrice) {
        if (status != OrderStatus.DRAFT) {
            throw new IllegalStateException("Only draft orders can change");
        }
        if (quantity <= 0) {
            throw new IllegalArgumentException("Quantity must be positive");
        }
        lines.add(new OrderLine(productId, quantity, unitPrice));
    }

    public void submit() {
        if (lines.isEmpty()) {
            throw new IllegalStateException("An order must contain at least one line");
        }
        status = OrderStatus.SUBMITTED;
    }

    public List<OrderLine> lines() {
        return List.copyOf(lines);
    }
}
```

Aggregate rules:

- protect invariants inside the boundary;
- reference other aggregates by identity where practical;
- keep boundaries small enough for safe transactions;
- change one aggregate per transaction where possible;
- coordinate cross-aggregate outcomes with application logic or events and accept eventual consistency when appropriate.

An aggregate is not merely an object graph loaded by an ORM. Its boundary is chosen from consistency rules.

### Domain Service

A domain service contains domain behaviour that does not naturally belong to one entity or value object.

```java
public interface ExchangeRate {
    Money convert(Money amount, Currency target);
}
```

Do not move all behaviour into services. First ask which object owns the information and invariant.

### Repository

A repository provides domain-oriented access to aggregates.

```java
public interface OrderRepository {
    Optional<Order> findBy(OrderId id);
    void save(Order order);
}
```

The domain-facing contract should not expose SQL, ORM sessions, or transport DTOs. A repository usually exists per aggregate root, not per database table.

### Factory

A factory constructs a valid aggregate or complex value when creation rules do not belong in a simple constructor. Named factory methods can express intent:

```java
Order renewal = Order.renewalOf(previousOrder, renewalDate);
```

### Domain Event

A domain event records a meaningful fact that has happened, such as `OrderSubmitted` or `PaymentAuthorised`.

Events can decouple secondary reactions, but reliable delivery across processes requires explicit handling for transactions, duplicate delivery, ordering, versioning, and failure. A database outbox is one common solution.

## Primitive Obsession

Primitive obsession means representing domain concepts only with general-purpose primitives or library types even when those concepts have distinct rules or behaviour.

```java
void charge(String customerId, String email, long amount, String currency)
```

This signature permits swapped strings, invalid email, unsupported currency, negative amount, and inconsistent units. Domain types make meaning and validation explicit:

```java
void charge(
        CustomerId customerId,
        EmailAddress email,
        Money amount)
```

### Useful Value Types

```java
public record CustomerId(UUID value) {
    public CustomerId {
        Objects.requireNonNull(value, "Customer ID is required");
    }
}

public record EmailAddress(String value) {
    public EmailAddress {
        if (value == null || value.isBlank() || !value.contains("@")) {
            throw new IllegalArgumentException("Invalid email address");
        }
        value = value.trim().toLowerCase(Locale.ROOT);
    }
}
```

Benefits include:

- validation in one place;
- impossible parameter swaps between unrelated concepts;
- domain-specific operations and formatting;
- clearer method signatures and tests;
- safer refactoring when representation changes.

Do not wrap every primitive automatically. A type earns its place when it carries meaning, rules, units, formatting, behaviour, or a recurring risk of confusion. A pagination limit local to one endpoint may remain an `int`; a regulated account number probably should not remain a `String`.

## Application and Infrastructure

A common dependency arrangement is:

```text
HTTP / message adapter
        |
        v
application use case
        |
        v
domain model
        ^
        |
repository and gateway interfaces
        ^
        |
database / provider adapters
```

- **Application services** coordinate a use case, transactions, repositories, and external ports.
- **Domain objects** make business decisions and protect invariants.
- **Infrastructure adapters** implement persistence, messaging, time, and third-party integration.
- **Interface adapters** translate HTTP, messages, command-line input, or UI actions.

The exact package layout matters less than keeping dependencies and responsibilities clear.

```java
public final class SubmitOrder {
    private final OrderRepository orders;
    private final EventPublisher events;

    public SubmitOrder(
            OrderRepository orders,
            EventPublisher events) {
        this.orders = orders;
        this.events = events;
    }

    public void execute(OrderId orderId) {
        Order order = orders.findBy(orderId)
                .orElseThrow(OrderNotFound::new);

        order.submit();
        orders.save(order);
        events.publish(new OrderSubmitted(orderId));
    }
}
```

Production code must decide how saving and event publication are made reliable together.

## Discovery and Modelling

Useful modelling activities include:

- walking through concrete business scenarios;
- writing examples of rules and exceptions;
- event storming with domain events, commands, policies, and actors;
- mapping terminology disagreements;
- identifying consistency and transaction boundaries;
- testing a model in code and refining it from feedback.

Models should evolve. A team learns the domain through implementation, incidents, customer behaviour, and conversations with experts.

## Testing a Domain Model

- Unit-test value-object validation and operations.
- Test aggregate behaviour through its public commands.
- Assert meaningful state and emitted events, not private fields.
- Integration-test repository mappings and transaction behaviour.
- Use contract tests for multiple adapter implementations.
- Test translations at context and third-party boundaries.
- Include concurrency tests when two commands may violate an invariant.

Mocks are useful at infrastructure boundaries, but excessive mocking of domain objects often hides the model rather than tests it.

## DDD and Microservices

DDD does not require microservices. A modular monolith can contain several bounded contexts and is often easier to build and operate.

Bounded contexts can inform later service boundaries because they clarify ownership and language. Deployment should remain a separate decision based on scaling, release independence, resilience, security, and team needs.

## Common Mistakes

- Starting with entities and repositories before learning the domain.
- Using database tables as aggregate boundaries.
- Sharing one canonical model across incompatible contexts.
- Making every noun an entity and every method a service.
- Treating annotations or folder names as evidence of DDD.
- Building large aggregates that lock or load too much data.
- Publishing events without delivery and idempotency plans.
- Assuming an anemic model is always wrong; simple CRUD may not need rich behaviour.
- Wrapping every primitive without adding meaning.
- Splitting contexts into network services by default.

## Review Checklist

### Language and Boundaries

- [ ] Do code and tests use terms recognised by domain experts?
- [ ] Are ambiguous terms scoped to a bounded context?
- [ ] Are context relationships and translations explicit?
- [ ] Is the core domain receiving the most modelling effort?

### Model

- [ ] Are identity and value represented deliberately?
- [ ] Do value objects validate themselves and remain immutable?
- [ ] Does each aggregate protect a real consistency boundary?
- [ ] Can changes occur only through the aggregate root?
- [ ] Are repositories defined around aggregates and domain needs?
- [ ] Are primitive values wrapped where rules or ambiguity justify it?

### Operation

- [ ] Are transaction boundaries explicit?
- [ ] Are event delivery, duplication, ordering, and versioning addressed?
- [ ] Are external models translated at the boundary?
- [ ] Is a service boundary justified independently of the model boundary?

## Practice Exercise

Model a lending system:

1. Build a shared language for member, copy, loan, reservation, and fine.
2. Identify the core, supporting, and generic subdomains.
3. Propose bounded contexts and explain one term that differs between them.
4. Identify entities, value objects, aggregates, and invariants.
5. Replace meaningful primitives such as ISBN, Member ID, and Money.
6. Walk through borrowing, returning, and overdue scenarios.
7. Explain transaction and event-delivery choices.

Revise the model when a scenario feels awkward; that friction is useful feedback.

## Related Guides

- [Software Design](./README.md)
- [Object-Oriented Programming](./object-oriented-programming.md)
- [SOLID Principles](./solid-principles.md)
- [Design Patterns](./design-patterns.md)
- [REST API](../quality-engineering/rest-api.md)
