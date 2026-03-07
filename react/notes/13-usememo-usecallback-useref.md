# Episode 13: useMemo, useCallback & useRef — Performance Hooks

> Bonus episode covering three performance hooks and a deep dive into how JavaScript variables work inside React components.

---

## Table of Contents
1. [The React Documentation — Where to Learn](#1-the-react-documentation--where-to-learn)
2. [The Two Most Important Hooks](#2-the-two-most-important-hooks)
3. [React StrictMode — Why Your Component Renders Twice in Dev](#3-react-strictmode--why-your-component-renders-twice-in-dev)
4. [Understanding the Problem — What Is a "Heavy Operation"?](#4-understanding-the-problem--what-is-a-heavy-operation)
5. [The Demo Setup — nth Prime Number](#5-the-demo-setup--nth-prime-number)
6. [The Problem useMemo Solves](#6-the-problem-usememo-solves)
7. [useMemo — Syntax and How It Works](#7-usememo--syntax-and-how-it-works)
8. [useMemo in Action — Before and After](#8-usememo-in-action--before-and-after)
9. [useCallback — Caching a Function Definition](#9-usecallback--caching-a-function-definition)
10. [Three Ways to Store Values in a Component](#10-three-ways-to-store-values-in-a-component)
11. [let Variables in React Components](#11-let-variables-in-react-components)
12. [State Variables in React Components](#12-state-variables-in-react-components)
13. [JavaScript Execution Context — Why let Resets](#13-javascript-execution-context--why-let-resets)
14. [useRef — Syntax and Core Concept](#14-useref--syntax-and-core-concept)
15. [useRef in Action — The Three-Variable Comparison](#15-useref-in-action--the-three-variable-comparison)
16. [Real-World useRef Use Case — setInterval ID](#16-real-world-useref-use-case--setinterval-id)
17. [Why let Fails for setInterval](#17-why-let-fails-for-setinterval)
18. [useRef as the Fix for setInterval](#18-useref-as-the-fix-for-setinterval)
19. [ref Does NOT Persist Across Mount/Unmount](#19-ref-does-not-persist-across-mountunmount)
20. [Summary — let vs State vs Ref Comparison Table](#20-summary--let-vs-state-vs-ref-comparison-table)
21. [Summary Interview Q&A](#21-summary-interview-qa)
22. [Coding Exercises](#22-coding-exercises)
23. [Quick Quiz](#23-quick-quiz)

---

## 1. The React Documentation — Where to Learn

The instructor opens the episode by pointing to the **official React documentation**. There are two versions:

- **Old docs:** `react.org` — outdated, still exists but no longer maintained
- **New docs:** `react.dev` — this is the source of truth. Always refer to this.

The new documentation is written by the people building React itself. It has:
- Updated hook references
- Interactive examples
- The official explanations of every hook

> "It is the source of truth for React. The React people who are building React are writing this." — Instructor

When in doubt about any hook, go to `react.dev/reference/react` — it lists every hook with examples.

---

## 2. The Two Most Important Hooks

The instructor emphasizes: **there are only two hooks you will use 99% of the time:**

| Rank | Hook | Usage |
|---|---|---|
| #1 | `useState` | State management — used constantly |
| #2 | `useEffect` | Side effects (API calls, timers) — used very often |
| Others | `useMemo`, `useCallback`, `useRef`, etc. | Used rarely, only for specific performance/reference needs |

Think of it like Google search results:
- First two links: clicked almost every time
- Third, fourth: sometimes
- Page 2 onwards: very rarely

That said — when you ARE building large-scale, performance-heavy apps, the other hooks become important. This episode covers three of them.

**Note:** `useParams` and `useNavigate` are NOT core React hooks — they come from React Router. `useSelector` and `useDispatch` come from Redux. Core React hooks come from the `react` package.

---

## 3. React StrictMode — Why Your Component Renders Twice in Dev

During the demo, the instructor notices the component renders twice on every state change. The reason: **React StrictMode**.

```jsx
// index.js
root.render(
  <React.StrictMode>   {/* ← This causes double renders in dev */}
    <App />
  </React.StrictMode>
);
```

**What StrictMode does in development:**
- Calls your component function **twice** on every render
- This helps detect side effects written incorrectly
- React checks that both render cycles produce the same result — if not, your code has a bug
- It ONLY happens in development, NOT in production

**Why React does this:**
When you push the same code with StrictMode to production, the double-render stops. React uses it internally to verify reconciliation consistency. It's React's way of saying "let me double-check your component is pure."

> "In production even if I push the same code with strict mode, it will not happen. This is just happening in dev." — Instructor

If the double rendering bothers you while learning, you can temporarily remove `<React.StrictMode>` from `index.js`.

---

## 4. Understanding the Problem — What Is a "Heavy Operation"?

**useMemo** exists to solve one specific problem: **expensive (heavy) operations running unnecessarily on every re-render.**

**Key concept:** React re-renders a component whenever:
- Any **state variable** changes
- Any **prop** changes

When a component re-renders, ALL the code inside it runs again — including expensive calculations.

**Heavy operations** are things that take significant CPU time:
- Calculating the nth prime number
- Sorting a massive list
- Complex mathematical computations
- Filtering/transforming large datasets

For lightweight operations, React is fast — you won't notice any lag. But for truly heavy operations, running them on every keystroke (state change) causes the page to freeze.

---

## 5. The Demo Setup — nth Prime Number

The instructor builds a demo component with two independent state variables:

```jsx
// Demo.jsx
const Demo = () => {
  const [number, setNumber] = useState(0);    // controls which prime to find
  const [isDarkTheme, setIsDarkTheme] = useState(false);  // controls theme

  return (
    <div className={`m-4 p-2 w-96 h-96 border border-black ${
      isDarkTheme ? "bg-gray-900 text-white" : ""
    }`}>
      {/* Input to change the number */}
      <input
        type="number"
        value={number}
        onChange={(e) => setNumber(Number(e.target.value))}
        className="border border-black w-72 px-2"
      />

      {/* Show the nth prime */}
      <div className="mt-4 font-bold text-xl">
        {number}th prime: {findPrime(number)}
      </div>

      {/* Theme toggle */}
      <div>
        <button
          className="m-10 p-2 bg-green-200"
          onClick={() => setIsDarkTheme(!isDarkTheme)}
        >
          Toggle Theme
        </button>
      </div>
    </div>
  );
};
```

**Helper function to find nth prime (heavy operation):**
```jsx
// helper.js
export function findPrime(n) {
  if (n <= 0) return 0;
  let count = 0;
  let num = 1;
  while (count < n) {
    num++;
    if (isPrime(num)) count++;
  }
  return num;
}

function isPrime(n) {
  if (n < 2) return false;
  for (let i = 2; i <= Math.sqrt(n); i++) {
    if (n % i === 0) return false;
  }
  return true;
}
```

**Observing the problem:**
- Type `1234567` (7 digits) → the page **freezes** completely
- The browser tab becomes unresponsive
- Chrome allocates separate memory to each tab, so one freezing tab won't crash Chrome

**The real problem:**
- Type `123456` (finding the 123456th prime = heavy)
- Now click "Toggle Theme"
- Even though the `number` didn't change, the page **freezes again** before the theme updates

Why? Because toggling the theme changes `isDarkTheme` state → component re-renders → `findPrime(number)` runs again → freeze.

The theme toggle has NOTHING to do with finding the prime number. Yet the prime calculation runs anyway. This is the problem `useMemo` solves.

---

## 6. The Problem useMemo Solves

Without memoization, every state change — no matter which state — causes everything in the component to re-run:

```
User types in input (number changes):
  → Re-render
  → findPrime(number) runs ← makes sense, number changed
  → Theme class is applied ← this is fast, no problem

User clicks Toggle Theme (isDarkTheme changes):
  → Re-render
  → findPrime(number) runs ← PROBLEM! number didn't change!
  → Theme class changes ← this is what we wanted
```

With `useMemo`:
```
User types in input (number changes):
  → Re-render
  → findPrime runs (dependency "number" changed) ← correct

User clicks Toggle Theme:
  → Re-render
  → findPrime SKIPPED (dependency "number" unchanged) ← cached!
  → Theme class changes ← fast!
```

---

## 7. useMemo — Syntax and How It Works

**Official definition:** `useMemo` is a React hook that lets you **cache the result of a calculation** between re-renders.

```jsx
const cachedValue = useMemo(
  () => expensiveFunction(dependency),  // function whose result to cache
  [dependency]                          // dependency array
);
```

**Parameters:**
1. **Function** — a function that does the expensive work and returns a value
2. **Dependency array** — list of variables that, when changed, should invalidate the cache and re-run the function

**Return value:** The cached result of the function. It's not a function — it's the value.

**Behavior:**
- On the first render: runs the function, caches the result
- On every re-render: checks if any dependency changed
  - **If no dependency changed:** returns the cached value, skips the function
  - **If a dependency changed:** runs the function again, caches the new result

---

## 8. useMemo in Action — Before and After

**Before (without useMemo) — re-calculates prime on every render:**
```jsx
const Demo = () => {
  const [number, setNumber] = useState(0);
  const [isDarkTheme, setIsDarkTheme] = useState(false);

  // ❌ Runs on EVERY re-render — even when theme changes!
  const prime = findPrime(number);

  return (...);
};
```

**After (with useMemo) — only re-calculates when number changes:**
```jsx
import { useState, useMemo } from "react";

const Demo = () => {
  const [number, setNumber] = useState(0);
  const [isDarkTheme, setIsDarkTheme] = useState(false);

  // ✅ Only runs when `number` changes
  const prime = useMemo(() => findPrime(number), [number]);

  return (
    <div className={`m-4 p-2 w-96 h-96 border border-black ${
      isDarkTheme ? "bg-gray-900 text-white" : ""
    }`}>
      <input
        type="number"
        value={number}
        onChange={(e) => setNumber(Number(e.target.value))}
        className="border border-black w-72 px-2"
      />

      <div className="mt-4 font-bold text-xl">
        {number}th prime: {prime}
      </div>

      <button
        className="m-10 p-2 bg-green-200"
        onClick={() => setIsDarkTheme(!isDarkTheme)}
      >
        Toggle Theme
      </button>
    </div>
  );
};
```

**Result:**
- Typing a 7-digit number: still freezes briefly (the calculation is genuinely needed)
- Clicking Toggle Theme after calculation: **instant** — no freeze, because prime is cached

**Important:** The component still RE-RENDERS on theme toggle. React is still doing its job. But now React doesn't re-run `findPrime` — it uses the cached result. The component renders, but the expensive work is skipped.

> "It is caching the result between re-renders. React gives us control — if the nth prime operation is heavy, put a memo. If it's not heavy, don't use memo." — Instructor

**When NOT to use useMemo:**
- For lightweight operations — overhead of useMemo itself (comparing dependencies) costs more than just re-running
- For simple state changes that are fast

---

## 9. useCallback — Caching a Function Definition

`useCallback` is the sibling of `useMemo`. The difference is subtle but important:

| Hook | What it caches | Example |
|---|---|---|
| `useMemo` | The **result** (value returned by a function) | `const prime = useMemo(() => findPrime(n), [n])` → `prime` is a number |
| `useCallback` | The **function itself** (the function definition) | `const fn = useCallback(() => findPrime(n), [n])` → `fn` is a function |

**useMemo:** caches the RESULT of a calculation between re-renders.
**useCallback:** caches a FUNCTION DEFINITION between re-renders.

```jsx
// useMemo — prime is a NUMBER (result)
const prime = useMemo(() => findPrime(number), [number]);
console.log(prime); // 677

// useCallback — getPrime is a FUNCTION
const getPrime = useCallback(() => findPrime(number), [number]);
console.log(getPrime); // ƒ () { return findPrime(number); }
console.log(getPrime()); // 677  ← you must call it
```

**When is useCallback useful?**
- When you're passing a callback function as a prop to a child component
- Without `useCallback`, a new function is created on every render — even if the logic is the same
- This causes child components to re-render unnecessarily (React compares props by reference)
- With `useCallback`, the same function reference is reused unless dependencies change

> The instructor kept this section brief: "You understood the basic principle. Explore it yourself. It is easy. Try some examples." — Instructor

---

## 10. Three Ways to Store Values in a Component

Before explaining `useRef`, the instructor takes a detour to teach something most React developers don't know — the difference between `let`, `state`, and `ref` for storing values in a component.

There are three ways to keep a value in a component:

| Type | Created with | Triggers re-render? | Persists across re-renders? |
|---|---|---|---|
| Normal variable | `let x = 0` | No | No — resets on every render |
| State variable | `const [x, setX] = useState(0)` | Yes | Yes |
| Ref variable | `const ref = useRef(0)` | No | Yes |

Understanding this table is the key to understanding `useRef`.

---

## 11. let Variables in React Components

The instructor demonstrates live:

```jsx
const Demo2 = () => {
  let x = 0;  // normal let variable

  return (
    <div>
      <span>{x}</span>
      <button onClick={() => { x = x + 1; console.log("x:", x); }}>
        Increase X
      </button>
    </div>
  );
};
```

**What happens when you click "Increase X"?**

The console shows: `x: 1`, `x: 2`, `x: 3` — the value IS increasing!

But the UI still shows `0`. It never updates on screen.

**Why?**
- The `let x` variable increments in memory
- But React doesn't know about it — no state change was triggered
- No re-render happens — so the UI shows the old value

**The second problem — reset on re-render:**

If you click a state-changing button (causing a re-render), `x` resets to 0!

Why? Because when the component re-renders, the function runs again from the top. `let x = 0` is executed again — brand new variable, value 0.

**Constant variable:**
```jsx
const x = 0;
// x = x + 1; ← TypeError! Cannot assign to constant
```

`const` doesn't let you reassign at all — you get an error. That's why state works with `const` — `setX` doesn't reassign the variable, it triggers React to call the function again with a new value.

---

## 12. State Variables in React Components

```jsx
const [y, setY] = useState(0);
```

**What happens when you click "Increase Y"?**
- `setY(y + 1)` is called
- React schedules a re-render
- Component function runs again
- `y` now holds the new value
- UI updates to show the new value

**State persists across re-renders** — that's the whole point of `useState`. React stores state outside the component function, so it survives function re-calls.

**But state causes re-renders** — that's the trade-off.

---

## 13. JavaScript Execution Context — Why let Resets

This is the "first principles" explanation from the instructor — the reason let variables reset:

> "At the end of the day, what is this component? It is a function. What happens when a function is called in JavaScript? A whole new execution context is created. A whole new memory space is created. A whole new thread of execution starts." — Instructor

When React re-renders a component:
1. It calls the component function again
2. JavaScript creates a fresh **execution context** for that function call
3. All variables declared inside the function are created fresh
4. `let x = 0` runs again → x is 0 again

This is pure JavaScript — not React magic. React is built on JavaScript. When you understand this, everything else clicks.

**The key insight:**
```
Click "Increase X" 5 times → x becomes 5 (in memory)
Click "Increase Y" (causes re-render) → component function runs again
→ let x = 0 executes → x is back to 0
```

State variables are DIFFERENT — React stores them outside the function, in a special area managed by React's Fiber architecture. So when the function runs again, React gives back the same state values.

---

## 14. useRef — Syntax and Core Concept

**Official definition:** `useRef` lets you reference a value that is **not needed for rendering**.

Put simply: useRef gives you a variable that:
- **Persists** between renders (doesn't reset like `let`)
- **Does NOT trigger re-renders** when changed (unlike `useState`)

**Syntax:**
```jsx
const ref = useRef(initialValue);
```

**What `useRef` returns — an object with `current`:**
```jsx
const ref = useRef(0);
console.log(ref);         // { current: 0 }
console.log(ref.current); // 0
```

**Important:** `useRef` does NOT return a plain value like `useState`. It returns an **object** with a property called `current`. The initial value you pass becomes `ref.current`.

```jsx
// useState returns [value, setter]
const [count, setCount] = useState(0);
count; // 0

// useRef returns { current: value }
const countRef = useRef(0);
countRef.current; // 0
```

**Updating a ref:**
```jsx
// No setter function! You directly mutate current
ref.current = ref.current + 1;  // ✅ Works
ref.current++;                  // ✅ Also works
setRef(ref.current + 1);        // ❌ No such function
```

**Why does React return an object instead of a plain value?**
The instructor admits: "I don't know why did they do that but that's how it is." The object wrapper is necessary because objects are passed by reference in JavaScript — React can maintain the same object across renders while allowing the `current` property inside to change.

---

## 15. useRef in Action — The Three-Variable Comparison

The instructor builds a component with all three types:

```jsx
import { useState, useRef } from "react";

const Demo2 = () => {
  // 1. let variable
  let x = 0;

  // 2. State variable
  const [y, setY] = useState(0);

  // 3. Ref variable
  const ref = useRef(0);

  console.log("Rendering");

  return (
    <div className="m-4 p-2 bg-slate-50 border border-black w-96 h-96">
      {/* Show current values */}
      <span className="font-bold text-xl">
        x: {x} | y: {y} | ref: {ref.current}
      </span>

      {/* Button for let variable */}
      <button
        className="bg-green-100 px-2 m-4"
        onClick={() => {
          x = x + 1;
          console.log("x:", x);
        }}
      >
        Increase X
      </button>

      {/* Button for state variable */}
      <button
        className="bg-blue-100 px-2 m-4"
        onClick={() => setY(y + 1)}
      >
        Increase Y
      </button>

      {/* Button for ref variable */}
      <button
        className="bg-purple-100 px-2 m-4"
        onClick={() => {
          ref.current = ref.current + 1;
          console.log("ref:", ref.current);
        }}
      >
        Increase Ref
      </button>
    </div>
  );
};
```

**Live experiment — what happens:**

| Action | UI updates? | Console | What happens |
|---|---|---|---|
| Click "Increase X" 5 times | NO | x: 1, 2, 3, 4, 5 | x increments in memory, not shown |
| Click "Increase Ref" 5 times | NO | ref: 1, 2, 3, 4, 5 | ref.current increments, not shown |
| Click "Increase Y" once | YES | "Rendering" | Re-render! Now x shows 0, ref shows 5 |

**The stunning observation the instructor demonstrates:**
1. Click "Increase X" 3 times → x is 3 in memory, UI shows 0
2. Click "Increase Ref" 3 times → ref.current is 3, UI shows 0
3. Click "Increase Y" → re-render happens
4. UI now shows: `x: 0 | y: 1 | ref: 3`
   - `x` reset to 0 (let variable wiped by new execution context)
   - `y` shows 1 (state persisted and updated)
   - `ref` shows 3 (ref persisted! didn't reset!)

> "This was amazing — did your mind get blown? The ref persisted the value. The let got wiped." — Instructor

---

## 16. Real-World useRef Use Case — setInterval ID

The most practical use case for `useRef` is storing a **timer ID** (from `setInterval` or `setTimeout`) so you can cancel it later from a button.

**The setup:**
```jsx
useEffect(() => {
  const intervalId = setInterval(() => {
    console.log("Namaste React " + Math.random());
  }, 1000);

  return () => clearInterval(intervalId); // cleanup on unmount
}, []);
```

This runs fine. But what if you want a **"Stop Printing" button** that can cancel the interval at any time — not just on unmount?

You need to:
1. Store the interval ID somewhere accessible to the button's onClick handler
2. Call `clearInterval(storedId)` when the button is clicked

---

## 17. Why let Fails for setInterval

**Attempt 1: Using a regular let variable**

```jsx
const Demo2 = () => {
  let i = null;  // try to store interval ID

  useEffect(() => {
    i = setInterval(() => {        // store ID in let
      console.log("Namaste React " + Math.random());
    }, 1000);

    return () => clearInterval(i);
  }, []);

  return (
    <button onClick={() => clearInterval(i)}>Stop Printing</button>
  );
};
```

**React warns you:**
```
Assignment to variable 'i' from inside React Hook useEffect will be
lost after each render. To preserve the value over time, store it in a
useRef instead.
```

**Why it fails:**
- `let i = null` is inside the component function
- Every re-render creates a new execution context → `i` is null again
- The `clearInterval(i)` in the button's onClick gets `null` — it can't stop the interval

**Attempt 2: Using let as a shared object**
```jsx
let i = { current: null }; // trying to simulate useRef
```

This might work for single renders but React will still warn you, and it breaks under multiple mounts/unmounts because `i` doesn't reset properly.

---

## 18. useRef as the Fix for setInterval

```jsx
import { useEffect, useRef } from "react";

const Demo2 = () => {
  const intervalRef = useRef(null);  // persists between renders, no re-render

  useEffect(() => {
    // Store interval ID in ref
    intervalRef.current = setInterval(() => {
      console.log("Namaste React " + Math.random());
    }, 1000);

    return () => clearInterval(intervalRef.current);
  }, []);

  return (
    <div>
      <button
        className="bg-red-900 text-white font-bold rounded px-4 py-2 m-4"
        onClick={() => clearInterval(intervalRef.current)}
      >
        Stop Printing
      </button>
    </div>
  );
};
```

**Why this works:**
- `intervalRef.current` stores the interval ID
- When the "Stop Printing" button is clicked, `clearInterval(intervalRef.current)` correctly references the stored ID
- The ref persists between renders — if any state changes cause a re-render, `intervalRef.current` still holds the interval ID

**Behavior observed:**
- Navigate to demo page → interval starts, logs every second
- Click "Stop Printing" → interval stops immediately
- Navigate away (home page) → cleanup runs, interval stops (from `useEffect` return)
- Navigate back → interval starts again (new mount, new effect)

---

## 19. ref Does NOT Persist Across Mount/Unmount

An important distinction the instructor demonstrates:

**Persists:**
- Between re-renders (state updates) on the same mounted component

**Does NOT persist:**
- When a component is unmounted and remounted (navigating away and back)

```
Visit /demo     → Component mounts, intervalRef.current = interval ID, ref = 0
Re-renders      → ref still holds interval ID, ref counter persists
Navigate to /   → Component UNMOUNTS, ref variable is gone from memory
Navigate to /demo → Component REMOUNTS, useRef(null) runs again, ref = 0
```

This is identical to `useState` behavior on unmount. No React value (state or ref) persists across a complete unmount/remount cycle — everything starts fresh.

The instructor clarifies this with the key principle:
> "Ref is persisted between every render cycle — that reconciliation, that update cycle. But does it persist after mounting and unmounting? Everything that whole component goes off and comes back. So that is why — all state, ref, everything is fresh." — Instructor

---

## 20. Summary — let vs State vs Ref Comparison Table

| Feature | `let x = 0` | `const [x, setX] = useState(0)` | `const ref = useRef(0)` |
|---|---|---|---|
| **Triggers re-render on change?** | No | Yes | No |
| **Persists across re-renders?** | No (resets) | Yes | Yes |
| **Persists across mount/unmount?** | No | No | No |
| **How to read** | `x` | `x` | `ref.current` |
| **How to update** | `x = x + 1` | `setX(x + 1)` | `ref.current = ref.current + 1` |
| **Returns** | value | [value, setter] | `{ current: value }` |
| **UI updates when changed?** | No | Yes | No |
| **React tracks it?** | No | Yes | Partially (persists, no re-render) |

**When to use each:**
- **let**: For temporary values that don't need persistence across renders (loop variables, calculations within the render)
- **State**: When the value affects the UI and should trigger a re-render
- **Ref**: When you need a value that persists across renders but changing it should NOT cause a re-render (interval IDs, DOM element references, previous value storage)

---

## 21. Summary Interview Q&A

**Q1: What are the most important React hooks and how often are they used?**

The two most important React hooks are `useState` and `useEffect` — these cover 99% of use cases. `useState` manages state and triggers re-renders. `useEffect` handles side effects like API calls and subscriptions. Other hooks like `useMemo`, `useCallback`, and `useRef` are used much less frequently, only in specific performance optimization or reference storage scenarios. Hooks like `useParams` and `useNavigate` are not core React hooks — they come from external libraries like React Router.

---

**Q2: What is useMemo and when would you use it?**

`useMemo` is a React hook that caches (memoizes) the result of an expensive calculation between re-renders. You use it when you have a heavy computation inside a component that would run unnecessarily on every re-render.

Example: If you're calculating the nth prime number and also have a theme toggle in the same component, without `useMemo` the prime calculation runs even when only the theme changes. With `useMemo(() => findPrime(number), [number])`, the calculation only re-runs when `number` changes — the theme toggle is instant.

You should NOT use `useMemo` for lightweight operations — the overhead of comparing dependencies costs more than just running the calculation.

---

**Q3: What is the difference between useMemo and useCallback?**

`useMemo` caches the **result** of a function call (a value). `useCallback` caches the **function itself** (a function definition).

```jsx
// useMemo — prime is a number
const prime = useMemo(() => findPrime(n), [n]);

// useCallback — getPrime is a function
const getPrime = useCallback(() => findPrime(n), [n]);
getPrime(); // must call it to get the number
```

`useCallback` is useful when passing callbacks as props to child components — it prevents the child from re-rendering unnecessarily because the function reference stays the same across renders.

---

**Q4: What is the difference between a let variable, a state variable, and a useRef variable in a React component?**

Three key differences: **re-rendering**, **persistence across renders**, and **how to update**:

- **let variable** (`let x = 0`): Incrementing it doesn't cause a re-render, AND it resets to the initial value on every re-render. This is because each render creates a new JavaScript execution context — a fresh function call — and `let x = 0` runs again from scratch.

- **State variable** (`useState`): Changing state causes a re-render AND the value persists. React stores state outside the component function so it survives re-calls.

- **Ref variable** (`useRef`): Changing `ref.current` does NOT cause a re-render, but the value PERSISTS across re-renders. It's like a mutable container that React maintains between renders without triggering the reconciliation process.

---

**Q5: What does useRef return and how do you access/update the value?**

`useRef(initialValue)` returns an **object** with a single property: `current`. The initial value you pass becomes `ref.current`.

```jsx
const ref = useRef(0);
// ref is { current: 0 }

// Access:
console.log(ref.current); // 0

// Update (no setter function — mutate directly):
ref.current = ref.current + 1;
```

---

**Q6: What is a real-world use case for useRef?**

Storing a `setInterval` or `setTimeout` ID so you can clear it from a button click handler. You can't use a `let` variable because React warns "assignment to variable from inside React Hook will be lost after each render." A state variable would cause an unnecessary re-render. A `useRef` persists the ID across renders without triggering re-renders:

```jsx
const intervalRef = useRef(null);

useEffect(() => {
  intervalRef.current = setInterval(() => console.log("tick"), 1000);
  return () => clearInterval(intervalRef.current);
}, []);

// Button can now stop the interval anytime
<button onClick={() => clearInterval(intervalRef.current)}>Stop</button>
```

---

**Q7: Why does a let variable inside a React component reset on every re-render?**

Because a React component is just a JavaScript function. Every time the component re-renders, React calls the function again. JavaScript creates a brand new **execution context** for each function call — fresh memory, fresh variables. `let x = 0` runs from the top again, so `x` starts at 0 every time.

State variables are different — React stores them outside the function in its internal Fiber structure, and passes them back to the function each render. That's why state persists while `let` doesn't.

---

**Q8: What is React StrictMode and why does it call components twice?**

`React.StrictMode` is a development tool that wraps your app to detect potential problems. In development, it intentionally calls component functions twice per render to verify that your component is a "pure function" — producing the same result both times. If the two renders produce different results, there's a bug in your code.

This double-rendering only happens in development (never in production). It helps catch side effects written incorrectly, impure renders, or outdated lifecycle usage.

---

## 22. Coding Exercises

### Exercise 1: useMemo — Slow Calculation
Build a component with a slow calculation and memoize it:

```jsx
import { useState, useMemo } from "react";

// Simulate a slow calculation
function slowDouble(n) {
  console.log("Calculating...");
  // Artificial delay simulation
  let result = 0;
  for (let i = 0; i < n * 1000000; i++) {
    result = i * 2;
  }
  return n * 2;
}

const MemoDemo = () => {
  const [count, setCount] = useState(1);
  const [isDark, setIsDark] = useState(false);

  // Without useMemo: slowDouble runs on every render (even theme toggle)
  // const doubled = slowDouble(count);

  // With useMemo: slowDouble only runs when count changes
  const doubled = useMemo(() => slowDouble(count), [count]);

  return (
    <div className={`p-8 ${isDark ? "bg-gray-900 text-white" : "bg-white"}`}>
      <h2>Count: {count}</h2>
      <h2>Doubled: {doubled}</h2>

      <button
        className="bg-blue-500 text-white px-4 py-2 m-2 rounded"
        onClick={() => setCount(count + 1)}
      >
        Increment Count
      </button>
      <button
        className="bg-green-500 text-white px-4 py-2 m-2 rounded"
        onClick={() => setIsDark(!isDark)}
      >
        Toggle Theme
      </button>
    </div>
  );
};
```

---

### Exercise 2: useCallback — Stable Function Reference
Demonstrate useCallback preventing child re-renders:

```jsx
import { useState, useCallback, memo } from "react";

// Without memo(), this re-renders even when props haven't changed
const ChildButton = memo(({ onClick, label }) => {
  console.log(`${label} rendered`);
  return (
    <button className="bg-purple-500 text-white px-4 py-2 m-2 rounded" onClick={onClick}>
      {label}
    </button>
  );
});

const CallbackDemo = () => {
  const [count, setCount] = useState(0);
  const [theme, setTheme] = useState("light");

  // Without useCallback: new function created every render
  // → ChildButton re-renders even when count doesn't change
  // const handleIncrement = () => setCount(count + 1);

  // With useCallback: same function reference reused
  // → ChildButton only re-renders when needed
  const handleIncrement = useCallback(() => setCount(c => c + 1), []);

  return (
    <div>
      <h2>Count: {count}</h2>
      <ChildButton onClick={handleIncrement} label="Increment" />
      <button
        className="bg-gray-300 px-4 py-2 m-2 rounded"
        onClick={() => setTheme(t => t === "light" ? "dark" : "light")}
      >
        Toggle Theme (should not re-render ChildButton)
      </button>
    </div>
  );
};
```

---

### Exercise 3: Three Variable Types — The Classic Demo
Build the exact demo from the episode:

```jsx
import { useState, useRef } from "react";

const ThreeVariablesDemo = () => {
  let x = 0;                        // let variable
  const [y, setY] = useState(0);    // state variable
  const ref = useRef(0);            // ref variable

  console.log("Rendering! x:", x, "| y:", y, "| ref:", ref.current);

  return (
    <div className="m-4 p-4 border-2 border-gray-400 rounded-lg w-80">
      <h2 className="font-bold text-xl mb-4">Variable Comparison</h2>

      <div className="mb-4 font-mono bg-gray-100 p-2 rounded">
        <p>let x = {x}</p>
        <p>state y = {y}</p>
        <p>ref.current = {ref.current}</p>
      </div>

      <button
        className="bg-red-100 border border-red-400 px-3 py-1 m-1 rounded text-sm"
        onClick={() => { x = x + 1; console.log("x is now:", x); }}
      >
        Increase X (let)
      </button>

      <button
        className="bg-blue-100 border border-blue-400 px-3 py-1 m-1 rounded text-sm"
        onClick={() => setY(y + 1)}
      >
        Increase Y (state)
      </button>

      <button
        className="bg-purple-100 border border-purple-400 px-3 py-1 m-1 rounded text-sm"
        onClick={() => { ref.current += 1; console.log("ref is now:", ref.current); }}
      >
        Increase Ref
      </button>

      <p className="mt-4 text-xs text-gray-500">
        Click X and Ref multiple times, then click Y to see what persists!
      </p>
    </div>
  );
};
```

**Expected behavior to observe:**
1. Click "Increase X" 3 times → console shows x:1, x:2, x:3 but UI still shows x=0
2. Click "Increase Ref" 3 times → console shows ref:1, ref:2, ref:3 but UI still shows ref=0
3. Click "Increase Y" once → UI updates, shows x=0, y=1, ref=3
   - x reset (let resets on render)
   - y incremented (state persisted + caused render)
   - ref shows 3 (ref persisted across the render!)

---

### Exercise 4: useRef for setInterval
Build a timer that can be started and stopped:

```jsx
import { useState, useRef, useEffect } from "react";

const Timer = () => {
  const [count, setCount] = useState(0);
  const [isRunning, setIsRunning] = useState(false);
  const intervalRef = useRef(null);  // store the interval ID

  const startTimer = () => {
    if (intervalRef.current) return; // already running
    intervalRef.current = setInterval(() => {
      setCount(c => c + 1);
    }, 1000);
    setIsRunning(true);
  };

  const stopTimer = () => {
    clearInterval(intervalRef.current);
    intervalRef.current = null;
    setIsRunning(false);
  };

  const resetTimer = () => {
    stopTimer();
    setCount(0);
  };

  useEffect(() => {
    // Cleanup on unmount
    return () => clearInterval(intervalRef.current);
  }, []);

  return (
    <div className="p-8 text-center">
      <h1 className="text-6xl font-bold mb-8">{count}s</h1>
      <div className="flex gap-4 justify-center">
        <button
          className="bg-green-500 text-white px-6 py-2 rounded-lg disabled:opacity-50"
          onClick={startTimer}
          disabled={isRunning}
        >
          Start
        </button>
        <button
          className="bg-red-500 text-white px-6 py-2 rounded-lg disabled:opacity-50"
          onClick={stopTimer}
          disabled={!isRunning}
        >
          Stop
        </button>
        <button
          className="bg-gray-500 text-white px-6 py-2 rounded-lg"
          onClick={resetTimer}
        >
          Reset
        </button>
      </div>
    </div>
  );
};
```

---

### Exercise 5: Spot the Bug — Which Variable Type?
For each scenario, choose the right variable type:

```jsx
// SCENARIO 1: You want to count how many times the user clicked a button
//             and show that count in the UI
// Answer: useState — changing the value should update the UI

// SCENARIO 2: You want to store a timeout ID so you can cancel it later
//             (the ID itself doesn't need to show in UI)
// Answer: useRef — persists across renders, no re-render needed

// SCENARIO 3: You have a loop variable inside a function called during render
// Answer: let — temporary, used once, no persistence needed

// SCENARIO 4: You want to cache an expensive calculation that depends on
//             one state variable, but the component has other state variables too
// Answer: useMemo — cache the result, re-calculate only when that dependency changes

// SCENARIO 5: You want to pass a handler function down to a child component
//             and prevent the child from re-rendering when other state changes
// Answer: useCallback — cache the function reference
```

---

## 23. Quick Quiz

1. Where is the official React documentation?
2. What are the two most important React hooks?
3. Why does a component render twice in development when StrictMode is on?
4. Does the double-render from StrictMode happen in production?
5. What does useMemo cache?
6. What are the two parameters you pass to useMemo?
7. When does useMemo re-run its function?
8. What does useCallback cache? How is it different from useMemo?
9. Name the three ways to store a value inside a React component.
10. Does changing a `let` variable inside a component trigger a re-render?
11. What happens to a `let` variable when the component re-renders?
12. Why does a `let` variable reset on every render? (The JavaScript reason)
13. What does `useRef(0)` return?
14. How do you read a ref's value?
15. How do you update a ref's value?
16. Does changing `ref.current` trigger a re-render?
17. Does a ref value persist across re-renders?
18. Does a ref value persist if the component unmounts and remounts?
19. What is a practical use case for useRef?
20. What does React warn you if you store a setInterval ID in a let variable?

**Answers:**
1. `react.dev` (the new official docs — not the old react.org)
2. `useState` and `useEffect`
3. StrictMode calls component functions twice to verify renders are consistent/pure — helps detect side effects written incorrectly
4. No — it only happens in development mode
5. The **result** (value) returned by an expensive function call
6. First: a function `() => expensiveCalc(dep)`, Second: a dependency array `[dep]`
7. Only when one of its dependencies changes (otherwise returns the cached result)
8. useCallback caches the **function itself** (its definition/reference), not its return value. useMemo → `cachedValue`, useCallback → `cachedFunction`
9. `let` variable, state variable (`useState`), ref variable (`useRef`)
10. No — `let` changes are invisible to React, no re-render is triggered
11. It resets to its initial value (e.g., `let x = 0` runs again from the top)
12. A React component is a JavaScript function. Every re-render = a new function call = a new execution context = all `let`/`const` variables are created fresh in memory
13. An object: `{ current: 0 }`
14. `ref.current`
15. Direct mutation: `ref.current = newValue` (no setter function like useState)
16. No — updating `ref.current` does NOT trigger a re-render
17. Yes — ref values survive re-renders (unlike `let`)
18. No — when a component unmounts, all its refs are destroyed. When it remounts, `useRef(initialValue)` starts fresh
19. Storing a `setInterval` or `setTimeout` ID so you can cancel it from a button click, without causing a re-render when the ID is stored
20. "Assignment to variable from inside React Hook will be lost after each render" — React itself tells you to use `useRef` instead
