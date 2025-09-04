



---

## 1. **Algorithm**

This code checks if a given string is a **palindrome**, considering only **alphanumeric characters** and ignoring **case differences**.

**Step-by-step flow:**

1. Start with two pointers:

   * `left` at the beginning of the string.
   * `right` at the end of the string.
2. Move `left` forward until it points to an alphanumeric character.
3. Move `right` backward until it points to an alphanumeric character.
4. Compare the characters at `left` and `right` (in lowercase for case-insensitivity).

   * If they differ → return `false`.
5. If they are equal, move both pointers closer (`left++`, `right--`).
6. Repeat until `left` crosses `right`.
7. If no mismatch is found → return `true` (the string is a palindrome).

---

## 2. **Code**

```java
class Solution {
    public static boolean isAlphanum(char c){
        return (c>='A'&&c<='Z')||(c>='a'&&c<='z')||(c>='0'&&c<='9');
    }
    
    public boolean isPalindrome(String s) {
        int left=0,right=s.length()-1;
        while(left<right){

            while(left<right && !isAlphanum(s.charAt(left)) ) left++;
            while(left<right && !isAlphanum(s.charAt(right)) ) right--;

            if(  (Character.toLowerCase(s.charAt(left)))!=(Character.toLowerCase(s.charAt(right)) ) ) return false;
            left++ ;
            right--;
        }
        return true;
       
    }
}
```

---

## 3. **Time Complexity**

* **O(n)**, where `n` is the length of the string.

  * Each character is checked at most once by either `left` or `right`.
  * The while loops just move pointers, so no nested scanning beyond `n`.

**Justification:** Both pointers traverse the string at most once.

---

## 4. **Space Complexity**

* **O(1)** → constant extra space.

  * No extra data structures are used.
  * Only pointers (`left`, `right`) and a few variables are maintained.

---

## 5. **Assumptions / Constraints**

* Input string `s` is non-null.
* Alphanumeric characters are restricted to standard **ASCII ranges**:

  * Uppercase A–Z
  * Lowercase a–z
  * Digits 0–9
* Palindrome check is **case-insensitive**.
* Non-alphanumeric characters are ignored.

---

## 6. **Optimizations (if any)**

The solution is already optimal (O(n) time, O(1) space).
Possible micro-optimizations:

* Instead of custom `isAlphanum`, could use `Character.isLetterOrDigit(c)` (built-in, more readable, supports Unicode).
* Preprocessing string (e.g., building a filtered lowercase string and then checking palindrome) → simpler but less space-efficient (O(n) space). The current two-pointer method is **better**.

---

## 7. **Edge Cases**

Handled:

* Empty string (`""`) → returns `true`.
* String with only non-alphanumeric chars (e.g., `"!!!"`) → returns `true` (since nothing to compare).
* Mixed case palindromes (e.g., `"A man, a plan, a canal: Panama"`) → returns `true`.
* Single character string → returns `true`.

Potentially tricky (but handled):

* Very long strings → still O(n), efficient.
* Unicode letters beyond A–Z, a–z, 0–9 → **not handled** with current `isAlphanum`. Would fail for inputs like `"ábba"`.

---


