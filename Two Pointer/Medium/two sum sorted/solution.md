https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/description/

# Algorithm

1. Start with two pointers: `left` at the beginning (index 0) and `right` at the end (index `n-1`) of the sorted array `numbers`.
2. Repeatedly compute the sum `numbers[left] + numbers[right]`.
3. If the sum equals `target`, return the pair of indices (converted to 1-based indexing).
4. If the sum is larger than `target`, move `right` one step left (`right--`) to reduce the sum.
5. If the sum is smaller than `target`, move `left` one step right (`left++`) to increase the sum.
6. Continue until you find the pair. (The code as written uses an unconditional loop and assumes a solution will be found.)

# Code

```java
class Solution {
    public int[] twoSum(int[] numbers, int target) {
        
        int n=numbers.length;
        int right=n-1;
        int left=0;
        while(true){
            if(numbers[left]+numbers[right]==target) return new int[]{++left,++right};
            else if(numbers[left]+numbers[right]>target)right--;
            else left++;
        }
    }
}
```

# Time Complexity

**O(n)** in the worst case.
Justification: each iteration moves either `left` one step to the right or `right` one step to the left. Neither pointer ever moves backwards, so there can be at most `n` moves total before `left` and `right` cross. Therefore the number of iterations is linear in the array size.

(Best case: O(1) if the first pair tried matches.)

# Space Complexity

**O(1)** extra space (constant).
Justification: only a few integer variables (`left`, `right`, `n`) are used regardless of input size. The returned `int[2]` is constant-size output (still considered O(1) auxiliary space).

# Assumptions / Constraints

* The input array `numbers` is sorted in non-decreasing (ascending) order. The two-pointer strategy relies on this property.
* The code **assumes a solution exists** (it never breaks out of the `while(true)` loop except by returning), which is typical for some problem statements (e.g., LeetCode Two Sum II that guarantees exactly one solution).
* The array length should be at least 2. If `numbers.length < 2` the code will likely throw an exception.
* Indices returned are **1-based** (because of the `++left`/`++right` used at return).
* Integer overflow is possible if `numbers[left] + numbers[right]` exceeds `Integer.MAX_VALUE` or drops below `Integer.MIN_VALUE`. The code uses `int` arithmetic, so no overflow protection is present.

# Optimizations / Improvements

The algorithm is already optimal for sorted arrays (two-pointer linear approach). Improvements are mostly about safety and clarity:

1. **Use `while (left < right)`** instead of `while(true)` to avoid possible out-of-bounds if no solution exists.
2. **Compute the sum once per loop** (store in a variable) to avoid duplicate work and to allow using `long` to prevent overflow.
3. **Return `left+1` / `right+1`** instead of pre-incrementing the pointer variables (pre-increment mutates variables unnecessarily before returning).
4. **Validate inputs** and throw a clear exception or return a sentinel if no solution exists.

Safer version (recommended):

```java
class Solution {
    public int[] twoSum(int[] numbers, int target) {
        if (numbers == null || numbers.length < 2) 
            throw new IllegalArgumentException("Input must contain at least two numbers");

        int left = 0, right = numbers.length - 1;
        while (left < right) {
            long sum = (long) numbers[left] + numbers[right]; // use long to avoid overflow
            if (sum == target) return new int[]{left + 1, right + 1};
            if (sum > target) right--;
            else left++;
        }
        throw new IllegalArgumentException("No two sum solution");
    }
}
```

# Edge Cases (handled / potentially breaking)

* **Handled (by algorithm nature):**

  * Negative numbers, zero, duplicates — algorithm works since it only relies on ordering.
* **Potentially breaking / not handled in original code:**

  * **No solution present**: original `while(true)` will eventually cause index-out-of-bounds (or infinite incorrect behavior). Use `while(left < right)` or explicit checks.
  * **Array length < 2**: `numbers[left]` / `numbers[right]` may cause `ArrayIndexOutOfBoundsException`.
  * **Integer overflow**: `numbers[left] + numbers[right]` may overflow `int`. Use `long` for the sum if values can be large.
  * **Using the same element twice**: if the problem forbids using the same index twice, ensure `left != right` is enforced (the `left < right` loop covers that). The original code could return the same index twice if such condition arises.
  * **Unsorted input**: algorithm is invalid if `numbers` is not sorted — it would produce incorrect results.
  * **Pre-increment side effect**: `return new int[]{++left, ++right}` modifies `left` and `right` before returning; although harmless here (method returns immediately), it’s less clear than `left + 1` / `right + 1`.

---
