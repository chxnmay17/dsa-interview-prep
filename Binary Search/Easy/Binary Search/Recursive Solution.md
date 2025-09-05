

---

## 1. Algorithm (in simple English)

This is a **recursive implementation of Binary Search**.
Here’s the step-by-step working:

1. Start with two pointers: `l` (left index) and `r` (right index).
2. If `l > r`, it means the element is not present → return `-1`.
3. Find the middle index `m = l + (r - l) / 2`.

   * This avoids potential overflow compared to `(l + r) / 2`.
4. Compare `nums[m]` with the `target`:

   * If equal → return `m` (index found).
   * If `nums[m] < target` → search in the **right half** (`m+1` to `r`).
   * Else → search in the **left half** (`l` to `m-1`).
5. Repeat recursively until the element is found or the range becomes invalid (`l > r`).

The `search` method simply starts the binary search across the entire array.

---

## 2. Code

```java
public class Solution {
    public int binary_search(int l, int r, int[] nums, int target) {
        if (l > r) return -1;
        int m = l + (r - l) / 2;

        if (nums[m] == target) return m;
        return (nums[m] < target) ?
            binary_search(m + 1, r, nums, target) :
            binary_search(l, m - 1, nums, target);
    }

    public int search(int[] nums, int target) {
        return binary_search(0, nums.length - 1, nums, target);
    }
}
```

---

## 3. Time Complexity

* Each recursive call halves the search space.
* Therefore, **Time Complexity = O(log n)** where `n = nums.length`.

**Justification:** At each step, only one half of the array is explored, not both.
Example: For `n = 16`, max recursive depth = `log₂(16) = 4`.

---

## 4. Space Complexity

* Recursive function calls use the **call stack**.
* In the worst case, recursion depth = `O(log n)`.
* No extra arrays or data structures are used.

👉 **Space Complexity = O(log n)** (because of recursion stack).

---

## 5. Assumptions / Constraints

* The input array `nums` is **sorted in ascending order**.
* If the array is empty (`nums.length == 0`), it returns `-1`.
* No duplicate handling is considered explicitly — if duplicates exist, the index returned will be of **one occurrence**, not necessarily the first/last.
* Assumes `nums` fits in memory, and indices do not overflow (safe in Java for normal arrays).
* Works for both positive and negative numbers in the array.

---

## 6. Optimizations (if any)

1. **Iterative Approach**:

   * Replacing recursion with a while loop would reduce space complexity from **O(log n)** to **O(1)**.

   ```java
   public int search(int[] nums, int target) {
       int l = 0, r = nums.length - 1;
       while (l <= r) {
           int m = l + (r - l) / 2;
           if (nums[m] == target) return m;
           if (nums[m] < target) l = m + 1;
           else r = m - 1;
       }
       return -1;
   }
   ```
2. **Duplicate Handling** (if required): Modify logic to find the first or last occurrence.

---

## 7. Edge Cases

Handled:

* `nums = []` → returns `-1`.
* `target` not in array → returns `-1`.
* `target` at first (`nums[0]`) or last (`nums[n-1]`) index → correctly found.

Potential issues:

* If input array is **unsorted**, results are unpredictable (binary search requires sorted input).
* Large recursion depth for very big arrays (though still `O(log n)`, could hit recursion limits if array size is huge, e.g., `>10^7`).

---

