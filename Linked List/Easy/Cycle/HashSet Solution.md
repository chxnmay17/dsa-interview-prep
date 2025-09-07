https://leetcode.com/problems/linked-list-cycle/


# 1. Algorithm 

1. Create an empty `HashSet` called `seen`.
2. Start at the list head and walk the list node-by-node using `cur`.
3. For each node:

   * If the node is already in `seen`, a cycle exists → return `true`.
   * Otherwise add the node to `seen` and move to `cur.next`.
4. If you reach `null`, the list ended with no repeats → return `false`.

In short: it detects a cycle by remembering every visited node and checking for repeats.

# 2. Code

```java
public class Solution {
    public boolean hasCycle(ListNode head) {
        HashSet<ListNode> seen = new HashSet<>();
        ListNode cur = head;
        while (cur != null) {
            if (seen.contains(cur)) {
                return true;
            }
            seen.add(cur);
            cur = cur.next;
        }
        return false;
    }
}
```

# 3. Time Complexity

**Average / Amortized:** `O(n)` where `n` = number of distinct nodes reachable from `head`.
**Justification:** each node is visited at most once; each `HashSet` insert / contains is amortized `O(1)`, so overall \~`n * O(1)`.

**Worst-case (theoretical):** `O(n^2)` if `HashSet` degenerates (pathological hash collisions or very poor `hashCode`), but this is uncommon with normal `hashCode` behavior.

# 4. Space Complexity

**Auxiliary space:** `O(n)` — the `HashSet` may store a reference for every distinct node encountered (in the acyclic case it stores all nodes; in the cyclic case it stores up to the first repeated node).

# 5. Assumptions / Constraints

* A `ListNode` class exists with a `.next` field (typical `class ListNode { int val; ListNode next; }`).
* The code relies on `HashSet` semantics: `contains()` and `add()` use `equals()` and `hashCode()`.

  * **Important assumption:** `ListNode` must *not* override `equals()`/`hashCode()` in a way that compares node *values* rather than identity. If `equals()` is value-based, two distinct node objects with the same value could appear equal and cause a false cycle detection.
* Input size may be large — memory use grows linearly with distinct nodes.
* The method does not modify the list.

# 6. Optimizations (if any)

1. **Space-optimal approach (recommended): Floyd’s Tortoise and Hare**

   * Uses two pointers (`slow`, `fast`). Move `slow` one step and `fast` two steps; if they meet, there is a cycle. No extra data structure → `O(1)` space, `O(n)` time.
   * Example:

     ```java
     public boolean hasCycle(ListNode head) {
         ListNode slow = head, fast = head;
         while (fast != null && fast.next != null) {
             slow = slow.next;
             fast = fast.next.next;
             if (slow == fast) return true; // reference equality
         }
         return false;
     }
     ```
2. **If you must use a set but want identity-based checking** (safe even if `ListNode.equals()` is overridden):

   ```java
   Set<ListNode> seen = Collections.newSetFromMap(new IdentityHashMap<>());
   ```

   This uses reference-identity rather than `equals()`/`hashCode()`.
3. **Avoid mutating the list** to mark visited nodes — mutating is risky if the caller depends on the list after the call.
4. If memory is a hard limit and list can be huge, prefer Floyd’s algorithm.

# 7. Edge Cases

* `head == null` → returns `false` (handled).
* Single node with `next == null` → returns `false` (handled).
* Single node with `next == head` (self-cycle) → `true` (handled).
* Cycle that begins after some nodes (standard case) → `true` (handled).
* **Potential bug scenario:** `ListNode.equals()`/`hashCode()` overridden to compare values → can produce false positives (e.g., two different node objects with same `val` seen as duplicate and method returns `true` even when no cycle).

  * Mitigation: use `IdentityHashMap`-backed set or use Floyd’s algorithm.
* Very large lists → may cause high memory usage or `OutOfMemoryError` due to `HashSet` growth.
* Concurrent modifications of the list from other threads while iterating — not handled (thread-safety not provided).

---


