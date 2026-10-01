---
tags:
  - software-design
---

# Design Patterns

A design pattern is a named, reusable approach to a recurring design problem. It describes a context, collaborating roles, and trade-offs; it is not code to copy unchanged.

Patterns are useful because they give teams a shared vocabulary. “Use a strategy for pricing” communicates more than a diagram of unnamed classes. The name does not replace an explanation of why the pattern fits.

## Pattern Categories

| Category | Purpose | Examples |
| --- | --- | --- |
| Creational | Control or clarify object construction | Factory Method, Abstract Factory, Builder, Singleton |
| Structural | Compose objects or adapt boundaries | Adapter, Decorator, Facade, Composite |
| Behavioural | Organise algorithms and collaboration | Strategy, Observer, Command, State |
| Architectural or domain | Shape larger boundaries and models | Repository, Ports and Adapters, CQRS |

The classic categories are useful for object-level patterns. Teams also use pattern language at domain, application, integration, and distributed-system levels.

## How to Select a Pattern

1. Describe the problem without naming a pattern.
2. Identify the forces: variation, lifecycle, coupling, performance, consistency, and failure.
3. Start with the simplest clear design.
4. Compare a pattern's benefit with its indirection and operational cost.
5. Confirm that tests and names still describe behaviour clearly.

Do not choose a pattern because its class diagram looks familiar.

## Factory

A factory centralises construction when selecting or assembling a concrete type would otherwise leak into clients.

```java
public final class NotificationChannelFactory {
    private final Map<String, Supplier<NotificationChannel>> channels;

    public NotificationChannelFactory(
            Map<String, Supplier<NotificationChannel>> channels) {
        this.channels = Map.copyOf(channels);
    }

    public NotificationChannel create(String name) {
        Supplier<NotificationChannel> supplier = channels.get(name);
        if (supplier == null) {
            throw new IllegalArgumentException("Unsupported channel: " + name);
        }
        return supplier.get();
    }
}
```

This registry allows composition code to add a channel without editing a switch. A small switch is still a reasonable factory when the choices are fixed.

“Factory” is an informal umbrella term. **Factory Method** delegates creation through an overridable method; **Abstract Factory** provides a family of related products. Use the precise name when the distinction matters.

**Costs:** hides direct construction, adds another abstraction, and can make dependencies less obvious if used as a service locator.

## Builder

A builder makes multi-step construction readable when an object has several optional values or must be assembled gradually.

```java
TestUser user = TestUser.builder()
        .withName("Ava")
        .withRole(Role.ADMIN)
        .active()
        .build();
```

Builders are especially useful for test data. A constructor or named factory is clearer when only a few required arguments exist.

Ensure `build()` validates the final object. A builder should not make invalid combinations silently possible.

## Singleton

Singleton is a creational pattern that controls construction so callers share one instance within a defined scope, usually through a global access point. It can fit an application-wide object with one deliberate lifetime, such as an immutable configuration snapshot. The design question is whether that shared lifetime is required, rather than whether global access is convenient.

### Example: One Configuration Instance

This Java example uses a private constructor and a static instance. The fixed value keeps the example focused on object identity; a real application's configuration needs a separate loading and validation policy.

```java
public final class AppSettings {
    private static final AppSettings INSTANCE = new AppSettings();

    private final int maxBatchSize;

    private AppSettings() {
        this.maxBatchSize = 100;
    }

    public static AppSettings getInstance() {
        return INSTANCE;
    }

    public int maxBatchSize() {
        return maxBatchSize;
    }
}
```

Ordinary callers cannot construct `AppSettings` themselves. Every call to `getInstance()` for this loaded class returns the same object. The instance is created during class initialisation, which Java coordinates safely across threads. This avoids the race in an unsynchronised lazy implementation that checks whether an instance is null before constructing it. [Java class initialisation](https://docs.oracle.com/javase/specs/jls/se25/html/jls-12.html#jls-12.4.2).

### Scope, State and Testing

One instance does not make arbitrary methods thread-safe. This example exposes only immutable state; adding a mutable counter, collection or setter would require its own concurrency design. `static final` fixes the reference, not the mutability of the referenced object.

The scope also matters. Separate JVM processes have separate instances, and separate defining class loaders can load distinct copies of the class. A Singleton does not coordinate several Kubernetes replicas or provide a distributed lock.

Global calls hide a dependency inside the caller and make it harder to substitute configuration in a test. Shared mutable state can also leak between tests and create order dependence. Often the simpler design is to construct a normal object once at application startup and inject it into its consumers, keeping both ownership and dependencies explicit.

A dependency-injection framework's singleton scope is a related lifetime policy, not necessarily this class-level pattern. In Spring it means one instance per bean definition per container; it does not guarantee one instance across all containers or make the bean thread-safe. [Spring bean scopes](https://docs.spring.io/spring-framework/reference/core/beans/factory-scopes.html).

### Worked Prediction: Same Instance or Same Value?

Inside a method, evaluate:

```java
AppSettings first = AppSettings.getInstance();
AppSettings second = AppSettings.getInstance();

System.out.println(first == second);
System.out.println(first.maxBatchSize());
```

**Check your reasoning:** The output is `true`, then `100`. Both variables refer to the same object, rather than two objects with matching values. If the application starts a second JVM, that process creates its own instance. Changing this class to hold mutable state would not change the identity result, but would introduce shared-state concerns.

## Strategy

Strategy encapsulates interchangeable algorithms behind one contract.

```java
public interface ShippingCost {
    Money calculateFor(Shipment shipment);
}

public final class Checkout {
    private final ShippingCost shippingCost;

    public Checkout(ShippingCost shippingCost) {
        this.shippingCost = shippingCost;
    }

    public Money totalFor(Shipment shipment) {
        return shipment.itemTotal().plus(shippingCost.calculateFor(shipment));
    }
}
```

Use Strategy when the algorithm varies independently of its caller and implementations share a meaningful contract. A lambda may be enough for a stateless, single-method strategy.

**Costs:** more types and configuration; the caller or composition root must choose an implementation.

## Adapter

An adapter translates an external or incompatible interface into the contract the application wants.

```java
public final class AcmePaymentAdapter implements PaymentGateway {
    private final AcmePaymentsClient client;

    public AcmePaymentAdapter(AcmePaymentsClient client) {
        this.client = client;
    }

    @Override
    public PaymentReceipt charge(Money amount, PaymentMethod method) {
        AcmeCharge response = client.createCharge(
                amount.minorUnits(),
                amount.currencyCode(),
                method.token());

        return new PaymentReceipt(response.id(), response.approved());
    }
}
```

The adapter contains provider-specific types and translation. Domain and application code remain expressed in their own language.

**Costs:** translation must be tested, error semantics may not map perfectly, and provider capabilities can leak if the local contract becomes too broad.

## Decorator

A decorator wraps an implementation of the same contract to add behaviour.

```java
public final class RetryingPaymentGateway implements PaymentGateway {
    private final PaymentGateway delegate;
    private final RetryPolicy retryPolicy;

    public RetryingPaymentGateway(
            PaymentGateway delegate,
            RetryPolicy retryPolicy) {
        this.delegate = delegate;
        this.retryPolicy = retryPolicy;
    }

    @Override
    public PaymentReceipt charge(Money amount, PaymentMethod method) {
        return retryPolicy.execute(
                () -> delegate.charge(amount, method));
    }
}
```

Decorators can add logging, caching, metrics, authorisation, or retry behaviour without changing the core implementation. Order matters when several decorators are stacked, and retries are safe only for operations with suitable idempotency guarantees.

```mermaid
classDiagram
    class PaymentGateway {
        <<interface>>
        +charge(Money, PaymentMethod) PaymentReceipt
    }
    class AcmePaymentAdapter {
        +charge(Money, PaymentMethod) PaymentReceipt
    }
    class RetryingPaymentGateway {
        -delegate PaymentGateway
        +charge(Money, PaymentMethod) PaymentReceipt
    }
    PaymentGateway <|.. AcmePaymentAdapter
    PaymentGateway <|.. RetryingPaymentGateway
    RetryingPaymentGateway o-- PaymentGateway : delegate
```

The adapter and the decorator both implement the same `PaymentGateway` contract; the decorator wraps any implementation of it, including the adapter, without either knowing about the other.

## Observer and Domain Events

Observer notifies subscribers when something happens. In-process listeners are simple, while messages between processes introduce delivery, ordering, duplication, schema evolution, and observability concerns.

Use past-tense event names such as `OrderPlaced`. Treat events as facts, not commands. Handlers should be idempotent where a message may be delivered more than once.

Avoid hiding the primary business flow in an event maze. Direct calls are often clearer when the action is required for the use case to succeed.

## State

The State pattern moves behaviour that depends on lifecycle state into state-specific objects. It can replace large repeated conditionals for complex workflows.

For a small fixed lifecycle, an enum and explicit transition method may be clearer. The important design is that invalid transitions are rejected in one place.

## Repository

A repository presents collection-like access to aggregates or domain objects while hiding persistence mechanics.

```java
public interface OrderRepository {
    Optional<Order> findBy(OrderId id);
    void save(Order order);
}
```

Keep the interface expressed in domain terms. Returning database query builders or ORM sessions defeats the boundary. Repositories are most valuable in rich domain models; a direct data-access approach may be simpler for read-heavy or CRUD-only features.

## Page Object Model

Page objects model the services a page or reusable component offers to a UI test. They keep locators and interaction mechanics out of test intent.

```java
public final class LoginPage {
    private final WebDriver driver;

    private final By email = By.id("email");
    private final By password = By.id("password");
    private final By submit = By.cssSelector("[data-testid='login']");
    private final By error = By.cssSelector("[role='alert']");

    public LoginPage(WebDriver driver) {
        this.driver = driver;
        new WebDriverWait(driver, Duration.ofSeconds(10))
                .until(ExpectedConditions.visibilityOfElementLocated(submit));
    }

    public HomePage logInAs(String username, String secret) {
        driver.findElement(email).sendKeys(username);
        driver.findElement(password).sendKeys(secret);
        driver.findElement(submit).click();
        return new HomePage(driver);
    }

    public LoginPage attemptLogin(String username, String secret) {
        driver.findElement(email).sendKeys(username);
        driver.findElement(password).sendKeys(secret);
        driver.findElement(submit).click();
        return this;
    }

    public String errorMessage() {
        return new WebDriverWait(driver, Duration.ofSeconds(10))
                .until(ExpectedConditions.visibilityOfElementLocated(error))
                .getText();
    }
}
```

```mermaid
sequenceDiagram
    participant T as Test
    participant L as LoginPage
    participant H as HomePage
    T->>L: logInAs(username, secret)
    L->>L: fill email, password, click submit
    L-->>T: return HomePage
    T->>H: assert expected state
```

### Page Object Guidance

Expose user-facing services such as `logInAs`, keeping locators and waiting details inside the page or component. When navigation has a known result, return the next page or component to make that transition clear.

Model repeated widgets as component objects and prefer composition over a deep base-page hierarchy. Inject the driver or browser session instead of storing it globally.

Keep test assertions in tests, although a page object may verify that it loaded correctly. A trivial page does not need a page object if the extra abstraction adds no clarity.

A page object is a test design pattern, not a copy of the page's HTML structure.

## Pattern Interactions

Patterns commonly collaborate:

- a factory creates a configured strategy;
- an adapter implements a port owned by the application;
- decorators wrap that adapter with metrics and retry behaviour;
- a repository loads an aggregate;
- the aggregate records a domain event;
- a builder creates readable test data.

This does not mean all are needed together. Each pattern must earn its place.

## Common Misuses

- Pattern matching before understanding the problem.
- Creating abstractions with only a speculative future use.
- Naming a switch “Factory” and claiming no future code will change.
- Using Singleton for mutable global state.
- Using Observer for required synchronous steps.
- Wrapping every dependency in several pass-through layers.
- Building generic repositories that erase domain language.
- Creating page objects with public locators and assertions for every test.

## Worked Scenario: Decorator Order Changes Meaning

Compare a metrics decorator around a retry decorator with a retry decorator around a metrics decorator. The underlying operation fails transiently twice and succeeds on its third attempt. Predict how many calls the metrics layer observes.

**Check your reasoning:** Metrics outside retry observes one logical operation including its retries; metrics inside retry observes three attempts. Both can be useful, but labels and latency meaning must be explicit. A design that reports attempts as customer operations can mislead incident analysis.

For payment operations, first establish whether repetition is safe and how the provider recognises the same operation. A generic retry wrapper cannot invent that contract. Explain why the provider-specific translation belongs in an adapter, while reusable timing behaviour can live in a decorator. The pattern names should follow those responsibilities, not replace their explanation.

## Interview Questions

> [!question] Interview Questions
> - How would you decide whether a design pattern is worth the indirection it adds, versus keeping the simpler design?
> - Given a checkout that needs to support several payment providers, how would you design the abstraction boundary, and where would you put provider selection?
> - How would you add metrics or retry behaviour to a payment adapter without editing the adapter itself?
> - What does Singleton guarantee, and why might constructing one object and injecting it be easier to test?

## Answer Notes

1. Identify a concrete source of variation or coupling and show how the pattern reduces the cost of change. If the extra interfaces and indirection have no current benefit, keep the simpler design.

2. Define a payment interface in the application's terms and implement one adapter per provider. Select and assemble the provider at a composition or configuration boundary so checkout logic does not depend on provider-specific APIs.

3. Wrap the adapter with a decorator that implements the same interface and delegates calls while recording metrics or applying a retry policy. Preserve the contract; retries need explicit transient-failure and idempotency rules.

4. Singleton controls access to one instance within a defined scope. It does not automatically make mutable state thread-safe or share an instance across processes. Constructing a normal object once and injecting it exposes dependencies and lets tests supply independent instances or substitutes.

## Related Guides

- [Object-Oriented Programming](./object-oriented-programming.md)
- [SOLID Principles](./solid-principles.md)
- [Domain-Driven Design](./domain-driven-design.md)
- [Testing](../quality-engineering/testing.md)
- [Java Concurrency](../programming/languages/java/java-concurrency.md) — shared identity does not make compound operations atomic.
- [Spring](../programming/frameworks/spring.md) — container-managed lifetimes and constructor injection.

Return to [Software Design](./README.md).
