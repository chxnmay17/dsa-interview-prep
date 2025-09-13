https://leetcode.com/problems/find-most-frequent-vowel-and-consonant/

# Algorithm 

1. Create a string `vowels = "aeiou"` to identify vowel characters.
2. Build a frequency map (`Map<Character,Integer>`) counting occurrences of every character in the input string `s`.
3. Walk through the map keys (unique characters). For each character:

   * If it appears in the `vowels` string, update `maxVowelFreq` with the maximum frequency seen among vowels.
   * Otherwise, update `maxConsonantFreq` with the maximum frequency seen among consonants.
4. Return the sum `maxVowelFreq + maxConsonantFreq`.

In short: count every character, find the most frequent vowel and the most frequent consonant, add their frequencies and return the result.

# Code

```java
class Solution {
    public int maxFreqSum(String s) {
        String vowels = "aeiou";
        Map<Character, Integer> freqMap = new HashMap<>();

       
        for (char ch : s.toCharArray()) {
            freqMap.put(ch, freqMap.getOrDefault(ch, 0) + 1);
        }

        int maxVowelFreq = 0;
        int maxConsonantFreq = 0;

        
        for (char ch : freqMap.keySet()) {
            int count = freqMap.get(ch);

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

# Time Complexity

* Building the frequency map: iterates over `s` once → O(n) where `n = s.length()`. Each `put` / `getOrDefault` on `HashMap` is average O(1).
* Iterating over `freqMap.keySet()`: O(k) where `k` is number of distinct characters (k ≤ n, and usually bounded by alphabet size).
* `vowels.indexOf(ch)` is O(5) constant-time (effectively O(1)).
* Overall: **O(n + k)** which is **O(n)** in typical notation (since k ≤ n and usually small constant bounded by alphabet).

# Space Complexity

* `freqMap` stores up to `k` entries (distinct characters). Space = **O(k)**.
* If input domain is limited (e.g., lowercase English letters), k ≤ 26 so space is effectively **O(1)**. Otherwise worst-case k = O(n).
* Additional constant space for a few ints and the `vowels` string → O(1).

# Assumptions / Constraints

* The code treats the characters exactly as they appear in `s`.

  * It only recognizes lowercase vowels `'a','e','i','o','u'` (because `vowels` is lowercase). Uppercase vowels (e.g., `'A'`) are treated as consonants (or non-vowels).
* Non-letter characters (digits, punctuation, whitespace, Unicode letters) are classified as "consonant" by the `else` branch — i.e., anything not found in `"aeiou"`.
* Input `s` is expected non-null. If `s` is `null`, the code throws `NullPointerException`.
* HashMap uses boxed `Character` and `Integer` (autoboxing overhead).
* No sorting required; stability not relevant.

# Optimizations (suggestions)

1. **Handle case-insensitivity (if needed)**:

   * Convert `s` to lowercase (`s = s.toLowerCase()`) if you want `'A'` to be treated as vowel.
2. **Limit to letters only (if desired)**:

   * Check `Character.isLetter(ch)` before counting, so digits/punctuation don't count as consonants.
3. **Faster vowel-check**:

   * Replace `vowels.indexOf(ch)` with a `boolean[]` or a `Set<Character>` for O(1) membership (currently `indexOf` is already constant small cost).

     ```java
     boolean[] isVowel = new boolean[128];
     isVowel['a'] = isVowel['e'] = ... = true;
     ```
4. **Reduce memory & boxing overhead** (if input is limited to ASCII lowercase):

   * Use an `int[26] counts` array instead of `HashMap<Character,Integer>`:

     * Increment `counts[ch - 'a']`.
     * Compute max vowel and consonant by indexing the array.
   * This lowers both time (less hashing) and space and avoids autoboxing.
5. **Single-pass alternative**:

   * If using an array of counts, you still need to compute max vowel and consonant; you can compute both after the single pass over counts (two loops over at most 26 entries). The counting itself is a single pass over `s`.
6. **Avoid iterating `freqMap.keySet()` twice**:

   * Current approach iterates once; fine. But if you wanted to compute on-the-fly you could update per-char frequencies and track running maxima for vowel/non-vowel (requires mapping char→index).

# Edge Cases

Handled implicitly:

* **Empty string** `""` → `freqMap` empty → `maxVowelFreq = 0`, `maxConsonantFreq = 0` → returns `0`.
* **All vowels** (e.g., `"aaaee"`) → `maxConsonantFreq` remains `0`, returns `maxVowelFreq`.
* **All consonants** → `maxVowelFreq` remains `0`, returns `maxConsonantFreq`.

Potentially problematic / not handled:

* **Null input** → throws `NullPointerException`.
* **Uppercase letters** are treated as consonants (likely unintended if input may include uppercase).
* **Non-letter characters** are treated as consonants (may or may not be intended).
* **Unicode characters / accented vowels** (e.g., `'á'`) are not recognized as vowels by `"aeiou"`.
* **Performance for huge alphabets**: If `s` contains many distinct Unicode code points, `k` can be large; `HashMap` still handles it but memory grows with number of distinct characters.

# Minimal improved version (example)

If you want a simple, efficient version for lowercase English letters that:

* treats only `a,e,i,o,u` as vowels,
* is case-insensitive,
* uses O(1) space (array of size 26):

```java
class Solution {
    public int maxFreqSum(String s) {
        if (s == null || s.isEmpty()) return 0;
        s = s.toLowerCase();
        int[] counts = new int[26];
        for (char ch : s.toCharArray()) {
            if (ch >= 'a' && ch <= 'z') counts[ch - 'a']++;
        }
        int maxVowel = 0, maxConsonant = 0;
        String vowels = "aeiou";
        for (char v : vowels.toCharArray()) maxVowel = Math.max(maxVowel, counts[v - 'a']);
        for (int i = 0; i < 26; i++) {
            char c = (char)('a' + i);
            if (vowels.indexOf(c) == -1) maxConsonant = Math.max(maxConsonant, counts[i]);
        }
        return maxVowel + maxConsonant;
    }
}
```

---


