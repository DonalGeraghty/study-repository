# Java Concurrency

Concurrency allows tasks to make progress during overlapping periods. Correct concurrent code requires explicit reasoning about shared state, visibility, atomicity, ordering, cancellation, and resource limits.

## Concurrency Versus Parallelism

- **Concurrency** is the composition of independently progressing tasks.
- **Parallelism** is simultaneous execution on multiple processing resources.

A concurrent design may run on one core. A parallel computation is also concurrent, but not every concurrent workload benefits from more CPU threads.

## Thread Safety

Code is thread-safe when it behaves correctly under permitted concurrent use.

Prefer these strategies in order:

1. immutable data;
2. thread confinement;
3. message passing or task ownership;
4. well-defined concurrent collections and atomic operations;
5. locks or other explicit synchronisation.

Reducing shared mutable state is usually easier than protecting it.

## Race Conditions

```java
final class Counter {
    private int value;

    void increment() {
        value++;
    }
}
```

`value++` is a read-modify-write sequence, not one atomic operation. Concurrent calls can lose updates.

## Visibility and Happens-Before

Threads may cache, reorder, or observe memory operations according to the Java Memory Model. One thread's write is not automatically visible to another at the expected time.

A **happens-before** relationship establishes visibility and ordering guarantees. It can arise from mechanisms including:

- monitor unlock followed by a later lock of the same monitor;
- a write to a volatile field followed by a later read of that field;
- actions before starting a thread and actions in that thread;
- actions in a thread and successful detection of its termination;
- concurrency-utility contracts.

“It usually works on my machine” is not a memory-visibility guarantee.

## synchronized

`synchronized` provides mutual exclusion and memory-visibility guarantees:

```java
final class Counter {
    private int value;

    synchronized void increment() {
        value++;
    }

    synchronized int value() {
        return value;
    }
}
```

Every access participating in the invariant must follow the same locking policy. Returning or exposing mutable protected state can break the policy.

Keep lock scope clear and avoid calling unknown or slow external code while holding a lock.

## volatile

A volatile field provides visibility and ordering guarantees for reads and writes of that field:

```java
final class Worker {
    private volatile boolean stopRequested;

    void requestStop() {
        stopRequested = true;
    }

    void runLoop() {
        while (!stopRequested) {
            doWork();
        }
    }
}
```

`volatile` does not make compound actions atomic:

```java
private volatile int count;

void increment() {
    count++; // Still not atomic
}
```

Use it for independent state publication or flags when the complete invariant fits its semantics.

## Atomic Variables

```java
AtomicInteger count = new AtomicInteger();
int updated = count.incrementAndGet();
```

Atomics support lock-free atomic operations on individual values. Compound invariants spanning several variables may still need a lock or immutable state transition.

`LongAdder` can reduce contention for frequently updated statistics, but `sum()` is not an atomic snapshot relative to concurrent updates.

## Locks

`ReentrantLock` supports explicit locking, interruptible acquisition, timed attempts, and multiple conditions:

```java
lock.lock();
try {
    updateState();
} finally {
    lock.unlock();
}
```

Always release a lock in `finally`. Prefer `synchronized` when its simpler structured locking is sufficient.

`ReadWriteLock` or `StampedLock` can help specialised read-heavy workloads, but additional complexity must be justified by measurement.

## Concurrent Collections

Use library implementations with documented concurrency contracts:

- `ConcurrentHashMap` for concurrent key-value access;
- `CopyOnWriteArrayList` for read-heavy, rarely modified snapshot traversal;
- `BlockingQueue` for producer-consumer coordination;
- `ConcurrentLinkedQueue` for non-blocking queue operations.

Use atomic compound APIs:

```java
cache.computeIfAbsent(key, this::load);
counts.merge(key, 1, Integer::sum);
```

A thread-safe collection does not make the objects inside it thread-safe.

## Executors

Separate task submission from thread management:

```java
try (ExecutorService executor = Executors.newFixedThreadPool(4)) {
    Future<Result> future = executor.submit(this::calculate);
    Result result = future.get();
}
```

Choose pool size and queue capacity from workload and resource constraints. An unbounded queue can hide overload until latency and memory become unacceptable.

Handle `ExecutionException`, cancellation, timeout, and interruption deliberately:

```java
try {
    return future.get(2, TimeUnit.SECONDS);
} catch (InterruptedException exception) {
    Thread.currentThread().interrupt();
    throw new OperationInterruptedException(exception);
}
```

## Virtual Threads

Virtual threads support a thread-per-task style for large numbers of mostly blocking operations:

```java
try (ExecutorService executor =
             Executors.newVirtualThreadPerTaskExecutor()) {
    Future<String> first = executor.submit(this::callService);
    Future<String> second = executor.submit(this::loadRecord);

    combine(first.get(), second.get());
}
```

Virtual threads:

- make blocking code scalable when tasks spend much of their time waiting;
- do not make CPU-bound work execute faster than available processors;
- should not normally be pooled merely to limit thread count;
- still need limits around scarce resources such as database connections or external quotas;
- do not remove races, deadlocks, or shared-state problems.

Use semaphores, connection pools, or other domain limits for scarce resources.

## CompletableFuture

`CompletableFuture` composes asynchronous stages:

```java
CompletableFuture<User> user =
        CompletableFuture.supplyAsync(this::loadUser, executor);

CompletableFuture<List<Order>> orders =
        user.thenCompose(value -> loadOrders(value.id()));

List<Order> result = orders
        .orTimeout(2, TimeUnit.SECONDS)
        .join();
```

Understand which executor runs each stage, how exceptions are wrapped, and how cancellation propagates. Long chains can become harder to debug than clear blocking code on virtual threads.

## Interruption and Cancellation

Interruption is cooperative:

```java
try {
    queue.take();
} catch (InterruptedException exception) {
    Thread.currentThread().interrupt();
    return;
}
```

Blocking library methods commonly throw `InterruptedException`. Propagate it or restore the flag when translating it. Code that ignores interruption can prevent shutdown.

Cancellation must define what happens to partially completed state and external operations.

## Deadlock

Deadlock can occur when threads wait in a cycle:

```text
Thread A holds lock 1 and waits for lock 2
Thread B holds lock 2 and waits for lock 1
```

Reduce risk by:

- acquiring locks in one global order;
- reducing nested locking;
- avoiding calls to external code while locked;
- using timed lock acquisition where suitable;
- preferring higher-level concurrency utilities.

Thread dumps are useful evidence when diagnosing deadlock.

## Other Liveness Problems

- **Starvation**: a task repeatedly fails to obtain resources or execution time.
- **Livelock**: tasks keep reacting to each other without making progress.
- **Priority inversion**: higher-priority work waits indirectly on lower-priority work.
- **Overload collapse**: excessive concurrency increases queueing and reduces useful throughput.

Correctness includes liveness, not only freedom from data races.

## Safe Publication and Immutability

Construct immutable state fully before sharing it:

```java
record Configuration(
        URI endpoint,
        Duration timeout,
        int maxAttempts
) {
    Configuration {
        Objects.requireNonNull(endpoint);
        Objects.requireNonNull(timeout);
    }
}
```

Final fields have special visibility guarantees when construction does not leak `this`. Do not publish an incompletely constructed object from its constructor.

## Testing Concurrent Code

Concurrent tests should assert observable coordination rather than sleep and hope:

- use barriers, latches, semaphores, or controllable executors;
- apply timeouts so failures terminate;
- repeat stress scenarios where probabilistic exposure is useful;
- capture thread dumps on hangs;
- test cancellation, interruption, shutdown, and overload;
- verify invariants rather than one incidental ordering;
- use specialised tools for race or concurrency stress where appropriate.

One passing test cannot prove absence of a race. Design and memory-model reasoning remain essential.

## Common Mistakes

- Assuming `++` is atomic.
- Using `volatile` for a multi-variable invariant.
- Synchronising writes but not reads.
- Locking on publicly accessible or mutable objects.
- Forgetting to release an explicit lock.
- Swallowing interruption.
- Using unbounded executors or queues.
- Blocking the shared common pool with uncontrolled I/O.
- Assuming virtual threads eliminate resource limits.
- Testing concurrency with fixed sleeps.

## Interview Checklist

You should be able to explain:

- concurrency versus parallelism;
- races, atomicity, visibility, ordering, and happens-before;
- `synchronized` versus `volatile`;
- atomics, locks, and concurrent collections;
- executor lifecycle and bounded resources;
- virtual-thread strengths and limitations;
- interruption and cancellation;
- deadlock prevention and diagnosis;
- approaches to testing concurrent behaviour.

## Further Reading

- [Java Language Specification: Threads and Locks](https://docs.oracle.com/javase/specs/jls/se25/html/jls-17.html)
- [`java.util.concurrent` package](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/util/concurrent/package-summary.html)
- [Virtual threads](https://docs.oracle.com/en/java/javase/25/core/virtual-threads.html)
- [`CompletableFuture` API](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/util/concurrent/CompletableFuture.html)

Return to the [Java Study Guide](./README.md).
