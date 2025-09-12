https://leetcode.com/problems/swap-nodes-in-pairs/?envType=problem-list-v2&envId=linked-list

# 1. Algorithm 

1. If the list is empty or has one node, return it unchanged.
2. Set the new head to the second node (because after the first swap the 2nd node becomes the list head).
3. Use a pointer `cur` to iterate from the first node of the next pair to process.
4. For each pair starting at `cur`:

   * Let `tmp` = `cur` (first node of current pair).
   * If there is a second node (`cur.next` exists):

     * Let `tmp2` = node after the pair (`cur.next.next`).
     * Move `cur` to the second node of the pair, and set `cur.next = tmp` (swap the two nodes).
     * Set `tmp.next` to point to the correct node after the swapped pair:

       * If `tmp2` is null → no nodes remain → `tmp.next = null`.
       * If `tmp2.next` is null → only one node remains → `tmp.next = tmp2`.
       * Otherwise → set `tmp.next = tmp2.next` (connect to the next pair's second node).
     * Move `cur` to `tmp2` (start processing the next pair).
   * If there is no second node, return the head (odd number of nodes, last single node stays in place).
5. Return the (possibly new) head.

# 2. Code (as-is)

```java
class Solution {
    public ListNode swapPairs(ListNode head) {
        if(head==null||head.next==null)return head;
        ListNode cur=head;
        head=cur.next;
        while(cur!=null){
            ListNode tmp=cur;
            if(cur.next!=null){
                ListNode tmp2=cur.next.next;
                cur=cur.next;
                cur.next=tmp;
                tmp.next=(tmp2==null)?null:(tmp2.next==null)?tmp2:tmp2.next;
                cur=tmp2;
            }
            else{return head;}
        }
      return head;  
    }
}
```

# 3. Time Complexity

**O(n)** — Each node is visited a constant number of times (the loop advances by two nodes per successful swap and by one check when a single trailing node remains). There are no nested loops or recursion that increase work beyond linear in the number of nodes `n`.

# 4. Space Complexity

**O(1)** extra space — only a fixed number of pointer variables (`cur`, `tmp`, `tmp2`, etc.) are used. The list is mutated in-place; no auxiliary arrays or recursive stack growth are used.

# 5. Assumptions / Constraints

* `ListNode` is a standard singly-linked node with a `next` pointer (and presumably a `val` field).
* The code mutates the original list (it is not creating new `ListNode` objects).
* Input size `n` fits in memory (no streaming constraints).
* There are no cycles in the input list (function assumes a proper terminating null).
* No requirement on preserving node values order beyond swapping adjacent nodes — swapping is by changing pointers, not by copying values.
* This is for singly-linked lists only.

# 6. Optimizations (if any)

The algorithm is already optimal asymptotically (O(n) time, O(1) space). Suggestions to improve readability and maintainability:

1. **Use a dummy node** to avoid special-casing head:

   * Create `dummy.next = head`, use `prev` pointer to connect swapped pairs, and iterate while `prev.next != null && prev.next.next != null`.
   * This makes logic simpler and avoids setting `head` separately at start.
2. **Clearer variable names** (`first`, `second`, `nextPair`) improve readability.
3. **Simplify the `tmp.next` assignment** with clearer conditionals (avoid nested ternaries):

   ```java
   ListNode nextPair = second.next;
   second.next = first;
   if (nextPair == null) first.next = null;
   else first.next = nextPair;
   ```

   (Or better: use `first.next = (nextPair == null) ? null : nextPair.next;` depending on linking strategy with dummy.)
4. **Use a loop that advances by two nodes using `prev` pointer** — that's the common, easy-to-read pattern:

   ```java
   ListNode dummy = new ListNode(0);
   dummy.next = head;
   ListNode prev = dummy;
   while (prev.next != null && prev.next.next != null) {
       ListNode a = prev.next;
       ListNode b = a.next;
       prev.next = b;
       a.next = b.next;
       b.next = a;
       prev = a;
   }
   return dummy.next;
   ```

   This avoids nested ternaries and special head handling.

# 7. Edge Cases (handled or that could break)

Handled correctly:

* **Empty list** (`head == null`) → returns `null`.
* **Single node** (`head.next == null`) → returns the node unchanged.
* **Even length list** → all adjacent pairs swapped.
* **Odd length list** → last node remains unchanged.

Potential issues / things to watch for:

* **Cyclical list**: the code assumes termination with `null`. If the input has a cycle, the loop may never terminate.
* **Readability pitfalls**: nested ternary (`tmp.next=(tmp2==null)?null:(tmp2.next==null)?tmp2:tmp2.next;`) is correct but hard to read and easy to misinterpret — refactor for clarity.
* **If ListNode class uses unusual contract** (e.g., sentinel nodes), behavior could differ — code assumes typical singly-linked list semantics.
* **Concurrent modification**: not thread-safe — concurrent updates to the list would break correctness.
* **NullPointer protection**: the code checks `cur.next` before dereferencing, so standard null pointers are avoided as written.

---


