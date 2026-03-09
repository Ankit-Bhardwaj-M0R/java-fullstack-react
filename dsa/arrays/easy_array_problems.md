# Easy Array Problems – HackerRank Style

---

## 1. Find the Maximum Element

**Problem:** Find the largest element in an array.

**Example:**
```
Input:  [3, 7, 1, 9, 2]
Output: 9
```

**Java Solution:**
```java
public static int findMax(int[] arr) {
    int max = arr[0];
    for (int num : arr)
        if (num > max) max = num;
    return max;
}
```

---

## 2. Find the Minimum Element

**Problem:** Find the smallest element in an array.

**Example:**
```
Input:  [3, 7, 1, 9, 2]
Output: 1
```

**Java Solution:**
```java
public static int findMin(int[] arr) {
    int min = arr[0];
    for (int num : arr)
        if (num < min) min = num;
    return min;
}
```

---

## 3. Reverse an Array

**Problem:** Reverse the elements of an array in-place.

**Example:**
```
Input:  [1, 2, 3, 4, 5]
Output: [5, 4, 3, 2, 1]
```

**Java Solution:**
```java
public static void reverseArray(int[] arr) {
    int left = 0, right = arr.length - 1;
    while (left < right) {
        int temp = arr[left];
        arr[left++] = arr[right];
        arr[right--] = temp;
    }
}
```

---

## 4. Sum of Array Elements

**Problem:** Return the sum of all elements in an array.

**Example:**
```
Input:  [1, 2, 3, 4, 5]
Output: 15
```

**Java Solution:**
```java
public static int arraySum(int[] arr) {
    int sum = 0;
    for (int num : arr) sum += num;
    return sum;
}
```

---

## 5. Check if Array is Sorted (Ascending)

**Problem:** Return true if the array is sorted in ascending order.

**Example:**
```
Input:  [1, 2, 3, 4, 5]
Output: true

Input:  [1, 3, 2, 4]
Output: false
```

**Java Solution:**
```java
public static boolean isSorted(int[] arr) {
    for (int i = 1; i < arr.length; i++)
        if (arr[i] < arr[i - 1]) return false;
    return true;
}
```

---

## 6. Find Second Largest Element

**Problem:** Find the second largest distinct element in an array.

**Example:**
```
Input:  [10, 5, 8, 20, 15]
Output: 15
```

**Java Solution:**
```java
public static int secondLargest(int[] arr) {
    int first = Integer.MIN_VALUE, second = Integer.MIN_VALUE;
    for (int num : arr) {
        if (num > first) { second = first; first = num; }
        else if (num > second && num != first) second = num;
    }
    return second;
}
```

---

## 7. Count Even and Odd Numbers

**Problem:** Count how many even and odd numbers exist in an array.

**Example:**
```
Input:  [1, 2, 3, 4, 5]
Output: Even: 2, Odd: 3
```

**Java Solution:**
```java
public static void countEvenOdd(int[] arr) {
    int even = 0, odd = 0;
    for (int num : arr) {
        if (num % 2 == 0) even++;
        else odd++;
    }
    System.out.println("Even: " + even + ", Odd: " + odd);
}
```

---

## 8. Move Zeros to the End

**Problem:** Move all zeros in an array to the end while maintaining the order of non-zero elements.

**Example:**
```
Input:  [0, 1, 0, 3, 12]
Output: [1, 3, 12, 0, 0]
```

**Java Solution:**
```java
public static void moveZeros(int[] arr) {
    int pos = 0;
    for (int num : arr)
        if (num != 0) arr[pos++] = num;
    while (pos < arr.length)
        arr[pos++] = 0;
}
```

---

## 9. Find Duplicates in an Array

**Problem:** Find all duplicate elements in an array.

**Example:**
```
Input:  [1, 2, 3, 2, 4, 1]
Output: [2, 1]
```

**Java Solution:**
```java
public static List<Integer> findDuplicates(int[] arr) {
    Set<Integer> seen = new HashSet<>();
    List<Integer> duplicates = new ArrayList<>();
    for (int num : arr) {
        if (!seen.add(num)) duplicates.add(num);
    }
    return duplicates;
}
```

---

## 10. Two Sum (Find Pair with Target Sum)

**Problem:** Find two indices such that their values add up to the target.

**Example:**
```
Input:  arr = [2, 7, 11, 15], target = 9
Output: [0, 1]  (arr[0] + arr[1] = 9)
```

**Java Solution:**
```java
public static int[] twoSum(int[] arr, int target) {
    Map<Integer, Integer> map = new HashMap<>();
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

**Problem:** Remove duplicates from a sorted array in-place and return the new length.

**Example:**
```
Input:  [1, 1, 2, 3, 3, 4]
Output: 4  (array becomes [1, 2, 3, 4, ...])
```

**Java Solution:**
```java
public static int removeDuplicates(int[] arr) {
    if (arr.length == 0) return 0;
    int pos = 1;
    for (int i = 1; i < arr.length; i++)
        if (arr[i] != arr[i - 1]) arr[pos++] = arr[i];
    return pos;
}
```

---

## 12. Rotate Array by K Positions

**Problem:** Rotate an array to the right by k steps.

**Example:**
```
Input:  [1, 2, 3, 4, 5], k = 2
Output: [4, 5, 1, 2, 3]
```

**Java Solution:**
```java
public static void rotate(int[] arr, int k) {
    k = k % arr.length;
    reverse(arr, 0, arr.length - 1);
    reverse(arr, 0, k - 1);
    reverse(arr, k, arr.length - 1);
}

private static void reverse(int[] arr, int left, int right) {
    while (left < right) {
        int temp = arr[left];
        arr[left++] = arr[right];
        arr[right--] = temp;
    }
}
```

---

## 13. Find the Missing Number (1 to N)

**Problem:** Given an array of n-1 integers in range [1, n], find the missing number.

**Example:**
```
Input:  [1, 2, 4, 5, 6]  (n = 6)
Output: 3
```

**Java Solution:**
```java
public static int missingNumber(int[] arr, int n) {
    int expectedSum = n * (n + 1) / 2;
    int actualSum = 0;
    for (int num : arr) actualSum += num;
    return expectedSum - actualSum;
}
```

---

## 14. Maximum Subarray Sum (Kadane's Algorithm)

**Problem:** Find the contiguous subarray with the largest sum.

**Example:**
```
Input:  [-2, 1, -3, 4, -1, 2, 1, -5, 4]
Output: 6  (subarray [4, -1, 2, 1])
```

**Java Solution:**
```java
public static int maxSubarraySum(int[] arr) {
    int maxSum = arr[0], currentSum = arr[0];
    for (int i = 1; i < arr.length; i++) {
        currentSum = Math.max(arr[i], currentSum + arr[i]);
        maxSum = Math.max(maxSum, currentSum);
    }
    return maxSum;
}
```

---

## 15. Find Intersection of Two Arrays

**Problem:** Return elements that appear in both arrays.

**Example:**
```
Input:  [1, 2, 3, 4], [2, 4, 6]
Output: [2, 4]
```

**Java Solution:**
```java
public static List<Integer> intersection(int[] a, int[] b) {
    Set<Integer> setA = new HashSet<>();
    for (int num : a) setA.add(num);
    List<Integer> result = new ArrayList<>();
    for (int num : b)
        if (setA.contains(num)) result.add(num);
    return result;
}
```

---

## 16. Find Union of Two Arrays

**Problem:** Return all distinct elements from both arrays.

**Example:**
```
Input:  [1, 2, 3], [2, 3, 4, 5]
Output: [1, 2, 3, 4, 5]
```

**Java Solution:**
```java
public static List<Integer> union(int[] a, int[] b) {
    Set<Integer> set = new LinkedHashSet<>();
    for (int num : a) set.add(num);
    for (int num : b) set.add(num);
    return new ArrayList<>(set);
}
```

---

## 17. Count Elements Less Than X

**Problem:** Count how many elements in the array are less than a given value X.

**Example:**
```
Input:  [1, 5, 3, 7, 2], X = 4
Output: 3  (1, 3, 2 are less than 4)
```

**Java Solution:**
```java
public static int countLessThan(int[] arr, int x) {
    int count = 0;
    for (int num : arr)
        if (num < x) count++;
    return count;
}
```

---

## 18. Array Left Rotation

**Problem:** Perform d left rotations on an array.

**Example:**
```
Input:  [1, 2, 3, 4, 5], d = 2
Output: [3, 4, 5, 1, 2]
```

**Java Solution:**
```java
public static int[] leftRotation(int[] arr, int d) {
    int n = arr.length;
    d = d % n;
    int[] result = new int[n];
    for (int i = 0; i < n; i++)
        result[i] = arr[(i + d) % n];
    return result;
}
```

---

## 19. Check if Array Contains a Value

**Problem:** Check if a specific value exists in an array.

**Example:**
```
Input:  [4, 2, 7, 1], target = 7
Output: true
```

**Java Solution:**
```java
public static boolean contains(int[] arr, int target) {
    for (int num : arr)
        if (num == target) return true;
    return false;
}
```

---

## 20. Frequency of Each Element

**Problem:** Print the frequency of every element in the array.

**Example:**
```
Input:  [1, 2, 2, 3, 1, 4]
Output: {1=2, 2=2, 3=1, 4=1}
```

**Java Solution:**
```java
public static Map<Integer, Integer> frequency(int[] arr) {
    Map<Integer, Integer> map = new LinkedHashMap<>();
    for (int num : arr)
        map.put(num, map.getOrDefault(num, 0) + 1);
    return map;
}
```

---

## 21. Merge Two Sorted Arrays

**Problem:** Merge two sorted arrays into one sorted array.

**Example:**
```
Input:  [1, 3, 5], [2, 4, 6]
Output: [1, 2, 3, 4, 5, 6]
```

**Java Solution:**
```java
public static int[] mergeSorted(int[] a, int[] b) {
    int[] result = new int[a.length + b.length];
    int i = 0, j = 0, k = 0;
    while (i < a.length && j < b.length)
        result[k++] = a[i] <= b[j] ? a[i++] : b[j++];
    while (i < a.length) result[k++] = a[i++];
    while (j < b.length) result[k++] = b[j++];
    return result;
}
```

---

## 22. Find Leaders in an Array

**Problem:** An element is a leader if it is greater than all elements to its right. Find all leaders.

**Example:**
```
Input:  [16, 17, 4, 3, 5, 2]
Output: [17, 5, 2]
```

**Java Solution:**
```java
public static List<Integer> findLeaders(int[] arr) {
    List<Integer> leaders = new ArrayList<>();
    int maxFromRight = arr[arr.length - 1];
    leaders.add(maxFromRight);
    for (int i = arr.length - 2; i >= 0; i--) {
        if (arr[i] > maxFromRight) {
            maxFromRight = arr[i];
            leaders.add(0, arr[i]);
        }
    }
    return leaders;
}
```

---

## 23. Find the Majority Element (Appears > n/2 times)

**Problem:** Find the element that appears more than n/2 times. (Boyer-Moore Voting)

**Example:**
```
Input:  [3, 3, 4, 2, 3, 3, 3]
Output: 3
```

**Java Solution:**
```java
public static int majorityElement(int[] arr) {
    int candidate = arr[0], count = 1;
    for (int i = 1; i < arr.length; i++) {
        if (arr[i] == candidate) count++;
        else if (--count == 0) { candidate = arr[i]; count = 1; }
    }
    return candidate;
}
```

---

## 24. Print Pairs with Given Sum

**Problem:** Print all pairs in the array that sum to a target value.

**Example:**
```
Input:  [1, 5, 3, 7, 4, 2], target = 6
Output: (1,5), (3,3)? -> (3, 3 not valid), actually: (1,5), (4,2)
```

**Java Solution:**
```java
public static void printPairs(int[] arr, int target) {
    Set<Integer> seen = new HashSet<>();
    for (int num : arr) {
        int complement = target - num;
        if (seen.contains(complement))
            System.out.println("(" + complement + ", " + num + ")");
        seen.add(num);
    }
}
```

---

## 25. Cumulative Sum Array (Prefix Sum)

**Problem:** Given an array, build a prefix sum array where each element is the sum of all previous elements including itself.

**Example:**
```
Input:  [1, 2, 3, 4, 5]
Output: [1, 3, 6, 10, 15]
```

**Java Solution:**
```java
public static int[] prefixSum(int[] arr) {
    int[] prefix = new int[arr.length];
    prefix[0] = arr[0];
    for (int i = 1; i < arr.length; i++)
        prefix[i] = prefix[i - 1] + arr[i];
    return prefix;
}
```

---

## Tips for HackerRank Array Problems

| Problem Type | Technique |
|-------------|-----------|
| Max/Min | Single pass, track variable |
| Sorting needed? | Use `Arrays.sort()` |
| Pairs/Two sum | HashMap for O(n) lookup |
| Subarrays | Sliding window or prefix sum |
| Duplicates | HashSet or frequency array |
| Rotation | Reverse trick or modulo indexing |
| Missing number | Expected sum formula `n*(n+1)/2` |
| Majority element | Boyer-Moore voting algorithm |
| Merging | Two-pointer on sorted arrays |

---

## Common Imports to Remember

```java
import java.util.*;
// Arrays, ArrayList, HashMap, HashSet, LinkedHashMap, LinkedHashSet
```

## Quick Utility Snippets

```java
// Sort array
Arrays.sort(arr);

// Copy array
int[] copy = Arrays.copyOf(arr, arr.length);

// Fill array with value
Arrays.fill(arr, 0);

// Convert array to list
List<Integer> list = Arrays.asList(boxedArr);

// Max/Min using streams
int max = Arrays.stream(arr).max().getAsInt();
int min = Arrays.stream(arr).min().getAsInt();
int sum = Arrays.stream(arr).sum();
```
