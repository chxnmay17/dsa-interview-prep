
https://leetcode.com/problems/longest-consecutive-sequence/
---

## 1. Algorithm 

The problem solved here is **“Longest Consecutive Sequence”**. The goal is to find the length of the longest sequence of consecutive integers in the given array.

Step-by-step working:

1. **Insert all elements into a HashSet** for O(1) average lookup.
2. **Iterate over each number** in the set:

   * If the number does **not** have a predecessor (`num - 1`), it means this number could be the **start of a consecutive sequence**.
   * From this starting number, keep checking for consecutive numbers (`num + 1`, `num + 2`, …) until the sequence ends.
   * Track the length of this sequence.
3. Keep updating the `longest` length found so far.
4. Return the maximum sequence length at the end.

---

## 2. Code (as-is)

```java
public class Solution {
    public int longestConsecutive(int[] nums) {
        Set<Integer> numSet = new HashSet<>();
        for (int num : nums) {
            numSet.add(num);
        }
        int longest = 0;

        for (int num : numSet) {
            if (!numSet.contains(num - 1)) {
                int length = 1;
                while (numSet.contains(num + length)) {
                    length++;
                }
                longest = Math.max(longest, length);
            }
        }
        return longest;
    }
}
```

---

## 3. Time Complexity

* Building the HashSet: **O(n)** (where *n* = number of elements).
* Iterating over each number: **O(n)**.
* Inner `while` loop: Each number is visited at most **once** across the whole algorithm (because only starts of sequences trigger the loop).

✅ **Overall Time Complexity = O(n)**

---

## 4. Space Complexity

* Extra space is used for the HashSet storing all unique elements: **O(n)**.
* No additional significant space is used (apart from a few variables).

✅ **Overall Space Complexity = O(n)**

---

## 5. Assumptions / Constraints

* Input array size = `n` (0 ≤ n).
* Numbers can be positive, negative, or zero.
* Duplicates don’t affect the result since we use a HashSet (unique storage).
* Works regardless of input order (array doesn’t need to be sorted).

---

## 6. Optimizations (if any)

* This approach is already **optimal** (O(n) time, O(n) space).
* Alternative (but less efficient): Sorting the array → O(n log n) time, O(1) space.
* If numbers are within a **small fixed range**, a boolean array (instead of HashSet) could reduce memory usage.

---

## 7. Edge Cases

* **Empty array (`[]`)** → returns `0`.
* **Single element (\[x])** → returns `1`.
* **All elements identical (\[2,2,2])** → returns `1`.
* **Already consecutive array (\[1,2,3,4])** → returns `length of array`.
* **Unordered input (\[100, 4, 200, 1, 3, 2])** → correctly returns `4` (`[1,2,3,4]`).
* **Negative numbers (\[ -2, -1, 0, 1 ])** → works fine (`4`).

---

