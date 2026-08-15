# Java Math and Randomness

The `java.lang.Math` class provides static numeric operations and constants. It is imported automatically with `java.lang`.

## Basic Operations

```java
int absolute = Math.abs(-10);
int larger = Math.max(20, 50);
int smaller = Math.min(20, 50);
```

### The Minimum-Value Edge Case

The positive counterpart of `Integer.MIN_VALUE` cannot fit in an `int`:

```java
System.out.println(Math.abs(Integer.MIN_VALUE)); // Still negative
```

Use `Math.absExact` when this overflow must be detected:

```java
int value = Math.absExact(Integer.MIN_VALUE); // ArithmeticException
```

## Exact Integer Arithmetic

Ordinary integer arithmetic wraps on overflow. `Math` provides checked alternatives:

```java
int sum = Math.addExact(first, second);
int difference = Math.subtractExact(first, second);
long product = Math.multiplyExact(quantity, unitValue);
int incremented = Math.incrementExact(counter);
int converted = Math.toIntExact(longValue);
```

These methods throw `ArithmeticException` instead of returning a wrapped value.

## Rounding

```java
System.out.println(Math.round(3.5));  // 4
System.out.println(Math.ceil(3.1));   // 4.0
System.out.println(Math.floor(3.9));  // 3.0
System.out.println((int) -3.9);       // -3: truncates toward zero
System.out.println(Math.floor(-3.9)); // -4.0
```

- `round(float)` returns `int`; `round(double)` returns `long`.
- `ceil` returns the smallest mathematical integer not less than the input.
- `floor` returns the largest mathematical integer not greater than the input.
- A cast truncates toward zero and is not the same as floor for negative values.

For decimal business rules, use `BigDecimal` with an explicit `RoundingMode`.

## Exponents, Roots, and Logarithms

```java
double power = Math.pow(2.0, 3.0); // 8.0
double squareRoot = Math.sqrt(16.0);
double cubeRoot = Math.cbrt(27.0);
double naturalLog = Math.log(Math.E);
double baseTenLog = Math.log10(1_000.0);
double hypotenuse = Math.hypot(3.0, 4.0);
```

Floating-point results can be rounded or special:

```java
System.out.println(Math.sqrt(-1.0)); // NaN
System.out.println(Math.log(0.0));   // -Infinity
```

Validate the mathematical domain when special values are not acceptable.

## Trigonometry

Trigonometric methods use radians:

```java
double radians = Math.toRadians(90.0);
double sine = Math.sin(radians);
double degrees = Math.toDegrees(Math.PI);
```

Floating-point approximation means `Math.sin(Math.PI)` is close to zero, not guaranteed to equal exactly zero.

## Useful Constants

```java
double circleRatio = Math.PI;
double naturalBase = Math.E;
```

Use domain-specific named constants for business meaning rather than embedding unexplained numeric literals.

## Clamp

Modern Java provides `Math.clamp` overloads:

```java
int pageSize = Math.clamp(requestedSize, 1, 100);
```

Check the JDK baseline of the project before using newer APIs.

## Random Number Generators

Choose a generator from the purpose.

### General Application Randomness

The `RandomGenerator` interface provides a common API for pseudorandom generators:

```java
RandomGenerator random = RandomGenerator.getDefault();
int dieRoll = random.nextInt(1, 7);
```

The origin is inclusive and the bound is exclusive.

### Reproducible Tests

Use a known algorithm and seed when reproducibility matters:

```java
RandomGeneratorFactory<RandomGenerator> factory =
        RandomGeneratorFactory.of("L64X128MixRandom");

RandomGenerator random = factory.create(12345L);
```

Record the seed when generating test data so failures can be reproduced. Do not assert exact sequences unless the selected algorithm and compatibility contract make that intentional.

### Concurrent Code

`ThreadLocalRandom` avoids sharing one mutable generator between worker threads:

```java
int value = ThreadLocalRandom.current().nextInt(1, 11);
```

For structured parallel generation, inspect the capabilities of splittable or jumpable generators in `java.util.random`.

### Security-Sensitive Randomness

Use `SecureRandom` for tokens, reset codes, keys, nonces, and other security-sensitive values:

```java
SecureRandom secureRandom = new SecureRandom();
byte[] token = new byte[32];
secureRandom.nextBytes(token);
```

`Math.random`, `Random`, and ordinary `RandomGenerator` implementations are not substitutes for a cryptographically strong generator.

## Math.random

`Math.random()` returns a pseudorandom `double` greater than or equal to 0.0 and less than 1.0:

```java
int value = (int) (Math.random() * 10) + 1;
```

This works for simple non-security examples, but `RandomGenerator.nextInt(origin, bound)` communicates the range more clearly and avoids manual scaling.

## Math Versus StrictMath

`StrictMath` provides reproducible results based on defined algorithms for many transcendental functions. Modern `Math` methods have strong accuracy requirements and may use platform-specific acceleration.

Choose `StrictMath` only when its reproducibility contract is required; it is not a blanket “more correct” replacement.

## Testing Numeric Code

Test:

- zero, one, minimum, maximum, and values around boundaries;
- negative inputs and sign changes;
- overflow and exact-method exceptions;
- NaN, infinity, and signed zero where floating point is accepted;
- tolerance based on domain scale;
- deterministic seeds for generated tests;
- distribution properties separately from one fixed output;
- explicit rounding rules.

Property-based testing is useful for invariants such as commutativity, bounds, reversibility within tolerance, or result conservation.

## Common Mistakes

- Assuming `Math.abs` always returns a non-negative primitive.
- Confusing floor with truncation for negative values.
- Using `Math.pow` and casting when exact integer arithmetic is required.
- Using pseudorandom generators for credentials.
- Writing flaky tests with an unrecorded random seed.
- Assuming a random distribution from a few observed samples.
- Comparing floating-point results without a domain-aware tolerance.

## Interview Checklist

You should be able to explain:

- wrapping arithmetic versus `Math.*Exact`;
- rounding and truncation for negative values;
- special floating-point results;
- inclusive origin and exclusive bound conventions;
- reproducible pseudorandomness versus security randomness;
- when `BigDecimal` is more appropriate than `Math` operations.

## Further Reading

- [`Math` API](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/lang/Math.html)
- [`StrictMath` API](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/lang/StrictMath.html)
- [`RandomGenerator` API](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/util/random/RandomGenerator.html)
- [`SecureRandom` API](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/security/SecureRandom.html)

Return to the [Java Study Guide](./README.md).
