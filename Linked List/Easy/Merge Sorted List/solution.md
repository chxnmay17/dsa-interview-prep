https://leetcode.com/problems/merge-two-sorted-lists/
---

## 1. Algorithm 

1. Create a **dummy node** (`current`) to help build the merged list without handling special cases for the first node.
2. Keep a pointer `head` to remember the start of the merged list.
3. Traverse both lists (`list1` and `list2`) simultaneously:

   * Compare the current nodes.
   * Attach the smaller node to the merged list (`current.next`).
   * Move forward in the list from which the node was chosen.
   * Move the `current` pointer forward.
4. When one list becomes empty:

   * Directly attach the remaining nodes of the other list (since it’s already sorted).
5. Return the merged list starting from `head.next` (skipping the dummy node).

---

## 2. Code (as given)

```java
class Solution {
    public ListNode mergeTwoLists(ListNode list1, ListNode list2) {
        ListNode current = new ListNode();
        ListNode head = current;
        while (list1 != null && list2 != null) {
            if (list1.val <= list2.val) {
                current.next = list1;
                list1 = list1.next;
            } else {
                current.next = list2;
                list2 = list2.next;
            }
            current = current.next;
        }
        if (list1 != null) {
            current.next = list1;
        } else {
            current.next = list2;
        }
        return head.next;
    }
}
```

---

## 3. Time Complexity

* Each node from both lists is **visited exactly once**.
* If list1 has `m` nodes and list2 has `n` nodes:
  **Time Complexity = O(m + n)**

✅ Justification: Each comparison/move takes constant time, and in total, at most `m + n` steps are performed.

---

## 4. Space Complexity

* **Auxiliary Space = O(1)** (no extra data structures, just pointers).
* The result reuses the existing nodes (no new nodes created except the dummy).

✅ Justification: Only a dummy node and a few pointers are used, regardless of input size.

---

## 5. Assumptions / Constraints

* Both input lists (`list1`, `list2`) are **individually sorted in non-decreasing order**.
* `ListNode` is a standard singly linked list node with fields: `int val`, `ListNode next`.
* Input sizes can be 0 (empty lists are valid).
* Stability: Since `<=` is used in comparison, nodes from `list1` are preferred when values are equal → merge is **stable**.

---

## 6. Optimizations (if any)

* The solution is already optimal in **time** and **space**.
* Minor optimization: instead of using a dummy node, handle the first node separately, but that adds branching logic without improving efficiency.
* Alternative: Use recursion (cleaner, but increases space complexity to O(m+n) due to call stack).

---

## 7. Edge Cases

Handled:

* Both lists are empty → returns `null`.
* One list empty, other non-empty → directly returns the non-empty list.
* Lists with duplicate values → handled correctly, preserves stability.

Potential issues:

* If `ListNode` constructor without arguments (`new ListNode()`) is not defined, compilation may fail (depending on implementation).

---
