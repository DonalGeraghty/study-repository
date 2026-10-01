---
tags:
  - programming/data-structures-and-algorithms
---

# Complexity and Problem Solving

An algorithm needs both a correctness argument and a cost model. A fast answer to the wrong problem is still wrong; a correct solution that cannot handle the input size may also be unusable.

Start by defining the input, output and constraints. Clarify whether duplicates matter, whether input may be changed, what an empty input means and which resource is limited. A simple brute-force solution gives you a baseline to understand and test before optimising.

## Quick Refresh

Big O describes an asymptotic upper bound on growth, not an exact duration. A tight bound can be written with Theta, such as `Theta(n)` for a complete linear scan. Interview discussions often use Big O for the tightest useful bound, but the distinction matters.

| Growth | Typical example | Effect of doubling a large input |
| --- | --- | --- |
| `O(1)` | Indexed array access | Roughly unchanged work |
| `O(log n)` | Binary search | A small additional number of steps |
| `O(n)` | Full traversal | Roughly twice the work |
| `O(n log n)` | Efficient comparison sorting | Slightly more than twice |
| `O(n²)` | Comparing all pairs | Roughly four times |
| `O(2^n)` | Exploring every subset | Roughly squares the number of possibilities |

Worst-case analysis bounds the most expensive input of a given size. Average-case analysis needs assumptions about the input distribution. Amortised analysis spreads occasional expensive operations over a sequence, without assuming random input; dynamic-array append is the standard example.

State the model behind the claim. The examples here normally treat bounded-size numeric operations and comparisons as constant time. Hashing a long string or calculating with very large integers can add costs that this simplified model leaves out.

## Worked Example: Counting Unordered Pairs

Suppose every pair of distinct positions must be considered once. Starting the inner loop at `i + 1` avoids self-pairs and counting both `(i, j)` and `(j, i)`:

```python
def count_pairs(values):
    count = 0
    for i in range(len(values)):
        for j in range(i + 1, len(values)):
            count += 1
    return count


print(count_pairs([10, 20, 30, 40]))  # 6
```

The inner loop executes `3 + 2 + 1 + 0 = 6` times. In general there are `n(n - 1) / 2` iterations, giving `Theta(n²)` time and `O(1)` auxiliary space under the stated model. The function counts pairs without storing them; materialising all pairs would require quadratic output space.

If the task only asks for the count, the formula `n * (n - 1) // 2` avoids enumeration. If each pair needs a separate compatibility check, the formula does not do that work. Optimisation must preserve the actual requirement.

## Correctness and Space

An invariant explains what the algorithm has established so far. Here, after finishing outer index `i`, every pair whose first index is at most `i` has been counted exactly once. The loops terminate because their ranges are finite; after the final iteration, all valid pairs are covered.

Distinguish input storage, auxiliary working memory and returned output. A recursive algorithm also uses stack space, even if it allocates no explicit collection. Two solutions with the same time bound may differ greatly in memory, locality, constants and implementation clarity.

## Common Failure Modes

Count work rather than loop syntax. Two consecutive linear loops are still linear; nested loops can be linear overall if one pointer only advances `n` times in total. Conversely, a single visible loop that copies a growing list on every iteration can be quadratic.

Benchmarking helps compare real implementations, but does not replace analysis or prove correctness. Test small boundary cases and compare an optimised version with a simple reference implementation on many small inputs.

## Worked Prediction

How many pairs does the example count for five positions, and what changes if every pair is saved in a list?

**Check your reasoning:** It counts `5 × 4 / 2 = 10`. Saving all pairs leaves time quadratic and changes output storage to `Theta(n²)`. Empty and one-element inputs both produce zero pairs. Doubling from five to ten gives 45 pairs, not exactly 40: growth classes describe the trend, not an exact ratio for small inputs.

## Interview Questions

> [!question] Interview Questions
> - What does Big O describe, and why is it not a runtime measurement?
> - How does amortised analysis differ from average-case analysis?
> - Why is the pair-counting example quadratic even though the inner loop becomes shorter?
> - How would you check that an optimisation preserves correctness?

## Answer Notes

1. It bounds growth as input size increases under a stated cost model. Hardware, constants, data layout and the actual inputs still affect elapsed time.

2. Amortised analysis bounds a sequence of operations, including occasional expensive ones. Average-case analysis averages over an assumed distribution of inputs.

3. The total is `(n - 1) + ... + 1 = n(n - 1) / 2`, whose dominant term is quadratic. Shorter later iterations reduce the constant, not the growth class.

4. State the invariant and termination argument, test boundaries, and compare against a trusted simple solution on small cases. Verify that input mutation and output semantics have not changed.

## Further Reading

- [Princeton: Analysis of Algorithms](https://algs4.cs.princeton.edu/14analysis/)

## Related Guides

- [Arrays and Strings](./arrays-and-strings.md) — operation costs behind common loops.
- [Problem-Solving Techniques](./problem-solving-techniques.md) — why moving pointers can keep nested work linear.
- [Coding Challenges](../coding-challenges/README.md) — record reasoning and complexity with each solution.

Return to [Data Structures and Algorithms](./README.md).
