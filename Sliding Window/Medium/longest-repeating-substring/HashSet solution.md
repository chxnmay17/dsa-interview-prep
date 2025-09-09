https://neetcode.io/problems/longest-substring-without-duplicates?list=neetcode150

# Algorithm 

1. Use a **sliding window** `[left, right)` that represents the current substring with all unique characters.
2. Keep a `HashSet` (`hs`) of characters currently in the window.
3. Move `right` forward one character at a time:

   * If `s.charAt(right)` is **not** in `hs`, add it, increment `right`, and update `res` as the window length (`right - left`).
   * If the character **is** already in `hs` (duplicate inside the window), remove `s.charAt(left)` from `hs` and increment `left` (shrink window from the left) until the duplicate is removed.
4. Repeat until `right` reaches the end of the string. `res` holds the length of the longest substring without repeating characters.

Invariant: `hs` always contains the distinct characters in `s[left..right-1]`.

# Code

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        int left=0,right=0,res=0;
        Set<Character> hs=new HashSet<>();
        while(right<s.length()){
            if(!hs.contains(s.charAt(right))){
                hs.add(s.charAt(right));
                right++;
                res=Math.max(res,right-left);
            }
            else{
                hs.remove(s.charAt(left));
                left++;
            }

        }
        return res;


}
}
```

# Time Complexity

**O(n)**, where `n = s.length()`.

**Justification:**

* Each character is added to the set at most once and removed at most once. `right` advances up to `n` times, and `left` advances up to `n` times. So total operations on indices ≈ `2n`.
* HashSet `add`, `remove`, and `contains` are amortized O(1).
* Thus overall amortized time is O(n).

(Technical note: in pathological HashSet collision cases worst-case could be worse, but for algorithmic analysis we use amortized O(1) for set ops.)

# Space Complexity

**O(min(n, σ))**, where `σ` is the size of the character set (alphabet).

**Justification:**

* The HashSet stores the characters currently inside the window; in the worst case it can contain every distinct character in the string (so ≤ `n`), but it is also upper-bounded by the alphabet size (for ASCII, σ = 128; for Unicode BMP σ = 65,536).
* So space is proportional to number of distinct characters held at once.

# Assumptions / Constraints

* **Input not null**: The code does **not** check `s == null`. Passing `null` will throw `NullPointerException`.
* **Character granularity**: Uses `s.charAt(i)` which works on Java `char` (UTF-16 code units). Characters outside the Basic Multilingual Plane (BMP) are represented as surrogate pairs and will be treated as two separate `char`s — if you need to treat full Unicode codepoints as single symbols, this code is not sufficient.
* **Indexing fits in `int`**: assumes `s.length()` fits in a Java `int` (practical).
* **Single-threaded**: not designed for concurrent use.
* **HashSet average performance**: assumes average O(1) for `contains/add/remove`.

# Optimizations (suggestions)

1. **Use last-seen index array / map to jump `left`** (preferred):

   * Keep an array/map `lastIndex[c]` = last index where `c` appeared.
   * When you see `c` at `right`, set `left = max(left, lastIndex[c] + 1)`. This prevents removing one-by-one and makes the code a single pass with fewer operations.
   * This reduces the number of `remove` calls and is generally faster in practice.

Optimized (Map) Java snippet:

```java
public int lengthOfLongestSubstring(String s) {
    Map<Character,Integer> last = new HashMap<>();
    int res = 0, left = 0;
    for (int right = 0; right < s.length(); right++) {
        char c = s.charAt(right);
        if (last.containsKey(c) && last.get(c) >= left) {
            left = last.get(c) + 1;
        }
        last.put(c, right);
        res = Math.max(res, right - left + 1);
    }
    return res;
}
```

Optimized (array) for ASCII (faster due to direct indexing):

```java
public int lengthOfLongestSubstring(String s) {
    int[] last = new int[128]; // or 256
    Arrays.fill(last, -1);
    int res = 0, left = 0;
    for (int right = 0; right < s.length(); right++) {
        char c = s.charAt(right);
        left = Math.max(left, last[c] + 1);
        res = Math.max(res, right - left + 1);
        last[c] = right;
    }
    return res;
}
```

2. **If you must support full Unicode codepoints**, iterate the string’s code points (`s.codePoints()`) and use an `Int2Int` map for last indices (or `Map<Integer,Integer>`).
3. **Early exits / checks**: check `s == null` and return `0` or throw an exception per your API contract.

# Edge Cases

Handled correctly:

* **Empty string `""`** → returns `0`.
* **All unique characters** → returns `n`.
* **All same characters** → returns `1`.
* **Mixed patterns** → algorithm correctly expands and shrinks the window to find max.

Potentially problematic / not handled:

* **`null` input** → causes `NullPointerException` (add `if (s == null) return 0;` if desired).
* **Unicode codepoints outside BMP** (emoji, certain rare characters): `s.charAt()` will split them into two `char` surrogate halves, counting them as two characters. If you require correct codepoint-level behavior, iterate codepoints instead.
* **Very large distinct-character alphabets** (e.g., many unique emoji/codepoints) — memory usage for `HashSet` or `Map` will grow to hold many entries; still bounded, but might be large.
* **HashSet worst-case performance**: pathological hash collisions could degrade performance (rare in practice).

---


