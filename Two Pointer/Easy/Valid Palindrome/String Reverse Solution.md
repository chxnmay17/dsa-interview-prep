https://leetcode.com/problems/valid-palindrome/description/
# 1) Algorithm 

1. Take the input string `s`.
2. Remove every character that is **not** a letter or digit (i.e., keep only `A–Z`, `a–z`, `0–9`).
3. Convert the remaining characters to lowercase.
4. Create a `StringBuilder` from this cleaned string and reverse it.
5. Compare the cleaned string with its reversed version.
6. If they are equal, return `true` (it’s a palindrome); otherwise return `false`.

---

# 2) Code 

```java


class Solution {
    
    public boolean isPalindrome(String s) {
        
        String normalizedString =s.replaceAll("[^A-Za-z0-9]","").toLowerCase();
        StringBuilder forReverse=new StringBuilder(normalizedString);
        return normalizedString.equals(forReverse.reverse().toString());
    }
}
```

---

# 3) Time Complexity

**O(n)**, where `n` is the length of the input string.

* `replaceAll("[^A-Za-z0-9]","")` scans the string and builds a new one → **O(n)**.
* `.toLowerCase()` scans the cleaned string → **O(n)** (in terms of the cleaned length).
* Building `StringBuilder`, reversing, and comparing (`equals`) each take **O(n)** in the cleaned length.
* Summed together → linear overall.

---

# 4) Space Complexity

**O(n)** auxiliary space.

* The cleaned string (`normalizedString`) uses up to **O(n)** space.
* The `StringBuilder` and the reversed `.toString()` create additional strings of comparable size.
* Regex processing also consumes extra transient memory.
* Overall auxiliary space remains linear.

---

# 5) Assumptions / Constraints

* **Character set**: Only ASCII letters/digits are considered (`A–Z`, `a–z`, `0–9`).
  Non-ASCII letters (e.g., `é`, `ñ`, `अ`) are **removed**, not normalized.
* **Case handling**: Lowercasing uses the platform default locale; for ASCII it’s effectively locale-independent.
* **Null input**: Not handled — passing `null` will throw a `NullPointerException`.
* **Input size**: Works in linear time but creates multiple full-size strings; very large inputs may stress memory.
* **Sorting stability**: Not applicable.

---

# 6) Optimizations (if any)

1. **Two-pointer in-place check (no extra strings, no regex)**

   * Skip non-alphanumeric characters while moving pointers from both ends.
   * Compare characters case-insensitively using `Character.toLowerCase`.
   * **Time**: O(n). **Space**: O(1).

   ```java
   class Solution {
       public boolean isPalindrome(String s) {
           if (s == null) return false; // or true, by definition—decide policy
           int i = 0, j = s.length() - 1;
           while (i < j) {
               while (i < j && !Character.isLetterOrDigit(s.charAt(i))) i++;
               while (i < j && !Character.isLetterOrDigit(s.charAt(j))) j--;
               char left = Character.toLowerCase(s.charAt(i));
               char right = Character.toLowerCase(s.charAt(j));
               if (left != right) return false;
               i++; j--;
           }
           return true;
       }
   }
   ```

2. **If you must keep the current approach**

   * Precompile the pattern and reuse it if this method is called frequently (avoid recompiling regex each call).
   * Avoid building the reversed string: after building `normalizedString`, do a two-pointer scan on it to save one extra string.

3. **Unicode-aware palindrome (if needed)**

   * Use `Character.isLetterOrDigit(int codePoint)` and iterate by code points.
   * Normalize text (e.g., NFC/NFKD) to handle composed/combining characters before comparison.

---

# 7) Edge Cases

* **Empty string** → becomes `""` and compares equal to its reverse → returns **true**.
* **Only non-alphanumeric characters** (e.g., `"!!!"`, `" , ; "`) → cleaned to empty → **true**.
* **Single character** (e.g., `"a"`, `"7"`) → **true**.
* **Mixed punctuation and case** (e.g., `"A man, a plan, a canal: Panama"`) → **true** as expected.
* **Null input** → **throws** `NullPointerException` (current code).
* **Non-ASCII letters** (e.g., `"AñA"`, `"été"`) → non-ASCII letters are removed; may yield incorrect semantic result.
* **Combining marks** (e.g., `"éve"` with combining acute) → combining marks removed; may change meaning.
* **Very long strings** → may cause higher GC pressure due to multiple large temporary strings.
