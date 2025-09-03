https://leetcode.com/problems/two-sum/

# 1. Algorithm 

1. Create an empty hash map `hm` that maps a number → its index in the array.
2. Iterate through the input array `nums` with index `i`.
3. For each number `nums[i]` compute the `complement = target - nums[i]`.
4. If `complement` is already in the map, we found a pair: return `[index_of_complement, i]`.
5. Otherwise store the current number and its index in the map: `hm.put(nums[i], i)`.
6. Continue until you find and return the pair. (The code assumes a valid pair exists, so it never intentionally falls through.)

Effect: one-pass scan that finds the two indices whose values sum to `target`. Indices are 0-based and returned as `[earlierIndex, currentIndex]`.

# 2. Code

```java
import java.util.HashMap;

class Solution {
    public int[] twoSum(int[] nums, int target) {
        HashMap<Integer, Integer> hm = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            int complement = target - nums[i];
            if (hm.containsKey(complement)) {
                return new int[] { hm.get(complement), i };
            }
            hm.put(nums[i], i);
        }
        // No need for fallback or exception if a solution is guaranteed
        return null; // This line is never reached
    }
}
```

# 3. Time Complexity

**O(n)** on average, where `n = nums.length`.

**Justification:** the loop visits each element exactly once (O(n) iterations). Each iteration does a constant-time hash lookup (`containsKey`) and put (`put`) — these are O(1) on average for Java `HashMap`. So the whole algorithm is O(n) average-time.

*(Notes on worst-case: with pathological hash collisions, HashMap operations could degrade, but Java 8+ converts long buckets to trees so worst-case becomes O(log n) per op; practically this remains O(n) for normal inputs.)*

# 4. Space Complexity

**O(n)** extra space.

**Justification:** in the worst case (no complement found until the end), the map stores each element once, so it needs space proportional to `n` (one map entry per array element). The returned array of size 2 is O(1) extra.

# 5. Assumptions / Constraints

* The method expects `nums` and `target` as 32-bit Java `int`.
* Assumes at least one valid pair exists (this is stated in many problem variants like LeetCode Two Sum). If not guaranteed, the function currently returns `null`.
* Indices returned are 0-based.
* HashMap operations are assumed to be O(1) average (typical).
* Input array fits in memory (storing a map of up to `n` entries is feasible).
* No concurrency considerations (not thread-safe).
* No special character sets or sorting stability concerns (numeric array only).

# 6. Optimizations (if any)

* **Pre-size the HashMap** to avoid rehashing:
  `HashMap<Integer,Integer> hm = new HashMap<>((int)(nums.length / 0.75f) + 1);`
  This reduces rehash work and small constant overhead for large `n`.
* **Use a primitive int→int map** (e.g., fastutil / Trove) to avoid boxing overhead of `Integer` if performance/memory is critical.
* **Input validation**: check `if (nums == null || nums.length < 2)` early and throw `IllegalArgumentException` to clarify contract.
* **Return policy**: instead of `return null`, throw an exception (e.g., `throw new IllegalArgumentException("No two sum solution");`) if no pair exists — this is safer than returning `null`.
* **If value range is small and known**, you can use an int-indexed array as a map for O(1) lookups without hashing (trades memory for speed).
* **If minimizing memory and indices need not correspond to original positions**, you could sort + two-pointer in O(n log n) time and O(1) space (but sorting loses original indices unless you store them).

# 7. Edge Cases

* **`nums == null`:** the code will throw `NullPointerException` when accessing `nums.length`. *Not handled* — add a null check if needed.
* **`nums.length < 2`:** loop is safe but no pair exists; current code returns `null`. Better to validate and throw.
* **Multiple valid pairs:** the code returns the first pair encountered during the left-to-right scan (index order depends on iteration).
* **Duplicate values:** handled correctly. Example `nums = [3,3], target = 6` → returns `[0,1]`.
* **Negative numbers / zero:** handled correctly — arithmetic and hash lookups work for negatives.
* **Integer overflow:** arithmetic uses `int` so standard Java overflow/wrap-around semantics apply. In typical problem constraints this isn't an issue because `target` is an int; if you expect sums outside `int` range, use `long` for intermediate computations and validate accordingly.
* **No solution present:** the implementation returns `null` (may lead to `NullPointerException` in callers). If solutions are not guaranteed, replace `return null` with a thrown exception or a documented contract.
* **Thread-safety:** not safe for concurrent use (not an issue for usual single-threaded problem solving).

---


