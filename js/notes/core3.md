# JavaScript Core Concepts - Part 3

> **Beginner-friendly study guide** covering Exception Handling, eval(), typeof vs instanceof, Object.create, Object.assign, and map/filter/reduce.

---

## Table of Contents
1. [Exception Handling (try / catch / throw)](#1-exception-handling-try--catch--throw)
2. [The eval() Function](#2-the-eval-function)
3. [Why eval() is Evil](#3-why-eval-is-evil)
4. [typeof vs instanceof](#4-typeof-vs-instanceof)
5. [Object.create()](#5-objectcreate)
6. [Object.assign()](#6-objectassign)
7. [map, filter & reduce](#7-map-filter--reduce)

---

## 1. Exception Handling (try / catch / throw)

### Interview Question
**"What is exception handling in JavaScript and how do try, catch, and throw work?"**

### Answer (Conversational)

Think of an exception as an **unexpected error** in your program — something that goes wrong at runtime. Without handling it, your program just **crashes** — Node crashes, the browser freezes, and all code after the error is dead.

Imagine you're writing code for a spacecraft's life-support system. 99.9% of the time it works fine — but that 0.1% crash could be catastrophic. That's why we need exception handling.

#### The `throw` keyword — Creating an Error

You can **throw** (create and signal) an error yourself using the `throw` keyword:

```javascript
throw "Something went wrong"; // throws a string (less ideal)
throw new Error("Something went wrong"); // throws an Error object (recommended)
```

When you throw something, JavaScript immediately **stops executing** and looks for somewhere to catch it. If nothing catches it, the program crashes.

```javascript
throw new Error("Bad thing happened");
console.log("This will NEVER run"); // unreachable code!
```

#### The `try` block — Wrapping Risky Code

Surround code that *might* throw an error with a `try` block:

```javascript
try {
  // risky code goes here
}
```

#### The `catch` block — Catching the Error

Follow `try` with a `catch` block. If anything in `try` throws, catch it here:

```javascript
try {
  throw new Error("Boom!");
  console.log("This won't run"); // skipped after throw
} catch (error) {
  console.error(error); // Error: Boom!
}
console.log("This WILL run"); // program continues after try/catch
```

The `catch` block works like a function — `error` (or `err`, or `e`) is a **parameter** that receives whatever was thrown.

#### Different Types of Errors

JavaScript has built-in error types:

```javascript
new Error("generic message")      // generic Error
new TypeError("wrong type")       // TypeError
new SyntaxError("bad syntax")     // SyntaxError
new RangeError("out of range")    // RangeError
new ReferenceError("not defined") // ReferenceError
```

#### Real-World Example: Accessing Nested Object Properties

```javascript
const user = { name: "Alex", age: 21 };

try {
  // user.profile doesn't exist — this will throw a TypeError!
  console.log(user.profile.color); // TypeError: Cannot read property 'color' of undefined

} catch (error) {
  console.error("Caught:", error); // gracefully handle it!
  console.error("Something bad happened");
}
console.log("Program continues..."); // still runs!
```

Without the `try/catch`, this TypeError would crash the whole program.

#### Code After an Error is Skipped

Inside a `try` block, code **after** a throw is never reached:

```javascript
try {
  const message = "Hello";
  console.log(message); // "Hello" — runs fine

  throw new Error("Boom!"); // error thrown here

  console.log("Do I get here?"); // NEVER runs — skipped!
} catch (error) {
  console.error(error); // logs "Error: Boom!"
}
```

#### Try-Catch Scoping (Important!)

Variables declared inside `try` are **not accessible** in `catch`:

```javascript
try {
  const message = "Hello"; // declared inside try block
  throw new Error("Uh oh");
} catch (error) {
  console.log(message); // ReferenceError: message is not defined!
}
```

Why? Because `try { }` and `catch { }` are **different scopes** — variables declared with `const`/`let` inside one scope can't be seen from another.

**Fix:** Declare the variable *outside* both blocks:

```javascript
let message = "Hello"; // declared in outer scope

try {
  console.log(message); // works fine
  throw new Error("Uh oh");
} catch (error) {
  console.error(error);
  console.log(message); // also works — outer scope is visible everywhere
}
```

#### The `finally` Block (Brief Note)

There's also a `finally` block that **always runs**, whether or not an error occurred — useful for cleanup:

```javascript
try {
  // risky code
} catch (error) {
  // handle error
} finally {
  // always runs (cleanup code, close connections, etc.)
}
```

### Coding Questions

**Q1:** What gets printed to the console?
```javascript
try {
  console.log("A");
  throw new Error("oops");
  console.log("B");
} catch (e) {
  console.log("C");
}
console.log("D");
```

**Answer:** `A`, `C`, `D` — "B" is skipped because the error is thrown before it. After catch, program continues to "D".

---

**Q2:** Will the following code crash? What will happen?
```javascript
const person = { name: "Alice" };

try {
  console.log(person.address.city);
} catch (error) {
  console.log("Handled:", error.message);
}
console.log("Done");
```

**Answer:** No crash. Output: `Handled: Cannot read properties of undefined (reading 'city')`, then `Done`.

---

**Q3:** What's wrong with this code? How do you fix it?
```javascript
try {
  const name = "Bob";
} catch (e) {
  console.log(name); // problem here
}
```

**Answer:** `name` is declared inside the `try` block's scope and is not accessible in `catch`. Fix: declare `name` before the `try` block.

---

**Q4:** Write a function `safeDivide(a, b)` that throws an error if `b` is 0, and returns `a / b` otherwise.
```javascript
function safeDivide(a, b) {
  if (b === 0) {
    throw new Error("Cannot divide by zero");
  }
  return a / b;
}

try {
  console.log(safeDivide(10, 2)); // 5
  console.log(safeDivide(10, 0)); // throws
} catch (error) {
  console.error("Error:", error.message); // Error: Cannot divide by zero
}
```

---

## 2. The eval() Function

### Interview Question
**"What is eval() in JavaScript and what does it do?"**

### Answer (Conversational)

`eval()` is a built-in JavaScript function that takes a **string** and evaluates it as JavaScript code (or as a mathematical expression).

```javascript
eval("2 + 3"); // returns 5
eval("console.log('Hello')"); // executes the console.log!
```

#### Practical Example: Calculator

Imagine a calculator app where you let users type `23 + 54 + 16` into a text box, and you want to compute the result:

```javascript
const input = "23 + 54 + 16"; // user input (a string)
const result = eval(input);   // eval treats it as math
console.log(result);          // 93
```

#### The Security Problem

`eval()` doesn't just evaluate math — it **executes arbitrary JavaScript code**. If a user can type into your input box:

```javascript
eval("document.write('<h1>Hacked!</h1>')"); // replaces entire page!
```

Worse, they could run malicious code to steal data, delete data, or crash your app.

**Best practice:** Never pass user-controlled input to `eval()`.

---

## 3. Why eval() is Evil

### Interview Question
**"Why is eval() considered bad practice in JavaScript? When should you avoid it?"**

### Answer (Conversational)

`eval()` has three main problems: **performance**, **security**, and **debugging difficulty**.

#### 1. Performance Problem

JavaScript engines like V8 use a **Just-In-Time (JIT) compiler** — they compile your code before running it to make it faster.

The problem with `eval()` is that the JIT compiler **doesn't know what the string contains** until the code actually runs. So JavaScript can't optimize it ahead of time. It has to include and process that string at runtime, which is **very expensive**.

#### 2. Security / Vulnerability Problem

When user input can reach `eval()`, users can:
- Crash your program
- **Steal your data**
- **Destroy your data**
- Execute any JavaScript they want on your page

There are packages like `safe-eval`, but they're **still not recommended in production**.

#### 3. Debugging Problem

Code that's dynamically generated (by `eval()`) is extremely hard to debug. Imagine trying to find a bug in code that changes based on 20 different factors — you can't even see what code ran!

#### Conclusion

> `eval()` does more harm than good if not used correctly. There are legitimate use cases (low-level interpreters, certain command-line tools), but in most applications: **just avoid it**.

### Coding Questions

**Q1:** What does `eval("typeof 42")` return?

**Answer:** `"number"` — eval runs `typeof 42` which returns the string `"number"`.

---

**Q2:** Why is the following code dangerous?
```javascript
const userInput = prompt("Enter something:");
eval(userInput);
```

**Answer:** The user can enter ANY JavaScript code. For example, `document.location = 'http://evil.com'` or `localStorage.clear()`. Never eval user input.

---

## 4. typeof vs instanceof

### Interview Question
**"What is the difference between typeof and instanceof in JavaScript? When should you use each?"**

### Answer (Conversational)

Both `typeof` and `instanceof` are used to **check the type of a value**, but they work very differently.

#### `typeof` — Unary Operator, Returns a String

`typeof` is a **unary operator** (needs only one operand). It returns a **string** describing the type:

```javascript
typeof "hello"       // "string"
typeof 42            // "number"
typeof true          // "boolean"
typeof undefined     // "undefined"
typeof null          // "object"  ← famous JavaScript bug!
typeof {}            // "object"
typeof []            // "object"  ← arrays are objects
typeof function(){}  // "function"
typeof Symbol()      // "symbol"
```

> **Important bug:** `typeof null === "object"` is a well-known quirk in JavaScript. `null` is a primitive value, not an object, but `typeof null` still returns `"object"` due to a historical bug in the language.

#### `instanceof` — Binary Operator, Returns Boolean

`instanceof` is a **binary operator** (needs two operands). It checks whether an object was created by a specific constructor:

```javascript
// Syntax:   object instanceof Constructor
new Date() instanceof Date         // true
new Date() instanceof Object       // true (all non-primitives are instances of Object)
[] instanceof Array                // true
[] instanceof Object               // true
```

It returns **true or false** — not a string.

#### Key Differences

| Feature | `typeof` | `instanceof` |
|---|---|---|
| Type | Unary operator | Binary operator |
| Returns | A string | Boolean (true/false) |
| Works on | Anything | Non-primitive objects only |
| Primitives | Works correctly | Does NOT work correctly |
| Null | Returns "object" (bug) | Returns false |

#### typeof with Primitives vs Objects

This is a crucial difference:

```javascript
const a = "hello";           // primitive string
const b = new String("hello"); // String object

typeof a;  // "string"
typeof b;  // "object"

a instanceof String; // false — 'a' is a primitive, not an object
b instanceof String; // true  — 'b' was created with String constructor
```

#### instanceof Doesn't Work for Primitive Types

```javascript
"hello" instanceof String; // false — primitives are not instances
42 instanceof Number;       // false
```

Use `typeof` for primitive type checking. Use `instanceof` for object/class type checking.

#### All Non-Primitive Objects are instanceof Object

```javascript
const date = new Date();
date instanceof Date;    // true
date instanceof Object;  // true — Object is the parent of all non-primitives

const b = new String("hello");
b instanceof Object;     // true
```

#### Tricky Interview Question: `typeof typeof 10`

```javascript
typeof 10       // → "number" (a string)
typeof "number" // → "string"

typeof typeof 10 // → "string"
```

Why? `typeof 10` returns the string `"number"`. Then `typeof "number"` returns `"string"`.

#### Combining Both: A Utility Function

If you want to check if something is a string — whether it's a **primitive** or a **String object** — combine both:

```javascript
function isString(value) {
  return typeof value === "string" || value instanceof String;
}

isString("hello");           // true  (typeof catches this)
isString(new String("hi"));  // true  (instanceof catches this)
isString(42);                // false
```

#### When to Use Which?

- Use `typeof` when checking for **primitive types**: string, number, boolean, undefined, function
- Use `instanceof` when checking if something was created from a **constructor function or class**

### Coding Questions

**Q1:** What does each of these return?
```javascript
typeof 42
typeof "hi"
typeof true
typeof undefined
typeof null
typeof {}
typeof []
typeof function(){}
```

**Answer:** `"number"`, `"string"`, `"boolean"`, `"undefined"`, `"object"`, `"object"`, `"object"`, `"function"`

---

**Q2:** What is the result of each?
```javascript
[] instanceof Array
[] instanceof Object
"hello" instanceof String
new String("hello") instanceof String
```

**Answer:** `true`, `true`, `false`, `true`

---

**Q3:** What does `typeof typeof undefined` return?

**Answer:** `"string"` — because `typeof undefined` returns the string `"undefined"`, and then `typeof "undefined"` returns `"string"`.

---

**Q4:** Write a function `isArray(value)` that returns true only if the value is an array.
```javascript
function isArray(value) {
  return value instanceof Array;
  // or Array.isArray(value) — even better!
}
```

---

## 5. Object.create()

### Interview Question
**"What is Object.create()? Why does it exist and how does it work?"**

### Answer (Conversational)

`Object.create()` is a **static method on the Object prototype** that creates a new object with its **prototype set to a specific object** you provide.

#### How it Works

```javascript
const cat = {
  makeSound: function() {
    console.log(this.sound);
  }
};

const mark = Object.create(cat); // mark's prototype is cat
mark.sound = "Meow";
mark.makeSound(); // "Meow"

// Verify the prototype relationship:
cat.isPrototypeOf(mark); // true
```

`mark` doesn't have `makeSound` defined on itself. When you call `mark.makeSound()`, JavaScript **delegates up the prototype chain** to `cat` and finds it there.

> **Important:** Objects created with `Object.create(cat)` are NOT copies of `cat`. They simply have a **reference** to `cat` as their prototype. Method calls are delegated up the chain.

#### Why Does Object.create() Exist?

Before `Object.create()`, the common way to create objects was using constructor functions with `new`. But that approach tried to simulate classes in a language that doesn't naturally have them, which led to weird quirks.

**Douglas Crockford** — the "grumpy grandfather of JavaScript," author of *JavaScript: The Good Parts*, and creator of JSLint — was frustrated with this. He invented `Object.create()` as a way to create objects that **directly expressed how JavaScript's prototype model actually works**, without pretending to be something it isn't (a class system).

He was influential enough that `Object.create()` was added directly into the JavaScript language itself.

#### Why NOT to Use setPrototypeOf

You might wonder: why not just do this?
```javascript
const mark = {};
Object.setPrototypeOf(mark, cat); // set prototype directly
```

**Don't do this in real applications.** Setting the prototype on an existing object is **terrible for performance** — it makes it very hard for JavaScript engines like V8 to optimize your code. This defeats the entire purpose of using prototypes!

`Object.create()` is better because it sets the prototype **at creation time**, which the engine can optimize.

#### How Object.create() Works Internally

You can think of it like this:
```javascript
function myObjectCreate(proto) {
  const obj = {};                    // 1. create empty object
  Object.setPrototypeOf(obj, proto); // 2. set its prototype
  return obj;                        // 3. return it
}
```

#### The `init` Pattern (Replacing a Constructor)

With `Object.create()`, there's no constructor. Instead, use an **`init` method**:

```javascript
const cat = {
  init: function(sound) {
    this.sound = sound;
    return this; // return 'this' to allow chaining
  },
  makeSound: function() {
    console.log(this.sound);
  }
};

const mark = Object.create(cat).init("Meow");
const waffles = Object.create(cat).init("Purr");

mark.makeSound();    // "Meow"
waffles.makeSound(); // "Purr"
```

`init` acts like a constructor — it sets up the object's own properties. Returning `this` lets you **chain the call** directly: `Object.create(cat).init("Meow")`.

#### Benefits

- More **natural** to JavaScript's prototype model
- Great **CPU and memory** characteristics (engines can optimize it)
- Clear and straightforward once you understand prototypes

### Coding Questions

**Q1:** What will this output?
```javascript
const animal = {
  speak: function() {
    console.log(this.name + " says " + this.sound);
  }
};

const dog = Object.create(animal);
dog.name = "Rex";
dog.sound = "Woof";
dog.speak();
```

**Answer:** `Rex says Woof` — `speak` is found on `animal` (dog's prototype) via the prototype chain.

---

**Q2:** What is the difference between these two approaches?
```javascript
// A:
const dog = Object.create(animal);

// B:
const dog = {};
Object.setPrototypeOf(dog, animal);
```

**Answer:** Both produce the same result in terms of prototype setup, but **B is bad practice** because modifying the prototype of an existing object has severe performance implications (the JS engine can't optimize it). Always prefer approach A.

---

**Q3:** Create a `vehicle` prototype object with a method `describe()`. Then create a `car` object using `Object.create`, add an `init` method that sets `brand` and `speed`, and call describe which logs `"Brand: X, Speed: Y"`.

```javascript
const vehicle = {
  init: function(brand, speed) {
    this.brand = brand;
    this.speed = speed;
    return this;
  },
  describe: function() {
    console.log("Brand: " + this.brand + ", Speed: " + this.speed);
  }
};

const car = Object.create(vehicle).init("Toyota", 120);
car.describe(); // "Brand: Toyota, Speed: 120"
```

---

## 6. Object.assign()

### Interview Question
**"What does Object.assign() do? What are its use cases?"**

### Answer (Conversational)

`Object.assign()` **merges (combines) two or more objects** into one. It copies all properties from the source objects into a target object.

#### Basic Merging

```javascript
const course = { name: "Web Programming" };
const grade  = { score: 92 };

const result = Object.assign(course, grade);
console.log(result); // { name: "Web Programming", score: 92 }
```

You can merge **more than two objects**:

```javascript
const teacher = { teacher: "Mrs. Waters" };
const finalResult = Object.assign(course, grade, teacher);
// { name: "Web Programming", score: 92, teacher: "Mrs. Waters" }
```

#### Conflict Resolution: Last Argument Wins

If two objects have the **same property name**, the **last one wins** (it overrides the earlier ones):

```javascript
const obj1 = { score: 75 };
const obj2 = { score: 92 };

const merged = Object.assign(obj1, obj2);
console.log(merged.score); // 92 — obj2's value wins (it was last)
```

Think of it as: later sources **overwrite** earlier targets.

#### Cloning an Object (Shallow Copy)

Pass an **empty object** as the first argument to create a clean copy:

```javascript
const original = { name: "Alice", age: 30 };
const copy = Object.assign({}, original);

console.log(copy); // { name: "Alice", age: 30 }
// copy is a NEW object, not the same reference as original
```

> **Note:** This is a **shallow copy** — it works perfectly for flat objects. Nested objects are still shared by reference.

#### Practical Use Case: Default Parameters in Functions

This is a very common pattern — use `Object.assign()` to merge user-provided options with defaults:

```javascript
function printName(options) {
  const defaults = { firstName: "Steve", lastName: "Bottle" };
  // options overrides defaults (last argument wins)
  options = Object.assign(defaults, options);
  console.log(options.firstName + " " + options.lastName);
}

printName({ firstName: "Dom" });           // "Dom Bottle" (lastName uses default)
printName({ firstName: "Dom", lastName: "Decode" }); // "Dom Decode" (both provided)
printName();                               // "Steve Bottle" (all defaults)
```

When you call `printName({ firstName: "Dom" })`, the `firstName` in `options` overrides the default, but `lastName` falls back to `"Bottle"` from defaults.

### Coding Questions

**Q1:** What is the output?
```javascript
const a = { x: 1, y: 2 };
const b = { y: 99, z: 3 };

const result = Object.assign(a, b);
console.log(result);
```

**Answer:** `{ x: 1, y: 99, z: 3 }` — `b`'s `y: 99` overrides `a`'s `y: 2` because `b` is the later argument.

---

**Q2:** How do you create a shallow copy of an object using Object.assign?

```javascript
const original = { name: "Bob", age: 25 };
const copy = Object.assign({}, original);
// copy is a new object, changing copy won't affect original
```

---

**Q3:** Create a function `createConfig(userConfig)` that merges user config with defaults:
```javascript
function createConfig(userConfig) {
  const defaults = {
    theme: "light",
    language: "en",
    fontSize: 14
  };
  return Object.assign(defaults, userConfig);
}

console.log(createConfig({ theme: "dark" }));
// { theme: "dark", language: "en", fontSize: 14 }
```

---

## 7. map, filter & reduce

### Interview Question
**"Explain map, filter, and reduce in JavaScript. What are they used for? Give examples."**

### Answer (Conversational)

`map`, `filter`, and `reduce` are **higher-order functions** (functions that accept another function as an argument). They are methods on arrays and represent the **functional programming** style in JavaScript.

#### map() — Transform Every Element

`map()` is used when you want to **transform each element** of an array and get a **new array** of the same length.

Think of it as: "Apply this transformation to every item and give me back the transformed list."

```javascript
const arr = [5, 1, 3, 2, 6];

// Double every value
const doubled = arr.map(x => x * 2);
console.log(doubled); // [10, 2, 6, 4, 12]

// Triple every value
const tripled = arr.map(x => x * 3);
console.log(tripled); // [15, 3, 9, 6, 18]

// Convert to binary
const binary = arr.map(x => x.toString(2));
console.log(binary); // ["101", "1", "11", "10", "110"]
```

You can also pass a named function:
```javascript
function double(x) { return x * 2; }
const result = arr.map(double); // same result
```

Or an anonymous function:
```javascript
const result = arr.map(function(x) { return x * 2; }); // same result
```

All three styles are equivalent. Arrow functions are most commonly used.

#### filter() — Keep Only Matching Elements

`filter()` is used when you want to **keep only certain elements** based on a condition. It returns a **new array** with only the elements where your function returned **truthy**.

Think of it as: "Give me only the items that pass this test."

```javascript
const arr = [5, 1, 3, 2, 6];

// Keep only odd values
const odds = arr.filter(x => x % 2 !== 0);
console.log(odds); // [5, 1, 3]

// Keep only even values
const evens = arr.filter(x => x % 2 === 0);
console.log(evens); // [2, 6]

// Keep only values greater than 4
const bigNums = arr.filter(x => x > 4);
console.log(bigNums); // [5, 6]

// Keep only values less than 3
const smallNums = arr.filter(x => x < 3);
console.log(smallNums); // [1, 2]
```

For each element, if your function returns `true` (or any truthy value), the element is **kept**. If it returns `false` (or falsy), it's **excluded**.

#### reduce() — Reduce Array to a Single Value

`reduce()` is the most powerful (and confusing at first). Use it when you need to **iterate over all elements and produce a single output value** — like a sum, a maximum, or a count.

It takes:
1. A **callback function** with two parameters:
   - `accumulator` (or `acc`) — carries the running result
   - `current` (or `curr`) — the current element being processed
2. An **initial value** for the accumulator

Think of the accumulator like a "running tally" that gets updated on each element.

```javascript
const arr = [5, 1, 3, 2, 6];

// Find the sum
const sum = arr.reduce((acc, curr) => acc + curr, 0);
console.log(sum); // 17
// Step-by-step: 0+5=5, 5+1=6, 6+3=9, 9+2=11, 11+6=17

// Find the maximum
const max = arr.reduce((acc, curr) => curr > acc ? curr : acc, 0);
console.log(max); // 6
// Step-by-step: max(0,5)=5, max(5,1)=5, max(5,3)=5, max(5,2)=5, max(5,6)=6
```

Compare with the traditional imperative approach:
```javascript
// Traditional (imperative) way to find sum:
function findSum(arr) {
  let sum = 0;
  for (let i = 0; i < arr.length; i++) {
    sum = sum + arr[i];
  }
  return sum;
}
```

In `reduce`:
- `sum` (the variable) → `accumulator`
- `arr[i]` (loop value) → `current`
- `let sum = 0` (initialization) → **initial value** (second arg to reduce)

#### Real-World Example: Array of User Objects

Most real API responses are arrays of objects, not just numbers. Here's where these functions really shine:

```javascript
const users = [
  { firstName: "Ali",    lastName: "Connell",  age: 16 },
  { firstName: "Donald", lastName: "Trump",    age: 75 },
  { firstName: "Ben",    lastName: "Affleck",  age: 58 },
  { firstName: "Elon",   lastName: "Musk",     age: 50 }
];

// map — Get full names of all users
const fullNames = users.map(user => user.firstName + " " + user.lastName);
console.log(fullNames);
// ["Ali Connell", "Donald Trump", "Ben Affleck", "Elon Musk"]

// filter — Get users under age 60
const youngUsers = users.filter(user => user.age < 60);
console.log(youngUsers);
// [{ firstName: "Ali", ... }, { firstName: "Ben", ... }, { firstName: "Elon", ... }]

// reduce — Calculate average age
const totalAge = users.reduce((acc, user) => acc + user.age, 0);
const avgAge = totalAge / users.length;
console.log(avgAge); // (16 + 75 + 58 + 50) / 4 = 49.75
```

#### Chaining: map + filter + reduce Together

These functions can be **chained**:

```javascript
// Get the sum of ages of all users under 60
const sumOfYoungAges = users
  .filter(user => user.age < 60)
  .map(user => user.age)
  .reduce((acc, age) => acc + age, 0);

console.log(sumOfYoungAges); // 16 + 58 + 50 = 124
```

#### Quick Reference

| Method | Purpose | Returns |
|---|---|---|
| `map()` | Transform each element | New array (same length) |
| `filter()` | Keep elements that match condition | New array (shorter or same) |
| `reduce()` | Combine all elements into one value | Single value |

### Coding Questions

**Q1:** Use `map` to square each number in this array: `[1, 2, 3, 4, 5]`
```javascript
const numbers = [1, 2, 3, 4, 5];
const squared = numbers.map(x => x * x);
console.log(squared); // [1, 4, 9, 16, 25]
```

---

**Q2:** Use `filter` to get only strings longer than 3 characters: `["hi", "hello", "hey", "howdy", "yo"]`
```javascript
const words = ["hi", "hello", "hey", "howdy", "yo"];
const longWords = words.filter(word => word.length > 3);
console.log(longWords); // ["hello", "howdy"]
```

---

**Q3:** Use `reduce` to find the sum of all numbers: `[10, 20, 30, 40]`
```javascript
const nums = [10, 20, 30, 40];
const sum = nums.reduce((acc, curr) => acc + curr, 0);
console.log(sum); // 100
```

---

**Q4:** Given this array of products, use `map` to get just the names and `filter` to get products cheaper than $50:
```javascript
const products = [
  { name: "Shirt", price: 25 },
  { name: "Jeans", price: 80 },
  { name: "Hat",   price: 15 },
  { name: "Shoes", price: 120 }
];

const names = products.map(p => p.name);
// ["Shirt", "Jeans", "Hat", "Shoes"]

const cheap = products.filter(p => p.price < 50);
// [{ name: "Shirt", price: 25 }, { name: "Hat", price: 15 }]
```

---

**Q5:** Use `reduce` to count how many users are older than 18:
```javascript
const users = [
  { name: "Alice", age: 15 },
  { name: "Bob",   age: 22 },
  { name: "Carol", age: 19 },
  { name: "Dave",  age: 16 }
];

const count = users.reduce((acc, user) => user.age > 18 ? acc + 1 : acc, 0);
console.log(count); // 2 (Bob and Carol)
```

---

## Summary Cheat Sheet

| Concept | Key Point |
|---|---|
| `throw` | Signals an error and stops current flow |
| `try` | Wraps risky code |
| `catch` | Catches any thrown error |
| `finally` | Always runs (cleanup) |
| Scope in try/catch | Variables in `try` are not visible in `catch` |
| `eval()` | Executes a string as JS code — avoid it! |
| `eval()` problems | Performance (no JIT), Security, Hard to debug |
| `typeof` | Unary, returns string, works on anything including primitives |
| `instanceof` | Binary, returns boolean, works on objects only |
| `typeof null` | Returns "object" — a historical bug |
| `Object.create(proto)` | Creates new object with given prototype |
| Douglas Crockford | Invented Object.create, author of JS: The Good Parts |
| `Object.assign(target, source)` | Merges source properties into target |
| Last argument wins | In Object.assign, later properties override earlier ones |
| `map()` | Transform all elements → new array |
| `filter()` | Keep elements passing condition → new array |
| `reduce(fn, initial)` | Accumulate all elements → single value |
