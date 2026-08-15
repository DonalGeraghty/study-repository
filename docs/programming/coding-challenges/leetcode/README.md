# LeetCode

This page records LeetCode problems after they have been completed as deliberate practice.

## Personal Completion Log

No independently completed problems have been recorded yet. The worked example below demonstrates the note format but does not count as a completed session.

## Worked Format Example

This example demonstrates the expected level of reasoning. Keep it separate from the personal completion log until the problem has been solved independently.

### Two Sum

- **Problem:** [Two Sum](https://leetcode.com/problems/two-sum/)
- **Difficulty:** Easy
- **Language:** Java
- **Topics:** arrays, hash maps

#### Problem Summary

Find the indexes of two distinct array elements whose values add to a target. Assume the input contract supplies one valid pair.

#### Approach

A nested-loop solution checks every pair in `O(n²)` time. Instead, scan once and store each value's index. Before storing the current value, look for the complement `target - value`. Looking first prevents one array element from being used twice and still handles duplicate values such as `[3, 3]`.

#### Complexity

- **Time:** `O(n)` expected time for `n` hash-map operations.
- **Space:** `O(n)` in the worst case.

#### Solution

```java
import java.util.HashMap;
import java.util.Map;

final class TwoSum {
    static int[] findPair(int[] values, int target) {
        Map<Integer, Integer> previousIndexes = new HashMap<>();

        for (int index = 0; index < values.length; index++) {
            int complement = target - values[index];
            Integer previousIndex = previousIndexes.get(complement);

            if (previousIndex != null) {
                return new int[] {previousIndex, index};
            }

            previousIndexes.put(values[index], index);
        }

        throw new IllegalArgumentException("no pair adds to the target");
    }
}
```

#### Lessons

- State the brute-force approach before optimising it.
- A hash map exchanges memory for faster lookup.
- Operation order is part of correctness when an element cannot match itself.
- Test negative numbers, duplicate values, and a pair near the end.

## Problem Entry Template

```markdown
## Problem Title

- **Problem:** [Original problem](https://leetcode.com/problems/example/)
- **Difficulty:** Easy, Medium, or Hard
- **Language:** Java, Kotlin, Python, or another language
- **Topics:** Arrays, hashing, graphs, dynamic programming, or other topics

### Problem Summary

Describe the task and important constraints in your own words.

### Approach

Explain the reasoning, alternatives considered, and why the selected approach works.

### Complexity

- **Time:** `O(...)`
- **Space:** `O(...)`

### Solution

Add the implementation in a language-labelled code block.

### Lessons

Record edge cases, mistakes, alternative solutions, and reusable patterns.
```

Return to [Coding Challenges](../README.md).
