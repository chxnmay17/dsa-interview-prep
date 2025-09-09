https://leetcode.com/problems/container-with-most-water/

# 1. Algorithm 

1. Place two pointers at the ends of the array: `left = 0`, `right = n-1`.
2. While `left < right`:

   * The possible container height is the smaller of the two heights at the pointers.
   * The width is `right - left`.
   * Area = `height_min * width`. Update the maximum area (`res`) if this area is larger.
   * To try to find a taller boundary (and possibly a larger area), move the pointer at the smaller height inward:

     * If `height[left] < height[right]` → increment `left`.
     * Else → decrement `right`.
3. Repeat until pointers meet. Return the recorded maximum area.

**Why moving the smaller pointer is safe:** any container using the smaller-height side with an inner pointer will have equal or smaller height and smaller width, so it cannot produce an area greater than the current one. Thus only moving the smaller side can possibly increase the height and therefore the area.

# 2. Code

```java
class Solution {
    public int maxArea(int[] height) {
        int left=0;
        int right=height.length-1;
        int res=0;
        while(left<right){

            int length=Math.min(height[left],height[right]);
            res=Math.max(res,length*(right-left));

            if(height[left]<height[right]) left++;
            else right--;

        }
        return res;
    }
}
```

# 3. Time Complexity

**O(n)** — justification:

* `left` starts at 0 and `right` at `n-1`. Each loop iteration moves one pointer inward (either `left++` or `right--`).
* Therefore the number of loop iterations is at most `n-1`. Each iteration does a constant amount of work (comparisons, multiplications, assignments).
* So overall time is linear in the number of elements.

# 4. Space Complexity

**O(1)** — justification:

* The algorithm uses a fixed number of extra variables (`left`, `right`, `res`, `length`) regardless of input size.
* No additional arrays or recursion are used.

# 5. Assumptions / Constraints

* `height` is a non-`null` integer array. If `height == null`, the given code will throw `NullPointerException`.
* Heights are stored in `int`. The code assumes the product `height[i] * (right-left)` fits in Java `int`. If values are large, the product might overflow.
* Typical LeetCode constraints (e.g., `0 <= height[i] <= 10^4`) make `int` safe — verify problem constraints in your notes.
* The algorithm does not modify the input array (it only reads it).
* The function returns `0` for arrays with length 0 or 1 (no valid container).

# 6. Optimizations (if any)

The algorithm is already optimal in time (O(n)) and space (O(1)). suggested improvements:

* **Avoid potential integer overflow:** If `height` values or width can be large, compute area in `long` to be safe:

  ```java
  class Solution {
      public long maxAreaLong(int[] height) {
          if (height == null) return 0L;
          int left = 0, right = height.length - 1;
          long res = 0L;
          while (left < right) {
              long h = Math.min(height[left], height[right]);
              long area = h * (right - left);
              if (area > res) res = area;
              if (height[left] < height[right]) left++;
              else right--;
          }
          return res;
      }
  }
  ```

  (Or keep return type `int` but check constraints or clamp results appropriately.)

* **Micro-optimizations** (negligible in big-O):

  * Cache `height[left]` and `height[right]` into local variables to avoid repeated array access.
  * Replace `Math.min`/`Math.max` calls with inline comparisons — small speed improvement, less method call overhead.

* **No correct asymptotic early-exit**: There’s no simple safe early break that guarantees correctness without scanning, so O(n) is best.

# 7. Edge Cases

* **Null input:** `height == null` → current code throws `NullPointerException`. Add a null check if needed.
* **Length 0 or 1:** `height.length < 2` → while loop never runs, returns `0`. Reasonable behavior.
* **All zeros:** returns `0`.
* **Monotonic arrays**:

  * Strictly increasing or decreasing arrays are handled correctly by pointer movement.
* **Equal heights at pointers:** code moves the `right` pointer (because `if (height[left] < height[right]) left++; else right--;`). That’s fine — moving either pointer when equal is acceptable and still leads to correct result.
* **Large values causing overflow:** If `height[i] * width` exceeds `Integer.MAX_VALUE`, result will overflow. Use `long` for area if input bounds might be large.
* **Negative values:** Problem domain usually restricts heights to non-negative. If negative heights are present, semantics of “area” become meaningless; ensure inputs conform to constraints.

---


