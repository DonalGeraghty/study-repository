# C# and .NET

C# is a statically typed language for the .NET platform. It supports object-oriented, generic, functional, and asynchronous programming across web services, desktop applications, cloud workloads, and test automation.

## Language, Runtime, and Tooling

- **C#** defines language syntax and semantics.
- **.NET runtime** executes managed code and provides garbage collection, type loading, and runtime services.
- **Base Class Library** supplies collections, I/O, networking, tasks, reflection, and other common APIs.
- **SDK** provides the compiler, CLI, project system, package tooling, and build infrastructure.

```bash
dotnet new console --name StudyApp
dotnet build StudyApp
dotnet run --project StudyApp
dotnet test
```

## Types and Nullability

C# distinguishes value types such as `int`, `bool`, and structs from reference types such as classes and arrays. Assignment copies a value type’s value; for a reference type it copies the reference.

Nullable reference types add compiler analysis to make missing-value intent explicit:

```csharp
static int NameLength(string? name)
{
    return name?.Length ?? 0;
}
```

Warnings improve design feedback but do not make runtime `null` impossible. Validate untrusted inputs at system boundaries.

## Records, Classes, and Interfaces

Use classes for objects with identity and lifecycle. Records provide value-oriented equality and concise immutable models.

```csharp
public sealed record TestResult(string Name, bool Passed);

public interface IResultStore
{
    Task SaveAsync(TestResult result, CancellationToken cancellationToken);
}
```

Depend on focused interfaces at boundaries. Avoid interfaces created only to mirror every class; abstractions should express a capability or substitution need.

## Collections and LINQ

Common generic collections include `List<T>`, `Dictionary<TKey,TValue>`, `HashSet<T>`, and queue types. LINQ composes filtering, projection, grouping, sorting, and aggregation:

```csharp
IEnumerable<string> failedNames = results
    .Where(result => !result.Passed)
    .OrderBy(result => result.Name)
    .Select(result => result.Name);
```

Many LINQ queries use deferred execution: the query runs when enumerated. Materialise with `ToList()` or `ToArray()` when a stable snapshot is required. Remember that database-backed `IQueryable<T>` providers translate expressions and may not behave like in-memory `IEnumerable<T>`.

## Exceptions and Resource Management

Throw exceptions when an operation cannot satisfy its contract. Catch specific types where code can recover, translate, retry, or add context. `using` disposes resources deterministically:

```csharp
await using FileStream stream = File.OpenRead(path);
// stream is disposed when the scope exits
```

Do not use exceptions for expected high-volume branching when a result model would express the outcome more clearly.

## Asynchronous Programming

`Task` and `Task<T>` represent asynchronous work. `await` suspends the method without blocking the calling thread while the awaited operation is incomplete.

```csharp
static async Task<string> LoadAsync(
    HttpClient client,
    Uri uri,
    CancellationToken cancellationToken)
{
    return await client.GetStringAsync(uri, cancellationToken);
}
```

Prefer `Task` over `async void` except for event handlers. Propagate cancellation, set timeouts, avoid blocking with `.Result` or `.Wait()`, and bound fan-out when starting many operations concurrently.

## Projects, Packages, and Configuration

The project file defines target frameworks, dependencies, compiler options, and build items. NuGet manages packages. Pin supported SDK behaviour, treat warnings intentionally, and keep secrets outside source-controlled configuration.

Use dependency injection for external capabilities when it clarifies ownership and testing. It should not turn every value or pure function into a service.

## Testing and Diagnostics

Separate pure business rules from clocks, files, networks, and databases. Unit-test the former and use focused integration tests for real infrastructure. Test asynchronous code by awaiting it, not by adding sleeps.

Useful commands include:

```bash
dotnet format --verify-no-changes
dotnet build --configuration Release
dotnet test --configuration Release
```

Use structured logs, exception stack traces, the debugger, dumps, traces, and runtime counters according to the failure being investigated.

## Readiness Checklist

You should be able to:

- explain value types, reference types, generics, equality, and nullability;
- model behaviour with classes, records, and focused interfaces;
- reason about LINQ translation, deferred execution, and materialisation;
- manage disposable resources and exception boundaries;
- write cancellable asynchronous operations without sync-over-async;
- structure SDK projects and control NuGet dependencies;
- test application logic and diagnose managed-runtime failures.

## Official References

- [C# documentation](https://learn.microsoft.com/dotnet/csharp/)
- [.NET documentation](https://learn.microsoft.com/dotnet/)
- [.NET CLI overview](https://learn.microsoft.com/dotnet/core/tools/)
- [Asynchronous programming](https://learn.microsoft.com/dotnet/csharp/asynchronous-programming/)

Return to the [Programming Languages](./README.md) guide.
