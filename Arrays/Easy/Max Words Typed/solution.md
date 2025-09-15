https://leetcode.com/problems/maximum-number-of-words-you-can-type/description/

---

### 1. **Algorithm **

The goal is to count how many words in the given string `text` **do not contain** any characters from the string `brokenLetters`.

**Step-by-step:**

1. Convert `brokenLetters` into a `HashSet<Character>` (`brokenSet`) for O(1) lookups.
2. Convert the entire `text` into a character array `letters`.
3. Initialize:

   * `res` → counts the number of words that can be typed.
   * `check` → indicates whether we are currently checking a word (reset after each space).
   * `contain` → flags whether the current word contains any broken letter.
4. Iterate over each character `letters[i]`:

   * If `check` is `true` and the current character is in `brokenSet`, set `contain = true` and `check = false` (stop checking this word further).
   * If the character is a space `' '` **or** it is the last character:

     * Reset `check = true` for the next word.
     * If `contain` is still `false`, increment `res` (this word has no broken letters).
     * Reset `contain = false` for the next word.
5. Return `res` as the count of words that can be typed.

---

### 2. **Code (as-is)**

```java
class Solution {
    public int canBeTypedWords(String text, String brokenLetters) {
        Set<Character> brokenSet = new HashSet<>();
        for (char c : brokenLetters.toCharArray()) {
            brokenSet.add(c);
        }
        char[] letters = text.toCharArray();
        int res = 0;
        boolean check = true;
        boolean contain = false;
        int size = text.length() - 1;
        for (int i = 0; i <= size; i++) {
            if (check && brokenSet.contains(letters[i])) {
                contain = true;
                check = false;
            }
            if (letters[i] == ' ' || i == size) {
                check = true;
                if (!contain) res++;
                contain = false;
            }
        }
        return res;
    }
}
```

---

### 3. **Time Complexity**

* **Building the set:** O(m), where `m` = length of `brokenLetters`.
* **Iterating through `text`:** O(n), where `n` = length of `text`.
* Total → **O(n + m)**

  * Justification: Each character of `brokenLetters` and `text` is visited once; HashSet lookups are O(1) on average.

---

### 4. **Space Complexity**

* `brokenSet` stores at most all unique characters of `brokenLetters` → O(k) where k ≤ 26 for lowercase letters (or the input alphabet size).
* `letters` is a char array copy of `text` → O(n).
* Booleans and counters → O(1).
* Total → **O(n + k)** (dominant term is O(n) due to copying the string into a char array).

---

### 5. **Assumptions / Constraints**

* Input `text` contains words separated by single spaces (no leading/trailing spaces expected).
* `brokenLetters` contains unique characters or may contain duplicates (duplicates don’t affect correctness).
* Character set is assumed to be standard ASCII letters (lowercase in the original LeetCode problem).
* Sorting/stability not relevant; only direct character checks are performed.

---

### 6. **Optimizations (if any)**

* **Avoid extra char array:** Instead of `text.toCharArray()`, iterate directly over `text.charAt(i)` to save O(n) space.
* **Early exit per word:** Already implemented (`check` flag prevents repeated lookups after a broken letter is found).
* Overall time complexity is already optimal O(n + m).

---

### 7. **Edge Cases**

Handled or to consider:

* **Empty `brokenLetters`:** Every word is typable; output is the total number of words.
* **Empty `text`:** Returns 0 (no words).
* **All letters broken:** Returns 0.
* **Single-word text:** Correctly counts even when there are no spaces.
* **Multiple consecutive spaces (if present):** Not explicitly handled; would count empty “words” as words. If such input is possible, trimming and splitting carefully would be safer.

---


