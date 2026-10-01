---
tags:
  - programming/data-structures-and-algorithms
---

# Arrays and Strings

An array stores a sequence in indexed positions, making access by position efficient. A dynamic array can grow by occasionally allocating more capacity and moving its contents. Python's `list` is a dynamic array of object references, not a linked list.

A string is a sequence of text units. Python strings are immutable and indexed by Unicode code point; one displayed character can contain several code points. Decide whether a task concerns bytes, code points or user-perceived characters before implementing text operations.

## Quick Refresh

For a Python list with `n` elements, typical operation costs are:

| Operation | Time | Reason |
| --- | --- | --- |
| Read or replace `items[i]` | `O(1)` | Direct indexed access |
| Scan for a value | `O(n)` worst case | May inspect every element |
| Append at the end | `O(1)` amortised | Occasional resizing costs `O(n)` |
| Insert or delete near the start | `O(n)` | Later references must move |
| Copy a slice of length `k` | `O(k)` | Creates a new list of references |

Indexed access does not make finding an unknown value constant time. Likewise, a slice is not a free view: list slices allocate another list. The objects inside a shallow copy can still be shared.

## Worked Example: Reverse a List In Place

Swap the two outer values, then move inward. At each step, positions outside the remaining interval already contain their final values:

```python
def reverse_in_place(items):
    left = 0
    right = len(items) - 1
    while left < right:
        items[left], items[right] = items[right], items[left]
        left += 1
        right -= 1


values = [2, 4, 6, 8, 10]
reverse_in_place(values)
print(values)  # [10, 8, 6, 4, 2]
```

The first swap produces `[10, 4, 6, 8, 2]`; the second produces the final result. The middle value stays in place. Each swap settles two positions, so time is `O(n)` and auxiliary space is `O(1)`.

The function mutates its argument and returns `None`. Empty and one-element lists require no swaps. A copied reversed list is a different contract because it preserves the original and uses `O(n)` output storage.

## Working with Strings

Strings cannot be reversed through indexed assignment. Building a new value is often the appropriate choice. When assembling many pieces, collect them and join once instead of relying on repeated concatenation to be efficient:

```python
parts = ["worker", "queue", "retry"]
label = " / ".join(parts)
print(label)  # worker / queue / retry
```

Account for the total text length when analysing string operations. Equality may inspect many code points, and changing case or normalising text can affect both meaning and length. An ASCII-only interview exercise should state that restriction rather than silently treating all text as ASCII.

## Common Failure Modes

Confusing an index with a value leads to incorrect bounds and updates. For a list of length `n`, valid non-negative indexes end at `n - 1`; an exclusive endpoint of `n` is useful for ranges, but is not an element index.

Aliasing can make mutation surprising. `other = values` shares the same list. Even `values.copy()` only copies the outer structure, so changes inside nested mutable objects remain visible through both copies.

Removing elements while traversing forwards can skip shifted values. Decide whether to construct a filtered result, write survivors into earlier positions, or traverse in a way that preserves the intended positions.

## Worked Prediction

Set `original = [[1], [2]]`, then `copied = original[:]`. Append `9` to `copied[0]`, then append `[3]` to `copied`. What do both lists contain?

**Check your reasoning:** `original` becomes `[[1, 9], [2]]`; `copied` becomes `[[1, 9], [2], [3]]`. The inner lists are shared, but the two outer lists are distinct. Replacing `copied[0]` with a new list would instead change only that outer list's reference.

## Interview Questions

> [!question] Interview Questions
> - Why is indexed array access fast while insertion at the front is usually linear?
> - How can append be amortised constant time when some appends copy the array?
> - What invariant makes the reversal example correct?
> - Why can a shallow copy fail to isolate changes to nested data?

## Answer Notes

1. The index locates a position directly. Inserting at the front must shift existing elements to preserve their order.

2. A dynamic array grows capacity in larger steps, so many cheap appends occur between expensive resizes. The total cost across a growing sequence is linear, giving constant amortised cost per append.

3. Positions outside the active interval are already correct. Each swap places both endpoints correctly and shrinks the interval until no pair remains.

4. It copies the outer references, not the referenced objects. Choose deeper copying or immutable data where nested independence is required.

## Official References

- [Python: Data Structures](https://docs.python.org/3/tutorial/datastructures.html)

## Related Guides

- [Linked Lists](./linked-lists.md) — compare indexed storage with node traversal.
- [Problem-Solving Techniques](./problem-solving-techniques.md) — use sequence structure to avoid repeated work.
- [Python](../languages/python.md) — language-level identity and mutation.

Return to [Data Structures and Algorithms](./README.md).
