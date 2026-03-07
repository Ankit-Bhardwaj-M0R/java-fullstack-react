# JavaScript Core Concepts - Study Guide (core1)

> **Topics covered:** Execution Context · Call Stack · Hoisting · Scope & Lexical Environment · Closures · `this` Keyword · `call`, `apply`, `bind`

---

## 1. How JavaScript Works & Execution Context

### Interview Question
**"What is an Execution Context in JavaScript? Describe its two components."**

### Answer (Conversational)

So, the most important thing to know about JavaScript is this one sentence: **"Everything in JavaScript happens inside the Execution Context."** Every single line you write, every variable, every function — it all lives inside this thing.

Think of the Execution Context as a **big box** or container where your JavaScript code runs. This box has **two compartments** inside it:

**1. Memory Component (also called Variable Environment)**
This is the place where all your variables and functions are stored as **key-value pairs**. Like a locker room — `a: 10`, `square: [function code]`. It's also called the **Variable Environment** because that's where variables *live*.

**2. Code Component (also called Thread of Execution)**
This is the place where code is actually **executed, one line at a time**. It's called the **Thread of Execution** because the code runs like a thread — it goes through your program line by line.

Here's a key fact: **JavaScript is a synchronous, single-threaded language.**

- **Single-threaded** → JavaScript can only execute **one command at a time**.
- **Synchronous** → It executes commands **in order**, one after another. It can't jump to line 5 until line 4 is done.

```
Execution Context
┌──────────────────────────────────────────────────────────┐
│  Memory Component (Variable Environment)                  │
│  a: undefined  →  a: 10                                   │
│  square: { entire function code stored here }             │
├──────────────────────────────────────────────────────────┤
│  Code Component (Thread of Execution)                     │
│  → executes code line by line                             │
└──────────────────────────────────────────────────────────┘
```

### Coding Questions

**Q1: What will this print? Why?**
```javascript
var x = 5;
console.log(x);
```
**Answer:** `5`. The variable `x` is first stored in memory (with value `undefined`), then in the code execution phase, `5` is assigned to it.

---

**Q2: What does "single-threaded" mean in JavaScript?**
```javascript
console.log("Line 1");
console.log("Line 2");
console.log("Line 3");
```
**Answer:** All three will print in order: `Line 1`, `Line 2`, `Line 3`. JavaScript runs one line at a time, in sequence.

---

## 2. How JavaScript Code Is Executed & The Call Stack

### Interview Question
**"Walk me through what happens step-by-step when you run this JavaScript code:"**
```javascript
var n = 2;
function square(num) {
  var ans = num * num;
  return ans;
}
var square2 = square(n);
var square4 = square(4);
```

### Answer (Conversational)

Great question! When you hit "run", JavaScript does something really beautiful behind the scenes. Here's how it works:

**Step 1: A Global Execution Context (GEC) is created.**
This GEC is pushed into the Call Stack (we'll talk about that in a moment). The GEC goes through **two phases**:

#### Phase 1 — Memory Creation Phase
JavaScript *skims* through the whole program before executing anything. It allocates memory to all variables and functions:
- `n` → `undefined` (a special placeholder)
- `square` → the *entire function code* is literally copied here
- `square2` → `undefined`
- `square4` → `undefined`

Think of `undefined` as JavaScript putting a sticky note that says "I know this exists, but I don't know its value yet."

#### Phase 2 — Code Execution Phase
Now JavaScript runs the code line by line:
- **Line 1:** `n = 2` → The value `2` replaces `undefined` for `n`
- **Line 2-4:** Function definition — nothing to execute here, skip!
- **Line 5:** `square(n)` is called — this is **function invocation**

When you invoke a function, **a brand new Execution Context** is created for that function, and it's pushed onto the Call Stack.

Inside `square(n)`:
- **Phase 1 (Memory):** `num → undefined`, `ans → undefined`
- **Phase 2 (Execution):**
  - `num` gets the value `2` (the argument passed)
  - `ans = num * num = 4`
  - `return ans` → the value `4` is returned, and this execution context is **deleted** (popped from Call Stack)
- Back in GEC: `square2 = 4`

Then the same thing happens for `square(4)`, which returns `16`, so `square4 = 16`. When everything's done, the GEC itself is deleted.

#### The Call Stack

The Call Stack is JavaScript's way of managing the order of execution contexts. Think of it like a stack of plates:
- When a program runs → Global Execution Context goes in (bottom of stack)
- When a function is called → its Execution Context is pushed ON TOP
- When a function returns → its Execution Context is POPPED OFF
- When the whole program finishes → GEC is popped off, stack is empty

```
Call Stack (while square(n) is running):
┌──────────────────────┐
│  square EC (E1)      │  ← Currently running
├──────────────────────┤
│  Global EC           │  ← Waiting
└──────────────────────┘
```

The Call Stack is also called: **Execution Context Stack, Program Stack, Control Stack, Runtime Stack, Machine Stack** — all the same thing!

### Coding Questions

**Q1: How many execution contexts are created for this code?**
```javascript
function greet() {
  return "Hello!";
}
var result = greet();
```
**Answer:** 2 — one Global Execution Context and one for the `greet()` function call.

---

**Q2: What happens to the execution context after a function returns?**
```javascript
function add(a, b) {
  return a + b;
}
var sum = add(3, 7);
console.log(sum); // ?
```
**Answer:** `10`. After `add(3, 7)` returns `10`, its execution context is deleted. The value `10` gets stored in `sum`.

---

**Q3: Trace through this code step by step:**
```javascript
var x = 10;
function double(num) {
  return num * 2;
}
var result = double(x);
console.log(result); // ?
```
**Answer:**
- Memory Phase: `x = undefined`, `double = [function]`, `result = undefined`
- Execution Phase: `x = 10` → call `double(10)` → `num = 10`, `return 20` → `result = 20`
- Output: `20`

---

## 3. Hoisting in JavaScript

### Interview Question
**"What is Hoisting? How does it work for variables vs. functions?"**

### Answer (Conversational)

This is where JavaScript gets interesting. In most programming languages, if you try to access a variable *before* you declare it, you get an error. But in JavaScript, something magical happens. Let me show you:

```javascript
getName(); // Namaste JavaScript ← works!
console.log(x); // undefined ← no error!

var x = 7;
function getName() {
  console.log("Namaste JavaScript");
}
```

Why does this work? You'd expect an error for calling `getName()` before it's defined, right? And you'd expect `x` to be `undefined` or throw an error. But instead:
- `getName()` actually *works*
- `x` gives us `undefined` (not an error)

**This phenomenon is called Hoisting.**

**Hoisting** is when you can access variables and functions *even before* they are initialized/declared in your code — without getting an error.

**But WHY does this happen?**

Remember the Memory Creation Phase (Phase 1)? Before ANY code runs, JavaScript goes through the whole program and:
- Allocates memory to variables → stores `undefined`
- Allocates memory to functions → stores the **entire function code**

So by the time the code runs, the memory for `x` already exists (with `undefined`), and the full code of `getName` is already stored. That's why they're accessible "before" their declaration!

**The difference between variables and functions:**
| | Variables (`var`) | Functions (regular) |
|---|---|---|
| During hoisting | Stored as `undefined` | Entire function code stored |
| If accessed before declaration | `undefined` | Works perfectly |

**Arrow Functions & Function Expressions behave differently:**
```javascript
console.log(myArrow); // undefined (not the function!)
var myArrow = () => console.log("arrow");
```
Arrow functions and function expressions are treated **like variables** — they get `undefined` during hoisting, not their function code.

**Undefined vs Not Defined:**
- `undefined` → JavaScript knows about the variable (it's in memory), but no value was assigned yet
- `not defined` → JavaScript has NO idea this variable exists anywhere!

```javascript
console.log(a); // undefined (if var a = 5 exists somewhere)
console.log(b); // ReferenceError: b is not defined (b doesn't exist at all)
```

**Interview tip:** Don't say "JavaScript moves the code to the top of the file." That's not accurate! The real answer is: during the Memory Creation Phase, variables get `undefined` and functions get their full code stored, *before* execution begins. That's why hoisting works.

### Coding Questions

**Q1: What will this output?**
```javascript
console.log(a);
var a = 5;
console.log(a);
```
**Answer:** `undefined`, then `5`. During the memory phase, `a` gets `undefined`. The first `console.log` runs before `a = 5` is assigned.

---

**Q2: What will this output?**
```javascript
greet();
function greet() {
  console.log("Hello!");
}
```
**Answer:** `Hello!`. Functions are fully hoisted — their entire code is stored in memory before execution, so calling them before the definition works.

---

**Q3: What's the difference between these two?**
```javascript
// Option A
console.log(sayHi); // ?
var sayHi = function() { return "Hi"; };

// Option B
console.log(sayHello); // ?
function sayHello() { return "Hello"; }
```
**Answer:**
- Option A: `undefined` — function expressions are hoisted like variables
- Option B: `[Function: sayHello]` — regular function declarations are fully hoisted

---

**Q4: Will this throw an error?**
```javascript
console.log(myFn);
var myFn = () => "arrow function";
```
**Answer:** No error. Prints `undefined` because arrow functions are treated as variables during hoisting — they get `undefined`, not the function code.

---

## 4. Scope Chain & Lexical Environment

### Interview Question
**"What is Lexical Environment? What is the Scope Chain in JavaScript?"**

### Answer (Conversational)

This is one of the most important concepts in JavaScript, and once you get it, closures and many other things will make total sense.

**Scope** simply means: *where can you access a specific variable or function in your code?*

Now here's the key insight from the transcript: **Scope in JavaScript is directly related to the Lexical Environment.**

**What is Lexical?**
Lexical means "in terms of where the code is physically written." If a function `c` is written *inside* function `a`, we say `c` is **lexically inside** `a`. If `a` is written inside the global scope, `a` is **lexically inside the global scope**.

**What is Lexical Environment?**
Whenever an Execution Context is created, a **Lexical Environment** is also created alongside it. A Lexical Environment is:

> **Local memory + Reference to the Lexical Environment of the parent**

```javascript
function a() {
  var b = 10;
  function c() {
    console.log(b); // Can access b! It's in parent's scope
  }
  c();
}
a();
```

When `c` is running and looks for `b`:
1. First checks its own local memory → not found
2. Follows the reference to parent's (function `a`'s) lexical environment → **found! `b = 10`**
3. Prints `10` ✓

If `b` wasn't in `a` either, it would go to `a`'s parent — the global scope. If still not found, it would reach `null` (the end), and JavaScript throws a `ReferenceError: b is not defined`.

**The Scope Chain:**
This chain of lexical environments being searched is called the **Scope Chain**. It goes from inner to outer, following parent references until it either finds the variable or hits `null`.

```
Scope Chain for function c:
c's local memory → a's local memory → Global memory → null
```

**What can't you do?**
```javascript
function a() {
  var b = 10; // b is inside a
}
console.log(b); // ReferenceError! b is NOT in global scope
```
Variables don't go *outward* — scope only goes *inward*. A parent can't access a child's variables, but a child can access a parent's.

### Coding Questions

**Q1: What will this print?**
```javascript
var x = 1;
function outer() {
  var y = 2;
  function inner() {
    console.log(x); // ?
    console.log(y); // ?
  }
  inner();
}
outer();
```
**Answer:** `1` then `2`. `inner()` can access `x` from global scope and `y` from `outer`'s scope via the scope chain.

---

**Q2: Will this work?**
```javascript
function parent() {
  var secret = 42;
}
console.log(secret); // ?
```
**Answer:** `ReferenceError: secret is not defined`. The global scope cannot access variables declared inside a function — scope only goes inward.

---

**Q3: How deep can the scope chain go?**
```javascript
var a = 1;
function level1() {
  var b = 2;
  function level2() {
    var c = 3;
    function level3() {
      console.log(a, b, c); // ?
    }
    level3();
  }
  level2();
}
level1();
```
**Answer:** `1 2 3`. `level3` can access `a` from global, `b` from `level1`, and `c` from `level2` — the scope chain goes as deep as needed.

---

## 5. Closures in JavaScript

### Interview Question
**"What is a Closure in JavaScript? Can you give an example?"**

### Answer (Conversational)

A Closure is one of the most discussed interview topics in JavaScript, and it builds directly on what we just learned about Lexical Environments.

**Definition:** A closure is when a **function bundled together with its lexical environment** forms a closure. In simpler words — a function that "remembers" the variables from its outer scope even after that outer scope has finished executing.

Let's see why this is surprising. Consider this:

```javascript
function makeCounter() {
  var count = 0; // This is in makeCounter's scope

  function increment() {
    count++;
    console.log(count);
  }

  return increment; // We return the inner function
}

var counter = makeCounter(); // makeCounter's execution context is DONE
counter(); // 1
counter(); // 2
counter(); // 3
```

Here's what's mind-bending: `makeCounter()` has *finished executing*. Its execution context has been deleted from the call stack. Yet when we call `counter()`, it still accesses and updates `count`!

**Why?** Because when `increment` was returned, it came bundled with a reference to its lexical environment — which includes `count`. The function "closed over" its surrounding scope. That's the closure.

**A function along with its lexical environment is what forms a closure.**

**Why are closures useful?**
- **Data privacy / Encapsulation:** You can hide variables from the outside world
- **Module design pattern:** The foundation of JavaScript modules
- **Memoization and caching**
- **Event handlers and callbacks**
- **Functions that run only once** (like initialization code)

```javascript
// Real example: Data privacy with closures
function createBank() {
  var balance = 0; // Private — can't be accessed directly from outside

  return {
    deposit: function(amount) { balance += amount; },
    withdraw: function(amount) { balance -= amount; },
    getBalance: function() { return balance; }
  };
}

var myAccount = createBank();
myAccount.deposit(100);
myAccount.withdraw(30);
console.log(myAccount.getBalance()); // 70
// console.log(balance); // ReferenceError! balance is private
```

**Closures remember the reference, not the value:**
This is a common gotcha. The closure holds a *reference* to the variable, not a snapshot of its value:

```javascript
function makeAdder(x) {
  return function(y) {
    return x + y; // x is remembered by reference from makeAdder's scope
  };
}

var add5 = makeAdder(5);
console.log(add5(3)); // 8
console.log(add5(10)); // 15
```

### Coding Questions

**Q1: What will this output?**
```javascript
function outer() {
  var name = "JavaScript";
  function inner() {
    console.log(name);
  }
  return inner;
}
var fn = outer();
fn(); // ?
```
**Answer:** `"JavaScript"`. Even after `outer()` finishes, `inner` (now called as `fn`) still has access to `name` through closure.

---

**Q2: Create a counter using closures that has increment, decrement, and reset.**
```javascript
function makeCounter() {
  var count = 0;
  return {
    increment: function() { count++; console.log(count); },
    decrement: function() { count--; console.log(count); },
    reset: function() { count = 0; console.log(count); }
  };
}
var counter = makeCounter();
counter.increment(); // 1
counter.increment(); // 2
counter.decrement(); // 1
counter.reset();     // 0
```

---

**Q3: What's the output? (Classic closure gotcha)**
```javascript
for (var i = 0; i < 3; i++) {
  setTimeout(function() {
    console.log(i);
  }, 1000);
}
```
**Answer:** `3, 3, 3` — not `0, 1, 2`! Because `var` doesn't have block scope — there's one shared `i`. By the time the setTimeout runs, the loop has already finished and `i` is `3`. **Fix:** Use `let` instead of `var` (block scoped), or use closures with IIFE.

---

**Q4: Fix the above code using a closure:**
```javascript
for (var i = 0; i < 3; i++) {
  (function(j) {
    setTimeout(function() {
      console.log(j); // 0, 1, 2 ✓
    }, 1000);
  })(i);
}
```

---

## 6. The `this` Keyword

### Interview Question
**"How does `this` behave in different contexts in JavaScript?"**

### Answer (Conversational)

`this` is one of the most confusing topics in JavaScript because it behaves differently depending on **how and where the function is called**. The MDN documentation for `this` is very dense — let's break it down simply.

**The Golden Rule:** The value of `this` is determined by **how the function is called**, not where it's defined.

---

#### Case 1: `this` in Global Space

```javascript
console.log(this); // In browser: window object
```
In the global scope, `this` refers to the **global object**. In a browser, that's the `window` object. In Node.js, it's a different object.

---

#### Case 2: `this` inside a Regular Function

```javascript
function show() {
  console.log(this);
}
show(); // In strict mode: undefined | In non-strict mode: window
```

**Strict mode:** `this` is `undefined` inside a plain function call.
**Non-strict mode:** Due to a mechanism called **"this substitution"** — whenever the value of `this` would be `undefined` or `null`, JavaScript replaces it with the global object (`window` in browser).

```javascript
"use strict";
function show() {
  console.log(this); // undefined
}
show();
```

---

#### Case 3: `this` inside an Object Method

```javascript
var student = {
  name: "Ache",
  printName: function() {
    console.log(this.name); // "Ache"
  }
};
student.printName(); // this = student object
```

When a function is a property of an object (called a **method**), `this` refers to that object. The value depends on who "owns" the function call — here it's `student`.

**Function vs Method:** A function defined inside an object is called a **method**. Same code, different name depending on context.

---

#### Case 4: `call`, `apply`, `bind` — Overriding `this`

These three methods let you **manually set** what `this` refers to.

**`call()`** — calls the function immediately, passing `this` explicitly:
```javascript
var student1 = { name: "Ache" };
var student2 = { name: "Deepika" };

function printName() {
  console.log(this.name);
}

printName.call(student1); // "Ache"
printName.call(student2); // "Deepika"
```

**`apply()`** — same as `call()`, but extra arguments are passed as an **array**:
```javascript
function greet(greeting, punctuation) {
  console.log(greeting + " " + this.name + punctuation);
}
greet.call(student1, "Hello", "!");    // Hello Ache!
greet.apply(student1, ["Hello", "!"]); // Hello Ache! (same result)
```

**`bind()`** — does NOT call immediately, it creates a **new function** with `this` bound:
```javascript
var boundFn = printName.bind(student2); // Returns a new function
boundFn(); // "Deepika" — can be called later
```

**Difference summary:**
| Method | Calls immediately? | Arguments |
|---|---|---|
| `call()` | Yes | Passed separately |
| `apply()` | Yes | Passed as array |
| `bind()` | No (returns new function) | Passed separately |

---

#### Case 5: `this` inside Arrow Functions

Arrow functions **do not have their own `this`**. They inherit `this` from their **enclosing lexical context** — meaning wherever the arrow function was defined, whatever `this` was there, that's what the arrow function uses.

```javascript
var obj = {
  name: "MyObj",
  // Regular method:
  regularMethod: function() {
    console.log(this.name); // "MyObj" ✓
  },
  // Arrow method:
  arrowMethod: () => {
    console.log(this); // window (global) — NOT obj!
  }
};
obj.regularMethod(); // "MyObj"
obj.arrowMethod();   // window object (because arrow function
                     // takes this from the global lexical context)
```

**Tricky example:**
```javascript
var obj = {
  name: "MyObj",
  outer: function() {
    // 'this' here is obj (it's a method call)
    var inner = () => {
      console.log(this.name); // "MyObj" ✓
    };
    inner();
  }
};
obj.outer(); // "MyObj"
```
Here, the arrow function `inner` takes `this` from its enclosing lexical context — which is `outer`, where `this` is `obj`. So `this.name` is `"MyObj"`.

---

#### Case 6: `this` in the DOM

```html
<button onclick="alert(this)">Click me</button>
```
In event handlers in HTML, `this` refers to the **HTML element** itself (the button element in this case).

---

#### Quick Recap Table

| Context | Value of `this` |
|---|---|
| Global scope | `window` (browser) |
| Regular function (strict) | `undefined` |
| Regular function (non-strict) | `window` (due to this substitution) |
| Object method | The object |
| Arrow function | Enclosing lexical context's `this` |
| DOM event handler | The HTML element |
| With `call/apply/bind` | Whatever you explicitly pass |

### Coding Questions

**Q1: What will this print?**
```javascript
var name = "Global";
var person = {
  name: "Local",
  greet: function() {
    console.log(this.name);
  }
};
person.greet(); // ?
var g = person.greet;
g(); // ?
```
**Answer:** `"Local"`, then `"Global"` (or `undefined` in strict mode). When called as `person.greet()`, `this` is `person`. When called as `g()` (a plain function call), `this` is the global object.

---

**Q2: What does this print?**
```javascript
var obj = {
  val: 100,
  show: () => {
    console.log(this.val);
  }
};
obj.show(); // ?
```
**Answer:** `undefined`. Arrow functions don't have their own `this` — they get it from the enclosing context, which is the global scope. `window.val` is `undefined`.

---

**Q3: Use `bind` to fix this:**
```javascript
var user = {
  name: "Alice",
  sayName: function() {
    console.log(this.name);
  }
};
var greet = user.sayName;
greet(); // undefined — this is wrong! Fix it.
```
**Answer:**
```javascript
var greet = user.sayName.bind(user);
greet(); // "Alice" ✓
```

---

## 7. `call`, `apply`, and `bind` — Deep Dive

### Interview Question
**"What is the difference between `call`, `apply`, and `bind`?"**

### Answer (Conversational)

These are three methods that every JavaScript function has (because functions are objects). They're used for **function borrowing** — using a method from one object for another object.

**`call()`:**
```javascript
var car1 = { brand: "Toyota", speed: 120 };
var car2 = { brand: "Honda", speed: 100 };

function showDetails(city, country) {
  console.log(this.brand + " going " + this.speed + "km/h in " + city + ", " + country);
}

showDetails.call(car1, "Mumbai", "India");
// Toyota going 120km/h in Mumbai, India

showDetails.call(car2, "Tokyo", "Japan");
// Honda going 100km/h in Tokyo, Japan
```

**`apply()`** — identical to `call()`, but arguments in an array:
```javascript
showDetails.apply(car1, ["Mumbai", "India"]);
// Toyota going 120km/h in Mumbai, India
```

**`bind()`** — doesn't call immediately, returns a new bound function:
```javascript
var showCar1Details = showDetails.bind(car1, "Mumbai", "India");
// Later in the code:
showCar1Details(); // Toyota going 120km/h in Mumbai, India
```

**Interview answer in one sentence:**
- `call` and `apply` invoke the function **immediately** with a new `this` (differ in argument format)
- `bind` **returns a new function** with `this` permanently bound (for later use)

### Coding Questions

**Q1: What will this print?**
```javascript
function multiply(a, b) {
  return a * b * this.factor;
}
var obj = { factor: 10 };

console.log(multiply.call(obj, 3, 4));   // ?
console.log(multiply.apply(obj, [3, 4])); // ?
var bound = multiply.bind(obj, 3);
console.log(bound(4));                    // ?
```
**Answer:** All three print `120` (`3 * 4 * 10`).

---

**Q2: Fix the code using `call`:**
```javascript
var teacher = {
  subject: "Math",
  introduce: function() {
    console.log("I teach " + this.subject);
  }
};
var newTeacher = { subject: "Science" };
// Make newTeacher use teacher's introduce method
```
**Answer:**
```javascript
teacher.introduce.call(newTeacher); // "I teach Science"
```

---
