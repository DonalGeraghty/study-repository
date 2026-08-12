# Java Streams and Lambdas

Lambdas express behaviour as values. Streams describe pipelines that transform or aggregate data. They can make data processing concise, but hidden side effects or inappropriate parallelism can make code harder to reason about.

## Functional Interfaces

A functional interface has one abstract method:

```java
@FunctionalInterface
interface Validator<T> {
    boolean isValid(T value);
}
```

A lambda can implement it:

```java
Validator<String> notBlank = value -> !value.isBlank();
```

Common interfaces in `java.util.function` include:

| Interface | Operation |
| :--- | :--- |
| `Predicate<T>` | `T -> boolean` |
| `Function<T, R>` | `T -> R` |
| `Consumer<T>` | accepts `T` and returns no value |
| `Supplier<T>` | supplies `T` |
| `UnaryOperator<T>` | `T -> T` |
| `BinaryOperator<T>` | combines two `T` values into `T` |

Primitive specialisations such as `IntPredicate` and `IntFunction` avoid boxing for primitive-heavy paths.

## Lambda Syntax

```java
Predicate<String> notEmpty = value -> !value.isEmpty();
Function<String, Integer> length = String::length;
Consumer<String> print = System.out::println;
Supplier<List<String>> createList = ArrayList::new;
```

Method references are useful when they remain clearer than an equivalent lambda.

## Captured Variables

Lambdas can capture local variables only when they are final or effectively final:

```java
int minimumLength = 3;

Predicate<String> longEnough =
        value -> value.length() >= minimumLength;
```

The restriction avoids one category of mutable local capture. It does not make referenced objects immutable:

```java
List<String> captured = new ArrayList<>();
Runnable task = () -> captured.add("value");
```

Mutating captured shared state can create races and makes stream pipelines difficult to reason about.

## Stream Fundamentals

A `Stream` is not a data structure. It represents a single-use computation over a source.

```java
List<String> result = names.stream()
        .filter(name -> !name.isBlank())
        .map(String::strip)
        .map(String::toUpperCase)
        .sorted()
        .toList();
```

A pipeline has:

1. a source;
2. zero or more intermediate operations;
3. a terminal operation.

Intermediate operations are lazy. Work normally begins when a terminal operation is invoked.

## Intermediate Operations

### filter

Keeps elements matching a predicate:

```java
Stream<Order> paid = orders.stream()
        .filter(order -> order.status() == Status.PAID);
```

### map

Transforms each element:

```java
Stream<String> ids = orders.stream()
        .map(Order::id);
```

### flatMap

Transforms each element into a stream and flattens the result:

```java
List<Item> items = orders.stream()
        .flatMap(order -> order.items().stream())
        .toList();
```

### distinct, sorted, limit, and skip

```java
List<String> firstNames = names.stream()
        .distinct()
        .sorted()
        .limit(10)
        .toList();
```

`distinct` depends on equality. `sorted` depends on natural ordering or a comparator. Stateful operations can require buffering and may be expensive for large or parallel streams.

## Terminal Operations

### Match and Find

```java
boolean anyFailed = results.stream().anyMatch(Result::failed);
boolean allComplete = results.stream().allMatch(Result::complete);
Optional<Order> first = orders.stream().findFirst();
```

Short-circuiting operations may avoid processing every element.

### Collect

```java
Map<Status, List<Order>> byStatus = orders.stream()
        .collect(Collectors.groupingBy(Order::status));
```

`Stream.toList()` returns an unmodifiable list. The exact mutability and implementation returned by other collectors follows each collector's contract; do not assume `Collectors.toList()` returns an `ArrayList`.

### Reduce

```java
int total = values.stream()
        .reduce(0, Integer::sum);
```

The identity and accumulator must follow the reduction contract, especially for parallel execution. Prefer specialised operations such as `sum`, `min`, or collectors when they express intent more clearly.

## Primitive Streams

`IntStream`, `LongStream`, and `DoubleStream` avoid boxing and provide numeric operations:

```java
int totalLength = names.stream()
        .mapToInt(String::length)
        .sum();

IntSummaryStatistics statistics = values.stream()
        .mapToInt(Integer::intValue)
        .summaryStatistics();
```

## Optional

`Optional<T>` models a result that may be absent:

```java
String displayName = users.stream()
        .filter(user -> user.id().equals(targetId))
        .findFirst()
        .map(User::displayName)
        .orElse("Unknown");
```

Useful methods include `map`, `flatMap`, `filter`, `orElseGet`, `orElseThrow`, and `ifPresent`.

`orElse` evaluates its argument eagerly:

```java
User user = cached.orElseGet(this::loadUser);
```

Prefer `orElseGet` when the fallback is expensive or has side effects.

`Optional` is most useful as a return type. Avoid using it indiscriminately for fields, parameters, collection elements, or serialised contracts unless the framework and design support it deliberately.

## Side Effects

Prefer non-interfering, stateless operations:

```java
List<String> normalised = names.stream()
        .map(String::strip)
        .filter(name -> !name.isEmpty())
        .toList();
```

Avoid:

```java
List<String> output = new ArrayList<>();
names.parallelStream()
        .filter(name -> !name.isBlank())
        .forEach(output::add);
```

This mutates shared non-thread-safe state and can corrupt the result. Collect through the stream instead.

`peek` is mainly for observing a pipeline during diagnosis. Business logic hidden in `peek` can disappear when pipeline execution changes or no terminal operation runs.

## Encounter Order

Some sources have a defined encounter order, such as a `List`. Others may not.

- `forEach` does not promise encounter order in a parallel stream.
- `forEachOrdered` preserves encounter order but can reduce parallel benefit.
- `findFirst` respects encounter order; `findAny` permits more freedom.

Do not rely on incidental `HashSet` or `HashMap` iteration order.

## Single Use

A stream cannot be reused after a terminal operation:

```java
Stream<String> stream = names.stream();
long count = stream.count();
// stream.findFirst(); // IllegalStateException
```

Create a new stream from the source or use a `Supplier<Stream<T>>` when a pipeline must be recreated.

## Parallel Streams

`parallelStream` uses parallel execution, commonly through the shared common fork/join pool.

Parallelism can help when:

- the dataset is sufficiently large;
- work per element is CPU-intensive;
- operations are associative, stateless, and independent;
- the source splits efficiently;
- ordering is not an expensive constraint;
- measurements demonstrate a benefit.

Avoid assuming parallel streams improve blocking I/O, small collections, latency-sensitive shared runtimes, or pipelines with synchronisation and side effects. Shared-pool contention can affect unrelated work.

Use explicit concurrency designs when ownership, cancellation, isolation, backpressure, or executor selection matters.

## Exceptions in Lambdas

Standard functional interfaces do not declare arbitrary checked exceptions. Avoid wrapping every exception blindly in `RuntimeException`.

Options include:

- handle the exception at the operation that can recover;
- create a domain-specific adapter;
- use an explicit loop when it communicates error handling more clearly;
- return a result type containing success or failure.

Streams are not automatically better than loops.

## When a Loop Is Clearer

Prefer a loop when the operation needs:

- complex branching or early exits;
- checked exception handling;
- mutation with explicit ordering;
- several related accumulators;
- detailed step-by-step debugging;
- performance control demonstrated by profiling.

Readable code is the goal, not maximum stream usage.

## Testing Stream Code

Test:

- empty, one-element, and duplicate sources;
- ordering and unordered sources;
- null policy;
- mapping and filtering boundaries;
- collector duplicate-key behaviour;
- sequential and parallel equivalence when parallel is supported;
- immutability of returned collections where contractual;
- exception and short-circuit behaviour.

## Common Mistakes

- Reusing a consumed stream.
- Mutating shared state from pipeline operations.
- Assuming laziness means results are cached.
- Using `peek` for required behaviour.
- Ignoring encounter-order semantics.
- Calling `parallelStream` without measurement.
- Using `orElse` for an expensive fallback.
- Treating `Optional` as a universal null replacement.
- Forcing complicated logic into a pipeline when a loop is clearer.

## Interview Checklist

You should be able to explain:

- functional interfaces and lambda capture;
- lazy intermediate and eager terminal operations;
- `map` versus `flatMap`;
- collectors and reductions;
- primitive streams;
- Optional usage and eager fallback;
- side effects and non-interference;
- encounter order;
- when parallel streams help or harm.

## Further Reading

- [`Stream` API](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/util/stream/Stream.html)
- [`Collectors` API](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/util/stream/Collectors.html)
- [`java.util.function` package](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/util/function/package-summary.html)
- [`Optional` API](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/util/Optional.html)
