# DSA Problems – Solved Using Java Collections

> All solutions use the **Java Collections Framework** (`ArrayList`, `LinkedList`,
> `ArrayDeque`, `PriorityQueue`, `HashMap`, `TreeMap`, `TreeSet`, `Collections` utilities, etc.)
>
> ```java
> import java.util.*;
> import java.util.stream.*;
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
// Collections.max() on a List
public static int findMax(int[] arr) {
    List<Integer> list = new ArrayList<>();
    for (int n : arr) list.add(n);
    return Collections.max(list);
}
```

> **Key:** `Collections.max(Collection)` returns the maximum element in O(n).

---

## 2. Find the Minimum Element

**Problem:** Find the smallest element in an array.

```
Input:  [3, 7, 1, 9, 2]
Output: 1
```

```java
public static int findMin(int[] arr) {
    List<Integer> list = new ArrayList<>();
    for (int n : arr) list.add(n);
    return Collections.min(list);
}
```

---

## 3. Reverse an Array

**Problem:** Reverse the elements of an array in-place.

```
Input:  [1, 2, 3, 4, 5]
Output: [5, 4, 3, 2, 1]
```

```java
public static List<Integer> reverseArray(int[] arr) {
    List<Integer> list = new ArrayList<>();
    for (int n : arr) list.add(n);
    Collections.reverse(list);   // mutates list in-place, O(n)
    return list;
}
```

> **Key:** `Collections.reverse(List)` reverses in-place — no manual swapping needed.

---

## 4. Sum of Array Elements

**Problem:** Return the sum of all elements.

```
Input:  [1, 2, 3, 4, 5]
Output: 15
```

```java
public static int arraySum(int[] arr) {
    List<Integer> list = new ArrayList<>();
    for (int n : arr) list.add(n);
    return list.stream().mapToInt(Integer::intValue).sum();
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
    List<Integer> list = new ArrayList<>();
    for (int n : arr) list.add(n);

    List<Integer> sorted = new ArrayList<>(list);
    Collections.sort(sorted);           // O(n log n)

    return list.equals(sorted);         // compares element-by-element
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
    // TreeSet keeps unique elements in sorted order
    TreeSet<Integer> set = new TreeSet<>();
    for (int n : arr) set.add(n);

    set.pollLast();          // remove the largest
    return set.last();       // next largest
}
```

> **Key:** `TreeSet` is a sorted, duplicate-free set. `pollLast()` removes max.

---

## 7. Count Even and Odd Numbers

**Problem:** Count how many even and odd numbers exist.

```
Input:  [1, 2, 3, 4, 5]
Output: Even: 2, Odd: 3
```

```java
public static void countEvenOdd(int[] arr) {
    List<Integer> list = new ArrayList<>();
    for (int n : arr) list.add(n);

    long even = list.stream().filter(n -> n % 2 == 0).count();
    long odd  = list.stream().filter(n -> n % 2 != 0).count();

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
public static List<Integer> moveZeros(int[] arr) {
    List<Integer> nonZero = new ArrayList<>();
    List<Integer> zeros   = new ArrayList<>();

    for (int n : arr) {
        if (n != 0) nonZero.add(n);
        else        zeros.add(0);
    }

    nonZero.addAll(zeros);   // append zeros at the end
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
public static List<Integer> findDuplicates(int[] arr) {
    Set<Integer> seen       = new HashSet<>();
    List<Integer> duplicates = new ArrayList<>();

    for (int n : arr) {
        if (!seen.add(n))          // add() returns false if already present
            duplicates.add(n);
    }
    return duplicates;
}
```

> **Key:** `HashSet.add()` returns `false` when the element already exists — a clean duplicate detector.

---

## 10. Two Sum (Find Pair with Target Sum)

**Problem:** Find two indices whose values add up to the target.

```
Input:  arr = [2, 7, 11, 15], target = 9
Output: [0, 1]
```

```java
public static int[] twoSum(int[] arr, int target) {
    Map<Integer, Integer> map = new HashMap<>();   // value → index

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

**Problem:** Keep only unique elements (preserving order).

```
Input:  [1, 1, 2, 3, 3, 4]
Output: [1, 2, 3, 4]
```

```java
public static List<Integer> removeDuplicatesSorted(int[] arr) {
    // LinkedHashSet preserves insertion order and removes duplicates
    Set<Integer> set = new LinkedHashSet<>();
    for (int n : arr) set.add(n);
    return new ArrayList<>(set);
}
```

> **Key:** `LinkedHashSet` = HashSet (no duplicates) + insertion-order iteration.

---

## 12. Rotate Array by K Positions (Right)

**Problem:** Rotate an array to the right by k steps.

```
Input:  [1, 2, 3, 4, 5], k = 2
Output: [4, 5, 1, 2, 3]
```

```java
public static List<Integer> rotateRight(int[] arr, int k) {
    List<Integer> list = new ArrayList<>();
    for (int n : arr) list.add(n);

    k = k % list.size();
    Collections.rotate(list, k);   // positive k = right rotation
    return list;
}
```

> **Key:** `Collections.rotate(list, distance)` — positive distance rotates right.

---

## 13. Array Left Rotation

**Problem:** Perform d left rotations.

```
Input:  [1, 2, 3, 4, 5], d = 2
Output: [3, 4, 5, 1, 2]
```

```java
public static List<Integer> leftRotation(int[] arr, int d) {
    List<Integer> list = new ArrayList<>();
    for (int n : arr) list.add(n);

    d = d % list.size();
    Collections.rotate(list, -d);   // negative distance = left rotation
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
    List<Integer> list = new ArrayList<>();
    for (int i = 1; i <= n; i++) list.add(i);    // [1..n]

    for (int num : arr) list.remove(Integer.valueOf(num));  // remove found

    return list.get(0);   // what's left is the missing number
}
```

> **Key:** `list.remove(Integer.valueOf(x))` removes by value (not by index).

---

## 15. Maximum Subarray Sum (Kadane's Algorithm)

**Problem:** Find the contiguous subarray with the largest sum.

```
Input:  [-2, 1, -3, 4, -1, 2, 1, -5, 4]
Output: 6  (subarray [4, -1, 2, 1])
```

```java
public static int maxSubarraySum(int[] arr) {
    List<Integer> list = new ArrayList<>();
    for (int n : arr) list.add(n);

    int maxSum = list.get(0), currentSum = list.get(0);
    for (int i = 1; i < list.size(); i++) {
        currentSum = Math.max(list.get(i), currentSum + list.get(i));
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
public static List<Integer> intersection(int[] a, int[] b) {
    Set<Integer> setA = new HashSet<>();
    for (int n : a) setA.add(n);

    Set<Integer> setB = new HashSet<>();
    for (int n : b) setB.add(n);

    setA.retainAll(setB);              // setA ∩ setB  (modifies setA in-place)
    return new ArrayList<>(setA);
}
```

> **Key:** `Set.retainAll(other)` keeps only elements present in both sets.

---

## 17. Find Union of Two Arrays

**Problem:** Return all distinct elements from both arrays.

```
Input:  [1, 2, 3], [2, 3, 4, 5]
Output: [1, 2, 3, 4, 5]
```

```java
public static List<Integer> union(int[] a, int[] b) {
    Set<Integer> set = new LinkedHashSet<>();
    for (int n : a) set.add(n);
    for (int n : b) set.add(n);       // duplicates ignored automatically
    return new ArrayList<>(set);
}
```

> **Key:** `Set.addAll()` / adding to a Set naturally deduplicates.

---

## 18. Count Elements Less Than X

**Problem:** Count how many elements are less than a given value X.

```
Input:  [1, 5, 3, 7, 2], X = 4
Output: 3
```

```java
public static long countLessThan(int[] arr, int x) {
    List<Integer> list = new ArrayList<>();
    for (int n : arr) list.add(n);
    return list.stream().filter(n -> n < x).count();
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
public static boolean contains(int[] arr, int target) {
    List<Integer> list = new ArrayList<>();
    for (int n : arr) list.add(n);
    return list.contains(target);   // O(n) linear search
}
```

**O(1) lookup version using HashSet:**
```java
public static boolean containsFast(int[] arr, int target) {
    Set<Integer> set = new HashSet<>();
    for (int n : arr) set.add(n);
    return set.contains(target);   // O(1) average
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
public static Map<Integer, Integer> frequency(int[] arr) {
    Map<Integer, Integer> map = new LinkedHashMap<>();
    for (int n : arr)
        map.merge(n, 1, Integer::sum);   // merge(key, value, remappingFn)
    return map;
}
```

**Using `Collections.frequency`:**
```java
public static void frequencyUsingCollections(int[] arr) {
    List<Integer> list = new ArrayList<>();
    for (int n : arr) list.add(n);

    Set<Integer> unique = new LinkedHashSet<>(list);
    for (int n : unique)
        System.out.println(n + " -> " + Collections.frequency(list, n));
}
```

> **Key:** `map.merge(key, 1, Integer::sum)` is the idiomatic Java 8+ way to count.

---

## 21. Merge Two Sorted Arrays

**Problem:** Merge two sorted arrays into one sorted array.

```
Input:  [1, 3, 5], [2, 4, 6]
Output: [1, 2, 3, 4, 5, 6]
```

```java
// Using PriorityQueue (min-heap)
public static List<Integer> mergeSorted(int[] a, int[] b) {
    PriorityQueue<Integer> pq = new PriorityQueue<>();
    for (int n : a) pq.offer(n);
    for (int n : b) pq.offer(n);

    List<Integer> result = new ArrayList<>();
    while (!pq.isEmpty()) result.add(pq.poll());  // poll() returns min
    return result;
}
```

> **Key:** `PriorityQueue` is a min-heap by default. `poll()` always returns the smallest.

---

## 22. Find Leaders in an Array

**Problem:** An element is a leader if it is greater than all elements to its right.

```
Input:  [16, 17, 4, 3, 5, 2]
Output: [17, 5, 2]
```

```java
public static List<Integer> findLeaders(int[] arr) {
    Deque<Integer> stack = new ArrayDeque<>();
    List<Integer> leaders = new ArrayList<>();

    stack.push(arr[arr.length - 1]);   // rightmost is always a leader

    for (int i = arr.length - 2; i >= 0; i--) {
        if (arr[i] > stack.peek()) {
            stack.push(arr[i]);
        }
    }

    while (!stack.isEmpty()) leaders.add(stack.pop());
    Collections.reverse(leaders);
    return leaders;
}
```

> **Key:** Use `ArrayDeque` as a Stack (`push`/`pop`/`peek`) — preferred over `Stack` class in Java.

---

## 23. Find the Majority Element (Appears > n/2 Times)

**Problem:** Find the element that appears more than n/2 times.

```
Input:  [3, 3, 4, 2, 3, 3, 3]
Output: 3
```

```java
public static int majorityElement(int[] arr) {
    Map<Integer, Integer> freq = new HashMap<>();
    int threshold = arr.length / 2;

    for (int n : arr) {
        freq.merge(n, 1, Integer::sum);
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
public static List<int[]> printPairs(int[] arr, int target) {
    Set<Integer> seen = new HashSet<>();
    List<int[]> pairs = new ArrayList<>();

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

**Problem:** Build a prefix sum array where each element is the cumulative sum up to that index.

```
Input:  [1, 2, 3, 4, 5]
Output: [1, 3, 6, 10, 15]
```

```java
public static List<Integer> prefixSum(int[] arr) {
    List<Integer> prefix = new ArrayList<>();
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
// Using ArrayDeque as a Stack
public static String reverseString(String s) {
    Deque<Character> stack = new ArrayDeque<>();
    for (char c : s.toCharArray()) stack.push(c);

    StringBuilder sb = new StringBuilder();
    while (!stack.isEmpty()) sb.append(stack.pop());
    return sb.toString();
}
```

> **Key:** Push all chars onto the stack, then pop (LIFO) to reverse order.

---

## 27. Check if a String is a Palindrome

**Problem:** Return true if the string reads the same forward and backward.

```
Input:  "racecar"  → true
Input:  "hello"    → false
```

```java
// Using ArrayDeque as a Deque (double-ended queue)
public static boolean isPalindrome(String s) {
    s = s.toLowerCase();
    Deque<Character> deque = new ArrayDeque<>();
    for (char c : s.toCharArray()) deque.addLast(c);

    while (deque.size() > 1) {
        if (!deque.pollFirst().equals(deque.pollLast())) return false;
    }
    return true;
}
```

> **Key:** `ArrayDeque` as a deque — compare front and back simultaneously.

---

## 28. Count Vowels in a String

**Problem:** Count the number of vowels (a, e, i, o, u).

```
Input:  "hello world"
Output: 3
```

```java
public static int countVowels(String s) {
    Set<Character> vowels = new HashSet<>(
        Arrays.asList('a','e','i','o','u','A','E','I','O','U')
    );

    List<Character> chars = new ArrayList<>();
    for (char c : s.toCharArray()) chars.add(c);

    return (int) chars.stream().filter(vowels::contains).count();
}
```

> **Key:** `Set.contains()` is O(1) — faster lookup than `String.indexOf()`.

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

    Map<Character, Integer> freq = new HashMap<>();

    for (char c : s1.toLowerCase().toCharArray())
        freq.merge(c, 1, Integer::sum);

    for (char c : s2.toLowerCase().toCharArray()) {
        if (!freq.containsKey(c)) return false;
        freq.merge(c, -1, Integer::sum);
        if (freq.get(c) < 0) return false;
    }
    return true;
}
```

**Sorting approach with Collections:**
```java
public static boolean isAnagramSort(String s1, String s2) {
    List<Character> l1 = new ArrayList<>(), l2 = new ArrayList<>();
    for (char c : s1.toLowerCase().toCharArray()) l1.add(c);
    for (char c : s2.toLowerCase().toCharArray()) l2.add(c);

    Collections.sort(l1);
    Collections.sort(l2);
    return l1.equals(l2);
}
```

---

## 30. First Non-Repeating Character

**Problem:** Find the first character in a string that does not repeat.

```
Input:  "aabbc"  → 'c'
Input:  "aabb"   → '\0' (none)
```

```java
public static char firstNonRepeating(String s) {
    // LinkedHashMap preserves insertion order
    Map<Character, Integer> map = new LinkedHashMap<>();
    for (char c : s.toCharArray())
        map.merge(c, 1, Integer::sum);

    for (Map.Entry<Character, Integer> entry : map.entrySet())
        if (entry.getValue() == 1) return entry.getKey();

    return '\0';
}
```

---

## 31. Count Occurrences of a Character

**Problem:** Count how many times a character appears in a string.

```
Input:  "mississippi", 's'
Output: 4
```

```java
public static int countChar(String s, char target) {
    List<Character> list = new ArrayList<>();
    for (char c : s.toCharArray()) list.add(c);
    return Collections.frequency(list, target);
}
```

> **Key:** `Collections.frequency(Collection, Object)` counts occurrences in O(n).

---

## 32. Remove Duplicates from a String

**Problem:** Remove duplicate characters, keeping only first occurrence.

```
Input:  "programming"
Output: "progamin"
```

```java
public static String removeDuplicates(String s) {
    Set<Character> seen = new LinkedHashSet<>();   // preserves insertion order
    for (char c : s.toCharArray()) seen.add(c);

    StringBuilder sb = new StringBuilder();
    for (char c : seen) sb.append(c);
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

    // Convert to List<Character> and use Collections.rotate
    List<Character> list = new ArrayList<>();
    for (char c : s1.toCharArray()) list.add(c);

    for (int k = 0; k < list.size(); k++) {
        Collections.rotate(list, 1);
        StringBuilder sb = new StringBuilder();
        for (char c : list) sb.append(c);
        if (sb.toString().equals(s2)) return true;
    }
    return false;
}
```

**Cleaner string-based check:**
```java
public static boolean isRotationFast(String s1, String s2) {
    return s1.length() == s2.length() && (s1 + s1).contains(s2);
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
    List<String> words = Arrays.asList(s.trim().split("\\s+"));
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
    List<String> words = new ArrayList<>(Arrays.asList(s.trim().split("\\s+")));
    Collections.reverse(words);
    return String.join(" ", words);
}
```

> **Key:** `Collections.reverse(List)` + `String.join()` — clean, one-liner logic.

---

## 36. Check if String Contains Only Digits

**Problem:** Return true if the string contains only numeric digits.

```
Input:  "12345"   → true
Input:  "123a5"   → false
```

```java
public static boolean isNumeric(String s) {
    List<Character> chars = new ArrayList<>();
    for (char c : s.toCharArray()) chars.add(c);
    return !chars.isEmpty() && chars.stream().allMatch(Character::isDigit);
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
    List<Character> chars = new ArrayList<>();
    for (char c : s.toCharArray()) chars.add(c);

    List<Character> shifted = new ArrayList<>();
    for (char c : chars) {
        if (Character.isLetter(c)) {
            char base = Character.isUpperCase(c) ? 'A' : 'a';
            shifted.add((char) ((c - base + k) % 26 + base));
        } else {
            shifted.add(c);
        }
    }

    StringBuilder sb = new StringBuilder();
    for (char c : shifted) sb.append(c);
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
// TreeSet trick: in lexicographic order, the first and last strings
// have the smallest common prefix among all strings.
public static String longestCommonPrefix(String[] strs) {
    if (strs == null || strs.length == 0) return "";

    TreeSet<String> set = new TreeSet<>(Arrays.asList(strs));
    String first = set.first();
    String last  = set.last();

    int i = 0;
    while (i < first.length() && i < last.length()
           && first.charAt(i) == last.charAt(i)) i++;

    return first.substring(0, i);
}
```

> **Key:** In a `TreeSet`, comparing only the lexicographically smallest and largest strings is enough to find the common prefix for all strings.

---

## 39. Check Balanced Parentheses

**Problem:** Check if a string of brackets is balanced.

```
Input:  "(()())"  → true
Input:  "(()"     → false
```

```java
public static boolean isBalanced(String s) {
    Deque<Character> stack = new ArrayDeque<>();
    Map<Character, Character> pairs = new HashMap<>();
    pairs.put(')', '(');
    pairs.put(']', '[');
    pairs.put('}', '{');

    for (char c : s.toCharArray()) {
        if (pairs.containsValue(c)) {
            stack.push(c);                          // opening bracket
        } else if (pairs.containsKey(c)) {
            if (stack.isEmpty() || stack.pop() != pairs.get(c))
                return false;                       // mismatched
        }
    }
    return stack.isEmpty();
}
```

> **Key:** `ArrayDeque` as a Stack is the standard approach for bracket matching.
> This version also handles `[]` and `{}` — not just `()`.

---

## 40. String Compression (Run-Length Encoding)

**Problem:** Compress a string using run-length encoding.

```
Input:  "aaabbc"
Output: "a3b2c1"
```

```java
public static String compress(String s) {
    // Use LinkedHashMap to maintain character order
    Map<Character, Integer> freq = new LinkedHashMap<>();
    char[] chars = s.toCharArray();

    // Process runs (not just total frequency — handle consecutive groups)
    int i = 0;
    List<String> parts = new ArrayList<>();
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
public static List<String> permutations(String s) {
    List<String> result = new ArrayList<>();
    generatePermutations(new ArrayList<>(Arrays.asList(s.split(""))),
                         new ArrayDeque<>(), result);
    return result;
}

private static void generatePermutations(List<String> chars,
                                          Deque<String> current,
                                          List<String> result) {
    if (chars.isEmpty()) {
        result.add(String.join("", current));
        return;
    }
    for (int i = 0; i < chars.size(); i++) {
        String ch = chars.remove(i);
        current.addLast(ch);
        generatePermutations(chars, current, result);
        current.removeLast();
        chars.add(i, ch);
    }
}
```

> **Key:** `Deque` (ArrayDeque) used as the "current path" — add to tail, remove from tail (backtracking).

---

## 42. Check if String is a Subsequence

**Problem:** Check if string `s` is a subsequence of string `t`.

```
Input:  s = "ace", t = "abcde"  → true
```

```java
public static boolean isSubsequence(String s, String t) {
    Deque<Character> queue = new ArrayDeque<>();
    for (char c : s.toCharArray()) queue.offer(c);  // add to tail

    for (char c : t.toCharArray()) {
        if (!queue.isEmpty() && queue.peek() == c)
            queue.poll();                            // matched, remove from head
    }
    return queue.isEmpty();                          // all chars matched
}
```

> **Key:** `ArrayDeque` as a Queue — `offer` to enqueue, `poll` to dequeue (FIFO).

---

## 43. Count Frequency of Each Character

**Problem:** Print the frequency of each character in a string.

```
Input:  "hello"
Output: {h=1, e=1, l=2, o=1}
```

```java
public static Map<Character, Integer> charFrequency(String s) {
    Map<Character, Integer> map = new LinkedHashMap<>();
    for (char c : s.toCharArray())
        map.merge(c, 1, Integer::sum);
    return map;
}
```

**Sorted by frequency (highest first) using TreeMap / PriorityQueue:**
```java
public static List<Map.Entry<Character, Integer>> sortedByFrequency(String s) {
    Map<Character, Integer> map = new LinkedHashMap<>();
    for (char c : s.toCharArray())
        map.merge(c, 1, Integer::sum);

    List<Map.Entry<Character, Integer>> entries = new ArrayList<>(map.entrySet());
    entries.sort((a, b) -> b.getValue() - a.getValue());  // desc frequency
    return entries;
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
    Set<Character> alphabetSeen = new HashSet<>();

    for (char c : s.toLowerCase().toCharArray())
        if (c >= 'a' && c <= 'z') alphabetSeen.add(c);

    // All 26 letters must be present
    return alphabetSeen.size() == 26;
}
```

> **Key:** HashSet naturally tracks unique elements — just check if all 26 letters are collected.

---

## 45. Top K Frequent Elements

**Problem:** Find the K most frequent elements in an array.

```
Input:  [1, 1, 1, 2, 2, 3], k = 2
Output: [1, 2]
```

```java
public static List<Integer> topKFrequent(int[] arr, int k) {
    // Step 1: count frequencies
    Map<Integer, Integer> freq = new HashMap<>();
    for (int n : arr) freq.merge(n, 1, Integer::sum);

    // Step 2: max-heap ordered by frequency (descending)
    PriorityQueue<Map.Entry<Integer, Integer>> maxHeap =
        new PriorityQueue<>((a, b) -> b.getValue() - a.getValue());

    maxHeap.addAll(freq.entrySet());

    // Step 3: poll top k
    List<Integer> result = new ArrayList<>();
    for (int i = 0; i < k; i++) result.add(maxHeap.poll().getKey());
    return result;
}
```

> **Key:** `PriorityQueue` with a custom comparator becomes a max-heap when you reverse the comparison.

---

# Collections Quick Reference

## Which Collection to Use?

| Need | Use |
|------|-----|
| Ordered list, index access | `ArrayList` |
| Frequent insert/delete at ends | `LinkedList` / `ArrayDeque` |
| Stack (LIFO) | `ArrayDeque` (`push`/`pop`) |
| Queue (FIFO) | `ArrayDeque` (`offer`/`poll`) |
| No duplicates, fast lookup | `HashSet` |
| No duplicates, insertion order | `LinkedHashSet` |
| No duplicates, sorted order | `TreeSet` |
| Key-value pairs, fast lookup | `HashMap` |
| Key-value pairs, insertion order | `LinkedHashMap` |
| Key-value pairs, sorted keys | `TreeMap` |
| Priority / min-max heap | `PriorityQueue` |

## Key Collections Utility Methods

```java
Collections.sort(list)                    // sort ascending
Collections.sort(list, Comparator)        // sort custom order
Collections.reverse(list)                 // reverse in-place
Collections.shuffle(list)                 // random shuffle
Collections.max(collection)               // maximum element
Collections.min(collection)               // minimum element
Collections.frequency(collection, obj)    // count occurrences
Collections.rotate(list, distance)        // rotate (+ = right, - = left)
Collections.swap(list, i, j)              // swap two elements
Collections.fill(list, val)               // fill all with value
Collections.disjoint(c1, c2)             // true if no common elements
Collections.unmodifiableList(list)        // read-only view
```

## PriorityQueue Patterns

```java
// Min-heap (default)
PriorityQueue<Integer> minHeap = new PriorityQueue<>();

// Max-heap
PriorityQueue<Integer> maxHeap = new PriorityQueue<>(Collections.reverseOrder());

// Custom comparator (sort by string length)
PriorityQueue<String> pq = new PriorityQueue<>(Comparator.comparingInt(String::length));
```

## Map Patterns

```java
// Count frequency
map.merge(key, 1, Integer::sum);

// Get or default
map.getOrDefault(key, 0);

// Put if absent
map.putIfAbsent(key, new ArrayList<>());

// Iterate entries
for (Map.Entry<K, V> entry : map.entrySet()) { ... }

// Sort map by value
entries.sort(Map.Entry.comparingByValue());
```

## ArrayDeque as Stack vs Queue

```java
Deque<Integer> deque = new ArrayDeque<>();

// Stack (LIFO)
deque.push(1);   // pushes to front
deque.pop();     // removes from front
deque.peek();    // views front

// Queue (FIFO)
deque.offer(1);  // adds to back
deque.poll();    // removes from front
deque.peek();    // views front
```
