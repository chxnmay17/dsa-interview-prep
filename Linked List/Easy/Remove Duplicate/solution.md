https://leetcode.com/problems/remove-duplicates-from-sorted-list/


# 1. Algorithm 

1. Start with a pointer `curr` at the list head.
2. Loop while `curr` is not `null`.
3. If `curr.next` exists and `curr.val == curr.next.val`, remove the next node by linking `curr.next` to `curr.next.next` (this deletes the duplicate node while keeping `curr` where it is).
4. If the above condition was false, advance `curr` to the next node.
5. Repeat until you reach the end of the list.
6. Return the (modified) head.

Key idea: because duplicates in a *sorted* list appear consecutively, the code repeatedly removes the immediate next node while it equals the current node; only when next is different does `curr` advance.

# 2. Code

```java
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        ListNode curr=head;
        while(curr!=null){
            if(curr.next!=null&&curr.val==curr.next.val){
                curr.next=curr.next.next;
                continue;
            }    
            if(curr!=null)curr=curr.next;
        }
        return head;
    }
}
```

# 3. Time Complexity

**O(n)** where `n` is the number of nodes.

Justification: each iteration of the loop either removes a node (`curr.next = curr.next.next`) or advances `curr`. A removal decreases the total number of nodes; an advance moves toward termination. Therefore the total number of loop iterations is proportional to `n` (more formally ≤ 2n in the worst case), so the runtime grows linearly.

# 4. Space Complexity

**O(1)** (constant extra space).

Justification: the algorithm uses a fixed number of pointers (`curr` and implicit temporary references like `curr.next`) and does in-place changes. No additional data structures proportional to `n` are used.

# 5. Assumptions / Constraints

* **Sorted list**: The code assumes the input is a sorted singly linked list (non-decreasing). Only consecutive equal values are removed; non-consecutive duplicates in an unsorted list will remain.
* **ListNode definition**: Assumes a typical Java `ListNode` class, e.g. `class ListNode { int val; ListNode next; }`.
* **Keeps first occurrence**: The algorithm preserves the first occurrence of equal values and deletes later consecutive duplicates.
* **No cycles**: Assumes the list is acyclic — if the list contains a cycle the loop may not terminate.
* **Java GC**: Removed nodes are eligible for garbage collection (no manual memory free required).

# 6. Optimizations (if any)

* **Code cleanup / readability**: the `if (curr != null) curr = curr.next;` is redundant because the loop condition `while (curr != null)` guarantees `curr` is non-null at that point. Prefer:

```java
ListNode curr = head;
while (curr != null && curr.next != null) {
    if (curr.val == curr.next.val) {
        curr.next = curr.next.next;
    } else {
        curr = curr.next;
    }
}
return head;
```

* **Edge behavior when head changes**: For this problem (keeping first occurrence) head never needs replacement. If you needed to *remove all nodes that have duplicates* (i.e., leave only unique values), use a dummy node and a different approach (LeetCode 82 variant).

* **Unsorted list variant**: If input may be unsorted and you want to delete *all* duplicates (first occurrences kept), use a `HashSet` to record seen values and remove nodes as you go — this makes time O(n) but uses O(n) extra space.

* **Cycle-safety**: If lists might have cycles and you still want safe termination, detect cycles first (Floyd’s algorithm) and handle accordingly.

# 7. Edge Cases

Handled / behaves correctly for:

* `head == null` (empty list) → returns `null`.
* Single-node list → returns same single node.
* Duplicates at head (e.g., `1->1->2`) → head remains the first `1`, later `1` removed.
* All nodes equal (e.g., `1->1->1->1`) → reduces to a single node `1`.
* No duplicates → list unchanged.

Potentially problematic or unhandled:

* **Unsorted list**: non-consecutive duplicates will not be removed.
* **Cycle in list**: the `while(curr != null)` may never terminate if there's a cycle.
* **Concurrent modification**: if another thread mutates the list concurrently, behavior is undefined.
* If you needed to *completely remove* values that appear more than once (so none of those values remain), this code is not suitable — you need a different algorithm using a dummy head and extra checks.

---


