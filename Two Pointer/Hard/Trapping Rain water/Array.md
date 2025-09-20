https://leetcode.com/problems/trapping-rain-water/


# 1. Algorithm 

1. Read `n = height.length`. If the array is empty (`n == 0`) return 0 (no bars → no water).
2. Build a `leftMax` array where `leftMax[i]` = tallest height from `height[0]` to `height[i]`.
3. Build a `rightMax` array where `rightMax[i]` = tallest height from `height[i]` to `height[n-1]`.
4. For each position `i`, the water that can be held at `i` is `min(leftMax[i], rightMax[i]) - height[i]` (because water is bounded by the smaller of the two boundaries).
5. Sum these values for all `i` into `res`.
6. Return `res`.

Short intuition: for each index you need the highest wall on its left and on its right; water at that index is the gap between the shorter boundary and the bar height.

# 2. Code

```java
public class Solution {
    public int trap(int[] height) {
        int n = height.length;
        if (n == 0) {
            return 0;
        }

        int[] leftMax = new int[n];
        int[] rightMax = new int[n];

        leftMax[0] = height[0];
        for (int i = 1; i < n; i++) {
            leftMax[i] = Math.max(leftMax[i - 1], height[i]);
        }

        rightMax[n - 1] = height[n - 1];
        for (int i = n - 2; i >= 0; i--) {
            rightMax[i] = Math.max(rightMax[i + 1], height[i]);
        }

        int res = 0;
        for (int i = 0; i < n; i++) {
            res += Math.min(leftMax[i], rightMax[i]) - height[i];
        }
        return res;
    }
}
```

# 3. Time Complexity

**O(n)** — justification:

* Building `leftMax` is a single pass (O(n)).
* Building `rightMax` is a single pass (O(n)).
* Final loop summing trapped water is a single pass (O(n)).
  No nested loops; constant work per element. Total = O(n) + O(n) + O(n) = O(n).

# 4. Space Complexity

**O(n) extra** — justification:

* Two auxiliary arrays `leftMax` and `rightMax` of length `n` → O(n) extra space.
* A few constant-size variables (`res`, loop counters) → O(1).
  If you count input array, overall storage is O(n), but extra workspace is O(n).

# 5. Assumptions / Constraints

* `height` is not `null`. (The code checks only `n == 0`; calling `height.length` on a `null` will throw `NullPointerException`.)
* Heights are `int` values. Typical problem assumption: non-negative integers.
* The code assumes normal integer arithmetic — the sum `res` is an `int`. If inputs are very large and the trapped water sum exceeds `Integer.MAX_VALUE`, `res` could overflow.
* No sorting stability or ordering assumptions are required.
* Works for `n = 0,1,2` and returns 0 (no trapped water).

# 6. Optimizations (if any)

**Main optimization**: reduce extra space from O(n) to O(1) using the two-pointer approach.

Idea (two-pointer):

* Keep two pointers `left` and `right`.
* Keep running `leftMax` and `rightMax` scalars.
* Move the pointer whose current height is smaller, and add trapped water using the corresponding scalar max. This computes water on-the-fly with a single pass.

Optimized Java implementation (O(n) time, O(1) extra space):

```java
public int trap(int[] height) {
    if (height == null || height.length < 3) return 0;

    int left = 0, right = height.length - 1;
    int leftMax = 0, rightMax = 0;
    long res = 0; // use long to avoid intermediate overflow

    while (left < right) {
        if (height[left] < height[right]) {
            if (height[left] >= leftMax) leftMax = height[left];
            else res += (leftMax - height[left]);
            left++;
        } else {
            if (height[right] >= rightMax) rightMax = height[right];
            else res += (rightMax - height[right]);
            right--;
        }
    }

    return (int) res; // careful: cast may overflow if res > Integer.MAX_VALUE
}
```

Notes:

* This version uses O(1) extra space and still runs in O(n).
* Use `long res` if you fear integer overflow; decide how to handle overflow before casting back to `int`.

Other micro-optimizations:

* Early return for `height == null` (add that check).
* Early return for `height.length < 3` (can't trap water).

# 7. Edge Cases

Handled by the code:

* `height.length == 0` → returns `0`.
* Arrays of length 1 or 2 → loops operate and result is `0` (no water).
* Strictly increasing or strictly decreasing arrays → result `0` (no basin).

Potentially problematic / not handled:

* `height == null` → `NullPointerException`. Add a null check.
* Integer overflow: if the total trapped water exceeds `Integer.MAX_VALUE`, `res` (an `int`) will overflow. Use `long` for accumulation if needed.
* Very large `n` with memory constraints — the O(n) auxiliary arrays might be expensive; use the two-pointer O(1) approach.
* Negative heights (if allowed) are unusual in context; algorithm formula still computes mathematically but problem domain normally forbids negatives — clarify input constraints.
* Extremal `int` values: if heights contain `Integer.MIN_VALUE` and `Integer.MAX_VALUE`, subtraction could overflow in extreme contrived cases. This is usually outside normal problem constraints.

---

