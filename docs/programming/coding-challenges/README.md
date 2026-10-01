---
tags:
  - programming/coding-challenges
  - moc
---

# Coding Challenges

This section records solved programming problems, reasoning approaches, complexity analysis, and lessons that transfer beyond an individual exercise.

Use [Data Structures and Algorithms](../data-structures-and-algorithms/README.md) for the underlying concepts and worked examples. Start with [complexity and problem solving](../data-structures-and-algorithms/complexity-and-problem-solving.md), then use the relevant structure or technique guide before attempting a challenge. Keep problem-specific solutions and reattempt notes here.

## Platforms

- [LeetCode](./leetcode/README.md) — algorithm and data-structure problems from LeetCode.
- [Codewars](./codewars/README.md) — kata and language-practice exercises from Codewars.

## What to Record

For each problem, capture:

1. the problem statement in your own words;
2. important constraints and edge cases;
3. the initial or brute-force approach;
4. the improved approach and why it works;
5. time and space complexity;
6. the implementation language;
7. mistakes, alternatives, and reusable lessons.

Avoid copying full copyrighted problem statements. Link to the original problem and summarise only the details needed to understand the solution.

## Reattempts and Transfer

Before viewing an existing solution, restate its invariant and work a tiny example by hand. Write the simplest correct approach, then justify the optimisation and its complexity. After checking the result, record the exact misconception rather than just whether the platform accepted it.

Reattempt the problem in a later session without opening the solution. Change one constraint, such as duplicate inputs, an empty collection, bounded memory, or streamed input, and explain whether the same approach still works. The worked entries in the platform indexes demonstrate format; they are not evidence that you personally solved the problem.

For example, trace Two Sum on `[3, 3]` with target `6`: checking for a previous complement before inserting the current index yields two distinct positions. Inserting first can mistakenly match an element with itself. Being able to explain that order is stronger evidence of learning than remembering the map-based code.

Return to [Programming](../README.md).
