

# Algorithm 

1. Quickly check if the two strings have the same length. If not, they can't be anagrams → return `false`.
2. Create two hash maps (`countS` and `countT`) to store how many times each character appears in `s` and `t`.
3. Walk through both strings once (same index `i`) and increment the character counts in their respective maps.
4. For each character key in `countS`, check that `countT` has the same key and the same count. If any mismatch → return `false`.
5. If every key matches, the strings are anagrams → return `true`.

# Code

```java
import java.util.HashMap;

class Solution {
    public boolean isAnagram(String s, String t) {
        if (s.length() != t.length()) {
            return false;
        }

        HashMap<Character, Integer> countS = new HashMap<>();
        HashMap<Character, Integer> countT = new HashMap<>();

        for (int i = 0; i < s.length(); i++) {
            countS.put(s.charAt(i), countS.getOrDefault(s.charAt(i), 0) + 1);
            countT.put(t.charAt(i), countT.getOrDefault(t.charAt(i), 0) + 1);
        }

        for (char c : countS.keySet()) {
            if (!countT.containsKey(c) || !countT.get(c).equals(countS.get(c))) {
                return false;
            }
        }

        return true;
    }
}
```

# Time Complexity

* **Average / Practical:** **O(n)** where `n = s.length()`.

  * Building both maps: one pass over `n` characters → O(n).
  * Comparing maps: iterate over at most `k` distinct keys (`k ≤ n`) and do O(1) lookups → O(k) ≤ O(n).
  * Total average ≈ O(n).
* **Worst-case (theoretical / HashMap collisions):** depends on Java HashMap internals — in modern Java (with treeified bins) worst-case per operation becomes O(log n), making worst-case overall **O(n log n)**. Practically, assume O(n).

# Space Complexity

* **O(k)** where `k` is number of distinct characters (keys) stored.
* **Worst-case:** `k = n` (all characters distinct) → **O(n)**.
* Also note HashMap has per-entry object overhead (so memory usage is higher than an `int[]`).

# Assumptions / Constraints

* **Inputs are non-null** (the code does not check for `null`; if `s` or `t` is `null`, it throws `NullPointerException`).
* **Case-sensitive**: `'A'` and `'a'` are different. If you want case-insensitive, normalize with `toLowerCase()` or `toUpperCase()`.
* **Normalization not handled**: Unicode combining characters (e.g., composed vs decomposed) are treated differently — use `java.text.Normalizer` if needed.
* The code iterates **UTF-16 `char` units** (`charAt`), so supplementary Unicode code points (outside BMP) are split into surrogate pairs — this may be wrong if you need to treat code points, not `char` units.
* No constraint on alphabet size — works for any `char` values but memory grows with distinct characters.

# Optimizations (suggestions & alternatives)

1. **Single-map frequency method (faster, less memory overhead):**

   * Increment counts for `s`, decrement for `t`. If any count goes negative during decrement, return `false` immediately. After the loop, if all counts are zero, return `true`.
   * This uses only one map/array and allows early exit.

   ```java
   // single-map approach (HashMap version)
   public boolean isAnagram(String s, String t) {
       if (s == null || t == null) return false;
       if (s.length() != t.length()) return false;

       HashMap<Character, Integer> counts = new HashMap<>();
       for (int i = 0; i < s.length(); i++) {
           counts.put(s.charAt(i), counts.getOrDefault(s.charAt(i), 0) + 1);
       }
       for (int i = 0; i < t.length(); i++) {
           char c = t.charAt(i);
           if (!counts.containsKey(c)) return false;
           counts.put(c, counts.get(c) - 1);
           if (counts.get(c) < 0) return false;
       }
       return true; // all zeros guaranteed because lengths are equal and no negatives encountered
   }
   ```
2. **Use an `int[]` frequency array when charset is small (e.g. only lowercase letters `a`–`z`):**

   * `int[] freq = new int[26];` — increment for `s`, decrement for `t`, then check all zeros or early exit on negative. This is the fastest and uses minimal memory **O(1)** (26).

   ```java
   // lowercase-only example
   public boolean isAnagram(String s, String t) {
       if (s.length() != t.length()) return false;
       int[] freq = new int[26];
       for (int i = 0; i < s.length(); i++) {
           freq[s.charAt(i) - 'a']++;
           freq[t.charAt(i) - 'a']--;
       }
       for (int x : freq) if (x != 0) return false;
       return true;
   }
   ```
3. **Use `countS.equals(countT)` instead of manual key loop**:

   * After building the two maps, `return countS.equals(countT);` is concise and correct; it compares keys and values.
4. **Sorting approach:** sort both strings' char arrays and compare — **O(n log n)** time, lower extra object overhead (besides arrays). Not optimal if you want linear time.

# Edge Cases (handled or potential failures)

* **Handled:**

  * **Different lengths**: function returns `false` early.
  * **Empty strings** (`""`, `""`): returns `true` (they are anagrams).
  * **Different characters / counts**: detected by map compare loop.
* **Not handled / may break or be surprising:**

  * **`null` inputs**: NPE — add `if (s == null || t == null) return false;` if desired.
  * **Unicode supplementary characters** (emojis, some Asian characters outside BMP): `charAt` operates on UTF-16 code units, not code points. Use `codePoints()` if code-point correctness is required.
  * **Unicode normalization & combining marks**: visually identical strings may not be equal byte-for-byte; normalize before counting (e.g., `Normalizer.normalize(str, Form.NFC)`).
  * **Case & whitespace differences**: `"Listen"` vs `"silent"` — you may want to normalize case and possibly remove spaces/punctuation.
  * **Large distinct-character sets**: HashMap overhead may be large; prefer arrays for bounded character sets.

# Quick cheat-sheet + mnemonic

* **One-line:** Check length → count frequencies → compare — **O(n)** time, **O(k)** space.
* **Mnemonic:** **L-A-C** → **L**ength check, **A**ccumulate counts, **C**ompare counts.

 

---


