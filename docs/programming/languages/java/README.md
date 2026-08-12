# Java Study Guide

This pathway covers the Java language and core library knowledge expected in application development, test automation, code review, and technical interviews. Examples use modern Java while distinguishing language guarantees from common HotSpot JVM implementation details.

## Topics

1. [Memory Management](./memory-management.md)
2. [Variables and Data Types](./variables.md)
3. [Numbers and Precision](./numbers.md)
4. [Math and Randomness](./math.md)
5. [Strings and Text](./strings.md)
6. [Collections Framework](./collections.md)
7. [Exceptions and Resources](./exceptions-and-resources.md)
8. [Generics](./generics.md)
9. [Streams and Lambdas](./streams-and-lambdas.md)
10. [Concurrency](./concurrency.md)
11. [Modern Java](./modern-java.md)

Object-oriented design, SOLID, and design patterns are covered in the [Software Design](../../../software-design/README.md) subject.

## Frameworks

- [Spring](../../frameworks/spring/README.md) — dependency injection, Spring Boot, web APIs, data access, configuration, and testing.

## Language, Runtime, and Platform

These terms are related but distinct:

- **Java language** defines syntax and semantics such as types, expressions, classes, exceptions, and threads.
- **Java Virtual Machine (JVM)** executes bytecode and provides runtime services such as class loading and garbage collection.
- **Java Development Kit (JDK)** supplies the compiler, launcher, standard library, diagnostics, and development tools.
- **Java Runtime Environment (JRE)** historically described the runtime needed to execute Java applications. Modern JDK distributions and custom runtime images have largely replaced the separate consumer JRE model.

Typical flow:

```text
Main.java --javac--> Main.class bytecode --java/JVM--> running program
```

```bash
javac Main.java
java Main
```

Build tools such as Maven and Gradle normally manage compilation, dependencies, tests, packaging, and execution for real projects.

## Core Mental Models

### Java Is Statically Typed

Every variable and expression has a type known at compile time. Runtime objects may have a more specific type through polymorphism.

```java
List<String> names = new ArrayList<>();
```

The variable has compile-time type `List<String>` while the object is an `ArrayList<String>`.

### Java Is Always Pass-by-Value

Method arguments are copied into parameters. For object types, the copied value is a reference; Java does not pass the variable itself by reference.

```java
static void rename(Person person) {
    person.setName("Ava"); // Mutates the referenced object
}

static void replace(Person person) {
    person = new Person("Sam"); // Reassigns only the local parameter
}
```

### Equality Has Multiple Meanings

- `==` compares primitive values or object-reference identity.
- `equals` expresses logical equality when a class implements it appropriately.
- `compareTo` or a `Comparator` expresses ordering.

Classes used in hash-based collections must keep `equals` and `hashCode` consistent.

### Prefer Interfaces at Boundaries

```java
List<String> names = new ArrayList<>();
Map<String, User> usersById = new HashMap<>();
```

Declare the capability clients need and choose a concrete implementation from performance, ordering, concurrency, and null-handling requirements.

## Quick Selection Guide

| Need | Typical choice | Important caveat |
| :--- | :--- | :--- |
| Indexed sequence | `ArrayList` | Middle insertion shifts later elements. |
| Unique values | `HashSet` | Correctness depends on `equals` and `hashCode`. |
| Insertion-ordered unique values | `LinkedHashSet` | Uses more memory than `HashSet`. |
| Sorted values | `TreeSet` | Ordering must be consistent and well-defined. |
| Queue or stack | `ArrayDeque` | Does not permit `null`. |
| Priority retrieval | `PriorityQueue` | Iteration is not sorted order. |
| Key-value lookup | `HashMap` | No iteration-order guarantee. |
| Concurrent key-value access | `ConcurrentHashMap` | Compound operations still need atomic APIs such as `compute`. |
| Exact decimal business arithmetic | `BigDecimal` | Define scale and rounding policy explicitly. |
| Very large integers | `BigInteger` | Operations allocate immutable results. |
| Repeated text construction | `StringBuilder` | Not designed for shared concurrent mutation. |
| Date and time | `java.time` types | Choose `Instant`, local, offset, or zoned types deliberately. |

## Recommended Learning Order

```text
Types and values
-> numbers and text
-> collections and generics
-> exceptions and resources
-> streams and lambdas
-> concurrency
-> modern language features
-> memory and runtime diagnosis
```

Memory management appears first in the numbered repository for historical reasons, but it is easier to understand after variables, references, and object lifecycles.

## Effective Practice

For each topic:

1. Predict a short program's output before running it.
2. Explain which behaviour is guaranteed by Java and which is an implementation detail.
3. Write boundary and failure tests.
4. Inspect compiler errors rather than immediately searching for a finished solution.
5. Compare two reasonable implementations and describe their trade-offs.
6. Use JDK documentation to verify library contracts.

## Readiness Checklist

You should be able to:

- explain primitive values, object references, scope, `final`, and `static`;
- distinguish identity, logical equality, and ordering;
- select numeric types and rounding policies safely;
- reason about immutable strings and Unicode;
- choose collection interfaces and implementations from requirements;
- write and consume generic APIs without raw types;
- model expected and exceptional outcomes clearly;
- use try-with-resources for deterministic cleanup;
- build stream pipelines without hidden side effects;
- identify shared mutable state and use concurrency utilities appropriately;
- use records, sealed types, pattern matching, and `java.time` where they improve the model;
- explain reachability, garbage collection, and common memory-retention problems;
- diagnose a failure using compiler output, exceptions, logs, tests, and JDK tools.

## Official References

- [Java learning resources](https://dev.java/learn/)
- [Java SE API documentation](https://docs.oracle.com/en/java/javase/25/docs/api/)
- [Java Language Specification](https://docs.oracle.com/javase/specs/jls/se25/html/)
- [Java Virtual Machine Specification](https://docs.oracle.com/javase/specs/jvms/se25/html/)
- [JDK 25 project](https://openjdk.org/projects/jdk/25/)
