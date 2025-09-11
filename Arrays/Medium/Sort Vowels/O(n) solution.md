https://leetcode.com/problems/sort-vowels-in-a-string/

# 1. Algorithm 

1. Convert the input string `s` into a character array `arr` so characters can be replaced in-place.
2. Create an integer frequency table `freq` (size 128) and iterate `arr` once; for every vowel encountered (`isVowel(c)`), increment `freq[c]`.
3. Define a hard-coded sorted vowel order string `sortedVowels = "AEIOUaeiou"` and a pointer `vowelPointer = 0` to scan that order.
4. Iterate `arr` again. When a vowel is found at position `i`, advance `vowelPointer` until you find the next vowel in `sortedVowels` whose `freq` is > 0. Replace `arr[i]` with that vowel and decrement its count in `freq`.
5. After processing all characters, convert `arr` back to a `String` and return it.

Effect: Non-vowel characters keep their original positions; vowels are replaced left-to-right with vowels taken in the order of `sortedVowels` (so all `'A'`s first, then `'E'`s, ..., then `'a'`s, etc.).

---

# 2. Code (as-is)

```java
class Solution {
    static boolean isVowel(char c) {
        return "aeiouAEIOU".indexOf(c) != -1;
    }

    public String sortVowels(String s) {
        char[] arr = s.toCharArray();
        
        // Count frequency of vowels (ASCII range 0-255)
        int[] freq = new int[128];  // ASCII char frequency table

        // Step 1: Count vowels
        for (char c : arr) {
            if (isVowel(c)) {
                freq[c]++;
            }
        }

        // Step 2: Sorted list of vowels (hardcoded order)
        String sortedVowels = "AEIOUaeiou";
        int vowelPointer = 0;

        // Step 3: Replace vowels in original string in sorted order
        for (int i = 0; i < arr.length; i++) {
            if (isVowel(arr[i])) {
                // Find next smallest vowel (in order)
                while (freq[sortedVowels.charAt(vowelPointer)] == 0) {
                    vowelPointer++;
                }
                arr[i] = sortedVowels.charAt(vowelPointer);
                freq[sortedVowels.charAt(vowelPointer)]--;
            }
        }

        return new String(arr);
    }
}
```

---

# 3. Time Complexity

**O(n)** where `n = s.length()`.

**Justification:**

* First loop (count vowels): O(n).
* Second loop (replace vowels): O(n) iterations. Inside that loop the `while` that advances `vowelPointer` can only move forward up to at most the size of `sortedVowels` (10) across the entire run — amortized constant work per vowel. So overall the second phase is O(n + constant) ⇒ O(n). All other operations are constant time. Therefore the full algorithm is O(n).

---

# 4. Space Complexity

**O(n)** (additional) in the worst case.

**Justification:**

* `arr` is a `char[]` copy of the input string → O(n) extra.
* `freq` is a fixed-size `int[128]` → O(1) (constant).
* `sortedVowels` and a few scalars → O(1).
  Therefore total extra space is dominated by `arr` ⇒ O(n).

(If you consider returning a new String built from `arr` as not extra, you could say auxiliary space is O(1) beyond the output, but practically `arr` is O(n).)

---

# 5. Assumptions / Constraints

* The code assumes input characters are within the ASCII range (indexing `freq` by `char` values into `int[128]`). Non-ASCII characters (e.g., Unicode > 127) will cause `ArrayIndexOutOfBoundsException`.
* The vowel set is limited to exactly the ten characters in `"aeiouAEIOU"`. No accented vowels (like `á`, `É`) are considered vowels.
* `sortedVowels = "AEIOUaeiou"` encodes the intended sorted order: all uppercase vowels first (A, E, I, O, U) then lowercase (a, e, i, o, u). This is a design choice (not lexicographic in Unicode codepoint order for mixed-case).
* The code treats `char` values as indices into `freq` without sanity checks. It assumes `char` numeric value < 128.
* Sorting stability: the algorithm only reorders vowels with a fixed predefined order; among vowels with the same character, original relative positions do not matter because they are identical.

---

# 6. Optimizations (and fixes / safer alternatives)

### Bugs / safety fixes to apply

1. **Fix the `freq` table size**: make `freq = new int[65536]` (or use a `Map<Character,Integer>`) if you want to safely index by `char` (16-bit Java `char`), or better, restrict/validate input to ASCII.
2. **Guard the `vowelPointer` advance**: the `while` loop uses `sortedVowels.charAt(vowelPointer)` without checking `vowelPointer` < `sortedVowels.length()`. Add a boundary check to prevent `IndexOutOfBoundsException` in case of logic errors or corrupt counts.

### Simpler / clearer implementation (recommended)

Collect vowels, sort them explicitly, then place them back. This is easier to reason about and avoids manual counting / pointer logic:

```java
// safer & easy-to-read alternative
public String sortVowelsFixed(String s) {
    char[] arr = s.toCharArray();
    List<Character> vowels = new ArrayList<>();
    for (char c : arr) if (isVowel(c)) vowels.add(c);

    // sort vowels by desired order: uppercase A,E,I,O,U first then lowercase a,e,i,o,u
    String order = "AEIOUaeiou";
    Map<Character, Integer> rank = new HashMap<>();
    for (int i = 0; i < order.length(); i++) rank.put(order.charAt(i), i);
    vowels.sort(Comparator.comparingInt(c -> rank.get(c)));

    // place sorted vowels back
    int idx = 0;
    for (int i = 0; i < arr.length; i++) {
        if (isVowel(arr[i])) arr[i] = vowels.get(idx++);
    }
    return new String(arr);
}
```

* Time: O(n log k) where k = number of vowels (sorting cost). In practice `k ≤ n` and for small alphabets k small; this is clearer.
* If you want guaranteed O(n), use counting with a mapping from the 10 vowel characters to counts (i.e., an `int[10]`), not `int[128]`.

### Memory-light counting approach

If you want the original counting-sort idea but safe and compact, map the 10 vowels to indices 0..9:

```java
String sortedVowels = "AEIOUaeiou";
int[] freq = new int[sortedVowels.length()];
// count
for (char c : arr) {
    int idx = sortedVowels.indexOf(c);
    if (idx != -1) freq[idx]++;
}
// replace
int p = 0;
for (int i = 0; i < arr.length; i++) {
    if (sortedVowels.indexOf(arr[i]) != -1) {
        while (freq[p] == 0) p++;
        arr[i] = sortedVowels.charAt(p);
        freq[p]--;
    }
}
```

* This removes unsafe `char` → `int` indexing and uses a fixed small array of size 10.

---

# 7. Edge Cases (handled / problematic)

### Properly handled:

* **Empty string** (`""`) → returns `""`.
* **No vowels** → `freq` stays zeros; the second loop never tries to replace anything → original string returned.
* **All vowels** → the algorithm will replace each vowel with the next vowel from `sortedVowels` according to counts, producing a fully reordered vowel-only string.

### Potentially problematic / unhandled:

* **Non-ASCII characters** (e.g., `é`, emoji): indexing `freq[c]` where `c >= 128` will throw `ArrayIndexOutOfBoundsException`.
* **If `sortedVowels` or `freq` logic gets out of sync** (e.g., due to a programming error or a mismatch between what `isVowel` recognizes and what `freq` counted), the `while` loop may increment `vowelPointer` past `sortedVowels.length() - 1` and `charAt(vowelPointer)` will raise `IndexOutOfBoundsException`. In correct runs this shouldn't happen because total vowel count from counting equals the number of vowel spots in pass 2, but defensive bounds checking is still recommended.
* **Case-sensitivity assumption**: vowels are specifically the 10 characters in `isVowel`. Any other character you might consider a vowel (international alphabets, digraphs, diacritics) will be ignored.
* **Mixed locale / encoding issues**: If the input includes combining characters or surrogate pairs, treating the input as `char[]` may split graphemes unexpectedly (rare for vowel-only problems but worth noting).

---


