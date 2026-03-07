# React — Missing Topics Part 4 (Final)

> **Still missing after Parts 1–3:**
> Stale Closures in useEffect · useEffect Dependency Gotchas · CSS Modules ·
> React.Children & cloneElement · Context Performance & Splitting ·
> React.memo Custom Comparator · React Compiler · "How Would You…" Scenario Questions ·
> Complete State Management Comparison

---

## Table of Contents
1. [Stale Closures in useEffect — The #1 Bug](#1-stale-closures-in-useeffect--the-1-bug)
2. [useEffect Dependency Array — All the Gotchas](#2-useeffect-dependency-array--all-the-gotchas)
3. [CSS Modules](#3-css-modules)
4. [React.Children and cloneElement](#4-reactchildren-and-cloneelement)
5. [Context Performance — Preventing Unnecessary Re-renders](#5-context-performance--preventing-unnecessary-re-renders)
6. [React.memo — Custom Comparator (Second Argument)](#6-reactmemo--custom-comparator-second-argument)
7. [React Compiler — Auto-Memoization (React 19)](#7-react-compiler--auto-memoization-react-19)
8. ["How Would You…" — Scenario Interview Questions](#8-how-would-you--scenario-interview-questions)
9. [Complete State Management Comparison](#9-complete-state-management-comparison)

---

## 1. Stale Closures in useEffect — The #1 Bug

### Interview Question
**"What is a stale closure in React? How does it happen in useEffect and how do you fix it?"**

### Answer (Conversational)

A **stale closure** is when a function "closes over" a variable from a previous render — so it's reading an **old, outdated value** instead of the current one. This is the most common useEffect bug in interviews and in real apps.

#### The Problem

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    const timer = setInterval(() => {
      console.log(count); // ← STALE! Always prints 0
      setCount(count + 1); // ← WRONG! Always sets to 0 + 1 = 1
    }, 1000);

    return () => clearInterval(timer);
  }, []); // ← empty dep array = runs once = captures count = 0 forever

  return <p>{count}</p>;
}
```

Here's what happens step by step:
1. Component renders, `count = 0`
2. `useEffect` runs once — the callback **closes over** `count = 0`
3. Every second, the interval fires — it reads `count` from the closure → always `0`
4. `setCount(0 + 1)` → always sets to `1` → counter is stuck at 1!

The closure captured `count = 0` at mount and never updated its reference.

---

#### Fix 1: Add `count` to the Dependency Array

```jsx
useEffect(() => {
  const timer = setInterval(() => {
    setCount(count + 1); // now 'count' is always current
  }, 1000);

  return () => clearInterval(timer); // cleanup runs before re-subscribing
}, [count]); // re-run when count changes
```

**Problem with this fix:** The interval is torn down and recreated every second — which works but is wasteful for intervals.

---

#### Fix 2: Functional Updater Form (Best for setCount)

```jsx
useEffect(() => {
  const timer = setInterval(() => {
    setCount(prev => prev + 1); // prev is ALWAYS the latest value — no closure needed!
  }, 1000);

  return () => clearInterval(timer);
}, []); // empty deps — runs once, no stale closure
```

The **functional updater form** `setState(prev => ...)` doesn't close over state — React guarantees `prev` is the latest value. This is the cleanest fix for counters/toggles.

---

#### Fix 3: useRef to Always Get Latest Value

For non-state values (like callbacks), store the latest value in a ref:

```jsx
function SearchWithCallback({ onSearch }) {
  const onSearchRef = useRef(onSearch);

  // Keep the ref updated on every render:
  useEffect(() => {
    onSearchRef.current = onSearch;
  });

  useEffect(() => {
    const timer = setInterval(() => {
      onSearchRef.current("query"); // always calls the latest version of onSearch
    }, 1000);
    return () => clearInterval(timer);
  }, []); // onSearch is NOT in deps — ref handles it
}
```

This is the pattern used by `useEvent` (an upcoming React hook) and many libraries internally.

---

#### Another Classic Stale Closure Example

```jsx
function App() {
  const [name, setName] = useState("Alice");

  function handleClick() {
    setTimeout(() => {
      alert(name); // ← What does this show?
    }, 3000);
  }

  return (
    <>
      <input value={name} onChange={e => setName(e.target.value)} />
      <button onClick={handleClick}>Alert in 3s</button>
    </>
  );
}
```

If you click the button when `name = "Alice"`, then immediately type "Bob", the alert shows **"Alice"** — not "Bob". The `handleClick` closure captured `name = "Alice"` at click time.

**Fix:** Use a ref to always read the latest value:
```jsx
const nameRef = useRef(name);
useEffect(() => { nameRef.current = name; }); // sync on every render

function handleClick() {
  setTimeout(() => {
    alert(nameRef.current); // always reads the latest name
  }, 3000);
}
```

### Coding Questions

**Q1:** What does this log — 0 or 5?
```jsx
const [count, setCount] = useState(0);

useEffect(() => {
  setCount(5);
  setTimeout(() => {
    console.log(count); // ?
  }, 1000);
}, []);
```

**Answer:** `0` — stale closure. The `setTimeout` callback captured `count = 0` when the effect ran. Even though `setCount(5)` was called, the closure still holds the old value. The actual rendered UI shows 5, but the `console.log` inside the closure reads 0.

---

**Q2:** Fix this so the interval always increments correctly:
```jsx
useEffect(() => {
  const id = setInterval(() => setCount(count + 1), 1000);
  return () => clearInterval(id);
}, []);
```

**Answer:**
```jsx
useEffect(() => {
  const id = setInterval(() => setCount(prev => prev + 1), 1000); // functional updater
  return () => clearInterval(id);
}, []);
```

---

## 2. useEffect Dependency Array — All the Gotchas

### Interview Question
**"What are the most common bugs caused by the useEffect dependency array?"**

### Answer (Conversational)

The dependency array is where most `useEffect` bugs live. Here are all the major gotchas.

---

#### Gotcha 1: Objects and Arrays as Dependencies → Infinite Loop

```jsx
// ❌ INFINITE LOOP — new object created on every render:
function UserProfile({ userId }) {
  const options = { userId, theme: "dark" }; // NEW object every render!

  useEffect(() => {
    fetchUser(options);
  }, [options]); // React does shallow comparison — new object !== old object → always runs!
}
```

Every render creates a new `options` object — even if the values are identical. React uses `Object.is()` for comparison, which checks **reference equality**, not deep equality. New reference = dependency changed = effect re-runs = new render = new object... **infinite loop**.

**Fixes:**
```jsx
// Fix 1: Use primitive values as deps (not the whole object):
useEffect(() => {
  fetchUser({ userId, theme: "dark" });
}, [userId]); // userId is a primitive — safe

// Fix 2: Move the object inside the effect (not a dep at all):
useEffect(() => {
  const options = { userId, theme: "dark" }; // created inside effect
  fetchUser(options);
}, [userId]); // only depend on userId

// Fix 3: useMemo to stabilize the object reference:
const options = useMemo(() => ({ userId, theme: "dark" }), [userId]);
useEffect(() => {
  fetchUser(options);
}, [options]); // options reference only changes when userId changes
```

---

#### Gotcha 2: Functions as Dependencies → Infinite Loop

```jsx
// ❌ INFINITE LOOP — function recreated every render:
function Component({ onData }) {
  useEffect(() => {
    onData(fetchResult); // onData is called inside effect
  }, [onData]); // onData is a new function reference every render!
}

// Parent passes a new function reference every time it renders:
<Component onData={(data) => setData(data)} />
// ↑ new arrow function = new reference = dependency changed = effect runs = re-render...
```

**Fix:** `useCallback` in the parent to stabilize the function reference:
```jsx
// In Parent:
const handleData = useCallback((data) => setData(data), []);
<Component onData={handleData} />
```

---

#### Gotcha 3: Missing Dependencies → Stale Closure (see Section 1)

```jsx
// ❌ Stale — ESLint will warn: 'userId' is missing from deps
useEffect(() => {
  fetchUser(userId); // uses userId but it's not in deps
}, []); // if userId changes, effect doesn't re-run → stale data shown
```

**Always trust the ESLint exhaustive-deps rule.** It catches exactly this.

---

#### Gotcha 4: The "Run Once" Trap

```jsx
// ❌ Common misconception — "I want this to run only on mount":
useEffect(() => {
  fetchInitialData(userId); // but what if userId changes?
}, []); // empty deps = only on mount = ignores userId changes
```

Ask yourself: "Should this effect re-run if any of these values change?" If yes, add them to deps. If truly once-on-mount (setting up a library, one-time event listener), empty deps is correct.

---

#### Gotcha 5: Async Functions in useEffect

```jsx
// ❌ WRONG — can't make useEffect callback async directly:
useEffect(async () => {
  const data = await fetchData(); // React warns: effect returns a Promise, not a cleanup fn
  setData(data);
}, []);

// ✓ CORRECT — define async inside the effect:
useEffect(() => {
  async function fetchAndSet() {
    const data = await fetchData();
    setData(data);
  }
  fetchAndSet(); // call it immediately
}, []);

// ✓ ALSO CORRECT — IIFE:
useEffect(() => {
  (async () => {
    const data = await fetchData();
    setData(data);
  })();
}, []);
```

**Why?** `useEffect` expects the callback to return either `undefined` or a cleanup function. An `async` function always returns a Promise — not a cleanup function.

---

#### Gotcha 6: Race Conditions in Async Effects

```jsx
// ❌ Race condition — fast typing can show wrong results:
function Search({ query }) {
  const [results, setResults] = useState([]);

  useEffect(() => {
    searchAPI(query).then(data => {
      setResults(data); // if "apple" response arrives AFTER "apps" response → wrong order!
    });
  }, [query]);
}

// ✓ Fix — ignore responses from cancelled requests:
useEffect(() => {
  let cancelled = false;

  searchAPI(query).then(data => {
    if (!cancelled) setResults(data); // only set if still current
  });

  return () => { cancelled = true; }; // cleanup cancels on next render
}, [query]);

// ✓ Even better — AbortController (from Async notes):
useEffect(() => {
  const controller = new AbortController();
  fetch(`/api/search?q=${query}`, { signal: controller.signal })
    .then(r => r.json())
    .then(setResults)
    .catch(err => { if (err.name !== "AbortError") setError(err); });

  return () => controller.abort();
}, [query]);
```

### Coding Questions

**Q1:** What's wrong with this code? What will happen?
```jsx
function App() {
  const [data, setData] = useState(null);
  const config = { timeout: 3000 };

  useEffect(() => {
    fetchData(config).then(setData);
  }, [config]);

  return <div>{data}</div>;
}
```

**Answer:** Infinite loop. `config` is a new object on every render. The effect sees a new reference → runs → triggers re-render → new config → runs again...

**Fix:** Move `config` inside the effect or use `useMemo`, or just use the primitive values as deps.

---

## 3. CSS Modules

### Interview Question
**"What are CSS Modules? How do they solve the global CSS problem?"**

### Answer (Conversational)

**CSS Modules** are CSS files where all class names are **automatically scoped locally** to the component that imports them. You write normal CSS, but class names get transformed into unique identifiers at build time — so there are never any naming conflicts.

---

#### The Problem CSS Modules Solve

In a large app with regular CSS, class names are global:

```css
/* Button.css */
.button { background: blue; }

/* Card.css */
.button { background: red; } /* ← CONFLICT! Which .button wins? */
```

This leads to naming collisions, specificity wars, and unpredictable styles.

---

#### How CSS Modules Work

```css
/* Button.module.css — note the .module.css extension */
.button {
  background: blue;
  padding: 8px 16px;
  border-radius: 4px;
}

.primary {
  background: #0070f3;
}

.danger {
  background: red;
}
```

```jsx
// Button.jsx
import styles from "./Button.module.css"; // import as an object

function Button({ variant = "primary", children }) {
  return (
    <button className={styles.button}>  {/* styles.button → unique class */}
      {children}
    </button>
  );
}

// Multiple classes:
<button className={`${styles.button} ${styles.primary}`}>
  Click me
</button>

// Conditional class:
<button className={`${styles.button} ${variant === "danger" ? styles.danger : styles.primary}`}>
  {children}
</button>
```

In the browser, `.button` becomes something like `Button_button__3kj2F` — unique to this component. No other component's `.button` will ever conflict.

---

#### Using clsx or classnames Library

For conditional classes, the `clsx` library is cleaner than template literals:

```bash
npm install clsx
```

```jsx
import clsx from "clsx";
import styles from "./Button.module.css";

function Button({ variant, disabled, className }) {
  return (
    <button
      className={clsx(
        styles.button,                    // always applied
        styles[variant],                  // styles.primary or styles.danger
        disabled && styles.disabled,      // only if disabled is true
        className                         // pass-through from parent
      )}
      disabled={disabled}
    >
      Click me
    </button>
  );
}
```

---

#### CSS Modules vs Other Styling Approaches

| Approach | Scoped? | JS Integration | Learning Curve | Best For |
|---|---|---|---|---|
| **Plain CSS** | No | None | Low | Simple projects |
| **CSS Modules** | Yes | Import as object | Low | Component-level styles without JS |
| **Styled Components** | Yes | Full (tagged templates) | Medium | Styled with props/themes |
| **Tailwind CSS** | Yes (utility-first) | className strings | Medium | Rapid UI, design systems |
| **Inline Styles** | Yes | Full (style object) | Low | Truly dynamic styles |

---

#### Composing Classes in CSS Modules

```css
/* shared.module.css */
.base {
  font-family: sans-serif;
  border: none;
  cursor: pointer;
}

/* Button.module.css */
.button {
  composes: base from "./shared.module.css"; /* inherit from another module */
  background: blue;
}
```

---

## 4. React.Children and cloneElement

### Interview Question
**"What are `React.Children` and `React.cloneElement`? When would you use them?"**

### Answer (Conversational)

These are advanced React APIs for **working with the children prop** programmatically — iterating over children, passing props down to them, or modifying them.

---

#### The Problem They Solve

Normally, `children` is opaque — you just render it. But sometimes a parent component needs to:
- Count the children
- Inject additional props into each child
- Render only certain children

---

#### React.Children.map — Iterate Over Children Safely

`Array.map()` on `children` can break if there's only one child (it won't be an array). `React.Children.map` handles all cases:

```jsx
function ButtonGroup({ children }) {
  // React.Children.map works whether there are 1, 2, or many children:
  return (
    <div className="button-group">
      {React.Children.map(children, (child, index) => (
        <div className="button-wrapper" key={index}>
          {child}
        </div>
      ))}
    </div>
  );
}

// Usage:
<ButtonGroup>
  <button>Save</button>
  <button>Cancel</button>
  <button>Delete</button>
</ButtonGroup>
```

**Other `React.Children` utilities:**
```jsx
React.Children.count(children);        // number of children
React.Children.toArray(children);      // convert to flat array
React.Children.only(children);         // assert exactly one child (throws if not)
React.Children.forEach(children, fn);  // like map but no return value
```

---

#### React.cloneElement — Inject Props Into Children

`cloneElement` creates a copy of a React element with additional (or overriding) props:

```jsx
React.cloneElement(element, newProps, ...newChildren)
```

**Use case: Parent injects props into children without the children explicitly asking**

```jsx
// A RadioGroup that injects 'name' and 'checked' into each Radio child:
function RadioGroup({ name, value, onChange, children }) {
  return (
    <fieldset>
      {React.Children.map(children, child =>
        React.cloneElement(child, {
          name,                        // inject name prop
          checked: child.props.value === value, // inject checked state
          onChange,                    // inject onChange handler
        })
      )}
    </fieldset>
  );
}

function Radio({ label, value, name, checked, onChange }) {
  return (
    <label>
      <input
        type="radio"
        name={name}
        value={value}
        checked={checked}
        onChange={() => onChange(value)}
      />
      {label}
    </label>
  );
}

// Usage — clean API, no prop drilling:
<RadioGroup name="size" value={size} onChange={setSize}>
  <Radio label="Small" value="sm" />
  <Radio label="Medium" value="md" />
  <Radio label="Large" value="lg" />
</RadioGroup>
```

The `Radio` components don't receive `name`, `checked`, or `onChange` from the JSX — the `RadioGroup` injects them via `cloneElement`.

---

#### When to Use vs Context

`cloneElement` is used for **direct parent→child prop injection** when the components are tightly coupled. For **deeply nested** situations or unrelated components, use Context instead.

| | `cloneElement` | Context |
|---|---|---|
| Coupling | Tight (parent/child) | Loose (any depth) |
| API | Explicit (visible in JSX) | Implicit (consumed anywhere) |
| Best for | UI library internals (RadioGroup, Tabs) | App-wide data (theme, user, cart) |

---

## 5. Context Performance — Preventing Unnecessary Re-renders

### Interview Question
**"What is the performance problem with React Context? How do you fix it?"**

### Answer (Conversational)

Context has a hidden performance trap that catches a lot of developers: **every component that consumes a context re-renders whenever ANY value in that context changes** — even if the specific value it uses didn't change.

---

#### The Problem

```jsx
// Putting too much in one context:
const AppContext = createContext(null);

function AppProvider({ children }) {
  const [user, setUser] = useState(null);
  const [theme, setTheme] = useState("light");
  const [cartCount, setCartCount] = useState(0);
  const [notifications, setNotifications] = useState([]);

  return (
    <AppContext.Provider value={{ user, setUser, theme, setTheme, cartCount, setCartCount, notifications }}>
      {children}
    </AppContext.Provider>
  );
}

// This component only cares about theme:
function Header() {
  const { theme } = useContext(AppContext); // but re-renders when cartCount changes!
  return <header className={theme}>...</header>;
}

// This component only cares about cartCount:
function CartIcon() {
  const { cartCount } = useContext(AppContext); // re-renders when user changes!
  return <span>{cartCount}</span>;
}
```

Every time a notification arrives, `Header` and `CartIcon` both re-render unnecessarily.

---

#### Fix 1: Split Context by Concern

```jsx
// Separate context for each concern:
const UserContext = createContext(null);
const ThemeContext = createContext("light");
const CartContext = createContext(null);
const NotificationContext = createContext([]);

// Now Header only subscribes to ThemeContext:
function Header() {
  const theme = useContext(ThemeContext); // only re-renders when theme changes!
  return <header className={theme}>...</header>;
}

// CartIcon only subscribes to CartContext:
function CartIcon() {
  const { cartCount } = useContext(CartContext); // only re-renders when cart changes!
  return <span>{cartCount}</span>;
}
```

**Rule:** Each context should hold **one concern** that changes together.

---

#### Fix 2: Memoize Context Value

If you must keep things in one context, memoize the value to prevent unnecessary re-renders:

```jsx
function AppProvider({ children }) {
  const [user, setUser] = useState(null);
  const [theme, setTheme] = useState("light");

  // Only create a new value object when user or theme actually changes:
  const value = useMemo(() => ({ user, setUser, theme, setTheme }), [user, theme]);

  return (
    <AppContext.Provider value={value}>
      {children}
    </AppContext.Provider>
  );
}
```

Without `useMemo`, the `value` object is a **new reference** on every render of `AppProvider` — even if `user` and `theme` didn't change — causing all consumers to re-render.

---

#### Fix 3: Separate State and Dispatch Contexts

A popular pattern from Redux — separate reading state from writing state. The dispatch function never changes (stable reference), so components that only dispatch never re-render when state changes:

```jsx
const CartStateContext = createContext(null);   // for reading
const CartDispatchContext = createContext(null); // for writing

function CartProvider({ children }) {
  const [cart, dispatch] = useReducer(cartReducer, []);

  return (
    <CartDispatchContext.Provider value={dispatch}> {/* stable reference */}
      <CartStateContext.Provider value={cart}>
        {children}
      </CartStateContext.Provider>
    </CartDispatchContext.Provider>
  );
}

// Only re-renders when cart state changes:
function CartList() {
  const cart = useContext(CartStateContext);
  return <ul>{cart.map(item => <li>{item.name}</li>)}</ul>;
}

// NEVER re-renders from cart changes (dispatch is stable):
function AddToCartButton({ item }) {
  const dispatch = useContext(CartDispatchContext);
  return <button onClick={() => dispatch({ type: "ADD", item })}>Add</button>;
}
```

---

#### Fix 4: React.memo on Context Consumers

If you can't split contexts, wrap consumers in `React.memo`:

```jsx
const CartIcon = React.memo(function CartIcon() {
  const { cartCount } = useContext(AppContext);
  return <span>{cartCount}</span>;
  // Still re-renders when cartCount changes — but NOT for user, theme, notifications
});
```

Wait — does `React.memo` help here? **Not by itself**. `React.memo` skips re-renders when *props* don't change. But context changes bypass `React.memo` — the component still re-renders when any context value changes. You need one of the above solutions (split contexts or memoize value).

---

## 6. React.memo — Custom Comparator (Second Argument)

### Interview Question
**"What is the second argument to React.memo? When would you use a custom comparator?"**

### Answer (Conversational)

`React.memo` has an optional **second argument** — a custom comparison function. By default, React.memo does a **shallow comparison** of all props. The custom comparator lets you define exactly when the component should re-render.

```jsx
const MemoizedComponent = React.memo(MyComponent, arePropsEqual);

// arePropsEqual(prevProps, nextProps) → return true to SKIP re-render
//                                     → return false to RE-RENDER
```

**Important:** It's the **opposite** of `shouldComponentUpdate`:
- `shouldComponentUpdate` returns `true` to render, `false` to skip
- `arePropsEqual` returns `true` to **SKIP** (they're equal), `false` to render

---

#### When Default Shallow Comparison Fails

```jsx
// Props with objects/arrays — shallow comparison says "not equal" every time!
const UserCard = React.memo(function UserCard({ user, tags }) {
  return <div>{user.name}: {tags.join(", ")}</div>;
});

function Parent() {
  return (
    // New object and array reference on every Parent render → memo never skips!
    <UserCard user={{ name: "Alice" }} tags={["admin", "user"]} />
  );
}
```

Even though the data is identical, the references are new — default shallow comparison re-renders every time.

---

#### Custom Comparator — Deep Compare Specific Fields

```jsx
const UserCard = React.memo(
  function UserCard({ user, tags }) {
    return <div>{user.name}: {tags.join(", ")}</div>;
  },
  (prevProps, nextProps) => {
    // Return true (skip re-render) if BOTH conditions are true:
    return (
      prevProps.user.id === nextProps.user.id &&      // same user
      prevProps.user.name === nextProps.user.name &&  // same name
      prevProps.tags.length === nextProps.tags.length && // same number of tags
      prevProps.tags.every((tag, i) => tag === nextProps.tags[i]) // same tags
    );
  }
);
```

---

#### Practical Example — Skip Re-render if Only Timestamp Changed

```jsx
const MessageItem = React.memo(
  function MessageItem({ message }) {
    return <p>{message.text}</p>;
  },
  (prev, next) => {
    // Only re-render if text changed — ignore timestamp updates:
    return prev.message.text === next.message.text;
  }
);
```

---

#### Warning: Don't Over-Engineer

Custom comparators add complexity and can introduce bugs (you might forget to compare a field that actually matters). The better solutions are usually:
1. Fix the parent to pass stable references (`useMemo` for objects, `useCallback` for functions)
2. Move state closer to where it's needed
3. Use a custom comparator only as a last resort when you can't change the parent

---

## 7. React Compiler — Auto-Memoization (React 19)

### Interview Question
**"What is the React Compiler? How does it change how we write React code?"**

### Answer (Conversational)

The **React Compiler** (previously called React Forget) is a **build-time tool** introduced with React 19 that **automatically adds memoization** to your components. It analyzes your code and inserts `useMemo`, `useCallback`, and `React.memo` optimizations where needed — without you writing them manually.

---

#### The Problem It Solves

Today, preventing unnecessary re-renders requires:
- Manually wrapping components with `React.memo`
- Manually wrapping functions with `useCallback`
- Manually wrapping values with `useMemo`
- Getting the dependency arrays right
- Maintaining all of this as code changes

This is tedious, error-prone, and results in either:
- Under-optimized code (developers skip memoization)
- Over-optimized code (developers memoize everything "just in case")

---

#### What the Compiler Does

```jsx
// You write this (no manual memoization):
function ProductList({ products, onAdd }) {
  const expensive = products
    .filter(p => p.inStock)
    .sort((a, b) => a.price - b.price);

  return (
    <ul>
      {expensive.map(p => (
        <ProductItem key={p.id} product={p} onAdd={onAdd} />
      ))}
    </ul>
  );
}

// The compiler AUTOMATICALLY generates something like:
function ProductList({ products, onAdd }) {
  const expensive = useMemo(
    () => products.filter(p => p.inStock).sort((a, b) => a.price - b.price),
    [products]
  );

  const memoizedOnAdd = useCallback(onAdd, [onAdd]);

  return (
    <ul>
      {expensive.map(p => (
        <ProductItem key={p.id} product={p} onAdd={memoizedOnAdd} />
      ))}
    </ul>
  );
}
```

The compiler figures out what depends on what and applies the minimal memoization automatically.

---

#### Key Rules: Your Code Must Follow Them

The compiler only works if your code follows the **Rules of React**:
- Components are pure functions (same input → same output)
- Never mutate props or state directly
- Hooks are called at the top level, not conditionally

If your code already follows these rules (which it should), the compiler "just works."

---

#### Impact on Developer Workflow

```jsx
// BEFORE React Compiler (today):
// You had to write this manually and maintain dependency arrays:
const handleClick = useCallback(() => doSomething(id), [id]);
const filtered = useMemo(() => items.filter(x => x.active), [items]);
const Child = React.memo(({ data }) => <div>{data.name}</div>);

// AFTER React Compiler:
// Just write normal components — the compiler handles optimization:
function handleClick() { doSomething(id); }
const filtered = items.filter(x => x.active);
function Child({ data }) { return <div>{data.name}</div>; }
```

---

#### Current Status (2024–2025)

- The React Compiler is available as `babel-plugin-react-compiler`
- Meta (Facebook) has been running it in production on Instagram for over a year
- Next.js 15+ has experimental compiler support
- It's opt-in — you don't have to use it
- The plan is for it to eventually be the default

```bash
# Enable in a project:
npm install babel-plugin-react-compiler
```

---

## 8. "How Would You…" — Scenario Interview Questions

### Interview Question
**"How would you optimize a React app that's rendering slowly?"**

### Answer (Conversational)

These scenario questions test whether you can **think like a senior engineer** — not just knowing individual tools but knowing how to diagnose and solve real problems.

---

#### "How would you optimize a slow React app?"

**Step 1: Measure before you optimize**

```
React DevTools Profiler → Record → Interact with the slow part → Stop
Look at: Which components rendered? How long did each take?
```

Never guess — profile first. The slowest component might not be what you expect.

**Step 2: Fix unnecessary re-renders first**

```jsx
// Identify: Is the component re-rendering when props didn't change?
// Fix with: React.memo, useMemo, useCallback

// Check the component tree: Is a high-level state triggering deep re-renders?
// Fix with: Move state down (state colocation)
```

**Step 3: Fix expensive computations**

```jsx
// Identify: Is a heavy calculation running on every render?
// Fix with: useMemo
const sortedList = useMemo(() => expensiveSort(items), [items]);
```

**Step 4: Fix slow lists**

```jsx
// Identify: Is a long list rendering all at once?
// Fix with: react-window for virtualization
import { FixedSizeList } from "react-window";
```

**Step 5: Code splitting for bundle size**

```jsx
// Identify: Is the initial bundle too large?
// Fix with: lazy + Suspense
const HeavyPage = lazy(() => import("./HeavyPage"));
```

---

#### "How would you handle authentication in a React app?"

```
1. On login → store token (httpOnly cookie via server = most secure, or
   localStorage = simpler but XSS risk)

2. Create auth context:
   { user, login, logout, isLoading }

3. Protected routes with <ProtectedRoute /> wrapper
   (covered in file 16)

4. Attach token to all API requests:
   - Axios interceptor (covered in JS notes)
   - Or TanStack Query's global fetcher

5. Handle token expiry:
   - Refresh token rotation
   - Redirect to login on 401 response (axios interceptor)

6. Persist auth state across refresh:
   - Read token from cookie/localStorage on app load
   - Validate with server before showing protected content
```

---

#### "How would you structure a large React app?"

```
src/
  features/           ← feature-based, not type-based!
    auth/
      components/
      hooks/
      api/
      types.ts
    cart/
      components/
      hooks/
      store/
      api/
  shared/             ← truly shared components
    components/
    hooks/
    utils/
  app/
    router.tsx
    store.ts          ← global state
    App.tsx
```

**Key principles:**
- Feature folders over type folders (not `components/`, `hooks/`, `utils/` at the top)
- Co-locate code that changes together
- Shared code lives in `shared/` — if only one feature uses it, keep it in that feature
- Each feature owns its own state, API calls, and types

---

#### "What would you do if a component re-renders too many times?"

```
1. Open React DevTools → Components tab
   → Check "Highlight updates when components render" checkbox
   → Interact with the app → see which components flash

2. Use the Profiler tab to record and see render counts

3. Check the cause:
   a. Parent re-renders → use React.memo on the child
   b. Context changes → split context or memoize context value
   c. Props with new object/array references → use useMemo/useCallback in parent
   d. State that's higher than it needs to be → move state down (colocation)

4. Verify fix → profile again → confirm render count decreased
```

---

#### "How would you implement infinite scroll?"

```jsx
function InfiniteList() {
  const [items, setItems] = useState([]);
  const [page, setPage] = useState(1);
  const [loading, setLoading] = useState(false);
  const observerRef = useRef(null); // ref to the sentinel element

  // Load more when sentinel enters viewport:
  useEffect(() => {
    const observer = new IntersectionObserver(
      (entries) => {
        if (entries[0].isIntersecting && !loading) {
          setPage(p => p + 1); // trigger next page fetch
        }
      },
      { threshold: 0.1 }
    );

    if (observerRef.current) observer.observe(observerRef.current);
    return () => observer.disconnect();
  }, [loading]);

  useEffect(() => {
    setLoading(true);
    fetchItems(page).then(newItems => {
      setItems(prev => [...prev, ...newItems]);
      setLoading(false);
    });
  }, [page]);

  return (
    <div>
      {items.map(item => <Item key={item.id} data={item} />)}
      {loading && <p>Loading more...</p>}
      <div ref={observerRef} /> {/* sentinel — triggers load when visible */}
    </div>
  );
}
```

---

## 9. Complete State Management Comparison

### Interview Question
**"When would you use Context, Redux, Zustand, Jotai, or TanStack Query? How do you choose?"**

### Answer (Conversational)

| Library | Best For | Key Trait | When to Use |
|---|---|---|---|
| **useState/useReducer** | Local component state | Built-in, no install | Single component or tight parent-child |
| **Context API** | Rarely-changing global data | Built-in, simple | Theme, locale, auth user — not frequent updates |
| **Redux Toolkit** | Complex, large-scale state | DevTools, time-travel, very structured | Large teams, complex state machines, need middleware |
| **Zustand** | Simple to medium global state | Minimal boilerplate, no Provider | Small-medium apps, flexible, fast to set up |
| **Jotai** | Atomic state | Atom-based, fine-grained updates | When you need very granular subscriptions |
| **TanStack Query** | Server state (API data) | Cache, background sync, dedup | ANY remote data — replaces fetch+useState+useEffect |
| **Recoil** | Atomic state (Facebook) | Similar to Jotai, Atom-based | Was popular, now less common vs Jotai/Zustand |

---

#### The Golden Decision Tree

```
Is the state only used in one component?
  → useState / useReducer

Is it shared between a few nearby components?
  → Lift state up to common parent

Is it global but changes rarely? (theme, locale, auth)
  → Context API

Is it global and changes frequently? (cart, notifications)
  → Zustand (small app) or Redux Toolkit (large app with team)

Is it data from an API/server?
  → TanStack Query (ALWAYS — don't use Redux for server data)

Do you need extreme granularity / atomic updates?
  → Jotai
```

---

#### Don't Mix Server State and Client State

The biggest mistake in state management:

```jsx
// ❌ WRONG — using Redux to store API data:
dispatch(setUsers(await fetchUsers())); // manual caching, no background sync
const users = useSelector(state => state.users.list); // stale data management yourself

// ✓ CORRECT — TanStack Query for server state, Redux/Zustand for UI state:
const { data: users } = useQuery({ queryKey: ["users"], queryFn: fetchUsers });
const cartCount = useSelector(state => state.cart.count); // Redux for UI state only
```

TanStack Query handles: caching, deduplication, background refetching, loading/error states, pagination, optimistic updates. Redux handles none of this by default.

---

## Complete Coverage Summary Across All Files

| File | Key Topics |
|---|---|
| **Episodes 1–13** | React basics, JSX, hooks (useState/useEffect/useContext/useMemo/useCallback/useRef), Router (basic), Redux, lazy/Suspense, class components, Tailwind, HOC, Context |
| **14** | useReducer, useLayoutEffect, useId, useTransition, useDeferredValue, forwardRef, useImperativeHandle, React.memo, Error Boundaries, Portals, Render Props, Compound Components |
| **15** | React 18 (batching, createRoot), React 19 (use, useActionState, useOptimistic, Server Components), Testing (RTL+Jest), TypeScript, TanStack Query, Zustand, Synthetic Events, Virtualization |
| **16** | Router advanced (useNavigate, useSearchParams, Protected Routes, Loaders), Custom hooks (useFetch, useLocalStorage, useDebounce), Anti-patterns (9), Next.js, React Hook Form, key deep dive, dangerouslySetInnerHTML, Accessibility |
| **17 (this file)** | Stale closures (3 fixes), useEffect gotchas (infinite loops, async, race conditions), CSS Modules, React.Children + cloneElement, Context performance (split/memo/dispatch pattern), React.memo custom comparator, React Compiler, "How would you…" scenarios, State management decision tree |
