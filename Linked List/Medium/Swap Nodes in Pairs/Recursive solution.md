https://leetcode.com/problems/swap-nodes-in-pairs/


# 1. Algorithm

1. `swapPairs(head)` just delegates to a helper `swap(head)`.
2. `swap(n)`:

   * If `n` is `null`, return `null` (empty list).
   * If `n.next` is `null`, return `n` (single node — nothing to swap).
   * Otherwise:

     * Let `n1 = n` (first node of current pair).
     * Let `n2 = n.next` (second node of current pair).
     * Let `n3 = n.next.next` (start of remaining list).
     * Swap the first two by doing `n2.next = n1`.
     * Recursively swap the rest: `n1.next = swap(n3)`.
     * Return `n2` as the new head of this (sub)list.
3. Recursion proceeds pair-by-pair, so the list is transformed pairwise: `1->2->3->4` becomes `2->1->4->3`.

# 2. Code

```java
class Solution {
    public ListNode swapPairs(ListNode head) {
        return swap(head);
    }
    public ListNode swap(ListNode n) {
        if (n == null) {
            return null;
        }
        if (n.next == null) {
            return n;
        }
        ListNode n1 = n, n2 = n.next, n3 = n.next.next;
        n2.next = n1;
        n1.next = swap(n3);
        return n2;
    }
}
```

# 3. Time Complexity

**O(n)** where `n` is the number of nodes.

**Justification:** Each node is visited a constant number of times (each recursive call processes two nodes and performs O(1) pointer updates). The recursion reduces the list by two nodes per call, so total work is proportional to `n`. Therefore time is linear: `T(n) = T(n-2) + O(1)` → `O(n)`.

# 4. Space Complexity

* **Auxiliary (non-recursive) space:** **O(1)** — only a few pointer variables (`n1`, `n2`, `n3`) are used.
* **Including recursion stack:** **O(n)** in the worst case (more precisely O(n/2) recursive calls → O(n) stack frames).
  Java does not perform tail-call elimination, so recursion depth can cause a `StackOverflowError` for very large lists.

# 5. Assumptions / Constraints

* `ListNode` is a standard singly-linked node (e.g. `int val; ListNode next;`).
* The list is **acyclic** (no cycle). If there are cycles, the algorithm may loop/overflow.
* The method **mutates** the original list (no new nodes created).
* Input size may need to be limited if running this recursively (stack depth).
* No assumptions about node values (duplicates allowed); the code swaps nodes (pointers), not values.
* Not concerned with sorting stability (irrelevant here).

# 6. Optimizations (if any)

* **Iterative version** avoids recursion and reduces stack usage to O(1). Use a dummy head and iterate swapping pairs with pointer manipulations. This is preferred in Java for very large lists.

Example iterative implementation (O(n) time, O(1) space):

```java
class Solution {
    public ListNode swapPairs(ListNode head) {
        ListNode dummy = new ListNode(0);
        dummy.next = head;
        ListNode prev = dummy;

        while (prev.next != null && prev.next.next != null) {
            ListNode a = prev.next;
            ListNode b = a.next;
            ListNode nextPair = b.next;

            // swap a and b
            prev.next = b;
            b.next = a;
            a.next = nextPair;

            // move prev two nodes forward
            prev = a;
        }
        return dummy.next;
    }
}
```

* **Why this helps:** eliminates recursion stack depth so safe for large `n` and still O(n) time.
* Other micro-optimizations are minimal — pointer reassignments are already constant-time.

# 7. Edge Cases

Handled correctly by code:

* **Empty list** (`head == null`) → returns `null`.
* **Single node** (`head.next == null`) → returns the same single node.
* **Even number of nodes** → all pairs swapped.
* **Odd number of nodes** → last node remains in place (no pair) — handled by base case.

Potential problems / not handled:

* **Very large lists**: recursion depth may cause `StackOverflowError`. Use iterative approach to avoid this.
* **Cyclic lists**: the code assumes no cycles — if the list has a cycle, behavior is undefined (infinite recursion or infinite loop depending on structure).
* **Concurrent modifications**: if another thread mutates the list concurrently, behavior is undefined (but that's true of most in-place list algorithms).

---

