https://leetcode.com/problems/longest-consecutive-sequence/


# 1. Algorithm 

1. If the input array is empty, return 0.
2. Put every number from the array into a `HashSet` (this removes duplicates and gives O(1) average membership testing).
3. Initialize `maxCount = 1` (because with at least one element the smallest possible longest-consecutive length is 1).
4. For every number `num` in the original array:

   * Start a local `count = 1`.
   * Repeatedly increment `num` (`++num`) and check whether the set contains the incremented value; for each found consecutive value increase `count`.
   * After the while-loop ends, update `maxCount` with the larger of `maxCount` and `count`.
5. Return `maxCount`.

In short: build a set, then for each value try to walk forward through consecutive integers using set membership checks, tracking the longest run seen.

# 2. Code

```java
class Solution {
    public int longestConsecutive(int[] nums) {
        
        if(nums.length==0) return 0;
        Set<Integer> numsSet=new HashSet<>();
        for(int num:nums){
            numsSet.add(num);
        }
        int maxCount=1;
        for(int num:nums){

            int count=1;
            while(numsSet.contains(++num)){
                count++;
            }
            maxCount=Math.max(count,maxCount);

        }
        return maxCount;
        
        }
    }
```

# 3. Time Complexity (with justification)

* **Worst-case:** **O(n²)**.
  *Why:* Suppose the array contains a long consecutive sequence of length `n` (e.g. `1..n`). For each element you begin counting forward; the total number of `contains` checks sums to roughly `n + (n-1) + (n-2) + ... + 1 = O(n²)`. Thus the algorithm can do quadratic work in the worst case.
* **Best-case:** **Θ(n)**.
  *Why:* If there are no consecutive neighbors (every `++num` check immediately fails), each element costs O(1) and building the set is O(n), so overall Θ(n).
* **Average-case:** Depends on input distribution. With random (sparse) integers it often behaves near O(n), but **there is no guarantee** — adversarial inputs cause O(n²).

# 4. Space Complexity (with justification)

* **O(n)** extra space.
  *Why:* The `HashSet` stores up to `n` distinct integers (boxed `Integer` objects), so memory scales linearly with input size. Other variables use O(1) extra space.

# 5. Assumptions / Constraints

* The method assumes the input `nums` is non-`null`. (If `nums` can be `null`, code should check that to avoid `NullPointerException`.)
* Integers are standard Java `int` values (32-bit). The code uses `++num` so **wrap-around** (overflow) is possible when `num == Integer.MAX_VALUE`.
* Duplicates in the input are allowed; the set will deduplicate them.
* Input order is arbitrary (unsorted) — the algorithm does not rely on any order.
* Sorting stability or character sets are irrelevant here (numbers only).

# 6. Optimizations (suggestions + improved code)

**Main optimization idea:** Only start counting at the *beginning* of a sequence. For any number `x`, if `x-1` exists in the set, then `x` is not the start of a sequence and you can skip it. Also iterate over the set (not the original array) to avoid repeated starts on duplicates. This makes each element be visited a small constant number of times — overall **O(n)** time (amortized/worst-case) and **O(n)** space.

**Improved, safe Java version (handles `Integer.MAX_VALUE` overflow):**

```java
public int longestConsecutive(int[] nums) {
    if (nums == null || nums.length == 0) return 0;

    Set<Integer> numsSet = new HashSet<>();
    for (int n : nums) numsSet.add(n);

    int maxCount = 0;
    for (int n : numsSet) {
        // only start counting if `n` is the start of a sequence
        if (!numsSet.contains(n - 1)) {
            int current = n;
            int count = 1;
            // avoid overflow when current == Integer.MAX_VALUE
            while (current != Integer.MAX_VALUE && numsSet.contains(current + 1)) {
                current++;
                count++;
            }
            maxCount = Math.max(maxCount, count);
        }
    }
    return maxCount;
}
```

* **Complexity after optimization:** Time **O(n)** (each integer is checked a constant number of times), Space **O(n)**.
* **Other possible optimizations:**

  * If input integer range is small and dense, `boolean[]` or `BitSet` can be more memory/time efficient.
  * Use primitive-specialized hash sets (e.g., Trove or FastUtil `IntSet`) to avoid boxing overhead for large `n`.
  * Sorting approach: sort the array (O(n log n)), then scan to find longest consecutive run — uses less extra memory but slower for very large `n`.

# 7. Edge Cases (handled vs. potentially problematic)

**Handled correctly by given code:**

* Empty array (`nums.length == 0`) → returns `0`.
* Single-element arrays → returns `1`.
* Negative numbers → fine, negatives treated the same as positives.
* Duplicates → deduplicated by the set; the algorithm still returns correct length.

**Potential problems / not handled / pitfalls:**

* **Null input** (`nums == null`) → will throw `NullPointerException`. Add an explicit null-check if `null` is possible.
* **Time blow-up (worst-case O(n²))** for long consecutive ranges (e.g., `1..n`) — may time out for large `n`. Use the optimized start-check to fix.
* **Integer overflow wrap-around:** when `num == Integer.MAX_VALUE`, `++num` overflows to `Integer.MIN_VALUE`. If both `Integer.MAX_VALUE` and `Integer.MIN_VALUE` are present in the input set, that overflow could make the code incorrectly join them as consecutive. The improved version above guards against that by checking `current != Integer.MAX_VALUE` before `current + 1`.
* **Memory pressure** for extremely large arrays since the `HashSet` stores boxed `Integer` objects (heavy per-element overhead). Primitive sets can reduce this.

---


