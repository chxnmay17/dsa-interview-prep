https://leetcode.com/problems/plus-one/description/


# Algorithm 

1. Start from the least-significant digit (rightmost element of the array).
2. For each digit moving left:

   * If the digit is less than 9, increment it by 1 and return the array immediately (no further carry).
   * If the digit is 9, set it to 0 and continue to the next more-significant digit (carry = 1).
3. If you finish the loop, every digit was 9 (e.g., `999` → `000`). Allocate a new array of length `n+1`, set the first element to `1` and leave the rest `0` → this represents the result `1000`.
4. Return the resulting array.

This performs the addition by propagating carries from right to left and only allocates extra space when the carry increases the number of digits.

# Code

```java
class Solution {
    public int[] plusOne(int[] digits) {
        int n = digits.length;

        for (int i = n - 1; i >= 0; i--) {
            if (digits[i] < 9) {
                digits[i]++;
                return digits;
            }
            digits[i] = 0;
        }

        
        int[] result = new int[n + 1];
        result[0] = 1;
        return result;
    }
}
```

# Time Complexity

* **Worst-case:** `O(n)` where `n` is `digits.length`.
  Justification: In the worst case (all digits are `9`), the loop visits every digit (`n` iterations) and then you allocate a new array of size `n+1` (allocation cost is `O(n)`), so overall `O(n)`.
* **Best-case:** `O(1)`.
  Justification: If the last digit is not `9`, you increment it and return immediately (constant work).
* **Average-case:** typically closer to `O(1)` for many random inputs (few carries), but complexity class remains `O(n)` because of the possible worst-case.

# Space Complexity

* **Auxiliary / extra space:** `O(1)` in typical cases (in-place update).
  Justification: The method modifies the input array in-place and only uses a constant amount of extra variables.
* **Worst-case (including new allocation):** `O(n)`.
  Justification: When all digits are `9`, a new array of size `n+1` is allocated to represent the extra digit—so additional space proportional to `n` is used.
* **If you count output storage:** The returned result requires `O(n)` or `O(n+1)` space anyway (same as input order of magnitude).

# Assumptions / Constraints

* `digits` is expected to be an array of decimal digits (`0` through `9`).
* Typical LeetCode-style constraints implicit: `digits.length >= 1` and digits do not have invalid values (no negatives, no >9).
* The array represents a non-negative integer in most-significant-first order (index 0 is the most significant digit).
* The method does **not** check for `null` input — calling with `null` will throw `NullPointerException`.
* The code assumes integers fit in arrays (no conversion to numeric types — safe for very large numbers stored digit-wise).

# Optimizations (if any)

The given implementation is already concise and asymptotically optimal for this problem. A few minor suggestions:

1. **Input validation (safety):** Add a `null` and optional range check for digits to make the method robust:

   ```java
   if (digits == null || digits.length == 0) throw new IllegalArgumentException(...);
   for (int d : digits) if (d < 0 || d > 9) throw new IllegalArgumentException(...);
   ```

   This costs `O(n)` if you validate every element, so only add if needed.

2. **Micro-style change:** Use a `carry` variable to make intent explicit — functionally equivalent, not asymptotically better.

3. **Avoid unnecessary allocation attempt:** Some propose checking if all digits are `9` first and then either allocate or modify in-place. That requires a full pre-scan (another `O(n)` pass) so it doesn't improve asymptotic cost.

4. **Documentation / tests:** Add JavaDoc and unit tests to cover edge cases.

Overall: algorithmic improvements are unnecessary — it's already optimal.

# Edge Cases

Handled correctly by the code:

* `[0]` → `[1]`
* `[1,2,3]` → `[1,2,4]` (last digit < 9: early return)
* `[1,2,9]` → `[1,3,0]` (carry propagates a bit, stops)
* `[9]` → `[1,0]`
* `[9,9,9]` → `[1,0,0,0]` (allocates new array)

Potential problems / not handled explicitly:

* **`null` input:** will throw `NullPointerException`. Add a guard if you need to handle `null`.
* **Empty array (`new int[0]`)**: current code returns `[1]` (because loop is skipped and it allocates `result[0]=1`). That might be acceptable or not depending on specification — usually input is non-empty.
* **Invalid digits (negative or >9):** code assumes valid digits; invalid values produce incorrect math.
* **Concurrent modifications / shared array:** method mutates the input `digits`. If caller reuses the same array elsewhere, they may be surprised. If mutation is undesired, clone the array first.
* **Extremely large arrays:** works but uses linear time & memory in worst case — expected.

# Quick test examples

* Input: `[1,2,3]` → Output: `[1,2,4]`
* Input: `[4,3,2,1]` → Output: `[4,3,2,2]`
* Input: `[9]` → Output: `[1,0]`
* Input: `[9,9,9]` → Output: `[1,0,0,0]`

---

