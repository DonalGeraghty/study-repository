# Java Collections Framework

The Java Collections Framework provides interfaces, implementations, algorithms, and interoperability conventions for groups of objects.

## Core Hierarchy

`Map` is part of the framework but does not extend `Collection`.

```text
Iterable
└── Collection
    ├── List
    ├── Set
    │   └── SortedSet / NavigableSet
    └── Queue
        └── Deque

Map
└── SortedMap / NavigableMap
```

Program to the interface that expresses the required behaviour:

```java
List<String> names = new ArrayList<>();
Set<String> uniqueNames = new HashSet<>();
Map<String, User> usersById = new HashMap<>();
```

Choose an implementation from ordering, lookup, mutation, concurrency, memory, null, and key-equality requirements.

## List

A `List` is an ordered sequence that normally permits duplicates and positional access.

### ArrayList

Backed by a resizable array.

- Constant-time positional access in the usual model.
- Amortised constant-time append.
- Middle insertion or removal shifts later elements.
- Excellent locality and usually the default general-purpose `List`.

### LinkedList

A doubly linked list that also implements `Deque`.

- Positional access requires traversal.
- Insertion at a known iterator position can relink nodes without shifting an array.
- Finding an arbitrary insertion position is still linear.
- Has per-element node overhead and poor locality.

“Use `LinkedList` for fast inserts anywhere” is misleading. `ArrayList` is often faster in practice unless the access pattern specifically benefits from linked-node operations. Use `ArrayDeque` for ordinary queues and stacks.

### CopyOnWriteArrayList

Creates a new backing array on mutation.

Useful when reads and iteration greatly outnumber writes and snapshot-style iteration is desired. It is unsuitable for frequent mutation or large write-heavy lists.

## Set

A `Set` contains no pair of elements considered equal by its contract.

### HashSet

- Uses hashing for expected constant-time basic operations.
- Has no iteration-order guarantee.
- Depends on stable, consistent `equals` and `hashCode`.

### LinkedHashSet

- Preserves a defined encounter order, commonly insertion order.
- Uses additional bookkeeping compared with `HashSet`.

### TreeSet

- Keeps elements ordered by natural ordering or a supplied `Comparator`.
- Basic operations are logarithmic.
- Equality for set membership follows comparison result, so a comparator inconsistent with `equals` can surprise callers.

### EnumSet

Compact, efficient set specialised for one enum type:

```java
EnumSet<Permission> permissions =
        EnumSet.of(Permission.READ, Permission.WRITE);
```

Prefer it to a general `HashSet<Permission>` when enum membership is the requirement.

## Queue and Deque

`Queue` supports elements awaiting processing. `Deque` supports insertion and removal at both ends.

### ArrayDeque

The usual choice for a non-concurrent queue or stack:

```java
Deque<Task> tasks = new ArrayDeque<>();
tasks.addLast(task);
Task next = tasks.removeFirst();

Deque<String> stack = new ArrayDeque<>();
stack.push("first");
String top = stack.pop();
```

It does not permit `null`, which keeps `null` available as a sentinel return for methods such as `poll`.

### PriorityQueue

Retrieves the least element according to its ordering:

```java
PriorityQueue<Integer> queue = new PriorityQueue<>();
queue.addAll(List.of(5, 1, 3));

System.out.println(queue.remove()); // 1
```

Iteration does not return elements in sorted order. Repeated removal does.

### BlockingQueue

Concurrency utilities such as `ArrayBlockingQueue` and `LinkedBlockingQueue` support producer-consumer coordination with blocking operations and optional capacity.

Prefer a bounded queue when unbounded backlog would exhaust memory or make latency unacceptable.

## Map

A `Map` associates unique keys with values.

### HashMap

- Expected constant-time `get` and `put` with suitable hashing.
- No iteration-order guarantee.
- Permits one null key and null values.
- Not safe for unsynchronised concurrent mutation.

### LinkedHashMap

Maintains encounter order and can be configured for access order, which is useful when implementing carefully bounded caches.

### TreeMap

Keeps keys ordered by natural ordering or a `Comparator` and provides navigational operations such as `floorEntry` and `ceilingEntry`.

### EnumMap

Compact map specialised for enum keys:

```java
Map<Status, Integer> counts = new EnumMap<>(Status.class);
```

### ConcurrentHashMap

Supports concurrent access without locking the entire map for ordinary operations. It does not permit null keys or values.

Use atomic compound operations:

```java
counts.merge(key, 1, Integer::sum);
cache.computeIfAbsent(key, this::loadValue);
```

This is unsafe as a compound operation even with a concurrent map:

```java
if (!map.containsKey(key)) {
    map.put(key, value);
}
```

Another thread can change the map between calls.

## equals and hashCode

Hash-based collections depend on this contract:

- equal objects must have equal hash codes;
- unequal objects may have the same hash code;
- equality and hash code should remain stable while an object is used as a key or set element.

```java
record UserId(String value) {
    UserId {
        Objects.requireNonNull(value);
    }
}
```

Records derive value-based `equals` and `hashCode` from components, making them useful immutable keys when their components are themselves suitable.

Mutating a key field used by `equals` or `hashCode` can make an entry effectively unreachable through normal lookup.

## Comparable and Comparator

Natural ordering:

```java
final class User implements Comparable<User> {
    @Override
    public int compareTo(User other) {
        return this.id.compareTo(other.id);
    }
}
```

External ordering:

```java
Comparator<User> byNameThenId =
        Comparator.comparing(User::name)
                .thenComparing(User::id);
```

Avoid subtraction-based comparison because it can overflow:

```java
// return this.score - other.score;
return Integer.compare(this.score, other.score);
```

Document null ordering with `nullsFirst` or `nullsLast` when nulls are allowed.

## Mutability and Factory Collections

`List.of`, `Set.of`, and `Map.of` create unmodifiable collections and reject null elements or values:

```java
List<String> roles = List.of("USER", "AUDITOR");
```

`Collections.unmodifiableList(source)` creates an unmodifiable **view**; changes through another reference to `source` remain visible.

```java
List<String> source = new ArrayList<>(List.of("A"));
List<String> view = Collections.unmodifiableList(source);
source.add("B");
System.out.println(view); // [A, B]
```

Use `List.copyOf(source)` when an unmodifiable snapshot is intended, subject to its null restrictions.

## Iteration and Modification

Enhanced for loops use an iterator:

```java
for (String name : names) {
    System.out.println(name);
}
```

Do not structurally modify an ordinary collection through another path while iterating:

```java
Iterator<String> iterator = names.iterator();
while (iterator.hasNext()) {
    if (iterator.next().isBlank()) {
        iterator.remove();
    }
}
```

Fail-fast `ConcurrentModificationException` is a best-effort bug detector, not a concurrency guarantee.

## Bulk Operations

Prefer clear collection operations:

```java
names.removeIf(String::isBlank);
allowed.retainAll(requested);
destination.addAll(source);
```

Streams are useful for transformation; collection methods are often clearer for direct mutation.

## Complexity Guide

These are typical asymptotic expectations, not latency guarantees:

| Operation | `ArrayList` | `LinkedList` | `HashSet` / `HashMap` | `TreeSet` / `TreeMap` |
| :--- | :---: | :---: | :---: | :---: |
| Positional access | O(1) | O(n) | N/A | N/A |
| Append | Amortised O(1) | O(1) | N/A | N/A |
| Search by value | O(n) | O(n) | Expected O(1) by key | O(log n) by key |
| Middle insert after search | O(n) | O(n) | N/A | N/A |
| Ordered traversal | Insertion/index order | List order | Not guaranteed | Sorted order |

Hash collisions, resizing, cache locality, allocation, comparator cost, and workload size affect real performance. Benchmark representative workloads when the choice matters.

## Selection Guide

| Requirement | Prefer |
| :--- | :--- |
| General indexed list | `ArrayList` |
| Double-ended queue or stack | `ArrayDeque` |
| Unique values, no order | `HashSet` |
| Unique values, encounter order | `LinkedHashSet` |
| Unique sorted values | `TreeSet` |
| Enum membership | `EnumSet` |
| General key-value lookup | `HashMap` |
| Key-value lookup with encounter order | `LinkedHashMap` |
| Sorted or navigable keys | `TreeMap` |
| Enum keys | `EnumMap` |
| Concurrent key-value access | `ConcurrentHashMap` |
| Bounded producer-consumer queue | `ArrayBlockingQueue` |
| Read-heavy, rarely modified concurrent list | `CopyOnWriteArrayList` |

## Common Mistakes

- Selecting an implementation from slogans rather than access patterns.
- Using mutable keys in a hash map.
- Implementing `equals` without compatible `hashCode`.
- Assuming `HashMap` iteration order.
- Assuming `PriorityQueue` iteration is sorted.
- Confusing an unmodifiable view with an immutable snapshot.
- Storing `null` where absence needs explicit modelling.
- Performing check-then-act sequences on a concurrent collection.
- Catching `ConcurrentModificationException` instead of fixing unsafe mutation.
- Returning mutable internal collections directly.

## Interview Checklist

You should be able to explain:

- the `Collection` hierarchy and why `Map` is separate;
- ordering, duplicates, nulls, and mutability contracts;
- `ArrayList` versus `LinkedList` with real access patterns;
- hashing and the `equals`/`hashCode` contract;
- natural ordering and comparators;
- queues, deques, and priority queues;
- unmodifiable factories, views, and copies;
- concurrent collections and atomic compound operations;
- asymptotic complexity versus measured performance.

## Further Reading

- [Collections Framework overview](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/util/doc-files/coll-overview.html)
- [`Collection` API](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/util/Collection.html)
- [`Map` API](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/util/Map.html)
- [Concurrent collections](https://docs.oracle.com/en/java/javase/25/docs/api/java.base/java/util/concurrent/package-summary.html)

Return to the [Java Study Guide](./README.md).
