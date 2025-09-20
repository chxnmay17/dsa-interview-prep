https://leetcode.com/problems/trapping-rain-water/

# 1. Algorithm 

* Maintain two pointers `l` (left) and `r` (right) at the ends of the array.
* Keep `lmax` = highest height seen so far from the left, and `rmax` = highest height seen so far from the right.
* On each iteration:

  * Update `lmax` with `h[l]` and `rmax` with `h[r]`.
  * Compare `lmax` and `rmax`.

    * If `lmax < rmax`, the water trapped above `h[l]` is limited by `lmax` (the smaller side), so add `lmax - h[l]` to the answer and move `l` right (increment).
    * Otherwise (including `lmax == rmax`), the water trapped above `h[r]` is limited by `rmax`, so add `rmax - h[r]` to the answer and move `r` left (decrement).
* Stop when `l >= r`. The accumulated `ans` is total trapped water.

Why this works (intuitive): water level at any position is determined by the smaller of the tallest bar to its left and tallest to its right. By always moving the pointer on the side with the smaller max, you ensure the smaller boundary is known and fixed, so the trapped water calculation for that side is correct.

# 2. Code

```java
public class Solution {
    public int trap(int[] h) {
        int l = 0, r = h.length - 1, lmax = Integer.MIN_VALUE, rmax = Integer.MIN_VALUE, ans = 0;
        while (l < r) {
        lmax = Math.max(lmax, h[l]);
        rmax = Math.max(rmax, h[r]);
        ans += (lmax < rmax) ? lmax - h[l++] : rmax - h[r--];
        }
        return ans;
    }
}
```

# 3. Time Complexity

**O(n)**, where `n = h.length`.

Justification: The `while (l < r)` loop moves either `l` forward or `r` backward on each iteration. Each pointer moves at most `n` steps total, so the loop executes at most `n` times. All operations inside the loop are O(1).

# 4. Space Complexity

**O(1)** — constant extra space.

Justification: The algorithm uses a fixed number of integer variables (`l, r, lmax, rmax, ans`) regardless of input size. No arrays or recursion are used.

# 5. Assumptions / Constraints

* The code expects `h` to be a non-`null` integer array. If `h == null` it will throw a `NullPointerException`.
* Typical problem assumption: heights are non-negative integers (≥ 0). The algorithm concept assumes heights represent vertical bars.
* The code uses `int` for accumulation; if heights and `n` are large, `ans` could overflow a 32-bit signed integer.
* `Integer.MIN_VALUE` is used to initialize `lmax`/`rmax` but they are immediately updated with `Math.max(..., h[?])` before use — so this works as long as `h` contains valid ints (which it does). Using `0` or first/last element would be clearer.
* The decision on equality (`lmax < rmax` uses `<`, not `<=`) means when `lmax == rmax` the code processes the right pointer.

# 6. Optimizations (if any)

The implementation is already the optimal two-pointer approach (O(n) time, O(1) space). Suggested small improvements for robustness/readability:

1. **Initialize `lmax` and `rmax` more clearly**:

   * Use `0` (or `h[0]`/`h[h.length-1]` when `h.length>0`) instead of `Integer.MIN_VALUE`. This avoids confusion.
   * Example: `int lmax = 0, rmax = 0;`

2. **Handle null and very small arrays early**:

   * Add `if (h == null || h.length < 3) return 0;` — no water can be trapped if fewer than 3 bars.

3. **Avoid integer overflow for large inputs**:

   * Use `long ans = 0L;` and return `(int)ans` or adapt return type to `long` if necessary (depends on constraints).

4. **Code clarity**:

   * Use descriptive variable names (`left`, `right`, `leftMax`, `rightMax`, `water`) and consistent indentation.

These changes make the code safer and clearer while preserving the same complexity.

# 7. Edge Cases

Handled correctly by code:

* **Empty array** (`h.length == 0`): The loop doesn't run; returns `0`.
* **Arrays of length 1 or 2**: No iterations (since `l < r` false quickly); returns `0` — correct, no water trapped.
* **Monotonic increasing or decreasing arrays**: Algorithm returns `0` — correct.
* **Plateaus / equal adjacent heights**: Handled naturally; equality case uses right pointer (no correctness loss).

Potential problems / unhandled edge cases:

* **`h == null`**: throws `NullPointerException`. Add null check if needed.
* **Integer overflow**: If `h` values and `n` are large (e.g., heights near `10^9` and many bars), `ans` may overflow `int`. Use `long`.
* **Negative heights**: Problem specification normally forbids negative heights; behavior is undefined for negative values (initialization with `Integer.MIN_VALUE` prevents early issues but semantics of "water" with negative heights are ill-defined).
* **Unclear initialization style**: Using `Integer.MIN_VALUE` is unconventional; it works here because `lmax` and `rmax` are updated before use, but it's fragile and confusing.

---

