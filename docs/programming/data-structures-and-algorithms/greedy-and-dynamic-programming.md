---
tags:
  - programming/data-structures-and-algorithms
---

# Greedy Algorithms and Dynamic Programming

Greedy algorithms commit to a locally attractive choice without exploring every future consequence. Dynamic programming (DP) solves defined subproblems and reuses their answers. The important distinction is what proves the choice or recurrence correct, rather than whether the implementation has a loop or recursion.

## Quick Refresh

| Approach | What must be justified | Typical cost concern |
| --- | --- | --- |
| Greedy | A local choice can belong to an optimal complete solution | Often sorting plus a scan |
| Memoisation | Repeated calls with the same state have the same result | Number of reached states, transitions and recursion depth |
| Tabulation | Dependencies are ready before a state is computed | Number of table entries and work per entry |

Memoisation is top-down: compute a state when requested and cache it. Tabulation is bottom-up: fill states in dependency order. DP works when the chosen state captures everything relevant to the remaining decision and smaller answers can form larger ones; overlapping subproblems make reuse valuable.

## Worked Example: Greedy Interval Selection

Choose as many non-overlapping intervals as possible on one resource. Intervals are half-open `[start, end)`, must have positive duration, and all have equal value. Sort by finishing time, then accept each interval that starts after or at the previous accepted finish:

```python
def select_intervals(intervals):
    if any(start >= end for start, end in intervals):
        raise ValueError("intervals must have positive duration")
    selected = []
    finish = None
    for start, end in sorted(intervals, key=lambda item: item[1]):
        if finish is None or start >= finish:
            selected.append((start, end))
            finish = end
    return selected


print(select_intervals([(1, 3), (2, 5), (3, 4), (4, 7)]))
# [(1, 3), (3, 4), (4, 7)]
```

The earliest-finishing interval leaves at least as much room for later work as any alternative first interval. Replacing the first interval of an optimal solution with this choice does not reduce how many later intervals fit. Repeating that exchange argument justifies the greedy choice.

Sorting takes `O(n log n)` time and the scan is linear. This implementation uses `O(n)` space for the sorted copy and selected output. If intervals carry different rewards, maximising count no longer solves the desired problem; the greedy proof does not carry over automatically.

## Worked Example: Minimum Coins with DP

For denominations `[1, 3, 4]` and amount `6`, taking the largest available coin first produces `4 + 1 + 1`. The better answer is `3 + 3`, so greedy change-making is not correct for arbitrary denominations.

Assume non-negative integer amounts, positive integer denominations and unlimited reuse of each denomination. Define `best[a]` as the minimum number of coins needed for amount `a`. Set `best[0] = 0`, then try every possible final coin:

```python
def min_coins(amount, coins):
    if amount < 0 or any(coin <= 0 for coin in coins):
        raise ValueError("amount must be non-negative and coins positive")
    unreachable = amount + 1
    best = [unreachable] * (amount + 1)
    best[0] = 0

    for subtotal in range(1, amount + 1):
        for coin in coins:
            if coin <= subtotal:
                best[subtotal] = min(best[subtotal], best[subtotal - coin] + 1)

    return None if best[amount] == unreachable else best[amount]


print(min_coins(6, [1, 3, 4]))  # 2
print(min_coins(3, [2]))        # None
```

Every valid solution has some final coin. Removing it leaves a smaller amount, whose optimal answer is already available. Taking the minimum over those final choices therefore covers every possible optimum. Positive coins ensure the dependency always points to a smaller subtotal.

For amount 6, the table is `[0, 1, 2, 1, 1, 2, 2]`. Amount zero needs no coins even with an empty denomination list. Positive amounts with no usable combination return `None`, rather than confusing impossibility with zero coins.

## State, Cost and Reconstruction

For amount `A` and `k` denominations, the DP uses `O(Ak)` time and `O(A)` space under the numeric cost model. This is pseudo-polynomial: it depends on the amount's numeric value, which can be huge compared with the number of digits needed to represent it.

The table returns a count, not the coins themselves. To reconstruct a solution, also record which final coin achieved each best value, then walk backwards from the requested amount. That is a different output requirement, even when the optimisation recurrence stays the same.

## Common Failure Modes

“The biggest choice seems best” is not a proof. Look for an exchange argument or another reason the local choice cannot exclude an optimum, and try to construct a counterexample before trusting it.

A memoisation key must include all state affecting the answer. If coins had limited quantities, the remaining amount alone would no longer describe the entire problem. Likewise, recording every subproblem does not rescue an incorrect recurrence or an incorrect base case.

## Worked Prediction

What do greedy largest-coin-first and the DP return for amount `6` with `[1, 3, 4]`? What should the DP return for amount `0` with no denominations?

**Check your reasoning:** Greedy uses three coins; the DP finds two. The zero-amount case returns `0` because choosing no coins is a valid solution. With amount `3` and only coin `2`, the DP returns `None` because the state remains unreachable.

## Interview Questions

> [!question] Interview Questions
> - What proves earliest-finish interval selection correct for maximising the number of intervals?
> - Why is choosing the largest coin first not generally correct?
> - How do memoisation and tabulation differ while solving the same recurrence?
> - How would you derive the time and space costs of the coin-change DP?

## Answer Notes

1. An optimal solution's first interval can be replaced with the earliest-finishing one without reducing room for later intervals. Reapply that argument to the remaining compatible intervals.

2. A large coin can leave an expensive remainder. For amount 6 with coins 1, 3 and 4, three greedy coins lose to two coins of value 3.

3. Memoisation computes requested states recursively and caches them; tabulation fills a chosen dependency order iteratively. Both need a sufficient state definition, valid base cases and correct transitions.

4. There are `A + 1` states and at most `k` transitions per positive state, giving `O(Ak)` time. The one-dimensional table uses `O(A)` space; numeric input size must be considered before allocating it.

## Further Reading

- [MIT: Algorithm Lecture Notes, including dynamic programming](https://ocw.mit.edu/courses/6-006-introduction-to-algorithms-spring-2020/pages/lecture-notes/)

## Related Guides

- [Recursion and Backtracking](./recursion-and-backtracking.md) — recognise repeated states during exploration.
- [Searching and Sorting](./searching-and-sorting.md) — ordering can expose a valid greedy choice.
- [Complexity and Problem Solving](./complexity-and-problem-solving.md) — define the input size and cost model.

Return to [Data Structures and Algorithms](./README.md).
