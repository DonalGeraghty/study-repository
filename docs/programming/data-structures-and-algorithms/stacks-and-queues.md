---
tags:
  - programming/data-structures-and-algorithms
---

# Stacks and Queues

Stacks and queues define the order in which stored work is retrieved. A stack is last-in, first-out (LIFO); a queue is first-in, first-out (FIFO). These are behaviours, not commitments to one underlying storage layout.

A stack fits nested work, undo operations and depth-first exploration. A queue fits work handled in arrival order and breadth-first exploration. Neither structure by itself supplies persistence, thread coordination or the delivery guarantees of a message broker.

## Quick Refresh

| Structure | Add | Remove | Python choice |
| --- | --- | --- | --- |
| Stack | Top | Top | `list.append()` and `list.pop()` |
| Queue | Back | Front | `deque.append()` and `deque.popleft()` |
| Deque | Either end | Either end | `collections.deque` |

List append and removal from the end are amortised constant-time operations. A deque supports approximately constant-time operations at either end. Repeated `list.pop(0)` shifts the remaining references, so draining a large list that way can take quadratic time. [Python deque documentation](https://docs.python.org/3/library/collections.html#collections.deque).

## Worked Example: Matching Brackets

A closing bracket must match the most recently opened bracket that has not yet closed. That is precisely a stack rule. This function checks `()`, `[]` and `{}`, ignoring other characters:

```python
def brackets_balanced(text):
    opening = set("([{")
    expected = {")": "(", "]": "[", "}": "{"}
    stack = []

    for char in text:
        if char in opening:
            stack.append(char)
        elif char in expected:
            if not stack or stack.pop() != expected[char]:
                return False
    return not stack


print(brackets_balanced("a[()]"))  # True
print(brackets_balanced("([)]"))   # False
```

After scanning any prefix without a mismatch, the stack contains its unmatched opening brackets in order. For `([)]`, the `)` encounters `[` on top, so the nesting is invalid even though the counts of each bracket type match.

The scan takes `O(n)` time and up to `O(n)` space when all characters are opening brackets. Empty input returns `True`. This is a bracket exercise, not a programming-language parser: quotes, escapes and comments would need additional rules.

## Worked Example: FIFO Processing

```python
from collections import deque

pending = deque(["build", "test"])
pending.append("package")
print(pending.popleft())  # build
print(list(pending))      # ['test', 'package']
```

The next task comes from the oldest end. If tasks were removed from the same end used to append them, the behaviour would be a stack instead. A priority queue is different again: it selects by priority, not simply arrival order.

## Common Failure Modes

Checking only bracket totals misses invalid ordering. Finishing a scan without an early mismatch also does not prove success: unmatched openers must leave a non-empty stack and produce failure.

Handle empty removal deliberately. In real worker systems, decide what happens when the producer is faster than the consumer: unlimited buffering can exhaust memory, while a bounded queue needs a blocking, rejection or shedding policy. A bounded deque that drops old entries is not a substitute for reliable job delivery.

## Worked Prediction

Push `A`, `B` and `C` into a stack and enqueue the same values into a queue. Remove one value, add `D`, then drain each structure. What is the complete removal order?

**Check your reasoning:** The stack removes `C, D, B, A`; the queue removes `A, B, C, D`. Adding `D` gives it immediate priority only in the stack. Separately, `brackets_balanced("(()")` is `False` because an opener remains unmatched.

## Interview Questions

> [!question] Interview Questions
> - What distinguishes a stack, a queue and a priority queue?
> - Why does matching nested brackets require more than counting openers and closers?
> - Why is a deque a better FIFO choice than repeatedly removing index zero from a Python list?
> - What must a worker system decide when its queue grows faster than it can drain?

## Answer Notes

1. A stack retrieves the most recently added item; a queue retrieves the oldest. A priority queue retrieves according to an ordering key, with tie behaviour determined separately.

2. The latest unmatched opener determines the valid closer. Equal totals can still hide crossed or reversed nesting.

3. Deque endpoint removal avoids shifting the remaining sequence. Each list removal at zero is linear in the number of remaining items.

4. It needs a capacity and overload policy, plus a decision about durability and retries. Growing memory without a bound is not a sustainable solution.

## Further Reading

- [Princeton: Stacks and Queues](https://algs4.cs.princeton.edu/13stacks/)

## Related Guides

- [Linked Lists](./linked-lists.md) — one possible storage representation.
- [Graphs](./graphs.md) — queue-based BFS and stack-based DFS.
- [Trees and Heaps](./trees-and-heaps.md) — priority-based retrieval.
- [RabbitMQ](../../platform-engineering/rabbitmq.md) — broker concerns beyond an in-memory queue.

Return to [Data Structures and Algorithms](./README.md).
