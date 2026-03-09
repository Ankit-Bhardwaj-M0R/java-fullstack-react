# Easy String Problems – HackerRank Style

---

## 1. Reverse a String

**Problem:** Given a string, return it reversed.

**Example:**
```
Input:  "hello"
Output: "olleh"
```

**Java Solution:**
```java
public static String reverseString(String s) {
    return new StringBuilder(s).reverse().toString();
}
```

**Manual approach:**
```java
public static String reverseString(String s) {
    char[] chars = s.toCharArray();
    int left = 0, right = chars.length - 1;
    while (left < right) {
        char temp = chars[left];
        chars[left++] = chars[right];
        chars[right--] = temp;
    }
    return new String(chars);
}
```

---

## 2. Check if a String is a Palindrome

**Problem:** Return true if the string reads the same forward and backward (ignore case).

**Example:**
```
Input:  "racecar"
Output: true

Input:  "hello"
Output: false
```

**Java Solution:**
```java
public static boolean isPalindrome(String s) {
    s = s.toLowerCase();
    int left = 0, right = s.length() - 1;
    while (left < right) {
        if (s.charAt(left) != s.charAt(right)) return false;
        left++;
        right--;
    }
    return true;
}
```

---

## 3. Count Vowels in a String

**Problem:** Count the number of vowels (a, e, i, o, u) in a given string.

**Example:**
```
Input:  "hello world"
Output: 3
```

**Java Solution:**
```java
public static int countVowels(String s) {
    int count = 0;
    String vowels = "aeiouAEIOU";
    for (char c : s.toCharArray()) {
        if (vowels.indexOf(c) != -1) count++;
    }
    return count;
}
```

---

## 4. Check Anagram

**Problem:** Given two strings, check if they are anagrams of each other.

**Example:**
```
Input:  "listen", "silent"
Output: true

Input:  "hello", "world"
Output: false
```

**Java Solution:**
```java
public static boolean isAnagram(String s1, String s2) {
    if (s1.length() != s2.length()) return false;
    int[] freq = new int[26];
    for (char c : s1.toLowerCase().toCharArray()) freq[c - 'a']++;
    for (char c : s2.toLowerCase().toCharArray()) freq[c - 'a']--;
    for (int f : freq) if (f != 0) return false;
    return true;
}
```

---

## 5. First Non-Repeating Character

**Problem:** Find the first character in a string that does not repeat.

**Example:**
```
Input:  "aabbc"
Output: 'c'

Input:  "aabb"
Output: '\0' (no unique character)
```

**Java Solution:**
```java
public static char firstNonRepeating(String s) {
    LinkedHashMap<Character, Integer> map = new LinkedHashMap<>();
    for (char c : s.toCharArray())
        map.put(c, map.getOrDefault(c, 0) + 1);
    for (Map.Entry<Character, Integer> entry : map.entrySet())
        if (entry.getValue() == 1) return entry.getKey();
    return '\0';
}
```

---

## 6. Count Occurrences of a Character

**Problem:** Given a string and a character, count how many times the character appears.

**Example:**
```
Input:  "mississippi", 's'
Output: 4
```

**Java Solution:**
```java
public static int countChar(String s, char target) {
    int count = 0;
    for (char c : s.toCharArray())
        if (c == target) count++;
    return count;
}
```

---

## 7. Remove Duplicates from a String

**Problem:** Remove duplicate characters from a string, keeping only the first occurrence.

**Example:**
```
Input:  "programming"
Output: "progamin"
```

**Java Solution:**
```java
public static String removeDuplicates(String s) {
    StringBuilder sb = new StringBuilder();
    Set<Character> seen = new LinkedHashSet<>();
    for (char c : s.toCharArray()) {
        if (seen.add(c)) sb.append(c);
    }
    return sb.toString();
}
```

---

## 8. Check if Two Strings are Rotations

**Problem:** Check if one string is a rotation of another.

**Example:**
```
Input:  "abcde", "cdeab"
Output: true
```

**Java Solution:**
```java
public static boolean isRotation(String s1, String s2) {
    if (s1.length() != s2.length()) return false;
    return (s1 + s1).contains(s2);
}
```

---

## 9. Count Words in a String

**Problem:** Count the number of words in a sentence (words separated by spaces).

**Example:**
```
Input:  "Hello World from Java"
Output: 4
```

**Java Solution:**
```java
public static int countWords(String s) {
    if (s == null || s.trim().isEmpty()) return 0;
    return s.trim().split("\\s+").length;
}
```

---

## 10. Reverse Words in a Sentence

**Problem:** Reverse the order of words in a sentence.

**Example:**
```
Input:  "Hello World"
Output: "World Hello"
```

**Java Solution:**
```java
public static String reverseWords(String s) {
    String[] words = s.trim().split("\\s+");
    StringBuilder sb = new StringBuilder();
    for (int i = words.length - 1; i >= 0; i--) {
        sb.append(words[i]);
        if (i != 0) sb.append(" ");
    }
    return sb.toString();
}
```

---

## 11. Check if String Contains Only Digits

**Problem:** Return true if the string contains only numeric digits.

**Example:**
```
Input:  "12345"
Output: true

Input:  "123a5"
Output: false
```

**Java Solution:**
```java
public static boolean isNumeric(String s) {
    return s != null && s.matches("\\d+");
}
```

---

## 12. Caesar Cipher (Shift by K)

**Problem:** Shift each letter in the string by K positions forward in the alphabet.

**Example:**
```
Input:  "abc", k = 3
Output: "def"
```

**Java Solution:**
```java
public static String caesarCipher(String s, int k) {
    k = k % 26;
    StringBuilder sb = new StringBuilder();
    for (char c : s.toCharArray()) {
        if (Character.isLetter(c)) {
            char base = Character.isUpperCase(c) ? 'A' : 'a';
            sb.append((char) ((c - base + k) % 26 + base));
        } else {
            sb.append(c);
        }
    }
    return sb.toString();
}
```

---

## 13. Longest Common Prefix

**Problem:** Find the longest common prefix among an array of strings.

**Example:**
```
Input:  ["flower","flow","flight"]
Output: "fl"
```

**Java Solution:**
```java
public static String longestCommonPrefix(String[] strs) {
    if (strs == null || strs.length == 0) return "";
    String prefix = strs[0];
    for (int i = 1; i < strs.length; i++) {
        while (!strs[i].startsWith(prefix))
            prefix = prefix.substring(0, prefix.length() - 1);
    }
    return prefix;
}
```

---

## 14. Check Balanced Parentheses

**Problem:** Check if a string of parentheses is balanced.

**Example:**
```
Input:  "(()())"
Output: true

Input:  "(()"
Output: false
```

**Java Solution:**
```java
public static boolean isBalanced(String s) {
    int count = 0;
    for (char c : s.toCharArray()) {
        if (c == '(') count++;
        else if (c == ')') count--;
        if (count < 0) return false;
    }
    return count == 0;
}
```

---

## 15. String Compression (Run-Length Encoding)

**Problem:** Compress a string using run-length encoding.

**Example:**
```
Input:  "aaabbc"
Output: "a3b2c1"
```

**Java Solution:**
```java
public static String compress(String s) {
    StringBuilder sb = new StringBuilder();
    int i = 0;
    while (i < s.length()) {
        char c = s.charAt(i);
        int count = 0;
        while (i < s.length() && s.charAt(i) == c) {
            i++;
            count++;
        }
        sb.append(c).append(count);
    }
    return sb.toString();
}
```

---

## 16. Find All Permutations of a String

**Problem:** Print all permutations of a given string.

**Example:**
```
Input:  "abc"
Output: abc, acb, bac, bca, cab, cba
```

**Java Solution:**
```java
public static void permutations(String s, String current) {
    if (s.isEmpty()) {
        System.out.println(current);
        return;
    }
    for (int i = 0; i < s.length(); i++) {
        permutations(s.substring(0, i) + s.substring(i + 1), current + s.charAt(i));
    }
}
// Call: permutations("abc", "");
```

---

## 17. Check if String is a Subsequence

**Problem:** Check if string `s` is a subsequence of string `t`.

**Example:**
```
Input:  s = "ace", t = "abcde"
Output: true
```

**Java Solution:**
```java
public static boolean isSubsequence(String s, String t) {
    int i = 0;
    for (int j = 0; j < t.length() && i < s.length(); j++) {
        if (s.charAt(i) == t.charAt(j)) i++;
    }
    return i == s.length();
}
```

---

## 18. Convert String to Integer (atoi)

**Problem:** Implement `parseInt` manually without using built-in parsing methods.

**Example:**
```
Input:  "123"
Output: 123

Input:  "-456"
Output: -456
```

**Java Solution:**
```java
public static int myAtoi(String s) {
    s = s.trim();
    if (s.isEmpty()) return 0;
    int sign = 1, i = 0, result = 0;
    if (s.charAt(0) == '-') { sign = -1; i++; }
    else if (s.charAt(0) == '+') { i++; }
    while (i < s.length() && Character.isDigit(s.charAt(i))) {
        result = result * 10 + (s.charAt(i++) - '0');
    }
    return sign * result;
}
```

---

## 19. Count Frequency of Each Character

**Problem:** Print the frequency of each character in a string.

**Example:**
```
Input:  "hello"
Output: h=1, e=1, l=2, o=1
```

**Java Solution:**
```java
public static Map<Character, Integer> charFrequency(String s) {
    Map<Character, Integer> map = new LinkedHashMap<>();
    for (char c : s.toCharArray())
        map.put(c, map.getOrDefault(c, 0) + 1);
    return map;
}
```

---

## 20. Pangram Check

**Problem:** Check if a sentence contains every letter of the alphabet at least once.

**Example:**
```
Input:  "The quick brown fox jumps over the lazy dog"
Output: true
```

**Java Solution:**
```java
public static boolean isPangram(String s) {
    boolean[] seen = new boolean[26];
    for (char c : s.toLowerCase().toCharArray())
        if (c >= 'a' && c <= 'z') seen[c - 'a'] = true;
    for (boolean b : seen) if (!b) return false;
    return true;
}
```

---

## Tips for HackerRank String Problems

| Topic | Key Methods |
|-------|------------|
| Reverse | `StringBuilder.reverse()`, two-pointer |
| Frequency | `HashMap`, `int[26]` array |
| Palindrome | Two-pointer from both ends |
| Anagram | Sort both strings or use frequency array |
| Substring | `String.contains()`, `String.indexOf()` |
| Split/Trim | `String.trim()`, `String.split("\\s+")` |
| Char ops | `Character.isDigit()`, `Character.isLetter()`, `Character.toLowerCase()` |
