https://leetcode.com/problems/sort-vowels-in-a-string/

# 1. Algorithm 

1. If the input string `s` has length 1, return it immediately.
2. Convert the string to a `char[]` so we can modify characters in place.
3. Scan the array once and collect every vowel (both upper- and lower-case) into a list `vowels` in the order they appear.
4. Sort the `vowels` list (default Java character ordering).
5. Scan the character array again; every time a vowel position is encountered, replace that position with the next character from the sorted `vowels` list.
6. Construct and return a new `String` from the modified character array.

Effect: vowels remain at the same indices in the string but their characters become sorted (lexicographically, using Java `Character` ordering). Consonants and other characters stay unchanged.

# 2. Code

```java
class Solution {
    static boolean isVowel(char c){
        return "aeiouAEIOU".indexOf(c) != -1;
    }

    public String sortVowels(String s) {
        if (s.length() == 1) return s;

        char[] arr = s.toCharArray();
        List<Character> vowels = new ArrayList<>();

        
        for (char c : arr) {
            if (isVowel(c)) {
                vowels.add(c);
            }
        }

       
        Collections.sort(vowels);

        
        int vowelIndex = 0;
        for (int i = 0; i < arr.length; i++) {
            if (isVowel(arr[i])) {
                arr[i] = vowels.get(vowelIndex++);
            }
        }

        return new String(arr);
    }
}
```

# 3. Time Complexity

Let `n = s.length()` and `m` = number of vowels in `s` (so `0 ≤ m ≤ n`).

* First scan to collect vowels: O(n).
* Sorting the `vowels` list: `Collections.sort` on `m` elements is O(m log m).
* Second scan to place sorted vowels back: O(n).

Total time: **O(n + m log m)**.
In the worst case (all characters are vowels, `m = n`), this is **O(n log n)**.

Notes:

* `isVowel` uses `String.indexOf` on a 10-char string — constant-time per call (O(1) with small constant), so it doesn't change asymptotic analysis.
* Autoboxing overhead: storing vowels as `Character` objects adds runtime constant factors (boxing/unboxing).

# 4. Space Complexity

* `char[] arr` takes O(n) extra space (though derived from the input string; in Java strings are immutable so copying to `char[]` is typical).
* `vowels` list stores `m` `Character` objects -> O(m) extra space plus boxing overhead.
* Additional small constant-space variables.

Total extra space: **O(n + m)** which is O(n) in the worst case.

If considering only auxiliary *additional* space beyond the `char[]` output buffer and ignoring the `char[]` copy, it's O(m).

# 5. Assumptions / Constraints

* Vowel definition is exactly `a, e, i, o, u` in both lowercase and uppercase (`aeiouAEIOU`).
* Non-ASCII or accented vowels (e.g., `á`, `ü`) are **not** considered vowels by this code.
* Sorting uses Java's natural `Character` ordering: uppercase letters come before lowercase (`'A' < 'a'`), so `'A'` sorts before `'a'`. The code does **not** perform case-insensitive sorting unless changed.
* Input string fits in memory (no streaming / external memory handling).
* `Collections.sort` relies on `Comparable` for boxed `Character` — autoboxing happens when collecting into `List<Character>`.

# 6. Optimizations (if any)

Practical suggestions to improve speed or memory:

1. **Avoid boxing by using a `char[]` for vowels**
   Collect vowels into a primitive `char[]` or `CharBuffer` and use `Arrays.sort(charArray)` — this removes `Character` autoboxing overhead.

   ```java
   // Example idea (sketch)
   char[] vowelArr = new char[m];
   // fill vowelArr...
   Arrays.sort(vowelArr);
   // then write back from vowelArr
   ```

2. **Counting sort / frequency approach (O(n) time, O(1) extra space)**
   Since there are only 10 possible vowel characters (`A,E,I,O,U,a,e,i,o,u`), you can count occurrences of each vowel and then reconstruct them in sorted order by consuming counts. This converts the sort step to O(1) (constant) with respect to the alphabet size, making total time O(n). This also avoids boxing.

   * Steps:

     * First pass: count each vowel (map vowel → count).
     * Second pass: when replacing vowel positions, pick the smallest vowel with non-zero count and decrement.
   * Complexity: O(n) time, O(1) extra space (counts array of size 10).

3. **Case-insensitive sorting variant**
   If desired behavior is case-insensitive lexicographic order (e.g., treat `A` and `a` equal and break ties by original case), use a comparator that compares `Character.toLowerCase(c)` and then tie-break with `c` (or keep original case policy).

4. **In-place stable algorithm?**
   Because we must keep vowels at the same indices but sort the vowel characters, an in-place algorithm without extra `m` storage is not straightforward. Counting sort (counts only) is the best for O(1) extra besides the `char[]`.

# 7. Edge Cases

Handled or relevant cases:

* **Empty string `""`** — current code: `s.length()` returns 0, not 1, so it proceeds; `arr` becomes `[]`, loops do nothing, returns `new String(arr)` -> `""`. OK.
* **Length 1** — explicitly returned early (good).
* **No vowels** — `vowels` list is empty; sorting is no-op; the second pass never replaces anything; original string returned unchanged.
* **All vowels** — `m = n`. Works; the whole array gets sorted into lexicographic order (with Java char order).
* **Mixed non-letter characters** (digits, punctuation) — `isVowel` returns false; they are preserved.
* **Upper/lower mixing** — code sorts according to Java's `Character` order (uppercase before lowercase). If the intended behavior is different (e.g., case-insensitive), this could be incorrect.
* **Unicode/accents** — characters like `é`, `ø` are not recognized as vowels; they’ll be treated as non-vowels.
* **Large strings** — uses O(n) memory and `Collections.sort` on `m` elements. For very large strings with mostly vowels, the `m log m` cost and boxing overhead may be significant.

Potential breaking issues:

* If the expected vowel set is different (e.g., includes `y` or accented vowels), results will be incorrect.
* If the expected sorting should be case-insensitive, results will differ from that expectation.
* Performance degradation due to autoboxing if `m` is large (many vowels).

---


