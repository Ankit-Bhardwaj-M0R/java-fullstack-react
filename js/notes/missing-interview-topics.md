# JavaScript — Missing Interview Topics Q&A Guide

> **Topics NOT covered in the other 6 notes files but commonly asked in interviews:**
> Fetch API · Axios · ES6 Classes · Template Literals · Optional Chaining & Nullish Coalescing · for...of vs for...in · Local Storage / Session Storage · Event Delegation · WeakMap & WeakSet · Object Utility Methods

---

## Table of Contents
1. [Fetch API](#1-fetch-api)
2. [Axios vs Fetch](#2-axios-vs-fetch)
3. [ES6 Classes](#3-es6-classes)
4. [Template Literals](#4-template-literals)
5. [Optional Chaining (?.) and Nullish Coalescing (??)](#5-optional-chaining--and-nullish-coalescing-)
6. [for...of vs for...in](#6-forof-vs-forin)
7. [Local Storage & Session Storage](#7-local-storage--session-storage)
8. [Event Delegation & Event Bubbling](#8-event-delegation--event-bubbling)
9. [WeakMap & WeakSet](#9-weakmap--weakset)
10. [Object Utility Methods](#10-object-utility-methods)

---

## 1. Fetch API

### Interview Question
**"What is the Fetch API? How do you make a GET and POST request using fetch()?"**

### Answer (Conversational)

The **Fetch API** is the modern, built-in browser way to make HTTP requests — like calling a REST API to get data from a server. It replaced the old `XMLHttpRequest` (XHR) approach, which was clunky and callback-heavy.

The golden rule: `fetch()` always returns a **Promise**. So you chain `.then()` on it or use `async/await`.

#### Making a GET Request

```javascript
// Basic GET request
fetch("https://api.example.com/users")
  .then(response => response.json())  // Step 1: parse JSON from the response
  .then(data => console.log(data))    // Step 2: use the data
  .catch(error => console.error(error));
```

Notice that `.json()` is itself **async** — it also returns a Promise. So you need two `.then()` chains. The first handles the Response object, the second handles the actual parsed data.

#### Making a POST Request

For POST, you pass a second argument — an options object — to `fetch()`:

```javascript
fetch("https://api.example.com/users", {
  method: "POST",
  headers: {
    "Content-Type": "application/json"
  },
  body: JSON.stringify({ name: "Alice", age: 25 }) // must be a string!
})
  .then(response => response.json())
  .then(data => console.log("Created:", data))
  .catch(error => console.error(error));
```

#### Using async/await (Cleaner Way)

```javascript
async function getUsers() {
  try {
    const response = await fetch("https://api.example.com/users");

    // Important: fetch does NOT throw on 4xx/5xx errors!
    // You must check response.ok manually
    if (!response.ok) {
      throw new Error(`HTTP error! Status: ${response.status}`);
    }

    const data = await response.json();
    console.log(data);
  } catch (error) {
    console.error("Failed:", error);
  }
}
```

#### The Classic Interview Gotcha: fetch doesn't reject on HTTP errors

This trips up a LOT of developers. If the server returns a `404` or `500`, **fetch does NOT throw an error**. The Promise still resolves — but with `response.ok === false`. You only get a rejection if there's a **network failure** (no internet, DNS failure, etc.).

```javascript
// This is WRONG — won't catch 404 or 500 errors!
fetch("https://api.example.com/notfound")
  .then(data => console.log("success")) // runs even on 404!
  .catch(err => console.log("error"));  // only runs on network failure

// This is CORRECT
fetch("https://api.example.com/notfound")
  .then(response => {
    if (!response.ok) throw new Error("Not OK: " + response.status);
    return response.json();
  })
  .then(data => console.log(data))
  .catch(err => console.error(err));
```

#### Response Methods

| Method | What it returns |
|---|---|
| `response.json()` | Parsed JSON as a JavaScript object |
| `response.text()` | Plain text |
| `response.blob()` | Binary data (images, files) |
| `response.ok` | `true` if status is 200–299 |
| `response.status` | HTTP status code (200, 404, 500...) |

### Coding Questions

**Q1:** What will happen when you run this code if the server returns a 404?
```javascript
fetch("https://api.example.com/missing")
  .then(res => res.json())
  .then(data => console.log("Got data:", data))
  .catch(err => console.log("Error caught:", err));
```

**Answer:** The `.catch()` will NOT fire on a 404. fetch only rejects on network errors. The `.then()` chain will run because the fetch technically "succeeded" at the network level. You'd get a JSON parsing error if the 404 response isn't JSON, or just unexpected data.

---

**Q2:** Write a reusable `fetchJSON(url)` function that handles errors properly.
```javascript
async function fetchJSON(url) {
  const response = await fetch(url);
  if (!response.ok) {
    throw new Error(`Request failed: ${response.status} ${response.statusText}`);
  }
  return response.json();
}

// Usage:
try {
  const users = await fetchJSON("https://api.example.com/users");
  console.log(users);
} catch (err) {
  console.error(err.message);
}
```

---

## 2. Axios vs Fetch

### Interview Question
**"What is Axios? How is it different from the Fetch API? When would you use one over the other?"**

### Answer (Conversational)

**Axios** is a third-party JavaScript library for making HTTP requests. You install it with `npm install axios`. Unlike `fetch`, it's not built into the browser — but it works in both the browser AND Node.js.

Think of Axios as "fetch with superpowers" — it handles a lot of the tedious boilerplate automatically.

#### Side-by-Side Comparison

**1. JSON parsing — Axios does it automatically**
```javascript
// With fetch — you do it manually:
const response = await fetch(url);
const data = await response.json(); // extra step

// With Axios — automatic:
const response = await axios.get(url);
const data = response.data; // already parsed!
```

**2. Error handling — Axios throws on 4xx/5xx automatically**
```javascript
// With fetch — you must check response.ok manually:
const res = await fetch(url);
if (!res.ok) throw new Error("HTTP error: " + res.status);

// With Axios — it throws automatically on 4xx/5xx:
try {
  const res = await axios.get(url);
} catch (error) {
  console.log(error.response.status); // 404, 500, etc.
}
```

**3. Sending JSON body — Axios serializes it automatically**
```javascript
// With fetch — you must stringify + set Content-Type manually:
fetch(url, {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ name: "Alice" })
});

// With Axios — just pass the object:
axios.post(url, { name: "Alice" }); // that's it!
```

**4. Interceptors — Axios's killer feature**

Interceptors let you run code before every request goes out (e.g., attach auth tokens) or before every response is handled (e.g., log errors globally). Fetch has no built-in way to do this.

```javascript
// Add Authorization token to every request automatically:
axios.interceptors.request.use(config => {
  config.headers.Authorization = `Bearer ${localStorage.getItem("token")}`;
  return config;
});

// Handle 401 Unauthorized globally:
axios.interceptors.response.use(
  response => response,
  error => {
    if (error.response.status === 401) {
      // redirect to login
      window.location = "/login";
    }
    return Promise.reject(error);
  }
);
```

**5. Request cancellation**
```javascript
// Fetch uses AbortController (covered in Async notes)
const controller = new AbortController();
fetch(url, { signal: controller.signal });
controller.abort();

// Axios has its own cancellation (also uses AbortController internally now)
const controller = new AbortController();
axios.get(url, { signal: controller.signal });
controller.abort();
```

#### Feature Comparison Table

| Feature | `fetch` | `axios` |
|---|---|---|
| Built-in (no install) | Yes | No (npm install) |
| Works in Node.js | Only with polyfill | Yes, natively |
| Auto JSON parsing | No | Yes |
| Throws on 4xx/5xx | No | Yes |
| Auto serialize body | No | Yes |
| Interceptors | No | Yes |
| Request timeout | Manual | Built-in |
| Progress events | No | Yes |

#### When to use which?

- **Use `fetch`** when you want zero dependencies, building something small, or you're already using async/await cleanly.
- **Use `axios`** in real-world applications where you need interceptors, automatic error handling, and cleaner code — especially when working with authenticated APIs.

### Coding Questions

**Q1:** Rewrite this fetch code using Axios:
```javascript
// Fetch version:
const res = await fetch("https://api.example.com/users");
if (!res.ok) throw new Error("Error " + res.status);
const data = await res.json();
console.log(data);

// Axios version:
const res = await axios.get("https://api.example.com/users");
console.log(res.data); // much simpler!
```

---

**Q2:** How would you attach a token to every Axios request without repeating yourself?
```javascript
// Set up once in your app:
axios.defaults.baseURL = "https://api.example.com";
axios.defaults.headers.common["Authorization"] = `Bearer ${token}`;

// Now every request includes the token automatically:
const res = await axios.get("/users"); // hits https://api.example.com/users with token
```

---

## 3. ES6 Classes

### Interview Question
**"What are ES6 Classes in JavaScript? How do they relate to prototypes?"**

### Answer (Conversational)

Here's the truth that every interviewer wants you to know: **ES6 classes are NOT a new feature**. They're just **syntactic sugar over JavaScript's existing prototype-based inheritance**. Under the hood, classes compile down to the same constructor function + prototype pattern that always existed.

Think of classes as a cleaner way to write the same thing — not a fundamentally different approach.

#### Creating a Class

```javascript
class Animal {
  // constructor runs when you do 'new Animal(...)'
  constructor(name, sound) {
    this.name = name;
    this.sound = sound;
  }

  // method on the prototype (shared by all instances)
  speak() {
    console.log(`${this.name} says ${this.sound}`);
  }
}

const dog = new Animal("Rex", "Woof");
dog.speak(); // Rex says Woof
```

#### Inheritance with `extends` and `super`

```javascript
class Dog extends Animal {
  constructor(name) {
    super(name, "Woof"); // calls Animal's constructor
    this.tricks = [];
  }

  learn(trick) {
    this.tricks.push(trick);
    console.log(`${this.name} learned ${trick}!`);
  }
}

const rex = new Dog("Rex");
rex.speak();        // Rex says Woof (inherited from Animal)
rex.learn("Sit");   // Rex learned Sit!
```

`super()` must be called **before you use `this`** in a child class constructor. If you forget, you get a ReferenceError.

#### Static Methods

`static` methods belong to the class itself, not to instances. You call them on the class, not on an object:

```javascript
class MathHelper {
  static add(a, b) {
    return a + b;
  }
}

MathHelper.add(3, 4); // 7 ✓
// new MathHelper().add(3, 4); // TypeError — add is not on instances
```

#### Getters and Setters

```javascript
class Circle {
  constructor(radius) {
    this._radius = radius; // convention: _ means "private"
  }

  get area() {
    return Math.PI * this._radius ** 2;
  }

  set radius(value) {
    if (value < 0) throw new Error("Radius can't be negative");
    this._radius = value;
  }
}

const c = new Circle(5);
console.log(c.area);  // 78.53... (called like a property, not a method)
c.radius = 10;        // calls the setter
```

#### Private Fields (Modern JS — `#`)

```javascript
class BankAccount {
  #balance = 0; // truly private — can't be accessed outside

  deposit(amount) {
    this.#balance += amount;
  }

  get balance() {
    return this.#balance;
  }
}

const account = new BankAccount();
account.deposit(100);
console.log(account.balance); // 100
console.log(account.#balance); // SyntaxError! Truly private
```

#### Classes vs Prototypes (What's happening under the hood)

```javascript
// Class syntax:
class Person {
  constructor(name) { this.name = name; }
  greet() { console.log("Hi, I'm " + this.name); }
}

// Is exactly the same as:
function Person(name) { this.name = name; }
Person.prototype.greet = function() { console.log("Hi, I'm " + this.name); };
```

Both produce the same result! `typeof Person` is `"function"` even when using class syntax.

### Coding Questions

**Q1:** Create a `Rectangle` class with `width` and `height`, a method `area()`, and a static method `isSquare(rect)` that returns true if width equals height.
```javascript
class Rectangle {
  constructor(width, height) {
    this.width = width;
    this.height = height;
  }

  area() {
    return this.width * this.height;
  }

  static isSquare(rect) {
    return rect.width === rect.height;
  }
}

const r = new Rectangle(5, 5);
console.log(r.area());               // 25
console.log(Rectangle.isSquare(r)); // true
```

---

**Q2:** What will this output?
```javascript
class Animal {
  constructor(name) { this.name = name; }
  speak() { return `${this.name} makes a sound.`; }
}

class Dog extends Animal {
  speak() { return `${this.name} barks.`; }
}

const d = new Dog("Rex");
console.log(d.speak());
```

**Answer:** `"Rex barks."` — Dog overrides the `speak()` method from Animal. This is **method overriding** / polymorphism.

---

## 4. Template Literals

### Interview Question
**"What are Template Literals in JavaScript? What problems do they solve?"**

### Answer (Conversational)

Template literals (also called template strings) let you **embed expressions directly inside strings** using backtick characters `` ` `` instead of quotes. They solve two big problems with the old approach: awkward string concatenation and multi-line strings.

#### The Old Way vs Template Literals

```javascript
const name = "Alice";
const age = 30;

// Old way — string concatenation (messy!):
const msg = "Hello, my name is " + name + " and I am " + age + " years old.";

// Template literal — clean and readable:
const msg = `Hello, my name is ${name} and I am ${age} years old.`;
```

The `${}` syntax is called **string interpolation** — anything inside `{}` is evaluated as a JavaScript expression.

#### Expressions, Not Just Variables

You can put any valid JavaScript expression inside `${}`:

```javascript
const a = 5;
const b = 10;

console.log(`Sum is ${a + b}`);           // Sum is 15
console.log(`Is adult: ${age >= 18}`);    // Is adult: true
console.log(`Upper: ${name.toUpperCase()}`); // Upper: ALICE
console.log(`${true ? "yes" : "no"}`);   // yes
```

#### Multi-line Strings

```javascript
// Old way — you needed \n and +
const html = "<div>\n" +
             "  <p>Hello</p>\n" +
             "</div>";

// Template literal — just press Enter!
const html = `
  <div>
    <p>Hello</p>
  </div>
`;
```

This is especially useful when building HTML templates dynamically.

#### Tagged Template Literals (Advanced)

You can prefix a template literal with a function name — this is a "tagged template":

```javascript
function highlight(strings, ...values) {
  return strings.reduce((result, str, i) => {
    return result + str + (values[i] ? `<b>${values[i]}</b>` : "");
  }, "");
}

const name = "Alice";
const result = highlight`Hello, ${name}! Welcome!`;
// "Hello, <b>Alice</b>! Welcome!"
```

Tagged templates are used by libraries like **styled-components** (CSS-in-JS) and **graphql-tag**.

### Coding Questions

**Q1:** Rewrite this using a template literal:
```javascript
const product = "laptop";
const price = 999;
const msg = "The " + product + " costs $" + price + ".";

// Answer:
const msg = `The ${product} costs $${price}.`;
```

---

**Q2:** What does this output?
```javascript
const x = 10;
console.log(`${x > 5 ? "big" : "small"} number`);
```

**Answer:** `"big number"` — ternary expressions work inside `${}`.

---

## 5. Optional Chaining (?.) and Nullish Coalescing (??)

### Interview Question
**"What is optional chaining (?.) and nullish coalescing (??) in JavaScript? Why are they useful?"**

### Answer (Conversational)

These two operators solve one of the most annoying problems in real-world JavaScript: safely accessing deeply nested properties without crashing when something is `null` or `undefined`.

#### Optional Chaining (?.)

Before `?.`, accessing a nested property on a potentially null/undefined object would crash:

```javascript
const user = null;
console.log(user.profile.name); // TypeError: Cannot read property 'profile' of null
```

You had to write defensive code:
```javascript
if (user && user.profile && user.profile.name) {
  console.log(user.profile.name);
}
```

With `?.`, you just write:
```javascript
console.log(user?.profile?.name); // undefined — no crash!
```

If any part of the chain is `null` or `undefined`, the whole expression **short-circuits** and returns `undefined` instead of throwing.

#### Works on methods and array indexes too

```javascript
const user = {
  getName: () => "Alice",
  hobbies: ["reading", "coding"]
};

// Optional method call:
user?.getName();      // "Alice"
user?.getAge?.();     // undefined — method doesn't exist, no crash

// Optional array access:
user?.hobbies?.[0];   // "reading"
user?.friends?.[0];   // undefined — friends doesn't exist
```

#### Nullish Coalescing (??)

`??` provides a default value when something is `null` or `undefined` — and crucially, it's smarter than `||`.

```javascript
const value = null;
console.log(value ?? "default"); // "default"

const zero = 0;
console.log(zero ?? "default");  // 0 — not "default"!
console.log(zero || "default");  // "default" — WRONG! 0 is falsy
```

The key difference:
- `||` provides a fallback for any **falsy** value (`0`, `""`, `false`, `null`, `undefined`)
- `??` provides a fallback **only** for `null` or `undefined`

This matters a lot when `0` or empty string `""` are valid values in your data!

#### Combining Both

```javascript
const config = {
  timeout: 0,    // valid — 0ms timeout means no timeout
  retries: null  // not set
};

// Wrong — 0 gets replaced by 3000 because 0 is falsy!
const timeout = config.timeout || 3000;   // 3000 (wrong!)

// Right — only null/undefined gets replaced:
const timeout = config.timeout ?? 3000;   // 0 (correct!)
const retries = config.retries ?? 5;      // 5 (null gets replaced)
```

#### Real-World Usage

```javascript
async function getUser(id) {
  const user = await fetchUser(id); // might return null

  return {
    name: user?.name ?? "Anonymous",
    email: user?.email ?? "no-email@example.com",
    age: user?.profile?.age ?? 0,
    city: user?.address?.city ?? "Unknown"
  };
}
```

### Coding Questions

**Q1:** What does each line output?
```javascript
const obj = { a: { b: { c: 42 } } };

console.log(obj?.a?.b?.c);    // ?
console.log(obj?.x?.y?.z);    // ?
console.log(obj?.a?.b?.c ?? "none"); // ?
```

**Answer:** `42`, `undefined`, `42`

---

**Q2:** What's the difference?
```javascript
const count = 0;
console.log(count || 10);  // ?
console.log(count ?? 10);  // ?
```

**Answer:** `10` (because 0 is falsy) and `0` (because 0 is not null/undefined). This is why `??` was introduced — to handle legitimate falsy values.

---

## 6. for...of vs for...in

### Interview Question
**"What is the difference between for...of and for...in in JavaScript? When do you use each?"**

### Answer (Conversational)

These two loops look similar but work completely differently. Mixing them up is a very common mistake.

#### `for...in` — Iterates over **keys** (property names)

`for...in` loops over the **enumerable property names** of an object. Use it for plain objects:

```javascript
const person = { name: "Alice", age: 30, city: "NYC" };

for (const key in person) {
  console.log(key, ":", person[key]);
}
// name : Alice
// age : 30
// city : NYC
```

**The catch with arrays:** Don't use `for...in` on arrays! It iterates over the index strings (`"0"`, `"1"`, `"2"`), and can also accidentally pick up prototype properties:

```javascript
const arr = [10, 20, 30];

for (const key in arr) {
  console.log(key);   // "0", "1", "2" — these are strings, not numbers!
}
```

#### `for...of` — Iterates over **values**

`for...of` works with any **iterable** (arrays, strings, Sets, Maps, generators). It gives you the actual values:

```javascript
const arr = [10, 20, 30];

for (const value of arr) {
  console.log(value); // 10, 20, 30
}

// Works on strings:
for (const char of "hello") {
  console.log(char); // h, e, l, l, o
}

// Works on Sets:
const set = new Set([1, 2, 3]);
for (const item of set) {
  console.log(item); // 1, 2, 3
}

// Works on Maps:
const map = new Map([["a", 1], ["b", 2]]);
for (const [key, value] of map) {
  console.log(key, value); // a 1, b 2
}
```

#### Key Differences

| | `for...in` | `for...of` |
|---|---|---|
| Works on | Objects (and arrays with caveats) | Any iterable (array, string, Set, Map, generator) |
| Gives you | Keys / property names | Values |
| Works on plain objects | Yes | No — objects are not iterable |
| Use for arrays? | Avoid | Yes |

#### Quick Rule of Thumb

- Looping over an **object's properties**? → `for...in`
- Looping over **values** in an array/string/Set/Map? → `for...of`

### Coding Questions

**Q1:** What does this output?
```javascript
const nums = [1, 2, 3];
for (const i in nums) {
  console.log(typeof i);
}
```

**Answer:** `"string"` three times. `for...in` gives you keys as strings, even for arrays. This is why you should avoid it with arrays.

---

**Q2:** Loop over this object's entries and print "key = value" for each:
```javascript
const scores = { Alice: 95, Bob: 87, Carol: 92 };

for (const [name, score] of Object.entries(scores)) {
  console.log(`${name} = ${score}`);
}
// Alice = 95
// Bob = 87
// Carol = 92
```

---

## 7. Local Storage & Session Storage

### Interview Question
**"What is Local Storage and Session Storage? How are they different from cookies?"**

### Answer (Conversational)

**Local Storage** and **Session Storage** are two ways to store data in the browser that don't get sent to the server with every request (unlike cookies). They're both part of the Web Storage API.

#### Local Storage — Persists forever

Data stored in Local Storage stays there even after you close the browser tab or restart your computer. It only goes away if you explicitly clear it in code or the user clears their browser data.

```javascript
// Storing data:
localStorage.setItem("username", "Alice");
localStorage.setItem("theme", "dark");

// Reading data:
const user = localStorage.getItem("username"); // "Alice"

// Removing one item:
localStorage.removeItem("theme");

// Clearing everything:
localStorage.clear();
```

**Storing objects:** localStorage only stores strings! You must JSON.stringify/parse:
```javascript
const user = { name: "Alice", age: 30 };

localStorage.setItem("user", JSON.stringify(user));       // store
const retrieved = JSON.parse(localStorage.getItem("user")); // retrieve
```

#### Session Storage — Cleared when tab closes

Same API as localStorage, but the data **disappears when the browser tab is closed**:

```javascript
sessionStorage.setItem("sessionToken", "abc123");
const token = sessionStorage.getItem("sessionToken");
```

#### Comparison Table

| | **Local Storage** | **Session Storage** | **Cookies** |
|---|---|---|---|
| Capacity | ~5-10 MB | ~5 MB | ~4 KB |
| Persists after tab close | Yes | No | Depends on expiry |
| Sent to server | No | No | Yes (every request) |
| Accessible from JS | Yes | Yes | Yes (if not HttpOnly) |
| Accessible cross-tab | Yes | No | Yes |

#### When to use what?

- **Local Storage:** User preferences (theme, language), non-sensitive cached data, keeping users logged in across sessions.
- **Session Storage:** Temporary data that should die with the tab (form data, step in a multi-step flow).
- **Cookies:** Authentication tokens (with `HttpOnly` flag so JS can't read them — more secure), data that needs to be sent to the server.

### Coding Questions

**Q1:** A user selects a theme. Save it to localStorage so it persists on refresh:
```javascript
// Save on selection:
function saveTheme(theme) {
  localStorage.setItem("theme", theme);
}

// Load on page start:
function loadTheme() {
  const theme = localStorage.getItem("theme") ?? "light";
  document.body.className = theme;
}
loadTheme();
```

---

**Q2:** What gets cleared when you call `sessionStorage.clear()`?

**Answer:** Only the session storage for the current **origin** (domain + port) in the current **tab**. Other tabs and localStorage are unaffected.

---

## 8. Event Delegation & Event Bubbling

### Interview Question
**"What is Event Bubbling in JavaScript? What is Event Delegation and why is it useful?"**

### Answer (Conversational)

#### Event Bubbling — Events travel UP the DOM tree

When you click on an element, the event doesn't just trigger on that element — it **bubbles up** through every parent element all the way to the `document`. This is called **event bubbling**.

```html
<div id="outer">
  <button id="btn">Click me</button>
</div>
```

```javascript
document.getElementById("btn").addEventListener("click", () => {
  console.log("Button clicked");
});
document.getElementById("outer").addEventListener("click", () => {
  console.log("Div clicked");
});
document.addEventListener("click", () => {
  console.log("Document clicked");
});
```

When you click the button, you'll see:
```
Button clicked
Div clicked
Document clicked
```

All three handlers fire, even though you only clicked the button! The event bubbles up.

#### Stopping Bubbling

Use `event.stopPropagation()` to prevent bubbling:

```javascript
document.getElementById("btn").addEventListener("click", (e) => {
  e.stopPropagation(); // stops the event here — parent won't hear it
  console.log("Button clicked");
});
```

#### Event Delegation — ONE listener, handle MANY elements

Here's where it gets practical. Imagine you have a list with 100 items, and each needs a click handler. Adding 100 event listeners is terrible for performance.

**Event Delegation** is the pattern of adding **one listener to the parent** and using `event.target` to figure out which child was clicked:

```javascript
const list = document.getElementById("todo-list");

// ONE listener on the parent handles clicks on ALL list items:
list.addEventListener("click", (event) => {
  if (event.target.tagName === "LI") {
    console.log("Clicked:", event.target.textContent);
    event.target.classList.toggle("done");
  }
});
```

This works even for **dynamically added items** — if you add new `<li>` elements to the list later, they automatically get handled by this listener without you having to add anything!

```javascript
// Adding new items later — they work automatically:
const newItem = document.createElement("li");
newItem.textContent = "New task";
list.appendChild(newItem); // handled by the existing listener!
```

#### Why Event Delegation is a pattern you MUST know

1. **Performance:** One listener vs hundreds
2. **Dynamic elements:** Works for elements added after the listener was attached
3. **Memory:** Fewer listeners = less memory usage

### Coding Questions

**Q1:** What's wrong with this code for a list with 1000 items?
```javascript
const items = document.querySelectorAll("li");
items.forEach(item => {
  item.addEventListener("click", () => {
    item.classList.toggle("selected");
  });
});
```

**Answer:** It creates 1000 event listeners, one per item. This is inefficient. Also, any `<li>` added dynamically after this code runs won't have a listener.

**Fix using delegation:**
```javascript
document.getElementById("list").addEventListener("click", (e) => {
  if (e.target.tagName === "LI") {
    e.target.classList.toggle("selected");
  }
});
```

---

## 9. WeakMap & WeakSet

### Interview Question
**"What are WeakMap and WeakSet in JavaScript? How are they different from Map and Set?"**

### Answer (Conversational)

`WeakMap` and `WeakSet` are like `Map` and `Set`, but with one crucial difference: they hold **weak references** to their keys/values. This means if nothing else in your program holds a reference to an object stored in a WeakMap/WeakSet, it can be **garbage collected** automatically.

#### WeakMap

A `WeakMap` is a collection of key-value pairs where:
- **Keys must be objects** (not primitives)
- Keys are held **weakly** — if the object has no other references, it gets garbage collected and the entry disappears

```javascript
let user = { name: "Alice" };
const weakMap = new WeakMap();

weakMap.set(user, { sessionData: "xyz" });
weakMap.get(user); // { sessionData: "xyz" }

user = null; // Remove the reference to the object
// Now the garbage collector can clean up the user object AND the WeakMap entry
// This does NOT happen with a regular Map!
```

#### Why this matters — Memory leak prevention

With a regular `Map`, if you use an object as a key and later set that object to `null`, the Map still holds a reference to it — the object can NEVER be garbage collected. This is a memory leak.

```javascript
const map = new Map();
let obj = { data: "big data" };
map.set(obj, "some value");

obj = null; // You think you're done with this object
// But Map still holds a reference! obj is never garbage collected!

// WeakMap doesn't have this problem:
const weakMap = new WeakMap();
weakMap.set(obj2, "some value");
obj2 = null; // Now the garbage collector can clean up obj2
```

#### WeakMap Methods (fewer than Map!)

```javascript
weakMap.set(key, value);  // add/update
weakMap.get(key);         // get value
weakMap.has(key);         // check if key exists
weakMap.delete(key);      // remove entry

// NOT available (by design):
// weakMap.size       — no size property
// weakMap.keys()     — no iteration
// weakMap.values()   — no iteration
// weakMap.clear()    — no clear
```

You can't iterate a WeakMap because keys can disappear at any time (garbage collection). Size can't be known reliably.

#### WeakSet

`WeakSet` is a collection of unique objects, held weakly. Same idea — if the object has no other references, it gets garbage collected and removed from the WeakSet.

```javascript
const weakSet = new WeakSet();
let user = { id: 1 };

weakSet.add(user);
weakSet.has(user);   // true
weakSet.delete(user);

user = null; // automatically removed from WeakSet eventually
```

#### Use Cases

- **WeakMap:** Caching computed results for objects without preventing GC. Storing metadata about DOM nodes (if the node is removed from the DOM, the entry is automatically cleaned up). Private data for class instances.
- **WeakSet:** Tracking which objects have been processed without preventing GC. Detecting circular references.

### Coding Questions

**Q1:** Why would you choose a WeakMap over a Map for this use case?
```javascript
// Caching expensive computations for DOM nodes:
const cache = new WeakMap();

function getExpensiveData(domNode) {
  if (!cache.has(domNode)) {
    cache.set(domNode, computeExpensiveData(domNode));
  }
  return cache.get(domNode);
}
```

**Answer:** When a DOM node is removed from the document, there are no more references to it. With a regular Map, the cache would hold a reference forever — memory leak. With WeakMap, the entry is automatically cleaned up when the DOM node is garbage collected.

---

## 10. Object Utility Methods

### Interview Question
**"What are Object.keys(), Object.values(), Object.entries(), Object.freeze(), and Object.seal()? When do you use them?"**

### Answer (Conversational)

These are handy static methods on the `Object` constructor that make working with plain objects much easier. They were all added in ES5 or ES6.

#### Object.keys() — Get all property names

Returns an array of an object's **own enumerable property names**:

```javascript
const user = { name: "Alice", age: 30, city: "NYC" };

Object.keys(user);   // ["name", "age", "city"]
Object.values(user); // ["Alice", 30, "NYC"]
```

#### Object.values() — Get all property values

```javascript
const scores = { Alice: 95, Bob: 87, Carol: 92 };

// Find the highest score:
const maxScore = Math.max(...Object.values(scores)); // 95

// Sum all scores:
const total = Object.values(scores).reduce((sum, s) => sum + s, 0); // 274
```

#### Object.entries() — Get key-value pairs as arrays

Returns an array of `[key, value]` pairs. Great for iterating over an object:

```javascript
const person = { name: "Alice", age: 30 };

for (const [key, value] of Object.entries(person)) {
  console.log(`${key}: ${value}`);
}
// name: Alice
// age: 30

// Convert object to Map:
const map = new Map(Object.entries(person));

// Filter an object's properties:
const onlyStrings = Object.fromEntries(
  Object.entries(person).filter(([key, val]) => typeof val === "string")
);
// { name: "Alice" }
```

#### Object.freeze() — Make an object immutable

After freezing, you can't add, remove, or modify properties. Attempts silently fail in non-strict mode, throw errors in strict mode:

```javascript
const config = Object.freeze({
  API_URL: "https://api.example.com",
  MAX_RETRIES: 3
});

config.API_URL = "https://evil.com"; // silently ignored!
config.newProp = "something";         // silently ignored!

console.log(config.API_URL); // still "https://api.example.com"
```

**Important:** Freeze is **shallow**! Nested objects are NOT frozen:

```javascript
const obj = Object.freeze({
  name: "Alice",
  address: { city: "NYC" } // this nested object is NOT frozen
});

obj.name = "Bob";          // ignored — frozen
obj.address.city = "LA";   // WORKS! nested object isn't frozen
```

#### Object.seal() — Prevent adding/removing but allow modification

Less strict than freeze — you can still change existing property values, but can't add or remove properties:

```javascript
const user = Object.seal({ name: "Alice", age: 30 });

user.name = "Bob";    // ✓ — modification allowed
user.email = "...";   // ✗ — can't add new properties
delete user.age;      // ✗ — can't delete properties
```

#### Object.fromEntries() — Convert entries back to an object

The reverse of `Object.entries()`:

```javascript
const entries = [["name", "Alice"], ["age", 30]];
const obj = Object.fromEntries(entries); // { name: "Alice", age: 30 }

// Useful for transforming objects:
const doubled = Object.fromEntries(
  Object.entries({ a: 1, b: 2, c: 3 }).map(([k, v]) => [k, v * 2])
);
// { a: 2, b: 4, c: 6 }
```

#### Quick Cheat Sheet

| Method | Returns | Use case |
|---|---|---|
| `Object.keys(obj)` | Array of keys | Iterate keys, check if key exists |
| `Object.values(obj)` | Array of values | Sum, max, filter values |
| `Object.entries(obj)` | Array of `[key, val]` pairs | Iterate, convert to Map |
| `Object.fromEntries(arr)` | Object | Convert Map/entries back to object |
| `Object.freeze(obj)` | Frozen object | Config constants, immutable state |
| `Object.seal(obj)` | Sealed object | Allow updates but not new properties |
| `Object.assign(target, src)` | Merged object | Merge/clone (covered in core3.md) |
| `Object.create(proto)` | New object | Set prototype (covered in core3.md) |

### Coding Questions

**Q1:** Given an object of student scores, return only the students who scored above 80:
```javascript
const scores = { Alice: 95, Bob: 72, Carol: 88, Dave: 61 };

const topStudents = Object.fromEntries(
  Object.entries(scores).filter(([name, score]) => score > 80)
);
// { Alice: 95, Carol: 88 }
```

---

**Q2:** What happens when you do this?
```javascript
const obj = Object.freeze({ x: 1, nested: { y: 2 } });
obj.x = 99;
obj.nested.y = 99;
console.log(obj.x, obj.nested.y);
```

**Answer:** `1, 99` — `obj.x` is frozen so the change is silently ignored, but `obj.nested` is not frozen (freeze is shallow), so `obj.nested.y` is successfully changed to `99`.

---

## Summary — What Was Missing from the 6 Notes Files

| Topic | File to study |
|---|---|
| Fetch API — GET/POST, error handling | This file, Section 1 |
| Axios — interceptors, vs fetch | This file, Section 2 |
| ES6 Classes — extends, super, static, private fields | This file, Section 3 |
| Template Literals — interpolation, tagged templates | This file, Section 4 |
| Optional Chaining `?.` and Nullish Coalescing `??` | This file, Section 5 |
| `for...of` vs `for...in` | This file, Section 6 |
| Local Storage / Session Storage / Cookies | This file, Section 7 |
| Event Bubbling & Event Delegation | This file, Section 8 |
| WeakMap & WeakSet | This file, Section 9 |
| Object.keys/values/entries/freeze/seal | This file, Section 10 |
