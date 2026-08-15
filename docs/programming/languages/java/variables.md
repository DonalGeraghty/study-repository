# Java Variables and Data Types

Java is statically and strongly typed. A variable has a compile-time type that determines which values it can hold and which operations are available.

## Primitive Types

Java has eight primitive types:

| Type | Meaning | Size or value set | Example |
| :--- | :--- | :--- | :--- |
| `byte` | Signed integer | 8-bit | `byte level = 100;` |
| `short` | Signed integer | 16-bit | `short year = 2026;` |
| `int` | Signed integer | 32-bit | `int count = 42;` |
| `long` | Signed integer | 64-bit | `long total = 3_000_000_000L;` |
| `float` | IEEE 754 binary floating point | 32-bit | `float ratio = 0.5F;` |
| `double` | IEEE 754 binary floating point | 64-bit | `double average = 12.5;` |
| `char` | UTF-16 code unit | 16-bit unsigned | `char initial = 'A';` |
| `boolean` | `true` or `false` | JVM representation is not a language-level storage guarantee | `boolean ready = true;` |

`char` represents one UTF-16 code unit, not necessarily one complete Unicode character. Some characters require a surrogate pair.

## Reference Types

Class, interface, array, enum, and record types are reference types.

```java
String name = "Ava";
int[] scores = {90, 95, 88};
List<String> roles = new ArrayList<>();
```

A reference value identifies an object or is `null`. Java does not expose the value as a raw memory address, and the garbage collector may move objects without changing program semantics.

## Value Versus Identity

```java
String first = new String("admin");
String second = new String("admin");

System.out.println(first == second);      // false: different objects
System.out.println(first.equals(second)); // true: equal content
```

- Use `==` for primitive-value comparison.
- Use `==` when object identity is intentionally the question.
- Use `equals` for logical object equality.
- Use `Objects.equals(a, b)` for a null-safe equality check.

Enums are canonical instances, so identity comparison with `==` is the normal choice for enum constants.

## Null

`null` is the null reference and can be assigned only to reference types.

```java
String message = null;
// int count = null; // Does not compile
```

Dereferencing `null` throws `NullPointerException`:

```java
int length = message.length();
```

Model required values as required and validate them at clear boundaries. Do not use `Optional` for every field or parameter merely to avoid thinking about nullability.

## Variable Categories and Scope

### Local Variables

Declared within a method, constructor, or block. They must be definitely assigned before use.

```java
int result;
// System.out.println(result); // Compile-time error
result = 10;
```

### Parameters

Method and constructor parameters are local variables initialised from argument values.

### Instance Fields

Each object has its own instance-field state:

```java
final class Player {
    private String name;
    private int score;
}
```

Fields receive default values such as `0`, `false`, or `null` if no explicit initialiser runs. Relying on defaults can obscure intent, so initialise important state explicitly.

### Static Fields

A static field is associated with the class rather than one particular instance:

```java
final class Player {
    private static int playerCount;
    private final String name;

    Player(String name) {
        this.name = Objects.requireNonNull(name);
        playerCount++;
    }
}
```

Static mutable state is shared and creates lifecycle, testing, and concurrency concerns. The expression `playerCount++` is not atomic, so the example is unsafe when constructors run concurrently.

Do not describe static field values as living in Metaspace. HotSpot Metaspace stores class metadata; exact field storage is a JVM implementation detail.

## Default Values

| Type | Field or array-element default |
| :--- | :--- |
| Integer primitives | `0` |
| Floating primitives | positive `0.0` |
| `char` | `'\\u0000'` |
| `boolean` | `false` |
| References | `null` |

Local variables have no automatic default available to source code; they must be definitely assigned before use.

## final

`final` prevents reassignment of a variable after initialisation:

```java
final List<String> names = new ArrayList<>();
names.add("Ava");       // Allowed: object mutates
// names = new ArrayList<>(); // Not allowed: variable reassignment
```

A final reference does not make its object immutable. Immutability requires that observable state cannot change after construction.

Use `static final` for class constants:

```java
private static final int MAX_RETRIES = 3;
```

## var and Type Inference

`var` allows the compiler to infer the type of an initialised local variable:

```java
var usersById = new HashMap<String, User>();
```

It is not dynamic typing, cannot be used without an initialiser, and does not change the inferred type later. Use it when the type remains clear from the expression; retain an explicit type when it communicates an important abstraction.

```java
List<User> users = new ArrayList<>(); // Interface is intentional
var count = users.size();             // int is obvious
```

## Primitive Conversion

### Widening

Some conversions occur implicitly:

```java
int count = 42;
long expanded = count;
double measured = expanded;
```

Conversion from a large integer to floating point can lose precision even when it is a permitted widening conversion.

### Narrowing

Narrowing requires an explicit cast and can lose information:

```java
long large = 3_000_000_000L;
int narrowed = (int) large; // Overflowed result
```

A cast tells the compiler the conversion is intentional; it does not make the value safe.

## Wrapper Types and Autoboxing

Each primitive has a wrapper class such as `Integer`, `Long`, `Double`, or `Boolean`. Collections and generics use reference types:

```java
List<Integer> numbers = List.of(1, 2, 3);
```

Autoboxing converts between `int` and `Integer` where allowed. It can introduce:

- allocation or identity surprises;
- `NullPointerException` during unboxing;
- hidden work in hot loops;
- equality mistakes when `==` is used.

```java
Integer boxed = null;
// int value = boxed; // NullPointerException during unboxing

Integer a = 1_000;
Integer b = 1_000;
System.out.println(a == b);      // Do not rely on wrapper identity
System.out.println(a.equals(b)); // true
```

## Pass-by-Value

Java always copies the argument value into the parameter.

```java
static void addRole(List<String> roles) {
    roles.add("ADMIN");
}

static void replaceList(List<String> roles) {
    roles = new ArrayList<>();
}
```

`addRole` mutates the shared object reached through the copied reference. `replaceList` changes only its local parameter variable.

## Arrays

Arrays are fixed-length objects with a reified component type:

```java
String[] names = new String[3];
names[0] = "Ava";
System.out.println(names.length);
```

Access outside `0` through `length - 1` throws `ArrayIndexOutOfBoundsException`. Arrays are covariant, which can defer an unsafe assignment to runtime:

```java
Object[] values = new String[1];
// values[0] = 42; // ArrayStoreException
```

Generic collections are usually safer and more expressive for variable-size application data.

## Common Mistakes

- Assuming references contain a stable, visible memory address.
- Comparing objects with `==` when logical equality is intended.
- Believing `final` makes a mutable object immutable.
- Relying on wrapper identity or forgetting null unboxing.
- Casting without validating numeric range.
- Using static mutable fields as hidden global state.
- Treating `var` as dynamic typing.
- Assuming one `char` always represents one user-perceived character.

## Interview Checklist

You should be able to explain:

- all eight primitive types;
- primitive values versus reference values;
- identity versus logical equality;
- field defaults versus local definite assignment;
- scope, lifetime, `final`, and `static`;
- widening, narrowing, boxing, and unboxing;
- why Java is pass-by-value;
- why Java memory cannot be reduced to “primitives on stack, objects on heap.”

## Further Reading

- [Java Language Specification: Types, Values, and Variables](https://docs.oracle.com/javase/specs/jls/se25/html/jls-4.html)
- [Java Language Specification: Conversions and Contexts](https://docs.oracle.com/javase/specs/jls/se25/html/jls-5.html)
- [Java Language Specification: Arrays](https://docs.oracle.com/javase/specs/jls/se25/html/jls-10.html)

Return to the [Java Study Guide](./README.md).
