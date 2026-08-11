# Object-Oriented Programming

Object-oriented programming (OOP) models a system as objects that collaborate through methods. Its main benefit is not syntax or code reuse; it is keeping related state, behaviour, and rules together behind clear boundaries.

## Objects, Classes, and Responsibilities

A **class** defines structure and behaviour. An **object** is a runtime instance with its own state and, where relevant, identity.

```java
public final class BankAccount {
    private final String accountNumber;
    private long balanceInCents;

    public BankAccount(String accountNumber, long openingBalanceInCents) {
        if (accountNumber == null || accountNumber.isBlank()) {
            throw new IllegalArgumentException("Account number is required");
        }
        if (openingBalanceInCents < 0) {
            throw new IllegalArgumentException("Opening balance cannot be negative");
        }
        this.accountNumber = accountNumber;
        this.balanceInCents = openingBalanceInCents;
    }

    public void withdraw(long amountInCents) {
        if (amountInCents <= 0) {
            throw new IllegalArgumentException("Amount must be positive");
        }
        if (amountInCents > balanceInCents) {
            throw new IllegalStateException("Insufficient funds");
        }
        balanceInCents -= amountInCents;
    }

    public long balanceInCents() {
        return balanceInCents;
    }
}
```

This object owns the rule that its balance cannot become negative. A public setter would weaken that guarantee.

## The Main OOP Ideas

### Encapsulation

Encapsulation hides representation and controls how state changes. It is more than declaring fields `private`: callers should not be able to bypass an object's invariants.

Prefer `order.addItem(product, quantity)` over `order.getItems().add(...)`. The first form gives `Order` a chance to validate quantity, prevent duplicates, or recalculate totals.

### Abstraction

An abstraction exposes what a collaborator can do while hiding how it does it.

```java
public interface PaymentGateway {
    PaymentReceipt charge(Money amount, PaymentMethod method);
}
```

Checkout code can use this capability without knowing the provider's HTTP format, credentials, or retry implementation.

### Polymorphism

Polymorphism lets several implementations honour the same contract.

```java
public interface DiscountPolicy {
    Money discountFor(Order order);
}

public final class NoDiscount implements DiscountPolicy {
    @Override
    public Money discountFor(Order order) {
        return Money.zero(order.currency());
    }
}

public final class PercentageDiscount implements DiscountPolicy {
    private final int percentage;

    public PercentageDiscount(int percentage) {
        if (percentage < 0 || percentage > 100) {
            throw new IllegalArgumentException("Percentage must be between 0 and 100");
        }
        this.percentage = percentage;
    }

    @Override
    public Money discountFor(Order order) {
        return order.subtotal().percentage(percentage);
    }
}
```

The caller invokes the contract without branching on implementation type.

### Inheritance

Inheritance lets a subtype inherit accessible behaviour and state from a parent. Use it when:

- the subtype is genuinely substitutable for the parent;
- the parent contract is stable and clear;
- shared behaviour represents the same concept, not merely a convenient code fragment.

Avoid inheritance when subclasses disable parent methods, inspect their own runtime type, or override most inherited behaviour. Deep hierarchies also spread change across many classes.

### Composition

Composition builds behaviour by giving an object collaborators.

```java
public final class CheckoutService {
    private final DiscountPolicy discountPolicy;
    private final PaymentGateway paymentGateway;

    public CheckoutService(
            DiscountPolicy discountPolicy,
            PaymentGateway paymentGateway) {
        this.discountPolicy = discountPolicy;
        this.paymentGateway = paymentGateway;
    }

    public PaymentReceipt checkout(Order order, PaymentMethod method) {
        Money discount = discountPolicy.discountFor(order);
        return paymentGateway.charge(order.subtotal().minus(discount), method);
    }
}
```

The discount and payment behaviours can vary independently. This is usually easier to test and evolve than a hierarchy such as `DiscountedStripeCheckout` and `RegularPayPalCheckout`.

## Interface or Abstract Class?

| Use an interface when | Use an abstract class when |
| --- | --- |
| Unrelated classes can provide the capability | Subtypes share a genuine base concept |
| Implementations need different class hierarchies | Related subclasses share protected implementation |
| The contract should remain small | Construction or state must be centralised |
| Multiple capabilities may be combined | The inheritance constraint is intentional |

A Java class can implement multiple interfaces but extend only one class. Interface default methods can evolve a contract, but they should not become a substitute for a class with significant shared state.

## Identity and Value

Some objects have a continuing identity even when their attributes change. A customer, order, or support ticket is commonly identified by an ID.

Other objects are defined entirely by their values. Money, date ranges, email addresses, and coordinates are common value objects.

```java
public record EmailAddress(String value) {
    public EmailAddress {
        if (value == null || !value.contains("@")) {
            throw new IllegalArgumentException("Invalid email address");
        }
        value = value.trim().toLowerCase();
    }
}
```

Records provide value-based equality and concise syntax, but their immutability is shallow: a component can still refer to a mutable object. Validate inputs and make defensive copies where necessary.

## Equality in Java

When a class defines logical equality, implement `equals` and `hashCode` consistently:

- equal objects must have equal hash codes;
- equality should be reflexive, symmetric, transitive, and consistent;
- avoid basing an entity's equality on mutable fields;
- prefer immutable value objects as map keys.

Generated implementations help, but the team must still decide what equality means for the concept.

## Cohesion and Coupling

**Cohesion** describes how closely related a class's responsibilities are. **Coupling** describes how strongly it depends on other code.

High cohesion and controlled coupling usually produce smaller public APIs, clearer ownership, easier tests, and less ripple effect from change. Zero coupling is not the goal: collaborating objects must depend on something. Prefer explicit dependencies on narrow, stable contracts.

## Immutability and Mutable State

Immutable objects are easier to reason about and share safely. They are a good default for values and messages.

Mutable objects remain useful for concepts with a lifecycle, but mutation should be performed through meaningful operations, validated, and kept within a clear ownership boundary. Do not leak mutable collections:

```java
public List<OrderLine> lines() {
    return List.copyOf(lines);
}
```

## Tell, Do Not Ask

Repeatedly reading an object's state and deciding its behaviour elsewhere can scatter its rules. Prefer `order.submit()` over reading the status and setting a new status externally.

This is a heuristic, not a ban on queries. Reporting, serialization, and UI rendering legitimately read state.

## Designing for Testability

- Pass dependencies through constructors.
- Avoid hidden global or static mutable state.
- Put time, randomness, files, and networks behind explicit boundaries.
- Test observable behaviour rather than private methods.
- Use real value and domain objects where inexpensive.
- Use fakes or mocks at slow or nondeterministic boundaries.

Testability is usually a consequence of explicit responsibilities, not a reason to expose implementation details.

## Common Mistakes

- Adding getters and setters for every field without protecting rules.
- Treating inheritance mainly as a code-reuse tool.
- Building deep class hierarchies.
- Using `instanceof` chains where a stable polymorphic operation belongs on the contract.
- Creating interfaces for every class without a real boundary or variation.
- Putting unrelated behaviour in utility or manager classes.
- Allowing domain objects to depend directly on frameworks or database APIs.
- Using raw strings and numbers for concepts with important semantics.

## Review Questions

- What responsibility and invariants does this object own?
- Can callers create or mutate it into an invalid state?
- Is the public API expressed in domain language?
- Does a subtype honour every expectation of its parent?
- Would composition make variations more independent?
- Does equality represent identity or value correctly?
- Are dependencies explicit and narrow?
- Can the behaviour be tested without a database or network?

## Practice Exercise

Model a library loan:

1. Identify entities and value objects.
2. Put borrowing and return rules on the object that owns them.
3. Represent notification and persistence as interfaces.
4. Add a second fine-calculation policy through composition.
5. Write tests for successful borrowing and rule violations.

Explain why each class exists and what change it contains.

## Completion Checklist

- [ ] I can explain encapsulation as protection of invariants.
- [ ] I can distinguish abstraction, inheritance, composition, and polymorphism.
- [ ] I can choose between an interface and an abstract class.
- [ ] I understand identity-based and value-based equality.
- [ ] I can recognise low cohesion and excessive coupling.
- [ ] I can design an object around behaviour rather than public data.

## Related Guides

- [SOLID Principles](./solid-principles.md)
- [Design Patterns](./design-patterns.md)
- [Domain-Driven Design](./domain-driven-design.md)
- [Java](../programming/java/README.md)
