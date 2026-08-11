# Java Strings and Text

`String` is an immutable sequence of UTF-16 code units. Text handling requires attention to equality, nullability, Unicode, locale, performance, and security—not only concatenation.

## Immutability

String operations return new values:

```java
String original = "  Java  ";
String trimmed = original.strip();

System.out.println(original); // "  Java  "
System.out.println(trimmed);  // "Java"
```

Immutability makes strings safe to share and useful as map keys, but repeated transformations can allocate many intermediate objects.

## Literals and the String Pool

String literals with the same content are normally interned:

```java
String first = "test";
String second = "test";
String third = new String("test");

System.out.println(first == second);      // true in this example
System.out.println(first == third);       // false
System.out.println(first.equals(third));  // true
```

Do not use pool behaviour as a reason to compare strings with `==`. Use `equals` for content.

Calling `intern` returns a canonical pooled representation, but manual interning is rarely needed in normal application code and can create retention or performance problems when misused.

## Equality and Ordering

```java
String actual = "Admin";

System.out.println(actual.equals("Admin"));           // true
System.out.println(actual.equalsIgnoreCase("admin")); // true
System.out.println(actual.compareTo("User") < 0);     // lexicographic order
```

Use `Objects.equals(first, second)` for null-safe equality:

```java
boolean same = Objects.equals(possiblyNull, expected);
```

`equalsIgnoreCase` is not a complete solution for every language-sensitive comparison. Use `Collator` or explicitly normalised domain rules when locale-aware human text ordering matters.

## Empty, Blank, and Null

- `null` means no `String` reference.
- `""` is an empty string with length zero.
- `isEmpty()` checks length zero.
- `isBlank()` also treats Unicode whitespace as blank according to its contract.
- `strip()` is Unicode-aware; `trim()` uses an older, narrower definition.

```java
String input = "   ";

System.out.println(input.isEmpty()); // false
System.out.println(input.isBlank()); // true
```

Define whether missing, empty, and blank values are distinct in the domain.

## Common Operations

```java
String value = "Java Testing";

int length = value.length();
boolean contains = value.contains("Test");
boolean starts = value.startsWith("Java");
String lower = value.toLowerCase(Locale.ROOT);
String replaced = value.replace("Testing", "Engineering");
String part = value.substring(0, 4);
String[] words = value.split("\\\\s+");
```

Use `Locale.ROOT` for locale-independent identifiers, protocols, and normalisation. Use the user's locale for user-facing text.

Avoid calling `toLowerCase()` without a locale in security or identifier logic because the default locale can change results.

## Concatenation

The compiler can optimise simple concatenation expressions:

```java
String message = "User " + userId + " logged in";
```

Repeatedly extending a string in a loop generally performs unnecessary allocation and copying:

```java
StringBuilder builder = new StringBuilder();

for (int index = 0; index < values.size(); index++) {
    if (index > 0) {
        builder.append(", ");
    }
    builder.append(values.get(index));
}

String result = builder.toString();
```

For joining known elements, prefer a higher-level API:

```java
String result = String.join(", ", values);
```

Measure before optimising ordinary one-off concatenation.

## StringBuilder

`StringBuilder` is mutable and intended for single-threaded text construction:

```java
StringBuilder builder = new StringBuilder("Order");
builder.append(" #").append(orderId);
builder.insert(0, "Processed ");
String message = builder.toString();
```

Useful methods include `append`, `insert`, `replace`, `delete`, `reverse`, `setLength`, and `toString`.

Capacity grows separately from length. Supplying an estimated initial capacity can reduce resizing for large predictable output:

```java
StringBuilder builder = new StringBuilder(1_024);
```

## StringBuffer and Concurrency

`StringBuffer` synchronises individual operations. It is usually slower than `StringBuilder` and does not automatically make a multi-step text-building algorithm correct under concurrent access.

Prefer thread confinement: give each task its own `StringBuilder` and combine completed results. Use `StringBuffer` only when its specific synchronisation contract matches the design.

## Text Blocks

Text blocks express multi-line source text:

```java
String json = """
        {
          "status": "ready",
          "count": 3
        }
        """;
```

Incidental indentation is removed according to text-block rules. Verify exact whitespace when the content is a protocol fixture or snapshot.

## Formatting

```java
String message = "Order %s contains %d items".formatted(orderId, itemCount);
```

Formatted conversion follows specific format rules. Specify a locale when numeric or date output must be predictable:

```java
String amount = String.format(Locale.ROOT, "%.2f", value);
```

For exact monetary values, format a suitable decimal or money type rather than converting through `double`.

## Unicode

`String.length()` returns UTF-16 code-unit count, not Unicode code-point count or user-perceived character count.

```java
String symbol = "😀";

System.out.println(symbol.length()); // 2 UTF-16 code units
System.out.println(symbol.codePointCount(0, symbol.length())); // 1 code point
```

Use code-point APIs when processing Unicode characters:

```java
symbol.codePoints()
        .forEach(codePoint -> System.out.println(
                Character.toChars(codePoint)
        ));
```

Even one code point is not always one displayed grapheme. Emoji sequences, combining marks, and scripts can require specialised Unicode-aware libraries for user-perceived character operations.

### Normalisation

Visually equivalent text can have different code-point sequences:

```java
String normalised = Normalizer.normalize(
        input,
        Normalizer.Form.NFC
);
```

Apply normalisation only when the domain defines it. User identifiers, filenames, cryptographic inputs, and natural-language text may have different requirements.

## Regular Expressions

`String.matches` tests the entire input and recompiles the pattern on each call:

```java
boolean valid = input.matches("[A-Z]{3}-\\\\d{4}");
```

Compile a reused pattern:

```java
private static final Pattern REFERENCE =
        Pattern.compile("[A-Z]{3}-\\\\d{4}");

boolean valid = REFERENCE.matcher(input).matches();
```

Review regexes for catastrophic backtracking, unexpected Unicode behaviour, and escaping at both Java-string and regex levels.

## Sensitive Text

`String` cannot be cleared because it is immutable. APIs sometimes accept `char[]` or `byte[]` for secrets so the caller can overwrite the buffer:

```java
char[] password = readPassword();
try {
    authenticate(password);
} finally {
    Arrays.fill(password, '\\0');
}
```

This reduces one retention risk but does not guarantee that no copies exist in input libraries, logs, crash dumps, or runtime internals. Never log credentials or tokens.

## Common Mistakes

- Comparing string content with `==`.
- Treating null, empty, and blank as interchangeable without a domain decision.
- Repeated `+=` concatenation in a large loop.
- Assuming `StringBuffer` makes an entire concurrent algorithm safe.
- Using the default locale for protocol identifiers.
- Treating `length()` as a count of displayed characters.
- Splitting or truncating inside a surrogate pair.
- Recompiling a complex regex repeatedly.
- Keeping credentials in strings or logging sensitive text.

## Interview Checklist

You should be able to explain:

- immutability and the string pool;
- identity versus content equality;
- empty, blank, and null values;
- compiler concatenation versus `StringBuilder`;
- why `StringBuffer` is not a general concurrency solution;
- UTF-16 code units, code points, and grapheme limitations;
- locale and normalisation concerns;
- text blocks, formatting, and regex reuse.

## Further Reading

- [`String` API](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/lang/String.html)
- [`StringBuilder` API](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/lang/StringBuilder.html)
- [`Character` API](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/lang/Character.html)
- [`Normalizer` API](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/text/Normalizer.html)
