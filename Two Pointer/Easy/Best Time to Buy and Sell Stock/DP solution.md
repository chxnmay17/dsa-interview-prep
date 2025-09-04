

# 1. Algorithm 

1. Keep two variables:

   * `minBuy`: the smallest price seen so far (the best day to buy up to the current day).
   * `maxP`: the maximum profit found so far.
2. Initialize `minBuy` to the first price and `maxP` to 0.
3. Iterate over the price list from left to right. For each current price (`sell`):

   * Compute the profit if we bought at `minBuy` and sold now: `sell - minBuy`. Update `maxP` if this profit is larger.
   * Update `minBuy` to the smaller of itself and the current `sell` (so future profits can use a cheaper buy).
4. After scanning all prices, `maxP` holds the maximum possible profit for exactly one buy-sell pair (or 0 if no profitable transaction exists).

Why this is correct (brief): at each sell day we are effectively checking the best possible buy day before it (tracked by `minBuy`). The algorithm considers every sell day with the best earlier buy, so it finds the global maximum difference `prices[j] - prices[i]` with `j > i`.

Small example (illustrative):
`prices = [7,1,5,3,6,4]`

* After scanning, `minBuy` updates to 1, and `maxP` becomes 5 (buy at 1, sell at 6).

# 2. Code

```java
public class Solution {
    public int maxProfit(int[] prices) {
        int maxP = 0;
        int minBuy = prices[0];

        for (int sell : prices) {
            maxP = Math.max(maxP, sell - minBuy);
            minBuy = Math.min(minBuy, sell);
        }
        return maxP;
    }
}
```

# 3. Time Complexity

**O(n)**, where `n = prices.length`.

**Justification:** The code makes a single pass over the `prices` array. Each iteration does a fixed number of constant-time operations (two comparisons/assignments). Therefore total time grows linearly with `n`.

# 4. Space Complexity

**O(1)** (constant extra space).

**Justification:** Only a fixed number of integer variables (`maxP`, `minBuy`, and the loop variable) are used regardless of input size. No additional arrays or dynamic data structures are allocated.

# 5. Assumptions / Constraints

* The code assumes `prices` is **non-null** and has at least one element (`prices[0]` is accessed).
* The problem is the *single transaction* variant: at most one buy and one sell are allowed.
* Time order matters (you must buy before you sell).
* Price values are `int`. If values can be extremely large (near `Integer.MIN_VALUE`/`Integer.MAX_VALUE`) the subtraction `sell - minBuy` could theoretically overflow.
* No transaction fees, no cooldowns, and you can both buy and sell on the same day (which yields profit 0 for that day).
* Sorting stability or character set not relevant.

# 6. Optimizations (if any)

* **Defensive checks**: handle `null` or empty arrays to avoid exceptions.
* **Loop from index 1**: a typical micro-cleanup — start the loop at index 1 because `minBuy` is initialized from `prices[0]`. That clarifies intent.
* **Use `long` if input range might overflow `int`**: if prices can be outside normal ranges, using `long` for intermediate differences prevents overflow.
* **Readability**: rename variables to `minPrice` and `maxProfit` for clarity.

Suggested improved (safe) variant:

```java
public class Solution {
    public int maxProfit(int[] prices) {
        if (prices == null || prices.length < 2) return 0; // handle empty/null or single element
        int maxP = 0;
        int minBuy = prices[0];

        for (int i = 1; i < prices.length; i++) {
            int sell = prices[i];
            maxP = Math.max(maxP, sell - minBuy);
            minBuy = Math.min(minBuy, sell);
        }
        return maxP;
    }
}
```

If worried about overflow:

```java
long minBuy = prices[0];
long maxP = 0;
for (int i = 1; i < prices.length; i++) {
    long profit = (long)prices[i] - minBuy;
    if (profit > maxP) maxP = profit;
    if (prices[i] < minBuy) minBuy = prices[i];
}
return (int) maxP; // or return long if API allows
```

# 7. Edge Cases

Handled correctly by original code:

* **Monotonic decreasing prices** (e.g., `[7,6,4,3,1]`) → `maxP` stays `0` (no profitable transaction).
* **All equal prices** → returns `0`.
* **Single-element array** (e.g., `[5]`) → original code returns `0` because `sell - minBuy` will be `0` on that one iteration.

Potential problems / not handled:

* **Empty array (`prices.length == 0`)** → original code throws `ArrayIndexOutOfBoundsException`.
* **Null array** → throws `NullPointerException`.
* **Integer overflow** if `prices` values are extreme (difference may overflow `int`). Unlikely for normal stock price inputs but possible in adversarial inputs.
* **If the problem variant allows multiple transactions or other constraints** (fees, cooldowns, k transactions) — this code does **not** solve those variants.

---


