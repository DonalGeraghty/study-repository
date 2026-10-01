---
tags:
  - programming/data-structures-and-algorithms
---

# Linked Lists

A linked list represents a sequence as nodes connected by references. The head points to the first node, and a singly linked node points to its successor. Reaching position `i` requires following links rather than calculating an array offset.

```text
head -> [4 | next] -> [7 | next] -> [9 | None]
```

## Quick Refresh

| Operation | Singly linked list | Important assumption |
| --- | --- | --- |
| Read by index or find a value | `O(n)` | Traverse from the head |
| Prepend | `O(1)` | Update the new node and head |
| Insert after a known node | `O(1)` | The node has already been found |
| Remove a known node | `O(1)` if its predecessor is known | Otherwise finding the predecessor can take `O(n)` |
| Append | `O(1)` with a tail reference | Otherwise traverse to the end |

A doubly linked list stores both previous and next references. That makes removal of a known node straightforward, but adds memory and more links to maintain. Neither form guarantees that an arbitrary position can be found quickly.

Linked lists can fit frequent local changes when node references are already available. Arrays often provide better locality and less per-element overhead, so “many insertions” alone is not enough reason to choose a linked list.

## Worked Example: Reverse the Links

The following complete example assumes a finite, acyclic singly linked list. Reversal reuses its nodes and returns the new head:

```python
class Node:
    def __init__(self, value, next_node=None):
        self.value = value
        self.next = next_node


def reverse_list(head):
    previous = None
    current = head
    while current is not None:
        following = current.next
        current.next = previous
        previous = current
        current = following
    return previous


def to_list(head):
    values = []
    while head is not None:
        values.append(head.value)
        head = head.next
    return values


head = Node(4, Node(7, Node(9)))
head = reverse_list(head)
print(to_list(head))  # [9, 7, 4]
```

At the start of each iteration, `previous` heads the reversed prefix and `current` heads the remaining suffix. Saving `following` preserves access to the suffix before changing the link. Moving both references grows the reversed prefix by one node.

Reversal visits each node once, taking `O(n)` time and `O(1)` auxiliary space. The separate `to_list` helper takes linear time and creates linear output storage; it is for observing the result, not part of reversal's space bound.

## Insertion, Deletion and Cycles

To insert `new` after a known node, first set `new.next` to that node's successor, then set the node's `next` to `new`. To delete the successor, redirect the node's link to the successor's successor. Check whether that successor exists, and update a stored tail when necessary.

Cycles break the assumption that traversal eventually reaches `None`. A slow pointer moving one link and a fast pointer moving two can detect a cycle when they meet, using constant extra space. An acyclic list eventually makes the fast pointer reach the end. This detects a cycle without changing the links.

## Common Failure Modes

Overwriting `current.next` before saving it can lose the rest of the list. Returning the old head after reversal exposes only the new tail. Treat empty lists and changes to the head or tail as normal cases, not exceptions to ignore.

Reversing links mutates the structure seen by anyone holding its nodes. A caller retaining the original head does not automatically gain a reference to the new head; make ownership and mutation part of the function's contract.

## Worked Prediction

Before reversing `4 -> 7 -> 9`, save `old_head = head`. After assigning the returned head, what does `to_list(old_head)` return, and what happens on an empty list?

**Check your reasoning:** It returns `[4]`, because the original head is now the tail with `next = None`. Reversing an empty list returns `None` without entering the loop. A one-node list returns the same node.

## Interview Questions

> [!question] Interview Questions
> - When is linked-list insertion really constant time?
> - Why must reversal save the next reference before changing a link?
> - How does a doubly linked list change deletion and memory costs?
> - How would you detect a cycle without storing every visited node?

## Answer Notes

1. When the insertion position is already available as a node reference, or the insertion is at the head. Finding a position first may require linear traversal.

2. The saved reference preserves the unprocessed suffix. Without it, redirecting the link can remove the algorithm's route to the remaining nodes.

3. A known node carries access to its predecessor and successor, allowing constant-time unlinking with appropriate head and tail updates. Every node needs an additional reference and consistent updates.

4. Move slow and fast pointers by one and two links. Meeting implies a cycle; reaching the end implies none, with `O(n)` time and `O(1)` extra space.

## Further Reading

- [Princeton: Linked Structures, Stacks and Queues](https://algs4.cs.princeton.edu/13stacks/)

## Related Guides

- [Arrays and Strings](./arrays-and-strings.md) — compare indexing, locality and insertion costs.
- [Stacks and Queues](./stacks-and-queues.md) — abstract behaviours that can use linked implementations.
- [Java Collections](../languages/java/java-collections.md) — choosing between library implementations.

Return to [Data Structures and Algorithms](./README.md).
