---
tags:
  - programming/languages
---

# C# and .NET

C# is a statically typed language for the .NET platform. It supports object-oriented, generic, functional, and asynchronous programming across web services, desktop applications, cloud workloads, and test automation.

## Language, Runtime, and Tooling

C# defines the language's syntax and semantics. The .NET runtime executes managed code and provides garbage collection, type loading and other runtime services.

The Base Class Library supplies common APIs for collections, I/O, networking, tasks and reflection. The SDK provides the compiler, CLI, project system, package tooling and build infrastructure used to develop the application.

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

## Worked Prediction: Deferred Queries

With `System.Linq` and generic collections available, predict both lines:

```csharp
var scores = new List<int> { 40, 80 };
var passing = scores.Where(score => score >= 70);
var snapshot = passing.ToList();
scores.Add(90);
Console.WriteLine(string.Join(",", passing));
Console.WriteLine(string.Join(",", snapshot));
```

**Check your reasoning:** The first line is `80,90`; the second is `80`. The query enumerates the current source when used, while `ToList()` captured the earlier matching values. Enumerating twice can repeat work or observe different data. Materialisation copies the sequence, not every mutable object inside it.

Change the elements to mutable objects and update a property after `ToList()`. Explain why that snapshot may still reflect the mutation. Likewise, a record provides value-oriented equality but does not automatically make referenced collections deeply immutable.

## Interview Questions

> [!question] Interview Questions
> - What's the difference between a value type and a reference type in C#, and why does that matter for equality and mutation?
> - What does LINQ's deferred execution actually defer, and when does a query get materialised?
> - Why is sync-over-async a problem, and how would you write a cancellable asynchronous operation instead?
> - How would you manage a disposable resource so it's released even when an exception is thrown?

## Answer Notes

1. Assignment copies a value type's value, while assigning a reference type copies a reference to the same object. Shared object mutation is visible through aliases; value types can also contain shared references. Equality depends on the type's defined semantics, not only this storage distinction.

2. Many LINQ queries defer enumeration and transformation until their results are consumed. ToList or ToArray materialises a snapshot; enumerating again may rerun work or observe changed data, depending on the source.

3. Blocking on asynchronous work wastes threads and can deadlock in some synchronisation contexts. Return Task-based results, await operations, pass CancellationToken through supported calls and ensure cleanup still runs on cancellation.

4. Use using or a using declaration for IDisposable, and await using for IAsyncDisposable where appropriate. Disposal runs when control leaves the scope, including exceptional exits; ensure ownership is clear so shared resources are not disposed prematurely.

## Official References

- [C# documentation](https://learn.microsoft.com/dotnet/csharp/)
- [.NET documentation](https://learn.microsoft.com/dotnet/)
- [.NET CLI overview](https://learn.microsoft.com/dotnet/core/tools/)
- [Asynchronous programming](https://learn.microsoft.com/dotnet/csharp/asynchronous-programming/)

Return to [Programming Languages](./README.md).
