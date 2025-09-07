https://leetcode.com/problems/reverse-linked-list/description/


---

## 1. Algorithm 

The goal is to **reverse a singly linked list**.
Steps:

1. Start with two pointers:

   * `prev` = `null` (will become the new tail)
   * `curr` = `head` (current node we are processing).
2. Traverse the list node by node until `curr` becomes `null`.
3. For each node:

   * Store the next node (`next = curr.next`).
   * Reverse the link (`curr.next = prev`).
   * Move `prev` one step forward (`prev = curr`).
   * Move `curr` one step forward (`curr = next`).
4. When traversal ends, `prev` will point to the new head of the reversed list.
5. Return `prev`.

---

## 2. Code (as-is)

```java
class Solution {
    public ListNode reverseList(ListNode head) {
        if (head == null)
            return null;
        ListNode curr = head;
        ListNode prev = null;
        while (curr != null) {
            ListNode next = curr.next;
            curr.next = prev;
            prev = curr;
            curr = next;
        }
        return prev;
    }
}
```

---

## 3. Time Complexity

* **O(n)** where `n` is the number of nodes in the linked list.

  * Each node is visited **exactly once**.
  * Each operation (pointer updates) inside the loop is **O(1)**.

---

## 4. Space Complexity

* **O(1)** (constant space).

  * Only uses three extra pointers: `prev`, `curr`, and `next`.
  * No additional data structures are used.

---

## 5. Assumptions / Constraints

* Input list can be empty (`head == null`), which is handled.
* The function modifies the list **in place** (no new list created).
* Works with any length of list (from 0 nodes to very large lists).
* `ListNode` is assumed to be a standard singly linked list node with at least `int val` and `ListNode next`.

---

## 6. Optimizations (if any)

* The code is already **optimal** in both time (**O(n)**) and space (**O(1)**).
* Alternative: recursive reversal (but that uses **O(n)** space due to recursion stack). The iterative version here is better in terms of memory efficiency.

---

## 7. Edge Cases

✅ **Handled**

* Empty list (`head == null`) → returns `null`.
* Single-node list → returns the same node.
* Multi-node list → correctly reversed.

⚠️ **Potential Issues**

* Extremely large lists (e.g., millions of nodes) → recursion would cause stack overflow, but this iterative approach avoids that problem.
* Modifies the input list permanently (if caller expects the original order, it’s lost).

---

