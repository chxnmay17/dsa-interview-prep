https://leetcode.com/problems/contains-duplicate/description/


---

## 1. Algorithm 

1. Take the given array of integers.
2. Sort the array in ascending order.
3. Traverse the array from start to end.
4. For each element, compare it with the next one.

   * If two consecutive elements are equal → duplicate found → return **true**.
5. If no duplicates are found after the traversal, return **false**.



---

## 2. Code

```java
class Solution {
    public boolean containsDuplicate(int[] nums) {
        Arrays.sort(nums); 
        for (int i = 0; i < nums.length - 1; i++) {
            if (nums[i] == nums[i+1]) {
                return true;
            }
        }
        return false;
    }
}
```

---

## 3. Time Complexity

* **Sorting**: `O(n log n)` (using Dual-Pivot QuickSort in Java for primitives).
* **Single pass comparison**: `O(n)`.
* **Total**: **O(n log n)** (sorting dominates).

---

## 4. Space Complexity

* Java’s `Arrays.sort()` for `int[]` is **in-place** (Dual-Pivot QuickSort).
* Only constant extra space is used (apart from input).
* **Space Complexity**: **O(1)** (ignoring input storage).

---

## 5. Assumptions / Constraints

* Input array `nums` can be empty or large.
* Elements are integers (`int` in Java).
* Sorting is stable but **stability doesn’t matter** here (only equality check needed).
* No explicit limit is given on input size, but performance depends on `n`.

---

## 6. Optimizations (if any)

* **Using a HashSet**:
  Instead of sorting, insert each number into a `HashSet`.

  * If a number already exists in the set → duplicate → return **true**.
  * Time Complexity: **O(n)** (on average).
  * Space Complexity: **O(n)**.
* Tradeoff: Faster in time but uses more memory.



---

## 7. Edge Cases

* **Empty array (`[]`)** → returns **false** (no duplicates).
* **Single element array (`[5]`)** → returns **false** (no duplicates).
* **All elements unique (`[1,2,3,4]`)** → returns **false**.
* **All elements same (`[7,7,7,7]`)** → returns **true**.
* **Large input array** → sorting still works but may be slower than hash-based method.

---


