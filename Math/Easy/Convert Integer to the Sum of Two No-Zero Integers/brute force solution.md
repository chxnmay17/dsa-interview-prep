Not in Neetcode's 150
---
https://leetcode.com/problems/convert-integer-to-the-sum-of-two-no-zero-integers/description/

# 1. Algorithm 

1. Loop `A` from `1` to `n-1`.
2. For each `A`, compute `B = n - A`.
3. Convert `A` and `B` to strings and check whether either string contains the character `'0'`.
4. If neither contains `'0'`, return the pair `[A, B]`.
5. If the loop finishes without finding such a pair, return an empty array.

In short: brute-force search for the first pair of positive integers that sum to `n` and have no digit `0` in their decimal representation.

---

# 2. Code (as-is)

```java
class Solution {

    public int[] getNoZeroIntegers(int n) {
        for (int A = 1; A < n; ++A) {
            int B = n - A;
            if (
                !String.valueOf(A).contains("0") &&
                !String.valueOf(B).contains("0")
            ) {
                return new int[] { A, B };
            }
        }
        return new int[0];
    }
}
```

---

# 3. Time Complexity

**Worst-case:** `O(n * d)` where `d` = number of digits in `n` (≈ `⌊log10 n⌋ + 1`).
**Reason:** The loop may try up to `n-1` values of `A`. For each `A` the code does two `String.valueOf(...)` calls and two `.contains("0")` scans — each string creation/scan is `O(d)`. So total ≈ `O(n * d)`, commonly written as `O(n log n)` in terms of `n`.

**Best-case:** `O(d)` (≈ `O(log n)`), if a valid pair is found immediately (e.g., `A = 1` works).

---

# 4. Space Complexity

**Auxiliary (extra) space:**

* If you count only long-lived allocations: `O(1)` (the returned array of size 2 is constant).
* If you include temporary strings used by `String.valueOf`, each iteration allocates `O(d)` extra memory, but they are short-lived and garbage-collected; peak temporary extra memory is `O(d)`.

So: **O(1)** (amortized, ignoring temporary string allocations) or **O(log n)** if you account for the temporary strings.

---

# 5. Assumptions / Constraints

* `n` is an `int` (32-bit) and expected to be ≥ 2 (for `n = 1` there are no two positive integers that sum to `1`).
* Decimal (base-10) digit representation is used.
* The returned pair is the *first* valid pair found (order depends on the loop).
* The function returns an empty array if no valid pair is found (the problem this implements usually guarantees a solution for valid `n`, so an empty array indicates no solution under the brute-force search).
* Performance assumes `n` is not extremely large; for very large `n` a brute-force loop up to `n` is likely too slow.

---

# 6. Optimizations (suggestions)

1. **Check digits arithmetically (avoid `String`):** replace `String.valueOf(...).contains("0")` with a digit-scan using `% 10` and `/= 10`. This avoids string allocation and is faster:

   ```java
   private boolean containsZero(int x) {
       if (x == 0) return true;
       while (x > 0) {
           if (x % 10 == 0) return true;
           x /= 10;
       }
       return false;
   }
   ```
2. **Only iterate up to `n/2`:** Because `(A, B)` and `(B, A)` are symmetric, you only need to test `A` in `[1, n/2]`; if a valid pair exists with `A > n/2` then its symmetric smaller counterpart will be found earlier. This halves the number of iterations in the worst case.
3. **Construct digits directly (linear in number of digits):** There exist constructive approaches that build two numbers digit-by-digit to avoid zeros without scanning up to `n`. Those run in `O(d)` time (where `d` is number of digits), but are more complex to implement (they handle borrow/placement between digits). Use such a method when `n` can be huge and performance matters.
4. **Early exit and micro-optimizations:** Check cheaper conditions first (e.g., skip `A` if last digit is `0` or similar) to reduce work.

With the first two simple changes (digit-scan + iterate to `n/2`) you keep correctness and reduce constant factors and iterations significantly.

---

# 7. Edge Cases (handled / potentially breaking)

Handled (by current code):

* Typical positive `n` values ≥ 2 — the code will find the first valid pair and return it.
* `n` where `A=1` already forms a valid pair — immediate return.

Potentially problematic / not explicitly handled:

* **`n <= 1`** — loop body never runs; function returns `new int[0]`. Depending on caller expectations this may be incorrect (caller might expect an exception or different contract).
* **Very large `n`** (close to `Integer.MAX_VALUE`) — the `O(n)` loop is too slow in practice. This becomes a performance/timeout problem rather than correctness.
* **Negative `n` or non-positive inputs** — behavior is not defined for such input; loop doesn't run and `[]` is returned.
* **Memory churn** — repeated calls allocate many short-lived strings (`String.valueOf`) which can cause GC pressure; switching to digit arithmetic removes that problem.
* **Return format expectation mismatch** — some problems expect exactly two numbers always; returning an empty array on failure might not match the problem contract if the caller assumes a guaranteed pair.
* **Integer overflow** — not likely here for `B = n - A` given types, but be mindful if adapting algorithm to other numeric types or languages.

---
