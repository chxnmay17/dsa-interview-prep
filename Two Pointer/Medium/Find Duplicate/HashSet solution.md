https://leetcode.com/problems/find-the-duplicate-number/description/
# 1. Algorithm 

1. Create an empty hash set `hs`.
2. Iterate through each integer `num` in the input array `nums`.
3. For each `num`:

   * If `num` is already in `hs`, that means we've seen it before → return `num` (we found a duplicate).
   * Otherwise add `num` to `hs` and continue.
4. If the loop finishes without finding any duplicate, return `0` (the code's fallback).

In short: scan left-to-right, keep track of seen numbers in a `HashSet`, and return the first repeated number encountered.

# 2. Code

```java
class Solution {
    public int findDuplicate(int[] nums) {
        Set<Integer> hs=new HashSet<>();
        for(int num:nums){
            if(hs.contains(num)) return num;
            hs.add(num);
        }
        return 0;
            }
}
```

# 3. Time Complexity

* **O(n)** time (amortized), where `n = nums.length`.
  Reason: the loop visits each element at most once. `HashSet` operations `contains()` and `add()` are O(1) on average (amortized). So total work ≈ constant × `n`.

Note: In adversarial worst-cases with many hash collisions, a single `contains` or `add` can degrade, but typical analysis uses amortized O(1) for Java's `HashSet`.

# 4. Space Complexity

* **O(n)** extra space in the worst case.
  Reason: in the worst case (no duplicate until the end), the `HashSet` will store up to `n-1` distinct integers, so additional memory grows linearly with `n`.

# 5. Assumptions / Constraints

* The code assumes `nums` is non-`null`. If `nums` is `null` the for-each will throw `NullPointerException`.
* The code returns `0` if no duplicate is found — that is a sentinel value and may be ambiguous if `0` is a valid array element or in contexts where a duplicate is guaranteed by problem statement.
* Values are treated as `int`; negative numbers are allowed (the algorithm still works).
* No imports are shown; in a full file you need `import java.util.Set; import java.util.HashSet;` (or `import java.util.*;`).
* If the problem guarantees there is at least one duplicate (e.g., typical LeetCode variant where `nums.length = n+1` and values in `[1..n]`), this method correctly returns some duplicate.

# 6. Optimizations (if any)

* **Space → O(1)**: If the problem guarantees numbers are within a known range (e.g., `1..n` with `n+1` elements), use Floyd's Tortoise and Hare (cycle detection) to find the duplicate in O(n) time and O(1) extra space.

  ```java
  // Floyd's Tortoise and Hare (works when numbers are 1..n and array length is n+1)
  public int findDuplicateFloyd(int[] nums) {
      int tortoise = nums[0];
      int hare = nums[0];
      do {
          tortoise = nums[tortoise];
          hare = nums[nums[hare]];
      } while (tortoise != hare);
      tortoise = nums[0];
      while (tortoise != hare) {
          tortoise = nums[tortoise];
          hare = nums[hare];
      }
      return hare;
  }
  ```
* **Modify input (O(1) space)**: If allowed to mutate `nums` and values are in a restricted range, you can mark visited indices by negating `nums[index]`. (Careful: destructive and requires known value range.)
* **Sort then scan**: Sort `nums` (O(n log n)) and scan adjacent pairs for equality; uses O(1) extra space if in-place sort is allowed, but it changes input order and is slower than the `HashSet` approach for large `n`.
* **Boolean/bitset**: If values are known to be within a small range, use a `boolean[] seen = new boolean[maxValue+1]` which is faster and avoids boxing; space equals range size.

Trade-offs: Floyd gives O(1) space but requires the specific range property. `HashSet` is simple, fast, and general-purpose.

# 7. Edge Cases

Handled / behavior as-is:

* **Multiple duplicates**: returns the first number that repeats during left→right scan — not necessarily the smallest or most frequent duplicate.
* **Duplicate value is `0`**: correctly detected and returned (but ambiguous because the function returns `0` also when no duplicate is found).
* **Negative numbers**: fine — `HashSet` handles negative ints.

Potentially problematic / not handled explicitly:

* **`nums == null`** → throws `NullPointerException`. Better to check and throw `IllegalArgumentException` or return a sentinel/`OptionalInt`.
* **Empty array or single-element array** → loop never finds a duplicate; method returns `0` (may be unexpected).
* **No duplicate present** → returns `0` (ambiguous if `0` is a valid element). Prefer throwing or returning an `OptionalInt` in robust APIs.
* **Very large arrays** → memory pressure because `HashSet` holds up to O(n) boxed `Integer` objects (boxing overhead + `HashSet` overhead).
* **Thread-safety**: not thread-safe; concurrent modifications by multiple threads will break behavior (no synchronization).

---

