https://leetcode.com/problems/find-the-duplicate-number/

---

## 1. **Algorithm **

* We are given an array `nums` of length `n+1`, where each number is in the range `[1, n]`. Since there are `n+1` numbers but only `n` possible values, the **Pigeonhole Principle** guarantees at least one duplicate.
* The problem is transformed into **cycle detection in a linked list**:

  * Treat each index as a "node".
  * Each `nums[i]` represents a pointer to the next node.
  * Since there is at least one duplicate, there will be a cycle.
* Steps:

  1. Initialize two pointers: `slow` and `fast`, both starting at index `0`.
  2. Move `slow` by one step (`slow = nums[slow]`) and `fast` by two steps (`fast = nums[nums[fast]]`).
  3. Continue until they meet (inside the cycle).
  4. Now, start another pointer `slow2` at index `0`.
  5. Move both `slow` and `slow2` one step at a time until they meet again.
  6. The meeting point is the duplicate number.

---

## 2. **Code**

```java
class Solution {
    public int findDuplicate(int[] nums) {
        int slow = 0, fast = 0;
        while (true) {
            slow = nums[slow];
            fast = nums[nums[fast]];
            if (slow == fast) break;
        }

        int slow2 = 0;
        while (true) {
            slow = nums[slow];
            slow2 = nums[slow2];
            if (slow == slow2) return slow;
        }
    }
}
```

---

## 3. **Time Complexity**

* **Phase 1 (Finding intersection):**
  The `slow` and `fast` pointers move through the array until they meet. This takes at most `O(n)` steps.

* **Phase 2 (Finding cycle start / duplicate):**
  Both pointers again move at most `O(n)` steps.

* ✅ **Overall Time Complexity = O(n)**

---

## 4. **Space Complexity**

* The algorithm only uses a few variables (`slow`, `fast`, `slow2`).

* No extra data structures are used.

* ✅ **Overall Space Complexity = O(1)**

---

## 5. **Assumptions / Constraints**

* Input array length = `n+1`.
* Elements are in the range `[1, n]`.
* There is **at least one duplicate** (as per problem guarantee).
* Array elements are valid indices for navigation (`nums[i]` will always be between `1` and `n`).

---

## 6. **Optimizations (if any)**

* This algorithm is already optimal for:

  * **Time:** O(n)
  * **Space:** O(1)

* Alternative approaches:

  * **Sorting-based approach:** O(n log n), O(1) extra space.
  * **HashSet approach:** O(n), O(n) space.
  * ✅ The Floyd’s Cycle Detection method is the **best possible** (O(n), O(1)).

---

## 7. **Edge Cases**

Handled:

* Minimum valid input (size 2 array, e.g., `[1,1]`). Works correctly.
* Multiple duplicates (e.g., `[3,1,3,4,2]`). Returns **one duplicate** (not necessarily all).
* Large input arrays within problem constraints.

Not handled / Potential issues:

* If array does **not follow constraints** (e.g., values outside `[1,n]` or no duplicate exists), the algorithm may enter an infinite loop.
* If input array is empty or too small, code may throw **ArrayIndexOutOfBoundsException**.

---

