# JavaScript Functions - Complete Guide

> **Beginner-friendly study guide** covering everything about JavaScript functions: declarations, expressions, arrow functions, callbacks, default parameters, spread/rest, currying, debounce, and throttle.

---

## Table of Contents
1. [Function Statement vs Function Expression](#1-function-statement-vs-function-expression)
2. [Anonymous Functions](#2-anonymous-functions)
3. [Named Function Expression](#3-named-function-expression)
4. [Parameters vs Arguments](#4-parameters-vs-arguments)
5. [First-Class Functions / First-Class Citizens](#5-first-class-functions--first-class-citizens)
6. [Callback Pattern](#6-callback-pattern)
7. [Arrow Functions (ES6)](#7-arrow-functions-es6)
8. [Default Parameters](#8-default-parameters)
9. [Spread and Rest Operators](#9-spread-and-rest-operators)
10. [Currying](#10-currying)
11. [Debounce and Throttle](#11-debounce-and-throttle)

---

## 1. Function Statement vs Function Expression

### Interview Question
**"What is the difference between a function statement (declaration) and a function expression in JavaScript? How does hoisting affect each?"**

### Answer (Conversational)

There are two primary ways to define functions in JavaScript, and they behave differently because of **hoisting**.

#### Function Statement (Function Declaration)

A **function statement** (also called a **function declaration**) starts with the `function` keyword at the beginning of a statement:

```javascript
function greet() {
  console.log("Hello!");
}
```

> **"Function Statement" and "Function Declaration" are the exact same thing.** These terms are used interchangeably.

Function statements are **hoisted fully** — meaning the entire function (name AND body) is moved to the top of its scope during the memory creation phase. So you can call them **before** they appear in the code:

```javascript
greet(); // Works! Prints "Hello!"

function greet() {
  console.log("Hello!");
}
```

This works because during hoisting, JavaScript stored `greet` as the full function in memory before any code ran.

#### Function Expression

A **function expression** assigns a function as a value to a variable:

```javascript
const greet = function() {
  console.log("Hello!");
};
```

Function expressions are treated like **variables**. In hoisting:
- The variable name (`greet`) is hoisted
- But its value (the function) is **NOT** — the variable starts as `undefined`

So calling a function expression before its definition results in an **error**:

```javascript
greet(); // TypeError: greet is not a function (greet is undefined at this point)

const greet = function() {
  console.log("Hello!");
};
```

When JavaScript reaches `greet()`, `greet` exists in memory (hoisted) but its value is `undefined`. Calling `undefined()` throws a TypeError.

#### `let` and `const` — Temporal Dead Zone

With `let` and `const`, function expressions are in the **Temporal Dead Zone (TDZ)** — you can't access them at all before the declaration line:

```javascript
greet(); // ReferenceError: Cannot access 'greet' before initialization

const greet = function() {
  console.log("Hello!");
};
```

#### Side-by-Side Comparison

```javascript
// FUNCTION STATEMENT — can call BEFORE the definition
sayHi(); // works!

function sayHi() {
  console.log("Hi!");
}

// FUNCTION EXPRESSION — cannot call BEFORE the definition
sayBye(); // TypeError or ReferenceError!

const sayBye = function() {
  console.log("Bye!");
};
```

### Coding Questions

**Q1:** What will happen when this code runs?
```javascript
hello();

var hello = function() {
  console.log("Hello World");
};
```

**Answer:** `TypeError: hello is not a function`. With `var`, `hello` is hoisted as `undefined`. Calling `undefined()` throws TypeError.

---

**Q2:** What will happen here?
```javascript
sayHello();

function sayHello() {
  console.log("Hello!");
}
```

**Answer:** Prints `"Hello!"` successfully — function declarations are fully hoisted.

---

**Q3:** What is the difference between how `var`, `let`/`const` work with function expressions in terms of hoisting?

**Answer:**
- `var`: hoisted as `undefined` — calling before definition gives `TypeError`
- `let`/`const`: in the Temporal Dead Zone — calling before definition gives `ReferenceError`

---

## 2. Anonymous Functions

### Interview Question
**"What is an anonymous function? When can and can't you use one?"**

### Answer (Conversational)

An **anonymous function** is a function **without a name**:

```javascript
function() {
  console.log("I have no name!");
}
```

#### Cannot Be Standalone

If you try to use an anonymous function as a standalone statement, JavaScript throws a **SyntaxError**:

```javascript
function() {        // SyntaxError: Function statements require a function name
  console.log("Error!");
}
```

This is because JavaScript sees the `function` keyword at the start of a statement and expects a named function declaration. Without a name, it has no identity — it can't be referenced or called anywhere.

#### Anonymous Functions Are Used as VALUES

Anonymous functions are valid and extremely common when used as a **value**:

```javascript
// Assigned to a variable
const greet = function() {
  console.log("Hello!");
};

// Passed as an argument (callback)
setTimeout(function() {
  console.log("1 second later");
}, 1000);

// Returned from another function
function makeGreeter() {
  return function() {
    console.log("Hello!");
  };
}
```

The key insight: anonymous functions are perfectly valid as **values**. They're only invalid as a standalone **statement**.

### Coding Questions

**Q1:** Which of these will cause a SyntaxError?
```javascript
// A:
const fn = function() { return 42; };

// B:
function() { return 42; }

// C:
setTimeout(function() { console.log("hi"); }, 1000);
```

**Answer:** B — anonymous function used as a standalone statement causes SyntaxError. A and C are fine.

---

**Q2:** What is the output?
```javascript
const add = function(a, b) { return a + b; };
console.log(add(3, 4));
```

**Answer:** `7`

---

## 3. Named Function Expression

### Interview Question
**"What is a named function expression? How is it different from a regular function expression and a function declaration?"**

### Answer (Conversational)

A **named function expression** is a function expression where the function itself has a name:

```javascript
const greet = function xyz() {
  console.log("Hello!");
};
```

Here:
- `greet` is the **variable** (accessible everywhere in scope)
- `xyz` is the function's **internal name** (only accessible inside the function body)

#### The Inner Name is Scoped to the Function

```javascript
const greet = function xyz() {
  console.log("Hello from xyz!");
};

greet(); // works — "Hello from xyz!"
xyz();   // ReferenceError: xyz is not defined
```

`xyz` simply does NOT exist in the outer scope. It only exists inside the function body itself (useful for recursion).

#### Use Case: Recursion

The inner name is useful for **recursive** function expressions:

```javascript
const countdown = function count(n) {
  if (n <= 0) {
    console.log("Done!");
    return;
  }
  console.log(n);
  count(n - 1); // 'count' is accessible inside!
};

countdown(3);
// 3
// 2
// 1
// Done!
```

#### Summary of Three Types

```javascript
// 1. Function Declaration (Statement)
function greet() { }
// Name is accessible everywhere in scope

// 2. Anonymous Function Expression
const greet = function() { };
// No internal name at all

// 3. Named Function Expression
const greet = function xyz() { };
// 'greet' accessible everywhere, 'xyz' only inside function body
```

### Coding Questions

**Q1:** What will this output?
```javascript
const fn = function myFn() {
  console.log("Inside");
};

fn();
myFn();
```

**Answer:** Prints `"Inside"` from `fn()`, then `ReferenceError: myFn is not defined` from `myFn()`.

---

**Q2:** When would you use a named function expression instead of an anonymous function expression?

**Answer:** Use it when you need the function to reference itself (recursion) or when you want better stack traces for debugging purposes (the name appears in error messages).

---

## 4. Parameters vs Arguments

### Interview Question
**"What is the difference between parameters and arguments in JavaScript?"**

### Answer (Conversational)

These terms are often used interchangeably, but they mean different things:

- **Parameters** are the **labels/identifiers** defined in the function signature (the definition)
- **Arguments** are the **actual values** passed when calling the function

```javascript
//          parameters
//           ↓    ↓
function add(a,   b) {
  return a + b;
}

//       arguments
//        ↓   ↓
add(    10,  20  );
```

Think of parameters as **placeholders** (like blank boxes). Arguments are the **actual things** you put into those boxes when you call the function.

Another way to remember: **Parameters are labels, arguments are values.**

```javascript
function greet(name) {  // 'name' is a parameter
  console.log("Hello, " + name);
}

greet("Alice"); // "Alice" is an argument
greet("Bob");   // "Bob" is an argument
```

The parameter `name` is defined once. But we can pass different arguments (`"Alice"`, `"Bob"`, etc.) each time we call the function.

### Coding Questions

**Q1:** Identify parameters and arguments:
```javascript
function multiply(x, y) {
  return x * y;
}
multiply(5, 8);
```

**Answer:** `x` and `y` are **parameters**. `5` and `8` are **arguments**.

---

## 5. First-Class Functions / First-Class Citizens

### Interview Question
**"What does it mean for JavaScript to have 'first-class functions'?"**

### Answer (Conversational)

When we say functions are **first-class citizens** in JavaScript, it means functions can be treated like any other value. They can be:

1. **Assigned to a variable**
2. **Passed as an argument** to another function
3. **Returned from** another function

```javascript
// 1. Assigned to a variable
const greet = function() {
  console.log("Hello!");
};

// 2. Passed as an argument
function execute(fn) {
  fn(); // call the passed function
}
execute(greet); // "Hello!"

// 3. Returned from a function
function createMultiplier(factor) {
  return function(number) {
    return number * factor;
  };
}
const double = createMultiplier(2);
console.log(double(5)); // 10
console.log(double(8)); // 16
```

This is a **fundamental feature** of JavaScript and what makes patterns like callbacks, higher-order functions, and currying possible.

> **First-class** doesn't mean "most important" — it means "can be used anywhere a value can be used." Functions in JavaScript are just values, same as numbers, strings, and objects.

### Coding Questions

**Q1:** Demonstrate that functions can be stored in an object:
```javascript
const calculator = {
  add: function(a, b) { return a + b; },
  subtract: function(a, b) { return a - b; }
};

console.log(calculator.add(5, 3));      // 8
console.log(calculator.subtract(5, 3)); // 2
```

---

**Q2:** Write a function `applyTwice(fn, value)` that applies a function to a value twice:
```javascript
function applyTwice(fn, value) {
  return fn(fn(value));
}

const double = x => x * 2;
console.log(applyTwice(double, 3)); // 12 (3 → 6 → 12)
```

---

## 6. Callback Pattern

### Interview Question
**"What is a callback function? What's the difference between synchronous and asynchronous callbacks?"**

### Answer (Conversational)

A **callback** is a function that is **passed as an argument to another function**, and is called (invoked) by that function.

```javascript
function greet(name, callback) {
  console.log("Hello, " + name);
  callback(); // invoke the passed function
}

function sayBye() {
  console.log("Goodbye!");
}

greet("Alice", sayBye);
// "Hello, Alice"
// "Goodbye!"
```

Any function that **accepts or returns** another function is called a **Higher Order Function**.

#### Synchronous Callbacks

A **synchronous callback** executes **immediately** — it happens in the normal flow, line by line.

Common synchronous higher-order functions you use all the time:

```javascript
// Array.sort — callback defines comparison
const nums = [3, 1, 4, 1, 5, 9];
nums.sort((a, b) => a - b);
console.log(nums); // [1, 1, 3, 4, 5, 9]

// Array.map — callback transforms each element
const doubled = [1, 2, 3].map(x => x * 2);
console.log(doubled); // [2, 4, 6]

// Array.filter — callback decides what to keep
const evens = [1, 2, 3, 4].filter(x => x % 2 === 0);
console.log(evens); // [2, 4]
```

#### Asynchronous Callbacks

An **asynchronous callback** executes **later** — after some event, timer, or network response.

```javascript
// setTimeout — callback runs after 2 seconds
setTimeout(function() {
  console.log("2 seconds have passed!");
}, 2000);
console.log("This runs first!"); // prints before the timeout callback

// Event listener — callback runs when button is clicked
document.getElementById("btn").addEventListener("click", function() {
  console.log("Button was clicked!");
});

// fetch — callback runs when API response arrives
fetch("https://api.example.com/data")
  .then(function(response) {
    return response.json();
  })
  .then(function(data) {
    console.log(data); // runs after network response
  });
```

#### Key Difference

| Type | When callback runs | Examples |
|---|---|---|
| Synchronous | Immediately, in order | `map`, `filter`, `reduce`, `sort` |
| Asynchronous | Later, after event/timer | `setTimeout`, event listeners, `fetch` |

### Coding Questions

**Q1:** What is the output order?
```javascript
console.log("Start");

setTimeout(function() {
  console.log("Timeout");
}, 0);

console.log("End");
```

**Answer:** `Start`, `End`, `Timeout` — even with `0ms` delay, `setTimeout` is asynchronous and runs after the current synchronous code finishes.

---

**Q2:** Write a `repeat(fn, times)` higher order function that calls `fn` a given number of times:
```javascript
function repeat(fn, times) {
  for (let i = 0; i < times; i++) {
    fn(i);
  }
}

repeat(i => console.log("Hello #" + i), 3);
// Hello #0
// Hello #1
// Hello #2
```

---

## 7. Arrow Functions (ES6)

### Interview Question
**"What are arrow functions? How do they differ from regular functions, especially regarding `this`?"**

### Answer (Conversational)

Arrow functions are a **shorter syntax** for writing functions, introduced in ES6. They use `=>` instead of the `function` keyword.

#### Syntax Conversion

```javascript
// Regular function expression
const double = function(x) {
  return x * 2;
};

// Arrow function — same thing
const double = (x) => {
  return x * 2;
};

// Arrow function — single param: no parens needed
const double = x => {
  return x * 2;
};

// Arrow function — single expression: implicit return (no return, no curly braces)
const double = x => x * 2;
```

All four versions do exactly the same thing.

#### Arrow Function Shorthand Rules

1. **Single parameter**: parentheses around param are optional
   ```javascript
   x => x * 2         // parens omitted
   (x) => x * 2       // parens included (also fine)
   ```

2. **No parameters**: empty parens required
   ```javascript
   () => console.log("Hello")
   ```

3. **Multiple parameters**: parens required
   ```javascript
   (a, b) => a + b
   ```

4. **Single-expression body**: implicit return (no `return` keyword, no `{}`)
   ```javascript
   x => x * 2         // implicitly returns x * 2
   ```

5. **Multi-line body**: needs `{}` and explicit `return`
   ```javascript
   x => {
     const result = x * 2;
     return result;  // explicit return required
   }
   ```

#### The Most Important Difference: `this` Behavior

This is the **biggest functional difference** between arrow functions and regular functions.

**Regular functions** define their own `this` — what `this` points to depends on **how the function is called**:

```javascript
const person = {
  name: "Alice",
  greet: function() {
    console.log(this.name); // 'this' = person object
  },
  greetLater: function() {
    setTimeout(function() {
      console.log(this.name); // 'this' = window/undefined (not person!)
    }, 1000);
  }
};

person.greet();       // "Alice"
person.greetLater();  // undefined (or error in strict mode)
```

**Arrow functions** do NOT have their own `this`. They **inherit `this` from the surrounding scope** (lexical `this`):

```javascript
const person = {
  name: "Alice",
  greetLater: function() {
    setTimeout(() => {
      console.log(this.name); // 'this' = person object (inherited from greetLater)
    }, 1000);
  }
};

person.greetLater(); // "Alice" — arrow function inherits 'this' from greetLater
```

This was a very common bug before arrow functions. Using `setTimeout(function() {...})` inside a method would lose `this`. Arrow functions fixed this because they don't create their own `this`.

#### When NOT to Use Arrow Functions

Arrow functions are NOT always the right choice. Avoid them:

1. **As object methods** (they won't have access to `this` = the object):
   ```javascript
   const obj = {
     name: "Alice",
     greet: () => {
       console.log(this.name); // 'this' is NOT obj here — it's the outer scope!
     }
   };
   ```

2. **As constructor functions** — you can't use `new` with arrow functions

### Coding Questions

**Q1:** Convert this to an arrow function:
```javascript
const square = function(n) {
  return n * n;
};
```

**Answer:**
```javascript
const square = n => n * n;
```

---

**Q2:** What is the output?
```javascript
const person = {
  name: "Bob",
  regularGreet: function() {
    console.log(this.name);
  },
  arrowGreet: () => {
    console.log(this.name);
  }
};

person.regularGreet(); // ?
person.arrowGreet();   // ?
```

**Answer:**
- `person.regularGreet()` → `"Bob"` (regular function, `this` = person)
- `person.arrowGreet()` → `undefined` (arrow function, `this` = outer scope, not person)

---

**Q3:** Write an arrow function `isEven` that returns `true` if a number is even:
```javascript
const isEven = n => n % 2 === 0;
console.log(isEven(4)); // true
console.log(isEven(7)); // false
```

---

## 8. Default Parameters

### Interview Question
**"How do default parameters work in JavaScript? What's the difference between passing `undefined` vs `null`?"**

### Answer (Conversational)

**Default parameters** let you give a parameter a fallback value, in case no argument (or `undefined`) is passed:

```javascript
function greet(name = "User") {
  console.log("Hello, " + name + "!");
}

greet("Alice");    // "Hello, Alice!"
greet();           // "Hello, User!"    — no argument → uses default
greet(undefined);  // "Hello, User!"    — undefined → uses default
greet(null);       // "Hello, null!"    — null does NOT trigger default!
```

#### The Critical Distinction: `undefined` vs `null`

This is a common interview trick:

- **`undefined`** triggers the default (either by not passing anything, or explicitly passing `undefined`)
- **`null`** does **NOT** trigger the default — `null` is treated as a deliberately provided value

```javascript
function test(val = "default") {
  console.log(val);
}

test();           // "default"   ← undefined triggers default
test(undefined);  // "default"   ← undefined triggers default
test(null);       // null        ← null does NOT trigger default
test(0);          // 0           ← 0 does NOT trigger default
test("");         // ""          ← empty string does NOT trigger default
```

Only `undefined` (or missing argument) triggers the default. Everything else — `null`, `0`, `""`, `false` — is used as-is.

#### Before ES6 — The Old Way

Before ES6 default parameters, developers used to write:

```javascript
// Old way (has a problem!)
function greet(name) {
  name = name || "User"; // truthy/falsy check
  console.log("Hello, " + name);
}

greet(0);     // "Hello, User" — WRONG! 0 is falsy, so "User" is used instead of 0
greet("");    // "Hello, User" — WRONG! "" is falsy
greet(false); // "Hello, User" — WRONG! false is falsy
```

The old `||` trick has a bug: it treats ALL falsy values (`0`, `""`, `false`) as "missing", not just `undefined`. ES6 default parameters fixed this properly.

#### Default Values Can Be Expressions

Default values can be any expression, including function calls:

```javascript
function getDefault() { return "World"; }

function greet(name = getDefault()) {
  console.log("Hello, " + name);
}

greet();       // "Hello, World"
greet("Alice"); // "Hello, Alice"
```

### Coding Questions

**Q1:** What will each call print?
```javascript
function power(base, exponent = 2) {
  return base ** exponent;
}

console.log(power(3));        // ?
console.log(power(3, 3));     // ?
console.log(power(3, null));  // ?
console.log(power(3, undefined)); // ?
```

**Answer:**
- `power(3)` → 9 (exponent defaults to 2)
- `power(3, 3)` → 27
- `power(3, null)` → 1 (null doesn't trigger default; `3 ** null` = `3 ** 0` = 1)
- `power(3, undefined)` → 9 (undefined triggers default, same as `power(3)`)

---

**Q2:** Rewrite this using ES6 default parameters:
```javascript
function createUser(name, role) {
  name = name || "Anonymous";
  role = role || "viewer";
  return { name, role };
}
```

**Answer:**
```javascript
function createUser(name = "Anonymous", role = "viewer") {
  return { name, role };
}
```

---

## 9. Spread and Rest Operators

### Interview Question
**"What are the spread and rest operators in JavaScript? What's the difference between them?"**

### Answer (Conversational)

Both use the `...` syntax, but they do **opposite things**. Context determines which one is being used.

#### Spread Operator — Expands Values

The **spread operator** appears at the **call site** (when calling a function). It **expands** an array (or object) into **individual values**:

```javascript
const nums = [1, 2, 3];

// Without spread: Math.max(nums) → NaN (can't pass array)
// With spread:
Math.max(...nums); // Same as Math.max(1, 2, 3) → 3
```

More examples:

```javascript
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];

// Combine arrays
const combined = [...arr1, ...arr2]; // [1, 2, 3, 4, 5, 6]

// Spread in the middle
console.log(10, ...arr1, 20); // 10 1 2 3 20

// Copy an array
const copy = [...arr1]; // [1, 2, 3] — new array!

// Spread objects (merge objects)
const obj1 = { a: 1, b: 2 };
const obj2 = { c: 3 };
const merged = { ...obj1, ...obj2 }; // { a: 1, b: 2, c: 3 }
```

#### Rest Operator — Collects Values into an Array

The **rest operator** appears in a **function definition** (parameter list). It **collects** individual arguments into an **array**:

```javascript
function sum(...args) {      // 'args' is an array of all arguments
  let total = 0;
  for (let num of args) {
    total += num;
  }
  return total;
}

sum(1, 2, 3);          // args = [1, 2, 3], returns 6
sum(1, 2, 3, 4, 5);    // args = [1, 2, 3, 4, 5], returns 15
```

Or more concisely using reduce:
```javascript
function sum(...args) {
  return args.reduce((acc, curr) => acc + curr, 0);
}
```

#### Mixing Named Parameters with Rest

You can have named params AND a rest parameter — rest must be **last**:

```javascript
function display(first, second, ...others) {
  console.log("First:", first);
  console.log("Second:", second);
  console.log("Others:", others); // array of remaining args
}

display(1, 2, 3, 4, 5);
// First: 1
// Second: 2
// Others: [3, 4, 5]
```

> **Important:** The named parameters "consume" the first arguments. The rest `...others` collects whatever is left.

#### What If You Pass Extra Unnamed Args?

When you have named params but pass more arguments:
```javascript
function calculate(a, b) {
  // a = first arg, b = second arg
  // extra args are ignored unless you use ...rest
}

calculate(1, 2, 3, 4); // 3 and 4 are ignored — no rest param to collect them
```

#### Spread vs Rest — Same Syntax, Opposite Behavior

| | Spread `...` | Rest `...` |
|---|---|---|
| Where | Call site (invoking a function) | Function definition (parameter list) |
| What it does | **Expands** array/object into individual values | **Collects** individual values into array |
| Direction | Array → Individual | Individual → Array |

#### How Libraries Use These Internally

When you see `Math.max()`, `Object.assign()`, `Array.push()` — they all work with individual arguments internally. Libraries are written with these patterns:

```javascript
// How something like Math.max might work internally:
function myMax(...args) {
  return args.reduce((max, curr) => curr > max ? curr : max, -Infinity);
}

// Call it:
myMax(3, 1, 4, 1, 5, 9); // 9
myMax(...[3, 1, 4, 1, 5, 9]); // same thing, spread the array
```

### Coding Questions

**Q1:** What does this output?
```javascript
function greet(first, ...rest) {
  console.log("Hello, " + first);
  console.log("Also say hi to:", rest);
}

greet("Alice", "Bob", "Charlie");
```

**Answer:**
```
Hello, Alice
Also say hi to: ["Bob", "Charlie"]
```

---

**Q2:** Use the spread operator to find the minimum of these values:
```javascript
const numbers = [8, 3, 11, 2, 7];
const min = Math.min(...numbers);
console.log(min); // 2
```

---

**Q3:** Write a `multiply` function that takes a multiplier and then any number of values, and returns an array of each value multiplied:
```javascript
function multiply(factor, ...nums) {
  return nums.map(n => n * factor);
}

console.log(multiply(3, 1, 2, 3, 4)); // [3, 6, 9, 12]
```

---

**Q4:** Merge two objects using the spread operator:
```javascript
const user = { name: "Alice", age: 25 };
const extra = { role: "admin", active: true };

const merged = { ...user, ...extra };
console.log(merged);
// { name: "Alice", age: 25, role: "admin", active: true }
```

---

## 10. Currying

### Interview Question
**"What is currying in JavaScript? Why is it useful? Write a curried sum function."**

### Answer (Conversational)

**Currying** is the technique of converting a function that takes multiple arguments into a series of functions that each take **one argument at a time** and return a new function expecting the next argument.

In other words, converting `f(a, b, c)` into `f(a)(b)(c)`.

> Curried functions are built by **chaining closures** — each function closes over the previous arguments while returning a new function for the next one.

#### Basic Example

```javascript
// Normal function
function add(a, b) {
  return a + b;
}
add(3, 4); // 7

// Curried version
function add(a) {
  return function(b) {
    return a + b;
  };
}
add(3)(4); // 7 — called with one arg at a time
```

When you call `add(3)`, it **returns a function** that remembers `a = 3` (closure!). Then you call that returned function with `(4)` and get `3 + 4 = 7`.

#### Multiple Levels of Currying

For three arguments:

```javascript
function sum(a) {
  return function(b) {
    return function(c) {
      return a + b + c;
    };
  };
}

sum(2)(6)(1); // 9
```

Or using arrow functions (much shorter!):

```javascript
const sum = a => b => c => a + b + c;

sum(2)(6)(1); // 9
```

#### Why Use Currying?

1. **Avoid repeating the same argument** — initialize once, reuse many times
2. **Create higher-order functions** — create specialized versions of a function
3. **Make functions pure and less error-prone**

#### Interview Example: `evaluate` Function

```javascript
function evaluate(operation) {
  return function(a) {
    return function(b) {
      if (operation === "sum")      return a + b;
      if (operation === "multiply") return a * b;
      if (operation === "divide")   return a / b;
      if (operation === "subtract") return a - b;
      return "Invalid operation";
    };
  };
}

evaluate("sum")(4)(2);      // 6
evaluate("multiply")(4)(2); // 8

// Key benefit: create reusable specialized functions!
const mul = evaluate("multiply"); // pre-configured multiplier
mul(3)(5);  // 15
mul(2)(6);  // 12
// No need to re-specify "multiply" every time!
```

#### Infinite Currying (Advanced Interview Question)

The interviewer might ask: "Write an `add` function that can be called with **any number** of arguments like `add(1)(2)(3)(4)()`":

```javascript
function add(a) {
  return function(b) {
    if (b !== undefined) {
      return add(a + b); // recurse with updated sum
    }
    return a; // empty call () ends the chain
  };
}

add(5)(2)(4)(8)(); // 19
//    5  →  7  →  11  → 19  → returns 19
```

How it works:
- `add(5)` → returns function, `a = 5`
- `(2)` → `b = 2`, not undefined → returns `add(5 + 2) = add(7)`
- `(4)` → `b = 4`, not undefined → returns `add(7 + 4) = add(11)`
- `(8)` → `b = 8`, not undefined → returns `add(11 + 8) = add(19)`
- `()` → `b = undefined` → returns `19`

#### Currying vs Partial Application

These are similar but different:

```javascript
// True currying: 3 args → 3 nested functions (one arg per function)
const curriedSum = a => b => c => a + b + c;
curriedSum(1)(2)(3); // ✓ curried

// Partial application: 3 args but only 2 nested functions
const partialSum = a => (b, c) => a + b + c;
partialSum(1)(2, 3); // partial application — accepts multiple args in second call
```

In **currying**, the number of nested functions = number of arguments.
In **partial application**, a function with N args is converted to a function with fewer args (smaller arity).

#### Real-World DOM Example

Currying is useful in web development to avoid repeating the same ID selector:

```javascript
function updateElementText(id) {
  return function(content) {
    document.querySelector("#" + id).textContent = content;
  };
}

// Initialize once with the element's ID
const updateHeader = updateElementText("heading");

// Now call it with just the content anytime!
updateHeader("Hello World");    // updates heading
updateHeader("New Heading");    // updates heading again
// No need to re-specify "#heading" every time!
```

#### Implement a `curry()` Utility Function (Senior-Level)

This converts any normal function into a curried function automatically:

```javascript
function curry(fn) {
  return function curriedFn(...args) {
    if (args.length >= fn.length) {
      return fn(...args); // enough args collected → call original fn
    } else {
      return function(...nextArgs) {
        return curriedFn(...args, ...nextArgs); // collect more args recursively
      };
    }
  };
}

// Normal function
function sum(a, b, c, d) {
  return a + b + c + d;
}

const totalSum = curry(sum);

totalSum(1)(2)(3)(4);   // 10
totalSum(1, 2)(3)(4);   // 10 — can also pass multiple args
totalSum(1)(2, 3)(4);   // 10 — flexible!
```

How it works:
- `fn.length` = number of parameters in the original function
- If enough args have been collected (`args.length >= fn.length`) → call the original function
- Otherwise → return another function that collects the next batch of args

### Coding Questions

**Q1:** Write a curried `multiply` function: `multiply(3)(4)` should return `12`.
```javascript
function multiply(a) {
  return function(b) {
    return a * b;
  };
}

console.log(multiply(3)(4)); // 12
```

---

**Q2:** Convert this to an arrow function:
```javascript
function power(base) {
  return function(exponent) {
    return base ** exponent;
  };
}
```

**Answer:**
```javascript
const power = base => exponent => base ** exponent;

power(2)(10); // 1024
```

---

**Q3:** Implement the `evaluate` function that supports: `evaluate("add")(5)(3)` → 8, `evaluate("multiply")(5)(3)` → 15

```javascript
const evaluate = operation => a => b => {
  if (operation === "add")      return a + b;
  if (operation === "multiply") return a * b;
  if (operation === "subtract") return a - b;
  if (operation === "divide")   return a / b;
};

evaluate("add")(5)(3);       // 8
evaluate("multiply")(5)(3);  // 15
```

---

**Q4:** What is the difference between currying and partial application?

**Answer:**
- **Currying**: N arguments → N nested functions (each accepting exactly one argument)
- **Partial application**: N arguments → function with fewer arguments (smaller arity, but second function can accept multiple args)

---

## 11. Debounce and Throttle

### Interview Question
**"What are debounce and throttle? When would you use each? Can you implement them?"**

### Answer (Conversational)

#### The Problem: Too Many Function Calls

Some events fire **extremely frequently**:
- Typing in a search box: fires on every keystroke
- Moving the mouse: fires hundreds of times per second
- Scrolling: fires constantly while scrolling

If your event handler makes API calls or does heavy computation, calling it 100+ times per second is:
- **Expensive** for the user (burns their data)
- **Expensive** for your server (100x more requests)
- **Slow** for slow devices

**Debounce** and **Throttle** are techniques to **limit** how often a function can run.

---

#### Debounce — Wait Until the User is Done

**Debounce** delays calling the function until there's been a **pause** in activity. It says: *"I'll only call you after X milliseconds of silence."*

Every time the function is called, it **resets the timer**. If it's called again before the timer fires, it resets again. The function only actually runs when the timer finally completes without being reset.

**Best for:** Search/autocomplete — wait until the user stops typing, then make one API call.

```
User types: "M" ... "a" ... "t" ... "r" ... "i" ... "x"
Debounce:   (timer resets each keystroke)            fires once → "Matrix"
Default:    fires 6 times (one per character)
```

**Debounce Implementation:**

```javascript
function debounce(callback, delay = 1000) {
  let timeout; // stores the current timer

  return function(...args) {
    clearTimeout(timeout); // cancel the previous timer
    timeout = setTimeout(() => {
      callback(...args); // run callback after delay
    }, delay);
  };
}

// Usage:
const handleSearch = debounce(function(text) {
  console.log("Searching for:", text);
}, 500);

// Attach to input event
input.addEventListener("input", (e) => handleSearch(e.target.value));
// Now the API call only fires 500ms after the user stops typing!
```

**How debounce works step by step:**
1. User types "M" → `clearTimeout` cancels any existing timer → new 500ms timer starts
2. User types "a" (before 500ms) → `clearTimeout` cancels that timer → new 500ms timer starts
3. ... (repeat for each keystroke)
4. User stops typing for 500ms → timer expires → `callback("Matrix")` runs **once**

---

#### Throttle — Rate-Limit the Function

**Throttle** lets the function run **immediately** on the first call, then prevents it from running again for a set period. It says: *"Run now, but then wait X milliseconds before allowing another run."*

Unlike debounce, throttle doesn't wait until the user is done — it fires at regular intervals while events are happening.

**Best for:** Mouse movement tracking, scroll events, window resize — situations where you want updates during the action, but not at 100% frequency.

```
User moves mouse constantly:
Default:  fires 1000 times in 10 seconds
Throttle: fires every second → 10 times in 10 seconds
```

**Throttle Implementation (Basic):**

```javascript
function throttle(callback, delay = 1000) {
  let shouldWait = false;

  return function(...args) {
    if (shouldWait) return; // still in waiting period, skip this call

    callback(...args);      // run immediately
    shouldWait = true;      // start waiting period

    setTimeout(() => {
      shouldWait = false;   // waiting period over
    }, delay);
  };
}

// Usage:
const handleMouseMove = throttle(function(e) {
  console.log("Mouse at:", e.clientX, e.clientY);
}, 100);

document.addEventListener("mousemove", handleMouseMove);
// Now mouse tracking only logs 10 times per second max (every 100ms)
```

**Advanced Throttle (Saves Last Call):**

The basic version misses the "last" event (e.g., final position of scroll). The advanced version saves the last set of arguments received during the wait period and fires them when the wait ends:

```javascript
function throttle(callback, delay = 1000) {
  let shouldWait = false;
  let waitingArgs = null;

  const timeoutFunc = () => {
    if (waitingArgs == null) {
      shouldWait = false; // no pending calls, just stop waiting
    } else {
      callback(...waitingArgs); // fire with the last saved arguments
      waitingArgs = null;
      setTimeout(timeoutFunc, delay); // restart the timer
    }
  };

  return function(...args) {
    if (shouldWait) {
      waitingArgs = args; // save this call for later
      return;
    }
    callback(...args);    // run immediately
    shouldWait = true;
    setTimeout(timeoutFunc, delay);
  };
}
```

This ensures you never miss the "final" state — the last value during the wait period gets executed after the delay.

---

#### Debounce vs Throttle — Side by Side

| Feature | Debounce | Throttle |
|---|---|---|
| When it fires | After a **pause** in activity | **Immediately**, then rate-limited |
| Pattern | "Wait until done" | "Fire at regular intervals" |
| Good for | Search input, form auto-save | Scroll, resize, mouse move |
| Fires during continuous events? | No — resets timer each time | Yes — fires every N ms |
| Fires after activity stops? | Yes — once after final pause | Not guaranteed |

#### Practical Comparison

```javascript
// Mouse move event (move mouse continuously for 10 seconds):
// default:  called ~1,000 times
// debounce: called 1 time (after you stop moving)
// throttle: called ~10 times (once every second)
```

### Coding Questions

**Q1:** When would you use debounce vs throttle for:
- a) Search autocomplete as user types
- b) Updating a map as user drags a window
- c) Auto-saving a document while user edits

**Answer:**
- a) **Debounce** — wait until user stops typing, then make one API call
- b) **Throttle** — update the map at a regular interval while dragging (but not every pixel)
- c) **Debounce** — wait for a pause in editing, then save

---

**Q2:** What will this output and when?
```javascript
const debouncedLog = debounce((msg) => console.log(msg), 500);

debouncedLog("A");
debouncedLog("B");
debouncedLog("C");
```

**Answer:** Only `"C"` is logged, after 500ms. Each call to `debouncedLog` resets the timer. Only the last call (`"C"`) survives.

---

**Q3:** Implement a simple `throttle` function that ensures `fn` can only be called once every `n` milliseconds:

```javascript
function throttle(fn, n) {
  let lastCall = 0;
  return function(...args) {
    const now = Date.now();
    if (now - lastCall >= n) {
      lastCall = now;
      fn(...args);
    }
  };
}

const throttled = throttle(() => console.log("fired"), 1000);
// No matter how many times you call throttled(), it only logs "fired" once per second
```

---

**Q4:** Why is the default behavior (no debounce/throttle) a problem for API calls on search inputs?

**Answer:** Each keystroke triggers a new API request. Typing "Matrix" (6 characters) = 6 API requests, all potentially coming back out of order with overlapping stale data. It wastes the user's bandwidth, spams the server, and can cause race conditions. Debouncing limits it to 1 request per pause in typing.

---

## Summary Cheat Sheet

| Concept | Key Point |
|---|---|
| Function Declaration | `function foo() {}` — fully hoisted, can call before definition |
| Function Expression | `const foo = function() {}` — NOT hoisted as function, can't call before |
| Anonymous Function | No name; cannot be standalone, used as values |
| Named Function Expression | `const x = function y() {}` — `y` only accessible inside function |
| Parameters | Labels in function definition |
| Arguments | Values passed at call time |
| First-Class Functions | Functions = values; can assign, pass, return |
| Callback | Function passed as argument to another function |
| Synchronous callback | Runs immediately (map, filter, sort) |
| Asynchronous callback | Runs later (setTimeout, fetch, event listeners) |
| Arrow function | Shorter syntax, implicit return, lexical `this` |
| Arrow `this` | Inherits from surrounding scope (doesn't define own `this`) |
| Default parameter | `fn(x = 5)` — `undefined` triggers it, `null` does NOT |
| Spread `...` | At call site, expands array → individual values |
| Rest `...` | In params, collects individual values → array |
| Currying | `f(a, b)` → `f(a)(b)` — one argument at a time |
| Partial application | N args → fewer args (some args pre-filled) |
| Currying benefit | Reuse pre-configured functions |
| Infinite currying | `add(1)(2)(3)()` — empty call ends chain |
| `curry(fn)` utility | Converts any fn to curried version using `fn.length` |
| Debounce | Wait until X ms of silence, then fire once |
| Throttle | Fire immediately, then rate-limit to once per X ms |
| Debounce use case | Search input, auto-save |
| Throttle use case | Scroll, resize, mouse tracking |
