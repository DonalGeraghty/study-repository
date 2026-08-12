# Modern Java

Modern Java includes language and library features that improve domain modelling, readability, safety, and concurrency. Use features supported by the project's chosen JDK baseline, and distinguish permanent features from preview features.

## Release Awareness

Java publishes regular feature releases and designates selected releases for long-term support through vendors. “LTS” describes a support offering rather than different language semantics.

Before adopting a feature:

- confirm the source and target release configured by the build;
- verify runtime and deployment support;
- check framework, IDE, static-analysis, and instrumentation compatibility;
- avoid preview features in production unless the project explicitly accepts their lifecycle and launch requirements.

## Records

A record concisely declares a transparent carrier for immutable component references:

```java
record UserId(String value) {
    UserId {
        if (value == null || value.isBlank()) {
            throw new IllegalArgumentException(
                    "User ID must not be blank"
            );
        }
    }
}
```

Java derives accessors, a canonical constructor, `equals`, `hashCode`, and `toString` from the components.

Records are shallowly immutable:

```java
record Team(List<String> members) {
    Team {
        members = List.copyOf(members);
    }
}
```

Without the defensive copy, callers could mutate the list held by the record.

Use records for value-like data carriers. They are not automatically appropriate for mutable entities, ORM proxies, or abstractions whose representation must remain hidden.

## Sealed Types

Sealed classes and interfaces restrict permitted direct subtypes:

```java
sealed interface PaymentResult
        permits Approved, Declined, Failed {
}

record Approved(String authorisationId)
        implements PaymentResult {
}

record Declined(String reason)
        implements PaymentResult {
}

record Failed(Throwable cause)
        implements PaymentResult {
}
```

Permitted subtypes must be `final`, `sealed`, or `non-sealed` as required by the hierarchy rules.

Sealed hierarchies are useful when the domain has a known set of variants and exhaustive handling is valuable.

## Pattern Matching

Pattern matching combines a type test with a binding:

```java
if (value instanceof String text && !text.isBlank()) {
    System.out.println(text.length());
}
```

Pattern switches can express variant handling:

```java
String describe(PaymentResult result) {
    return switch (result) {
        case Approved approved ->
                "Approved: " + approved.authorisationId();
        case Declined declined ->
                "Declined: " + declined.reason();
        case Failed failed ->
                "Failed: " + failed.cause().getMessage();
    };
}
```

An exhaustive sealed hierarchy can remove a default branch and let the compiler identify newly added variants that require handling.

## Switch Expressions

`switch` can produce a value:

```java
int priority = switch (severity) {
    case CRITICAL -> 1;
    case HIGH -> 2;
    case MEDIUM -> 3;
    case LOW -> 4;
};
```

Use `yield` for a block:

```java
int score = switch (status) {
    case ACTIVE -> 10;
    case REVIEW -> {
        audit(status);
        yield 5;
    }
    case CLOSED -> 0;
};
```

Arrow labels avoid accidental fall-through. Traditional switch statements remain useful when fall-through is intentional and clearly documented.

## Text Blocks

Text blocks reduce escaping for multi-line content:

```java
String payload = """
        {
          "name": "Ava",
          "active": true
        }
        """;
```

They do not parse or validate JSON, SQL, HTML, or another embedded language. Prefer parameterised SQL and proper serializers rather than interpolating untrusted data into a text block.

## var

Local-variable type inference can remove repetition:

```java
var response = client.send(request, BodyHandlers.ofString());
```

The variable remains statically typed. Use an explicit type when it communicates an abstraction or makes unfamiliar code easier to understand.

## Optional

`Optional<T>` communicates that a return value may be absent:

```java
Optional<User> findUser(UserId id) {
    return repository.find(id);
}

User user = findUser(id)
        .orElseThrow(() -> new UserNotFoundException(id));
```

Avoid:

- returning `null` from a method declared to return `Optional`;
- calling `get` without establishing presence;
- using `Optional` for every field or parameter;
- wrapping collections in `Optional` when an empty collection is meaningful;
- using `orElse` when an expensive fallback should be lazy.

## java.time

Use the modern date-time API instead of legacy `Date` and `Calendar` for new code.

| Type | Meaning |
| :--- | :--- |
| `Instant` | A point on the UTC timeline |
| `LocalDate` | Date without time or zone |
| `LocalTime` | Time without date or zone |
| `LocalDateTime` | Date and time without zone or offset |
| `OffsetDateTime` | Date and time with a UTC offset |
| `ZonedDateTime` | Date and time with a region time zone |
| `Duration` | Time-based amount |
| `Period` | Date-based amount |

```java
Clock clock = Clock.systemUTC();
Instant now = clock.instant();

ZonedDateTime dublinTime = now.atZone(
        ZoneId.of("Europe/Dublin")
);
```

Inject a `Clock` into time-dependent code to make tests deterministic:

```java
Clock fixed = Clock.fixed(
        Instant.parse("2026-08-11T10:00:00Z"),
        ZoneOffset.UTC
);
```

Do not store a `LocalDateTime` when the domain requires an unambiguous moment. Daylight-saving transitions can make local times ambiguous or invalid.

Use `DateTimeFormatter` for explicit formats:

```java
String value = DateTimeFormatter.ISO_INSTANT.format(now);
```

## Immutable Collection Factories

```java
List<String> roles = List.of("USER", "AUDITOR");
Map<String, Integer> limits = Map.of(
        "read", 100,
        "write", 10
);
```

These collections are unmodifiable and reject nulls. Use `copyOf` for an unmodifiable snapshot of another collection.

## HTTP Client

The standard HTTP client supports synchronous and asynchronous requests:

```java
HttpClient client = HttpClient.newBuilder()
        .connectTimeout(Duration.ofSeconds(2))
        .build();

HttpRequest request = HttpRequest.newBuilder()
        .uri(URI.create("https://api.example.com/health"))
        .timeout(Duration.ofSeconds(5))
        .GET()
        .build();

HttpResponse<String> response = client.send(
        request,
        HttpResponse.BodyHandlers.ofString()
);
```

Connection timeout and request timeout are separate concerns. Validate status, fields, content type, and response contract rather than assuming successful transport means successful application behaviour.

## Modules

The Java Platform Module System can declare module dependencies and exported packages:

```java
module com.example.orders {
    requires java.net.http;
    exports com.example.orders.api;
}
```

Modules strengthen configuration and encapsulation at module boundaries. They are distinct from Maven or Gradle dependencies, although build tools can compile and package modular applications.

Adoption has ecosystem and migration costs; do not add modules merely because the feature exists.

## Preview Features

Preview language or VM features require explicit compile and runtime flags and can change or disappear:

```bash
javac --enable-preview --release 25 Example.java
java --enable-preview Example
```

Never copy a preview example into a project without checking the exact JDK release and project policy. Code compiled with preview features is tied to that release's preview definition.

## Modernisation Guidelines

- Upgrade in small, verified steps.
- Establish runtime, build, framework, and library compatibility first.
- Add tests around important behaviour before mechanical migration.
- Prefer features that improve the model or remove defect-prone code.
- Keep formatting-only changes separate from behavioural changes.
- Measure startup, memory, throughput, and latency when runtime upgrades matter operationally.
- Read migration guides and release notes for removed or strongly encapsulated internals.

## Common Mistakes

- Assuming a record is deeply immutable.
- Adding a default switch branch that hides missing sealed variants.
- Using pattern matching only to compress unreadable control flow.
- Treating `var` as dynamic typing.
- Storing instants in local date-time values.
- Calling `Optional.get` without a presence guarantee.
- Assuming unmodifiable collections copy mutable elements.
- Using text blocks to concatenate untrusted SQL.
- Enabling a preview feature without planning future upgrades.

## Interview Checklist

You should be able to explain:

- records and shallow immutability;
- sealed hierarchies and exhaustive switches;
- pattern matching and switch expressions;
- appropriate use of `var` and `Optional`;
- `Instant` versus local, offset, and zoned time;
- deterministic time through `Clock`;
- unmodifiable collection factories;
- standard HTTP client basics;
- modules and preview-feature trade-offs.

## Further Reading

- [Java language updates](https://docs.oracle.com/en/java/javase/25/language/java-language-changes.html)
- [Records](https://docs.oracle.com/en/java/javase/25/language/records.html)
- [Sealed classes and interfaces](https://docs.oracle.com/en/java/javase/25/language/sealed-classes-and-interfaces.html)
- [`java.time` package](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/time/package-summary.html)
- [Java HTTP client](https://docs.oracle.com/en/java/javase/25/docs/api/java.net.http/java/net/http/HttpClient.html)
