https://leetcode.com/problems/contains-duplicate/description/

---

## **1. Algorithm **

The code checks whether an integer array contains any duplicate values.

**Step-by-step process:**

1. Create an empty `HashSet` called `seen`.
2. Traverse the array `nums` one element at a time:

   * If the current element already exists in `seen`, return `true` (a duplicate is found).
   * Otherwise, add the element to `seen`.
3. If the loop finishes without finding any duplicate, return `false`.


---

## **2. Code**

```java
public class Solution {
    public boolean hasDuplicate(int[] nums) {
        Set<Integer> seen = new HashSet<>();
        for (int num : nums) {
            if (seen.contains(num)) {
                return true;
            }
            seen.add(num);
        }
        return false;
    }
}
```

---

## **3. Time Complexity**

* **Best case:** `O(1)` → If the duplicate is found at the start (e.g., `[1,1,...]`).
* **Worst case:** `O(n)` → If no duplicates exist, we must check every element.
* **Average case:** `O(n)` → HashSet operations (`contains`, `add`) take **O(1)** on average (amortized).

✅ **Final Time Complexity:** **O(n)**

---

## **4. Space Complexity**

* A `HashSet` is used to store seen numbers.
* In the worst case (no duplicates), all `n` elements are stored in the set.

✅ **Final Space Complexity:** **O(n)**

---

## **5. Assumptions / Constraints**

* Input: `nums` is an integer array.
* HashSet allows `null` but not duplicates — suitable for this problem.
* Works for both positive and negative integers.
* No explicit constraints on array size, but large arrays will use proportional memory (`O(n)`).
* Order of elements does **not** matter (just detecting duplicates).

---

## **6. Optimizations (if any)**

1. **Sorting-based approach (O(n log n) time, O(1) space if in-place):**

   * Sort the array, then check adjacent elements for duplicates.
   * Better space usage but slower than HashSet for large data.

2. **Bitset/boolean array optimization:**

   * If input values are within a **known small range** (e.g., `0–10^6`), use a boolean array or BitSet instead of HashSet to reduce memory overhead.

---

## **7. Edge Cases**

1. **Empty array (`[]`)** → No duplicates → returns `false`.
2. **Single element (`[5]`)** → No duplicates → returns `false`.
3. **All unique values (`[1,2,3,4]`)** → No duplicates → returns `false`.
4. **Immediate duplicate (`[2,2,3,4]`)** → Duplicate found quickly → returns `true`.
5. **Duplicates at the end (`[1,2,3,4,1]`)** → Duplicate found only after scanning entire array.
6. **Large array with many duplicates** → Still efficient since HashSet handles lookups in O(1).

---

