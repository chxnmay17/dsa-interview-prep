
https://leetcode.com/problems/find-most-frequent-vowel-and-consonant/

# 1. Algorithm 

1. Create an integer array `freq[26]` to count occurrences of each lowercase English letter `a`–`z`.
2. Iterate through every character `ch` in the input string `s` and increment `freq[ch - 'a']`.
3. Define the string `vowels = "aeiou"`.
4. Scan the `freq` array (for `i = 0..25`):

   * If `freq[i] == 0` skip.
   * Recover the character `ch = (char)('a' + i)`.
   * If `ch` is found in `vowels`, update `maxVowelFreq` with the larger of itself and `freq[i]`.
   * Otherwise update `maxConsonantFreq` similarly.
5. Return the sum `maxVowelFreq + maxConsonantFreq`.

In short: count letters, find the most frequent vowel and the most frequent consonant, and return their sum.

# 2. Code

```java
class Solution {
    public int maxFreqSum(String s) {
        int[] freq = new int[26]; 

        for (char ch : s.toCharArray()) {
            freq[ch - 'a']++;
        }

        String vowels = "aeiou";
        int maxVowelFreq = 0;
        int maxConsonantFreq = 0;

        for (int i = 0; i < 26; i++) {
            int count = freq[i];
            if (count == 0)
                continue;

            char ch = (char) ('a' + i);

            if (vowels.indexOf(ch) != -1) {
                maxVowelFreq = Math.max(maxVowelFreq, count);
            } else {
                maxConsonantFreq = Math.max(maxConsonantFreq, count);
            }
        }

        return maxVowelFreq + maxConsonantFreq;
    }
}
```

# 3. Time Complexity

* Counting characters: iterating over `s.toCharArray()` is **O(n)** where `n = s.length()`.
* Scanning the fixed-size `freq` array: **O(26)** which is constant time.
* `vowels.indexOf(ch)` checks a 5-character string, constant time **O(1)**.
* Overall: **O(n)** time complexity.

  * Justification: the dominant cost is one pass over the input string; all other operations are constant-bounded.

# 4. Space Complexity

* `freq` array of size 26 → **O(1)** (constant extra space).
* A few primitive variables (`maxVowelFreq`, `maxConsonantFreq`) → **O(1)**.
* The `toCharArray()` call creates a char\[] of size `n` if `s.toCharArray()` is used (Java creates an array), which makes temporary extra memory **O(n)**.

  * If you iterate `charAt(i)` instead of `toCharArray()`, extra space reduces to **O(1)**.
* So:

  * Algorithmic extra space: **O(1)** (ignoring `toCharArray()` buffer).
  * If counting the `toCharArray()` buffer: **O(n)** transient.

# 5. Assumptions / Constraints

* The code assumes `s` contains only lowercase English letters `'a'`–`'z'`. (Index calculation `ch - 'a'` will break for other chars or uppercase.)
* Input `s` is non-`null`. If `s == null` the code will throw a `NullPointerException`.
* Vowel set is exactly `a, e, i, o, u` (English vowels only).
* Sorting stability, ordering, Unicode, or locale issues are not relevant here.
* The algorithm looks only for the single most-frequent vowel and the single most-frequent consonant (ties are resolved by `Math.max`, i.e., the maximum count value — which is correct for the stated goal).

# 6. Optimizations (if any)

1. **Avoid extra char\[] allocation**
   Replace `for (char ch : s.toCharArray())` with a loop using `s.charAt(i)` to avoid creating a temporary `char[]`:

   ```java
   for (int i = 0; i < s.length(); i++) {
       char ch = s.charAt(i);
       freq[ch - 'a']++;
   }
   ```

   This reduces transient space from O(n) to O(1).

2. **Single-pass max tracking**
   Instead of counting then scanning freq, you can update max vowel/consonant frequencies on the fly in the counting loop:

   ```java
   int maxVowel = 0, maxCons = 0;
   boolean isVowel = (ch == 'a' || ch == 'e' || ch == 'i' || ch == 'o' || ch == 'u');
   // after increment freq[idx]++:
   if (isVowel) maxVowel = Math.max(maxVowel, freq[idx]);
   else maxCons = Math.max(maxCons, freq[idx]);
   ```

   This keeps overall time O(n) and may slightly reduce constant factors by removing the second loop over 26 elements (small effect).

3. **Faster vowel test**
   Replace `vowels.indexOf(ch) != -1` with a boolean test or a precomputed boolean array for `isVowel` to avoid repeated string scans (though scanning a 5-char string is constant, a boolean lookup is cleaner and clearer):

   ```java
   boolean[] isVowel = new boolean[26];
   isVowel['a' - 'a'] = true; // etc.
   // then check isVowel[idx]
   ```

4. **Robustness**
   Add input validation (null check, optional toLowerCase()) if uppercase or non-letter inputs are possible.

# 7. Edge Cases

Handled implicitly:

* **Empty string `""`** → `freq` all zeros → returns `0`.
* **Only vowels or only consonants** → one of `maxVowelFreq` or `maxConsonantFreq` remains `0`; returns the correct sum.
* **Multiple characters with same max frequency** → code returns the maximum count value (ties are fine; it only needs the max), not the character identity.

Potentially problematic / not handled:

* **`s == null`** → throws `NullPointerException`.
* **Uppercase letters** (e.g., `'A'`) → `ch - 'a'` becomes negative and will cause `ArrayIndexOutOfBoundsException`. Not handled.
* **Non-letter characters** (digits, punctuation, spaces) → same problem, will likely cause out-of-range indices.
* **Unicode letters beyond a–z** → will break as above.

---

## Quick suggested robust version (small snippet)

If you want a drop-in safer version handling `null` and mixed case and without the `toCharArray()` allocation:

```java
public int maxFreqSum(String s) {
    if (s == null || s.isEmpty()) return 0;
    int[] freq = new int[26];
    boolean[] isVowel = new boolean[26];
    isVowel['a' - 'a'] = true;
    isVowel['e' - 'a'] = true;
    isVowel['i' - 'a'] = true;
    isVowel['o' - 'a'] = true;
    isVowel['u' - 'a'] = true;

    int maxVowel = 0, maxCons = 0;
    for (int i = 0; i < s.length(); i++) {
        char ch = Character.toLowerCase(s.charAt(i));
        if (ch < 'a' || ch > 'z') continue; // skip non a-z
        int idx = ch - 'a';
        freq[idx]++;
        if (isVowel[idx]) maxVowel = Math.max(maxVowel, freq[idx]);
        else maxCons = Math.max(maxCons, freq[idx]);
    }
    return maxVowel + maxCons;
}
```

This version is robust to case and non-letter chars, uses O(1) extra space, and does the job in a single pass.

---


