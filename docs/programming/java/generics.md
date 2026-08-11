# Java Generics

Generics allow types and methods to express relationships between types while providing compile-time checking and reducing casts.

## Parameterised Types

```java
List<String> names = new ArrayList<>();
names.add("Ava");

String first = names.get(0);
```

`List<String>` states that elements are strings. A raw `List` discards this information and moves failures from compile time toward runtime.

## Generic Classes

```java
final class Box<T> {
    private final T value;

    Box(T value) {
        this.value = value;
    }

    T value() {
        return value;
    }
}

Box<Integer> count = new Box<>(42);
```

`T` is a type parameter. Names such as `T`, `E`, `K`, `V`, and `R` are conventional when their meaning is clear.

## Generic Methods

```java
static <T> T first(List<T> values) {
    if (values.isEmpty()) {
        throw new NoSuchElementException("values is empty");
    }
    return values.get(0);
}
```

The type parameter appears before the return type. Type inference usually removes the need to supply it explicitly.

## Invariance

Generic types are invariant:

```java
List<Integer> integers = List.of(1, 2, 3);
// List<Number> numbers = integers; // Does not compile
```

If it compiled, adding a `Double` through `numbers` would violate the original `List<Integer>`.

Arrays are covariant and therefore can fail at runtime:

```java
Number[] numbers = new Integer[1];
// numbers[0] = 1.5; // ArrayStoreException
```

Generics favour compile-time rejection.

## Wildcards

### Upper-Bounded Wildcards

Read values as a common upper type:

```java
static double sum(List<? extends Number> values) {
    double total = 0.0;
    for (Number value : values) {
        total += value.doubleValue();
    }
    return total;
}
```

The method accepts `List<Integer>`, `List<Double>`, and other number lists. It cannot safely add an arbitrary number because the exact element type is unknown.

### Lower-Bounded Wildcards

Write values of a type:

```java
static void addDefaults(List<? super Integer> target) {
    target.add(0);
    target.add(1);
}
```

The list might be `List<Integer>`, `List<Number>`, or `List<Object>`. Values read from it are known only as `Object`.

### Unbounded Wildcards

`List<?>` means a list of some unknown type:

```java
static int sizeOf(List<?> values) {
    return values.size();
}
```

It is safer and more expressive than raw `List` when element type does not matter.

## PECS

The guideline **Producer Extends, Consumer Super** helps choose wildcard direction:

- a source producing `T` values: `? extends T`;
- a destination consuming `T` values: `? super T`.

```java
static <T> void copy(
        List<? extends T> source,
        List<? super T> destination
) {
    destination.addAll(source);
}
```

Do not apply PECS mechanically when the same structure must both consume and produce a precise type.

## Bounded Type Parameters

```java
static <T extends Comparable<? super T>> T maximum(
        List<? extends T> values
) {
    return values.stream()
            .max(Comparator.naturalOrder())
            .orElseThrow();
}
```

A type parameter can have multiple bounds:

```java
static <T extends Closeable & Serializable> void process(T value) {
    // ...
}
```

The class bound, if present, comes first.

## Generic API Design

Use a type parameter when it expresses a relationship:

```java
static <T> T choose(T first, T second) {
    return ThreadLocalRandom.current().nextBoolean()
            ? first
            : second;
}
```

Use a wildcard when callers do not need to know or name the exact type:

```java
static void printAll(Collection<?> values) {
    values.forEach(System.out::println);
}
```

Avoid adding generic parameters that do not improve safety or communicate a relationship.

## Type Erasure

Java implements most generic type information through erasure. At runtime, `List<String>` and `List<Integer>` have the same raw class:

```java
System.out.println(
        new ArrayList<String>().getClass()
                == new ArrayList<Integer>().getClass()
); // true
```

Consequences include:

- no `new T()`;
- no generic array creation such as `new T[10]`;
- no `instanceof List<String>`;
- no overloading methods whose signatures erase identically;
- static fields cannot depend on a class's type parameter.

Use a `Class<T>` token, factory, or supplier when runtime construction is needed:

```java
final class Factory<T> {
    private final Supplier<? extends T> supplier;

    Factory(Supplier<? extends T> supplier) {
        this.supplier = supplier;
    }

    T create() {
        return supplier.get();
    }
}
```

## Reifiable Types

A reifiable type has enough runtime representation for certain checks. Examples include primitive types, non-generic types, raw types, and unbounded wildcard parameterisations such as `List<?>`.

```java
if (value instanceof List<?> list) {
    System.out.println(list.size());
}
```

The runtime cannot verify `List<String>` because element type arguments are erased.

## Heap Pollution

Heap pollution occurs when a variable of a parameterised type refers to an object that violates its type expectation, often through raw types, unchecked casts, or generic varargs.

```java
List<String> strings = new ArrayList<>();
List raw = strings;
raw.add(42);

// String value = strings.get(0); // ClassCastException
```

Treat unchecked warnings as defects to understand, not noise to suppress broadly.

## Generic Varargs

Varargs use arrays, which can interact unsafely with non-reifiable element types:

```java
@SafeVarargs
static <T> List<T> combine(List<? extends T>... lists) {
    List<T> result = new ArrayList<>();
    for (List<? extends T> list : lists) {
        result.addAll(list);
    }
    return result;
}
```

Use `@SafeVarargs` only when the method truly does not perform potentially unsafe operations on the varargs array. The annotation suppresses warnings; it does not make an unsafe method safe.

## Common Mistakes

- Using raw types.
- Expecting `List<Integer>` to be a subtype of `List<Number>`.
- Writing to `? extends T` or expecting precise reads from `? super T`.
- Using wildcards where a named type relationship is needed.
- Ignoring unchecked warnings.
- Assuming generic type arguments are fully available at runtime.
- Performing unchecked casts without validating the underlying structure.
- Adding `@SafeVarargs` without proving safety.

## Interview Checklist

You should be able to explain:

- generic classes and methods;
- invariance and the difference from array covariance;
- upper, lower, and unbounded wildcards;
- PECS;
- bounded type parameters;
- erasure and its restrictions;
- reifiable types;
- raw types, unchecked warnings, and heap pollution.

## Further Reading

- [Java Language Specification: Type Variables](https://docs.oracle.com/javase/specs/jls/se25/html/jls-4.html#jls-4.4)
- [Java Language Specification: Parameterized Types](https://docs.oracle.com/javase/specs/jls/se25/html/jls-4.html#jls-4.5)
- [Java tutorials: Generics](https://dev.java/learn/generics/)
