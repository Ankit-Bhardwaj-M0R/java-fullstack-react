# JavaScript Asynchronous Programming — Complete Guide

> **Beginner-friendly study guide** covering Callbacks, Promises (consuming & creating), Promise APIs, async/await, Web Workers, setTimeout & Closures, and AbortController.

---

## Table of Contents
1. [Callbacks & Inversion of Control](#1-callbacks--inversion-of-control)
2. [Promises — What & Why](#2-promises--what--why)
3. [Creating a Promise (resolve, reject, .then, .catch)](#3-creating-a-promise-resolve-reject-then-catch)
4. [Promise Chaining](#4-promise-chaining)
5. [Promise APIs (all, allSettled, race, any)](#5-promise-apis-all-allsettled-race-any)
6. [async / await](#6-async--await)
7. [Web Workers](#7-web-workers)
8. [setTimeout & Closures (Classic Interview Trap)](#8-settimeout--closures-classic-interview-trap)
9. [AbortController — Cancelling Async Tasks](#9-abortcontroller--cancelling-async-tasks)

---

## 1. Callbacks & Inversion of Control

### Interview Question
**"What is a callback? What is 'inversion of control' and why is it a problem?"**

### Answer (Conversational)

A **callback** is a function you pass into another function, trusting that other function to call it at the right time. This is the original way JavaScript handled async work:

```javascript
// E-commerce example:
createOrder(cart, function(orderId) {
  // this callback will be called once createOrder is done
  proceedToPayment(orderId);
});
```

This worked — but it has a serious problem.

#### The Problem: Inversion of Control

When you write `createOrder(cart, callback)`, you are **giving control of your code** to the `createOrder` API. You're trusting it to:
- Call your callback **once** (not zero times, not five times)
- Call it **at the right time** (after the order is created, not before)
- Call it **without errors** you didn't expect

But what if `createOrder` is a third-party library? What if it has a bug and calls your payment callback **twice**? What if it never calls it?

> **Inversion of Control** = you lose control of your own code by passing it inside someone else's function. You have to blindly trust their implementation.

This is a fundamental trust problem that **Promises were designed to solve**.

#### Callback Hell (Pyramid of Doom)

A related problem: when async operations depend on each other, callbacks get nested:

```javascript
// Callback Hell — code grows HORIZONTALLY
createOrder(cart, function(orderId) {
  proceedToPayment(orderId, function(paymentInfo) {
    showOrderSummary(paymentInfo, function() {
      updateWallet(function() {
        // now you can't even read this anymore...
      });
    });
  });
});
```

This is called **Callback Hell** or the **Pyramid of Doom**. The deeper your nesting, the harder the code is to read, debug, and maintain.

---

## 2. Promises — What & Why

### Interview Question
**"What is a Promise in JavaScript? Why do we use them? What are the states of a promise?"**

### Answer (Conversational)

#### The Simple Definition
A Promise is like a **placeholder container** for a value you don't have yet. Imagine you order food at a restaurant — you're not given the food immediately, you're given a **token** (a promise of future food). The token represents the eventual result.

In JavaScript, async operations (API calls, file reads, timers) don't complete instantly. A Promise is that **token** — it tells you "I'll have a result for you eventually."

#### The MDN (Interview-Grade) Definition

> **A Promise is an object representing the eventual completion of an asynchronous operation.**

Say this in an interview and you'll impress the interviewer. Break it down:
- **Object** — it's a regular JavaScript object
- **Represents** — it stands in place of / tracks
- **Eventual completion** — it will finish at some point (not now)
- **Async operation** — some work happening in the background

#### The 3 States of a Promise

A promise can only ever be in one of three states — never more, never something else:

```
Promise States:
┌─────────────────────────────────────────┐
│  1. pending    → waiting for result     │
│  2. fulfilled  → operation succeeded ✓  │
│  3. rejected   → operation failed ✗     │
└─────────────────────────────────────────┘
```

> **Important:** Once a promise moves from `pending` to `fulfilled` or `rejected`, it **never changes again**. It's immutable (locked in).

#### The Promise Object

When you log a Promise in the browser, you see something like:

```javascript
Promise { <pending> }       // still waiting
Promise { "result data" }   // fulfilled with this value
Promise { <rejected> }      // failed
```

Internally, promises have two key properties:
- `[[PromiseState]]` — the current state (`"pending"`, `"fulfilled"`, or `"rejected"`)
- `[[PromiseResult]]` — the data (undefined if pending, value if fulfilled, error if rejected)

#### Why Promises Solve Inversion of Control

With promises, instead of **passing** your callback into someone else's function, you **receive** a promise object and **attach** your callback to it yourself:

```javascript
// Old callback way — giving away control:
createOrder(cart, function(orderId) {
  proceedToPayment(orderId);
});

// Promise way — you keep control:
const orderPromise = createOrder(cart); // returns a promise

orderPromise.then(function(orderId) {
  proceedToPayment(orderId);
});
```

The difference: **you** are calling `.then()` on the promise — not the other way around. The promise will call your callback exactly **once** when it resolves. You're not handing over control — you're subscribing to a result.

#### Guarantees Promises Give You (Why They're Trustworthy)

1. A promise can only be resolved **once** — calling resolve twice has no effect
2. Only **three possible states** — there's no "half-resolved" or mystery state
3. The result is **immutable** — once resolved, the value can't be changed
4. You can pass the promise object **anywhere** in your code — it's just a regular object

### Coding Questions

**Q1:** What are the three states of a Promise?

**Answer:** `pending` (waiting), `fulfilled` (success), `rejected` (failure). Once fulfilled or rejected, it cannot change.

---

**Q2:** What does this code print?
```javascript
const p = new Promise((resolve) => {
  resolve("Hello!");
});
console.log(p);
```

**Answer:** `Promise { 'Hello!' }` — the promise immediately resolves and holds the value `"Hello!"`.

---

**Q3:** Why does returning a promise instead of accepting a callback solve inversion of control?

**Answer:** With callbacks you hand your code to another function to call. With promises, you get back a promise object and attach your own handler with `.then()` — you decide when and how to consume the result.

---

## 3. Creating a Promise (resolve, reject, .then, .catch)

### Interview Question
**"How do you create a Promise? What are resolve and reject? How do you handle success and failure?"**

### Answer (Conversational)

#### The Producer Side — Creating a Promise

To create a Promise, use the `Promise` **constructor** with the `new` keyword:

```javascript
const pr = new Promise(function(resolve, reject) {
  // your async logic goes here
});
```

The constructor takes a **function** with two parameters:
- `resolve` — call this when your async operation **succeeds**
- `reject` — call this when your async operation **fails**

> These are functions **provided by JavaScript** — you don't write them. JavaScript gives them to you. Just call them when you're done.

#### Full Real-World Example: `createOrder` API

```javascript
// PRODUCER: creating the promise
function createOrder(cart) {
  const pr = new Promise(function(resolve, reject) {
    // Step 1: validate the cart
    if (!validateCart(cart)) {
      const err = new Error("Cart is not valid");
      reject(err); // REJECT — tell consumer something went wrong
      return;
    }

    // Step 2: create the order (simulating a DB call)
    const orderId = "12345"; // pretend this came from a database
    if (orderId) {
      resolve(orderId); // RESOLVE — pass the order ID to consumer
    }
  });

  return pr; // return the promise to whoever called createOrder
}

function validateCart(cart) {
  return true; // simplified validation
}
```

#### The Consumer Side — Handling the Result

```javascript
// CONSUMER: using the promise
const cart = ["shoes", "pants", "cap"];
const orderPromise = createOrder(cart);

// .then() handles the SUCCESS case (resolved)
orderPromise.then(function(orderId) {
  console.log("Order created! ID:", orderId);
});

// .catch() handles the FAILURE case (rejected)
orderPromise.catch(function(error) {
  console.error("Order failed:", error.message);
});
```

#### Simulating an Async Delay

In real life, a database call takes time. You can simulate this with `setTimeout`:

```javascript
function createOrder(cart) {
  return new Promise(function(resolve, reject) {
    setTimeout(function() {
      const orderId = "12345";
      resolve(orderId); // resolves after 5 seconds
    }, 5000);
  });
}

const p = createOrder(cart);
console.log(p); // Promise { <pending> } — not done yet!

p.then(function(orderId) {
  console.log(orderId); // "12345" — printed after 5 seconds
});
```

When you log `p` immediately after calling `createOrder`, you see `Promise { <pending> }` — because the promise hasn't resolved yet. 5 seconds later, your `.then()` callback fires with the result.

#### Handling Rejection Properly

When a promise is rejected and you don't handle it, you get an **Uncaught error** in the browser — a scary red error that means your code crashed silently for the user.

**Always write a `.catch()` to handle errors gracefully:**

```javascript
createOrder(cart)
  .then(function(orderId) {
    console.log("Success:", orderId);
  })
  .catch(function(error) {
    console.error("Caught:", error.message);
    // Show user-friendly message like "Your cart is invalid"
  });
```

> Never rely on uncaught errors. You should always catch your errors **before the browser catches them for you**.

### Coding Questions

**Q1:** Fill in the blanks — what goes in the Promise constructor?
```javascript
function fetchData() {
  return new Promise(function(_____, _____) {
    // if success:
    _____(data);
    // if failure:
    _____(new Error("Failed"));
  });
}
```

**Answer:** `resolve`, `reject`, `resolve(data)`, `reject(new Error("Failed"))`

---

**Q2:** What will this print and when?
```javascript
const p = new Promise((resolve) => {
  setTimeout(() => resolve("Done!"), 2000);
});

console.log("Before");
p.then(result => console.log(result));
console.log("After");
```

**Answer:**
```
Before
After
Done!   ← (after 2 seconds)
```
JavaScript doesn't wait. "Before" and "After" print immediately. "Done!" prints after 2 seconds.

---

**Q3:** Write a function `wait(ms)` that returns a promise which resolves after `ms` milliseconds:
```javascript
function wait(ms) {
  return new Promise(function(resolve) {
    setTimeout(resolve, ms);
  });
}

wait(1000).then(() => console.log("1 second passed!"));
```

---

## 4. Promise Chaining

### Interview Question
**"What is Promise chaining? How does it solve callback hell? What is a common mistake when chaining?"**

### Answer (Conversational)

#### The Problem: Callback Hell (Revisited)

In an e-commerce site, operations depend on each other — create order → payment → show summary → update wallet. With callbacks, code grows **horizontally**:

```javascript
// Pyramid of Doom — hard to read and maintain
createOrder(cart, function(orderId) {
  proceedToPayment(orderId, function(paymentInfo) {
    showOrderSummary(paymentInfo, function() {
      updateWallet(function() {
        // deeply nested — nightmare to debug
      });
    });
  });
});
```

#### The Solution: Promise Chaining

Promise chaining converts this into vertical, readable code using `.then()`:

```javascript
createOrder(cart)
  .then(function(orderId) {
    return proceedToPayment(orderId); // pass data to next step
  })
  .then(function(paymentInfo) {
    return showOrderSummary(paymentInfo);
  })
  .then(function() {
    return updateWallet();
  })
  .catch(function(error) {
    console.error("Something went wrong:", error.message);
  });
```

Code now grows **vertically** — much more readable, maintainable, and debuggable. One `.catch()` at the end handles any error from any step.

#### The #1 Common Mistake: Forgetting to RETURN

This is the most common bug in promise chains:

```javascript
// BUG — data doesn't flow properly
createOrder(cart)
  .then(function(orderId) {
    proceedToPayment(orderId); // ⚠️ forgot to return!
  })
  .then(function(paymentInfo) {
    // paymentInfo is UNDEFINED here!
    // proceedToPayment was called but its result was lost
    showOrderSummary(paymentInfo);
  });
```

```javascript
// CORRECT — always return from .then() when chaining
createOrder(cart)
  .then(function(orderId) {
    return proceedToPayment(orderId); // ✓ return the promise
  })
  .then(function(paymentInfo) {
    return showOrderSummary(paymentInfo); // ✓ paymentInfo is correct now
  });
```

> **Rule:** Whatever value you return from a `.then()` gets passed as the argument to the next `.then()` in the chain. If you forget `return`, the next `.then()` receives `undefined`.

#### Arrow Function Style (More Concise)

Many developers prefer arrow functions in chains:

```javascript
createOrder(cart)
  .then(orderId => proceedToPayment(orderId))   // implicit return!
  .then(paymentInfo => showOrderSummary(paymentInfo))
  .then(() => updateWallet())
  .catch(error => console.error(error.message));
```

Arrow functions with a single expression **implicitly return** — no `return` keyword needed. This makes chains very compact.

### Coding Questions

**Q1:** What's wrong with this chain? Fix it.
```javascript
fetchUser()
  .then(function(user) {
    fetchPosts(user.id); // problem here
  })
  .then(function(posts) {
    console.log(posts);
  });
```

**Answer:** Missing `return`. `posts` will be `undefined` because the result of `fetchPosts()` is not returned to the chain. Fix: `return fetchPosts(user.id);`

---

**Q2:** Convert this callback hell into a promise chain:
```javascript
login(user, function(token) {
  fetchProfile(token, function(profile) {
    showDashboard(profile, function() {
      console.log("Done");
    });
  });
});
```

**Answer:**
```javascript
login(user)
  .then(token => fetchProfile(token))
  .then(profile => showDashboard(profile))
  .then(() => console.log("Done"))
  .catch(err => console.error(err));
```

---

## 5. Promise APIs (all, allSettled, race, any)

### Interview Question
**"Explain the four Promise APIs: Promise.all, Promise.allSettled, Promise.race, and Promise.any. When would you use each?"**

### Answer (Conversational)

All four APIs take an **array of promises** and return a new promise. They differ in **when** they resolve/reject and **what** they return.

---

### Promise.all() — All or Nothing

**Contract:** If ALL promises succeed → return array of all results. If ANY promise fails → immediately throw that error.

This is a **"fail-fast"** API.

```
P1 ─────────────────── 3s ✓ (Val 1)
P2 ──── 1s ✓ (Val 2)
P3 ──────────── 2s ✓ (Val 3)
                              ↓ after 3s (longest)
Result: ["Val 1", "Val 2", "Val 3"]
```

If one fails:
```
P1 ─────────────────── 3s ✓
P2 ──── 1s ✗ (Error!)
P3 ──────────── 2s ✓
        ↓ after 1s (immediately when P2 failed)
Result: throws Error from P2 (doesn't wait for P1 or P3)
```

```javascript
Promise.all([p1, p2, p3])
  .then(results => {
    console.log(results); // ["Val 1", "Val 2", "Val 3"]
  })
  .catch(error => {
    console.error(error); // the error from whichever promise failed first
  });
```

**Use case:** When you need the result of ALL API calls before showing a page. If one fails, the whole thing fails (e.g., fetching all required sections for a dashboard).

---

### Promise.allSettled() — Wait for Everything

**Contract:** Waits for ALL promises to settle (succeed OR fail). Returns an array of **every result** — successes and failures — in the same order.

```
P1 ─────────────────── 3s ✓ (Val 1)
P2 ──── 1s ✗ (Error!)
P3 ──────────── 2s ✓ (Val 3)
                              ↓ after 3s (ALL settled)
Result: [
  { status: "fulfilled", value: "Val 1" },
  { status: "rejected",  reason: Error },
  { status: "fulfilled", value: "Val 3" }
]
```

Unlike `Promise.all`, it **never rejects** — it always resolves with the full picture.

```javascript
Promise.allSettled([p1, p2, p3])
  .then(results => {
    results.forEach(result => {
      if (result.status === "fulfilled") {
        console.log("Success:", result.value);
      } else {
        console.log("Failed:", result.reason);
      }
    });
  });
```

**Use case:** Showing 5 cards on a page, each populated by a different API call. If one fails, show the other 4 and skip the failed one. You need ALL the results to decide what to show.

---

### Promise.race() — First Settled Wins

**Contract:** Returns the result of whichever promise **settles first** — success OR failure. Doesn't care which one wins, just the fastest.

```
P1 ─────────────────── 3s ✓
P2 ──── 1s ✓ (Val 2)   ← FIRST settled
P3 ──────────── 2s ✓
        ↓ after 1s
Result: "Val 2" (just a value, not an array)
```

If the first-settled promise fails:
```
P1 ─────────────── 3s ✓
P2 ──── 1s ✗ (Error)  ← FIRST settled (but failed)
P3 ──────── 2s ✓
     ↓ after 1s
Result: throws Error (from P2 — didn't wait for P1/P3)
```

**Key terminology:** "Settled" means received a response — doesn't matter if it was success or failure.

```javascript
Promise.race([p1, p2, p3])
  .then(result => console.log("Winner:", result))
  .catch(error => console.error("First settled (failed):", error));
```

**Use case:** Timeout pattern — race your actual request against a timeout promise. If the request takes too long, the timeout promise wins and you can show an error.

---

### Promise.any() — First SUCCESS Wins

**Contract:** Returns the result of the **first successful (fulfilled) promise**. Ignores failures and waits for any success. If ALL fail → throws an `AggregateError` with all errors.

```
P1 ─────────────────── 3s ✓ (Val 1)
P2 ──── 1s ✗ (rejected — ignored!)
P3 ──────── 2s ✓ (Val 3)  ← FIRST success
                ↓ after 2s
Result: "Val 3" (not an array, just the first success value)
```

If all fail:
```
P1 ─────── 1s ✗
P2 ──────────── 2s ✗
P3 ────────────────── 3s ✗
                        ↓ after 3s (all settled)
Result: AggregateError containing [err1, err2, err3]
```

```javascript
Promise.any([p1, p2, p3])
  .then(result => console.log("First success:", result))
  .catch(error => {
    console.error("All failed");
    console.error(error.errors); // array of all errors
  });
```

**Use case:** Making requests to multiple servers/mirrors — return whichever responds successfully first.

---

### Quick Comparison Table

| API | Resolves When | Rejects When | Returns |
|---|---|---|---|
| `Promise.all` | **All succeed** | Any one fails (fail-fast) | Array of all values |
| `Promise.allSettled` | **All settle** (success or fail) | Never rejects | Array of `{status, value/reason}` |
| `Promise.race` | **First settles** (success or fail) | If first settled is rejection | Value of first settled |
| `Promise.any` | **First succeeds** | All fail | Value of first success (or AggregateError) |

### Terminology to Know for Interviews

- **Settled** = received a response (either fulfilled or rejected)
- **Fulfilled** = specifically succeeded / resolved
- **Rejected** = specifically failed
- **AggregateError** = special error containing an array of multiple errors

### Coding Questions

**Q1:** You have 3 API calls. You want to show data only if ALL succeed. Which API do you use?

**Answer:** `Promise.all` — if any fails, it fails immediately and you can show an error page.

---

**Q2:** You have 5 API calls fetching 5 cards. If some fail, show the ones that succeeded. Which API?

**Answer:** `Promise.allSettled` — it waits for all to finish and gives you the full picture. Then filter for fulfilled ones.

---

**Q3:** What does this print and when?
```javascript
const p1 = new Promise(resolve => setTimeout(() => resolve("P1"), 3000));
const p2 = new Promise(resolve => setTimeout(() => resolve("P2"), 1000));
const p3 = new Promise(resolve => setTimeout(() => resolve("P3"), 2000));

Promise.all([p1, p2, p3]).then(console.log);
```

**Answer:** After **3 seconds** (the longest): `["P1", "P2", "P3"]` — waits for all three, returns in original array order.

---

**Q4:** Same promises, but using `Promise.race`. What prints and when?

**Answer:** After **1 second**: `"P2"` — P2 finishes first (1s), so race returns its value immediately.

---

**Q5:** What does `Promise.any` return when all promises reject?

**Answer:** An `AggregateError` — a special error object whose `.errors` property is an array containing all the individual rejection errors.

---

## 6. async / await

### Interview Question
**"What is async/await? How does it work behind the scenes? What happens when JavaScript hits an await keyword?"**

### Answer (Conversational)

`async/await` is **syntactic sugar** on top of Promises. It lets you write asynchronous code that **looks and reads like synchronous code**, making it much easier to follow.

#### What `async` does

Put `async` before a function to make it an **async function**:

```javascript
async function getData() {
  // this is an async function
}
```

An async function **always returns a Promise** — even if you return a plain value, it gets wrapped in a resolved promise.

#### What `await` does

Inside an async function, put `await` before a Promise:

```javascript
async function handlePayment() {
  const orderId = await createOrder(cart); // waits here
  const paymentInfo = await proceedToPayment(orderId); // then waits here
  console.log(paymentInfo);
}
```

`await` tells JavaScript: "pause execution of this function until this promise resolves, then give me the resolved value."

#### Without `await` vs With `await`

```javascript
// Without await — JS doesn't wait
function handlePayment() {
  const p = createOrder(cart); // returns pending promise immediately
  console.log(p); // Promise { <pending> }
  console.log("This runs BEFORE the order is created!"); // prints first
}

// With await — execution appears to pause
async function handlePayment() {
  const orderId = await createOrder(cart); // JS waits here
  console.log(orderId); // "12345" — available immediately after await
  console.log("This runs AFTER the order is created!"); // prints second
}
```

> **Important:** `await` can ONLY be used inside an `async` function. Using `await` outside an async function throws a SyntaxError.

#### What Actually Happens Behind the Scenes

This is the most important part for interviews. `await` does NOT literally pause JavaScript (JavaScript is single-threaded and can't block).

What **actually** happens when JavaScript hits `await`:

1. The async function **exits the call stack** — it removes itself temporarily
2. The promise is registered; JavaScript continues executing other code
3. When the promise resolves, the async function is **put back into the call stack**
4. Execution resumes from where it left off

```
handlePayment() starts
  → hits await createOrder() (Promise — takes 5s)
  → handlePayment() LEAVES the call stack
  → main thread continues other work
  ... 5 seconds later ...
  → createOrder() resolves
  → handlePayment() RE-ENTERS the call stack
  → continues from next line with orderId value
```

This is why the main thread is **never blocked** — the async function gets out of the way and comes back when it's ready.

#### Tricky Interview Question: Multiple Awaits

```javascript
async function getData() {
  const p1 = new Promise(resolve => setTimeout(() => resolve("Val 1"), 10000)); // 10s
  const p2 = new Promise(resolve => setTimeout(() => resolve("Val 2"), 5000));  //  5s

  const val1 = await p1; // waits 10 seconds
  console.log(val1);     // prints after 10s

  const val2 = await p2; // p2 already resolved at 5s! (it started when p1 started)
  console.log(val2);     // prints immediately after val1 (no additional wait!)
}

getData();
console.log("Hello World"); // prints immediately (before getData resolves anything)
```

Output:
```
Hello World          ← immediately
Val 1                ← after 10 seconds
Val 2                ← immediately after Val 1 (5s already elapsed)
```

Why? Both `p1` and `p2` **start resolving in parallel** when they are created. The `await p1` waits 10 seconds. By the time it resolves, `p2` has already been resolved for 5 seconds — so `await p2` returns instantly.

#### Sequential vs Parallel Execution

```javascript
// SEQUENTIAL — takes 10 + 5 = 15 seconds total
async function sequential() {
  const val1 = await p1; // wait 10s
  const val2 = await p2; // wait another 5s
}

// But if p1 and p2 are defined BEFORE the awaits, they run in parallel:
// PARALLEL — takes max(10, 5) = 10 seconds total
async function parallel() {
  const p1 = createP1(); // starts immediately
  const p2 = createP2(); // starts immediately
  const val1 = await p1; // waits for p1 (10s)
  const val2 = await p2; // p2 already resolved! returns instantly
}
```

#### Error Handling in async/await

Use `try/catch` instead of `.catch()`:

```javascript
async function handlePayment() {
  try {
    const orderId = await createOrder(cart);
    const paymentInfo = await proceedToPayment(orderId);
    console.log("Payment done:", paymentInfo);
  } catch (error) {
    console.error("Something went wrong:", error.message);
  }
}
```

This is equivalent to using `.catch()` on a promise chain, but reads much more naturally.

#### async/await vs .then()/.catch() — Which to Use?

| Feature | `.then()/.catch()` | `async/await` |
|---|---|---|
| Readability | Can get complex with chaining | Reads like synchronous code |
| Error handling | `.catch()` at end | `try/catch` block |
| Debugging | Harder (async stack traces) | Easier |
| Use case | Fine for simple one-liner chains | Preferred for complex logic |

Both produce the same behavior. `async/await` is generally preferred for readability.

### Coding Questions

**Q1:** What does this print and in what order?
```javascript
async function fetchData() {
  console.log("Start");
  const result = await new Promise(resolve => setTimeout(() => resolve("Done"), 2000));
  console.log(result);
  console.log("End");
}

fetchData();
console.log("Outside");
```

**Answer:**
```
Start
Outside   ← runs while fetchData waits for the promise
Done      ← after 2 seconds
End
```

---

**Q2:** Rewrite this `.then()` chain using async/await:
```javascript
fetch(url)
  .then(res => res.json())
  .then(data => console.log(data))
  .catch(err => console.error(err));
```

**Answer:**
```javascript
async function fetchData() {
  try {
    const res = await fetch(url);
    const data = await res.json();
    console.log(data);
  } catch (err) {
    console.error(err);
  }
}
```

---

**Q3:** What's wrong with this code?
```javascript
function getData() {
  const data = await fetch("https://api.example.com");
  console.log(data);
}
```

**Answer:** `await` is used inside a regular function, not an `async` function. This throws a SyntaxError. Fix: add `async` before `function`.

---

**Q4:** What is the call stack behavior when JavaScript encounters `await p` and `p` is still pending?

**Answer:** The async function **exits the call stack** (removes itself). JavaScript continues executing other code. When `p` resolves, the async function is placed back in the call stack and continues from the next line. The main thread is never blocked.

---

## 7. Web Workers

### Interview Question
**"What is a Web Worker? Why would you use one? How does communication work?"**

### Answer (Conversational)

#### JavaScript is Single-Threaded

By default, JavaScript runs on a **single thread** — the **main thread**. There is only one path of execution. The main thread is responsible for:
- Running all your JavaScript
- Rendering the page
- Responding to user interactions

If your code does **heavy computation** (like summing 1 to 10 billion in a loop), the main thread gets **blocked** — the UI freezes, buttons stop responding, the page appears broken.

#### What is a Web Worker?

A **Web Worker** is a separate JavaScript thread that runs **in the background** — completely separate from the main thread. You can offload expensive work to a worker so the main thread stays free and responsive.

```
Main Thread:  [User events] [Rendering] [Normal JS]
                                        |
                                        | (creates worker)
                                        ↓
Worker Thread:            [Heavy CPU computation]
                          [Running in background]
                                        |
                                        | (sends result back)
                                        ↓
Main Thread:  [Receives result] [Updates UI]
```

> **Critical limitation:** Web Workers **cannot access the DOM**. Only the main thread can read and modify the DOM. Workers are for computation only.

#### Creating a Web Worker

You create a worker by pointing to a separate JavaScript file:

**main.js (main thread)**
```javascript
// Create a new worker pointing to worker.js
const worker = new Worker("worker.js");
```

**worker.js (runs in background)**
```javascript
// Worker file — runs in its own thread
```

#### Communication: postMessage API

The main thread and worker communicate by **passing messages** to each other. Neither can directly call functions in the other — they must send messages.

**Sending a message TO the worker (main → worker):**
```javascript
// In main.js
worker.postMessage("hello worker");         // send a string
worker.postMessage({ data: [1, 2, 3] });    // or an object
```

**Receiving a message IN the worker:**
```javascript
// In worker.js
onmessage = function(event) {
  console.log(event.data); // "hello worker" — the data is in event.data
  // self.onmessage = function(e) {...} also works (self = the worker itself)
};
```

**Sending a message FROM the worker (worker → main):**
```javascript
// In worker.js
postMessage(result); // sends data back to main thread
```

**Receiving a message IN the main thread (from worker):**
```javascript
// In main.js
worker.onmessage = function(event) {
  console.log(event.data); // the result from the worker
  alert("Final sum is: " + event.data);
};
```

#### Full Example: Offloading Heavy Computation

**Problem:** Summing 1 to 10 billion freezes the UI.

**main.js:**
```javascript
const worker = new Worker("worker.js");

// When worker sends back a result, show it
worker.onmessage = function(event) {
  alert("The final sum is: " + event.data);
};

// Button to trigger the work
document.getElementById("calcBtn").addEventListener("click", function() {
  worker.postMessage("start"); // tell worker to start working
});

// This button still works while worker computes!
document.getElementById("bgBtn").addEventListener("click", function() {
  document.body.style.background = "blue"; // UI stays responsive!
});
```

**worker.js:**
```javascript
onmessage = function(event) {
  // Runs in background — doesn't block main thread
  let sum = 0;
  for (let i = 1; i <= 10_000_000_000; i++) {
    sum += i;
  }
  postMessage(sum); // send result back to main thread
};
```

Now when you click "Calculate", the worker runs the heavy loop in the background. You can still change the background color — the UI stays responsive!

#### Important Notes
- The global object inside a worker is the **worker itself** (not `window`)
- `self` refers to the worker (same as just using `onmessage` directly)
- Workers can use `fetch`, `setTimeout`, `setInterval`, `WebSockets` — but NOT the DOM

### Coding Questions

**Q1:** What is the key limitation of Web Workers?

**Answer:** Web Workers cannot access or manipulate the DOM. Only the main thread has access to the DOM.

---

**Q2:** How does the main thread send data to a worker?
```javascript
const worker = new Worker("worker.js");
worker.postMessage({ task: "compute", numbers: [1, 2, 3] });
```

**Answer:** Using `worker.postMessage(data)`. The data is available in the worker via `event.data` inside an `onmessage` handler.

---

**Q3:** What problem does a Web Worker solve? Give an example.

**Answer:** A heavy `for` loop (like summing 10 billion numbers) blocks the main thread, making the page unresponsive. Offloading it to a Web Worker lets the main thread continue handling UI events while the worker computes in the background.

---

## 8. setTimeout & Closures (Classic Interview Trap)

### Interview Question
**"What is the output of this code and why? How do you fix it?"**
```javascript
for (var i = 1; i <= 5; i++) {
  setTimeout(function() {
    console.log(i);
  }, i * 1000);
}
```

### Answer (Conversational)

#### What You'd Expect vs What Actually Happens

Most developers expect: `1` after 1s, `2` after 2s, `3` after 3s, `4` after 4s, `5` after 5s.

**What actually happens:** `6` `6` `6` `6` `6` — after 1s, 2s, 3s, 4s, 5s respectively.

#### Why? (Two Reasons Working Together)

**Reason 1: JavaScript doesn't wait**

"Time and tide and JavaScript wait for none."

When JavaScript hits `setTimeout`, it:
1. Takes the callback function
2. Attaches a timer to it
3. Stores them somewhere (Web API area)
4. **Immediately moves on** — does NOT wait for the timer!

So the loop runs completely (i goes 1 → 2 → 3 → 4 → 5 → 6) before ANY timeout fires.

**Reason 2: Closures remember references, not values**

Each setTimeout callback forms a **closure** — it closes over the variable `i`. But it captures the **reference** to `i` (the memory address), NOT a copy of its current value.

All 5 callback functions point to the **exact same `i` variable** in memory:

```
callback1 ──→ [reference to i]
callback2 ──→ [reference to i]  ← all pointing to same spot!
callback3 ──→ [reference to i]
callback4 ──→ [reference to i]
callback5 ──→ [reference to i]
```

By the time the first timer fires (1 second), the loop has already finished and `i` has been incremented to **6** (the condition `i <= 5` was checked when `i` was 6 and failed, ending the loop).

So all 5 callbacks read `i` and see `6`. That's why all print `6`.

#### Fix 1: Use `let` Instead of `var`

`let` is **block-scoped** — a new copy of `i` is created for each iteration of the loop:

```javascript
for (let i = 1; i <= 5; i++) {
  setTimeout(function() {
    console.log(i); // each closure has its OWN i
  }, i * 1000);
}
// Output: 1 (after 1s), 2 (after 2s), 3 (after 3s), 4 (after 4s), 5 (after 5s) ✓
```

With `let`, each iteration creates a brand new `i` variable. The closure for each setTimeout captures a different variable (different memory location). When timers fire, each reads its own personal copy of `i`.

#### Fix 2: Wrap in a Closure (If You Must Use `var`)

An interviewer might say "use `var` only." You can still fix it using a closure:

```javascript
for (var i = 1; i <= 5; i++) {
  (function close(x) { // immediately invoked, creates new scope each time
    setTimeout(function() {
      console.log(x); // closes over x — a fresh copy each iteration
    }, x * 1000);
  })(i); // pass current i as argument
}
// Output: 1, 2, 3, 4, 5 ✓
```

How it works:
- Each iteration calls `close(i)` — passing the current value of `i` as argument `x`
- `x` is a new variable in each function call's scope
- The setTimeout callback closes over `x` (not `i`)
- Each callback has its own `x` with the right value

#### What setTimeout Actually Does

```javascript
function x() {
  let i = 1;
  setTimeout(function() {
    console.log(i); // closure — i is remembered
  }, 1000);
}
x(); // prints 1 after 1 second
```

1. `setTimeout` takes the callback and stores it
2. Attaches a timer
3. JavaScript moves to the next line immediately (doesn't wait)
4. When timer expires, the callback is put back into the call stack
5. Callback executes, reads `i` from its closure

### Coding Questions

**Q1:** What is the output?
```javascript
for (var i = 0; i < 3; i++) {
  setTimeout(function() {
    console.log(i);
  }, 0);
}
```

**Answer:** `3` `3` `3` — even with 0ms delay, all timeouts fire after the loop completes. `i` is `3` by then.

---

**Q2:** Fix the above using `let`:
```javascript
for (let i = 0; i < 3; i++) {
  setTimeout(function() {
    console.log(i);
  }, 0);
}
// Output: 0, 1, 2 ✓
```

---

**Q3:** What is the output and why?
```javascript
function outer() {
  var count = 0;
  setTimeout(function() {
    count++;
    console.log(count);
  }, 1000);
  count++;
  console.log(count);
}
outer();
```

**Answer:** `1` immediately (synchronous `count++` then log), then `2` after 1 second (setTimeout callback runs, sees `count` is now 1, increments to 2). The callback closes over the `count` reference.

---

## 9. AbortController — Cancelling Async Tasks

### Interview Question
**"What is an AbortController? How do you cancel a fetch request? What else can you use it for?"**

### Answer (Conversational)

#### The Problem: You Can't Easily Cancel Async Operations

Before `AbortController`, if you started a `fetch()` request and wanted to cancel it (user navigated away, timeout, etc.), there was no clean way to do it.

`AbortController` solves this — it gives you a **signal** you can attach to async operations, and a way to fire that signal to cancel them.

#### Creating an AbortController

```javascript
const controller = new AbortController();
const signal = controller.signal; // the signal object
```

- `controller` — controls the aborting (has the `.abort()` method)
- `signal` — passed to async operations to link them to this controller

To cancel/abort everything linked to this controller:
```javascript
controller.abort(); // fires the abort signal
```

You can also pass a reason:
```javascript
controller.abort("User navigated away");
```

#### Use Case 1: Cancelling a fetch Request

```javascript
const controller = new AbortController();

async function fetchData() {
  try {
    const response = await fetch("https://api.example.com/data", {
      signal: controller.signal // link this request to the controller
    });
    const data = await response.json();
    console.log(data);
  } catch (error) {
    if (error.name === "AbortError") {
      console.log("Fetch was cancelled!");
    } else {
      console.error("Other error:", error);
    }
  }
}

fetchData();

// Cancel the fetch after 3 seconds (e.g., user changed page)
setTimeout(() => {
  controller.abort();
}, 3000);
```

When `controller.abort()` is called, the fetch throws an `AbortError`. Check `error.name === "AbortError"` in your catch block to handle it separately from other errors.

#### Use Case 2: Removing Event Listeners

Many people don't know you can use `AbortController` with event listeners! Normally you'd need a reference to the function to pass to `removeEventListener`. With abort signals, you don't need that:

```javascript
const controller = new AbortController();
const { signal } = controller;

// Add event listeners with a signal
document.addEventListener("mousemove", logMousePosition, { signal });
document.addEventListener("mouseup", handleMouseUp, { signal });

// To remove ALL listeners linked to this controller:
controller.abort(); // removes both listeners automatically!
```

Without `AbortController`, removing an event listener requires keeping a reference to the callback function. The `signal` approach is cleaner, especially when you have many listeners.

#### Use Case 3: Listening for the Abort Event

The signal itself emits an `"abort"` event when aborted:

```javascript
const controller = new AbortController();
const { signal } = controller;

// Listen for the abort event on the signal itself
signal.addEventListener("abort", function() {
  console.log("Aborted! Reason:", signal.reason);
}, { once: true }); // once: true means it fires only once and removes itself

controller.abort("timeout"); // triggers the abort event above
// Output: "Aborted! Reason: timeout"
```

`{ once: true }` is important — it ensures the listener automatically removes itself after firing, so it doesn't linger in memory.

#### Use Case 4: Aborting WebSocket Connections

```javascript
async function initWebSocket(url, signal) {
  if (signal.aborted) {
    throw new Error("Already aborted");
  }

  const socket = new WebSocket(url);

  signal.addEventListener("abort", function() {
    socket.close(); // automatically close socket when aborted
  }, { once: true });

  // ... use socket normally
}

const controller = new AbortController();
initWebSocket("wss://example.com", controller.signal);

// Close the socket after 30 seconds:
setTimeout(() => controller.abort("Session expired"), 30000);
```

#### Use Case 5: Cancelling Heavy Operations in a Class

```javascript
class HeavyProcessor {
  constructor(signal) {
    this.signal = signal;
  }

  process() {
    if (this.signal.aborted) {
      throw new Error("Already aborted before starting");
    }

    for (let i = 0; i < 10; i++) {
      if (this.signal.aborted) {
        throw new Error("Aborted mid-computation");
      }
      console.log("Processing step", i);
    }
  }
}

const controller = new AbortController();
controller.abort(); // abort before even starting

const processor = new HeavyProcessor(controller.signal);
try {
  processor.process();
} catch (error) {
  console.log(error.message); // "Already aborted before starting"
}
```

#### Summary

| Feature | What it does |
|---|---|
| `new AbortController()` | Creates controller + signal pair |
| `controller.signal` | The AbortSignal to pass to operations |
| `controller.abort()` | Fires the abort — cancels all linked operations |
| `controller.abort("reason")` | Fires with a custom reason string |
| `signal.aborted` | Boolean — `true` if already aborted |
| `signal.reason` | The reason string passed to `.abort()` |
| `error.name === "AbortError"` | Check if an error came from aborting |
| `{ signal }` in fetch options | Links the request to the controller |
| `{ signal }` in addEventListener | Links the listener to the controller |
| `{ once: true }` in addEventListener | Listener removes itself after first fire |

Available in Node.js v15 and above.

### Coding Questions

**Q1:** Write code that cancels a fetch request if it takes longer than 5 seconds:
```javascript
const controller = new AbortController();

// Abort after 5 seconds
const timeout = setTimeout(() => controller.abort("Timeout"), 5000);

fetch("https://api.example.com/data", { signal: controller.signal })
  .then(res => res.json())
  .then(data => {
    clearTimeout(timeout); // cancel timeout if request succeeds in time
    console.log(data);
  })
  .catch(error => {
    if (error.name === "AbortError") {
      console.log("Request timed out!");
    }
  });
```

---

**Q2:** How do you check if an abort signal has already been triggered before starting work?

**Answer:** `if (signal.aborted) { throw new Error("Already aborted"); }` — check `signal.aborted` at the start.

---

**Q3:** What is the advantage of using AbortController with event listeners over `removeEventListener`?

**Answer:** With `removeEventListener`, you must keep a reference to the exact function passed to `addEventListener`. With `AbortController`, you just call `controller.abort()` and all listeners attached with that signal are automatically removed — no need to track references, especially helpful when you have multiple listeners.

---

## Summary Cheat Sheet

| Concept | Key Point |
|---|---|
| Callback | Function passed to another function to call later |
| Inversion of Control | Giving away control of your code to another function |
| Callback Hell | Deeply nested callbacks — code grows horizontally |
| Promise | Object representing eventual completion of async operation |
| Promise states | `pending`, `fulfilled`, `rejected` — only these 3 |
| `.then()` | Attaches success handler to a promise |
| `.catch()` | Attaches failure handler (always write this!) |
| Creating a promise | `new Promise((resolve, reject) => {...})` |
| `resolve(value)` | Marks promise as fulfilled with a value |
| `reject(error)` | Marks promise as rejected with an error |
| Promise chaining | `.then().then().then()` — vertical code, no more pyramid |
| Return in chain | Always `return` from `.then()` to pass data down chain |
| `Promise.all` | All succeed → array of results; any fail → immediate error |
| `Promise.allSettled` | All settle → array of results AND errors |
| `Promise.race` | First settled (success OR fail) wins |
| `Promise.any` | First success wins; all fail → AggregateError |
| `async` | Makes function always return a Promise |
| `await` | Waits for Promise to resolve inside async function |
| Behind the scenes `await` | Async function exits call stack, re-enters when resolved |
| Web Worker | Separate JS thread for CPU-heavy work |
| Worker limitation | Cannot access the DOM |
| `postMessage` | Communication between main thread and worker |
| setTimeout + var bug | All callbacks close over same `i` reference → all print `6` |
| Fix: use `let` | Block scope creates new `i` per iteration |
| Fix: use closure | Wrap in IIFE to create new scope per iteration |
| AbortController | Object that can cancel linked async operations |
| `controller.signal` | Pass to fetch/addEventListener to link them |
| `controller.abort()` | Cancels all linked operations |
| `error.name === "AbortError"` | Check if error was from abort |
