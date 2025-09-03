# 1) Algorithm



1. **Same length?** If `s` and `t` don’t have the same length, they can’t be anagrams → return `false`.
2. **Sort:** Convert both strings to character arrays and sort each array.
3. **Scan:** Walk once over the arrays; if any character at the same index differs → return `false`.
4. **All matched:** If you finish the scan with no differences → return `true`.

# 2) Code (as-is)

```java
class Solution {
    public boolean isAnagram(String s, String t) {
        if (s.length() != t.length()) return false;
        
        char[] sArr = s.toCharArray();
        char[] tArr = t.toCharArray();
        
        Arrays.sort(sArr);
        Arrays.sort(tArr);
        
        for (int i = 0; i < sArr.length; i++) {
            if (sArr[i] != tArr[i]) return false;
        }
        
        return true;
    }
}
```

# 3) Time Complexity

* **Average / Typical:** **O(n log n)** due to sorting two length-`n` arrays (`toCharArray()` is O(n), compare loop is O(n), both dominated by sorting).
* **Worst-case (Java detail):** `Arrays.sort(char[])` uses Dual-Pivot Quicksort for primitives → **O(n²)** in a contrived worst case. (In practice, average O(n log n).)

# 4) Space Complexity

* **Auxiliary space:** **O(n)**

  * Two new arrays of size `n` are created (`sArr` and `tArr`).
  * Sorting is in-place on those arrays (O(1) extra), and the final comparison is O(1) extra.
* **Mnemonic:** “Two copies → 2n chars → O(n).”

# 5) Assumptions / Constraints

* **Non-null inputs:** Code does **not** check for `null` (`s` or `t` being `null` would throw `NullPointerException` on `length()`).
* **Case sensitivity:** `'A'` ≠ `'a'` (no case normalization).
* **Whitespace & punctuation:** Treated as normal characters (affect result).
* **Character model:** Operates on **UTF-16 code units** (`char`).

  * Code points outside BMP (e.g., some emojis) are represented by **surrogate pairs** and are treated as two separate `char`s.
  * No Unicode normalization (e.g., `"é"` vs `"e"+"´"` differ).
* **Sorting stability:** Irrelevant here (we only compare equality after sorting; order within equal chars doesn’t matter).
* **Memory:** Requires enough memory to hold two `n`-length `char[]` arrays.

# 6) Optimizations (if any)

**Goal: Linear time, constant extra space (beyond a fixed counter array).**

1. **Counting approach (best when alphabet is small/fixed):**

   * Use `int[26]` for lowercase English letters, or `int[256]` for extended ASCII.
   * Increment counts for `s`, decrement for `t`, and ensure all zeros at the end.
   * **Time:** O(n). **Space:** O(1) (fixed-size array).
2. **General Unicode:**

   * Use `HashMap<Integer,Integer>` counting **code points** (iterate via `codePoints()`), not `char`, to handle surrogate pairs correctly.
   * **Time:** O(n). **Space:** O(u) where `u` = number of distinct code points.
3. **Minor cleanups:**

   * After sorting, `Arrays.equals(sArr, tArr)` can replace the manual loop (readability; same complexity).
4. **Early exits:**

   * In counting method, you can return `false` as soon as any count goes negative while processing `t`.

**Mnemonic:** “For speed, **Count not Sort**.”

# 7) Edge Cases

* **Handled well:**

  * **Different lengths:** Immediate `false`.
  * **Empty strings:** Both empty → sorted arrays equal → `true`.
  * **Repeated characters:** Sorting groups duplicates together → works.

* **Potential pitfalls / could break or mislead:**

  * **`null` inputs:** Not checked → `NullPointerException`.
  * **Case differences:** `"anagram"` vs `"Anagram"` → `false` unless you normalize (e.g., `.toLowerCase()`).
  * **Whitespace/punctuation:** `"a n a"` vs `"ana"` → `false` unless you strip spaces.
  * **Unicode normalization:** `"é"` (single code point) vs `"e\u0301"` (combining) → `false` unless you normalize (e.g., `Normalizer.normalize(..., Form.NFC)`).
  * **Emoji / non-BMP:** Treats surrogate pairs separately; two strings anagrammatic by **code points** might fail by **code units**.
  * **Adversarial worst-case sort:** Pathological inputs could push Dual-Pivot Quicksort to O(n²) (rare in practice).
  * **Very large inputs:** Extra O(n) memory for copies may be a concern.

---

**Quick recall card:**

* **Idea:** Sort both, compare → **SSS**.
* **Time:** O(n log n) avg.
* **Space:** O(n) for copies.
* **Upgrade:** Use **Count not Sort** for O(n) time.
