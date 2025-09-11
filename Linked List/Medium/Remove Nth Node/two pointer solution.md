
https://leetcode.com/problems/remove-nth-node-from-end-of-list/
# Algorithm

1. Use two pointers `p1` and `p2`, both starting at the list head. Also create a dummy node `prev` that points to head (the code uses value `-1` for this dummy).
2. Move `p2` forward `n` steps. Now `p2` is `n` nodes ahead of `p1`.
3. Move `p1`, `prev`, and `p2` forward together one step at a time until `p2` becomes `null`.

   * At loop end, `p1` points to the node that should be removed (the nth from the end).
   * `prev` points to the node immediately before `p1` (initially the dummy).
4. If `prev` is still the dummy, the node to remove was the original head → return `head.next`.
5. Otherwise, bypass `p1` by setting `prev.next = p1.next`, and return the original `head`.

Short example: list `[1,2,3,4,5]`, `n=2`

* After advancing `p2` 2 steps it points at `3`.
* Moving all three pointers until `p2` becomes `null` lands `p1` at `4` (2nd from end) and `prev` at `3`. The code then removes `4`.

# Code

```java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        
        ListNode p1=head;
        ListNode p2=head;
        ListNode prev=new ListNode(-1,head);
        for(int i=0;i<n;i++){
            
            p2=p2.next;
            
        }
        while(p2!=null){
            prev=prev.next;
            p1=p1.next;
            p2=p2.next;
        }
        if(prev.val==-1){
            return head.next;
        }
        prev.next=p1.next;
        return head;
    }
}
```

# Time Complexity

**O(L)** where `L` is the length of the list.

**Why:** The code advances `p2` `n` steps (≤ L), then advances all pointers in the second loop for the remaining nodes (≤ L − n). Total pointer moves ≤ L + constant ⇒ linear time.

# Space Complexity

**O(1)** extra space.

**Why:** Only a constant number of pointers (`p1`, `p2`, `prev`) and one dummy node are used. No additional memory proportional to input size is allocated.

# Assumptions / Constraints

* `ListNode` supports `new ListNode(int val, ListNode next)` (LeetCode-style).
* The code assumes `head` is non-`null` and `n >= 1` and `n <= length` (these are typical LeetCode constraints).
* The dummy node uses value `-1` as a sentinel; code tests `prev.val == -1` to detect the dummy.
* The list is singly linked (no access to previous nodes).
* Sorting, stability, or character set is not relevant here.

# Optimizations / Fixes (suggested)

The algorithm is already optimal in time (one pass) and space (O(1).) But the implementation has a fragile sentinel check and lacks input validation. Suggested fixes:

1. **Use a true `dummy` reference** and compare `prev == dummy` rather than checking `prev.val == -1`. Checking `val` is unsafe if list nodes can contain `-1`.
2. **Validate inputs** to avoid `NullPointerException` when `head == null` or `n > length`.
3. **Return `dummy.next`** at the end — simpler and robust for head removal.

Corrected, robust version:

```java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        if (head == null || n <= 0) return head; // could also throw exception

        ListNode dummy = new ListNode(0, head);
        ListNode p1 = head;
        ListNode p2 = head;
        // Advance p2 n steps, but validate
        for (int i = 0; i < n; i++) {
            if (p2 == null) return head; // n > length: keep original or handle as error
            p2 = p2.next;
        }

        ListNode prev = dummy;
        // Move until p2 is null — prev lags behind p1 by one node
        while (p2 != null) {
            prev = prev.next;
            p1 = p1.next;
            p2 = p2.next;
        }

        // Remove p1
        prev.next = p1.next;
        return dummy.next;
    }
}
```

Notes:

* Using `dummy` and returning `dummy.next` avoids special-casing head removal.
* The check `if (p2 == null) return head;` is one policy for `n > length` (other choices: throw `IllegalArgumentException`, or treat `n` modulo `length`, etc.).

# Edge Cases (handled / potential problems)

Handled or partially handled by original code:

* **Removing the head node** (`n == length`): original code tries to detect this using `prev.val == -1` and returns `head.next`. It *works* if node values never equal `-1`, but this check is **fragile**.
* **Single-node list** with `n=1`: original code returns `null` (works).

Not handled / can break (important):

* **`head == null`** → `NullPointerException` in the `for` loop.
* **`n > length`** → `NullPointerException` while advancing `p2` in the `for` loop.
* **`n == 0`** (invalid per typical problem statement) → behavior undefined; likely leads to errors.
* **Nodes containing value `-1`**: because the code tests `prev.val == -1` to detect dummy, it may mis-detect a non-dummy node that happens to have value `-1` and incorrectly return `head.next`.
* **Cyclic lists**: algorithm assumes an acyclic singly-linked list; cycles would make the second loop infinite.
* **Concurrent modification**: not thread-safe — not relevant for typical DSA problems.

# Quick test cases to try

1. `head = [1,2,3,4,5], n = 2` → `[1,2,3,5]`
2. `head = [1], n = 1` → `[]` (null)
3. `head = [1,2], n = 2` → `[2]` (remove head)
4. `head = null, n = 1` → should be handled gracefully (current code crashes)
5. `head = [ -1, 2, 3 ], n = 2` → current code could misbehave if sentinel check relies on `-1` value

---

