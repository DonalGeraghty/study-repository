# SOLID Principles

SOLID is a set of five design heuristics for assigning responsibilities and managing dependencies in object-oriented code. The principles help a design respond to change, but they are not laws, metrics, or a requirement to create an interface for every class.

| Principle | Design question |
| --- | --- |
| Single Responsibility | Does this unit contain one cohesive responsibility? |
| Open/Closed | Can an expected variation be added without repeatedly rewriting stable policy? |
| Liskov Substitution | Does every subtype preserve the contract clients rely on? |
| Interface Segregation | Are clients forced to depend on operations they do not use? |
| Dependency Inversion | Does policy depend on abstractions instead of technical details? |

## Single Responsibility Principle

> A module should have one reason to change.

“One reason” does not mean one method. It means one cohesive responsibility, usually associated with one stakeholder or source of change.

### Problem

```java
public final class InvoiceService {
    public Money calculateTotal(Invoice invoice) {
        return invoice.subtotal().plus(invoice.tax());
    }

    public void save(Invoice invoice) {
        // SQL and database mapping
    }

    public void emailReceipt(Invoice invoice) {
        // Template and email-provider logic
    }
}
```

Pricing, persistence, and communication change for different reasons. A database migration should not risk pricing behaviour.

### Improvement

```java
public final class InvoiceCalculator {
    public Money totalFor(Invoice invoice) {
        return invoice.subtotal().plus(invoice.tax());
    }
}

public interface InvoiceRepository {
    void save(Invoice invoice);
}

public interface ReceiptSender {
    void sendReceipt(Invoice invoice);
}
```

An application service can coordinate these collaborators. Coordination itself is a valid responsibility.

### Warning Signs

- A class description needs “and” several times.
- A small change repeatedly touches unrelated methods.
- Tests require many unrelated dependencies.
- Different teams or stakeholders frequently edit the same file.
- The class mixes domain policy, transport, persistence, and presentation.

Do not split mechanically until every class has one method. Excessive fragmentation can make a simple workflow harder to understand.

## Open/Closed Principle

> Software entities should be open for extension but closed for modification.

This does not mean existing code must never change. It means stable policy should expose deliberate extension points for variations that are genuinely expected.

### Repeated Branching

```java
public Money deliveryCost(Order order) {
    return switch (order.deliveryType()) {
        case STANDARD -> standardCost(order);
        case EXPRESS -> expressCost(order);
    };
}
```

If delivery types change often and the branch is repeated across the system, model the variation:

```java
public interface DeliveryPricing {
    Money priceFor(Order order);
}

public final class StandardDeliveryPricing implements DeliveryPricing {
    @Override
    public Money priceFor(Order order) {
        return Money.of("EUR", 5_00);
    }
}

public final class Checkout {
    private final DeliveryPricing deliveryPricing;

    public Checkout(DeliveryPricing deliveryPricing) {
        this.deliveryPricing = deliveryPricing;
    }

    public Money totalFor(Order order) {
        return order.subtotal().plus(deliveryPricing.priceFor(order));
    }
}
```

Adding a pricing policy no longer changes checkout policy. Construction code still has to select an implementation, and that is expected.

### Apply Carefully

- Abstract around observed or strongly expected variation.
- Keep the simplest branch when the set is small and stable.
- Prefer explicit configuration to reflection-based magic.
- Revisit an extension point when its assumptions change.

An unused extension point is cost, not flexibility.

## Liskov Substitution Principle

> Code using a base type should continue to work correctly with any of its subtypes.

Substitution is behavioural. Matching method signatures is insufficient. A subtype should not:

- require stronger preconditions;
- promise weaker postconditions;
- break the parent's invariants;
- introduce surprising exceptions, side effects, or performance behaviour where these form part of the contract.

### Violation

```java
public interface Account {
    Money balance();
    void withdraw(Money amount);
}

public final class FixedTermAccount implements Account {
    private final Money balance;

    public FixedTermAccount(Money balance) {
        this.balance = balance;
    }

    @Override
    public Money balance() {
        return balance;
    }

    @Override
    public void withdraw(Money amount) {
        throw new UnsupportedOperationException("Funds are locked");
    }
}
```

A caller accepting `Account` reasonably expects withdrawal to be supported. The subtype rejects a valid base operation.

### Improvement

```java
public interface Account {
    Money balance();
}

public interface WithdrawableAccount extends Account {
    void withdraw(Money amount);
}

public final class CurrentAccount implements WithdrawableAccount {
    private Money balance;

    public CurrentAccount(Money balance) {
        this.balance = balance;
    }

    @Override
    public Money balance() {
        return balance;
    }

    @Override
    public void withdraw(Money amount) {
        balance = balance.minus(amount);
    }
}

public final class FixedTermAccount implements Account {
    private final Money balance;

    public FixedTermAccount(Money balance) {
        this.balance = balance;
    }

    @Override
    public Money balance() {
        return balance;
    }
}
```

The type system now states the difference instead of deferring the surprise until runtime.

### Contract Tests

When several implementations share a contract, run the same behavioural test suite against all of them. This is especially useful for repositories, storage adapters, and service clients.

## Interface Segregation Principle

> Clients should not be forced to depend on methods they do not use.

Design interfaces around client capabilities rather than around a large implementation.

### Fat Interface

```java
public interface OfficeMachine {
    void print(Document document);
    Scan scan(Page page);
    void fax(Document document);
}
```

A print-only device must throw exceptions or provide meaningless methods.

### Focused Capabilities

```java
public interface Printer {
    void print(Document document);
}

public interface Scanner {
    Scan scan(Page page);
}

public final class MultiFunctionDevice implements Printer, Scanner {
    // Implements both complete contracts.
}
```

Small interfaces improve discoverability and reduce the number of clients affected by change. They should still be cohesive: splitting every method into its own interface only creates noise.

## Dependency Inversion Principle

> High-level policy and low-level details should depend on abstractions. Abstractions should not be owned by volatile details.

### Direct Dependency

```java
public final class PlaceOrder {
    private final JdbcOrderRepository repository =
            new JdbcOrderRepository(System.getenv("DB_URL"));

    public void execute(Order order) {
        repository.save(order);
    }
}
```

The use case chooses its database, configuration source, and construction. It is difficult to test or reuse without JDBC.

### Inverted Dependency

```java
public interface OrderRepository {
    void save(Order order);
}

public final class PlaceOrder {
    private final OrderRepository repository;

    public PlaceOrder(OrderRepository repository) {
        this.repository = repository;
    }

    public void execute(Order order) {
        repository.save(order);
    }
}
```

The application owns the abstraction it needs. A JDBC adapter implements it, while a composition root wires the concrete object graph:

```java
OrderRepository repository = new JdbcOrderRepository(dataSource);
PlaceOrder placeOrder = new PlaceOrder(repository);
```

### Dependency Inversion vs Dependency Injection

- **Dependency inversion** is the design choice that policy depends on an abstraction.
- **Dependency injection** is a technique for supplying a dependency from outside.
- An IoC container can perform injection, but constructor calls often suffice.

Injecting a concrete low-level type improves test setup but does not by itself invert the dependency.

## How the Principles Work Together

Consider a checkout use case:

- SRP separates checkout policy from database and email concerns.
- OCP provides a stable boundary for delivery or discount policies that vary.
- LSP ensures each policy honours the same behavioural contract.
- ISP keeps checkout dependent only on the operations it needs.
- DIP makes the use case depend on repository and gateway abstractions.

Applying one principle can create tension with another. Splitting responsibilities may add more dependencies; introducing an abstraction may increase indirection. Prefer the design that keeps the current system understandable while protecting important change boundaries.

## Misuses to Avoid

- Creating an interface and implementation pair for every class.
- Treating any edit to existing code as an OCP failure.
- Defining SRP as “a class can do only one thing.”
- Checking LSP only at compile time.
- Splitting interfaces without considering real clients.
- Using a dependency-injection framework as proof of DIP.
- Mocking every collaborator and coupling tests to call order.
- Applying all five principles to trivial data structures.

## Review Checklist

### Responsibilities

- [ ] Can the class's responsibility be described in one cohesive sentence?
- [ ] Are domain decisions separated from I/O and framework code?
- [ ] Do collaborators represent different responsibilities rather than arbitrary fragments?

### Extension and Contracts

- [ ] Is an abstraction protecting a real variation?
- [ ] Can every implementation satisfy the complete contract?
- [ ] Are preconditions, results, failures, and side effects clear?

### Interfaces and Dependencies

- [ ] Does each client see only the capabilities it needs?
- [ ] Does business policy avoid direct knowledge of infrastructure?
- [ ] Are dependencies visible in constructors or method parameters?
- [ ] Is object construction kept near the application boundary?

## Interview Exercise

Given an order service that calculates a discount, writes directly to a database, and emails a receipt:

1. Identify the reasons it changes.
2. Refactor only the boundaries that provide value.
3. State the contracts and likely variations.
4. Explain how the concrete application is assembled.
5. Name one abstraction you deliberately did not add.

A strong answer discusses context and trade-offs rather than merely naming each letter.

## Related Guides

- [Object-Oriented Programming](./object-oriented-programming.md)
- [Design Patterns](./design-patterns.md)
- [Domain-Driven Design](./domain-driven-design.md)
- [Testing](../quality-engineering/testing.md)

Return to [Software Design](./README.md).
