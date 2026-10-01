---
tags:
  - programming/data-structures-and-algorithms
---

# Searching and Sorting

Searching finds an item or a boundary; sorting establishes an order that other algorithms can exploit. Start by asking whether input is already sorted, whether it may be mutated, and how many searches must be performed.

Linear search works on unsorted input in `O(n)` worst-case time. Binary search repeatedly halves an ordered search interval, taking `O(log n)` comparisons when indexed access is constant time. Sorting solely for one lookup usually costs more than scanning once.

## Quick Refresh

| Sort | Typical time | Worst time | Usual array implementation |
| --- | --- | --- | --- |
| Insertion sort | `O(n²)` average; `O(n)` best | `O(n²)` | In-place; stable with strict shifting |
| Merge sort | `O(n log n)` | `O(n log n)` | `O(n)` extra storage; can be stable |
| Quicksort | Expected `O(n log n)` with suitable pivots | `O(n²)` | Commonly in-place partitioning; usually unstable |

A stable sort preserves the original order of equal keys. In-place describes storage, not stability. Quicksort also uses call-stack space: a straightforward recursive implementation can need linear depth in its worst case.

## Worked Example: Find the First Matching Value

This implementation returns the first matching index in an ascending sorted sequence, or `-1`. It first finds the lower bound: the earliest position whose value is not less than the target.

```python
def first_index(values, target):
    low = 0
    high = len(values)
    while low < high:
        middle = (low + high) // 2
        if values[middle] < target:
            low = middle + 1
        else:
            high = middle
    if low < len(values) and values[low] == target:
        return low
    return -1


print(first_index([1, 2, 2, 2, 5], 2))  # 1
print(first_index([1, 2, 2, 2, 5], 4))  # -1
```

Every index below `low` contains a value smaller than the target; every index at or above `high` contains a value at least as large. The half-open interval `[low, high)` contains the remaining uncertainty. Both branches shrink it, and equality moves the right boundary left to keep searching for the first occurrence.

Time is `O(log n)` and auxiliary space is `O(1)`. Empty input skips the loop and returns `-1`. Python's `bisect_left` supplies the lower-bound operation in library code, but the final equality check is still needed when asking whether the target exists. [Bisection documentation](https://docs.python.org/3/library/bisect.html).

## Worked Example: Insertion Sort

Maintain a sorted prefix and insert each next value by shifting larger values right:

```python
def insertion_sort(values):
    for index in range(1, len(values)):
        value = values[index]
        position = index
        while position > 0 and values[position - 1] > value:
            values[position] = values[position - 1]
            position -= 1
        values[position] = value


items = [4, 2, 3]
insertion_sort(items)
print(items)  # [2, 3, 4]
```

After inserting `2`, the prefix is `[2, 4]`; inserting `3` shifts `4` once. The strict `>` comparison preserves the order of equal values. Reverse-sorted input requires roughly `n(n - 1) / 2` shifts, while already sorted input requires only one unsuccessful comparison per outer iteration.

## Merge Sort and Quicksort

Merge sort divides the sequence into smaller pieces, sorts them and merges sorted pieces by repeatedly taking the smaller front value. Merging `[1, 4]` and `[2, 3]` produces `1, 2, 3, 4`. Linear work at each of logarithmically many levels gives `O(n log n)` time; choosing from the left on ties preserves stability. [Merge sort](https://algs4.cs.princeton.edu/22mergesort/).

Quicksort partitions around a pivot so smaller and larger regions can be sorted independently. Well-balanced partitions give logarithmic depth, but repeatedly producing an empty region and a region of size `n - 1` gives quadratic work. Randomisation reduces the likelihood of persistently bad pivots; it does not remove the worst-case bound. [Quicksort](https://algs4.cs.princeton.edu/23quicksort/).

## Common Failure Modes

Binary search requires the ordering or monotonic condition used by its branch decision. Applying it to unsorted data can return a plausible wrong result. Mixing inclusive and exclusive endpoints can skip values or prevent termination.

In application code, normally use the language's sorting library instead of maintaining a custom sort. Python's `sorted()` returns a new list, while `list.sort()` changes the existing list and returns `None`; both are stable. Choose keys deliberately and account for their evaluation costs. [Python sorting guide](https://docs.python.org/3/howto/sorting.html).

## Worked Prediction

What does `first_index([2, 2, 2], 2)` return? If insertion sort used `>=` instead of `>` when shifting, would it still be stable?

**Check your reasoning:** Binary search returns `0` because equality keeps moving `high` left. Insertion sort with `>=` can move a later equal-key item ahead of an earlier one, so stability is lost even though the keys still end up sorted.

## Interview Questions

> [!question] Interview Questions
> - Why might linear search be preferable to sorting followed by binary search?
> - What invariant lets this binary search find the first duplicate safely?
> - How do merge sort and quicksort differ in worst-case behaviour and storage?
> - What is sorting stability, and how does insertion sort preserve it here?

## Answer Notes

1. One scan costs `O(n)`, while comparison sorting normally costs `O(n log n)` before the lookup. Repeated searches or already sorted input can change the trade-off.

2. Values below `low` are too small and those at or above `high` are large enough. Moving `high` on equality preserves earlier candidates until the boundaries meet.

3. Standard array merge sort guarantees `O(n log n)` time using linear auxiliary storage. Quicksort commonly partitions in place but has quadratic worst-case time and recursion-space costs.

4. Stability retains the input order of equal keys. Shifting only strictly greater values leaves earlier equal values before the newly inserted one.

## Related Guides

- [Arrays and Strings](./arrays-and-strings.md) — indexing and mutation costs.
- [Recursion and Backtracking](./recursion-and-backtracking.md) — recursive decomposition and stack depth.
- [Trees and Heaps](./trees-and-heaps.md) — maintain order or priorities without repeatedly sorting everything.

Return to [Data Structures and Algorithms](./README.md).
