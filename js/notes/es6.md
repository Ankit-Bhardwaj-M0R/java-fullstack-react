


# ES6 JavaScript — Complete Interview Q&A Guide
> **For Beginners | Based 100% on the Transcript**
> Covers all 7 topics: `let/const/TDZ` · `Block Scope & Shadowing` · `Destructuring` · `CommonJS vs ES6 Modules` · `import vs require` · `Generators` · `Symbols & Iterables`

---

# 📌 TOPIC 1 — `let`, `const`, and the Temporal Dead Zone (TDZ)

---

## Q1. Are `let` and `const` hoisted in JavaScript?

**Yes — and this is one of the most asked interview questions.**

Many people say "let and const are NOT hoisted." That answer is **wrong**. The correct answer is: they ARE hoisted, but they behave very differently from `var`.

Here's what happens behind the scenes before any code runs:

- JavaScript scans through your entire code first and **allocates memory** for all variables — `var`, `let`, and `const`. This memory allocation is called **hoisting**.
- `var` gets stored in the **global object** (which is `window` in a browser) and is **immediately given the value `undefined`**. That's why you can access it before its line runs.
- `let` and `const` are stored in a **completely separate, sacred memory space** — NOT on the `window` object. And crucially, **no value is assigned to them yet**. So if you try to access them before their initialization line, JavaScript throws an error.

```js
console.log(a); // ❌ ReferenceError: Cannot access 'a' before initialization
let a = 10;

console.log(b); // ✅ undefined — no error
var b = 10;
```

> **Best interview answer:** "Yes, `let` and `const` are hoisted, but they are placed in a separate memory space and are in the **Temporal Dead Zone** until the line where they are initialized runs."



## Q2. What is the Temporal Dead Zone (TDZ)?

The **Temporal Dead Zone** is the time gap between when a `let` or `const` variable is **hoisted** (memory is reserved for it) and when it is actually **initialized** (assigned a value) in your code.

During this gap:
- The variable **exists in memory** (hoisting has happened)
- But you **cannot read or use it**
- If you try, you get a `ReferenceError` with the message: *"Cannot access 'a' before initialization"*

```js
// ← TDZ starts here for 'a' (memory reserved but no value)
console.log(a); // ❌ ReferenceError — still in TDZ
let a = 10;     // ← TDZ ends here — 'a' now has value 10
console.log(a); // ✅ 10
```

The transcript shows this in the **browser debugger** — you can literally see the variable `a` sitting in a separate "script" scope with no value, while `b` (declared with `var`) is already available on the `window` object with `undefined`.

> **Key takeaway:** TDZ is the phase from hoisting until initialization. Accessing a variable in TDZ gives a `ReferenceError`.

> **Best practice:** Always declare and initialize your variables **at the top of your code** so you never accidentally hit the TDZ.

---

## Q3. What is the difference between `var`, `let`, and `const`?

This is probably **the most frequently asked JavaScript interview question**.

### `var`
- Stored in the **global `window` object**
- Accessible before its line runs — gives `undefined` (no error)
- Can be **re-declared** in the same scope with no error
- Is **function-scoped**, not block-scoped

### `let`
- Stored in a **separate "script" memory space** — NOT on `window`
- Has a **Temporal Dead Zone** — cannot access before initialization
- **Cannot be re-declared** in the same scope → gives `SyntaxError`
- Is **block-scoped**

### `const`
- Same as `let` in most ways, but **even stricter**
- **Must be initialized at the point of declaration** — `const a;` alone gives `SyntaxError`
- **Cannot be re-assigned** after initialization → gives `TypeError`
- **Cannot be re-declared**
- Is **block-scoped**

```js
// var can be re-declared — no problem
var b = 10;
var b = 100; // ✅ totally fine

// let cannot be re-declared
let a = 10;
let a = 20; // ❌ SyntaxError: Identifier 'a' has already been declared

// const must be initialized immediately
const c;     // ❌ SyntaxError: Missing initializer in const declaration

// const cannot be re-assigned
const d = 5;
d = 10;      // ❌ TypeError: Assignment to constant variable

// let CAN be declared first, then assigned later
let x;
x = 10; // ✅ this is fine with let
// but NOT with const:
const y;
y = 10; // ❌ SyntaxError
```

> **Transcript recommendation:** Use `const` whenever possible. If the value needs to change, use `let`. Avoid `var` in modern code because it leads to unexpected bugs.

---

## Q4. What is the difference between `SyntaxError`, `ReferenceError`, and `TypeError`?

The transcript explains these three error types clearly. Knowing them impresses interviewers.

### `SyntaxError` — "Your code structure is wrong"
This happens **before any code runs at all**. JavaScript reads your code, finds a problem with the grammar/structure, and refuses to execute even a single line.

```js
const a;         // ❌ SyntaxError: Missing initializer in const declaration
let a = 10;
let a = 20;      // ❌ SyntaxError: Identifier 'a' has already been declared
```

### `ReferenceError` — "I can't find that variable"
This happens when you try to access a variable that either **doesn't exist** or is **in the Temporal Dead Zone**.

```js
console.log(x);  // ❌ ReferenceError: x is not defined  (x was never declared)

console.log(a);  // ❌ ReferenceError: Cannot access 'a' before initialization  (TDZ)
let a = 10;
```

Notice the **different messages**: "not defined" = variable doesn't exist anywhere. "before initialization" = variable is in TDZ.

### `TypeError` — "You're doing something illegal with this value"
This happens when you try to perform an operation that **isn't allowed for that type of value**.

```js
const a = 10;
a = 20;  // ❌ TypeError: Assignment to constant variable
```

> **Memory trick:** `SyntaxError` = code never even starts. `ReferenceError` = can't find the variable. `TypeError` = found it, but what you're doing with it is illegal.

---

## Q5. Is `var` attached to the `window` object? What about `let` and `const`?

Yes — `var` declarations in the global scope are **attached to the `window` object**. You can access them as `window.b`.

`let` and `const` are **NOT on the `window` object**. They live in a separate memory space. Even after you initialize them, `window.a` gives you `undefined`.

```js
var b = 10;
console.log(window.b);  // ✅ 10

let a = 10;
console.log(window.a);  // ❌ undefined — not on window
```

The transcript demonstrates this live in the browser console — you can type `window.b` and see `10`, but `window.a` returns `undefined` as if the variable doesn't exist.

---

# 📌 TOPIC 2 — Block Scope & Shadowing

---

## Q6. What is a block in JavaScript?

A **block** is any code wrapped in curly braces `{ }`. It is also called a **compound statement**.

The whole purpose of a block is simple: **group multiple statements together so they can be used where JavaScript expects only one statement**.

For example, an `if` statement can only accept ONE statement after it. If you want to run multiple lines inside an `if`, you wrap them in a block:

```js
// This breaks — if expects one statement, not two separate lines
if (true) let x = 10; console.log(x); // ❌ SyntaxError

// This works — block groups them into one compound statement
if (true) {
  let x = 10;      // \
  console.log(x);  //  → grouped into ONE block
}                  // ✅
```

The same logic applies to `for` loops, `while` loops, function bodies — they all use blocks.

> **Interview answer:** "A block is a pair of curly braces `{}` that groups multiple JavaScript statements into one. It's also called a compound statement. It lets us use multiple statements where JavaScript expects only one."

---

## Q7. What is Block Scope? How does it work with `var`, `let`, and `const`?

**Block scope** means that variables declared inside a block `{ }` are **only accessible inside that block**. Once you step outside, they're gone.

But — and this is crucial — **only `let` and `const` respect block scope. `var` does NOT.**

The transcript shows this using the browser debugger:
- When execution is paused inside a block, you can see `let` and `const` variables appear under **"Block" scope** in the developer tools
- `var` appears under **"Global" scope** — meaning it escaped the block entirely

```js
{
  var a = 10;    // goes to GLOBAL scope — accessible anywhere
  let b = 20;    // stays inside this block — dies when block ends
  const c = 30;  // stays inside this block — dies when block ends
}

console.log(a);  // ✅ 10 — var leaked out of the block
console.log(b);  // ❌ ReferenceError: b is not defined
console.log(c);  // ❌ ReferenceError: c is not defined
```

> **Interview answer:** "Block scope means variables are only accessible inside the `{ }` they were declared in. `let` and `const` are block-scoped. `var` is not — it's function-scoped or global."

---

## Q8. What is Shadowing in JavaScript?

**Shadowing** happens when you declare a variable inside a block with the **same name** as a variable in an outer scope. The inner variable "shadows" (hides) the outer one **only inside that block**.

### Shadowing with `var` — dangerous!

```js
var a = 100;

{
  var a = 10;     // this SHADOWS the outer 'a'
  console.log(a); // 10
}

console.log(a);   // 10 — wait, the outer 'a' also changed!
```

Why did the outer `a` change? Because **both `var a` declarations point to the same memory location** (the global scope). There's only one `a` in memory. Changing it inside the block changes it everywhere.

The transcript shows this in the debugger — when `var a = 10` executes inside the block, you can watch the global `a` change to `10` in real time.

### Shadowing with `let` — safe!

```js
let b = 100;

{
  let b = 20;     // this creates a NEW 'b' in block scope
  console.log(b); // 20
}

console.log(b);   // 100 — outer 'b' is UNCHANGED ✅
```

With `let`, the inner `b` and outer `b` are **two completely separate variables in two different memory spaces**. The transcript shows both appearing in the debugger — one under "Block" scope, one under "Script" scope.

`const` works exactly like `let` for shadowing — it creates a separate variable in block scope.

---

## Q9. What is Illegal Shadowing?

**Illegal shadowing** is when you try to shadow a `let` variable using `var` inside a block. JavaScript will NOT allow this.

```js
let a = 10;

{
  var a = 20;  // ❌ SyntaxError: Identifier 'a' has already been declared
}
```

**Why is this illegal?** Because `var` is function-scoped, not block-scoped. When you declare `var a` inside a block, it tries to exist in the outer (function or global) scope. But `let a` is already there. And `let` doesn't allow re-declaration in the same scope — so there's a conflict.

### What IS allowed:

```js
// ✅ Shadow var with var — fine
var a = 10;
{ var a = 20; }

// ✅ Shadow let with let — fine (creates a new block-scoped variable)
let b = 10;
{ let b = 20; }

// ✅ Shadow let with var INSIDE A FUNCTION — fine
// (because var is now scoped to the function, not the outer scope)
let c = 10;
function someFunction() {
  var c = 20; // ✅ no error — var stays inside this function's scope
}
```

The transcript explains the rule: **`var` must not cross the scope boundary where `let` already exists**. Inside a function, `var`'s boundary is the function itself — so it doesn't interfere with the outer `let`.

> **Rule to remember:** You can shadow `let` with `let`. You can shadow `var` with `var`. But you **cannot** shadow `let` with `var` in a block.

---

## Q10. Does Block Scope also follow Lexical Scope (Scope Chain)?

Yes! Block scope follows **lexical scope** — which means inner blocks can access variables from outer blocks (scope chain), but outer blocks cannot access variables from inner blocks.

```js
const a = 10;  // outer block

{
  {
    console.log(a); // ✅ 10 — inner block can access outer 'a'
  }
}
```

The transcript shows this in the browser debugger — each nested block has its own scope, and they're chained together. If the variable isn't found in the current block, JavaScript looks in the parent block, then its parent, and so on.

> **Bonus from the transcript:** Arrow functions and regular functions have **exactly the same scope rules**. There's no difference in scoping behavior. No need to learn separate rules for arrow functions.

---

# 📌 TOPIC 3 — Destructuring Assignment (Array & Object)

---

## Q11. What is Destructuring in JavaScript?

**Destructuring** is an ES6 feature that lets you **unpack values from an array or properties from an object into individual variables** — in one clean line.

Without destructuring (old way):
```js
const a = alphabet[0];
const b = alphabet[1];
```

With destructuring (ES6 way):
```js
const [a, b] = alphabet;
```

The idea is: take an array or object, and "break it apart" into smaller variables. The transcript describes it as converting an array or object into "smaller objects, smaller elements, or smaller variables."

> **Why it matters:** This is absolutely crucial for React development and modern JavaScript. It makes code significantly cleaner and easier to read.

---

## Q12. How does Array Destructuring work? Show all the examples from the video.

Array destructuring uses **square brackets `[ ]`** on the left side. Position in the array determines which variable gets which value.

### Basic destructuring:
```js
const alphabet = ['a', 'b', 'c', 'd', 'e', 'f'];

const [a, b] = alphabet;
console.log(a); // 'a'
console.log(b); // 'b'
```

### Skipping elements (leave a blank comma):
```js
const [a, , c] = alphabet;
// a = 'a'  (position 0)
// skipped 'b'  (position 1)
// c = 'c'  (position 2)
```

### Using the spread operator `...` to get the rest:
```js
const [a, , c, ...rest] = alphabet;
// a = 'a'
// skipped 'b'
// c = 'c'
// rest = ['d', 'e', 'f']  ← an array of everything remaining
```

### Combining two arrays with spread:
```js
const newArray = [...alphabet, ...numbers];
// combines both arrays into one — similar to concat()
```

### With functions that return multiple values:
```js
function sumAndMultiply(a, b) {
  return [a + b, a * b];
}

const [sum, multiply] = sumAndMultiply(2, 3);
console.log(sum);      // 5
console.log(multiply); // 6
```

### Setting default values:
If a function returns fewer values than you're destructuring, you can set a default:
```js
const [sum, multiply, division = 'no division'] = sumAndMultiply(2, 3);
console.log(division); // 'no division' — default kicks in since function only returns 2 values
```

---

## Q13. How does Object Destructuring work? Show all examples from the video.

Object destructuring uses **curly braces `{ }`** on the left. Unlike arrays, it matches by **key name**, not position.

### Basic object destructuring:
```js
const person = { name: 'Kyle', age: 25, favoriteFood: 'Pizza' };

const { name, age } = person;
console.log(name); // 'Kyle'
console.log(age);  // 25
```

### Renaming the variable (use a colon):
```js
const { name: firstName } = person;
console.log(firstName); // 'Kyle'
// The key 'name' is pulled from the object, but stored in variable 'firstName'
```

### Setting default values:
```js
const { favoriteFood = 'rice' } = person;
// If 'favoriteFood' exists → use it. If not → use 'rice' as default
```

### Using spread to get remaining properties:
```js
const { name, ...rest } = person;
// name = 'Kyle'
// rest = { age: 25, favoriteFood: 'Pizza' }
```

### Nested object destructuring:
```js
const person = { name: 'Kyle', address: { city: 'London', zip: 'SW1' } };

const { address: { city } } = person;
console.log(city); // 'London'
```

### Combining two objects with spread (merge):
```js
const person1 = { name: 'Kyle', age: 25 };
const person2 = { age: 30, job: 'Developer' };

const person3 = { ...person1, ...person2 };
// { name: 'Kyle', age: 30, job: 'Developer' }
// person2's 'age' overrides person1's 'age' because it comes second
```

### Most important use case — destructuring in function parameters:
The transcript calls this the **most important and useful part** of object destructuring.

```js
// Old way — pass whole object and access properties inside
function printUser(user) {
  console.log(user.name, user.age);
}

// ES6 way — destructure directly in the parameter
function printUser({ name, age }) {
  console.log(name, age);
}

printUser(person); // Same result, much cleaner code
```

This pattern is **everywhere in React** — component props are objects and you destructure them right in the function signature.

---

# 📌 TOPIC 4 — CommonJS Modules (`require`/`exports`) vs ES6 Modules (`import`/`export`)

---

## Q14. Why do we even need modules? What problem do they solve?

By default in JavaScript, every variable you create goes into the **global namespace** (the `window` object in a browser). This is a major problem:

```js
// main.js
var a = "hello";
// Now window.a = "hello" — visible to EVERYTHING
```

In a large app with hundreds of files and thousands of variables, this means:
- You might accidentally **override** another variable with the same name
- When **working with other developers**, two people might use the same variable name and one silently overwrites the other
- Your code becomes almost **impossible to maintain**

**The solution: Modules.**

The key idea of any module system is: **every file is an isolated module by default**. Nothing is shared unless you explicitly choose to export it.

### Historical solution before modules — IIFE (Immediately Invoked Function Expression):

The transcript explains this was the first solution people used:

```js
// Wrap everything in a self-executing function
(function() {
  var a = "hello"; // 'a' is NOT on window — safe inside the function
  console.log(a);
})();

// Now window.a is undefined — not polluted
```

But this doesn't scale. If you have 50 files, you can't easily share variables between them using IIFEs. That's why **CommonJS modules** were invented.

---

## Q15. What are CommonJS Modules? How do `require` and `module.exports` work?

CommonJS is the module system used by **Node.js**. It's been around for a long time and you'll still see it everywhere in backend JavaScript.

### Exporting from a file (foo.js):
```js
// foo.js
function getFullName(name, surname) {
  return name + ' ' + surname;
}

module.exports = getFullName;
// Now getFullName is available to any file that requires foo.js
```

If you don't write `module.exports`, the file is **completely isolated** — nothing leaks out.

### Importing in another file (bar.js):
```js
// bar.js
const getFullName = require('./foo'); // relative path to foo.js

console.log(getFullName('Monster', 'Lessons')); // "Monster Lessons"
```

### Exporting multiple things:
```js
// foo.js — two functions
function getFullName(name, surname) { return name + ' ' + surname; }
function getSurname(name, surname) { return surname; }

// Option 1: export as an object
module.exports = { getFullName, getSurname };

// Option 2: use exports.something directly (same result)
exports.getFullName = getFullName;
exports.getSurname = getSurname;
```

### Importing multiple things — use destructuring:
```js
// bar.js
const { getFullName, getSurname } = require('./foo');
// Much cleaner — you get individual functions directly, not a module object
```

### Key feature: `require()` can go INSIDE conditions or functions:
```js
if (1 === 1) {
  const foo = require('./foo'); // ✅ totally valid in CommonJS
  console.log(foo.getFullName('Monster', 'Lessons'));
}
```

This works because **`require()` is just a regular JavaScript function**. It runs at the exact moment that line of code is reached.

> **Note from the transcript:** CommonJS modules do NOT exist natively in the browser. If you see `require()` in browser code, some library is emulating it (like Webpack or Browserify).

---

## Q16. What are ES6 Modules? How do `import` and `export` work?

ES6 modules are the **modern, official** JavaScript module system — supported natively in all major browsers and modern Node.js.

### Exporting a single default thing:
```js
// es6foo.js
export default function getFullName(name, surname) {
  return name + ' ' + surname;
}
```

### Importing a default export:
```js
// es6bar.js
import getFullName from './es6foo.js'; // must include .js extension in browsers!

console.log(getFullName('Monster', 'Lessons'));
```

### Exporting multiple named things:
```js
// es6foo.js
export const getFullName = (name, surname) => name + ' ' + surname;
export const getSurname = (name, surname) => surname;
```

### Importing named exports (uses destructuring syntax):
```js
import { getFullName, getSurname } from './es6foo.js';
```

### Using in HTML — must add `type="module"`:
```html
<script src="es6bar.js" type="module"></script>
```

> **Important note from the transcript:** ES6 modules require an **HTTP server** — they won't work if you just open an HTML file directly from your filesystem (you'll get a CORS error). Use a tool like `npx serve` to run a local server.

---

## Q17. What is the main difference between `require` (CommonJS) and `import` (ES6)?

This is a **very popular interview question**. The transcript covers this in detail.

| Feature | `require` (CommonJS) | `import` (ES6) |
|---|---|---|
| **Type** | Just a regular function | A language keyword |
| **When it runs** | At runtime — when that line executes | At parse time — before any code runs |
| **Can it go inside `if`/functions?** | ✅ Yes | ❌ No — must be at the top level |
| **Is it hoisted?** | ❌ No — runs exactly where you write it | ✅ Yes — all imports move to the top automatically |
| **Path must be a string literal?** | ❌ No — can be dynamic: `require('./dir/' + name)` | ✅ Yes — path must be a fixed string |
| **Works natively in browser?** | ❌ No | ✅ Yes |
| **Static analysis possible?** | ❌ No | ✅ Yes — TypeScript and tools can check at parse time |

### The most important difference — Static vs Dynamic:

`require` is **dynamic** (runs at runtime):
```js
// This works in CommonJS
if (someCondition) {
  const module = require('./someFile');
}
```

`import` is **static** (analyzed before code runs):
```js
// This BREAKS in ES6 modules
if (someCondition) {
  import something from './someFile'; // ❌ SyntaxError
}
```

**Why does static matter?** Because tools like TypeScript, ESLint, and bundlers can **read and understand your imports before executing any code**. This gives you:
- Errors caught at compile time, not runtime
- Better IDE autocomplete
- Ability to do **tree-shaking** (removing unused code from the final bundle)

> **Summary:** `require` is just a function — dynamic, flexible, runs at runtime. `import` is a language keyword — static, strict about placement, analyzed at parse time.

---

## Q18. Why does Node.js still use CommonJS if ES6 modules are better?

The transcript answers this directly: **it's extremely difficult to change**.

Node.js has a **massive codebase** that has been built over many years using CommonJS. Switching everything to ES6 modules would break thousands of packages and applications that depend on the existing behavior.

There are tools that let you **write** ES6 module syntax in Node.js, but they transpile (convert) it to CommonJS under the hood.

---

# 📌 TOPIC 5 — `import` vs `require` — Deeper Dive

---

## Q19. Why was `require` synchronous and why does that matter?

`require()` is **synchronous** — it fully loads and executes the required file before moving to the next line.

This is fine for Node.js (server-side) because all files are on the local disk and load almost instantly.

But in a **browser**, files are loaded over the internet. If you used synchronous loading in the browser, the entire page would **freeze** while waiting for each module to download. That's why the browser needed a different approach — and that's why ES6 `import` is **asynchronous**.

---

## Q20. What is the Dual Package Hazard?

This is a real problem the transcript explains: if someone uses **both** `require()` AND `import` on the same package in the same project, they end up loading **two separate copies** of that package's code into memory.

This can cause all kinds of weird bugs, especially if the package has any shared state (like a configuration singleton).

---

## Q21. What role do bundlers play in the import vs require debate?

Tools like **Babel**, **TypeScript**, and **Webpack** add complexity here.

You might be *writing* ES6 `import/export` syntax, but these tools might be *converting* (transpiling) it to CommonJS `require/exports` in the output.

> **Tip from the transcript:** If you want to know which module system you're actually using, look at your **built output code**. If you see `require()` calls, your bundler is emitting CommonJS. If you see `import/export`, you're using native ES modules.

---

## Q22. What is Bun and how does it relate to this?

**Bun** is a modern JavaScript runtime (like Node.js but faster). One of its main features is that it **solves the compatibility problem** between CommonJS and ES modules — you can use both `require()` and `import` in the same project without issues.

However, the transcript notes Bun achieves this in a way that is **not fully spec-compliant** — it patches things together in a non-standard way.

> **Recommendation from the transcript:** Even if you use Bun, you should still **prefer ES modules** everywhere you can. All versions of Node.js with long-term support now support ESM. The web has settled on one module system that works everywhere — use it.

---

# 📌 TOPIC 6 — Generators (`function*`, `yield`)

---

## Q23. What is a Generator function in JavaScript?

A **generator** is a special kind of function that can **pause itself in the middle** and **resume later from exactly where it stopped**.

A normal function runs from start to finish in one go. A generator can run a little, pause, give you a value, and then wait for you to ask for the next value.

You create a generator using `function*` (a star after the `function` keyword). Inside, you use `yield` to pause and return a value.

```js
function* simpleGenerator() {
  yield 1;  // pause here, return 1
  yield 2;  // pause here, return 2
  yield 3;  // pause here, return 3
}
```

Think of `yield` as a special `return` that remembers where it left off.

---

## Q24. How do you actually USE a generator? What is a generator object?

Calling a generator function **doesn't run any code**. It just creates and returns a **generator object**. The code only runs when you call `.next()` on that object.

```js
const generatorObject = simpleGenerator();
// Nothing printed yet — no code has run inside the generator
```

### Calling `.next()`:

Every `.next()` call runs the generator until the next `yield` and returns an object with two properties:
- `value` — the yielded value
- `done` — `false` if there's more code to run, `true` if the generator has finished

```js
console.log(generatorObject.next()); // { value: 1, done: false }
console.log(generatorObject.next()); // { value: 2, done: false }
console.log(generatorObject.next()); // { value: 3, done: false }
console.log(generatorObject.next()); // { value: undefined, done: true }
```

### How execution flows step by step:

The transcript adds `console.log` statements to show exactly what runs when:

```js
function* simpleGenerator() {
  console.log('before 1');
  yield 1;
  console.log('after 1, before 2');
  yield 2;
  console.log('after 2, before 3');
  yield 3;
  console.log('after 3');
}

const gen = simpleGenerator();

gen.next();
// Prints: "before 1"
// Returns: { value: 1, done: false }

gen.next();
// Prints: "after 1, before 2"
// Returns: { value: 2, done: false }

gen.next();
// Prints: "after 2, before 3"
// Returns: { value: 3, done: false }

gen.next();
// Prints: "after 3"
// Returns: { value: undefined, done: true }
```

### Multiple generator objects are independent:

```js
const gen1 = simpleGenerator();
const gen2 = simpleGenerator();

gen1.next(); // { value: 1, done: false }
gen1.next(); // { value: 2, done: false }

gen2.next(); // { value: 1, done: false } — starts fresh, independent of gen1
```

Each generator object is a **completely separate instance** — creating a new one doesn't affect existing ones.

---

## Q25. What are the practical use cases for generators?

### Use case 1: Safe infinite loops (like generating unique IDs)

In normal JavaScript, an infinite loop like `while(true)` will freeze your program. But with a generator, it only runs one step each time you call `.next()`, so you stay in control:

```js
function* generateId() {
  let id = 1;
  while (true) {       // ← infinite loop, but safe inside a generator!
    yield id;
    id++;
  }
}

const idGen = generateId();
console.log(idGen.next().value); // 1
console.log(idGen.next().value); // 2
console.log(idGen.next().value); // 3
// Can keep calling this forever — 'done' will always be false
```

To reset IDs, just create a new generator object:
```js
const freshIdGen = generateId(); // starts back at 1
```

### Use case 2: Creating iterators

Generators make it very easy to create an iterator from an array or any custom data:

```js
function* arrayIterator(arr) {
  for (let i = 0; i < arr.length; i++) {
    yield arr[i];
  }
}

const iter = arrayIterator([1, 3, 5]);
console.log(iter.next()); // { value: 1, done: false }
console.log(iter.next()); // { value: 3, done: false }
console.log(iter.next()); // { value: 5, done: false }
console.log(iter.next()); // { value: undefined, done: true }
```

This is especially useful because many JavaScript libraries expect iterators, not arrays.

---

## Q26. Can you pass a value INTO a generator through `.next()`?

Yes! You can pass a value as an argument to `.next(value)`. That value gets **returned by the `yield` expression** inside the generator.

```js
function* generateId() {
  let id = 1;
  while (true) {
    const increment = yield id;
    // 'increment' = whatever we passed to .next()
    if (increment != null) {
      id += increment;
    } else {
      id += 1;
    }
  }
}

const gen = generateId();
gen.next();    // { value: 1, done: false }
gen.next(4);   // { value: 5, done: false } — we passed 4, so id jumped by 4
gen.next(3);   // { value: 8, done: false } — jumped by 3
gen.next();    // { value: 9, done: false } — no argument, default +1
```

> **Important:** Passing a value to the **first** `.next()` call does nothing — there's no `yield` yet to receive it.

---

## Q27. What are the three methods on a generator object?

### `.next(value)`
Runs the generator until the next `yield`. Returns `{ value, done }`. Optionally sends a value back into the generator.

### `.return(value)`
Immediately **exits** the generator — even if there's more code to run. Sets `done: true` and returns the value you pass in.

```js
const gen = generateId();
gen.next(); // { value: 1, done: false }
gen.next(); // { value: 2, done: false }
gen.return(10); // { value: 10, done: true } — generator is now done
gen.next(); // { value: undefined, done: true } — stays done
```

### `.throw(error)`
Throws an error **inside** the generator at the current yield point. Useful for generators that need to handle errors internally.

```js
gen.throw(new Error('hi')); // throws "Error: hi" from inside the generator
```

The transcript notes `.throw()` is mainly useful when building libraries, but it's important to know it exists.

---

# 📌 TOPIC 7 — Symbol, Iterable Protocol, Iterator Protocol & Custom Iterables

---

## Q28. What is a Symbol in JavaScript?

A **Symbol** is a primitive data type introduced in ES6. Every Symbol is **completely unique** — no two symbols are ever equal, even if they have the same description.

```js
const sym1 = Symbol('id');
const sym2 = Symbol('id');

console.log(sym1 === sym2); // false — they are always unique
```

Symbols are mostly used as **unique property keys** on objects. This is especially useful for things like `Symbol.iterator` — if JavaScript had used a regular string like `"iterator"` as the key, any developer's code could accidentally overwrite it. Using a Symbol makes it impossible to conflict accidentally.

---

## Q29. What is the Iterable Protocol?

The **Iterable Protocol** is a set of rules that determines whether an object can be iterated over — meaning whether you can use it in a `for...of` loop or with the spread operator `...`.

**The rule:** For an object to be iterable, it must have a property with the key **`Symbol.iterator`**. The value of that property must be a **function** that returns an **iterator**.

```js
// Arrays are iterable because they have Symbol.iterator
const arr = [1, 2, 3];
console.log(arr[Symbol.iterator]); // ƒ values() { [native code] }

// Numbers are NOT iterable — no Symbol.iterator
const num = 12;
console.log([...num]); // ❌ TypeError: num is not iterable
```

When you write `for...of` or use `...`, JavaScript:
1. Looks for the `Symbol.iterator` property on the object
2. If found → calls that function to get an iterator
3. If not found → throws `TypeError: X is not iterable`

---

## Q30. What is the Iterator Protocol?

The **Iterator Protocol** defines what makes something an **iterator** (which is different from an iterable — these are two related but different things).

An **iterator** is an object that has a `.next()` method. Every time you call `.next()`, it must return an object with:
- `value` — the current item
- `done` — `true` if iteration is complete, `false` if there are more items

```js
const arr = [1, 2, 3];

// Step 1: Get the iterator from the iterable
const iterator = arr[Symbol.iterator]();

// Step 2: Call .next() manually (this is what for...of does for you)
console.log(iterator.next()); // { value: 1, done: false }
console.log(iterator.next()); // { value: 2, done: false }
console.log(iterator.next()); // { value: 3, done: false }
console.log(iterator.next()); // { value: undefined, done: true }
```

This works on strings too:
```js
const str = 'hello';
const iter = str[Symbol.iterator]();

iter.next(); // { value: 'h', done: false }
iter.next(); // { value: 'e', done: false }
// ...etc
```

> **Key distinction:**
> - **Iterable** = "I can be iterated" — has `Symbol.iterator` property
> - **Iterator** = "I define HOW to iterate" — has `.next()` method that returns `{ value, done }`
>
> When you use `for...of`, JavaScript gets the iterator from the iterable, then calls `.next()` repeatedly until `done: true`.

---

## Q31. How do you override a built-in iterator? (String example from the video)

The transcript shows a fun (but don't do this in production!) example of overriding the default String iterator to return `"nope"` for every character:

```js
String.prototype[Symbol.iterator] = function() {
  let count = this.length; // 'this' is the string being iterated

  return {
    next() {
      if (count > 0) {
        count--;
        return { value: 'nope', done: false }; // return 'nope' instead of the character
      } else {
        return { done: true }; // stop when we've gone through every character
      }
    }
  };
};

console.log([...'hello']); // ['nope', 'nope', 'nope', 'nope', 'nope']

for (let x of 'doggy') {
  console.log(x); // nope nope nope nope nope
}
```

This demonstrates that **the same protocol works everywhere** — whether you're overriding a built-in or creating your own from scratch.

> ⚠️ **Warning from the transcript:** Always make sure `done: true` is returned eventually. If it never is, `for...of` and `...` will call `.next()` forever, freezing your browser and "possibly ruining your life" (the instructor's words).

---

## Q32. How do you create a Custom Iterable from scratch?

The transcript builds a `range()` function — a custom iterable that generates numbers from a start to an end with a given step.

```js
function range(start = 1, end = 10, step = 1) {
  return {
    // ← This object is the ITERABLE (has Symbol.iterator)
    [Symbol.iterator]() {
      let current = start;

      return {
        // ← This returned object is the ITERATOR (has .next())
        next() {
          if (current <= end) {
            const result = { value: current, done: false };
            current += step; // advance to next value
            return result;
          } else {
            return { value: undefined, done: true }; // finished!
          }
        }
      };
    }
  };
}
```

### Usage:

```js
console.log([...range()]);           // [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
console.log([...range(1, 20, 5)]);   // [1, 6, 11, 16]
console.log([...range(20, 200, 20)]); // [20, 40, 60, 80, 100, 120, 140, 160, 180, 200]

for (const num of range(15, 50, 5)) {
  console.log(num); // 15, 20, 25, 30, 35, 40, 45, 50
}
```

### Why use an intermediate variable in `next()`?

```js
next() {
  if (current <= end) {
    const result = { value: current, done: false }; // save value BEFORE advancing
    current += step;                                  // now advance
    return result;                                    // return saved value
  }
  return { value: undefined, done: true };
}
```

If you tried to `return` and then do `current += step`, the advance would never happen (function exits on `return`). So you save the current value first, advance, then return the saved value.

---

## Q33. What is the difference between an Iterable and an Iterator?

| | **Iterable** | **Iterator** |
|---|---|---|
| What it is | An object that CAN be iterated | An object that DEFINES HOW to iterate |
| Key it has | `[Symbol.iterator]` method | `.next()` method |
| What it returns | An iterator (when `Symbol.iterator` is called) | `{ value, done }` (when `.next()` is called) |
| Example | Array, String, Set, Map | The object returned by `arr[Symbol.iterator]()` |

**Simple analogy:**
- **Iterable** = a book that can be read page by page
- **Iterator** = a bookmark tracking your current page

The book (iterable) can give you multiple independent bookmarks (iterators) — each tracking a different position.

---

# 📋 QUICK REFERENCE CHEAT SHEET

```
TOPIC 1 — let/const/TDZ
━━━━━━━━━━━━━━━━━━━━━━━━
✅ let and const ARE hoisted — but in a separate "sacred" memory space (NOT window)
✅ TDZ = time between hoisting and initialization — cannot access variable here
✅ var → accessible before init (gives undefined), stored on window
✅ let → TDZ before init, cannot re-declare in same scope, block-scoped
✅ const → like let, must init at declaration, cannot re-assign, block-scoped
✅ SyntaxError = code never runs | ReferenceError = can't find var | TypeError = illegal operation
✅ Best practice: use const > let > avoid var

TOPIC 2 — Block Scope & Shadowing
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ Block = { } = compound statement — groups multiple statements into one
✅ let/const → block-scoped (stay inside the block)
✅ var → NOT block-scoped (escapes to function/global scope)
✅ Shadowing = inner variable hides outer variable with same name inside the block
✅ var shadowing = dangerous — both point to same global memory
✅ let shadowing = safe — creates a new independent variable in block scope
✅ Illegal shadowing = trying to shadow let with var in a block → SyntaxError
✅ But: var INSIDE A FUNCTION can shadow outer let — that's fine
✅ Block scope follows lexical scope (scope chain)
✅ Arrow functions have the same scope rules as regular functions

TOPIC 3 — Destructuring
━━━━━━━━━━━━━━━━━━━━━━
✅ Array destructuring = uses [ ] = based on position
✅ Object destructuring = uses { } = based on key name
✅ Skip array elements with blank commas: const [a, , c] = arr
✅ Get remaining with spread: const [a, ...rest] = arr
✅ Rename object keys: const { name: firstName } = obj
✅ Set defaults: const [a = 'default'] = arr  or  const { x = 0 } = obj
✅ Nested objects: const { address: { city } } = person
✅ Combine objects: const merged = { ...obj1, ...obj2 } — later keys override earlier ones
✅ Most important: destructure in function parameters → const f = ({ name, age }) => {}

TOPIC 4 — CommonJS vs ES6 Modules
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ Problem modules solve: variables leaking into global scope
✅ IIFE was the first solution — wrap code in self-executing function
✅ CommonJS (Node.js): require() to import, module.exports to export
✅ ES6 Modules (browser + modern): import to import, export/export default to export
✅ require is dynamic — runs at runtime, can be inside if/functions
✅ import is static — analyzed at parse time, must be at top level
✅ import is hoisted — always moved to top | require runs where you write it
✅ import path must be a string literal | require path can be dynamic
✅ ES6 modules need type="module" in HTML and must be served over HTTP
✅ ES6 modules enable static analysis (TypeScript, tree-shaking, etc.)

TOPIC 5 — import vs require (deeper)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ require = synchronous (fine for Node disk I/O, bad for browser downloads)
✅ import = asynchronous (designed for browser use over network)
✅ Dual Package Hazard = using both require and import on same package → 2 copies in memory
✅ Bundlers (Babel/TypeScript) may convert import to require in the output
✅ Bun fixes CommonJS/ESM interop but in a non-spec-compliant way
✅ Recommendation: use ESM (import/export) everywhere you can

TOPIC 6 — Generators
━━━━━━━━━━━━━━━━━━━━
✅ function* = generator function | yield = pause and return a value
✅ Calling generator returns a generator object — no code runs yet
✅ .next() runs until next yield, returns { value, done }
✅ done: false = more to go | done: true = finished
✅ Multiple generator objects from same function = independent instances
✅ Infinite generators are safe — only run one step at a time
✅ Pass value to .next(val) → returned from yield inside generator
✅ First .next() call ignores any passed value
✅ .return(val) = exit generator immediately with that value
✅ .throw(err) = throw error inside generator at current yield

TOPIC 7 — Symbol / Iterable / Iterator Protocol
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ Symbol = unique primitive — Symbol('x') !== Symbol('x') always
✅ Iterable Protocol: object must have [Symbol.iterator] method returning an iterator
✅ Iterator Protocol: object must have .next() that returns { value, done }
✅ for...of and ... both: call Symbol.iterator to get iterator, then call .next() until done: true
✅ Built-in iterables: Array, String, Map, Set
✅ NOT iterable: plain numbers, plain objects {}
✅ Custom iterable: return object with [Symbol.iterator]() { return { next() { return {value, done} } } }
✅ ALWAYS return done: true eventually — or your program will freeze forever!
```

---

*End of ES6 Interview Q&A Guide — Sources: Namaste JavaScript & Web Dev Simplified*
```
