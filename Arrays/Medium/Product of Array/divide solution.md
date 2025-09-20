https://leetcode.com/problems/product-of-array-except-self/

# 1. Algorithm 

1. Walk the array once and compute:

   * `prod` = product of all non-zero elements.
   * `zeroCount` = number of zeros in the array.
2. If there are more than one zero, every element's product-except-self is `0` → return an all-zero array.
3. Otherwise, build the result array:

   * If exactly one zero: the result at index `i` is `prod` if `nums[i] == 0`, else `0`.
   * If no zeros: the result at index `i` is `prod / nums[i]` (division is safe because `nums[i] != 0`).
4. Return the result.

# 2. Code

```java
public class Solution {
    public int[] productExceptSelf(int[] nums) {
        int prod = 1, zeroCount = 0;
        for (int num : nums) {
            if (num != 0) {
                prod *= num;
            } else {
                zeroCount++;
            }
        }

        if (zeroCount > 1) {
            return new int[nums.length];
        }

        int[] res = new int[nums.length];
        for (int i = 0; i < nums.length; i++) {
            if (zeroCount > 0) {
                res[i] = (nums[i] == 0) ? prod : 0;
            } else {
                res[i] = prod / nums[i];
            }
        }
        return res;
    }
}
```

# 3. Time Complexity

**O(n)** — Explanation:

* First loop computes `prod` and `zeroCount` in O(n).
* Second loop computes the result in O(n).
* Total = O(n) + O(n) = O(n).

# 4. Space Complexity

**O(n)** overall (if you count output), **O(1)** extra auxiliary space:

* The function allocates an output array `res` of size `n` → unavoidable if you must return all elements, so O(n).
* Besides the output, only a few integer variables (`prod`, `zeroCount`, loop index) are used → O(1) auxiliary space.

# 5. Assumptions / Constraints

* Inputs are Java `int` values (32-bit signed).
* `nums.length` can be 0 (code handles it — returns empty `res`) or ≥1.
* The code assumes integer multiplication and integer division semantics.
* It assumes the final `prod` (product of non-zero elements) fits in an `int` (no overflow).
* If `zeroCount == 0`, every `nums[i]` is non-zero so `prod / nums[i]` is defined.
* No requirement of sorting stability or special character sets (not applicable).

# 6. Optimizations (if any)

Suggestions to improve robustness or avoid pitfalls:

1. **Avoid overflow**:

   * Replace `int prod` with `long prod` to reduce overflow risk.
   * For guaranteed correctness with huge magnitudes, use `BigInteger` (slower).
2. **Remove division (preferred in interview settings)**:

   * Use prefix and suffix products to compute result without division:

     * Build `left[i]` = product of `nums[0..i-1]` in one pass (or accumulate on the fly).
     * Multiply by `right[i]` = product of `nums[i+1..n-1]` in another pass (or accumulate from right).
   * This yields O(n) time, O(1) extra space if you reuse output array for left products and a running right product.
   * Advantage: avoids division issues and handles zeros naturally.
3. **Early returns**:

   * If `nums.length <= 1`, you can return `[1]` for single element (current code already does this).
4. **Use `long` with prefix-suffix** — combines overflow reduction and division-free approach.

Example sketch (division-free, O(n) time, O(1) extra):

```java
int[] res = new int[n];
// first pass: res[i] = product of elements left of i
int leftProd = 1;
for (int i = 0; i < n; i++) {
    res[i] = leftProd;
    leftProd *= nums[i];
}
// second pass: multiply by product of elements right of i
int rightProd = 1;
for (int i = n - 1; i >= 0; i--) {
    res[i] *= rightProd;
    rightProd *= nums[i];
}
```

(Use `long` types if values may overflow.)

# 7. Edge Cases

Handled by the code:

* **Multiple zeros** (`zeroCount > 1`): returns all zeros.
* **Exactly one zero**: only the position of the zero gets the product of all non-zero elements; others are 0.
* **No zeros**: uses division `prod / nums[i]` to get product except self.
* **Single-element array**: returns `[1]` (since prod/nums\[0] = 1).

Potential issues / things that could break or behave unexpectedly:

* **Integer overflow**: `prod *= num` can overflow the `int` range silently and produce wrong results. Example: product of many large numbers or certain combinations (like `Integer.MIN_VALUE * -1`) may overflow.
* **Division rounding**: not an issue here because division is exact in the mathematical sense when no overflow occurs (prod is product of all nums, so divisible by each `nums[i]`), but if overflow altered `prod`, integer division could give wrong results.
* **Very large arrays**: product magnitude grows quickly — overflow risk increases.
* **Mutable input expectation**: code doesn't modify `nums` (good), but assumptions about immutability are fine.
* **Zero / negative combos**: negatives are handled correctly if no overflow occurs.

---


