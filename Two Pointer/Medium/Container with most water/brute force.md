https://leetcode.com/problems/container-with-most-water/

# 1. Algorithm 

1. Read the array `height` of size `n`.
2. Initialize `res = 0` to hold the maximum area found.
3. For every pair of indices `(i, j)` with `0 ≤ i < j < n`:

   * Compute the limiting height `min(height[i], height[j])`.
   * Compute the width `(j - i)` and the area `minHeight * (j - i)`.
   * If this area is larger than `res`, update `res`.
4. After checking all pairs, return `res`.

This is a brute-force check of all possible container pairs.

# 2. Code

```java
class Solution {
    public int maxArea(int[] height) {
        int n=height.length;
        int res=0;
        for(int i=0;i<n;i++){
            for(int j=i+1;j<n;j++){
                int minHeight =Math.min(height[i],height[j]);
                res=Math.max(res,minHeight*(j-i));
            }
        }
        return res;
    }
}
```

# 3. Time Complexity

* **O(n²)** (quadratic).
* **Why:** Outer loop runs `n` times; inner loop runs `n-1, n-2, ... , 1` times. Total iterations = `∑_{k=1}^{n-1} k = n(n-1)/2`, which is Θ(n²). Each iteration does O(1) work (a couple of arithmetic/comparison operations), so overall Θ(n²).

# 4. Space Complexity

* **O(1)** additional space (constant).
* **Why:** Only a fixed number of extra integer variables (`n`, `res`, loop indices, `minHeight`) are used. The input array is not counted as extra space.

# 5. Assumptions / Constraints

* `height` is a non-null array of integers. If `height == null` the code will throw `NullPointerException`.
* Typical problem constraints assume non-negative heights (≥ 0). If heights can be negative the geometric interpretation breaks (area could be negative).
* Integer overflow: `minHeight * (j-i)` is computed as `int * int` and stored as `int`. Under usual LeetCode constraints (height ≤ 10⁴ and n ≤ 10⁵), the maximum area is ≤ 10⁹ and fits in `int`. But if heights or `n` are larger, the multiplication could overflow a 32-bit `int`.
* The code assumes you want the maximum area between two distinct indices; it returns `0` if `n < 2` (because no pairs exist).

# 6. Optimizations (if any)

**Best practical optimization:** two-pointer approach — O(n) time, O(1) space.

Idea: place one pointer at left (`L=0`) and one at right (`R=n-1`). Compute area, then move the pointer at the shorter side inward (because moving the taller side cannot increase area when width decreases and limiting height is the shorter side). Repeat until `L >= R`.

Optimized Java (safe with large area if you want `long` result):

```java
// returns int if you trust constraints; use long if you want safe larger ranges
public int maxAreaOptimized(int[] height) {
    int L = 0, R = height.length - 1;
    int res = 0;
    while (L < R) {
        int minH = Math.min(height[L], height[R]);
        res = Math.max(res, minH * (R - L));
        if (height[L] < height[R]) L++;
        else R--;
    }
    return res;
}

// safer version returning long to avoid overflow:
public long maxAreaOptimizedLong(int[] height) {
    int L = 0, R = height.length - 1;
    long res = 0;
    while (L < R) {
        long minH = Math.min(height[L], height[R]);
        res = Math.max(res, minH * (R - L));
        if (height[L] < height[R]) L++;
        else R--;
    }
    return res;
}
```

* **Time:** O(n) — each pointer moves at most `n` steps.
* **Space:** O(1).

**Other minor optimizations for the original brute-force:**

* Early pruning inside the inner loop: if `height[i] * (n-1 - i) ≤ res` then no j > i can beat `res` with `i` fixed — you can `break` the inner loop for that `i`. This is a best-effort heuristic; worst-case still O(n²).
* Avoid repeated calls to `Math.min/Math.max` by using local variables (micro-optimization).

# 7. Edge Cases

**Handled by the code:**

* `height.length == 0` or `1`: loops do nothing, `res` stays `0` → returns `0`. (Semantically reasonable.)
* Zero heights: if all heights are `0`, computes area `0` correctly.

**Potentially problematic / not handled:**

* `height == null`: throws `NullPointerException`. Add a guard: `if (height == null || height.length < 2) return 0;`.
* Negative heights: algorithm will still run but area may be negative; usually heights are defined non-negative—if negatives are possible, clarify intended behavior or validate input.
* Integer overflow: if `height[i]` and `j-i` are large, `minHeight * (j-i)` could overflow `int`. Use `long` for the area calculation or return `long`.
* Performance / TLE: for large `n` (e.g., `n` in tens or hundreds of thousands), the O(n²) brute-force will be too slow — use the two-pointer O(n) approach.
* Single-element arrays: returns `0` (makes sense, but check problem requirement).

---


