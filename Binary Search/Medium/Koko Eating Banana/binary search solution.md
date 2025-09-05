https://leetcode.com/problems/koko-eating-bananas/

---

## 1. **Algorithm **

This code solves the **"Koko Eating Bananas" problem** using **binary search**.
The goal: find the minimum speed `k` at which Koko can eat all bananas in `h` hours.

**Step by step:**

1. **Find the maximum pile size** (because eating faster than this max size is unnecessary).

   * This becomes the upper bound for our search.

2. **Binary Search between 1 and max pile size (`l=1, r=max`)**:

   * Pick a mid-value `k` as a possible eating speed.
   * Calculate `totalTime` needed if Koko eats at speed `k`:
     For each pile, time = `ceil(pile/k)` → implemented as `(pile - 1) / k + 1` to avoid floating-point math.
   * If `totalTime <= h`, it means Koko can finish on time → try smaller speeds (`r = k - 1`).
   * If `totalTime > h`, she needs to eat faster → increase speed (`l = k + 1`).

3. **Return the minimum valid speed (`res`)** found.

---

## 2. **Code (as-is)**

```java
class Solution {
    public static int max(int[] piles){
        int max=piles[0];
        for(int pile:piles){
            if(pile>max) max=pile;
        }
        return max;
    }
    
    public int minEatingSpeed(int[] piles, int h) {
        int l=1,r=max(piles);
        int res=r;
        while(l<=r){
            int k = l+(r-l)/2;
            long totalTime=0;
            for(int pile:piles){
                totalTime+=(pile-1)/k+1;
            }
            if(totalTime<=h){
                res=k;
                r=k-1;
            }
            else l=k+1;
        }
        return res;
    }
}
```

---

## 3. **Time Complexity**

* **Finding max pile size:** `O(n)` (where `n` = number of piles).
* **Binary Search range:** `log(maxPile)`.
* **Each mid calculation requires checking all piles:** `O(n)`.

👉 **Overall Time Complexity = O(n · log(maxPile))**

**Justification:** For each binary search step (log(maxPile)), we scan the array of piles (`n`) to compute `totalTime`.

---

## 4. **Space Complexity**

* Uses only a few integer variables (`l`, `r`, `res`, `k`, `totalTime`).
* No extra data structures.

👉 **Overall Space Complexity = O(1)**

---

## 5. **Assumptions / Constraints**

* `piles.length ≥ 1` (non-empty input).
* `h ≥ piles.length` (must have at least as many hours as piles, otherwise impossible).
* `piles[i] > 0` (banana counts are positive).
* Input integers fit within `int`, but `totalTime` is stored as `long` to avoid overflow.
* Division logic `(pile-1)/k + 1` assumes integer division and avoids floating-point errors.

---

## 6. **Optimizations (if any)**

* **Already optimal approach:** Binary search is the most efficient way here (brute force would be O(maxPile \* n)).
* Could **inline `max()` function** to avoid an extra pass, but complexity remains the same.
* If piles are very large, using **prefix sums or pruning** wouldn’t help since each pile must be checked individually.
* Space is already minimal (O(1)).

---

## 7. **Edge Cases**

Handled:

* **Single pile:** Works correctly, returns `ceil(pile/h)`.
* **Large pile values:** Handled since `totalTime` uses `long`.
* **Exact h match:** Works since condition `<= h` ensures minimal speed found.
* **Smallest h = n:** Ensures each pile must be finished in exactly 1 hour.

Potential pitfalls:

* If `piles` is empty (not allowed per constraints).
* If `h < piles.length`, algorithm doesn’t explicitly handle impossible cases (but problem constraints usually prevent this).

---

