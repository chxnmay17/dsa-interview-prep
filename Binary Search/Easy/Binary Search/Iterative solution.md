# 1. Algorithm 

* This is an **iterative binary search** on a sorted integer array `nums`.
* Start with two pointers: `l` at the beginning (`0`) and `r` at the end (`nums.length - 1`).
* Repeat while `l <= r`:

  * Compute the middle index `m = l + (r - l) / 2` (this avoids overflow from `l + r`).
  * If `nums[m] == target`, return `m` (we found the target).
  * If `nums[m] > target`, the target (if present) must be in the left half → set `r = m - 1`.
  * Else (`nums[m] < target`), the target is in the right half → set `l = m + 1`.
* If the loop finishes without finding the target, return `-1`.
* Intuition: each comparison halves the search interval, so we quickly converge to the target or conclude it isn't present.

Example trace (nums = \[1,3,5,7,9], target = 5):

* l=0, r=4 → m=2 → nums\[2]=5 → return 2.

# 2. Code

```java
class Solution {
    public int search(int[] nums, int target) {
        int l=0,r=nums.length-1;
        while(l<=r){
            int m=l+((r-l)/2);
            if(nums[m]==target) return m;
            else if(nums[m]>target) r=m-1;
            else l=m+1;
        }
        return -1;
    }
}
```

# 3. Time Complexity

* **O(log n)**, where `n = nums.length`.
* Justification: each loop iteration halves the search interval (`l..r`). The number of iterations grows proportional to the base-2 logarithm of `n`. Each iteration does a constant amount of work (index arithmetic and one array access/comparison).

# 4. Space Complexity

* **O(1)** additional space.
* Justification: only a constant number of primitive variables (`l, r, m`) are used regardless of input size. No extra arrays or recursion.

# 5. Assumptions / Constraints

* `nums` is sorted in **ascending order**. Binary search requires a sorted array; otherwise results are undefined.
* Array indices fit in Java `int` (standard for Java arrays).
* `nums` is non-`null` (the code does not check for `null`; a `null` input will throw `NullPointerException`).
* Duplicates: if there are multiple occurrences of `target`, the function returns **some** matching index (not guaranteed to be first or last).
* Uses integer arithmetic; `m = l + (r - l) / 2` avoids overflow that could occur with `(l + r) / 2`.

# 6. Optimizations (if any)

The implementation is already optimal in time (binary search). Possible improvements depending on requirements:

* **Null check**: add `if (nums == null) return -1;` to avoid NPE.
* **Early range check**: if `nums.length > 0` and `(target < nums[0] || target > nums[nums.length-1])` return `-1` — saves a few steps for out-of-range values.
* **Use library**: `Arrays.binarySearch(nums, target)` is a correct, well-tested alternative.
* **Find first/last occurrence**: if you need the leftmost/rightmost index in presence of duplicates, change behavior when `nums[m] == target`:

  * To find leftmost: set `r = m - 1` and keep track of best index.
  * To find rightmost: set `l = m + 1` and keep track.
* **Unsigned shift mid**: `m = l + ((r - l) >>> 1)` (Java) — equivalent here; mostly style/preference.
* For extremely performance-critical code, minimize repeated array bounds checks by local variable referencing, but Java JIT usually optimizes.

# 7. Edge Cases

Handled correctly:

* **Empty array (`nums.length == 0`)**: `r = -1`, loop not entered → returns `-1`.
* **Single-element array**: works as expected.
* **Target at ends (first or last element)**: found properly.
* **Large/small integers**: `target` can be any `int` value; comparisons are valid.
* **Avoids integer index overflow**: mid is computed as `l + (r - l)/2`.

Not handled / can break:

* **`nums == null`** → `NullPointerException`. Add a null check if needed.
* **Array not sorted** → results incorrect (binary search assumption violated).
* **If you require first/last occurrence with duplicates**, current code returns an arbitrary matching index (not guaranteed to be first/last).
* **Concurrent modification** of the array from another thread while searching — not thread-safe (rare in typical use).
* **Very large arrays near VM limits**: Java array size limits apply; not specifically a bug in algorithm but a platform constraint.

---


