---
tags:
  - programming/data-structures-and-algorithms
  - moc
---

# Data Structures and Algorithms

Data structures organise information; algorithms describe how to work with it. Learn them together so you can explain why a solution is correct, what it costs and which constraints would change your choice.

These guides use short explanations, small Python examples and worked predictions. The concepts transfer between languages; operation costs still depend on the chosen implementation. Examples use Python 3.10 or later and the standard library, with no additional packages required.

## Learning Order

| Order | Guide | Focus |
| --- | --- | --- |
| 1 | [Complexity and Problem Solving](./complexity-and-problem-solving.md) | Constraints, correctness, Big O and time-space trade-offs |
| 2 | [Arrays and Strings](./arrays-and-strings.md) | Indexing, traversal, dynamic arrays, mutation and text |
| 3 | [Linked Lists](./linked-lists.md) | Nodes, links, insertion, deletion and reversal |
| 4 | [Stacks and Queues](./stacks-and-queues.md) | LIFO, FIFO, deques and bracket matching |
| 5 | [Hash Tables and Sets](./hash-tables-and-sets.md) | Keys, collisions, equality and efficient lookup |
| 6 | [Searching and Sorting](./searching-and-sorting.md) | Binary search, insertion sort, merge sort and quicksort |
| 7 | [Recursion and Backtracking](./recursion-and-backtracking.md) | Base cases, call stacks and exploring choices |
| 8 | [Trees and Heaps](./trees-and-heaps.md) | Search trees, traversals, balance and priority queues |
| 9 | [Graphs](./graphs.md) | Representation, BFS, DFS, cycles and shortest paths |
| 10 | [Problem-Solving Techniques](./problem-solving-techniques.md) | Two pointers, sliding windows and prefix sums |
| 11 | [Greedy Algorithms and Dynamic Programming](./greedy-and-dynamic-programming.md) | Proving choices, defining states and reusing subproblems |

Recursion appears before trees so recursive traversals have a familiar mechanism. The techniques guide can also be read after arrays and hash tables if you want more sequence practice before continuing.

## How to Study

Read the mental model, then trace an example on paper before running it. Name the invariant—the fact that remains true as the algorithm progresses—and explain why it gives the correct result when the algorithm stops.

Change one constraint: allow duplicates, remove sorted order, introduce negative values or require less memory. Check whether the reasoning still works. Each guide ends with four interview questions and matching answer notes; attempt the questions before reading the notes.

Use [Coding Challenges](../coding-challenges/README.md) to record individual solutions and later reattempts. Keep the reusable concepts here. The [Java Collections guide](../languages/java/java-collections.md) maps several of these ideas to concrete Java APIs, while [Python](../languages/python.md) explains the language used in the examples.

Return to [Programming](../README.md).
