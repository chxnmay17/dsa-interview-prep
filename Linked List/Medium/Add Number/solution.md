
https://leetcode.com/problems/add-two-numbers
# 1. Algorithm

1. Create a dummy head node `res` to simplify list construction; `current` points to it.
2. Maintain an integer `carry` initially `0`.
3. Loop while **at least one** of the following is true: `l1` not null, `l2` not null, or `carry != 0`.

   * Start `sum = carry`.
   * If `l1` exists, add `l1.val` to `sum` and advance `l1`.
   * If `l2` exists, add `l2.val` to `sum` and advance `l2`.
   * Set `carry = sum / 10` (integer division).
   * Create a new node with value `sum % 10` and attach it to `current.next`.
   * Advance `current` to the newly created node.
4. After loop ends, return `res.next` (skip dummy).

Example: l1 = 2->4->3 (342), l2 = 5->6->4 (465)

* Iter1: sum=2+5+0=7 -> node 7, carry 0
* Iter2: sum=4+6+0=10 -> node 0, carry 1
* Iter3: sum=3+4+1=8 -> node 8, carry 0
  Result: 7->0->8 (807)

# 2. Code (as-is)

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode() {}
 *     ListNode(int val) { this.val = val; }
 *     ListNode(int val, ListNode next) { this.val = val; this.next = next; }
 * }
 */
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode res = new ListNode(0); // Dummy node to ease list creation
        ListNode current = res;
        int carry = 0;

        while (l1 != null || l2 != null || carry != 0) {
            int sum = carry;

            if (l1 != null) {
                sum += l1.val;
                l1 = l1.next;
            }

            if (l2 != null) {
                sum += l2.val;
                l2 = l2.next;
            }

            carry = sum / 10;
            current.next =new ListNode(sum % 10);
            current=current.next;
            
        }

        return res.next;
    }
}
```

# 3. Time Complexity

**O(max(n, m))**, where `n` and `m` are the lengths of `l1` and `l2`.

**Justification:** Each loop iteration consumes at least one node from `l1` or `l2` (or handles the final carry). The number of iterations is at most `max(n, m) + 1` (the `+1` for a final carry). Each iteration does O(1) work (a few additions, divisions, pointer moves), so overall O(max(n,m)).

# 4. Space Complexity

**O(max(n, m))** (not counting input lists).

**Justification:** The algorithm allocates a new output list whose length is at most `max(n, m) + 1` (the extra node if there is a final carry). Additional auxiliary space is O(1) (few integer variables and pointers).

# 5. Assumptions / Constraints

* `ListNode.val` holds a single decimal digit (0–9). The algorithm assumes digits are valid (0–9).
* The lists represent non-negative integers in **reverse order** (least significant digit first).
* No handling of negative digits or non-integer values.
* The code assumes `l1` and/or `l2` can be `null` (treated as 0) — the `while` condition handles this.
* Input sizes can be large but each node is processed once; no risk of integer overflow in `sum` because sum = up to 9 + 9 + 1 = 19 (fits in `int` easily).
* Sorting stability, charset, etc. are irrelevant here.

# 6. Optimizations (if any)

The implementation is already clean and optimal in time asymptotics. Some possible micro-optimizations / alternatives:

* **Reuse existing nodes** (in-place) to save allocation:

  * Instead of always creating new nodes, you can reuse nodes from `l1` or `l2` (whichever remains) to build the result and only allocate when necessary. That reduces memory allocations but complicates logic and may mutate inputs (not acceptable if inputs must remain unchanged).
* **Avoid dummy node** (minor): You can handle head creation separately, but dummy node simplifies logic and is preferred for clarity.
* **Slight micro-optimizations**: minimize repeated field accesses (already minimal). Not needed in typical interviews.
* **Recursive approach**: possible but less space-efficient (adds recursion stack) and not better for this reverse-order variant.
* **Use fewer condition checks**: current code checks `l1 != null` and `l2 != null` every iteration — required for correctness.

Recommendation: keep the current approach for clarity and safety; only reuse nodes if memory allocations are a strict constraint and you're allowed to mutate inputs.

# 7. Edge Cases

Handled by code:

* **Different lengths**: if one list is longer, the while loop continues consuming remaining nodes from the longer list and includes carry.
* **Final carry**: `carry != 0` in the loop condition ensures a final node is created when carry remains (e.g., 5 + 5 = 0 -> carry 1 -> add final node 1).
* **One or both lists null**: If `l1` or `l2` is `null`, it's treated as 0; if both are `null` and `carry` is 0, the loop never executes and `res.next` is `null` — but typically both null inputs are not expected; more common is at least one non-null. If both inputs are `null`, returning `res.next` (null) means empty list; you might prefer to return a single node with 0 — depends on problem spec.

Potential problematic / unhandled cases (assumptions violated):

* **Non-digit node values**: If `val` is outside 0–9 (e.g., negative or >9), result becomes incorrect — algorithm assumes valid digits.
* **Null head semantics**: Some contexts expect `addTwoNumbers(null, null)` => node `0`. Current code returns `null`. If you want a `0` node, you can handle that case explicitly.
* **Concurrent mutation / shared nodes**: If other code shares references to nodes and expects them unchanged, any optimization that reuses nodes could break external expectations.
* **Extremely large lists**: No algorithmic problem, but memory/time linear in size — watch for runtime/environment limits.

---


