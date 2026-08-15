# Java Exceptions and Resources

Exceptions represent abnormal conditions that interrupt normal control flow. Good exception design preserves meaning, failure context, and resource safety without hiding defects.

## Throwable Hierarchy

```text
Throwable
├── Error
└── Exception
    ├── checked exceptions
    └── RuntimeException
        └── unchecked exceptions
```

### Checked Exceptions

A checked exception must be caught or declared:

```java
String read(Path path) throws IOException {
    return Files.readString(path);
}
```

Use a checked exception when callers can reasonably recover or must make a deliberate policy decision and the API benefits from compiler enforcement.

### Unchecked Exceptions

Subclasses of `RuntimeException` do not need to be declared:

```java
throw new IllegalArgumentException("quantity must be positive");
```

They commonly represent invalid arguments, illegal state, broken invariants, or programming errors. “Unchecked” does not mean unimportant or safe to ignore.

### Errors

`Error` subclasses normally indicate serious JVM, linkage, or environment problems. Application code should not catch `Throwable` or broadly catch `Error` as ordinary recovery.

## Throwing and Declaring

`throw` creates an exceptional transfer of control:

```java
if (quantity <= 0) {
    throw new IllegalArgumentException(
            "quantity must be positive: " + quantity
    );
}
```

`throws` declares possible checked exceptions:

```java
void importFile(Path path) throws IOException {
    // ...
}
```

An exception message should help diagnose the condition without revealing secrets or personal data.

## Catch Specific Exceptions

```java
try {
    return repository.load(id);
} catch (TimeoutException exception) {
    throw new UserLookupException(
            "Timed out loading user " + id,
            exception
    );
}
```

Catch an exception only when the current layer can:

- recover;
- retry according to an explicit policy;
- translate it into a meaningful abstraction;
- add useful context;
- perform cleanup before propagating.

Avoid:

```java
try {
    process();
} catch (Exception exception) {
    return null;
}
```

This hides programming defects and loses the difference between valid absence and failure.

## Preserve the Cause

When translating an exception, pass the original cause:

```java
final class OrderLoadException extends RuntimeException {
    OrderLoadException(String message, Throwable cause) {
        super(message, cause);
    }
}
```

Without the cause, stack traces lose the original failure evidence.

## Multi-Catch

Handle exceptions identically when they represent the same recovery policy:

```java
try {
    parseAndStore(input);
} catch (ParseException | IOException exception) {
    throw new ImportException("Import failed", exception);
}
```

Do not combine exceptions merely to reduce lines when they need different handling.

## finally

A `finally` block runs as control leaves a `try` statement in most circumstances:

```java
lock.lock();
try {
    updateSharedState();
} finally {
    lock.unlock();
}
```

Do not return from `finally`. It can suppress an exception or override the intended return value.

For `AutoCloseable` resources, prefer try-with-resources.

## Try-With-Resources

Resources declared in the header close in reverse declaration order:

```java
try (
        InputStream input = Files.newInputStream(source);
        OutputStream output = Files.newOutputStream(target)
) {
    input.transferTo(output);
}
```

Any object implementing `AutoCloseable` can participate.

If the body and `close` both throw, the body exception is primary and close exceptions are suppressed:

```java
try {
    copy();
} catch (IOException exception) {
    for (Throwable suppressed : exception.getSuppressed()) {
        System.out.println(suppressed.getMessage());
    }
}
```

This preserves more evidence than a hand-written `finally` that accidentally replaces the original exception.

An effectively final resource created earlier can also be used:

```java
BufferedReader reader = Files.newBufferedReader(path);
try (reader) {
    return reader.readLine();
}
```

## Designing Exception Contracts

A useful exception contract answers:

- Which conditions are expected?
- Which are retryable?
- Which represent invalid input?
- Which indicate absence rather than failure?
- Which details are safe to expose?
- At which boundary is the exception translated into an HTTP response, message failure, or process exit?

Do not use exceptions for ordinary branching when a value such as an empty collection, result type, or `Optional` represents expected absence more clearly.

Conversely, returning `null` for every failure destroys information.

## Custom Exceptions

Create a custom type when callers need a stable domain-level distinction:

```java
final class InsufficientStockException extends RuntimeException {
    private final String productId;

    InsufficientStockException(String productId) {
        super("Insufficient stock for product " + productId);
        this.productId = productId;
    }

    String productId() {
        return productId;
    }
}
```

Avoid creating a separate class for every message when callers do not behave differently.

## InterruptedException

Interruption is a cooperative cancellation signal. Do not swallow it:

```java
try {
    queue.put(task);
} catch (InterruptedException exception) {
    Thread.currentThread().interrupt();
    throw new TaskSubmissionException(
            "Interrupted while submitting task",
            exception
    );
}
```

Either propagate `InterruptedException` or restore the interrupt status when translating it, unless the code intentionally consumes the cancellation signal.

## Assertions

Java assertions are disabled by default unless enabled with `-ea`:

```java
assert index >= 0 : "index must be non-negative";
```

Use assertions for internal invariants during development, not public argument validation, authorisation, or required production checks.

## Logging Exceptions

Log once at the layer that owns the operational response. Logging and rethrowing at every layer creates duplicate noise.

Prefer structured context and pass the exception object to the logging API:

```java
logger.error("Order import failed for batch {}", batchId, exception);
```

Do not concatenate stack traces or expose credentials, tokens, full payloads, or sensitive identifiers.

## Testing Failure Paths

Test:

- the expected exception type;
- meaningful message or structured properties where part of the contract;
- preserved causes;
- resource closure;
- state after partial failure;
- retry and interruption behaviour;
- suppressed exceptions when relevant;
- absence of sensitive information.

```java
IllegalArgumentException exception = assertThrows(
        IllegalArgumentException.class,
        () -> service.createOrder(0)
);

assertTrue(exception.getMessage().contains("quantity"));
```

Avoid asserting an entire incidental stack trace or message that is not contractual.

## Common Mistakes

- Catching `Exception` or `Throwable` without a deliberate boundary policy.
- Swallowing exceptions or returning `null`.
- Throwing a new exception without preserving the cause.
- Logging and rethrowing at every layer.
- Retrying permanent failures.
- Ignoring `InterruptedException`.
- Closing resources only on the success path.
- Returning from `finally`.
- Using assertions for production validation.
- Exposing internal implementation or sensitive values in messages.

## Interview Checklist

You should be able to explain:

- checked versus unchecked exceptions and `Error`;
- `throw` versus `throws`;
- when to catch, translate, or propagate;
- cause chains and suppressed exceptions;
- `finally` and try-with-resources;
- deterministic cleanup versus garbage collection;
- interruption handling;
- how to test exceptional behaviour.

## Further Reading

- [Java Language Specification: Exceptions](https://docs.oracle.com/javase/specs/jls/se25/html/jls-11.html)
- [`Throwable` API](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/lang/Throwable.html)
- [`AutoCloseable` API](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/lang/AutoCloseable.html)

Return to the [Java Study Guide](./README.md).
