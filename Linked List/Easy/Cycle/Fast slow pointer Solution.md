https://leetcode.com/problems/linked-list-cycle/
# 1. Algorithm 

* Use **two pointers** (`slow` and `fast`) both starting at `head`.
* Repeatedly move `slow` one step (`slow = slow.next`) and `fast` two steps (`fast = fast.next.next`) while it's safe to do so (`fast != null && fast.next != null`).
* After each move, check `if (slow == fast)` — if true, a cycle exists (the fast pointer has "lapped" the slow pointer inside the cycle).
* If the loop exits because `fast` or `fast.next` became `null`, there is **no** cycle; return `false`.

Why this works (short): if there's no cycle, `fast` will reach `null`. If there is a cycle, `fast` moves one extra node closer to `slow` each iteration (relative speed = 1) and will meet `slow` within at most one cycle length after both are inside the cycle.

Small trace (example): A → B → C → D → B (cycle starts at B)

* Iteration 1: slow=B, fast=C
* Iteration 2: slow=C, fast=B
* Iteration 3: slow=D, fast=D → meet → return `true`

---

# 2. Code

```java
public class Solution {
    public boolean hasCycle(ListNode head) {
        
        ListNode slow=head;
        ListNode fast=head;

        while(fast!=null && fast.next!=null){
            slow=slow.next;
            fast=fast.next.next;
            if(slow==fast) return true;
        }
        return false;



    }
}
```

---

# 3. Time Complexity

**O(n)** where `n` is the number of nodes reachable from `head`.

**Justification:**

* If there is no cycle, `fast` advances two nodes per loop and will hit `null` after about `n/2` iterations → O(n).
* If there is a cycle, `slow` and `fast` enter the cycle after at most `μ` steps (μ = nodes before cycle), and then `fast` closes the gap to `slow` at rate 1 per iteration; they meet within at most `λ` more steps (λ = cycle length). So total steps ≤ μ + λ = O(n).

---

# 4. Space Complexity

**O(1)** — only a constant number of extra pointers (`slow`, `fast`) and a few local variables are used, irrespective of input size.

---

# 5. Assumptions / Constraints

* `ListNode` is a standard singly-linked node (e.g. `class ListNode { int val; ListNode next; }`).
* Single-threaded execution (no concurrent mutation of the list while algorithm runs).
* Node identity is determined by **reference equality** (`==`), which is correct in Java for detecting the same node object.
* The list fits in available memory (algorithm itself doesn't allocate per-node memory).
* Code assumes safe `next` traversal guarded by `fast!=null && fast.next!=null` to avoid `NullPointerException`.

---

# 6. Optimizations (if any)

* **No algorithmic optimization required** — Floyd’s Tortoise-and-Hare is already optimal in time `O(n)` and space `O(1)` for cycle detection.
* Micro suggestions for clarity/readability (not necessary for correctness):

  * Early-return check `if (head == null || head.next == null) return false;` (the current code already handles that implicitly).
  * Add comments describing the pointer movement and why equality is checked after advancing pointers.
* If you *also* need the **start node of the cycle** or **cycle length**, you can extend the algorithm:

  * After detecting `slow==fast`, to find cycle start: reset `slow = head` and move both pointers one step at a time; they meet at cycle entry.
  * To get cycle length: after meeting, keep `fast` moving until it completes one loop and count steps.
* Alternative (worse space): Use a `HashSet<ListNode>` to store visited nodes and check for repeats — simpler to implement but `O(n)` space.

---

# 7. Edge Cases

Handled correctly by the code:

* `head == null` → while loop never runs → returns `false`.
* Single node, `head.next == null` → returns `false`.
* Single node that points to itself (`head.next == head`) → returns `true`.
* Two-node cycle (second node points back to first) → returns `true`.
* Two nodes no cycle → returns `false`.

Potential pitfalls (not problems in this code, but things to be aware of):

* Concurrent modification of the list during traversal may break correctness or cause unpredictable behavior.
* If someone overrides `equals()` on `ListNode` and expects value-based equality — the algorithm relies on **reference equality** (`==`) to detect the exact same node object.
* The method only **detects** cycle existence; it does not return the cycle entry node or length (but can be extended to do so as noted above).

---

