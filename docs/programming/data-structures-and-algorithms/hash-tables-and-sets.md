---
tags:
  - programming/data-structures-and-algorithms
---

# Hash Tables and Sets

A hash table maps a key to a storage location using a hash, then checks equality to identify the actual key. A map associates keys with values; a set records membership without a separate value. Python's `dict` and `set` provide these behaviours.

Hashing is useful when the important question is “have I seen this?” or “what belongs to this key?” It does not inherently arrange keys in sorted order or support efficient range queries.

## Quick Refresh

| Operation | Typical hash-table cost | Caveat |
| --- | --- | --- |
| Membership or lookup | Expected `O(1)` | Collisions and key costs matter |
| Insert or delete | Expected/amortised `O(1)` | Resizing occasionally moves many entries |
| Visit all entries | `O(n)` in the usual model | Cannot inspect all values in constant time |
| Storage | `O(n)` | Extra capacity supports efficient lookup |

These estimates assume suitable hashing, controlled load and bounded-size keys. A basic hash table can degrade to linear lookup under heavy collisions. Computing or comparing a long key also has a cost; expected constant-time lookup is not a promise that key processing is free.

## Collisions, Equality and Load

A collision occurs when different keys map to the same slot or bucket. Separate chaining keeps entries together in a bucket; open addressing probes other positions in the table. Neither may treat a matching hash as proof of equal keys.

Equal keys must have equal hashes, and the equality-relevant state of a stored key must remain stable. Otherwise a later lookup can search a different place or fail to recognise the entry. As the table fills, collision handling becomes more expensive; resizing restores room at the cost of occasional bulk work. [Princeton's hash-table explanation](https://algs4.cs.princeton.edu/34hash/).

In Python, lists are not hashable keys. A tuple is usable only if its elements are hashable too. A dictionary preserves insertion order, but that is not sorted-key order; a set should not be used as an ordering contract.

## Worked Example: Find a Pair by Its Complement

Given integers and a target, return two distinct positions whose values sum to the target, or `None` if no pair exists. The map stores previously visited values:

```python
def two_sum(values, target):
    seen = {}
    for index, value in enumerate(values):
        complement = target - value
        if complement in seen:
            return (seen[complement], index)
        seen[value] = index
    return None


print(two_sum([8, 3, 6, 4], 10))  # (2, 3)
print(two_sum([3, 3], 6))         # (0, 1)
```

Before each iteration, the map contains only earlier positions. Checking before inserting therefore prevents using the current position twice. A repeated value can overwrite an earlier stored position without invalidating the requirement to return any one pair.

The algorithm takes expected `O(n)` time and `O(n)` extra space for bounded-size integers. Comparing every pair takes quadratic time but constant auxiliary space. If the requirement changes to returning every pair or choosing a particular ordering, this implementation needs a new contract and potentially a different storage policy.

## Counting and Deduplication

A set is enough when only existence matters. A frequency map is needed when counts matter, such as checking whether two inputs contain the same values with the same multiplicities. Converting both inputs to sets would erase those multiplicities.

Preserving the first occurrence order while deduplicating usually means keeping a result sequence alongside a seen set. The structure that answers membership need not also own presentation order.

## Common Failure Modes

Inserting before checking the complement can incorrectly match an element with itself. Treating lookup as guaranteed constant time hides collision and key-size assumptions, while using a set where counts matter can silently change the problem.

Hash-table hashes are also different from password hashing or cryptographic integrity checks. The data-structure problem is locating entries efficiently, not protecting secrets.

## Worked Prediction

What does `two_sum([3], 6)` return? What goes wrong if the current value is inserted before looking for its complement?

**Check your reasoning:** The correct function returns `None`. Inserting first would allow the current index to match itself and produce `(0, 0)`, violating the distinct-position requirement. With `[3, 3]`, checking first correctly finds the earlier position on the second iteration.

## Interview Questions

> [!question] Interview Questions
> - Why must a hash table check equality even when hashes match?
> - When does a set lose information that a frequency map preserves?
> - Why does the two-sum example check before inserting?
> - What assumptions are hidden in an expected constant-time lookup claim?

## Answer Notes

1. Different keys can collide. Equality identifies the actual key among entries sharing a hash location.

2. A set stores membership only, so duplicate counts disappear. A frequency map retains the number of occurrences of each key.

3. The map must contain only earlier positions. That guarantees a match uses two distinct input positions, including when the values are equal.

4. Hash distribution, table load and key hashing/equality costs must be suitable. Individual resizes and bad collision patterns can cost more.

## Official References

- [Python: Dictionaries and Sets](https://docs.python.org/3/tutorial/datastructures.html)

## Related Guides

- [Complexity and Problem Solving](./complexity-and-problem-solving.md) — distinguish expected, amortised and worst-case costs.
- [Java Collections](../languages/java/java-collections.md) — map contracts and stable keys in Java.
- [Hashing](../../engineering-foundations/hashing.md) — the separate security uses of hashes.

Return to [Data Structures and Algorithms](./README.md).
