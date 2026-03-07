# JavaScript — Missing Interview Topics Q&A Guide (Part 2)

> **Topics still not covered after Part 1:**
> `==` vs `===` · Map & Set · Array Methods Deep Dive · Deep Copy (structuredClone) · Memoization · Pure Functions & Side Effects · Short-Circuit Evaluation · Computed Property Names · Logical Assignment Operators · Truthy/Falsy Complete Guide

---

## Table of Contents
1. [== vs === (Loose vs Strict Equality)](#1--vs--loose-vs-strict-equality)
2. [Map and Set](#2-map-and-set)
3. [Array Methods Deep Dive](#3-array-methods-deep-dive)
4. [Deep Copy — structuredClone, JSON trick, spread](#4-deep-copy--structuredclone-json-trick-spread)
5. [Memoization](#5-memoization)
6. [Pure Functions & Side Effects](#6-pure-functions--side-effects)
7. [Short-Circuit Evaluation](#7-short-circuit-evaluation)
8. [Computed Property Names](#8-computed-property-names)
9. [Logical Assignment Operators (&&=, ||=, ??=)](#9-logical-assignment-operators---)
10. [Truthy & Falsy — The Complete Guide](#10-truthy--falsy--the-complete-guide)

---

## 1. == vs === (Loose vs Strict Equality)

### Interview Question
**"What is the difference between `==` and `===` in JavaScript? Why should you prefer `===`?"**

### Answer (Conversational)

This is one of the most common JavaScript interview questions, and it all comes down to one word: **coercion**.

- `===` is **strict equality** — checks value AND type. No coercion.
- `==` is **loose equality** (abstract equality) — converts types to match before comparing.

```javascript
// === never converts types:
5 === 5;      // true  — same value, same type
5 === "5";    // false — different types, DONE

// == tries to convert types first:
5 == "5";     // true  — "5" is converted to number 5, then 5 == 5
0 == false;   // true  — false is converted to 0, then 0 == 0
0 == "";      // true  — "" is converted to 0, then 0 == 0
"" == false;  // true  — both become 0
```

#### The Weird Rules of == (Coercion Rules)

The JS spec has specific steps for `==`. Here are the tricky ones:

```javascript
// null and undefined are only == to each other (and nothing else):
null == undefined;  // true  ← special rule
null == 0;          // false
null == false;      // false
null == "";         // false
undefined == false; // false

// NaN is never equal to anything, even itself:
NaN == NaN;  // false!
NaN === NaN; // false!
// Use Number.isNaN() or Object.is(NaN, NaN) to check for NaN

// Objects compared by reference, not value:
[] == [];   // false — two different objects in memory
{} == {};   // false
[] == false; // true  — [] converts to "" converts to 0, false converts to 0
```

#### The Famous == Trap with []

```javascript
[] == false;  // true?! Here's why:
// Step 1: false → 0
// Step 2: [] → "" (array's .toString())
// Step 3: "" → 0
// Step 4: 0 == 0 → true
```

#### Object.is() — Even Stricter Than ===

`Object.is()` is like `===` but also correctly handles `NaN` and `-0`:

```javascript
NaN === NaN;        // false (broken in ===)
Object.is(NaN, NaN); // true ✓

-0 === +0;          // true (broken in ===)
Object.is(-0, +0);  // false ✓
```

#### Rule of Thumb

Always use `===`. The only legitimate use of `==` is checking for both `null` and `undefined` at once:

```javascript
// This is the one acceptable use of ==:
if (value == null) {
  // catches both null and undefined
}

// Equivalent to:
if (value === null || value === undefined) {
  // same result, but longer
}
```

### Coding Questions

**Q1:** What does each expression evaluate to?
```javascript
0 == false;     // ?
"" == false;    // ?
null == false;  // ?
null == undefined; // ?
NaN == NaN;     // ?
```

**Answer:** `true`, `true`, `false`, `true`, `false`

---

**Q2:** What's the output?
```javascript
console.log([] == ![]);
```

**Answer:** `true` — and this is why `==` is dangerous! `![]` evaluates to `false` (arrays are truthy, so `!truthy` = `false`). Then `[] == false` → both become `0` → `true`.

---

**Q3:** How does `Object.is()` differ from `===`?
```javascript
console.log(NaN === NaN);          // false
console.log(Object.is(NaN, NaN)); // true

console.log(-0 === +0);            // true
console.log(Object.is(-0, +0));   // false
```

---

## 2. Map and Set

### Interview Question
**"What is a Map in JavaScript? What is a Set? How are they different from plain objects and arrays?"**

### Answer (Conversational)

#### Map — Like an Object, but Better for Key-Value Pairs

A **Map** is a collection of key-value pairs — just like a plain object. But Maps have some important differences:

- **Any type can be a key** (not just strings/symbols like in objects): objects, functions, numbers, anything
- **Maintains insertion order** — always iterates in the order you added items
- **Has a `.size` property** — no need to do `Object.keys(obj).length`
- **No inherited keys** — plain objects inherit keys from `Object.prototype`; Maps don't

```javascript
const map = new Map();

// Any type as key:
map.set("name", "Alice");     // string key
map.set(42, "the answer");    // number key
map.set(true, "yes");         // boolean key
const obj = { id: 1 };
map.set(obj, "user data");    // object as key!

map.get("name");    // "Alice"
map.get(42);        // "the answer"
map.get(obj);       // "user data"

map.has("name");    // true
map.delete("name");
map.size;           // 3
```

#### Iterating a Map

```javascript
const scores = new Map([
  ["Alice", 95],
  ["Bob", 87],
  ["Carol", 92]
]);

// for...of (most common):
for (const [name, score] of scores) {
  console.log(`${name}: ${score}`);
}

// Other iteration methods:
scores.keys();    // iterator of keys
scores.values();  // iterator of values
scores.entries(); // iterator of [key, value] pairs

// Convert to array:
[...scores.keys()];   // ["Alice", "Bob", "Carol"]
[...scores.values()]; // [95, 87, 92]
```

#### When to use Map vs Object?

| Situation | Use |
|---|---|
| Non-string keys (e.g., objects as keys) | Map |
| Need to know the size easily | Map |
| Frequent add/delete operations | Map |
| JSON serialization | Object |
| Simple data structure / config | Object |
| Need prototype methods (not recommended but FYI) | Object |

#### Set — Like an Array, but Unique Values Only

A **Set** is a collection of **unique values** — duplicates are automatically ignored.

```javascript
const set = new Set();

set.add(1);
set.add(2);
set.add(2); // duplicate — ignored!
set.add(3);

set.size;      // 3 — not 4!
set.has(2);    // true
set.delete(2);
set.has(2);    // false

// Initialize with an array:
const fruits = new Set(["apple", "banana", "apple", "cherry"]);
// Set(3) { "apple", "banana", "cherry" } — "apple" deduplicated!
```

#### The #1 Use Case for Set: Remove Duplicates from an Array

```javascript
const nums = [1, 2, 2, 3, 3, 3, 4];
const unique = [...new Set(nums)]; // [1, 2, 3, 4]
```

This is extremely clean and commonly asked in interviews.

#### Iterating a Set

```javascript
const tags = new Set(["js", "react", "node"]);

for (const tag of tags) {
  console.log(tag); // js, react, node
}

[...tags]; // ["js", "react", "node"] — convert to array
```

#### Set Operations (Union, Intersection, Difference)

```javascript
const a = new Set([1, 2, 3, 4]);
const b = new Set([3, 4, 5, 6]);

// Union — all elements from both:
const union = new Set([...a, ...b]); // {1, 2, 3, 4, 5, 6}

// Intersection — only elements in both:
const intersection = new Set([...a].filter(x => b.has(x))); // {3, 4}

// Difference — in a but not in b:
const difference = new Set([...a].filter(x => !b.has(x))); // {1, 2}
```

### Coding Questions

**Q1:** Remove all duplicate values from this array using a Set:
```javascript
const arr = [1, 1, 2, 3, 3, 4, 5, 5];
const unique = [...new Set(arr)]; // [1, 2, 3, 4, 5]
```

---

**Q2:** Why does this fail with a plain object but work with a Map?
```javascript
// Object — can't use object as key:
const obj = {};
const key = { id: 1 };
obj[key] = "value";
console.log(Object.keys(obj)); // ["[object Object]"] — key was stringified!

// Map — object as key works perfectly:
const map = new Map();
map.set(key, "value");
map.get(key); // "value" ✓
```

---

**Q3:** Count the frequency of each character in a string using Map:
```javascript
function charFrequency(str) {
  const freq = new Map();
  for (const char of str) {
    freq.set(char, (freq.get(char) ?? 0) + 1);
  }
  return freq;
}

charFrequency("hello");
// Map { h: 1, e: 1, l: 2, o: 1 }
```

---

## 3. Array Methods Deep Dive

### Interview Question
**"Explain the array methods find, findIndex, some, every, flat, flatMap, splice, slice, sort, includes, indexOf, and Array.from."**

### Answer (Conversational)

You already know `map`, `filter`, and `reduce` from the core3.md notes. Here are the rest of the essential array methods.

#### find() — Get the First Match

Returns the **first element** that passes the test, or `undefined` if none do.

```javascript
const users = [
  { id: 1, name: "Alice" },
  { id: 2, name: "Bob" },
  { id: 3, name: "Carol" }
];

const user = users.find(u => u.id === 2);
// { id: 2, name: "Bob" }

users.find(u => u.id === 99); // undefined — not found
```

`find` vs `filter`: `find` returns **one element**, `filter` returns **an array of all matches**.

#### findIndex() — Get the Index of First Match

Like `find`, but returns the **index** instead of the element. Returns `-1` if not found.

```javascript
const nums = [10, 20, 30, 40];

nums.findIndex(n => n > 15); // 1 — index of 20
nums.findIndex(n => n > 99); // -1 — not found
```

#### some() — Does ANY element pass the test?

Returns `true` as soon as **one element** passes. Stops early (short-circuits).

```javascript
const ages = [15, 18, 22, 13];

ages.some(age => age >= 18); // true — at least one person is 18+
ages.some(age => age >= 25); // false — no one is 25+
```

#### every() — Do ALL elements pass the test?

Returns `true` only if **every single element** passes. Stops early on the first failure.

```javascript
const scores = [85, 90, 78, 92];

scores.every(s => s >= 70); // true — everyone passed 70
scores.every(s => s >= 80); // false — 78 < 80, so false
```

**`some` vs `every`:**
- `some` = "Is there at least one?" (like logical OR across elements)
- `every` = "Are they all?" (like logical AND across elements)

#### flat() — Flatten Nested Arrays

`flat()` removes one level of nesting by default. Pass a depth number to flatten deeper.

```javascript
const nested = [1, [2, 3], [4, [5, 6]]];

nested.flat();    // [1, 2, 3, 4, [5, 6]] — one level
nested.flat(2);   // [1, 2, 3, 4, 5, 6]  — two levels
nested.flat(Infinity); // flatten no matter how deep
```

#### flatMap() — map() then flat() in one step

Like calling `.map()` followed by `.flat(1)`, but more efficient:

```javascript
const sentences = ["Hello World", "Foo Bar"];

sentences.map(s => s.split(" "));
// [["Hello", "World"], ["Foo", "Bar"]] — nested

sentences.flatMap(s => s.split(" "));
// ["Hello", "World", "Foo", "Bar"] — flattened automatically
```

#### splice() — Modify Array In-Place

`splice` **mutates** the original array. It can remove, insert, or replace elements.

```javascript
// splice(startIndex, deleteCount, ...itemsToInsert)

const arr = ["a", "b", "c", "d", "e"];

// Remove 2 elements starting at index 1:
arr.splice(1, 2); // returns ["b", "c"]
// arr is now ["a", "d", "e"]

// Insert without removing (deleteCount = 0):
arr.splice(1, 0, "X", "Y"); // inserts at index 1
// arr is now ["a", "X", "Y", "d", "e"]

// Replace element:
arr.splice(2, 1, "Z"); // remove 1 at index 2, insert "Z"
```

#### slice() — Copy Part of Array (Non-Mutating)

`slice` returns a **new array** without modifying the original.

```javascript
const arr = [10, 20, 30, 40, 50];

arr.slice(1, 3);  // [20, 30] — from index 1 up to (NOT including) 3
arr.slice(2);     // [30, 40, 50] — from index 2 to end
arr.slice(-2);    // [40, 50] — last 2 elements
arr.slice();      // [10, 20, 30, 40, 50] — full shallow copy!

// Original is unchanged:
console.log(arr); // [10, 20, 30, 40, 50]
```

**`splice` vs `slice` — the critical difference:**
| | `splice` | `slice` |
|---|---|---|
| Modifies original? | **Yes** (mutates) | No |
| Returns | Removed elements | New array copy |
| Use case | Remove/insert into array | Extract a portion |

#### sort() — Sort In-Place

`sort()` **mutates** the original array and by default sorts as strings (alphabetical), which breaks for numbers!

```javascript
// Default sort — WRONG for numbers:
[10, 9, 2, 100].sort(); // [10, 100, 2, 9] — sorted as strings!

// Correct sort for numbers (ascending):
[10, 9, 2, 100].sort((a, b) => a - b); // [2, 9, 10, 100]

// Descending:
[10, 9, 2, 100].sort((a, b) => b - a); // [100, 10, 9, 2]

// Sort strings alphabetically (default works):
["banana", "apple", "cherry"].sort(); // ["apple", "banana", "cherry"]

// Sort objects by property:
const users = [{ name: "Carol" }, { name: "Alice" }, { name: "Bob" }];
users.sort((a, b) => a.name.localeCompare(b.name));
// [{ name: "Alice" }, { name: "Bob" }, { name: "Carol" }]
```

#### includes() and indexOf()

```javascript
const arr = [1, 2, 3, NaN];

// includes — returns boolean (handles NaN correctly!):
arr.includes(2);   // true
arr.includes(99);  // false
arr.includes(NaN); // true ✓

// indexOf — returns index or -1 (does NOT handle NaN):
arr.indexOf(2);    // 1
arr.indexOf(99);   // -1
arr.indexOf(NaN);  // -1 ✗ — NaN !== NaN, so indexOf fails for NaN

// Use includes over indexOf when you just want to check existence
```

#### Array.from() — Create Array from Anything Iterable

```javascript
// From a string:
Array.from("hello"); // ["h", "e", "l", "l", "o"]

// From a Set (great for deduplication):
Array.from(new Set([1, 2, 2, 3])); // [1, 2, 3]

// From a Map:
Array.from(new Map([["a", 1], ["b", 2]])); // [["a", 1], ["b", 2]]

// Create array of length N with values (map function as 2nd arg):
Array.from({ length: 5 }, (_, i) => i + 1); // [1, 2, 3, 4, 5]
Array.from({ length: 3 }, () => 0);          // [0, 0, 0]

// From DOM NodeList (common use!):
const divs = Array.from(document.querySelectorAll("div")); // real array
divs.forEach(div => console.log(div)); // can now use array methods!
```

#### at() — Access Elements with Negative Indexing

```javascript
const arr = [10, 20, 30, 40, 50];

arr.at(0);   // 10 — first element
arr.at(-1);  // 50 — last element (no more arr[arr.length - 1]!)
arr.at(-2);  // 40 — second to last
```

### Coding Questions

**Q1:** Given an array of products, find the first one that is out of stock:
```javascript
const products = [
  { name: "Shirt", inStock: true },
  { name: "Jeans", inStock: false },
  { name: "Hat", inStock: true }
];

const outOfStock = products.find(p => !p.inStock);
// { name: "Jeans", inStock: false }
```

---

**Q2:** Check if all scores are passing (>= 60):
```javascript
const scores = [85, 72, 91, 68];
const allPassing = scores.every(s => s >= 60); // true
```

---

**Q3:** What's the output and why?
```javascript
[3, 1, 10, 2].sort();
```

**Answer:** `[1, 10, 2, 3]` — sorted as strings! "10" comes before "2" because "1" < "2" alphabetically. Always pass a comparator for numbers: `.sort((a, b) => a - b)`.

---

**Q4:** Create an array of numbers 1 to 10 using `Array.from`:
```javascript
const nums = Array.from({ length: 10 }, (_, i) => i + 1);
// [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
```

---

## 4. Deep Copy — structuredClone, JSON trick, spread

### Interview Question
**"What are the different ways to deep copy an object in JavaScript? What are the trade-offs?"**

### Answer (Conversational)

This is a big gotcha topic. JavaScript passes objects by reference, so a simple assignment or shallow spread doesn't create a true independent copy. Nested objects are still shared.

#### Why Shallow Copy Isn't Enough

```javascript
const original = {
  name: "Alice",
  address: { city: "NYC" } // nested object
};

// Shallow copy with spread:
const copy = { ...original };

copy.name = "Bob";           // ✓ doesn't affect original.name
copy.address.city = "LA";    // ✗ MUTATES original.address.city too!

console.log(original.address.city); // "LA" — unexpected mutation!
```

The spread `{ ...obj }` only copies the **top level** — nested objects still point to the same reference.

#### Method 1: structuredClone() — The Modern Best Way

`structuredClone()` is the **correct, built-in way** to deep copy in modern JavaScript. It was added in 2022 and is available in all modern browsers and Node.js 17+.

```javascript
const original = {
  name: "Alice",
  address: { city: "NYC" },
  hobbies: ["reading", "coding"],
  date: new Date()
};

const deepCopy = structuredClone(original);

deepCopy.address.city = "LA";
console.log(original.address.city); // "NYC" — unchanged!

deepCopy.hobbies.push("gaming");
console.log(original.hobbies); // ["reading", "coding"] — unchanged!
```

**What structuredClone handles correctly:**
- Nested objects and arrays
- Dates, RegExps, Maps, Sets
- Circular references!

**What it can't handle:**
- Functions (they're excluded / throw an error)
- DOM nodes
- class instances with custom methods (methods aren't cloned)

```javascript
// Functions are NOT cloned:
const obj = { fn: () => "hello", name: "Alice" };
const clone = structuredClone(obj); // throws DataCloneError — functions can't be cloned
```

#### Method 2: JSON.parse(JSON.stringify()) — The Old Way

The classic trick, but with important limitations:

```javascript
const original = { name: "Alice", address: { city: "NYC" } };
const deepCopy = JSON.parse(JSON.stringify(original));

deepCopy.address.city = "LA";
console.log(original.address.city); // "NYC" ✓
```

**What gets LOST / broken:**
```javascript
const problematic = {
  date: new Date(),          // becomes string! not a Date
  fn: function() {},          // removed entirely!
  undef: undefined,           // removed entirely!
  nan: NaN,                   // becomes null
  inf: Infinity,              // becomes null
  regex: /hello/,             // becomes {}
};

const copy = JSON.parse(JSON.stringify(problematic));
// {
//   date: "2024-01-01T00:00:00.000Z", // string, not Date
//   nan: null,
//   inf: null,
//   regex: {}
//   // fn and undef are gone!
// }
```

Also, **circular references crash** JSON.stringify.

#### Method 3: Spread + Spread (Manual Nested)

For specific cases where you know the structure:

```javascript
const original = { name: "Alice", address: { city: "NYC" } };

// Manually spread at each level:
const copy = {
  ...original,
  address: { ...original.address } // explicitly copy the nested object
};
```

This works but doesn't scale to deeply nested structures.

#### Comparison Summary

| Method | Handles Nested | Handles Functions | Handles Date | Handles Circular Refs | Availability |
|---|---|---|---|---|---|
| `{ ...obj }` | No (shallow) | Yes (reference) | Yes (reference) | Yes | Always |
| `JSON.parse/stringify` | Yes | No (drops) | No (to string) | No (crashes) | Always |
| `structuredClone()` | Yes | No (throws) | Yes | Yes | Modern JS |
| Lodash `_.cloneDeep()` | Yes | No | Yes | Yes | With library |

**Rule of Thumb:** Use `structuredClone()` in 2024. Only fall back to JSON trick if you're sure your data has no Dates, functions, or circular refs.

### Coding Questions

**Q1:** Which method would you use and why?
- Copying a Redux state object with nested arrays and plain data → `structuredClone()`
- Quick copy of a simple config object with strings/numbers → spread `{...obj}` (shallow is fine)
- Copying to send over `JSON.stringify` anyway → JSON trick works fine since you're stringifying anyway

---

**Q2:** What happens here?
```javascript
const a = { x: { y: 1 } };
const b = { ...a };
b.x.y = 999;
console.log(a.x.y);
```

**Answer:** `999` — spread is a shallow copy. `b.x` and `a.x` both point to the same nested object.

---

## 5. Memoization

### Interview Question
**"What is memoization? Implement a memoize function."**

### Answer (Conversational)

**Memoization** is an optimization technique where you **cache the result of expensive function calls** and return the cached result when the same inputs occur again. It trades memory for speed — you use extra memory to avoid repeating expensive calculations.

Think of it like remembering an answer you already calculated instead of working it out from scratch every time.

#### Basic Example — Fibonacci without Memoization

```javascript
// Without memoization — extremely slow for large n
function fib(n) {
  if (n <= 1) return n;
  return fib(n - 1) + fib(n - 2); // recalculates the same values over and over!
}

fib(40); // calculates fib(38) twice, fib(37) four times, etc.
// For fib(40), this does ~2 billion operations!
```

#### With Memoization — Lightning Fast

```javascript
function memoizedFib() {
  const cache = {}; // closure over this cache object

  return function fib(n) {
    if (n in cache) {
      return cache[n]; // return cached result immediately!
    }
    if (n <= 1) return n;
    cache[n] = fib(n - 1) + fib(n - 2);
    return cache[n];
  };
}

const fib = memoizedFib();
fib(40); // calculates each value only once — blazing fast!
fib(40); // instant — already cached!
```

#### Building a Generic memoize() Function

This is the version interviewers often want you to implement:

```javascript
function memoize(fn) {
  const cache = new Map();

  return function(...args) {
    // Create a cache key from the arguments
    const key = JSON.stringify(args);

    if (cache.has(key)) {
      console.log("Cache hit!");
      return cache.get(key);
    }

    const result = fn.apply(this, args);
    cache.set(key, result);
    return result;
  };
}

// Usage:
function expensiveAdd(a, b) {
  console.log("Computing...");
  return a + b;
}

const memoAdd = memoize(expensiveAdd);
memoAdd(1, 2); // "Computing..." → 3
memoAdd(1, 2); // "Cache hit!" → 3 (no recomputation!)
memoAdd(3, 4); // "Computing..." → 7 (new args, computes)
memoAdd(3, 4); // "Cache hit!" → 7
```

#### Trade-offs

- **Pro:** Dramatically speeds up repeated calls with same arguments
- **Con:** Uses memory to store results
- **Con:** `JSON.stringify` as a key breaks for functions or circular objects as arguments
- **When to use:** Pure functions (same input always gives same output) with expensive computation

### Coding Questions

**Q1:** Implement memoize that only works for single-argument functions:
```javascript
function memoize(fn) {
  const cache = new Map();
  return function(arg) {
    if (cache.has(arg)) return cache.get(arg);
    const result = fn(arg);
    cache.set(arg, result);
    return result;
  };
}
```

---

**Q2:** Why does memoization only work with pure functions?

**Answer:** Because memoization assumes the same inputs always produce the same outputs. If a function depends on external state (like a global variable or the current time), caching would return stale results. Pure functions have no such side effects — they always return the same output for the same input.

---

## 6. Pure Functions & Side Effects

### Interview Question
**"What is a pure function? What are side effects? Why do they matter?"**

### Answer (Conversational)

#### Pure Functions

A **pure function** is a function that:
1. **Always returns the same output for the same input** — no randomness, no external dependencies
2. **Has no side effects** — doesn't modify anything outside of itself

```javascript
// Pure function — same input, same output, modifies nothing:
function add(a, b) {
  return a + b;
}

add(2, 3); // always 5 — no matter when or where you call it
add(2, 3); // always 5
```

#### Impure Function Examples

```javascript
// IMPURE — depends on external variable:
let tax = 0.2;
function calculateTotal(price) {
  return price * (1 + tax); // depends on 'tax' outside the function!
}
// If tax changes, same price gives different total!

// IMPURE — modifies external state (side effect):
let total = 0;
function addToTotal(n) {
  total += n; // modifies outer variable!
}

// IMPURE — Math.random() is not deterministic:
function getRandom(max) {
  return Math.floor(Math.random() * max); // different every time!
}

// IMPURE — Date.now() changes over time:
function getAge(birthYear) {
  return new Date().getFullYear() - birthYear; // different result each year
}
```

#### What are Side Effects?

A **side effect** is anything a function does that affects the world outside of it:

- Modifying a variable outside the function
- Modifying an object/array that was passed in
- Making an API call (fetch, axios)
- Writing to a file or database
- `console.log()` — even this is a side effect!
- Modifying the DOM
- Setting a timer (setTimeout)

```javascript
// This has a side effect — mutates the input array:
function addItem(arr, item) {
  arr.push(item); // modifies the original array!
  return arr;
}

// Pure version — returns new array, original untouched:
function addItem(arr, item) {
  return [...arr, item]; // new array every time
}
```

#### Why Pure Functions Matter

1. **Predictable** — easy to understand and reason about
2. **Testable** — just call with input, check output. No mocking needed.
3. **Memoizable** — same input = same output, so results can be cached safely
4. **Parallelizable** — no shared state = safe to run concurrently
5. **React/Redux depend on them** — React re-renders are based on detecting state changes, which only works reliably with pure functions that return new objects instead of mutating

```javascript
// Redux reducer — must be a pure function:
function counterReducer(state = 0, action) {
  switch (action.type) {
    case "INCREMENT":
      return state + 1; // returns new value, doesn't mutate
    default:
      return state;
  }
}
```

### Coding Questions

**Q1:** Is this function pure? Why or why not?
```javascript
function double(arr) {
  return arr.map(x => x * 2);
}
```

**Answer:** Yes — it returns a new array without modifying the input, and the output depends only on the input.

---

**Q2:** Make this impure function pure:
```javascript
// Impure:
const users = [];
function addUser(name) {
  users.push({ name }); // side effect!
}

// Pure:
function addUser(users, name) {
  return [...users, { name }]; // returns new array
}
```

---

## 7. Short-Circuit Evaluation

### Interview Question
**"What is short-circuit evaluation in JavaScript? How can it be used practically?"**

### Answer (Conversational)

**Short-circuit evaluation** means JavaScript stops evaluating a logical expression as soon as it knows the result — it doesn't bother checking the rest.

#### `&&` — Stops at First Falsy, Returns That Value

With `&&`, if the left side is **falsy**, the whole expression is falsy — no need to check the right side:

```javascript
false && "hello";     // false — stopped at false, never evaluated "hello"
null && doSomething(); // null — doSomething() is NEVER called!
0 && "anything";      // 0

true && "hello";  // "hello" — left was truthy, so returns the right side
1 && "hello";     // "hello"
"yes" && 42;      // 42
```

**Practical use — conditionally render or call:**
```javascript
// Only call if user exists:
user && sendEmail(user); // sendEmail only runs if user is truthy

// React JSX pattern (conditional rendering):
{isLoggedIn && <Dashboard />}  // Dashboard only renders if isLoggedIn is true
```

#### `||` — Stops at First Truthy, Returns That Value

With `||`, if the left side is **truthy**, the whole expression is truthy — no need to check the right side:

```javascript
"hello" || "world";  // "hello" — left was truthy, stop here
true || doSomething(); // true — doSomething() is NEVER called!

false || "world";    // "world" — left was falsy, return right side
null || "default";   // "default"
0 || 42;             // 42
"" || "fallback";    // "fallback"
```

**Practical use — default values:**
```javascript
// Old way to set defaults (before ??):
function greet(name) {
  name = name || "Guest"; // if name is falsy, use "Guest"
  console.log("Hello, " + name);
}

// Problem: 0 and "" are falsy, so they get replaced too!
greet(0); // "Hello, Guest" — probably wrong!
```

#### Returning Non-Boolean Values

A key insight: `&&` and `||` don't necessarily return `true` or `false`. They return **one of the actual operands**:

```javascript
// && returns first falsy or last value:
1 && 2 && 3;       // 3 — all truthy, returns last
1 && null && 3;    // null — returns first falsy

// || returns first truthy or last value:
null || "" || 0;   // 0 — all falsy, returns last
null || "hello";   // "hello" — returns first truthy
```

**Real-world pattern:**
```javascript
// Get user's name or fall back to "Anonymous":
const displayName = user?.name || "Anonymous";

// Only access method if it exists:
const result = config.transform && config.transform(data);

// Chain of fallbacks:
const value = primary || secondary || tertiary || "default";
```

### Coding Questions

**Q1:** What is the output?
```javascript
console.log(false && "hello");
console.log(true && "hello");
console.log(null || "default");
console.log("value" || "default");
```

**Answer:** `false`, `"hello"`, `"default"`, `"value"`

---

**Q2:** What's the practical difference between these?
```javascript
const a = config.timeout || 3000;
const b = config.timeout ?? 3000;
```

**Answer:** If `config.timeout` is `0` (no timeout):
- `a = 3000` — because `0` is falsy, `||` replaces it (likely wrong!)
- `b = 0` — because `0` is not null/undefined, `??` keeps it (correct!)

---

## 8. Computed Property Names

### Interview Question
**"What are computed property names in JavaScript? When would you use them?"**

### Answer (Conversational)

**Computed property names** let you use a **dynamic value** (variable or expression) as an object's property key using square bracket `[...]` syntax in an object literal.

Before computed property names, if you wanted a dynamic key you had to do it in two steps:

```javascript
// OLD WAY — two steps:
const key = "name";
const obj = {};
obj[key] = "Alice"; // set dynamic key after creation

// NEW WAY — computed property name, one step:
const key = "name";
const obj = { [key]: "Alice" }; // { name: "Alice" }
```

#### Any Expression Works Inside the Brackets

```javascript
const prefix = "user";

const obj = {
  [prefix + "Name"]: "Alice",     // userNamee: "Alice"
  [prefix + "Age"]: 30,           // userAge: 30
  [`${prefix}Email`]: "a@b.com"   // userEmail: "a@b.com"
};
```

#### Practical Uses

**1. Building objects in loops:**
```javascript
const fields = ["firstName", "lastName", "email"];
const formData = {};

fields.forEach(field => {
  formData[field] = ""; // initialise each field to ""
});

// Or with reduce + computed property:
const formData = fields.reduce((acc, field) => {
  return { ...acc, [field]: "" };
}, {});
// { firstName: "", lastName: "", email: "" }
```

**2. React — handling multiple form inputs with one handler:**
```javascript
function handleChange(event) {
  const { name, value } = event.target;
  setFormData(prev => ({
    ...prev,
    [name]: value // dynamic key = the input's name attribute!
  }));
}

// One handler works for ALL inputs:
// <input name="firstName" onChange={handleChange} />
// <input name="email" onChange={handleChange} />
```

**3. Object from dynamic data:**
```javascript
function createAction(type, payload) {
  return { type, payload }; // shorthand property names
}

// With computed:
function createReducerMap(actions) {
  return actions.reduce((map, action) => ({
    ...map,
    [action.type]: action.handler
  }), {});
}
```

### Coding Questions

**Q1:** What does this output?
```javascript
const prop = "color";
const obj = { [prop]: "red", [`${prop}Code`]: "#FF0000" };
console.log(obj);
```

**Answer:** `{ color: "red", colorCode: "#FF0000" }`

---

**Q2:** Use a computed property to dynamically update a field in state (React pattern):
```javascript
function updateField(state, fieldName, value) {
  return { ...state, [fieldName]: value };
}

const state = { name: "Alice", email: "alice@a.com" };
updateField(state, "email", "new@a.com");
// { name: "Alice", email: "new@a.com" }
```

---

## 9. Logical Assignment Operators (&&=, ||=, ??=)

### Interview Question
**"What are the logical assignment operators in JavaScript (`&&=`, `||=`, `??=`)? When were they introduced?"**

### Answer (Conversational)

These were introduced in **ES2021** (ES12). They combine logical operators with assignment — a shorthand for very common patterns.

#### `||=` — Assign if Currently Falsy

`x ||= y` means: "If `x` is falsy, assign `y` to it."

```javascript
// Old way:
if (!user.name) {
  user.name = "Anonymous";
}
// Or:
user.name = user.name || "Anonymous";

// New way with ||=:
user.name ||= "Anonymous"; // same thing, cleaner!
```

#### `&&=` — Assign if Currently Truthy

`x &&= y` means: "If `x` is truthy, assign `y` to it."

```javascript
// Old way:
if (user.isAdmin) {
  user.isAdmin = false;
}
// Or:
user.isAdmin = user.isAdmin && false;

// New way:
user.isAdmin &&= false; // sets to false only if it was already truthy
```

**Use case:** Update a property only when it has a value:
```javascript
// Only transform the name if it exists:
user.name &&= user.name.toUpperCase();
// If name is "alice" → becomes "ALICE"
// If name is "" or null → stays as is (falsy, no assignment)
```

#### `??=` — Assign if Currently null or undefined

`x ??= y` means: "If `x` is `null` or `undefined`, assign `y` to it."

```javascript
// Old way:
if (user.preferences == null) {
  user.preferences = {};
}
// Or:
user.preferences = user.preferences ?? {};

// New way:
user.preferences ??= {}; // set default only if null/undefined
```

**This is smarter than `||=`** for cases where `0`, `false`, or `""` are valid values:

```javascript
let score = 0;

score ||= 100;  // score becomes 100! (0 is falsy — wrong!)
score ??= 100;  // score stays 0   (0 is not null/undefined — correct!)
```

#### All Three Together

```javascript
function initConfig(config) {
  config.timeout ??= 3000;    // set if not set yet (null/undefined)
  config.retries ??= 3;
  config.debug &&= false;     // clear debug flag if it was set
  config.name ||= "unnamed";  // set name if falsy (including empty string)
  return config;
}
```

### Coding Questions

**Q1:** What's the value of `x` after each line?
```javascript
let x = null;
x ??= "hello";  // ?

let y = 0;
y ||= 42;       // ?

let z = "admin";
z &&= z.toUpperCase(); // ?
```

**Answer:** `x = "hello"` (was null), `y = 42` (0 is falsy), `z = "ADMIN"` (was truthy so assignment runs)

---

**Q2:** What's the difference between `x ||= y` and `x = x || y`?

**Answer:** They produce the same result BUT `x ||= y` only performs the assignment if `x` is falsy. `x = x || y` always performs the assignment. This matters when setting a property triggers a setter:
```javascript
// If setting 'name' has side effects (like a DOM update),
// x ||= y avoids the unnecessary assignment when x is already truthy
```

---

## 10. Truthy & Falsy — The Complete Guide

### Interview Question
**"What values are falsy in JavaScript? What are truthy? Give examples of common gotchas."**

### Answer (Conversational)

JavaScript's type coercion means values are automatically converted to `true` or `false` when used in boolean contexts (if statements, while loops, `&&`, `||`, ternaries, etc.).

#### The Complete List of FALSY Values (Only 8!)

Memorize these — there are only 8 falsy values in all of JavaScript:

```javascript
false       // the boolean false
0           // the number zero
-0          // negative zero
0n          // BigInt zero
""          // empty string
''          // empty string (same thing)
``          // empty template literal (same thing)
null        // null
undefined   // undefined
NaN         // Not a Number
```

**That's it.** Everything else is **truthy**.

#### Everything TRUTHY (Selected Gotchas)

```javascript
// These are ALL truthy — might surprise you:
"0"        // non-empty string, even though it's "zero"
"false"    // non-empty string, even though it says "false"
[]         // empty array is truthy!
{}         // empty object is truthy!
function() {} // functions are truthy
-1         // any non-zero number
Infinity   // truthy
-Infinity  // truthy
new Date() // truthy
```

#### The Array and Object Gotcha

```javascript
// Empty array — TRUTHY:
if ([]) console.log("truthy"); // prints "truthy"!

// But an empty array is "falsy-like" in loose comparison with false:
[] == false; // true!  (coercion: [] → "" → 0; false → 0; 0==0)

// This contradicts itself! [] is truthy but [] == false is true
// This is why == is dangerous — use === always
```

#### Practical Uses of Truthy/Falsy

```javascript
// Guard clause:
function greet(name) {
  if (!name) return "No name provided"; // catches null, undefined, ""
  return "Hello, " + name;
}

// Default values:
const displayName = username || "Guest";

// Conditional execution:
isLoggedIn && loadDashboard();

// Check array has items:
if (arr.length) { /* do something */ }

// Check object exists:
if (user) { console.log(user.name); }
```

#### Boolean() and Double-Negation !!

You can explicitly convert any value to its boolean equivalent:

```javascript
Boolean(0);          // false
Boolean("");         // false
Boolean(null);       // false
Boolean([]);         // true
Boolean({});         // true
Boolean("hello");    // true

// !! is a shorthand for Boolean():
!!0;        // false
!!"hello";  // true
!!null;     // false
!![];       // true
```

### Coding Questions

**Q1:** What does each `if` branch print?
```javascript
if (0) console.log("A");
else console.log("B");       // B (0 is falsy)

if ("") console.log("C");
else console.log("D");       // D ("" is falsy)

if ([]) console.log("E");
else console.log("F");       // E ([] is truthy!)

if (null) console.log("G");
else console.log("H");       // H (null is falsy)
```

---

**Q2:** What will these evaluate to?
```javascript
!!"0"    // ?
!![]     // ?
!!""     // ?
!!0      // ?
```

**Answer:** `true`, `true`, `false`, `false`

---

**Q3:** What's the bug here and how do you fix it?
```javascript
function setVolume(volume) {
  const v = volume || 50; // default to 50 if no volume
  audio.setVolume(v);
}
setVolume(0); // What happens?
```

**Answer:** When `volume` is `0`, it's falsy, so `v` becomes `50` — which is wrong! `0` volume (mute) is a valid setting. Fix: use `??` instead: `const v = volume ?? 50;`

---

## Summary — What Was Covered in Part 2

| Topic | Key Point |
|---|---|
| `==` vs `===` | `===` never coerces types. `==` has surprising coercion rules. Always use `===`. |
| Map | Key-value pairs with any type as key, maintains order, has `.size` |
| Set | Unique values only. Best use: remove duplicates `[...new Set(arr)]` |
| `find` / `findIndex` | Returns first match (element or index) |
| `some` / `every` | "Any?" vs "All?" — short-circuits early |
| `flat` / `flatMap` | Flatten nested arrays |
| `splice` vs `slice` | splice mutates (remove/insert), slice copies (non-mutating) |
| `sort` | Default sorts as strings — always pass comparator for numbers |
| `Array.from` | Create array from iterables; `Array.from({length:N}, fn)` |
| `structuredClone` | Modern deep copy. Beats JSON trick — handles Dates, Sets, Maps, circular refs |
| Memoization | Cache results of pure function calls. Generic implementation with Map. |
| Pure Functions | Same input → same output, no side effects. Foundation of React/Redux. |
| Short-circuit `&&` / `||` | `&&` stops at first falsy, `||` stops at first truthy. Return actual values. |
| Computed property names | `{ [key]: value }` — dynamic keys in object literals |
| Logical assignment `&&=` `\|\|=` `??=` | Assign only when condition is met. ES2021. |
| Truthy/Falsy | Only 8 falsy values. `[]` and `{}` are truthy. Always use `===`. |
