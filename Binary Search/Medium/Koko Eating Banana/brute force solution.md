
https://leetcode.com/problems/koko-eating-bananas/
# 1. Algorithm 

1. Start with a candidate eating `speed = 1`.
2. Repeatedly:

   * Compute the total hours required if Koko eats every pile at the current `speed`. For each pile, time needed = `ceil(pile / speed)`. Sum these times into `totalTime`.
   * If `totalTime <= h`, current `speed` is sufficient → return it.
   * Otherwise increase `speed` by 1 and try again.
3. (Note) This uses a brute-force linear search on `speed` starting from 1 and increasing until the condition is met.

# 2. Code

```java
public class Solution {
    public int minEatingSpeed(int[] piles, int h) {
        int speed = 1;
        while (true) {
            long totalTime = 0;
            for (int pile : piles) {
                totalTime += (int) Math.ceil((double) pile / speed);
            }

            if (totalTime <= h) {
                return speed;
            }
            speed++;
        }
    }
}
```

# 3. Time Complexity

* Let `n = piles.length` and let `S` be the speed value returned (the first speed that satisfies the condition).
  Each iteration of the `while` loop does an `O(n)` scan to compute `totalTime`. The loop increments `speed` from 1 up to `S`.
  So time complexity = **O(n \* S)**.
* In the worst case `S` can be `maxPile` (the largest pile), so worst-case complexity = **O(n \* M)** where `M = max(piles)`.
  This is **very slow** if piles contain large values (e.g. up to 10^9).
* Best case: if `speed = 1` already works, complexity = **O(n)**.

# 4. Space Complexity

* Uses constant extra space (a few variables, `totalTime`, `speed`).
* Excluding input storage, space complexity = **O(1)**.

# 5. Assumptions / Constraints

* `piles` is an `int[]` and each `pile` is non-negative (typically positive in the problem statement).
* `h` is a non-negative integer.
* The code assumes it is always possible to finish within `h` hours for some finite `speed`. If that is not guaranteed, the `while(true)` loop can run indefinitely (or until integer overflow).
* Numeric safety:

  * The code sums per-pile times into a `long totalTime` which prevents overflow for realistic constraints.
  * Each `ceil` is cast to `int`. Since each per-pile time ≤ `pile`, casting to `int` is safe when `pile` fits in `int`.
* Sorting/stability/character set are irrelevant here.

# 6. Optimizations (suggestions)

This brute-force approach is easily replaced by a much faster **binary search** on the speed domain `[1, maxPile]`.

Improvements:

* Upper bound for speed = `maxPile`. There is no need to try speeds > `maxPile` (because speed ≥ maxPile makes each pile take at most 1 hour).
* Replace floating-point `Math.ceil((double)pile/speed)` with integer arithmetic: `(pile + speed - 1) / speed` (avoids double casts and is faster / exact).
* If `h < n` (less hours than number of piles), it's impossible (even speed > maxPile gives at least one hour per pile). Handle this explicitly instead of looping forever.
* Binary search complexity: **O(n log M)** (n per check, log M checks), drastically faster than O(n \* M).

Example optimized Java implementation (binary search + integer ceil):

```java
public class Solution {
    public int minEatingSpeed(int[] piles, int h) {
        int n = piles.length;
        if (n == 0) return 0;                 // or throw depending on spec
        if (h < n) return -1;                 // impossible: at least 1 hour per pile

        int maxPile = 0;
        for (int p : piles) maxPile = Math.max(maxPile, p);

        int low = 1, high = maxPile;
        while (low < high) {
            int mid = low + (high - low) / 2;
            long total = 0;
            for (int p : piles) {
                total += (p + mid - 1) / mid; // integer ceil
                if (total > h) break;         // small micro-optimization
            }
            if (total <= h) {
                high = mid;
            } else {
                low = mid + 1;
            }
        }
        return low;
    }
}
```

This returns the minimum valid speed (or `-1` if impossible under the `h < n` check) and runs in **O(n log M)** time and **O(1)** extra space.

# 7. Edge Cases (handled / potential problems)

Handled or implicit:

* `pile == 0` → adds 0 hours (works).
* Large sum of times → `totalTime` is `long`, so sum overflow is unlikely for realistic constraints.

Potential problems / not handled:

* **Impossible case (`h < n`)**: The original code will never terminate (or will behave incorrectly after integer overflow). You should explicitly check and handle `if (h < piles.length)`.
* **Integer overflow of `speed`**: If the loop never finds a speed and `speed` keeps incrementing, `speed` may eventually overflow `int` causing unpredictable results. A fixed upper bound (maxPile) prevents that.
* **Floating point inefficiency / tiny rounding risk**: Using `Math.ceil((double) pile / speed)` is correct here but slower and unnecessary; prefer integer arithmetic for exactness and speed.
* **Empty `piles`**: Original returns `speed = 1` immediately because `totalTime` = 0. Depending on spec you might prefer `0` or an error.
* **Negative values**: If `piles` contains negative numbers (not expected), behavior is undefined — assume non-negative piles by problem definition.

---

