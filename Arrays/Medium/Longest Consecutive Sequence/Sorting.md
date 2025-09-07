
https://leetcode.com/problems/longest-consecutive-sequence/
# 1. Algorithm (in simple English)

1. If the input array is empty, return 0 (no sequence).
2. Sort the array in ascending order.
3. Walk the sorted array from left to right, tracking the current consecutive streak length (`count`) and the maximum found so far (`res`).
4. For each element:

   * If it's the same as the previous element, skip it (duplicates don't extend a streak).
   * If it's exactly `previous + 1`, it's consecutive: increment `count` and update `res`.
   * Otherwise (gap > 1), reset `count` to 1 (start a new streak from the current value).
5. After the loop return `res` (length of the longest consecutive sequence).

# 2. Code

(Shown exactly as you provided.)

```java
class Solution {
    public int longestConsecutive(int[] nums) {
        if(nums.length==0) return 0;
        Arrays.sort(nums);
        int count=1;
        int res=1;
        for(int i=1;i<nums.length;i++){
            if(nums[i-1]==nums[i] )continue;
            if(nums[i]!=nums[i-1]+1) count=1;
            else{
                count++;
                res=Math.max(res,count);
                }
        }
        return res;
        
        }
    }
```

# 3. Time Complexity

* **O(n log n)** overall.

  * Sorting the array (Arrays.sort) dominates: **O(n log n)** for `n = nums.length`.
  * The single pass over the sorted array is **O(n)**.
  * Combined: **O(n log n) + O(n) = O(n log n)**.

# 4. Space Complexity

* **Auxiliary space: O(1)** (in-place, ignoring the input array).

  * The algorithm uses a constant number of extra variables (`count`, `res`, loop index).
  * Note: Java's `Arrays.sort(int[])` sorts primitive `int` arrays in-place (dual-pivot quicksort) and requires only a small stack / constant temporary space; if you count sort implementation internals you may consider **O(log n)** auxiliary stack in some implementations. But asymptotically the solution uses constant extra memory beyond the input.

# 5. Assumptions / Constraints

* The code assumes `nums` is **not null**. If `nums == null` it will throw `NullPointerException`.
* Works for negative and positive integers (standard `int` range).
* Duplicates are allowed and are explicitly skipped by the `continue`.
* Requires `java.util.Arrays` to be available/imported (common in typical contexts).
* Sorting changes the input order (i.e., destructive); stability of the sort is irrelevant here.
* If integer overflow is a concern, note `nums[i-1] + 1` can overflow when `nums[i-1] == Integer.MAX_VALUE`. Practically this doesn't create a valid consecutive next value, but it's something to be aware of.

# 6. Optimizations (if any)

* **O(n)** average-time solution (trades time for space): use a `HashSet`.

  * Insert all numbers into a `HashSet`.
  * For each number `x` in the set, if `x-1` is **not** in the set, `x` is a sequence start — then repeatedly check `x+1, x+2, ...` in the set to count the streak.
  * This avoids sorting and runs in **O(n)** average time with **O(n)** extra space.
* Minor improvements to the current approach:

  * Add a `null` check at the start (`if (nums == null) return 0;`).
  * Early return for `nums.length == 1`.
  * Use a `long` cast when doing `nums[i-1] + 1` to avoid overflow: `if ((long) nums[i] == (long) nums[i-1] + 1)`.
  * Reduce repeated indexing by storing `prev = nums[i-1]` in a variable (micro-optimization).
* Example of the HashSet approach (concise Java):

```java
public int longestConsecutive(int[] nums) {
    if (nums == null || nums.length == 0) return 0;
    Set<Integer> set = new HashSet<>();
    for (int n : nums) set.add(n);

    int longest = 0;
    for (int n : set) {
        if (!set.contains(n - 1)) {                // n is sequence start
            int cur = n;
            int len = 1;
            while (set.contains(cur + 1)) {
                cur++;
                len++;
            }
            longest = Math.max(longest, len);
        }
    }
    return longest;
}
```

* Complexity of HashSet version: **Time O(n)** average, **Space O(n)**.

# 7. Edge Cases (handled / potential breakage)

Handled by the code:

* **Empty array** (`nums.length == 0`) → returns `0`.
* **Duplicates** — skipped by `if(nums[i-1]==nums[i]) continue;` so duplicates don't inflate counts.
* **Single element** → returns `1`.

Potential issues / not handled:

* **Null input** (`nums == null`) → throws `NullPointerException`.
* **Integer overflow** when computing `nums[i-1] + 1` (if `nums[i-1] == Integer.MAX_VALUE`). Practically there cannot be a valid `nums[i] == Integer.MAX_VALUE + 1`, but the overflow wraps and comparison behavior should be considered; safer to cast to `long` before adding.
* **Very large arrays** — sorting may be slow or memory-heavy in practice; use HashSet approach if input is huge and memory allows.
* **All equal elements** (e.g., `[5,5,5]`) → returns `1` which is correct.
* **Non-sorted input** — handled because the code sorts the array first.
* **Huge span between min and max** — the algorithm is fine, but if you used `nums[i] - nums[i-1]` as a check you might risk overflow for extreme min/max differences; prefer `(long)` casts if you worry about extremes.

---


