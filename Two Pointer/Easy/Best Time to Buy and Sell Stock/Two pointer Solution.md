
# 1. Algorithm 

* Maintain two indices:

  * `left` = index of the candidate buy day (initially 0).
  * `right` = index of the candidate sell day (initially 1).
* Walk `right` from 1 to end of array:

  * If `prices[right] > prices[left]`, compute potential profit `prices[right] - prices[left]` and update `maxP` if it's larger.
  * Else (found a new lower price), move `left` to `right` because that lower price is a better buy candidate.
  * Increment `right`.
* Return `maxP` (0 if no profitable transaction exists).
* Invariant: `left` always points to the smallest price seen so far up to `right-1`, so `prices[right] - prices[left]` is the best profit with sell at `right`.

# 2. Code

```java
class Solution {
    public int maxProfit(int[] prices) {
        int left=0,right=1;
        int maxP=0;
        while(right<prices.length){
            if(prices[left]<prices[right]) maxP=Math.max(maxP,prices[right]-prices[left]);
            else left=right;
            right++; 
        }
        return maxP;
    }
}
```

# 3. Time Complexity

**O(n)**, where `n = prices.length`.
Justification: `right` increments exactly `n-1` times and each loop body does only O(1) work (constant comparisons and assignments). `left` only moves forward (never back), so each element is examined a constant number of times.

# 4. Space Complexity

**O(1)** extra space.
Justification: Only a fixed number of integer variables (`left`, `right`, `maxP`) are used regardless of input size. The input array is not counted as extra space.

# 5. Assumptions / Constraints

* `prices` is an `int[]` (array of integers).
* The algorithm assumes a single buy-and-then-sell transaction (no multiple transactions).
* No transaction fees, no cooldowns, and you must buy before you sell (sell index > buy index).
* If `prices` is `null`, the code will throw `NullPointerException` — input should be non-null.
* Works with `prices.length == 0` or `1` (returns 0).
* Assumes values fit in Java `int`. If values may be near `Integer.MAX_VALUE`, consider using `long` to avoid overflow when subtracting (rare for stock prices but possible in other contexts).

# 6. Optimizations (if any)

The algorithm is already optimal in time and space for this problem (O(n) time, O(1) space). Small practical improvements / style changes:

* Use a named variable `minPrice` instead of `left` to make intent clearer:

  ```java
  int minPrice = Integer.MAX_VALUE, maxP = 0;
  for (int p : prices) {
      if (p < minPrice) minPrice = p;
      else maxP = Math.max(maxP, p - minPrice);
  }
  ```
* Early return: if `prices.length < 2` return 0 immediately — this is minor but explicit.
* Use `long` for intermediate calculation if input domain could overflow `int`.
* Rename `left`/`right` to `buyIdx`/`sellIdx` (readability).

# 7. Edge Cases

* **Handled correctly**:

  * Empty array (`[]`) → returns `0`.
  * Single-element array (`[x]`) → returns `0`.
  * Strictly decreasing prices (e.g., `[7,6,4,3,1]`) → returns `0` (no profit possible).
  * Repeated equal prices (e.g., `[3,3,3]`) → returns `0`.
* **Potentially problematic**:

  * `prices == null` → `NullPointerException`. Validate input if necessary.
  * Extremely large integer values near `Integer.MAX_VALUE` could cause overflow in some contexts; using `long` for calculations avoids this.
* **Behavioral constraints**:

  * The algorithm allows only one buy and one sell — if multiple transactions were allowed, different algorithm needed.

---

