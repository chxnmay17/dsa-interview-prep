https://leetcode.com/problems/convert-integer-to-the-sum-of-two-no-zero-integers/
---

# 1. Algorithm 

* The code builds two integers `a` and `b` such that `a + b == n` and neither `a` nor `b` contains the digit `0`.
* It processes `n` **digit-by-digit from least significant to most significant** (units, tens, hundreds, ...).
* For each digit-position it picks a small `split` (either `1` or `2`) to assign to `a` at that position, then subtracts that amount from `b` (initially `b = n`).
* The choice rule is: normally use `split = 1`, but if subtracting 1 would make the current digit of `b` equal `0` (i.e. `n % 10 == 1`), use `split = 2` instead — that avoids creating a zero digit in `b`.
* After applying the split at the current base (`base = 1, 10, 100, ...`) the code:

  * adds `split * base` to `a`,
  * subtracts `split * base` from `b`,
  * shifts `base *= 10`,
  * reduces `n = (n - split) / 10` to move to the next higher digit (this handles any borrow automatically).
* Repeat until there are no more digits to process (loop condition `n > 1`).
* Return `[a, b]`.

Short correctness idea: At each digit we ensure the digit placed into `b` is non-zero by avoiding `split == (that digit)`. Because digits are processed LSB→MSB with borrowing handled by integer arithmetic, this produces two numbers without zero digits whose sum is the original `n`.

---

# 2. Code

```java
class Solution {
    public int[] getNoZeroIntegers(int n) {
        int a = 0, b = n;
        int base = 1;

        while (n > 1) {
            int split = 1;
            if ((n - split) % 10 == 0) {
                split += 1;
            }
            a += split * base;
            b -= split * base;
            base *= 10;
            n = (n - split) / 10;
        }

        return new int[]{a, b};
    }
}
```

---

# 3. Time Complexity

**O(d)** where `d` is the number of digits in `n` (equivalently `O(log₁₀ n)`).

**Justification:** each loop iteration processes exactly one digit (constant work per iteration). The loop runs once per digit until all digits are handled, so runtime scales linearly with digit count.

---

# 4. Space Complexity

**O(1)** extra space.

**Justification:** only a fixed number of integer variables (`a`, `b`, `base`, `split`, `n`) are used; no extra arrays or recursion that scale with input size.

---

# 5. Assumptions / Constraints

* Assumes `n` is an integer in the positive range (the LeetCode-style problem expects `n ≥ 2`). If `n == 1` or `n <= 0` the output will be meaningless for the "no-zero digits" requirement.
* Uses Java `int` — so for extremely large `n` (more digits than `int` supports), overflow is possible. Typical problem constraints (e.g. `2 ≤ n ≤ 10^9`) are safe.
* The algorithm assumes base-10 digits and standard integer division/truncation semantics.
* It assumes we only need `split` values small (1 or 2) — the logic relies on that sufficing to avoid zeros.

---

# 6. Optimizations (suggestions)

The algorithm is already optimal in time (linear in digits) and constant space. Suggested improvements for **readability**, **robustness** and **safety**:

1. **Use `long` for safety** (avoid overflow on `base`, `a`, `b` if `n` might be near `Integer.MAX_VALUE`):

```java
public int[] getNoZeroIntegers(int n) {
    long a = 0, b = n;
    long base = 1;
    int nn = n;

    while (nn > 1) {
        int split = 1;
        if ((nn - split) % 10 == 0) split = 2;
        a += split * base;
        b -= split * base;
        base *= 10;
        nn = (nn - split) / 10;
    }
    return new int[]{(int)a, (int)b};
}
```

2. **Simpler split expression** (equivalent, slightly clearer):

```java
int split = (n % 10 == 1) ? 2 : 1;
```

(but note: because the code updates `n` with `(n - split)/10`, it uses `n`’s current value — so use current `n % 10` or keep original logic).

3. **Explicit loop over digits** — using `while (n > 0)` and carefully handling final digit can make the intent clearer, but the current `while (n > 1)` is a compact correct choice for `n >= 2`.

4. **Alternative (string approach)**: convert `n` to string and construct two digit-strings that avoid zeros. That is often simpler to reason about but still O(d) time and O(d) extra space.

5. **Avoid unnecessary arithmetic operations**: small micro-optimizations won't change asymptotic complexity.

---

# 7. Edge Cases

**Handled by the code:**

* Numbers containing `0` digits (e.g., `n = 101`, `n = 1000`) — the algorithm handles borrowing and ensures final digits in both `a` and `b` are non-zero.
* Numbers with trailing `1` digits (e.g., `11`, `21`, `101`) — code uses `split = 2` where needed to avoid creating a `0` in `b`.

**Potentially problematic / must be guarded against:**

* `n <= 1`: the loop uses `while (n > 1)` and will return `a = 0` and `b = n` for `n = 1` (which produces `0` having digit `0` — invalid for "no-zero digits"). So require `n >= 2`.
* Negative `n` or zero: algorithm not designed for non-positive inputs.
* **Integer overflow**: if `n` is very large (beyond `int` constraints) `base *= 10` or `a += split * base` may overflow. Use `long` or `BigInteger` for safety if needed.
* If the problem asks for **both numbers > 0**: this code returns positive `a` and `b` for `n >= 2`, so OK.

---

### Quick worked example (illustrates iterations)

Take `n = 101`:

* Start: `a = 0`, `b = 101`, `base = 1`, `n = 101`.
* Iteration 1: `n%10 = 1` → choose `split = 2`.
  `a += 2*1 = 2`, `b -= 2*1 = 99`, `base = 10`, `n = (101 - 2)/10 = 9`.
* Iteration 2: `n%10 = 9` → choose `split = 1`.
  `a += 1*10 = 12`, `b -= 10 = 89`, `base = 100`, `n = (9 - 1)/10 = 0`.
* Done. Return `[12, 89]` — both have no zero digits and sum to 101.

---

