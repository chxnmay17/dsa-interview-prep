https://leetcode.com/problems/product-of-array-except-self/
# 1. Algorithm

1. If `nums` has length `n`, create an output array `res` of length `n`.
2. First pass (left → right): fill `res[i]` with the product of all elements *to the left* of `i`.

   * Initialize `res[0] = 1` and a running `product = 1`.
   * For `i` from `1` to `n-1`: multiply `product` by `nums[i-1]` and store it in `res[i]`.
   * After this pass, `res[i] == product(nums[0..i-1])`.
3. Second pass (right → left): maintain a running suffix `product` that contains the product of all elements *to the right* of `i` and multiply `res[i]` by that suffix.

   * Reinitialize `product = 1`.
   * For `i` from `n-1` down to `0`: `res[i] *= product`, then `product *= nums[i]`.
   * After this, `res[i] == product(nums[0..i-1]) * product(nums[i+1..n-1])`, i.e. product of all elements except `nums[i]`.
4. Return `res`.

(Short correctness intuition: the output for position `i` is prefix \* suffix; the two passes compute those separately and multiply them.)

Example quick trace for `nums = [1,2,3,4]`:

* After first pass (prefixes): `res = [1, 1, 2, 6]`  (1, 1, 1*2, 1*2\*3)
* Second pass (apply suffixes): suffix run produces final `res = [24, 12, 8, 6]`.

# 2. Code

```java
class Solution {
    public int[] productExceptSelf(int[] nums) {
        int n=nums.length;
        int[] res=new int[n];
        int product=1;
        res[0]=1;
        for(int i=1;i<n;i++){
            product=nums[i-1]*product;
            res[i]=product;
        }
        product=1;
        for(int i=n-1;i>=0;i--){
            res[i]*=product;
            product*=nums[i];
        }
        return res;
    }
}
```

# 3. Time Complexity

**O(n)**, where `n = nums.length`.

**Justification:** The code makes two linear passes over the array:

* First loop runs `n-1` iterations.
* Second loop runs `n` iterations.
  Each iteration does a constant amount of work (a multiplication and an assignment), so the total work is proportional to `n`.

# 4. Space Complexity

* **Output-inclusive:** **O(n)** because the `res` array of size `n` is used to store the result.
* **Auxiliary (extra) space:** **O(1)** — only a few scalar variables (`product`, loop counters) are used in addition to the output array.

(If the problem counts the output array as extra space, then it's O(n). If the output is excluded by definition, the algorithm uses O(1) extra space.)

# 5. Assumptions / Constraints

* `nums` is non-`null` and (implicitly) `nums.length >= 1`. If `nums.length == 0`, the line `res[0]=1;` will throw `ArrayIndexOutOfBoundsException`.
* Elements are `int`. The code uses `int` for the running product, so **integer overflow** is possible if products exceed `Integer.MAX_VALUE` or drop below `Integer.MIN_VALUE`.
* The algorithm does **not** use division (so it is safe when zeros are present).
* The result fits into `int` under the assumed input constraints. (If not, you must change types or use `BigInteger`.)
* The input order is irrelevant; this algorithm preserves the original `nums` array (it does not modify `nums`).

# 6. Optimizations (if any)

The implementation is already the common optimal solution in terms of asymptotics: **O(n)** time and **O(1)** extra space (excluding output). Suggested practical improvements:

* **Null / empty check**: add a guard to safely handle `null` or empty arrays:

  ```java
  if (nums == null || nums.length == 0) return new int[0];
  ```
* **Avoid integer overflow**:

  * Use `long product` for intermediate accumulation:

    ```java
    long product = 1L;
    ```

    then cast when storing into `res` (if you are sure final values fit in int).
  * If you cannot guarantee fits-in-int, use `long[]` for `res` or `BigInteger` for fully safe arbitrary-size products.
* **Alternate zero-aware approach** (slightly different trade-offs): count zeros and compute product of non-zero elements. This can let you use one pass to compute `prod` and zero-count, then another pass to fill results using division (when zeroCount == 0) or placing `prod` at single zero position (when zeroCount == 1). That approach:

  * Is also O(n) time, O(1) extra space.
  * Uses division (so not suitable if the problem forbids division).
  * Avoids the two-pass prefix-suffix method but has the same complexity.
* **Micro-optimizations**: small code hygiene (local variable for `n`, avoid recomputing lengths in loop conditions — already done).

If overflow is a realistic concern, the most robust fix is either use `long`/`long[]` or `BigInteger`, or use counting-zeros + `BigInteger` as required.

# 7. Edge Cases

* **Empty array (`nums.length == 0`)** — current code will throw due to `res[0]=1`. Add a guard if you want to accept empty input.
* **Single element** (`n == 1`) — code returns `[1]`, which is correct for the usual problem spec.
* **Zeros in array** — handled correctly by this approach:

  * 0 zeros → each `res[i]` becomes product of all elements divided by `nums[i]` conceptually (computed via prefix/suffix).
  * 1 zero → only the index at the zero position gets product of non-zero elements; all other positions are 0.
  * ≥2 zeros → all outputs are 0.
* **Negative numbers** — handled correctly (signs propagate through multiplication).
* **Integer overflow** — not handled. Large absolute values or long arrays can overflow `int` during accumulation and produce incorrect results. Consider `long` or `BigInteger` if overflow is possible.
* **`nums == null`** — current code will NPE/throw; add null guard if API might pass null.

---

