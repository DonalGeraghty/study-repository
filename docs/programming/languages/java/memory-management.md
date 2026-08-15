# Java Memory Management

Java provides automatic memory management, but developers still need to understand object reachability, resource ownership, allocation pressure, and diagnostic evidence.

The Java specifications define observable behaviour without requiring every JVM to use exactly the same physical layout. Terms such as heap, thread stack, and Metaspace below describe the standard JVM model and common HotSpot implementation—not a guarantee that every value occupies one fixed location.

## Runtime Memory Areas

### Heap

The heap is shared between threads and is the primary area from which objects and arrays are allocated. Garbage collectors reclaim heap storage for objects that are no longer reachable.

The JVM may optimise allocations through escape analysis, scalar replacement, thread-local allocation buffers, or other techniques. Avoid reasoning that every source-level `new` must produce one physically distinct heap allocation visible to the developer.

### JVM Stacks

Each thread has a JVM stack containing frames for active method calls. A frame conceptually contains local variables, operand-stack state, and information needed to return from the method.

Primitive values and references can appear in local-variable slots, but “primitives live on the stack and objects live on the heap” is too simplistic:

- primitive fields are part of their containing object;
- array elements are part of the array object;
- static fields belong to class state;
- the JVM can optimise physical representation;
- the language does not expose raw object addresses.

Deep or uncontrolled recursion can exhaust a thread's stack and produce `StackOverflowError`.

### Metaspace and Class Metadata

HotSpot stores class metadata in native memory called **Metaspace**. PermGen was removed in JDK 8.

Metaspace is not where Java static field values are generally described as living. A static field is associated with a class, but its runtime storage is an implementation detail. If a static field references an object, that reference can keep the object reachable.

Class metadata can be unloaded when the defining class loader and its classes become unreachable. Class-loader leaks are therefore a common source of long-lived server memory retention.

### Native and Off-Heap Memory

A Java process also uses memory outside the managed Java heap, including:

- thread stacks;
- Metaspace and code cache;
- JIT-compiled code and JVM structures;
- direct byte buffers;
- native libraries;
- memory-mapped files.

An operating-system memory problem is not automatically a Java heap leak.

## Reachability

Garbage collection is based on reachability from **GC roots**, not on whether a variable name still appears in source code.

Typical roots include:

- live thread and stack references;
- static references from loaded classes;
- JNI references;
- JVM-internal references.

```java
List<byte[]> retained = new ArrayList<>();

void allocate() {
    byte[] temporary = new byte[1_000_000];
    retained.add(temporary);
}
```

The local variable `temporary` disappears after the method returns, but the array remains reachable through `retained`. Garbage collection cannot reclaim an object that the application still references.

Setting a variable to `null` does not force collection. It can remove one reference, but the object is eligible for reclamation only when no relevant reachability path remains.

## Reference Strength

Java supplies reference types for specialised retention policies:

| Reference | Typical meaning |
| :--- | :--- |
| Strong reference | Normal reference; prevents collection while reachable. |
| `SoftReference` | May be cleared in response to memory demand; not a dependable general cache policy. |
| `WeakReference` | Does not prevent collection once only weakly reachable. |
| `PhantomReference` | Supports post-mortem cleanup coordination with a `ReferenceQueue`. |

Prefer explicit bounded caches and deterministic resource management. Reference classes are advanced tools, not substitutes for clear ownership.

## Garbage Collection

A collector identifies reachable objects and reclaims space associated with unreachable objects. Collectors may use combinations of:

- tracing from roots;
- copying live objects;
- sweeping unreachable regions;
- compacting live data;
- generations or regions;
- parallel work;
- concurrent work performed while application threads run.

“Mark, sweep, compact” is a useful introductory model, not a universal three-step algorithm used identically by every collector.

## Generational Behaviour

Many collectors exploit the observation that a large proportion of objects become unreachable soon after allocation. They may collect young objects frequently and retain surviving objects in older regions.

Do not assume every collector exposes exactly “young,” “old,” “minor GC,” and “major GC” in the same way. Collector terminology and mechanics differ.

## Collectors and Trade-Offs

HotSpot provides collectors designed for different goals. Availability and defaults depend on the JDK and runtime configuration.

- **Serial GC** uses a simple collector suited to small or constrained workloads.
- **Parallel GC** prioritises throughput using multiple GC threads.
- **G1 GC** is the common server default and balances throughput with pause-time goals.
- **ZGC** targets very low pause times for large heaps and latency-sensitive applications.
- Other OpenJDK distributions may provide additional collectors.

Every collector makes trade-offs among throughput, latency, memory footprint, CPU use, and operational complexity. Select and tune from measured service objectives rather than folklore.

## Stop-the-World Pauses

All mainstream collectors require some operations during which application threads reach a safe point and pause. Concurrent collectors perform more work alongside the application, reducing pause duration but not making every pause disappear.

Measure pause distributions and application latency together. A low GC pause does not guarantee low request latency if allocation, CPU contention, or dependencies dominate.

## Explicit Collection

```java
System.gc();
```

This is a request, not a guarantee. The runtime can ignore it, and an explicit full collection may damage latency. Application code should rarely depend on it.

## Garbage Collection Is Not Resource Cleanup

Files, sockets, database connections, locks, and similar resources need deterministic release:

```java
try (BufferedReader reader = Files.newBufferedReader(path)) {
    return reader.readLine();
}
```

Try-with-resources calls `close` even when the block exits through an exception. Do not wait for garbage collection to release scarce external resources.

## Memory Leaks in Managed Code

A Java memory leak usually means objects remain reachable longer than intended.

Common causes include:

- unbounded static collections or caches;
- listeners and callbacks that are never removed;
- `ThreadLocal` values retained by pooled threads;
- tasks queued faster than they are processed;
- class loaders retained during redeployment;
- maps keyed by objects with unexpectedly long lifetimes;
- executor services or threads that are never shut down;
- caches without size, expiry, or eviction policies;
- accidental retention through inner objects or lambdas.

## Memory Pressure Symptoms

Potential symptoms include:

- steadily increasing live heap after full collections;
- increasing GC frequency or pause time;
- falling throughput;
- `OutOfMemoryError` variants;
- operating-system memory growth;
- allocation stalls;
- excessive object churn without long-term retention.

Different `OutOfMemoryError` messages point to different areas, such as Java heap, Metaspace, direct buffer memory, or inability to create native threads.

## Diagnostic Workflow

1. Reproduce or observe the problem with the least intrusive tooling available.
2. Confirm whether the pressure is heap, Metaspace, native memory, threads, or allocation rate.
3. Correlate memory behaviour with workload, deployment, and configuration changes.
4. Capture GC logs, Java Flight Recorder data, class histograms, or heap dumps when appropriate.
5. Compare retained-object paths and dominators rather than only object counts.
6. Fix ownership or lifecycle behaviour, then repeat the same measurement.

Useful JDK tools include:

```bash
jcmd <pid> VM.flags
jcmd <pid> GC.heap_info
jcmd <pid> GC.class_histogram
jcmd <pid> JFR.start name=memory settings=profile duration=60s filename=memory.jfr
```

Heap dumps can contain credentials and personal data. Store and share them as sensitive production artefacts.

Enable unified GC logging when launching an application:

```bash
java -Xlog:gc*:file=gc.log:time,uptime,level,tags -jar app.jar
```

Flag availability and syntax can vary by JDK; verify against the runtime in use.

## Common Misconceptions

- **“Calling `null` frees the object.”** It removes one reference; reachability and collection timing determine reclamation.
- **“GC prevents memory leaks.”** It prevents manual deallocation errors but cannot infer that a reachable object is no longer useful.
- **“More heap always fixes memory problems.”** It may delay failure, increase pause costs, or hide unbounded retention.
- **“Objects are deleted immediately when a method returns.”** Lifetime follows reachability, not lexical scope alone.
- **“Static values live in Metaspace.”** Metaspace holds HotSpot class metadata; static references can retain ordinary objects.
- **“ZGC or G1 eliminates all pauses.”** Concurrent collectors reduce specific pauses but cannot remove every safepoint or latency source.

## Interview Checklist

You should be able to explain:

- heap, JVM stacks, Metaspace, and native memory without oversimplifying;
- GC roots and reachability;
- why a reachable object can still represent a leak;
- generational collection as a strategy rather than a language guarantee;
- throughput versus pause-time collector goals;
- why try-with-resources is unrelated to object reclamation;
- how you would gather evidence for a suspected memory leak.

## Further Reading

- [Java 25 GC tuning guide](https://docs.oracle.com/en/java/javase/25/gctuning/)
- [Introduction to garbage collection tuning](https://docs.oracle.com/en/java/javase/25/gctuning/introduction-garbage-collection-tuning.html)
- [Troubleshooting memory leaks](https://docs.oracle.com/en/java/javase/25/troubleshoot/troubleshooting-memory-leaks.html)
- [Java Virtual Machine Specification: Runtime Data Areas](https://docs.oracle.com/javase/specs/jvms/se25/html/jvms-2.html#jvms-2.5)

Return to the [Java Study Guide](./README.md).
