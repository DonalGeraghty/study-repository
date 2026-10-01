---
tags:
  - programming/data-structures-and-algorithms
---

# Problem-Solving Techniques

Two pointers, sliding windows and prefix sums avoid repeating work on sequences. They are useful because of an invariant, not because a problem contains an array. Identify what information can be safely discarded or reused before choosing a technique.

## Quick Refresh

| Technique | Useful structure | Main idea |
| --- | --- | --- |
| Two pointers | Often sorted data or opposing sequence boundaries | Move a boundary only when its discarded candidates cannot help |
| Sliding window | Contiguous ranges with an incrementally maintained property | Add at one end and remove at the other |
| Prefix sums | Repeated range-sum queries on unchanged data | Subtract cumulative totals |

Two moving boundaries do not automatically imply quadratic time. If each only moves forward through `n` positions, their total movement is linear. Work performed at each movement must still be counted.

## Worked Example: A Pair in Sorted Data

For ascending sorted integers, compare the smallest and largest remaining values. If their sum is too small, keeping that left value and moving the right boundary left cannot improve it. The symmetric argument applies when the sum is too large:

```python
def sorted_pair(values, target):
    left = 0
    right = len(values) - 1
    while left < right:
        total = values[left] + values[right]
        if total == target:
            return (left, right)
        if total < target:
            left += 1
        else:
            right -= 1
    return None


print(sorted_pair([-2, 1, 4, 7, 9], 8))  # (1, 3)
```

Each step discards an endpoint that cannot participate in a solution within the remaining interval. Time is `O(n)` with `O(1)` auxiliary space. If input must first be sorted, include that cost and remember that original indexes are lost unless tracked separately. The hash-table approach handles unsorted input with a different memory trade-off.

## Worked Example: A Variable-Size Window

Find the longest contiguous range whose sum is at most a non-negative budget. The following algorithm requires non-negative values: extending a window cannot reduce its sum, and removing a value cannot increase it.

```python
def longest_within_budget(values, budget):
    if budget < 0 or any(value < 0 for value in values):
        raise ValueError("values and budget must be non-negative")
    left = 0
    total = 0
    best = 0
    for right, value in enumerate(values):
        total += value
        while total > budget:
            total -= values[left]
            left += 1
        best = max(best, right - left + 1)
    return best


print(longest_within_budget([1, 2, 1, 1, 3], 4))  # 3
```

After shrinking, the current window is valid. A previously discarded left boundary cannot become useful by appending more non-negative values. Each value enters and leaves at most once, so the nested loop still gives `O(n)` time and `O(1)` auxiliary space. Empty input returns zero; zeros are permitted.

For a fixed-width window, initialise the first total, then add the incoming value and subtract the outgoing one. This reduces repeated summation from `O(nk)` to `O(n)` for width `k`, and does not require non-negative values because the boundaries are fixed by position rather than a sum condition.

## Worked Example: Prefix Sums

Let `prefix[i]` mean the sum of values before index `i`. The initial zero makes a range starting at index zero follow the same rule as every other range:

```python
def build_prefix(values):
    prefix = [0]
    for value in values:
        prefix.append(prefix[-1] + value)
    return prefix


prefix = build_prefix([2, -1, 5, 3])
left, right = 1, 3
print(prefix[right] - prefix[left])  # 4: sum of indexes [1, 3)
```

Preprocessing takes `O(n)` time and space, then each valid half-open range query `[left, right)` takes `O(1)`. Negative values work because subtraction cancels the common prefix. If the original data changes, stored prefix sums become stale and may need rebuilding or a different data structure.

## Common Failure Modes

Sliding windows rely on the particular property being maintained. Negative values break the shrinking rule above: a currently excessive sum could later become valid without moving the left boundary. Other window tasks, such as maintaining character counts, have their own conditions.

Mixing inclusive and exclusive boundaries gives off-by-one errors. A window ending at inclusive `right` has length `right - left + 1`; a prefix-sum query ending at exclusive `right` has length `right - left`.

## Worked Prediction

If the non-negative validation were removed, would the window algorithm find the optimal length for `[4, -3, 2]` with budget `3`?

**Check your reasoning:** No. It discards `4` immediately and reports length 2, although the whole sequence sums to 3 and has length 3. The published function rejects this input rather than claiming a wrong answer. Prefix sums can represent these sums correctly, but finding the best range still requires an algorithm suited to negative values.

## Interview Questions

> [!question] Interview Questions
> - Why can the sorted-pair algorithm safely discard an endpoint?
> - Why is the window example linear despite its nested loop?
> - Why do negative values break this variable-window rule but not prefix sums?
> - What trade-off makes prefix sums useful for repeated queries?

## Answer Notes

1. Sorted order proves that every remaining partner for that endpoint also fails in the same direction. That argument is unavailable on unsorted input.

2. Each value is added once and removed at most once. The inner loop's work is bounded across the entire run, not repeated from scratch for every right endpoint.

3. Negatives can make an invalid window valid later, invalidating an irreversible discard. Prefix subtraction is an algebraic identity and does not require monotonic sums.

4. Linear preprocessing and storage make each later range sum constant time. Updates undermine that benefit because many stored totals can become stale.

## Further Reading

- [MIT: Algorithm Lecture Notes](https://ocw.mit.edu/courses/6-006-introduction-to-algorithms-spring-2020/pages/lecture-notes/)

## Related Guides

- [Arrays and Strings](./arrays-and-strings.md) — positions, ranges and mutation.
- [Hash Tables and Sets](./hash-tables-and-sets.md) — an alternative pair lookup on unsorted input.
- [Complexity and Problem Solving](./complexity-and-problem-solving.md) — count total work and state assumptions.

Return to [Data Structures and Algorithms](./README.md).
