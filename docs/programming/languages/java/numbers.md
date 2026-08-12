# Java Numbers and Precision

Choosing a numeric type requires understanding its range, representation, overflow behaviour, precision, and domain rules. “Decimal-looking” source code does not imply decimal storage.

## Integer Primitive Types

| Type | Range |
| :--- | :--- |
| `byte` | -128 to 127 |
| `short` | -32,768 to 32,767 |
| `int` | -2³¹ to 2³¹ - 1 |
| `long` | -2⁶³ to 2⁶³ - 1 |

Integer literals are `int` by default when representable. Use `L` for a `long` literal:

```java
long population = 8_000_000_000L;
```

Underscores improve readability without affecting value.

## Integer Arithmetic and Promotion

Arithmetic on `byte`, `short`, and `char` operands normally promotes them to `int`:

```java
byte first = 10;
byte second = 20;
int sum = first + second;
```

Integer division discards the fractional part toward zero:

```java
System.out.println(7 / 2);   // 3
System.out.println(-7 / 2);  // -3
System.out.println(7 / 2.0); // 3.5
```

Convert before division when a floating result is intended.

## Integer Overflow

Primitive integer arithmetic wraps using two's-complement semantics:

```java
int maximum = Integer.MAX_VALUE;
System.out.println(maximum + 1); // -2147483648
```

No exception is thrown by ordinary arithmetic. Use exact methods when overflow is a defect:

```java
int total = Math.addExact(firstCount, secondCount);
long product = Math.multiplyExact(unitPrice, quantity);
int converted = Math.toIntExact(longValue);
```

These methods throw `ArithmeticException` when the result is outside the target range.

## Floating-Point Types

- `float` is a 32-bit IEEE 754 binary floating-point type and needs an `F` suffix for most literals.
- `double` is a 64-bit IEEE 754 binary floating-point type and is the default for floating literals.

Binary floating point represents a finite set of values. Many decimal fractions have no exact finite binary representation:

```java
double result = 0.1 + 0.2;

System.out.println(result);        // 0.30000000000000004
System.out.println(result == 0.3); // false
```

This is representation behaviour, not a Java arithmetic defect.

## Comparing Floating-Point Results

Use a tolerance derived from the domain and magnitude:

```java
static boolean approximatelyEqual(
        double first,
        double second,
        double absoluteTolerance,
        double relativeTolerance
) {
    double difference = Math.abs(first - second);
    double scale = Math.max(Math.abs(first), Math.abs(second));
    return difference <= Math.max(
            absoluteTolerance,
            relativeTolerance * scale
    );
}
```

A universal epsilon such as `0.000001` is not correct for every scale. Also define intended behaviour for NaN and infinity.

Testing libraries often provide delta- or tolerance-aware assertions; prefer those over custom comparisons where suitable.

## NaN, Infinity, and Signed Zero

Floating-point operations can produce special values:

```java
double positiveInfinity = 1.0 / 0.0;
double notANumber = 0.0 / 0.0;

System.out.println(Double.isInfinite(positiveInfinity)); // true
System.out.println(Double.isNaN(notANumber));            // true
System.out.println(notANumber == notANumber);             // false
```

Use `Double.isNaN` rather than equality to detect NaN.

Positive and negative zero compare equal with `==` but can behave differently in operations:

```java
System.out.println(0.0 == -0.0); // true
System.out.println(1.0 / 0.0);   // Infinity
System.out.println(1.0 / -0.0);  // -Infinity
```

## BigInteger

`BigInteger` represents immutable arbitrary-precision integers:

```java
BigInteger large = new BigInteger("999999999999999999999999");
BigInteger result = large.multiply(BigInteger.TWO);
```

Operations return new values. Arbitrary precision avoids primitive overflow but does not mean unlimited memory or execution time.

## BigDecimal

`BigDecimal` represents immutable arbitrary-precision signed decimal values using an unscaled integer and a scale.

It is suitable for decimal business rules such as prices and tax when scale and rounding are explicit. It does not make every operation automatically exact: non-terminating division requires a rounding policy, and a finite `MathContext` can round results.

### Construct Values Safely

```java
BigDecimal fromText = new BigDecimal("0.1");
BigDecimal fromLong = BigDecimal.valueOf(10);
BigDecimal fromDoubleText = BigDecimal.valueOf(0.1);

BigDecimal surprising = new BigDecimal(0.1);
System.out.println(surprising);
```

`new BigDecimal(0.1)` preserves the exact binary `double` value, which is not decimal 0.1. Prefer a decimal string when the intended decimal is known, or `BigDecimal.valueOf(double)` when converting a pre-existing `double` through its canonical string form.

### Immutability

```java
BigDecimal total = new BigDecimal("10.00");
total.add(new BigDecimal("2.50"));
System.out.println(total); // 10.00

total = total.add(new BigDecimal("2.50"));
System.out.println(total); // 12.50
```

Ignoring the returned value is a common defect.

### Scale, Equality, and Ordering

```java
BigDecimal first = new BigDecimal("1.0");
BigDecimal second = new BigDecimal("1.00");

System.out.println(first.equals(second));    // false
System.out.println(first.compareTo(second)); // 0
```

`equals` considers value and scale. `compareTo` compares numerical value. This inconsistency matters in maps and sets: `HashSet` and `TreeSet` can treat these values differently.

Define whether scale is part of domain equality instead of reflexively using one comparison everywhere.

### Division and Rounding

```java
BigDecimal amount = new BigDecimal("10");
BigDecimal divisor = new BigDecimal("3");

BigDecimal rounded = amount.divide(
        divisor,
        2,
        RoundingMode.HALF_EVEN
);
```

`amount.divide(divisor)` throws `ArithmeticException` when the exact decimal expansion does not terminate.

Select rounding from domain rules:

- `HALF_EVEN` is common for repeated statistical or financial rounding;
- `HALF_UP` matches some familiar commercial rules;
- `UNNECESSARY` asserts that no rounding should be required and throws otherwise.

Do not assume one mode applies to every currency, jurisdiction, tax rule, or calculation stage.

### Money

A robust money model normally includes:

- an amount;
- a currency;
- permitted scale;
- rounding rules;
- allocation rules for splitting values.

`BigDecimal` supplies arithmetic but not the full domain model. Avoid using `double` for exact monetary equality or storing all currencies with an assumed two-decimal scale.

## Parsing and Formatting

Parsing machine interchange and formatting for users are different concerns.

- Use stable locale-independent formats for protocols and persistence.
- Use `NumberFormat` for locale-sensitive display and input.
- Do not parse formatted currency by stripping symbols manually.
- Validate scale, range, NaN, and infinity at trust boundaries.

## Common Mistakes

- Using `int` multiplication before assigning the overflowed result to `long`.
- Assuming casts validate range.
- Comparing calculated `double` values with exact equality.
- Using one arbitrary epsilon for every magnitude.
- Constructing `BigDecimal` from a decimal `double` literal.
- Ignoring the immutable result of a `BigDecimal` operation.
- Forgetting that `equals` includes scale.
- Dividing without an explicit rounding policy.
- Treating `BigDecimal` as a complete money type.

## Interview Checklist

You should be able to explain:

- integer promotion, division, overflow, and exact arithmetic methods;
- why 0.1 cannot be represented exactly as a finite binary floating-point value;
- absolute and relative tolerance;
- NaN, infinity, and signed zero;
- `BigInteger` versus primitive integers;
- `BigDecimal` construction, scale, equality, division, and rounding;
- why numeric choices must follow domain rules.

## Further Reading

- [Java Language Specification: Primitive Types and Values](https://docs.oracle.com/javase/specs/jls/se25/html/jls-4.html#jls-4.2)
- [`BigDecimal` API](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/math/BigDecimal.html)
- [`BigInteger` API](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/math/BigInteger.html)
- [`Math` API](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/lang/Math.html)
