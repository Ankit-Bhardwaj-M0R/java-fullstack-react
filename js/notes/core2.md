# JavaScript Core Concepts - Study Guide (core2)

> **Topics covered:** Prototypes & Prototypal Inheritance · Factory Functions · Constructor Functions · IIFE · Strict Mode · Value vs Reference Types · Type Conversion & Coercion · Event Loop & Web APIs · JS Engine Architecture (V8) · Memory Management · Garbage Collection

---

## 1. Prototype and Prototypal Inheritance

### Interview Question
**"What is a Prototype in JavaScript? How does Prototypal Inheritance work?"**

### Answer (Conversational)

Every JavaScript object, function, and array has a hidden link to another object called its **prototype**. This prototype object contains built-in properties and methods that the original object can use for free.

**A real example:** When you create an array:
```javascript
let arr = [1, 2, 3];
arr.push(4); // How does arr know about .push()?
arr.length;  // How does arr know about .length?
```
You never defined `push` or `length` — so where do they come from? They live on `Array.prototype`, and your `arr` has a hidden link to it!

**The `__proto__` property:**
Every object has a special property `__proto__` that points to its prototype:
```javascript
let obj = {};
console.log(obj.__proto__); // Object.prototype — has toString, hasOwnProperty etc.
```

**The Prototype Chain:**
When you try to access a property, JavaScript searches in this order:
1. The object itself
2. Its `__proto__` (prototype)
3. The prototype's `__proto__`
4. ... continues up the chain
5. Until it reaches `null` — the chain ends here

```
myObj → Object.prototype → null
myArr → Array.prototype → Object.prototype → null
myFn  → Function.prototype → Object.prototype → null
```

**What is Prototypal Inheritance?**
It's a way to share methods between objects without duplicating code. Instead of defining the same method in every object, you put it on a prototype and all objects in the chain can access it.

```javascript
// Without prototypal inheritance (BAD — code duplication):
var person1 = {
  name: "Alice",
  greet: function() { console.log("Hi, I'm " + this.name); }
};
var person2 = {
  name: "Bob",
  greet: function() { console.log("Hi, I'm " + this.name); } // duplicated!
};

// With prototypal inheritance (GOOD — shared method):
var personProto = {
  greet: function() { console.log("Hi, I'm " + this.name); }
};
var person1 = { name: "Alice" };
var person2 = { name: "Bob" };

person1.__proto__ = personProto;
person2.__proto__ = personProto;

person1.greet(); // "Hi, I'm Alice" — found in prototype
person2.greet(); // "Hi, I'm Bob" — same method, different object
```

**Method Borrowing:**
You can set one object's `__proto__` to another object so it can "borrow" its methods:
```javascript
var objectB = {
  sayHello: function() { console.log("Hello!"); }
};
var objectA = { name: "A" };
objectA.__proto__ = objectB;
objectA.sayHello(); // "Hello!" — borrowed from objectB
```

**Key difference: `prototype` vs `__proto__`**
- `prototype` → property of a **constructor function** (used when creating new objects with `new`)
- `__proto__` → property of an **object instance** that points to its prototype

### Coding Questions

**Q1: What will this print?**
```javascript
var animal = {
  makeSound: function() { console.log("Generic sound"); }
};
var dog = { name: "Rex" };
dog.__proto__ = animal;
dog.makeSound(); // ?
```
**Answer:** `"Generic sound"` — `dog` doesn't have `makeSound`, so JS looks up the prototype chain and finds it in `animal`.

---

**Q2: Where does `toString()` come from?**
```javascript
var x = 42;
console.log(x.toString()); // "42"
```
**Answer:** From `Number.prototype.toString()`. The number `x` → `Number.prototype` → `Object.prototype`. `toString()` lives on `Object.prototype`.

---

**Q3: Check if a property is the object's own or inherited:**
```javascript
var parent = { shared: true };
var child = { own: true };
child.__proto__ = parent;

console.log(child.hasOwnProperty("own"));    // ?
console.log(child.hasOwnProperty("shared")); // ?
```
**Answer:** `true`, then `false`. `own` belongs directly to `child`; `shared` is inherited from `parent`.

---

## 2. Factory Functions

### Interview Question
**"What is a Factory Function and why would you use one?"**

### Answer (Conversational)

Imagine you want to create multiple circle objects. The naive way is:

```javascript
var circle1 = {
  radius: 1,
  draw: function() { console.log("Drawing circle with radius 1"); }
};
var circle2 = {
  radius: 2,
  draw: function() { console.log("Drawing circle with radius 2"); }
};
```

The problem? You've **duplicated the `draw` method** in every object. If there's a bug in `draw`, you have to fix it in multiple places. If you had 10 circles, that's 10 copies of the same code. Terrible!

**The Factory Function solution:**
A factory function is a regular function that **produces objects** — like a factory producing products. You define the logic once, and call the function with different values to get different objects:

```javascript
function createCircle(radius) {
  return {
    radius,        // shorthand for radius: radius (ES6 feature)
    draw() {       // shorthand for draw: function() {}
      console.log("Drawing circle with radius " + radius);
    }
  };
}

var circle1 = createCircle(1);
var circle2 = createCircle(2);

circle1.draw(); // "Drawing circle with radius 1"
circle2.draw(); // "Drawing circle with radius 2"
```

Now `draw` is defined in ONE place. Bug? Fix it once, all circles benefit!

**Naming convention for factory functions:** `camelCase` (e.g., `createCircle`, `makeUser`)

**The shorthand syntax for object methods:**
```javascript
// Old way:
{ draw: function() { ... } }

// New way (ES6 shorthand — drop the 'function' keyword):
{ draw() { ... } }
```

### Coding Questions

**Q1: Create a factory function for a person:**
```javascript
function createPerson(name, age) {
  return {
    name,
    age,
    greet() {
      console.log("Hi, I'm " + this.name + " and I'm " + this.age);
    }
  };
}
var person1 = createPerson("Alice", 25);
var person2 = createPerson("Bob", 30);
person1.greet(); // "Hi, I'm Alice and I'm 25"
person2.greet(); // "Hi, I'm Bob and I'm 30"
```

---

**Q2: What's the advantage of factory functions over object literals?**
**Answer:** No code duplication. Methods are defined once and shared through the factory, so bug fixes need to happen in only one place. Objects can be created with different values (parameters) but the same structure.

---

## 3. Constructor Functions

### Interview Question
**"What is a Constructor Function? How is it different from a Factory Function?"**

### Answer (Conversational)

A Constructor Function is another pattern for creating objects. The key difference is in how it's called — using the `new` keyword.

**Naming convention for constructor functions:** `PascalCase` (first letter of every word is uppercase — e.g., `Circle`, `Person`, `Car`). This is the convention used in Java and C# too, which is why it feels familiar to developers from those backgrounds.

```javascript
function Circle(radius) {
  this.radius = radius;
  this.draw = function() {
    console.log("Drawing circle with radius " + this.radius);
  };
}

var circle1 = new Circle(1);
var circle2 = new Circle(2);

circle1.draw(); // "Drawing circle with radius 1"
circle2.draw(); // "Drawing circle with radius 2"
```

**What happens when you use `new`? (Three things):**
1. A new **empty object** is created automatically: `{}`
2. `this` is set to point to that new empty object
3. At the end of the function, `this` (the new object) is **returned automatically**

So you don't need a `return` statement — it happens under the hood!

**Factory vs Constructor — Side by Side:**
```javascript
// Factory Function
function createCircle(radius) {
  return { radius, draw() { ... } }; // explicit return
}
var c = createCircle(5); // no 'new'

// Constructor Function
function Circle(radius) {
  this.radius = radius;
  this.draw = function() { ... }; // no explicit return
}
var c = new Circle(5); // use 'new'
```

**Which is better?** Neither is universally "better" — both produce the same result. Use what your team/project prefers. The constructor style is familiar to Java/C# developers; the factory style is preferred by some for its simplicity.

### Coding Questions

**Q1: Create a `Car` constructor:**
```javascript
function Car(brand, speed) {
  this.brand = brand;
  this.speed = speed;
  this.describe = function() {
    console.log(this.brand + " goes " + this.speed + "km/h");
  };
}
var car1 = new Car("Toyota", 120);
var car2 = new Car("Honda", 100);
car1.describe(); // "Toyota goes 120km/h"
```

---

**Q2: What happens if you forget `new`?**
```javascript
function Person(name) {
  this.name = name;
}
var p = Person("Alice"); // No 'new'!
console.log(p);          // ?
console.log(window.name); // ?
```
**Answer:** `p` is `undefined` (the function doesn't return anything). `window.name` is `"Alice"` — because without `new`, `this` inside the function refers to the global object, so you accidentally set a global variable! This is why the `PascalCase` naming convention exists — it's a visual reminder to use `new`.

---

## 4. IIFE — Immediately Invoked Function Expression

### Interview Question
**"What is an IIFE and why would you use it?"**

### Answer (Conversational)

IIFE stands for **Immediately Invoked Function Expression**. It's a function that is defined and called at the exact same moment — you don't need to call it separately.

**The syntax:**
```javascript
(function() {
  // code here
})(); // ← the () at the end immediately invokes it
```

Or with an arrow function:
```javascript
(() => {
  // code here
})();
```

**Why do we need it?**
Consider this problem: You have some initialization code that needs to run once when the page loads, and it uses a variable called `counter`. You also have a function that uses its own `counter` variable. If both are in global scope, they'll interfere with each other:

```javascript
// Problem: Global variable pollution
var counter = 10; // initialization counter
counter++;

function someFunction() {
  var counter = 0; // this counter should be separate!
  counter++;
  console.log(counter);
}
someFunction(); // Expected: 1, but counter might be 12 now!
```

**IIFE solves two problems:**

**Problem 1: Global Variable Pollution**
The IIFE creates its own scope. Variables inside it don't leak into the global scope:
```javascript
(function() {
  var counter = 10; // This counter is ONLY inside this IIFE
  counter++;
  console.log(counter); // 11
})();

function someFunction() {
  var counter = 0; // Completely separate counter — no conflict!
  counter++;
  console.log(counter); // 1 ✓
}
someFunction();
```

**Problem 2: Initialize Only Once**
Since the IIFE runs immediately when the page loads, and is never called again, it's perfect for one-time setup code. The initialization code runs exactly once.

```javascript
// Real-world example: Initializing a module
var App = (function() {
  var private_data = "secret"; // Cannot be accessed outside

  return {
    getData: function() { return private_data; }
  };
})();

App.getData(); // "secret"
// App.private_data — undefined! Hidden safely.
```

**The key insight:** All modern JavaScript frameworks (Angular, React, etc.) use JavaScript at their core. These frameworks themselves use patterns like IIFE, closures, hoisting, and prototypes internally. Understanding JavaScript fundamentals is understanding the foundation all frameworks are built on.

### Coding Questions

**Q1: Convert this to an IIFE:**
```javascript
// Original
function init() {
  var count = 0;
  count++;
  console.log("Initialized:", count);
}
init();

// As IIFE:
(function() {
  var count = 0;
  count++;
  console.log("Initialized:", count);
})();
```

---

**Q2: IIFE with parameters:**
```javascript
(function(name) {
  console.log("Hello, " + name + "!");
})("JavaScript");
// Output: "Hello, JavaScript!"
```

---

**Q3: Why is this useful in a loop?**
```javascript
// Classic closure problem — IIFE fix:
for (var i = 0; i < 3; i++) {
  (function(j) {
    setTimeout(function() {
      console.log(j); // 0, 1, 2 ✓
    }, 1000);
  })(i);
}
```

---

## 5. Strict Mode — `"use strict"`

### Interview Question
**"What is Strict Mode in JavaScript? Name at least 4 things it prevents."**

### Answer (Conversational)

Strict Mode is a way to make JavaScript more secure and help you catch bugs. It was added because early JavaScript had some... questionable design decisions, and strict mode fixes many of them. It literally adds a second, stricter mode to fix the language!

**How to enable it:**
```javascript
// For an entire file (put at the very top):
"use strict";
var x = 5;

// For a specific function only:
function myFn() {
  "use strict";
  // ...
}
```

**Automatic Strict Mode (you don't need to write it manually):**
- JavaScript **Classes** → automatically in strict mode
- JavaScript **Modules** (`type="module"` in HTML) → automatically in strict mode

---

**8 Things Strict Mode Prevents:**

**1. Accidental Global Variables (typos)**
```javascript
// Without strict mode:
myVariable = 10; // No error! Creates a global variable on window
// With strict mode:
"use strict";
myVariable = 10; // ReferenceError! — you didn't declare it with var/let/const
```

**2. Writing to Read-Only / Special Variables**
```javascript
"use strict";
undefined = 10;  // TypeError! — undefined is read-only
NaN = 5;        // TypeError! — NaN is read-only
```

**3. Modifying Read-Only Properties**
```javascript
"use strict";
var obj = {};
Object.defineProperty(obj, "readOnly", { value: 10, writable: false });
obj.readOnly = 15; // TypeError! — can't modify a read-only property
```

**4. Deleting Non-Deletable Properties**
```javascript
"use strict";
delete Object.prototype; // TypeError! — can't delete prototype
```

**5. Duplicate Parameter Names**
```javascript
"use strict";
function combine(a, a, c) { // SyntaxError! — duplicate parameter 'a'
  return a + a + c;
}
```
Without strict mode, the second `a` would silently overwrite the first — a very tricky bug!

**6. Octal Literals (leading zeros)**
```javascript
"use strict";
var x = 015; // SyntaxError! — 015 in octal = 13 in decimal (confusing!)
// To properly write octal: use 0o15
```
Without strict mode, a leading zero means "octal" — which means `015` is actually `13`, not `15`. Strict mode prevents this silent confusion.

**7. Setting Properties on Primitive Values**
```javascript
"use strict";
var str = "hello";
str.newProp = 5; // TypeError! — primitives can't have properties
```

**8. Functions: `this` is `undefined` Instead of Global Object**
```javascript
"use strict";
function show() {
  console.log(this); // undefined (NOT window)
}
show();
```
This is a **security feature** — it prevents accidental exposure of the global `window` object. You have to explicitly bind `this` if you need it:
```javascript
show.bind({ name: "MyObj" })(); // { name: "MyObj" }
```

**The `arguments` Object Behavior Change:**
```javascript
"use strict";
function sum(a, b) {
  a = 10; // Changing parameter locally
  console.log(a);           // 10
  console.log(arguments[0]); // 1 (original value — not affected by the change)
}
sum(1, 2);
// Without strict mode, arguments[0] would also become 10 (weird behavior!)
```

### Coding Questions

**Q1: Will this throw an error in strict mode?**
```javascript
"use strict";
x = 5;
console.log(x);
```
**Answer:** Yes, `ReferenceError: x is not defined`. In strict mode, you must declare variables with `var`, `let`, or `const`.

---

**Q2: What is printed here?**
```javascript
"use strict";
function test() {
  console.log(this);
}
test(); // ?
```
**Answer:** `undefined`. In strict mode, `this` inside a regular function call is `undefined` (not the `window` object).

---

**Q3: Fix this code for strict mode:**
```javascript
"use strict";
function greet(name, name) { // Error!
  return "Hello " + name;
}
```
**Answer:** Remove the duplicate parameter:
```javascript
"use strict";
function greet(firstName, lastName) {
  return "Hello " + firstName + " " + lastName;
}
```

---

## 6. Value Types vs Reference Types

### Interview Question
**"What is the difference between primitive (value) types and reference types in JavaScript? How are they copied?"**

### Answer (Conversational)

This is crucial to understand to avoid subtle bugs in your code. JavaScript has two categories of types:

**Value Types (Primitives):**
- `number`, `string`, `boolean`, `symbol`, `undefined`, `null`

**Reference Types (Objects):**
- Objects `{}`, Arrays `[]`, Functions

**The key difference is how they are stored and copied.**

#### Primitives — Copied by Value

```javascript
var x = 10;
var y = x; // y gets a COPY of 10
x = 20;    // changing x...

console.log(x); // 20
console.log(y); // 10 — y is NOT affected! It's an independent copy.
```

When you copy a primitive, you get an independent copy. Changing one does not affect the other. Think of it like copying a Word document — two separate files.

#### Reference Types — Copied by Reference

```javascript
var obj1 = { value: 10 };
var obj2 = obj1; // obj2 gets the REFERENCE (address) to the SAME object

obj1.value = 20;

console.log(obj1.value); // 20
console.log(obj2.value); // 20 — SAME object! Both variables point to the same place.
```

When you "copy" an object, you copy the **memory address** (reference), not the actual object. Both variables point to the same object in memory. Changing through one variable changes what the other sees.

**Visual representation:**
```
Primitives:
x → [10]   (value stored directly in variable)
y → [10]   (separate copy)

Reference types:
obj1 → [address: 0x001] → { value: 20 }  (in memory)
obj2 → [address: 0x001] → { value: 20 }  (same address!)
```

#### Primitives inside Functions

```javascript
function increase(number) {
  number++; // only changes LOCAL copy
}
var num = 10;
increase(num);
console.log(num); // Still 10! The function got a copy.
```

#### Reference Types inside Functions

```javascript
function modify(obj) {
  obj.value++; // modifies the ORIGINAL object
}
var myObj = { value: 10 };
modify(myObj);
console.log(myObj.value); // 11! The original was changed.
```

**Conclusion:**
- Primitives → **copied by value** (independent copies)
- Objects/Arrays/Functions → **copied by reference** (same object)

### Coding Questions

**Q1: What will this print?**
```javascript
var a = "hello";
var b = a;
b = "world";
console.log(a); // ?
console.log(b); // ?
```
**Answer:** `"hello"` and `"world"`. Strings are primitives — copied by value. Changing `b` doesn't affect `a`.

---

**Q2: What will this print?**
```javascript
var arr1 = [1, 2, 3];
var arr2 = arr1;
arr2.push(4);
console.log(arr1); // ?
console.log(arr2); // ?
```
**Answer:** Both print `[1, 2, 3, 4]`. Arrays are objects (reference types) — `arr2` just points to the same array as `arr1`.

---

**Q3: How do you create an independent copy of an object?**
```javascript
var original = { name: "Alice", age: 25 };

// Method 1: Spread operator (shallow copy)
var copy1 = { ...original };

// Method 2: Object.assign (shallow copy)
var copy2 = Object.assign({}, original);

copy1.name = "Bob";
console.log(original.name); // "Alice" — not affected ✓
```
**Warning:** Both are **shallow copies** — they only copy top-level properties. Nested objects are still shared by reference!

---

**Q4: Explain what "shallow copy" means:**
```javascript
var obj = { name: "Alice", address: { city: "Mumbai" } };
var shallowCopy = { ...obj };

shallowCopy.name = "Bob";           // Changes only in copy ✓
shallowCopy.address.city = "Delhi"; // Changes in BOTH! (nested object is still shared)

console.log(obj.name);         // "Alice" ✓
console.log(obj.address.city); // "Delhi" — shared reference!
```

---

## 7. Type Conversion & Type Coercion

### Interview Question
**"What is the difference between Type Conversion and Type Coercion in JavaScript?"**

### Answer (Conversational)

Regardless of which programming language you use, you work with data. Sometimes you need to convert that data from one type to another. In JavaScript, there are two ways this happens:

**Type Conversion (Explicit)** — YOU, the developer, intentionally convert the type:
```javascript
var num = 6;
var str = String(num);    // Convert number to string — explicit
var num2 = Number("123"); // Convert string to number — explicit
var bool = Boolean(0);    // Convert to boolean — explicit
```

**Type Coercion (Implicit)** — JavaScript automatically converts types for you, often unexpectedly:
```javascript
var result = "5" + 3; // "53" — JS coerces 3 to string!
var result2 = "5" - 3; // 2 — JS coerces "5" to number!
```

**Why two words?**
- **Conversion** = you did it on purpose
- **Coercion** = JavaScript did it automatically (sometimes surprisingly)

---

#### Number Conversions

```javascript
Number("42");      // 42
Number("42.5");    // 42.5
Number("");        // 0
Number("hello");   // NaN (Not a Number — can't convert)
Number(true);      // 1
Number(false);     // 0
Number(null);      // 0
Number(undefined); // NaN
```

---

#### String Conversions

```javascript
String(42);        // "42"
String(true);      // "true"
String(null);      // "null"
String(undefined); // "undefined"
(42).toString();   // "42"
```

---

#### Boolean Conversions (Truthy vs Falsy)

In JavaScript, every value is either **truthy** (behaves like `true`) or **falsy** (behaves like `false`).

**Falsy values** (only 6 of them!):
```javascript
Boolean(false);     // false
Boolean(0);         // false
Boolean(-0);        // false
Boolean("");        // false (empty string)
Boolean(null);      // false
Boolean(undefined); // false
Boolean(NaN);       // false
```

**Everything else is truthy:**
```javascript
Boolean("hello");  // true
Boolean(1);        // true
Boolean([]);       // true (empty array is truthy!)
Boolean({});       // true (empty object is truthy!)
Boolean("0");      // true (string "0" is truthy!)
```

---

#### Coercion Surprises (Common Interview Traps!)

```javascript
// + operator with string → concatenation (coerces number to string)
"5" + 3;   // "53"
"5" + true; // "5true"

// -, *, / → arithmetic (coerces string to number)
"5" - 3;  // 2
"5" * 2;  // 10
"5" / 2;  // 2.5

// Equality comparisons
null == undefined; // true (special rule)
null === undefined; // false (strict, no coercion)
"5" == 5;  // true (coercion happens!)
"5" === 5; // false (strict equality, no coercion)
```

**The `typeof` operator:**
```javascript
typeof 42;         // "number"
typeof "hello";    // "string"
typeof true;       // "boolean"
typeof undefined;  // "undefined"
typeof null;       // "object" ← famous JavaScript bug! null is NOT an object
typeof {};         // "object"
typeof [];         // "object"
typeof function(){}; // "function"
```

### Coding Questions

**Q1: What will this print?**
```javascript
console.log(1 + "2" + 3);  // ?
console.log(1 + 2 + "3");  // ?
```
**Answer:** `"123"` and `"33"`. In the first, `1 + "2"` = `"12"` (coercion to string), then `"12" + 3` = `"123"`. In the second, `1 + 2` = `3` (both numbers), then `3 + "3"` = `"33"`.

---

**Q2: Convert these explicitly:**
```javascript
// Convert the string "42" to a number, then check its type
var str = "42";
var num = Number(str);
console.log(num);       // 42
console.log(typeof num); // "number"
```

---

**Q3: Is this truthy or falsy?**
```javascript
if ([]) {
  console.log("truthy");
} else {
  console.log("falsy");
}
```
**Answer:** `"truthy"`. An empty array `[]` is a **truthy** value in JavaScript! This surprises many beginners.

---

**Q4: What is NaN? How do you check for it?**
```javascript
var result = Number("hello"); // NaN
console.log(result === NaN);  // false! NaN is never equal to anything, including itself
console.log(isNaN(result));   // true ✓ — use isNaN() to check
console.log(Number.isNaN(result)); // true ✓ — even better
```

---

## 8. Event Loop, Web APIs & Callback Queue

### Interview Question
**"How does JavaScript handle asynchronous operations if it's single-threaded?"**

### Answer (Conversational)

This is one of the most fascinating concepts in JavaScript. The question seems like a contradiction — JavaScript is single-threaded (does one thing at a time), but it can do things like fetch data from the network, wait for user clicks, run timers... all while still being responsive. How?

The answer is: JavaScript doesn't work alone. It works with the **Browser (or Node.js)**, which provides extra help through **Web APIs**.

Here's the full picture of how JavaScript actually works:

```
┌─────────────────────────────────────────────────────────────────┐
│  BROWSER / RUNTIME ENVIRONMENT                                   │
│                                                                  │
│  ┌──────────────────┐    ┌────────────────────────────────────┐  │
│  │  JavaScript       │    │  Web APIs (provided by browser)    │  │
│  │  Engine           │    │  - setTimeout / setInterval        │  │
│  │                   │    │  - fetch / XMLHttpRequest          │  │
│  │  ┌─────────────┐  │    │  - DOM events (click, scroll...)   │  │
│  │  │  Call Stack  │  │    │  - localStorage, geolocation      │  │
│  │  └─────────────┘  │    └────────────────────────────────────┘  │
│  │                   │           ↓                               │
│  │  ┌─────────────┐  │    ┌──────────────────────────────────┐   │
│  │  │   Heap      │  │    │  Callback Queue (Task Queue)      │   │
│  │  └─────────────┘  │    │  [setTimeout callbacks here]      │   │
│  └──────────────────┘    └──────────────────────────────────┘   │
│          ↑                              ↓                        │
│          │          Event Loop          │                        │
│          │   (checks if stack is empty) │                        │
│          └──────────────────────────────┘                        │
│                                                                  │
│          ┌──────────────────────────────────────────┐           │
│          │  Microtask Queue (Higher Priority!)        │           │
│          │  [Promise callbacks, MutationObserver]    │           │
│          └──────────────────────────────────────────┘           │
└─────────────────────────────────────────────────────────────────┘
```

**Step-by-step example:**
```javascript
console.log("Start");

setTimeout(function() {
  console.log("Timeout!");
}, 2000);

fetch("https://api.example.com/data")
  .then(function(res) {
    console.log("Fetch done!");
  });

console.log("End");
```

**What happens:**
1. `console.log("Start")` → runs immediately, prints "Start"
2. `setTimeout(...)` → the timer is registered with the **Web API**. JS doesn't wait — it moves on!
3. `fetch(...)` → the network request is handed to the **Web API**. JS moves on!
4. `console.log("End")` → runs immediately, prints "End"
5. After 2 seconds → setTimeout's callback goes to **Callback Queue**
6. When fetch completes → the `.then` callback goes to **Microtask Queue**
7. **Event Loop** sees the call stack is empty → checks **Microtask Queue first** → "Fetch done!" runs
8. Then checks **Callback Queue** → "Timeout!" runs

**Output:**
```
Start
End
Fetch done!
Timeout!
```

**The Event Loop's job:**
The Event Loop continuously asks: "Is the Call Stack empty?" If yes, it moves functions from the queues into the Call Stack to be executed. This is how JavaScript achieves asynchronous behavior while remaining single-threaded.

---

#### Microtask Queue vs Callback Queue

| | Microtask Queue | Callback Queue (Task Queue) |
|---|---|---|
| What goes in? | Promises, MutationObserver | setTimeout, setInterval, DOM events |
| Priority | **Higher** — runs first | Lower — runs after microtasks |
| Also called | Job Queue | Task Queue |

**Important:** The Event Loop always drains the entire Microtask Queue before picking from the Callback Queue. This can lead to **starvation** — if Microtasks keep creating more Microtasks, setTimeout callbacks might never run!

```javascript
console.log("1");
setTimeout(() => console.log("2"), 0);    // Goes to Callback Queue
Promise.resolve().then(() => console.log("3")); // Goes to Microtask Queue
console.log("4");

// Output: 1, 4, 3, 2
// Microtask ("3") runs before Callback ("2")!
```

### Coding Questions

**Q1: What is the output order?**
```javascript
console.log("A");
setTimeout(() => console.log("B"), 0);
console.log("C");
```
**Answer:** `A`, `C`, `B`. Even with 0 milliseconds, `setTimeout` goes through the Web API → Callback Queue, so it runs after all synchronous code.

---

**Q2: What happens here?**
```javascript
console.log("1");
setTimeout(() => console.log("2"), 0);
Promise.resolve().then(() => console.log("3"));
console.log("4");
```
**Answer:** `1`, `4`, `3`, `2`. Synchronous first → then Microtask Queue (promise) → then Callback Queue (setTimeout).

---

**Q3: What is starvation in the Event Loop?**
**Answer:** If the Microtask Queue keeps generating new microtasks (promises resolving and creating new promises), the Callback Queue items (like `setTimeout`) never get a chance to run. This is called "starvation of the task queue."

---

## 9. JavaScript Engine Architecture — Google's V8

### Interview Question
**"Is JavaScript interpreted or compiled? What happens inside the JS engine?"**

### Answer (Conversational)

This is a great interview question! The short answer: **JavaScript is Just-In-Time (JIT) compiled** — it uses BOTH interpretation AND compilation.

**First, what's the difference?**

| Interpreter | Compiler |
|---|---|
| Executes code line by line | Converts entire code to optimized output first |
| Starts executing fast | Takes time upfront to compile |
| Doesn't need to wait | Produces faster running code |

Early JavaScript was purely **interpreted** — that's how it was originally designed (quick scripts for browsers). But modern JavaScript engines are much smarter.

**Modern V8 (Google's JS engine — used in Chrome and Node.js) does JIT compilation:**

```
Your JavaScript Code
         ↓
    1. PARSING
       ├─ Tokenization (breaks code into tokens)
       └─ AST (Abstract Syntax Tree) — a tree structure representing your code
         ↓
    2. INTERPRETATION
       └─ Interpreter converts to Bytecode (runs line by line)
         ↓
    3. COMPILATION (in parallel, while interpreting!)
       └─ Compiler takes "hot" (frequently run) code
          and optimizes it into highly efficient machine code
         ↓
    Final: Optimized Machine Code runs on CPU
```

**JIT = Just-In-Time compilation:**
The compiler works *while* the interpreter is running. When it notices code being executed frequently ("hot code"), it optimizes it. This gives you the best of both worlds — fast startup (interpreter) and fast execution (compiler).

**What's inside the JS Engine?**
- **Call Stack** — manages execution contexts
- **Heap (Memory Heap)** — stores objects and variables dynamically
- **Garbage Collector** — automatically frees unused memory
- **Interpreter** — runs code line by line, creates bytecode
- **Optimizing Compiler** — makes frequently-used code faster

**The Runtime Environment:**
The JS Engine alone isn't enough. It runs inside a **Runtime Environment** — like a browser or Node.js. The runtime provides:
- The JS Engine (V8 in Chrome/Node.js, SpiderMonkey in Firefox, JavaScriptCore in Safari)
- Web APIs (browser) or Node.js APIs
- Event Loop & Queues

**ECMAScript (ES):**
ECMAScript is the standardization body that defines how JavaScript should work. All JavaScript engines must implement the ECMAScript specification. ES6 (2015), ES7, ES8, etc. are versions of this standard.

### Coding Questions

**Q1: What does the JS engine do when it first sees your code?**
**Answer:** It first **parses** the code — tokenizes it and creates an Abstract Syntax Tree (AST). Then the interpreter converts it to bytecode and starts executing line by line, while the JIT compiler simultaneously optimizes frequently executed sections.

---

**Q2: Why is V8 faster than early JavaScript interpreters?**
**Answer:** V8 uses JIT (Just-In-Time) compilation — it doesn't just interpret line by line. It identifies "hot" code paths and compiles them to optimized machine code, so they run much faster on subsequent calls.

---

## 10. Memory Management in JavaScript

### Interview Question
**"How does JavaScript manage memory? What are the Stack and Heap?"**

### Answer (Conversational)

Unlike languages like C and C++, you don't manually allocate and free memory in JavaScript. The JS engine handles all of that. But understanding how it works helps you write more efficient code.

**Two types of memory:**

#### Stack Memory
- Used for **primitive values** and **function call contexts**
- Fast and efficient
- Works like a stack (LIFO — last in, first out)
- Variables on the stack have a fixed size
- When a function returns, its local variables are automatically removed from the stack

```javascript
function calculate() {
  var x = 10;  // Goes on the stack
  var y = 20;  // Goes on the stack
  return x + y;
} // x and y are removed from stack when function returns
```

#### Heap Memory
- Used for **objects, arrays, functions** — anything dynamically sized
- More complex memory management
- Objects can grow and shrink (not fixed size)

```javascript
var obj = { name: "Alice", age: 25 }; // Object goes on the Heap
// A reference (pointer) to the heap location is stored on the stack
```

**How variables actually work:**
Most variables in JavaScript are really just **pointers** (addresses) pointing to where the actual data lives:
```javascript
var str1 = "hello";
var str2 = "hello"; // V8 uses "string interning" — both point to SAME location!
// No need to store "hello" twice
```

**V8's Smart Optimization — String Interning:**
If you create two variables with the same string value, V8 detects this and makes both point to the same memory location — saving space!

**Shallow Copy vs Deep Copy:**
```javascript
var a = { x: 1, nested: { y: 2 } };

// Shallow copy — top-level props copied, nested still shared:
var b = { ...a };
b.x = 99;          // Only b.x changes
b.nested.y = 99;   // Changes BOTH a.nested.y and b.nested.y!

// Deep copy — everything independent:
var c = JSON.parse(JSON.stringify(a)); // One way (but loses functions)
```

**Immutability:**
This "modification by reference" issue is why **immutability** (never modifying objects, always creating new ones) became popular in React/Redux. Libraries like Immutable.js ensure that when you "modify" an object, you always get a new object with the changes.

### Coding Questions

**Q1: What goes on the Stack vs Heap?**
```javascript
var num = 42;           // Stack (primitive)
var str = "hello";      // Stack (primitive reference) + Heap (string data)
var arr = [1, 2, 3];    // Stack (reference) + Heap (array data)
var obj = { a: 1 };     // Stack (reference) + Heap (object data)
function fn() { }       // Stack (reference) + Heap (function data)
```

---

**Q2: Will this create a bug?**
```javascript
function updateUser(user) {
  user.name = "Modified"; // modifies original!
}
var original = { name: "Alice" };
updateUser(original);
console.log(original.name); // ?
```
**Answer:** `"Modified"` — the function received the reference to the original object and mutated it. If you want to avoid this, pass a copy: `updateUser({ ...original })`.

---

## 11. Garbage Collection

### Interview Question
**"How does JavaScript garbage collection work? What are memory leaks?"**

### Answer (Conversational)

Since JavaScript manages memory automatically, you might wonder: how does it know when to free memory? This is **Garbage Collection (GC)**.

**The Basic Rule: Reachability**
JavaScript keeps objects in memory as long as they are **reachable** — meaning they can be accessed from your code through some chain of references. When an object becomes unreachable, it becomes "garbage" and its memory can be freed.

```javascript
var obj = { name: "Alice" }; // obj is reachable
obj = null; // Now the original object is unreachable → garbage!
// Eventually, the GC will free that memory
```

**When does GC automatically clean up?**
- Variables that go out of scope (inside a function that returned)
- Objects with no remaining references pointing to them

```javascript
function createData() {
  var local = { huge: "data" }; // allocated on heap
  return local.huge;
} // 'local' object is now unreachable → eligible for GC
```

**WeakMap and WeakSet — GC-Friendly Collections:**
A `Map` holds strong references — values stay in memory as long as the Map exists. A `WeakMap` holds **weak references** — if the key object becomes unreachable, GC can collect it even if it's still "in" the WeakMap.

```javascript
// Regular Map — memory leak risk!
var cache = new Map();
setInterval(function() {
  var obj = { i: Date.now() };
  cache.set(obj, "value"); // obj stays in memory forever (Map holds reference)
}, 1);

// WeakMap — no leak!
var safeCache = new WeakMap();
setInterval(function() {
  var obj = { i: Date.now() };
  safeCache.set(obj, "value"); // obj is GC'd when it goes out of scope
}, 1);
```

**V8's Two Garbage Collectors:**

**Minor GC (Scavenger) — for Young Generation:**
- New objects are allocated in the "Young Generation" (1–8 MB)
- When it fills up, GC runs: live objects are copied to "to-space", dead objects are discarded
- Fast, but memory-hungry (needs twice the space)
- Objects that survive multiple GC cycles get promoted to Old Generation

**Major GC (Mark-Sweep-Compact) — for Old Generation:**
- Takes up most of the heap
- Uses Mark-Sweep-Compact algorithm:
  1. **Mark:** Traverse all references from root, mark reachable objects
  2. **Sweep:** Mark unreachable objects as free space
  3. **Compact:** Move live objects together to eliminate fragmentation
- Runs concurrently and in parallel to minimize pauses

**Memory Leaks — Common Causes:**
1. **Forgotten timers/intervals:** `setInterval` callbacks keep running and holding references
2. **Global variables:** Variables on `window` are never collected
3. **Detached DOM nodes:** Removed from DOM but still referenced in JS
4. **Closures:** Unintentionally keeping large objects in scope

```javascript
// Memory leak example:
var elements = [];
setInterval(function() {
  elements.push(new Array(1000).fill("data")); // keeps growing!
}, 100);

// Fix: Clear references when done
function cleanup() {
  elements = []; // Now all old arrays can be GC'd
}
```

**Detecting Memory Leaks:**
- Use Chrome DevTools → Memory tab
- Take Heap Snapshots before and after actions
- Compare snapshots to find what's growing
- Use "Allocation instrumentation on timeline" to see memory allocation over time

### Coding Questions

**Q1: Will this cause a memory leak? Why?**
```javascript
var stored = [];
document.addEventListener("click", function() {
  stored.push(new Array(10000));
});
```
**Answer:** Yes. Every click adds 10,000 elements to `stored`, which is a global variable and never gets freed. Over time, memory usage grows indefinitely.

**Fix:**
```javascript
document.addEventListener("click", function() {
  var temp = new Array(10000); // local variable, freed after function ends
  console.log(temp.length);
});
```

---

**Q2: What is the difference between `Map` and `WeakMap`?**
```javascript
var map = new Map();
var weakMap = new WeakMap();

// Map — strong references, items stay until manually deleted
// Can be iterated with for...of

// WeakMap — weak references, items can be GC'd when key is unreachable
// Cannot be iterated (no .keys(), .values(), .entries())
// Keys MUST be objects (not primitives)
```

---

**Q3: Will this object be garbage collected?**
```javascript
function run() {
  var bigData = { values: new Array(100000) };
  // Use bigData...
  return bigData.values.length;
}
run();
```
**Answer:** Yes — after `run()` returns, `bigData` is no longer reachable (it's a local variable). It becomes eligible for garbage collection.

---

## Summary Cheat Sheet

| Concept | Key Point |
|---|---|
| **Prototype** | Every object has a `__proto__` link to a prototype object |
| **Prototype Chain** | JS searches properties up the chain until null |
| **Factory Function** | Regular function that returns an object (camelCase) |
| **Constructor Function** | Function used with `new` keyword (PascalCase) |
| **IIFE** | Function that runs immediately — prevents global pollution |
| **Strict Mode** | `"use strict"` — prevents common mistakes and bad practices |
| **Value Types** | Primitives — copied by value (independent) |
| **Reference Types** | Objects — copied by reference (shared) |
| **Type Coercion** | JS automatically converts types (implicit) |
| **Type Conversion** | You explicitly convert types (explicit) |
| **Event Loop** | Moves callbacks from queues to call stack when stack is empty |
| **Microtask Queue** | Promises — higher priority than callback queue |
| **Callback Queue** | setTimeout, DOM events — lower priority |
| **V8 / JIT** | JavaScript is JIT compiled — both interpreted and compiled |
| **Stack** | Fast memory for primitives and function context |
| **Heap** | Dynamic memory for objects, arrays, functions |
| **GC** | Automatic memory management — frees unreachable objects |
| **WeakMap/WeakSet** | GC-friendly collections — weak references to keys |
