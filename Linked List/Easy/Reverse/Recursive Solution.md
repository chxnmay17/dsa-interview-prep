https://leetcode.com/problems/reverse-linked-list/


# 1. Algorithm

1. If the list is empty or has one node, return it — that's the base case.
2. Recursively reverse the sublist starting at `head.next` and get `newHead` (the head of the reversed tail).
3. Make the original second node point back to the original head (i.e., `head.next.next = head`), effectively flipping the link between the first two nodes in that local context.
4. Break the old `head -> next` link by setting `head.next = null` (so the old head becomes the tail of the reversed part).
5. Return `newHead` (the head of the fully reversed list) up the recursion stack.

Short trace for list `1 -> 2 -> 3 -> null`:

* recurse down until `3` (base case), return `3`.
* at node `2`: `2.next.next = 2` makes `3 -> 2`; `2.next = null` makes `2` tail.
* at node `1`: `1.next.next = 1` makes `2 -> 1`; `1.next = null`.
* return `3` as new head → resulting list `3 -> 2 -> 1 -> null`.

# 2. Code

```java
class Solution {
    public ListNode reverseList(ListNode head) {
        if (head == null || head.next == null)
            return head;

        ListNode newHead = reverseList(head.next); 
        head.next.next = head;  
        head.next = null;       

        return newHead; 
    }
}
```

# 3. Time Complexity

**O(n)**, where `n` is the number of nodes.

**Why:** each node causes exactly one recursive call and only O(1) pointer operations are performed at each call (setting two pointers). So total work is proportional to the number of nodes.

# 4. Space Complexity

**O(n)** additional space due to recursion call stack (one stack frame per node).

**Notes:**

* Apart from the recursion stack, the algorithm uses O(1) extra heap memory (no extra data structures).
* In Java, recursion depth = number of nodes → for very large lists you risk a `StackOverflowError`. Java does **not** guarantee tail-call optimization, so this recursive approach cannot be converted into a constant-stack tail-call in standard Java.

# 5. Assumptions / Constraints

* `ListNode` is a standard singly-linked node with (at least) a `next` pointer (e.g., `int val; ListNode next;`).
* Input is a non-circular singly-linked list. If the list has a cycle, the recursion will not terminate.
* The method mutates the input list in-place and returns the new head.
* List length must be small enough to fit the program’s call stack (practical limit depends on JVM and platform).
* No assumptions about node values (integers or otherwise) are used — algorithm depends only on pointers.

# 6. Optimizations (if any)

* **Iterative version (preferred for large lists):** uses three pointers and runs in O(n) time and O(1) extra space (no recursion stack). Example:

```java
class Solution {
    public ListNode reverseList(ListNode head) {
        ListNode prev = null;
        ListNode curr = head;
        while (curr != null) {
            ListNode nextTemp = curr.next;
            curr.next = prev;
            prev = curr;
            curr = nextTemp;
        }
        return prev;
    }
}
```

* **Why prefer iterative:** avoids recursion overhead and prevents stack overflow for long lists.
* **Other notes:** Using an explicit stack would increase space to O(n) and is generally worse than recursion. Java has no reliable tail-call elimination, so a “tail-recursive” rewrite won't give O(1) stack in practice.

# 7. Edge Cases

Handled by the code:

* **Empty list (`head == null`)** — returns `null`.
* **Single-node list** — returns that node unchanged.

Potential breaks / unhandled problems:

* **Very long list** → risk of `StackOverflowError` because recursion uses O(n) stack frames.
* **Circular list / cycle** → infinite recursion (no termination).
* **Concurrent modification** from other threads while reversing — could produce undefined behavior (not thread-safe).
* **Malformed nodes** (e.g., a node's `next` pointer pointing to arbitrary memory / null checks skipped) — outside normal assumptions.

---

