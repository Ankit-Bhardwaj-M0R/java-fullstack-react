# Collection Framework - Complete Interview Questions Guide

Let me give you every possible Collection-related interview question with conversational answers in simple English with examples.

## **CORE CONCEPTS**

### 1. What is the Collection Framework in Java?

Think of the Collection Framework as a toolbox that Java gives you to store and manage groups of objects. Instead of creating your own ways to store data every time, Java provides ready-made classes like ArrayList, HashSet, HashMap etc. 

**Example:** Instead of manually managing arrays and writing code to add/remove elements, you just use `ArrayList<String> names = new ArrayList<>();` and call `names.add("John")`.

### 2. Why do we need the Collection Framework?

Before the Collection Framework, developers used arrays or created their own data structures. Arrays have fixed size and limited functionality. 

**Example:** With array: `String[] names = new String[10];` - size is fixed. With ArrayList: `ArrayList<String> names = new ArrayList<>();` - size grows automatically. You can easily do `names.remove("John")`, `names.contains("Mary")` etc.

### 3. What is the hierarchy of Collection Framework?

At the top, there's an `Iterable` interface, then `Collection` interface extends it. Under Collection, you have three main branches: `List`, `Set`, and `Queue`. Separately, there's `Map` interface.

**Example structure:**
```
Iterable
  └── Collection
       ├── List (ArrayList, LinkedList, Vector)
       ├── Set (HashSet, TreeSet, LinkedHashSet)
       └── Queue (PriorityQueue, LinkedList)
  
Map (HashMap, TreeMap, LinkedHashMap)
```

### 4. What's the difference between Collection and Collections?

`Collection` is an interface - it's like a blueprint. `Collections` is a utility class with helper methods.

**Example:**
```java
// Collection - interface
Collection<String> list = new ArrayList<>();

// Collections - utility class with static methods
Collections.sort(list);
Collections.reverse(list);
Collections.shuffle(list);
```

### 5. Why is Map not part of Collection interface?

Map stores key-value pairs, while Collection stores single elements. They have fundamentally different structures.

**Example:**
```java
// Collection stores single values
List<String> names = new ArrayList<>();
names.add("John");  // just one value

// Map stores key-value pairs
Map<Integer, String> students = new HashMap<>();
students.put(101, "John");  // key and value both needed
```

### 6. What is the difference between Collection API and Stream API?

Collection API is for storing and managing data. Stream API is for processing data (filtering, mapping, reducing).

**Example:**
```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

// Collection API - storing data
numbers.add(6);
numbers.remove(0);

// Stream API - processing data
long count = numbers.stream()
                    .filter(n -> n > 2)
                    .count();  // Result: 3
```

### 7. What are the main interfaces in Collection Framework?

The main interfaces are: `Collection`, `List`, `Set`, `Queue`, `Deque`, and `Map`.

**Example usage:**
```java
List<String> list = new ArrayList<>();        // ordered, allows duplicates
Set<String> set = new HashSet<>();            // no duplicates
Queue<String> queue = new LinkedList<>();     // FIFO processing
Map<String, Integer> map = new HashMap<>();   // key-value pairs
```

---

## **LIST INTERFACE**

### 8. What is List interface and its characteristics?

List is an ordered collection that allows duplicate elements. You can access elements by index position.

**Example:**
```java
List<String> fruits = new ArrayList<>();
fruits.add("Apple");
fruits.add("Banana");
fruits.add("Apple");  // duplicate allowed
System.out.println(fruits.get(0));  // Access by index: Apple
// Output: [Apple, Banana, Apple]
```

### 9. Difference between ArrayList and LinkedList?

ArrayList uses array internally - fast for reading by index. LinkedList uses nodes with pointers - fast for insertions/deletions.

**Example:**
```java
// ArrayList - fast random access
List<String> arrayList = new ArrayList<>();
arrayList.add("A");
arrayList.add("B");
System.out.println(arrayList.get(1));  // Very fast: B

// LinkedList - fast insertion/deletion
List<String> linkedList = new LinkedList<>();
linkedList.add("A");
linkedList.add(0, "Z");  // Insert at beginning - fast
// Output: [Z, A]
```

### 10. When would you use LinkedList over ArrayList?

Use LinkedList when you're doing lots of insertions and deletions in the middle or at the beginning.

**Example - Music Playlist:**
```java
LinkedList<String> playlist = new LinkedList<>();
playlist.add("Song1");
playlist.add("Song2");
playlist.add("Song3");

// User wants to insert song at position 1
playlist.add(1, "NewSong");  // Fast in LinkedList
// Output: [Song1, NewSong, Song2, Song3]

// User removes song from beginning
playlist.removeFirst();  // Fast in LinkedList
```

### 11. What is Vector and how is it different from ArrayList?

Vector is like ArrayList but thread-safe - all methods are synchronized. It's slower and legacy code.

**Example:**
```java
// Vector - synchronized (thread-safe but slow)
Vector<String> vector = new Vector<>();
vector.add("Item1");

// ArrayList - not synchronized (fast but not thread-safe)
ArrayList<String> list = new ArrayList<>();
list.add("Item1");

// Modern way for thread-safety
List<String> syncList = Collections.synchronizedList(new ArrayList<>());
```

### 12. Difference between ArrayList and Array?

Array has fixed size, ArrayList grows dynamically. Arrays can hold primitives, ArrayList needs wrapper classes.

**Example:**
```java
// Array - fixed size
String[] names = new String[3];
names[0] = "John";
names[1] = "Mary";
names[2] = "Bob";
// names[3] = "Alice"; // Error! Size is fixed

// ArrayList - dynamic size
ArrayList<String> nameList = new ArrayList<>();
nameList.add("John");
nameList.add("Mary");
nameList.add("Bob");
nameList.add("Alice");  // No problem! Size grows automatically
// Output: [John, Mary, Bob, Alice]
```

### 13. How does ArrayList grow its size?

When ArrayList is full, it creates a new array with 1.5 times capacity, copies old elements, then adds new element.

**Example:**
```java
ArrayList<Integer> numbers = new ArrayList<>(2);  // Initial capacity: 2
numbers.add(1);  // [1]
numbers.add(2);  // [1, 2] - full now
numbers.add(3);  // Creates new array of size 3 (2 * 1.5), copies old elements
                 // [1, 2, 3]
```

### 14. What is the initial capacity of ArrayList?

Default initial capacity is 10. You can specify custom capacity to avoid frequent resizing.

**Example:**
```java
// Default capacity: 10
ArrayList<String> list1 = new ArrayList<>();

// Custom capacity: 100 (if you know you'll add 100 items)
ArrayList<String> list2 = new ArrayList<>(100);
// Avoids multiple resize operations, better performance
```

### 15. Can we add null elements in List?

Yes, List allows null elements. You can even add multiple nulls.

**Example:**
```java
List<String> items = new ArrayList<>();
items.add("Apple");
items.add(null);
items.add("Banana");
items.add(null);
System.out.println(items);
// Output: [Apple, null, Banana, null]
```

### 16. How to make ArrayList read-only?

Use `Collections.unmodifiableList()` to create a read-only wrapper.

**Example:**
```java
ArrayList<String> list = new ArrayList<>();
list.add("Item1");
list.add("Item2");

// Create read-only version
List<String> readOnlyList = Collections.unmodifiableList(list);

// Try to modify
readOnlyList.add("Item3");  // Throws UnsupportedOperationException

// Original list can still be modified
list.add("Item3");  // Works fine
```

### 17. What is CopyOnWriteArrayList?

Thread-safe variant where every modification creates a copy of the array. Reads are very fast and don't need locking.

**Example - Event Listeners:**
```java
CopyOnWriteArrayList<String> listeners = new CopyOnWriteArrayList<>();

// Thread 1: Adding listeners (creates copy)
listeners.add("Listener1");
listeners.add("Listener2");

// Thread 2: Reading listeners (no locking needed, very fast)
for(String listener : listeners) {
    System.out.println(listener);
}

// Best for scenarios with many reads, few writes
```

### 18. How to convert Array to ArrayList?

Use `Arrays.asList()` or create new ArrayList with array elements.

**Example:**
```java
// Method 1: Arrays.asList() - fixed size
String[] array = {"A", "B", "C"};
List<String> list1 = Arrays.asList(array);
// list1.add("D"); // Error! Fixed size

// Method 2: New ArrayList - dynamic size
List<String> list2 = new ArrayList<>(Arrays.asList(array));
list2.add("D");  // Works fine
System.out.println(list2);  // Output: [A, B, C, D]
```

### 19. How to convert ArrayList to Array?

Use `toArray()` method.

**Example:**
```java
ArrayList<String> list = new ArrayList<>();
list.add("Apple");
list.add("Banana");
list.add("Cherry");

// Method 1: Object array
Object[] arr1 = list.toArray();

// Method 2: String array (preferred)
String[] arr2 = list.toArray(new String[0]);
System.out.println(Arrays.toString(arr2));
// Output: [Apple, Banana, Cherry]
```

### 20. How to sort an ArrayList?

Use `Collections.sort()` or `list.sort()`.

**Example:**
```java
ArrayList<Integer> numbers = new ArrayList<>();
numbers.add(5);
numbers.add(2);
numbers.add(8);
numbers.add(1);

// Method 1: Collections.sort()
Collections.sort(numbers);
System.out.println(numbers);  // Output: [1, 2, 5, 8]

// Method 2: list.sort() with lambda
ArrayList<String> names = new ArrayList<>();
names.add("John");
names.add("Alice");
names.add("Bob");
names.sort((a, b) -> a.compareTo(b));
System.out.println(names);  // Output: [Alice, Bob, John]
```

### 21. How to reverse an ArrayList?

Use `Collections.reverse()`.

**Example:**
```java
ArrayList<String> colors = new ArrayList<>();
colors.add("Red");
colors.add("Green");
colors.add("Blue");
System.out.println(colors);  // Output: [Red, Green, Blue]

Collections.reverse(colors);
System.out.println(colors);  // Output: [Blue, Green, Red]
```

### 22. How to remove duplicates from ArrayList?

Convert to Set (which doesn't allow duplicates), then back to List.

**Example:**
```java
ArrayList<Integer> numbers = new ArrayList<>();
numbers.add(1);
numbers.add(2);
numbers.add(2);
numbers.add(3);
numbers.add(1);
System.out.println(numbers);  // Output: [1, 2, 2, 3, 1]

// Remove duplicates
Set<Integer> set = new LinkedHashSet<>(numbers);  // LinkedHashSet maintains order
ArrayList<Integer> uniqueList = new ArrayList<>(set);
System.out.println(uniqueList);  // Output: [1, 2, 3]
```

### 23. Difference between remove(int index) and remove(Object o)?

`remove(int index)` removes element at that position. `remove(Object o)` removes first occurrence of that object.

**Example:**
```java
ArrayList<Integer> numbers = new ArrayList<>();
numbers.add(10);
numbers.add(20);
numbers.add(30);
numbers.add(20);

// Remove by index
numbers.remove(1);  // Removes element at index 1 (20)
System.out.println(numbers);  // Output: [10, 30, 20]

// Remove by object
numbers.remove(Integer.valueOf(20));  // Removes first occurrence of 20
System.out.println(numbers);  // Output: [10, 30]
```

### 24. What is the difference between size() and capacity() in ArrayList?

`size()` is number of elements currently in list. `capacity()` is the size of internal array.

**Example:**
```java
ArrayList<String> list = new ArrayList<>(20);  // Capacity: 20
list.add("A");
list.add("B");
list.add("C");

System.out.println("Size: " + list.size());  // Output: 3 (actual elements)
// Capacity is 20 (internal array size) but not directly accessible
```

### 25. Can we have ArrayList of ArrayList?

Yes, you can create nested ArrayList (2D list).

**Example - Student Marks:**
```java
ArrayList<ArrayList<Integer>> studentMarks = new ArrayList<>();

// Student 1 marks
ArrayList<Integer> student1 = new ArrayList<>();
student1.add(85);
student1.add(90);
student1.add(78);

// Student 2 marks
ArrayList<Integer> student2 = new ArrayList<>();
student2.add(92);
student2.add(88);
student2.add(95);

studentMarks.add(student1);
studentMarks.add(student2);

System.out.println(studentMarks);
// Output: [[85, 90, 78], [92, 88, 95]]
```

---

## **SET INTERFACE**

### 26. What is Set interface and its characteristics?

Set doesn't allow duplicate elements. It's like a mathematical set.

**Example:**
```java
Set<String> fruits = new HashSet<>();
fruits.add("Apple");
fruits.add("Banana");
fruits.add("Apple");  // Duplicate - won't be added
System.out.println(fruits);  
// Output: [Apple, Banana] - no duplicates
```

### 27. Difference between HashSet, LinkedHashSet, and TreeSet?

HashSet is random order (fastest). LinkedHashSet maintains insertion order. TreeSet maintains sorted order (slowest).

**Example:**
```java
// HashSet - random order
Set<Integer> hashSet = new HashSet<>();
hashSet.add(3);
hashSet.add(1);
hashSet.add(2);
System.out.println(hashSet);  // Output: [1, 2, 3] or any order

// LinkedHashSet - insertion order
Set<Integer> linkedSet = new LinkedHashSet<>();
linkedSet.add(3);
linkedSet.add(1);
linkedSet.add(2);
System.out.println(linkedSet);  // Output: [3, 1, 2]

// TreeSet - sorted order
Set<Integer> treeSet = new TreeSet<>();
treeSet.add(3);
treeSet.add(1);
treeSet.add(2);
System.out.println(treeSet);  // Output: [1, 2, 3]
```

### 28. How does HashSet check for duplicates?

HashSet uses `hashCode()` first to find bucket, then `equals()` to check actual equality.

**Example:**
```java
class Student {
    int id;
    String name;
    
    Student(int id, String name) {
        this.id = id;
        this.name = name;
    }
    
    @Override
    public int hashCode() {
        return id;  // Same ID = same bucket
    }
    
    @Override
    public boolean equals(Object obj) {
        Student other = (Student) obj;
        return this.id == other.id;  // Check actual equality
    }
}

Set<Student> students = new HashSet<>();
students.add(new Student(1, "John"));
students.add(new Student(1, "John"));  // Duplicate - not added
System.out.println(students.size());  // Output: 1
```

### 29. Can HashSet contain null?

Yes, HashSet allows one null element.

**Example:**
```java
Set<String> set = new HashSet<>();
set.add("Apple");
set.add(null);
set.add("Banana");
set.add(null);  // Only one null allowed
System.out.println(set);  
// Output: [null, Apple, Banana]
```

### 30. Can TreeSet contain null?

No, TreeSet doesn't allow null because it needs to compare elements for sorting.

**Example:**
```java
Set<String> treeSet = new TreeSet<>();
treeSet.add("Apple");
treeSet.add(null);  // Throws NullPointerException
// TreeSet tries to compare null with "Apple" for sorting, which fails
```

### 31. What happens if two objects have same hashCode but different equals?

They'll be stored in same bucket but as different elements (hash collision).

**Example:**
```java
class Book {
    String title;
    
    Book(String title) {
        this.title = title;
    }
    
    @Override
    public int hashCode() {
        return 1;  // All books have same hashCode
    }
    
    @Override
    public boolean equals(Object obj) {
        Book other = (Book) obj;
        return this.title.equals(other.title);
    }
}

Set<Book> books = new HashSet<>();
books.add(new Book("Java"));
books.add(new Book("Python"));
// Both have hashCode=1, but equals is different
System.out.println(books.size());  // Output: 2 (both stored)
```

### 32. When would you use TreeSet?

Use TreeSet when you need automatic sorting.

**Example - Leaderboard:**
```java
// Players with scores
TreeSet<Integer> scores = new TreeSet<>();
scores.add(150);
scores.add(200);
scores.add(100);
scores.add(175);

System.out.println("Leaderboard (ascending):");
for(Integer score : scores) {
    System.out.println(score);
}
// Output: 100, 150, 175, 200

// For descending order
TreeSet<Integer> descendingScores = new TreeSet<>(Collections.reverseOrder());
descendingScores.addAll(scores);
System.out.println(descendingScores);  // Output: [200, 175, 150, 100]
```

### 33. How to sort elements in HashSet?

Convert HashSet to TreeSet or to List and sort.

**Example:**
```java
HashSet<String> names = new HashSet<>();
names.add("John");
names.add("Alice");
names.add("Bob");
names.add("Diana");

// Method 1: Convert to TreeSet
TreeSet<String> sortedSet = new TreeSet<>(names);
System.out.println(sortedSet);  // Output: [Alice, Bob, Diana, John]

// Method 2: Convert to List and sort
List<String> list = new ArrayList<>(names);
Collections.sort(list);
System.out.println(list);  // Output: [Alice, Bob, Diana, John]
```

### 34. What is LinkedHashSet used for?

Use it when you need uniqueness but also want to maintain insertion order.

**Example - Removing duplicates while maintaining order:**
```java
List<String> items = Arrays.asList("Apple", "Banana", "Apple", "Cherry", "Banana");
System.out.println("Original: " + items);
// Output: [Apple, Banana, Apple, Cherry, Banana]

LinkedHashSet<String> uniqueItems = new LinkedHashSet<>(items);
System.out.println("After removing duplicates: " + uniqueItems);
// Output: [Apple, Banana, Cherry] - order maintained
```

### 35. How to iterate over a Set?

Use enhanced for loop, iterator, or forEach.

**Example:**
```java
Set<String> fruits = new HashSet<>();
fruits.add("Apple");
fruits.add("Banana");
fruits.add("Cherry");

// Method 1: Enhanced for loop
for(String fruit : fruits) {
    System.out.println(fruit);
}

// Method 2: Iterator
Iterator<String> iterator = fruits.iterator();
while(iterator.hasNext()) {
    System.out.println(iterator.next());
}

// Method 3: forEach (Java 8)
fruits.forEach(fruit -> System.out.println(fruit));

// Method 4: Stream
fruits.stream().forEach(System.out::println);
```

### 36. Can we access Set elements by index?

No, Set doesn't have index-based access because it doesn't maintain order (except LinkedHashSet maintains insertion order).

**Example:**
```java
Set<String> set = new HashSet<>();
set.add("Apple");
set.add("Banana");

// set.get(0);  // Error! No such method

// To access by index, convert to List
List<String> list = new ArrayList<>(set);
System.out.println(list.get(0));  // Now works
```

### 37. Difference between HashSet and HashMap?

HashSet stores single values, implements Set interface. HashMap stores key-value pairs, implements Map interface.

**Example:**
```java
// HashSet - stores values
HashSet<String> set = new HashSet<>();
set.add("Apple");
set.add("Banana");

// HashMap - stores key-value pairs
HashMap<Integer, String> map = new HashMap<>();
map.put(1, "Apple");
map.put(2, "Banana");

System.out.println(set);   // Output: [Apple, Banana]
System.out.println(map);   // Output: {1=Apple, 2=Banana}
```

---

## **QUEUE INTERFACE**

### 38. What is Queue interface?

Queue is designed for holding elements before processing - FIFO (First In First Out) by default.

**Example - Print Queue:**
```java
Queue<String> printQueue = new LinkedList<>();
printQueue.offer("Document1.pdf");
printQueue.offer("Document2.pdf");
printQueue.offer("Document3.pdf");

// Process documents in order
while(!printQueue.isEmpty()) {
    String doc = printQueue.poll();
    System.out.println("Printing: " + doc);
}
// Output: Printing: Document1.pdf, Document2.pdf, Document3.pdf
```

### 39. Difference between Queue and Deque?

Queue allows insertion at rear and removal from front (single-ended). Deque allows operations at both ends.

**Example:**
```java
// Queue - single-ended
Queue<String> queue = new LinkedList<>();
queue.offer("First");
queue.offer("Second");
String removed = queue.poll();  // Removes from front only
System.out.println(removed);  // Output: First

// Deque - double-ended
Deque<String> deque = new LinkedList<>();
deque.offerFirst("Middle");
deque.offerFirst("First");   // Add at front
deque.offerLast("Last");      // Add at rear
System.out.println(deque);    // Output: [First, Middle, Last]
deque.pollLast();             // Remove from rear
```

### 40. Difference between offer() and add() in Queue?

`offer()` returns false if queue is full (bounded). `add()` throws exception. Similarly, `poll()` returns null, `remove()` throws exception.

**Example:**
```java
Queue<String> queue = new LinkedList<>();

// add() - throws exception if fails
queue.add("Item1");

// offer() - returns false if fails (safer)
boolean added = queue.offer("Item2");
System.out.println("Added: " + added);  // Output: true

// poll() - returns null if empty
String item = queue.poll();
System.out.println(item);  // Output: Item1

queue.poll();  // Remove Item2
String empty = queue.poll();  // Queue is empty
System.out.println(empty);  // Output: null (no exception)

// remove() - throws exception if empty
// queue.remove();  // Would throw NoSuchElementException
```

### 41. What is PriorityQueue?

PriorityQueue doesn't follow FIFO - elements come out based on priority (natural ordering or Comparator).

**Example - Task Scheduler:**
```java
class Task implements Comparable<Task> {
    String name;
    int priority;  // 1=highest, 5=lowest
    
    Task(String name, int priority) {
        this.name = name;
        this.priority = priority;
    }
    
    @Override
    public int compareTo(Task other) {
        return this.priority - other.priority;  // Lower number = higher priority
    }
    
    @Override
    public String toString() {
        return name + "(Priority:" + priority + ")";
    }
}

PriorityQueue<Task> tasks = new PriorityQueue<>();
tasks.offer(new Task("Email", 3));
tasks.offer(new Task("Bug Fix", 1));      // Highest priority
tasks.offer(new Task("Meeting", 2));
tasks.offer(new Task("Documentation", 4));

while(!tasks.isEmpty()) {
    System.out.println("Processing: " + tasks.poll());
}
// Output:
// Processing: Bug Fix(Priority:1)
// Processing: Meeting(Priority:2)
// Processing: Email(Priority:3)
// Processing: Documentation(Priority:4)
```

### 42. Can PriorityQueue contain null?

No, null is not allowed because PriorityQueue needs to compare elements.

**Example:**
```java
PriorityQueue<String> queue = new PriorityQueue<>();
queue.offer("Apple");
queue.offer(null);  // Throws NullPointerException
// Can't compare null with "Apple"
```

### 43. What is BlockingQueue?

Thread-safe queue that blocks when trying to take from empty queue or put into full queue.

**Example - Producer-Consumer:**
```java
BlockingQueue<String> queue = new ArrayBlockingQueue<>(5);

// Producer thread
Thread producer = new Thread(() -> {
    try {
        queue.put("Item1");
        System.out.println("Produced: Item1");
        queue.put("Item2");
        System.out.println("Produced: Item2");
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
});

// Consumer thread
Thread consumer = new Thread(() -> {
    try {
        Thread.sleep(2000);  // Wait 2 seconds
        String item = queue.take();  // Blocks if queue is empty
        System.out.println("Consumed: " + item);
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
});

producer.start();
consumer.start();
```

### 44. Difference between poll() and peek() in Queue?

`poll()` removes and returns the element. `peek()` just returns without removing.

**Example:**
```java
Queue<String> queue = new LinkedList<>();
queue.offer("First");
queue.offer("Second");
queue.offer("Third");

String peeked = queue.peek();
System.out.println("Peeked: " + peeked);      // Output: First
System.out.println("Queue: " + queue);         // Output: [First, Second, Third]

String polled = queue.poll();
System.out.println("Polled: " + polled);      // Output: First
System.out.println("Queue: " + queue);         // Output: [Second, Third]
```

### 45. How to use LinkedList as Stack and Queue?

LinkedList implements Deque, so it can be used as both Stack (LIFO) and Queue (FIFO).

**Example:**
```java
// As Queue (FIFO)
Queue<String> queue = new LinkedList<>();
queue.offer("First");
queue.offer("Second");
System.out.println(queue.poll());  // Output: First (FIFO)

// As Stack (LIFO)
Deque<String> stack = new LinkedList<>();
stack.push("First");
stack.push("Second");
System.out.println(stack.pop());   // Output: Second (LIFO)
```

---

## **MAP INTERFACE**

### 46. What is Map interface?

Map stores key-value pairs. Each key is unique and maps to exactly one value.

**Example - Student Records:**
```java
Map<Integer, String> students = new HashMap<>();
students.put(101, "John");
students.put(102, "Alice");
students.put(103, "Bob");

System.out.println("Student 102: " + students.get(102));
// Output: Student 102: Alice
```

### 47. Difference between HashMap, LinkedHashMap, and TreeMap?

HashMap is random order (fastest). LinkedHashMap maintains insertion order. TreeMap maintains sorted order by keys.

**Example:**
```java
// HashMap - random order
Map<Integer, String> hashMap = new HashMap<>();
hashMap.put(3, "Three");
hashMap.put(1, "One");
hashMap.put(2, "Two");
System.out.println(hashMap);  
// Output: {1=One, 2=Two, 3=Three} or any order

// LinkedHashMap - insertion order
Map<Integer, String> linkedMap = new LinkedHashMap<>();
linkedMap.put(3, "Three");
linkedMap.put(1, "One");
linkedMap.put(2, "Two");
System.out.println(linkedMap);  
// Output: {3=Three, 1=One, 2=Two}

// TreeMap - sorted by key
Map<Integer, String> treeMap = new TreeMap<>();
treeMap.put(3, "Three");
treeMap.put(1, "One");
treeMap.put(2, "Two");
System.out.println(treeMap);  
// Output: {1=One, 2=Two, 3=Three}
```

### 48. How does HashMap work internally?

HashMap uses array of buckets. It calculates hashCode of key to find bucket, then stores key-value pair there. If bucket has multiple entries (collision), it uses LinkedList or TreeNode.

**Example:**
```java
Map<String, Integer> map = new HashMap<>();

// Step 1: Calculate hashCode of "Apple"
// Step 2: Find bucket using: index = hashCode % arrayLength
// Step 3: Store "Apple"=10 in that bucket

map.put("Apple", 10);
map.put("Banana", 20);
map.put("Cherry", 30);

// When getting value
int value = map.get("Apple");
// Step 1: Calculate hashCode of "Apple"
// Step 2: Find bucket
// Step 3: Search in that bucket using equals()
System.out.println(value);  // Output: 10
```

### 49. What is hashing?

Hashing converts a key into an integer (hash code) to determine where to store the value in the array.

**Example:**
```java
String key = "Apple";
int hashCode = key.hashCode();  // e.g., 63476538
int bucketIndex = hashCode % 16;  // If array size is 16, index will be 0-15

// HashMap uses this index to store/retrieve value quickly
```

### 50. What is collision in HashMap?

Collision happens when two different keys have the same hash code and map to the same bucket.

**Example:**
```java
class Product {
    String name;
    
    Product(String name) {
        this.name = name;
    }
    
    @Override
    public int hashCode() {
        return 1;  // All products have same hashCode - causes collision
    }
    
    @Override
    public boolean equals(Object obj) {
        Product other = (Product) obj;
        return this.name.equals(other.name);
    }
}

Map<Product, Integer> stock = new HashMap<>();
stock.put(new Product("Laptop"), 10);
stock.put(new Product("Phone"), 20);
// Both have hashCode=1, stored in same bucket using LinkedList
System.out.println(stock.size());  // Output: 2
```

### 51. How does HashMap handle collisions?

Before Java 8: Uses LinkedList in the bucket. From Java 8: Uses LinkedList until 8 entries, then converts to TreeNode (Red-Black tree) for better performance.

**Example concept:**
```java
// Bucket structure internally:
// Bucket[5]: Apple=10 -> Banana=20 -> Cherry=30 (LinkedList)
// If more than 8 entries in same bucket:
// Bucket[5]: TreeNode structure (sorted by key)
```

### 52. What is load factor in HashMap?

Load factor (default 0.75) determines when HashMap should resize. When 75% full, it doubles capacity and rehashes all entries.

**Example:**
```java
// HashMap with initial capacity 16, load factor 0.75
Map<String, Integer> map = new HashMap<>();

// After adding 12 entries (16 * 0.75 = 12)
// HashMap will resize to capacity 32
// All existing entries will be rehashed to new buckets

// You can set custom load factor
Map<String, Integer> customMap = new HashMap<>(16, 0.5f);
// Will resize when 50% full (8 entries)
```

### 53. Why is String or Integer recommended as HashMap key?

Because they are immutable and have proper hashCode() and equals() implementation. Mutable keys can cause problems.

**Example of problem with mutable key:**
```java
class MutableKey {
    int value;
    
    MutableKey(int value) {
        this.value = value;
    }
    
    @Override
    public int hashCode() {
        return value;
    }
    
    @Override
    public boolean equals(Object obj) {
        return this.value == ((MutableKey)obj).value;
    }
}

Map<MutableKey, String> map = new HashMap<>();
MutableKey key = new MutableKey(1);
map.put(key, "Value1");

// Change key after inserting
key.value = 2;  // hashCode changes!

// Now can't retrieve value
System.out.println(map.get(key));  // Output: null (wrong bucket!)

// Better to use String or Integer (immutable)
Map<String, String> goodMap = new HashMap<>();
goodMap.put("Key1", "Value1");  // String key can't be modified
```

### 54. Can we have null key and null value in HashMap?

Yes, HashMap allows one null key and multiple null values.

**Example:**
```java
Map<String, String> map = new HashMap<>();
map.put(null, "Value1");     // Null key allowed
map.put("Key1", null);       // Null value allowed
map.put("Key2", null);       // Multiple null values allowed
map.put(null, "Value2");     // Overwrites previous null key

System.out.println(map);
// Output: {null=Value2, Key1=null, Key2=null}
```

### 55. Can TreeMap have null key?

No, TreeMap doesn't allow null key because it needs to compare keys for sorting. But it allows null values.

**Example:**
```java
Map<String, String> treeMap = new TreeMap<>();
treeMap.put("A", "Value1");
treeMap.put("B", null);        // Null value - OK
treeMap.put(null, "Value2");   // NullPointerException - Can't compare null
```

### 56. Difference between HashMap and Hashtable?

HashMap is not synchronized (fast, not thread-safe), allows null key/value. Hashtable is synchronized (slow, thread-safe), doesn't allow null.

**Example:**
```java
// HashMap - not thread-safe, allows null
Map<String, String> hashMap = new HashMap<>();
hashMap.put(null, "Value1");  // OK
hashMap.put("Key1", null);    // OK

// Hashtable - thread-safe, no null
Map<String, String> hashtable = new Hashtable<>();
hashtable.put(null, "Value1");  // NullPointerException
hashtable.put("Key1", null);    // NullPointerException

// For thread-safety, better use ConcurrentHashMap
Map<String, String> concurrentMap = new ConcurrentHashMap<>();
```

### 57. What is ConcurrentHashMap?

Thread-safe HashMap with better performance than Hashtable. It locks only a portion (segment) of the map, not the entire map.

**Example - Web Server Session Management:**
```java
ConcurrentHashMap<String, String> sessions = new ConcurrentHashMap<>();

// Thread 1: Adding session
Thread t1 = new Thread(() -> {
    sessions.put("user1", "Session1");
    sessions.put("user2", "Session2");
});

// Thread 2: Reading session
Thread t2 = new Thread(() -> {
    String session = sessions.get("user1");
    System.out.println("Session: " + session);
});

t1.start();
t2.start();
// No data corruption, better performance than Hashtable
```

### 58. Difference between HashMap and ConcurrentHashMap?

HashMap is not thread-safe. ConcurrentHashMap is thread-safe and allows concurrent reads/writes without locking entire map.

**Example:**
```java
// HashMap - not thread-safe
Map<String, Integer> hashMap = new HashMap<>();
// Multiple threads modifying simultaneously can cause issues

// ConcurrentHashMap - thread-safe
Map<String, Integer> concurrentMap = new ConcurrentHashMap<>();
// Multiple threads can safely read/write simultaneously

// Example with multiple threads
Runnable task = () -> {
    for(int i = 0; i < 1000; i++) {
        concurrentMap.put("Key" + i, i);
    }
};

Thread t1 = new Thread(task);
Thread t2 = new Thread(task);
t1.start();
t2.start();
// Safe with ConcurrentHashMap, would have issues with HashMap
```

### 59. How to iterate over a Map?

Multiple ways: entrySet(), keySet(), values(), or forEach.

**Example:**
```java
Map<String, Integer> map = new HashMap<>();
map.put("Apple", 10);
map.put("Banana", 20);
map.put("Cherry", 30);

// Method 1: entrySet() - get both key and value
for(Map.Entry<String, Integer> entry : map.entrySet()) {
    System.out.println(entry.getKey() + " = " + entry.getValue());
}

// Method 2: keySet() - get keys only
for(String key : map.keySet()) {
    System.out.println(key + " = " + map.get(key));
}

// Method 3: values() - get values only
for(Integer value : map.values()) {
    System.out.println(value);
}

// Method 4: forEach (Java 8)
map.forEach((key, value) -> System.out.println(key + " = " + value));
```

### 60. How to sort a HashMap by keys?

Convert to TreeMap or use Stream API.

**Example:**
```java
Map<String, Integer> map = new HashMap<>();
map.put("Zebra", 10);
map.put("Apple", 20);
map.put("Mango", 30);

// Method 1: Convert to TreeMap
Map<String, Integer> sortedMap = new TreeMap<>(map);
System.out.println(sortedMap);
// Output: {Apple=20, Mango=30, Zebra=10}

// Method 2: Stream API
map.entrySet().stream()
   .sorted(Map.Entry.comparingByKey())
   .forEach(entry -> System.out.println(entry.getKey() + " = " + entry.getValue()));
```

### 61. How to sort a HashMap by values?

Use Stream API or custom Comparator.

**Example - Sort students by marks:**
```java
Map<String, Integer> studentMarks = new HashMap<>();
studentMarks.put("John", 85);
studentMarks.put("Alice", 95);
studentMarks.put("Bob", 75);
studentMarks.put("Diana", 90);

// Sort by values (marks)
studentMarks.entrySet().stream()
   .sorted(Map.Entry.comparingByValue(Comparator.reverseOrder()))
   .forEach(entry -> System.out.println(entry.getKey() + " = " + entry.getValue()));

// Output:
// Alice = 95
// Diana = 90
// John = 85
// Bob = 75
```

### 62. What is WeakHashMap?

WeakHashMap has weak references to keys. If key has no other references, it can be garbage collected.

**Example - Cache Implementation:**
```java
Map<String, String> cache = new WeakHashMap<>();

String key1 = new String("Key1");
cache.put(key1, "Value1");

System.out.println("Before GC: " + cache.size());  // Output: 1

key1 = null;  // No more references to key
System.gc();  // Request garbage collection

Thread.sleep(1000);  // Wait for GC
System.out.println("After GC: " + cache.size());   // Output: 0 (entry removed)
```

### 63. What is IdentityHashMap?

IdentityHashMap uses `==` (reference equality) instead of `equals()` for comparing keys.

**Example:**
```java
Map<String, String> normalMap = new HashMap<>();
Map<String, String> identityMap = new IdentityHashMap<>();

String key1 = new String("Key");
String key2 = new String("Key");

// HashMap - uses equals() - considers both same
normalMap.put(key1, "Value1");
normalMap.put(key2, "Value2");  // Overwrites Value1
System.out.println("HashMap size: " + normalMap.size());  // Output: 1

// IdentityHashMap - uses == - considers both different
identityMap.put(key1, "Value1");
identityMap.put(key2, "Value2");  // Stores as separate entry
System.out.println("IdentityHashMap size: " + identityMap.size());  // Output: 2
```

### 64. How to check if a key exists in Map?

Use `containsKey()` method.

**Example:**
```java
Map<String, Integer> inventory = new HashMap<>();
inventory.put("Laptop", 10);
inventory.put("Phone", 20);
inventory.put("Tablet", 15);

// Check if product exists
if(inventory.containsKey("Laptop")) {
    System.out.println("Laptop stock: " + inventory.get("Laptop"));
} else {
    System.out.println("Product not found");
}
// Output: Laptop stock: 10
```

### 65. How to check if a value exists in Map?

Use `containsValue()` method.

**Example:**
```java
Map<String, String> employees = new HashMap<>();
employees.put("E001", "John");
employees.put("E002", "Alice");
employees.put("E003", "Bob");

// Check if employee name exists
if(employees.containsValue("Alice")) {
    System.out.println("Alice works here");
} else {
    System.out.println("Alice doesn't work here");
}
// Output: Alice works here
```

### 66. What is the purpose of compute() methods in Map?

`compute()`, `computeIfAbsent()`, `computeIfPresent()` help update map values based on logic.

**Example - Word Frequency Counter:**
```java
String text = "hello world hello java world";
String[] words = text.split(" ");

Map<String, Integer> frequency = new HashMap<>();

for(String word : words) {
    // Old way
    // if(frequency.containsKey(word)) {
    //     frequency.put(word, frequency.get(word) + 1);
    // } else {
    //     frequency.put(word, 1);
    // }
    
    // New way with compute
    frequency.compute(word, (key, value) -> value == null ? 1 : value + 1);
}

System.out.println(frequency);
// Output: {hello=2, world=2, java=1}
```

### 67. What is computeIfAbsent() used for?

It computes value only if key is absent. Useful for initializing collections in map.

**Example - Grouping Students by Department:**
```java
Map<String, List<String>> departments = new HashMap<>();

// Without computeIfAbsent
// if(!departments.containsKey("CS")) {
//     departments.put("CS", new ArrayList<>());
// }
// departments.get("CS").add("John");

// With computeIfAbsent (cleaner)
departments.computeIfAbsent("CS", k -> new ArrayList<>()).add("John");
departments.computeIfAbsent("CS", k -> new ArrayList<>()).add("Alice");
departments.computeIfAbsent("ECE", k -> new ArrayList<>()).add("Bob");

System.out.println(departments);
// Output: {CS=[John, Alice], ECE=[Bob]}
```

### 68. What is merge() method in Map?

`merge()` combines old value with new value using a function. Great for aggregations.

**Example - Shopping Cart:**
```java
Map<String, Integer> cart = new HashMap<>();

// Add items to cart
cart.merge("Apple", 2, Integer::sum);   // Add 2 apples
cart.merge("Banana", 3, Integer::sum);  // Add 3 bananas
cart.merge("Apple", 3, Integer::sum);   // Add 3 more apples (total 5)

System.out.println(cart);
// Output: {Apple=5, Banana=3}

// Another example - concatenating strings
Map<String, String> map = new HashMap<>();
map.merge("Key1", "Hello", (old, new_val) -> old + " " + new_val);
map.merge("Key1", "World", (old, new_val) -> old + " " + new_val);
System.out.println(map.get("Key1"));
// Output: Hello World
```

### 69. How to get a default value if key doesn't exist?

Use `getOrDefault()` method.

**Example:**
```java
Map<String, Integer> scores = new HashMap<>();
scores.put("John", 85);
scores.put("Alice", 95);

// Without getOrDefault
int bobScore = scores.get("Bob");  // null
// Need null check

// With getOrDefault (cleaner)
int bobScore2 = scores.getOrDefault("Bob", 0);  // Returns 0 if not found
System.out.println("Bob's score: " + bobScore2);  // Output: 0
```

### 70. How to convert Map to List?

Convert keys, values, or entries to List.

**Example:**
```java
Map<String, Integer> map = new HashMap<>();
map.put("Apple", 10);
map.put("Banana", 20);
map.put("Cherry", 30);

// List of keys
List<String> keys = new ArrayList<>(map.keySet());
System.out.println(keys);  // Output: [Apple, Banana, Cherry]

// List of values
List<Integer> values = new ArrayList<>(map.values());
System.out.println(values);  // Output: [10, 20, 30]

// List of entries
List<Map.Entry<String, Integer>> entries = new ArrayList<>(map.entrySet());
for(Map.Entry<String, Integer> entry : entries) {
    System.out.println(entry.getKey() + " = " + entry.getValue());
}
```

---

## **COMPARABLE AND COMPARATOR**

### 71. What is Comparable interface?

Comparable provides natural ordering for a class using `compareTo()` method. Class itself defines how to compare.

**Example - Student by Roll Number:**
```java
class Student implements Comparable<Student> {
    int rollNo;
    String name;
    
    Student(int rollNo, String name) {
        this.rollNo = rollNo;
        this.name = name;
    }
    
    @Override
    public int compareTo(Student other) {
        return this.rollNo - other.rollNo;  // Sort by roll number
    }
    
    @Override
    public String toString() {
        return rollNo + "-" + name;
    }
}

List<Student> students = new ArrayList<>();
students.add(new Student(103, "Bob"));
students.add(new Student(101, "Alice"));
students.add(new Student(102, "Charlie"));

Collections.sort(students);  // Uses compareTo()
System.out.println(students);
// Output: [101-Alice, 102-Charlie, 103-Bob]
```

### 72. What is Comparator interface?

Comparator provides external ordering. You can create multiple comparators for different sorting criteria.

**Example - Student by Name or Marks:**
```java
class Student {
    int rollNo;
    String name;
    int marks;
    
    Student(int rollNo, String name, int marks) {
        this.rollNo = rollNo;
        this.name = name;
        this.marks = marks;
    }
    
    @Override
    public String toString() {
        return rollNo + "-" + name + "-" + marks;
    }
}

List<Student> students = new ArrayList<>();
students.add(new Student(103, "Bob", 85));
students.add(new Student(101, "Alice", 95));
students.add(new Student(102, "Charlie", 75));

// Sort by name
Collections.sort(students, new Comparator<Student>() {
    public int compare(Student s1, Student s2) {
        return s1.name.compareTo(s2.name);
    }
});
System.out.println("By name: " + students);

// Sort by marks (descending)
Collections.sort(students, new Comparator<Student>() {
    public int compare(Student s1, Student s2) {
        return s2.marks - s1.marks;  // Reverse order
    }
});
System.out.println("By marks: " + students);
```

### 73. Difference between Comparable and Comparator?

Comparable: Natural ordering, class implements it, single sorting logic, uses `compareTo()`.
Comparator: External ordering, separate class, multiple sorting criteria, uses `compare()`.

**Example:**
```java
class Product implements Comparable<Product> {
    String name;
    double price;
    
    Product(String name, double price) {
        this.name = name;
        this.price = price;
    }
    
    // Comparable - natural ordering by name
    @Override
    public int compareTo(Product other) {
        return this.name.compareTo(other.name);
    }
    
    @Override
    public String toString() {
        return name + "-$" + price;
    }
}

// Comparator - custom ordering by price
class PriceComparator implements Comparator<Product> {
    public int compare(Product p1, Product p2) {
        return Double.compare(p1.price, p2.price);
    }
}

List<Product> products = new ArrayList<>();
products.add(new Product("Laptop", 1000));
products.add(new Product("Phone", 500));
products.add(new Product("Tablet", 300));

// Using Comparable (natural ordering)
Collections.sort(products);
System.out.println("By name: " + products);
// Output: [Laptop-$1000.0, Phone-$500.0, Tablet-$300.0]

// Using Comparator (custom ordering)
Collections.sort(products, new PriceComparator());
System.out.println("By price: " + products);
// Output: [Tablet-$300.0, Phone-$500.0, Laptop-$1000.0]
```

### 74. Can we use lambda for Comparator?

Yes, since Comparator is a functional interface, we can use lambda expressions.

**Example:**
```java
List<String> names = Arrays.asList("John", "Alice", "Bob", "Diana");

// Old way
Collections.sort(names, new Comparator<String>() {
    public int compare(String s1, String s2) {
        return s1.compareTo(s2);
    }
});

// Lambda way
Collections.sort(names, (s1, s2) -> s1.compareTo(s2));

// Even simpler with method reference
Collections.sort(names, String::compareTo);

// Reverse order
Collections.sort(names, (s1, s2) -> s2.compareTo(s1));
System.out.println(names);  // Output: [John, Diana, Bob, Alice]
```

### 75. How to sort using multiple fields?

Use `Comparator.thenComparing()` to chain multiple comparisons.

**Example - Sort employees by department, then by salary:**
```java
class Employee {
    String name;
    String department;
    int salary;
    
    Employee(String name, String department, int salary) {
        this.name = name;
        this.department = department;
        this.salary = salary;
    }
    
    @Override
    public String toString() {
        return name + "-" + department + "-$" + salary;
    }
}

List<Employee> employees = new ArrayList<>();
employees.add(new Employee("John", "IT", 50000));
employees.add(new Employee("Alice", "HR", 60000));
employees.add(new Employee("Bob", "IT", 55000));
employees.add(new Employee("Charlie", "HR", 58000));

// Sort by department, then by salary
Comparator<Employee> comparator = Comparator
    .comparing((Employee e) -> e.department)
    .thenComparing(e -> e.salary);

Collections.sort(employees, comparator);
System.out.println(employees);
// Output: [Alice-HR-$60000, Charlie-HR-$58000, John-IT-$50000, Bob-IT-$55000]
// Actually HR department first, then within HR sorted by salary
```

---

## **ITERATOR AND LISTITERATOR**

### 76. What is Iterator?

Iterator is used to traverse through collection one by one. It can remove elements while iterating.

**Example:**
```java
List<String> fruits = new ArrayList<>();
fruits.add("Apple");
fruits.add("Banana");
fruits.add("Cherry");
fruits.add("Date");

Iterator<String> iterator = fruits.iterator();
while(iterator.hasNext()) {
    String fruit = iterator.next();
    System.out.println(fruit);
    
    // Remove items starting with 'B'
    if(fruit.startsWith("B")) {
        iterator.remove();
    }
}

System.out.println(fruits);
// Output: [Apple, Cherry, Date]
```

### 77. Difference between Iterator and ListIterator?

Iterator: Unidirectional (forward only), works with all collections, can remove elements.
ListIterator: Bidirectional (forward & backward), works with List only, can add/remove/modify elements.

**Example:**
```java
List<String> list = new ArrayList<>();
list.add("A");
list.add("B");
list.add("C");

// Iterator - forward only
Iterator<String> iterator = list.iterator();
while(iterator.hasNext()) {
    System.out.println(iterator.next());
}

// ListIterator - bidirectional
ListIterator<String> listIterator = list.listIterator();

// Forward
while(listIterator.hasNext()) {
    System.out.println(listIterator.next());
}

// Backward
while(listIterator.hasPrevious()) {
    System.out.println(listIterator.previous());
}

// Can add elements
listIterator.add("D");
```

### 78. Can we modify collection while iterating?

Not directly - causes `ConcurrentModificationException`. Use Iterator's remove() or use ConcurrentCollections.

**Example:**
```java
List<String> list = new ArrayList<>();
list.add("Apple");
list.add("Banana");
list.add("Cherry");

// Wrong way - throws ConcurrentModificationException
for(String item : list) {
    if(item.equals("Banana")) {
        // list.remove(item);  // ERROR!
    }
}

// Correct way 1 - Use iterator
Iterator<String> iterator = list.iterator();
while(iterator.hasNext()) {
    String item = iterator.next();
    if(item.equals("Banana")) {
        iterator.remove();  // Correct
    }
}

// Correct way 2 - Use removeIf (Java 8)
list.removeIf(item -> item.equals("Cherry"));
```

### 79. What is fail-fast iterator?

Fail-fast iterator throws `ConcurrentModificationException` if collection is modified during iteration.

**Example:**
```java
List<String> list = new ArrayList<>();
list.add("A");
list.add("B");
list.add("C");

Iterator<String> iterator = list.iterator();

// Modifying collection while iterating
while(iterator.hasNext()) {
    String item = iterator.next();
    list.add("D");  // Modification - causes fail-fast
}
// Throws ConcurrentModificationException
```

### 80. What is fail-safe iterator?

Fail-safe iterator works on a copy of collection, so modifications don't throw exceptions. Used in concurrent collections.

**Example:**
```java
CopyOnWriteArrayList<String> list = new CopyOnWriteArrayList<>();
list.add("A");
list.add("B");
list.add("C");

Iterator<String> iterator = list.iterator();

// Modifying collection while iterating
while(iterator.hasNext()) {
    String item = iterator.next();
    System.out.println(item);
    list.add("D");  // No exception - fail-safe
}

System.out.println("Final list: " + list);
// Iterator worked on snapshot, modifications happened on actual list
```

---

## **UTILITY METHODS**

### 81. How to reverse a List?

Use `Collections.reverse()`.

**Example:**
```java
List<Integer> numbers = new ArrayList<>();
numbers.add(1);
numbers.add(2);
numbers.add(3);
numbers.add(4);
numbers.add(5);

System.out.println("Original: " + numbers);
// Output: [1, 2, 3, 4, 5]

Collections.reverse(numbers);
System.out.println("Reversed: " + numbers);
// Output: [5, 4, 3, 2, 1]
```

### 82. How to shuffle a List?

Use `Collections.shuffle()`.

**Example - Card Deck:**
```java
List<String> deck = new ArrayList<>();
deck.add("Ace of Spades");
deck.add("King of Hearts");
deck.add("Queen of Diamonds");
deck.add("Jack of Clubs");

System.out.println("Original deck: " + deck);

Collections.shuffle(deck);
System.out.println("Shuffled deck: " + deck);
// Random order each time
```

### 83. How to find min and max in Collection?

Use `Collections.min()` and `Collections.max()`.

**Example:**
```java
List<Integer> scores = Arrays.asList(85, 92, 78, 95, 88);

int minScore = Collections.min(scores);
int maxScore = Collections.max(scores);

System.out.println("Minimum score: " + minScore);  // Output: 78
System.out.println("Maximum score: " + maxScore);  // Output: 95

// With custom objects using Comparator
List<String> names = Arrays.asList("John", "Alice", "Bob");
String longest = Collections.max(names, Comparator.comparing(String::length));
System.out.println("Longest name: " + longest);  // Output: Alice
```

### 84. How to find frequency of an element?

Use `Collections.frequency()`.

**Example:**
```java
List<String> fruits = Arrays.asList("Apple", "Banana", "Apple", "Cherry", "Apple", "Banana");

int appleCount = Collections.frequency(fruits, "Apple");
int bananaCount = Collections.frequency(fruits, "Banana");

System.out.println("Apple appears: " + appleCount + " times");   // Output: 3
System.out.println("Banana appears: " + bananaCount + " times"); // Output: 2
```

### 85. How to create immutable List?

Use `Collections.unmodifiableList()` or `List.of()` (Java 9+).

**Example:**
```java
// Method 1: Collections.unmodifiableList()
List<String> mutableList = new ArrayList<>();
mutableList.add("Apple");
mutableList.add("Banana");

List<String> immutableList1 = Collections.unmodifiableList(mutableList);
// immutableList1.add("Cherry");  // Throws UnsupportedOperationException

// Method 2: List.of() (Java 9+)
List<String> immutableList2 = List.of("Apple", "Banana", "Cherry");
// immutableList2.add("Date");  // Throws UnsupportedOperationException

System.out.println(immutableList2);
```

### 86. How to create synchronized Collection?

Use `Collections.synchronizedList()` or similar methods.

**Example:**
```java
// Create synchronized list
List<String> list = new ArrayList<>();
List<String> syncList = Collections.synchronizedList(list);

// Thread-safe operations
Thread t1 = new Thread(() -> {
    syncList.add("Item1");
    syncList.add("Item2");
});

Thread t2 = new Thread(() -> {
    syncList.add("Item3");
    syncList.add("Item4");
});

t1.start();
t2.start();

// For iteration, still need manual synchronization
synchronized(syncList) {
    for(String item : syncList) {
        System.out.println(item);
    }
}
```

### 87. What is Arrays.asList()?

Converts array to fixed-size List. You can't add/remove elements, but can modify existing ones.

**Example:**
```java
String[] array = {"Apple", "Banana", "Cherry"};
List<String> list = Arrays.asList(array);

System.out.println(list);  // Output: [Apple, Banana, Cherry]

// Can modify
list.set(0, "Apricot");
System.out.println(list);  // Output: [Apricot, Banana, Cherry]

// Cannot add/remove
// list.add("Date");     // Throws UnsupportedOperationException
// list.remove("Banana"); // Throws UnsupportedOperationException

// Changes reflect in original array
System.out.println(array[0]);  // Output: Apricot
```

### 88. Difference between Arrays.asList() and new ArrayList()?

`Arrays.asList()` creates fixed-size list backed by array. `new ArrayList()` creates resizable list.

**Example:**
```java
// Arrays.asList() - fixed size
List<String> fixedList = Arrays.asList("A", "B", "C");
// fixedList.add("D");  // Error!

// new ArrayList() - resizable
List<String> resizableList = new ArrayList<>(Arrays.asList("A", "B", "C"));
resizableList.add("D");  // Works fine
System.out.println(resizableList);  // Output: [A, B, C, D]
```

### 89. How to copy one List to another?

Multiple ways: constructor, `addAll()`, `Collections.copy()`, Java 8 streams.

**Example:**
```java
List<String> original = new ArrayList<>();
original.add("Apple");
original.add("Banana");
original.add("Cherry");

// Method 1: Constructor
List<String> copy1 = new ArrayList<>(original);

// Method 2: addAll()
List<String> copy2 = new ArrayList<>();
copy2.addAll(original);

// Method 3: Collections.copy()
List<String> copy3 = new ArrayList<>(Arrays.asList(new String[original.size()]));
Collections.copy(copy3, original);

// Method 4: Stream (Java 8)
List<String> copy4 = original.stream().collect(Collectors.toList());

// All copies are independent
copy1.add("Date");
System.out.println("Original: " + original);  // Output: [Apple, Banana, Cherry]
System.out.println("Copy: " + copy1);         // Output: [Apple, Banana, Cherry, Date]
```

### 90. What is the difference between Collection.toArray() and Collection.toArray(T[])?

`toArray()` returns Object[]. `toArray(T[])` returns typed array.

**Example:**
```java
List<String> list = new ArrayList<>();
list.add("Apple");
list.add("Banana");
list.add("Cherry");

// Method 1: toArray() - returns Object[]
Object[] objArray = list.toArray();
// String str = objArray[0];  // Error! Need casting

// Method 2: toArray(T[]) - returns String[]
String[] strArray = list.toArray(new String[0]);
String str = strArray[0];  // No casting needed
System.out.println(str);  // Output: Apple
```

---

## **ADVANCED CONCEPTS**

### 91. What is the diamond problem and how does Collection framework solve it?

Diamond problem occurs when a class implements multiple interfaces with same method. Collections don't face this because interfaces provide default implementations (Java 8+).

**Example:**
```java
interface A {
    default void display() {
        System.out.println("A");
    }
}

interface B {
    default void display() {
        System.out.println("B");
    }
}

// Diamond problem
class C implements A, B {
    @Override
    public void display() {
        // Must override to resolve conflict
        A.super.display();  // Choose A's implementation
        // or B.super.display();
    }
}

C obj = new C();
obj.display();  // Output: A
```

### 92. What is EnumSet?

Special Set implementation for enum types. Very efficient - uses bit vectors internally.

**Example - Days of Week:**
```java
enum Day {
    MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY
}

// Create EnumSet with specific days
EnumSet<Day> weekdays = EnumSet.range(Day.MONDAY, Day.FRIDAY);
System.out.println("Weekdays: " + weekdays);
// Output: [MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY]

EnumSet<Day> weekend = EnumSet.of(Day.SATURDAY, Day.SUNDAY);
System.out.println("Weekend: " + weekend);
// Output: [SATURDAY, SUNDAY]

// All days
EnumSet<Day> allDays = EnumSet.allOf(Day.class);
System.out.println("All days: " + allDays);
```

### 93. What is EnumMap?

Map implementation specifically for enum keys. Very efficient and maintains enum's natural order.

**Example - Employee Count by Department:**
```java
enum Department {
    HR, IT, FINANCE, MARKETING
}

EnumMap<Department, Integer> employeeCount = new EnumMap<>(Department.class);
employeeCount.put(Department.IT, 50);
employeeCount.put(Department.HR, 20);
employeeCount.put(Department.FINANCE, 15);
employeeCount.put(Department.MARKETING, 25);

System.out.println(employeeCount);
// Output: {HR=20, IT=50, FINANCE=15, MARKETING=25} (enum order maintained)

// Iterate
for(Map.Entry<Department, Integer> entry : employeeCount.entrySet()) {
    System.out.println(entry.getKey() + ": " + entry.getValue() + " employees");
}
```

### 94. What is the purpose of Objects.equals() vs ==?

`==` compares references (memory address). `Objects.equals()` compares content and handles null safely.

**Example:**
```java
String s1 = new String("Hello");
String s2 = new String("Hello");
String s3 = s1;
String s4 = null;
String s5 = null;

// == compares references
System.out.println(s1 == s2);     // false (different objects)
System.out.println(s1 == s3);     // true (same object)

// equals() compares content
System.out.println(s1.equals(s2)); // true (same content)
// s4.equals(s5);  // NullPointerException

// Objects.equals() - null-safe
System.out.println(Objects.equals(s1, s2));  // true
System.out.println(Objects.equals(s4, s5));  // true (both null)
System.out.println(Objects.equals(s1, s4));  // false (one is null)
```

### 95. How to make a custom object sortable in TreeSet?

Implement Comparable or provide Comparator to TreeSet constructor.

**Example - Employee by Salary:**
```java
class Employee implements Comparable<Employee> {
    String name;
    int salary;
    
    Employee(String name, int salary) {
        this.name = name;
        this.salary = salary;
    }
    
    @Override
    public int compareTo(Employee other) {
        return this.salary - other.salary;  // Sort by salary
    }
    
    @Override
    public String toString() {
        return name + "-$" + salary;
    }
}

TreeSet<Employee> employees = new TreeSet<>();
employees.add(new Employee("John", 50000));
employees.add(new Employee("Alice", 60000));
employees.add(new Employee("Bob", 45000));

System.out.println(employees);
// Output: [Bob-$45000, John-$50000, Alice-$60000] (sorted by salary)
```

### 96. What happens if we add duplicate in TreeSet?

TreeSet uses `compareTo()` or Comparator to check duplicates. If comparison returns 0, it's considered duplicate and not added.

**Example:**
```java
class Student implements Comparable<Student> {
    int id;
    String name;
    
    Student(int id, String name) {
        this.id = id;
        this.name = name;
    }
    
    @Override
    public int compareTo(Student other) {
        return this.id - other.id;  // Compare by ID only
    }
    
    @Override
    public String toString() {
        return id + "-" + name;
    }
}

TreeSet<Student> students = new TreeSet<>();
students.add(new Student(1, "John"));
students.add(new Student(2, "Alice"));
students.add(new Student(1, "Bob"));  // Same ID - considered duplicate

System.out.println(students);
// Output: [1-John, 2-Alice] (Bob not added, ID 1 already exists)
```

### 97. Can we have heterogeneous objects in Collection?

Yes in raw collections, but not recommended. Use generics for type safety.

**Example:**
```java
// Without generics - heterogeneous (not recommended)
List rawList = new ArrayList();
rawList.add("String");
rawList.add(10);
rawList.add(true);
rawList.add(new Date());

// Runtime error possible
String str = (String) rawList.get(1);  // ClassCastException!

// With generics - homogeneous (recommended)
List<String> stringList = new ArrayList<>();
stringList.add("String1");
stringList.add("String2");
// stringList.add(10);  // Compile error - type safe!
```

### 98. What is the use of Collections.emptyList()?

Returns immutable empty list. Useful to avoid null checks and save memory.

**Example:**
```java
public class StudentService {
    public List<String> getStudentNames(String department) {
        if(department == null) {
            return Collections.emptyList();  // Instead of null
        }
        // ... fetch students
        return Arrays.asList("John", "Alice");
    }
}

StudentService service = new StudentService();
List<String> students = service.getStudentNames(null);

// No null check needed
for(String student : students) {
    System.out.println(student);  // Doesn't throw NullPointerException
}

// students.add("Bob");  // Throws UnsupportedOperationException (immutable)
```

### 99. What is the difference between Collections.emptyList() and new ArrayList()?

`Collections.emptyList()` returns immutable empty list (single instance). `new ArrayList()` creates mutable empty list (new instance every time).

**Example:**
```java
// emptyList() - immutable, same instance
List<String> empty1 = Collections.emptyList();
List<String> empty2 = Collections.emptyList();
System.out.println(empty1 == empty2);  // true (same object)
// empty1.add("Item");  // UnsupportedOperationException

// new ArrayList() - mutable, different instances
List<String> list1 = new ArrayList<>();
List<String> list2 = new ArrayList<>();
System.out.println(list1 == list2);  // false (different objects)
list1.add("Item");  // Works fine
```

### 100. How does ConcurrentHashMap achieve thread-safety without locking entire map?

ConcurrentHashMap divides map into segments and locks only the segment being modified, allowing concurrent access to other segments.

**Example concept:**
```java
ConcurrentHashMap<String, Integer> map = new ConcurrentHashMap<>();

// Thread 1 modifies Segment 1
Thread t1 = new Thread(() -> {
    map.put("Key1", 10);  // Locks only Segment 1
});

// Thread 2 can simultaneously modify Segment 2
Thread t2 = new Thread(() -> {
    map.put("Key100", 20);  // Locks only Segment 2 (different segment)
});

// Thread 3 can read from any segment
Thread t3 = new Thread(() -> {
    Integer value = map.get("Key1");  // No locking for reads
});

t1.start();
t2.start();
t3.start();
// Better concurrency than Hashtable which locks entire map
```

---

That's a comprehensive list of 100 Collection Framework interview questions with conversational answers and practical examples! Each answer explains the concept in simple English with real-world scenarios.