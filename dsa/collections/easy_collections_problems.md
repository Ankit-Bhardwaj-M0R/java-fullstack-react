# DSA Problems – Solved Using ArrayList, HashMap, HashSet & Collections

> **Allowed collections only:**
> - `ArrayList` — ordered list, index access, used as stack (add/remove last)
> - `HashMap` — key-value pairs, O(1) lookup
> - `HashSet` — unique elements, O(1) lookup
> - `Collections` — utility methods (sort, reverse, max, min, frequency, rotate, swap)
>
> ```java
> import java.util.*;
> ```

---

# PART 1 – ARRAY PROBLEMS

---

## 1. Find the Maximum Element

**Problem:** Find the largest element in an array.

```
Input:  [3, 7, 1, 9, 2]
Output: 9
```

```java
public static int findMax(int[] arr) {
    ArrayList<Integer> list = new ArrayList<>();
    for (int n : arr) list.add(n);
    return Collections.max(list);
}
```

---

## 2. Find the Minimum Element

**Problem:** Find the smallest element in an array.

```
Input:  [3, 7, 1, 9, 2]
Output: 1
```

```java
public static int findMin(int[] arr) {
    ArrayList<Integer> list = new ArrayList<>();
    for (int n : arr) list.add(n);
    return Collections.min(list);
}
```

---

## 3. Reverse an Array

**Problem:** Reverse the elements of an array.

```
Input:  [1, 2, 3, 4, 5]
Output: [5, 4, 3, 2, 1]
```

```java
public static ArrayList<Integer> reverseArray(int[] arr) {
    ArrayList<Integer> list = new ArrayList<>();
    for (int n : arr) list.add(n);
    Collections.reverse(list);
    return list;
}
```

> `Collections.reverse(list)` reverses the list in-place in O(n).

---

## 4. Sum of Array Elements

**Problem:** Return the sum of all elements.

```
Input:  [1, 2, 3, 4, 5]
Output: 15
```

```java
public static int arraySum(int[] arr) {
    ArrayList<Integer> list = new ArrayList<>();
    for (int n : arr) list.add(n);
    int sum = 0;
    for (int n : list) sum += n;
    return sum;
}
```

---

## 5. Check if Array is Sorted (Ascending)

**Problem:** Return true if the array is sorted in ascending order.

```
Input:  [1, 2, 3, 4, 5]  → true
Input:  [1, 3, 2, 4]     → false
```

```java
public static boolean isSorted(int[] arr) {
    ArrayList<Integer> list = new ArrayList<>();
    for (int n : arr) list.add(n);

    ArrayList<Integer> sorted = new ArrayList<>(list);
    Collections.sort(sorted);

    return list.equals(sorted);
}
```

---

## 6. Find Second Largest Element

**Problem:** Find the second largest distinct element.

```
Input:  [10, 5, 8, 20, 15]
Output: 15
```

```java
public static int secondLargest(int[] arr) {
    ArrayList<Integer> list = new ArrayList<>();
    for (int n : arr) list.add(n);

    Collections.sort(list, Collections.reverseOrder());  // sort descending

    int first = list.get(0);
    for (int n : list) {
        if (n != first) return n;   // first element different from max
    }
    return -1;  // no second distinct element
}
```

---

## 7. Count Even and Odd Numbers

**Problem:** Count how many even and odd numbers exist.

```
Input:  [1, 2, 3, 4, 5]
Output: Even: 2, Odd: 3
```

```java
public static void countEvenOdd(int[] arr) {
    ArrayList<Integer> list = new ArrayList<>();
    for (int n : arr) list.add(n);

    int even = 0, odd = 0;
    for (int n : list) {
        if (n % 2 == 0) even++;
        else odd++;
    }
    System.out.println("Even: " + even + ", Odd: " + odd);
}
```

---

## 8. Move Zeros to the End

**Problem:** Move all zeros to the end while maintaining the order of non-zero elements.

```
Input:  [0, 1, 0, 3, 12]
Output: [1, 3, 12, 0, 0]
```

```java
public static ArrayList<Integer> moveZeros(int[] arr) {
    ArrayList<Integer> nonZero = new ArrayList<>();
    ArrayList<Integer> zeros   = new ArrayList<>();

    for (int n : arr) {
        if (n != 0) nonZero.add(n);
        else        zeros.add(0);
    }
    nonZero.addAll(zeros);
    return nonZero;
}
```

---

## 9. Find Duplicates in an Array

**Problem:** Find all duplicate elements.

```
Input:  [1, 2, 3, 2, 4, 1]
Output: [2, 1]
```

```java
public static ArrayList<Integer> findDuplicates(int[] arr) {
    HashSet<Integer> seen       = new HashSet<>();
    ArrayList<Integer> duplicates = new ArrayList<>();

    for (int n : arr) {
        if (!seen.add(n))   // add() returns false if already present
            duplicates.add(n);
    }
    return duplicates;
}
```

> `HashSet.add()` returns `false` when the element already exists — a clean duplicate detector.

---

## 10. Two Sum (Find Pair with Target Sum)

**Problem:** Find two indices whose values add up to the target.

```
Input:  arr = [2, 7, 11, 15], target = 9
Output: [0, 1]
```

```java
public static int[] twoSum(int[] arr, int target) {
    HashMap<Integer, Integer> map = new HashMap<>();  // value → index

    for (int i = 0; i < arr.length; i++) {
        int complement = target - arr[i];
        if (map.containsKey(complement))
            return new int[]{map.get(complement), i};
        map.put(arr[i], i);
    }
    return new int[]{};
}
```

---

## 11. Remove Duplicates from Sorted Array

**Problem:** Keep only unique elements (preserving first-occurrence order).

```
Input:  [1, 1, 2, 3, 3, 4]
Output: [1, 2, 3, 4]
```

```java
public static ArrayList<Integer> removeDuplicatesSorted(int[] arr) {
    HashSet<Integer> seen = new HashSet<>();
    ArrayList<Integer> result = new ArrayList<>();

    for (int n : arr) {
        if (seen.add(n))   // only add if not already seen
            result.add(n);
    }
    return result;
}
```

---

## 12. Rotate Array by K Positions (Right)

**Problem:** Rotate an array to the right by k steps.

```
Input:  [1, 2, 3, 4, 5], k = 2
Output: [4, 5, 1, 2, 3]
```

```java
public static ArrayList<Integer> rotateRight(int[] arr, int k) {
    ArrayList<Integer> list = new ArrayList<>();
    for (int n : arr) list.add(n);

    k = k % list.size();
    Collections.rotate(list, k);   // positive = right rotation
    return list;
}
```

> `Collections.rotate(list, distance)` — positive distance rotates right, negative rotates left.

---

## 13. Array Left Rotation

**Problem:** Perform d left rotations.

```
Input:  [1, 2, 3, 4, 5], d = 2
Output: [3, 4, 5, 1, 2]
```

```java
public static ArrayList<Integer> leftRotation(int[] arr, int d) {
    ArrayList<Integer> list = new ArrayList<>();
    for (int n : arr) list.add(n);

    d = d % list.size();
    Collections.rotate(list, -d);   // negative = left rotation
    return list;
}
```

---

## 14. Find the Missing Number (1 to N)

**Problem:** Given n-1 integers in range [1, n], find the missing one.

```
Input:  [1, 2, 4, 5, 6],  n = 6
Output: 3
```

```java
public static int missingNumber(int[] arr, int n) {
    ArrayList<Integer> full = new ArrayList<>();
    for (int i = 1; i <= n; i++) full.add(i);   // [1, 2, ..., n]

    for (int num : arr)
        full.remove(Integer.valueOf(num));        // remove by value, not index

    return full.get(0);
}
```

> `list.remove(Integer.valueOf(x))` removes by value; `list.remove(x)` removes by index.

---

## 15. Maximum Subarray Sum (Kadane's Algorithm)

**Problem:** Find the contiguous subarray with the largest sum.

```
Input:  [-2, 1, -3, 4, -1, 2, 1, -5, 4]
Output: 6
```

```java
public static int maxSubarraySum(int[] arr) {
    ArrayList<Integer> list = new ArrayList<>();
    for (int n : arr) list.add(n);

    int maxSum = list.get(0), currentSum = list.get(0);
    for (int i = 1; i < list.size(); i++) {
        int val = list.get(i);
        currentSum = Math.max(val, currentSum + val);
        maxSum = Math.max(maxSum, currentSum);
    }
    return maxSum;
}
```

---

## 16. Find Intersection of Two Arrays

**Problem:** Return elements that appear in both arrays.

```
Input:  [1, 2, 3, 4], [2, 4, 6]
Output: [2, 4]
```

```java
public static ArrayList<Integer> intersection(int[] a, int[] b) {
    HashSet<Integer> setA = new HashSet<>();
    for (int n : a) setA.add(n);

    ArrayList<Integer> result = new ArrayList<>();
    HashSet<Integer> added = new HashSet<>();   // avoid duplicates in result

    for (int n : b) {
        if (setA.contains(n) && added.add(n))
            result.add(n);
    }
    return result;
}
```

---

## 17. Find Union of Two Arrays

**Problem:** Return all distinct elements from both arrays.

```
Input:  [1, 2, 3], [2, 3, 4, 5]
Output: [1, 2, 3, 4, 5]
```

```java
public static ArrayList<Integer> union(int[] a, int[] b) {
    HashSet<Integer> seen = new HashSet<>();
    ArrayList<Integer> result = new ArrayList<>();

    for (int n : a) {
        if (seen.add(n)) result.add(n);
    }
    for (int n : b) {
        if (seen.add(n)) result.add(n);
    }
    return result;
}
```

---

## 18. Count Elements Less Than X

**Problem:** Count how many elements are less than a given value X.

```
Input:  [1, 5, 3, 7, 2], X = 4
Output: 3
```

```java
public static int countLessThan(int[] arr, int x) {
    ArrayList<Integer> list = new ArrayList<>();
    for (int n : arr) list.add(n);

    int count = 0;
    for (int n : list) {
        if (n < x) count++;
    }
    return count;
}
```

---

## 19. Check if Array Contains a Value

**Problem:** Check if a specific value exists.

```
Input:  [4, 2, 7, 1], target = 7
Output: true
```

```java
// O(n) using ArrayList
public static boolean contains(int[] arr, int target) {
    ArrayList<Integer> list = new ArrayList<>();
    for (int n : arr) list.add(n);
    return list.contains(target);
}

// O(1) using HashSet
public static boolean containsFast(int[] arr, int target) {
    HashSet<Integer> set = new HashSet<>();
    for (int n : arr) set.add(n);
    return set.contains(target);
}
```

---

## 20. Frequency of Each Element

**Problem:** Print the frequency of every element.

```
Input:  [1, 2, 2, 3, 1, 4]
Output: {1=2, 2=2, 3=1, 4=1}
```

```java
public static HashMap<Integer, Integer> frequency(int[] arr) {
    ArrayList<Integer> list = new ArrayList<>();
    for (int n : arr) list.add(n);

    HashMap<Integer, Integer> map = new HashMap<>();
    for (int n : list)
        map.put(n, map.getOrDefault(n, 0) + 1);
    return map;
}
```

**Using `Collections.frequency`:**
```java
public static void frequencyUsingCollections(int[] arr) {
    ArrayList<Integer> list = new ArrayList<>();
    for (int n : arr) list.add(n);

    HashSet<Integer> unique = new HashSet<>(list);
    for (int n : unique)
        System.out.println(n + " -> " + Collections.frequency(list, n));
}
```

> `Collections.frequency(list, obj)` counts occurrences of obj in the list in O(n).

---

## 21. Merge Two Sorted Arrays

**Problem:** Merge two sorted arrays into one sorted array.

```
Input:  [1, 3, 5], [2, 4, 6]
Output: [1, 2, 3, 4, 5, 6]
```

```java
public static ArrayList<Integer> mergeSorted(int[] a, int[] b) {
    ArrayList<Integer> result = new ArrayList<>();
    for (int n : a) result.add(n);
    for (int n : b) result.add(n);
    Collections.sort(result);
    return result;
}
```

**Two-pointer approach (O(n) for pre-sorted input):**
```java
public static ArrayList<Integer> mergeSortedTwoPointer(int[] a, int[] b) {
    ArrayList<Integer> result = new ArrayList<>();
    int i = 0, j = 0;
    while (i < a.length && j < b.length) {
        if (a[i] <= b[j]) result.add(a[i++]);
        else               result.add(b[j++]);
    }
    while (i < a.length) result.add(a[i++]);
    while (j < b.length) result.add(b[j++]);
    return result;
}
```

---

## 22. Find Leaders in an Array

**Problem:** An element is a leader if it is greater than all elements to its right.

```
Input:  [16, 17, 4, 3, 5, 2]
Output: [17, 5, 2]
```

```java
// ArrayList used as a stack: add() to push, remove(size-1) to pop
public static ArrayList<Integer> findLeaders(int[] arr) {
    ArrayList<Integer> stack = new ArrayList<>();
    stack.add(arr[arr.length - 1]);   // rightmost is always a leader

    for (int i = arr.length - 2; i >= 0; i--) {
        if (arr[i] > stack.get(stack.size() - 1)) {
            stack.add(arr[i]);
        }
    }
    Collections.reverse(stack);
    return stack;
}
```

> ArrayList as a stack: `add()` = push, `get(size-1)` = peek, `remove(size-1)` = pop.

---

## 23. Find the Majority Element (Appears > n/2 Times)

**Problem:** Find the element that appears more than n/2 times.

```
Input:  [3, 3, 4, 2, 3, 3, 3]
Output: 3
```

```java
public static int majorityElement(int[] arr) {
    HashMap<Integer, Integer> freq = new HashMap<>();
    int threshold = arr.length / 2;

    for (int n : arr) {
        freq.put(n, freq.getOrDefault(n, 0) + 1);
        if (freq.get(n) > threshold) return n;
    }
    return -1;
}
```

---

## 24. Print All Pairs with Given Sum

**Problem:** Print all pairs in the array that sum to a target value.

```
Input:  [1, 5, 3, 7, 4, 2], target = 6
Output: (1, 5), (4, 2)
```

```java
public static ArrayList<int[]> printPairs(int[] arr, int target) {
    HashSet<Integer> seen = new HashSet<>();
    ArrayList<int[]> pairs = new ArrayList<>();

    for (int n : arr) {
        int complement = target - n;
        if (seen.contains(complement))
            pairs.add(new int[]{complement, n});
        seen.add(n);
    }
    return pairs;
}
```

---

## 25. Prefix Sum Array (Cumulative Sum)

**Problem:** Build a prefix sum array where each element is the cumulative sum.

```
Input:  [1, 2, 3, 4, 5]
Output: [1, 3, 6, 10, 15]
```

```java
public static ArrayList<Integer> prefixSum(int[] arr) {
    ArrayList<Integer> prefix = new ArrayList<>();
    int running = 0;
    for (int n : arr) {
        running += n;
        prefix.add(running);
    }
    return prefix;
}
```

---

# PART 2 – STRING PROBLEMS

---

## 26. Reverse a String

**Problem:** Return the string reversed.

```
Input:  "hello"
Output: "olleh"
```

```java
// ArrayList used as a stack to reverse characters
public static String reverseString(String s) {
    ArrayList<Character> stack = new ArrayList<>();
    for (char c : s.toCharArray()) stack.add(c);   // push

    StringBuilder sb = new StringBuilder();
    while (!stack.isEmpty())
        sb.append(stack.remove(stack.size() - 1));  // pop from end
    return sb.toString();
}
```

---

## 27. Check if a String is a Palindrome

**Problem:** Return true if the string reads the same forward and backward.

```
Input:  "racecar"  → true
Input:  "hello"    → false
```

```java
public static boolean isPalindrome(String s) {
    s = s.toLowerCase();
    ArrayList<Character> list = new ArrayList<>();
    for (char c : s.toCharArray()) list.add(c);

    int left = 0, right = list.size() - 1;
    while (left < right) {
        if (!list.get(left).equals(list.get(right))) return false;
        left++;
        right--;
    }
    return true;
}
```

---

## 28. Count Vowels in a String

**Problem:** Count the number of vowels (a, e, i, o, u).

```
Input:  "hello world"
Output: 3
```

```java
public static int countVowels(String s) {
    HashSet<Character> vowels = new HashSet<>();
    for (char c : "aeiouAEIOU".toCharArray()) vowels.add(c);

    int count = 0;
    for (char c : s.toCharArray()) {
        if (vowels.contains(c)) count++;
    }
    return count;
}
```

> `HashSet.contains()` is O(1) — faster than `String.indexOf()`.

---

## 29. Check Anagram

**Problem:** Check if two strings are anagrams of each other.

```
Input:  "listen", "silent"  → true
Input:  "hello", "world"    → false
```

```java
public static boolean isAnagram(String s1, String s2) {
    if (s1.length() != s2.length()) return false;

    HashMap<Character, Integer> freq = new HashMap<>();

    for (char c : s1.toLowerCase().toCharArray())
        freq.put(c, freq.getOrDefault(c, 0) + 1);

    for (char c : s2.toLowerCase().toCharArray()) {
        if (!freq.containsKey(c)) return false;
        freq.put(c, freq.get(c) - 1);
        if (freq.get(c) < 0) return false;
    }
    return true;
}
```

**Sorting approach:**
```java
public static boolean isAnagramSort(String s1, String s2) {
    ArrayList<Character> l1 = new ArrayList<>();
    ArrayList<Character> l2 = new ArrayList<>();
    for (char c : s1.toLowerCase().toCharArray()) l1.add(c);
    for (char c : s2.toLowerCase().toCharArray()) l2.add(c);
    Collections.sort(l1);
    Collections.sort(l2);
    return l1.equals(l2);
}
```

---

## 30. First Non-Repeating Character

**Problem:** Find the first character that does not repeat.

```
Input:  "aabbc"  → 'c'
Input:  "aabb"   → '\0' (none)
```

```java
public static char firstNonRepeating(String s) {
    // HashMap for frequency + ArrayList to preserve insertion order
    HashMap<Character, Integer> freq = new HashMap<>();
    ArrayList<Character> order = new ArrayList<>();

    for (char c : s.toCharArray()) {
        if (!freq.containsKey(c)) order.add(c);   // track first-seen order
        freq.put(c, freq.getOrDefault(c, 0) + 1);
    }

    for (char c : order) {
        if (freq.get(c) == 1) return c;
    }
    return '\0';
}
```

> `ArrayList` preserves insertion order; `HashMap` gives O(1) frequency lookup.

---

## 31. Count Occurrences of a Character

**Problem:** Count how many times a character appears in a string.

```
Input:  "mississippi", 's'
Output: 4
```

```java
public static int countChar(String s, char target) {
    ArrayList<Character> list = new ArrayList<>();
    for (char c : s.toCharArray()) list.add(c);
    return Collections.frequency(list, target);
}
```

---

## 32. Remove Duplicates from a String

**Problem:** Remove duplicate characters, keeping only the first occurrence.

```
Input:  "programming"
Output: "progamin"
```

```java
public static String removeDuplicates(String s) {
    HashSet<Character> seen = new HashSet<>();
    ArrayList<Character> result = new ArrayList<>();

    for (char c : s.toCharArray()) {
        if (seen.add(c))   // add returns false if already present
            result.add(c);
    }

    StringBuilder sb = new StringBuilder();
    for (char c : result) sb.append(c);
    return sb.toString();
}
```

---

## 33. Check if Two Strings are Rotations

**Problem:** Check if one string is a rotation of another.

```
Input:  "abcde", "cdeab"  → true
```

```java
public static boolean isRotation(String s1, String s2) {
    if (s1.length() != s2.length()) return false;

    ArrayList<Character> list = new ArrayList<>();
    for (char c : s1.toCharArray()) list.add(c);

    for (int k = 0; k < list.size(); k++) {
        Collections.rotate(list, 1);   // rotate right by 1
        StringBuilder sb = new StringBuilder();
        for (char c : list) sb.append(c);
        if (sb.toString().equals(s2)) return true;
    }
    return false;
}
```

---

## 34. Count Words in a String

**Problem:** Count the number of words in a sentence.

```
Input:  "Hello World from Java"
Output: 4
```

```java
public static int countWords(String s) {
    if (s == null || s.trim().isEmpty()) return 0;
    ArrayList<String> words = new ArrayList<>();
    for (String w : s.trim().split("\\s+")) words.add(w);
    return words.size();
}
```

---

## 35. Reverse Words in a Sentence

**Problem:** Reverse the order of words in a sentence.

```
Input:  "Hello World"
Output: "World Hello"
```

```java
public static String reverseWords(String s) {
    ArrayList<String> words = new ArrayList<>();
    for (String w : s.trim().split("\\s+")) words.add(w);
    Collections.reverse(words);
    return String.join(" ", words);
}
```

---

## 36. Check if String Contains Only Digits

**Problem:** Return true if the string contains only numeric digits.

```
Input:  "12345"   → true
Input:  "123a5"   → false
```

```java
public static boolean isNumeric(String s) {
    if (s == null || s.isEmpty()) return false;
    ArrayList<Character> list = new ArrayList<>();
    for (char c : s.toCharArray()) list.add(c);
    for (char c : list) {
        if (!Character.isDigit(c)) return false;
    }
    return true;
}
```

---

## 37. Caesar Cipher (Shift by K)

**Problem:** Shift each letter by K positions forward in the alphabet.

```
Input:  "abc", k = 3
Output: "def"
```

```java
public static String caesarCipher(String s, int k) {
    k = k % 26;
    ArrayList<Character> chars = new ArrayList<>();
    for (char c : s.toCharArray()) chars.add(c);

    ArrayList<Character> result = new ArrayList<>();
    for (char c : chars) {
        if (Character.isLetter(c)) {
            char base = Character.isUpperCase(c) ? 'A' : 'a';
            result.add((char) ((c - base + k) % 26 + base));
        } else {
            result.add(c);
        }
    }

    StringBuilder sb = new StringBuilder();
    for (char c : result) sb.append(c);
    return sb.toString();
}
```

---

## 38. Longest Common Prefix

**Problem:** Find the longest common prefix among an array of strings.

```
Input:  ["flower", "flow", "flight"]
Output: "fl"
```

```java
public static String longestCommonPrefix(String[] strs) {
    if (strs == null || strs.length == 0) return "";

    ArrayList<String> list = new ArrayList<>();
    for (String s : strs) list.add(s);
    Collections.sort(list);   // lexicographic sort

    // After sorting, only first and last need to be compared
    String first = list.get(0);
    String last  = list.get(list.size() - 1);

    int i = 0;
    while (i < first.length() && i < last.length()
           && first.charAt(i) == last.charAt(i)) i++;

    return first.substring(0, i);
}
```

> After sorting, the lexicographically smallest and largest strings cover all possible prefix comparisons for the whole list.

---

## 39. Check Balanced Parentheses

**Problem:** Check if a string of brackets is balanced.

```
Input:  "(()())"   → true
Input:  "(()"      → false
Input:  "{[()]}"   → true
```

```java
// ArrayList used as a stack
public static boolean isBalanced(String s) {
    HashMap<Character, Character> pairs = new HashMap<>();
    pairs.put(')', '(');
    pairs.put(']', '[');
    pairs.put('}', '{');

    HashSet<Character> opening = new HashSet<>(pairs.values());
    ArrayList<Character> stack = new ArrayList<>();

    for (char c : s.toCharArray()) {
        if (opening.contains(c)) {
            stack.add(c);                                    // push opening bracket
        } else if (pairs.containsKey(c)) {
            if (stack.isEmpty()) return false;
            char top = stack.remove(stack.size() - 1);      // pop
            if (top != pairs.get(c)) return false;
        }
    }
    return stack.isEmpty();
}
```

---

## 40. String Compression (Run-Length Encoding)

**Problem:** Compress a string using run-length encoding.

```
Input:  "aaabbc"
Output: "a3b2c1"
```

```java
public static String compress(String s) {
    ArrayList<String> parts = new ArrayList<>();
    int i = 0;
    char[] chars = s.toCharArray();

    while (i < chars.length) {
        char c = chars[i];
        int count = 0;
        while (i < chars.length && chars[i] == c) { i++; count++; }
        parts.add(c + String.valueOf(count));
    }
    return String.join("", parts);
}
```

---

## 41. Find All Permutations of a String

**Problem:** Print all permutations of a given string.

```
Input:  "abc"
Output: [abc, acb, bac, bca, cab, cba]
```

```java
public static ArrayList<String> permutations(String s) {
    ArrayList<String> result = new ArrayList<>();
    ArrayList<Character> chars = new ArrayList<>();
    for (char c : s.toCharArray()) chars.add(c);
    generatePermutations(chars, new ArrayList<>(), result);
    return result;
}

private static void generatePermutations(ArrayList<Character> remaining,
                                          ArrayList<Character> current,
                                          ArrayList<String> result) {
    if (remaining.isEmpty()) {
        StringBuilder sb = new StringBuilder();
        for (char c : current) sb.append(c);
        result.add(sb.toString());
        return;
    }
    for (int i = 0; i < remaining.size(); i++) {
        char ch = remaining.remove(i);
        current.add(ch);
        generatePermutations(remaining, current, result);
        current.remove(current.size() - 1);   // backtrack
        remaining.add(i, ch);
    }
}
```

> `ArrayList` acts as both the "remaining characters" pool and the "current path" stack.

---

## 42. Check if String is a Subsequence

**Problem:** Check if string `s` is a subsequence of string `t`.

```
Input:  s = "ace", t = "abcde"  → true
```

```java
// ArrayList used as a queue (remove from front = index 0)
public static boolean isSubsequence(String s, String t) {
    ArrayList<Character> queue = new ArrayList<>();
    for (char c : s.toCharArray()) queue.add(c);

    for (char c : t.toCharArray()) {
        if (!queue.isEmpty() && queue.get(0) == c)
            queue.remove(0);   // matched — dequeue from front
    }
    return queue.isEmpty();    // all chars of s matched
}
```

---

## 43. Count Frequency of Each Character

**Problem:** Print the frequency of each character in a string.

```
Input:  "hello"
Output: {h=1, e=1, l=2, o=1}
```

```java
public static HashMap<Character, Integer> charFrequency(String s) {
    ArrayList<Character> chars = new ArrayList<>();
    for (char c : s.toCharArray()) chars.add(c);

    HashMap<Character, Integer> map = new HashMap<>();
    for (char c : chars)
        map.put(c, map.getOrDefault(c, 0) + 1);
    return map;
}
```

**Sort characters by frequency (most frequent first):**
```java
public static ArrayList<Character> sortedByFrequency(String s) {
    HashMap<Character, Integer> freq = charFrequency(s);

    ArrayList<Character> chars = new ArrayList<>(freq.keySet());
    // Sort descending by frequency using Collections.sort with comparator
    Collections.sort(chars, (a, b) -> freq.get(b) - freq.get(a));
    return chars;
}
```

---

## 44. Pangram Check

**Problem:** Check if a sentence contains every letter of the alphabet at least once.

```
Input:  "The quick brown fox jumps over the lazy dog"
Output: true
```

```java
public static boolean isPangram(String s) {
    HashSet<Character> seen = new HashSet<>();
    for (char c : s.toLowerCase().toCharArray()) {
        if (c >= 'a' && c <= 'z') seen.add(c);
    }
    return seen.size() == 26;
}
```

---

## 45. Top K Frequent Elements

**Problem:** Find the K most frequent elements in an array.

```
Input:  [1, 1, 1, 2, 2, 3], k = 2
Output: [1, 2]
```

```java
public static ArrayList<Integer> topKFrequent(int[] arr, int k) {
    // Step 1: count frequencies
    HashMap<Integer, Integer> freq = new HashMap<>();
    for (int n : arr)
        freq.put(n, freq.getOrDefault(n, 0) + 1);

    // Step 2: put all keys in a list and sort by frequency descending
    ArrayList<Integer> keys = new ArrayList<>(freq.keySet());
    Collections.sort(keys, (a, b) -> freq.get(b) - freq.get(a));

    // Step 3: return first k elements
    return new ArrayList<>(keys.subList(0, k));
}
```

---

# Quick Reference

## ArrayList as a Stack (LIFO)

```java
ArrayList<Integer> stack = new ArrayList<>();
stack.add(x);                        // push
stack.get(stack.size() - 1);        // peek
stack.remove(stack.size() - 1);     // pop
stack.isEmpty();                     // check empty
```

## ArrayList as a Queue (FIFO)

```java
ArrayList<Integer> queue = new ArrayList<>();
queue.add(x);        // enqueue (add to end)
queue.get(0);        // peek front
queue.remove(0);     // dequeue (remove from front)
queue.isEmpty();     // check empty
```

## HashMap Patterns

```java
map.getOrDefault(key, 0)              // get with default
map.put(key, map.getOrDefault(k,0)+1) // count / increment
map.containsKey(key)                  // check existence
map.keySet()                          // all keys
map.values()                          // all values
map.entrySet()                        // key-value pairs
```

## HashSet Patterns

```java
set.add(x)        // returns false if already present (duplicate check)
set.contains(x)   // O(1) lookup
set.remove(x)     // remove element
new HashSet<>(list) // deduplicate a list
```

## Collections Utility Methods

```java
Collections.sort(list)                  // sort ascending
Collections.sort(list, (a,b)->b-a)      // sort descending
Collections.reverse(list)               // reverse in-place
Collections.max(list)                   // maximum element
Collections.min(list)                   // minimum element
Collections.frequency(list, obj)        // count occurrences
Collections.rotate(list, k)             // rotate (+ right, - left)
Collections.swap(list, i, j)            // swap two elements
Collections.shuffle(list)               // random shuffle
Collections.fill(list, val)             // fill all with value
```
