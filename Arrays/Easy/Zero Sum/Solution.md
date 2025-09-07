https://leetcode.com/problems/find-n-unique-integers-sum-up-to-zero/description/

# 1. Algorithm 

1. Allocate an integer array `res` of length `n`.
2. Keep an `index` pointer starting at 0.
3. For every integer `i` from `1` to `n/2` (integer division):

   * Put `i` into `res[index++]`.
   * Put `-i` into `res[index++]`.
     These pairs cancel each other (sum to 0).
4. If `n` is odd, put a single `0` in the remaining slot (`res[index] = 0`) so the total count becomes `n`.
5. Return `res`.

Goal: produce `n` **unique** integers whose sum is `0`. Pairs `i` and `-i` cancel out; `0` is added only when needed (odd `n`).

Example outputs:

* `n = 1` → `[0]`
* `n = 2` → `[1, -1]`
* `n = 5` → `[1, -1, 2, -2, 0]`

# 2. Code

```java
class Solution {
    public int[] sumZero(int n) {
        int[] res = new int[n];
        int index = 0;
        for (int i = 1; i <= n / 2; i++) {
            res[index++] = i;
            res[index++] = -i;
        }
        if (n % 2 != 0) {
            res[index] = 0;
        }
        return res;
    }
}
```

# 3. Time Complexity

**O(n)**.

**Why:** The loop runs `floor(n/2)` iterations; each iteration performs two O(1) assignments → about `n` assignments overall. Array allocation `new int[n]` is O(n) as well. All other operations are constant time. So total time grows linearly with `n`.

# 4. Space Complexity

**O(n)** extra space.

**Why:** The method allocates an integer array `res` of length `n` to return — that dominates memory. Additional variables (`index`, loop counter) use O(1) extra space.

# 5. Assumptions / Constraints

* `n` should be a non-negative integer.

  * If `n < 0`, `new int[n]` throws `NegativeArraySizeException`.
* If `n == 0`, the function returns an empty array (`new int[0]`).
* The code produces `n` **unique** integers: `±1, ±2, ..., ±(n/2)` and `0` if needed.
* The integers fit in Java `int` for any practical `n` (but extremely large `n` is limited by `Integer.MAX_VALUE` and available memory).
* Output order is alternating positive/negative pairs (not sorted). The problem usually doesn't require a sorted result.
* Assumes typical JVM memory — very large `n` may cause `OutOfMemoryError`.

# 6. Optimizations (if any)

The algorithm is already optimal in asymptotic terms (you must output `n` numbers, so O(n) time and O(n) space are necessary). Suggestions for readability or tiny micro-optimizations:

1. **Compute half once** (avoid repeated division in the loop header):

```java
int half = n / 2;
for (int i = 1; i <= half; i++) {
    res[index++] = i;
    res[index++] = -i;
}
```

2. **Index arithmetic (clearer indexing, slightly faster/cleaner)**:

```java
int half = n / 2;
for (int i = 0; i < half; i++) {
    res[2*i] = i + 1;
    res[2*i + 1] = -(i + 1);
}
if (n % 2 != 0) res[n - 1] = 0;
```

This removes the separate `index` variable and directly computes positions.

3. **If memory is a concern and the caller can consume a stream/iterator**, return a lazy generator instead of an array (not applicable if the API requires an `int[]`).

4. **Java 8 alternative (concise but not asymptotically better):**
   Using `IntStream` to build values — more functional/concise but similar complexity.

# 7. Edge Cases

* **`n = 0`** → returns `[]` (empty array). Valid from code perspective.
* **`n = 1`** → returns `[0]`. Correct.
* **`n` even** (e.g., `n = 4`) → loop fills all positions with `±` pairs; `index` ends at `n`.
* **`n` odd** (e.g., `n = 5`) → loop fills `n-1` slots, then `0` is placed in the last slot.
* **`n < 0`** → `new int[n]` throws `NegativeArraySizeException`. Code does not guard against negative input.
* **Very large `n`** → may cause `OutOfMemoryError` or hit practical limits of JVM/OS memory; also `n` must be ≤ `Integer.MAX_VALUE`.
* **Uniqueness**: by construction there are no duplicate values (pairs `i` and `-i` plus optional `0`).
* **Overflow**: Not a practical concern here because values assigned are ≤ `n/2` in magnitude and `n` itself must be within `int` limits for array creation.

---


