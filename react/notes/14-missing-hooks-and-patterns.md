# React — Missing Hooks & Patterns Interview Guide

> **What the existing 13 notes covered:** useState, useEffect, useContext, useMemo, useCallback, useRef, custom hooks, Redux
>
> **What this file covers (7 missing hooks + 5 missing patterns):**
> `useReducer` · `useLayoutEffect` · `useId` · `useTransition` · `useDeferredValue` · `useImperativeHandle` · `forwardRef` · `React.memo` · Error Boundaries · Portals · Render Props · Compound Components · Key Interview Comparison Questions

---

## Table of Contents
1. [useReducer — useState's Powerful Sibling](#1-usereducer--usestate-s-powerful-sibling)
2. [useLayoutEffect — The Synchronous useEffect](#2-uselayouteffect--the-synchronous-useeffect)
3. [useId — Generating Unique IDs](#3-useid--generating-unique-ids)
4. [useTransition — Non-Urgent State Updates](#4-usetransition--non-urgent-state-updates)
5. [useDeferredValue — Debouncing Without setTimeout](#5-usedeferredvalue--debouncing-without-settimeout)
6. [forwardRef — Passing Refs to Child Components](#6-forwardref--passing-refs-to-child-components)
7. [useImperativeHandle — Customizing Exposed Ref API](#7-useimperativehandle--customizing-exposed-ref-api)
8. [React.memo — Preventing Unnecessary Re-renders](#8-reactmemo--preventing-unnecessary-re-renders)
9. [Error Boundaries — Catching Render Errors](#9-error-boundaries--catching-render-errors)
10. [Portals — Rendering Outside the Root](#10-portals--rendering-outside-the-root)
11. [Render Props Pattern](#11-render-props-pattern)
12. [Compound Components Pattern](#12-compound-components-pattern)
13. [Key Interview Comparison Questions](#13-key-interview-comparison-questions)

---

## 1. useReducer — useState's Powerful Sibling

### Interview Question
**"What is useReducer? When would you use it instead of useState?"**

### Answer (Conversational)

`useReducer` is a hook for managing state — just like `useState` — but it's designed for situations where your state logic is **complex** or the next state depends on the previous one in multiple different ways.

Think of it as bringing the Redux pattern (action → reducer → new state) **directly into a single component** without installing Redux.

The syntax is:
```jsx
const [state, dispatch] = useReducer(reducer, initialState);
```

- `state` — the current state (like `useState`)
- `dispatch` — a function to send an action (instead of a setter)
- `reducer` — a pure function `(state, action) => newState`
- `initialState` — the starting value

#### Real Example — A Counter with Multiple Actions

```jsx
// 1. Define the reducer (pure function — same pattern as Redux!)
function counterReducer(state, action) {
  switch (action.type) {
    case "INCREMENT":
      return { count: state.count + 1 };
    case "DECREMENT":
      return { count: state.count - 1 };
    case "RESET":
      return { count: 0 };
    case "INCREMENT_BY":
      return { count: state.count + action.payload };
    default:
      return state; // always return current state for unknown actions
  }
}

// 2. Use it in a component
function Counter() {
  const [state, dispatch] = useReducer(counterReducer, { count: 0 });

  return (
    <div>
      <p>Count: {state.count}</p>
      <button onClick={() => dispatch({ type: "INCREMENT" })}>+</button>
      <button onClick={() => dispatch({ type: "DECREMENT" })}>-</button>
      <button onClick={() => dispatch({ type: "RESET" })}>Reset</button>
      <button onClick={() => dispatch({ type: "INCREMENT_BY", payload: 5 })}>+5</button>
    </div>
  );
}
```

#### Real-World Example — Form State

This is where `useReducer` really shines. Instead of 5 separate `useState` calls:

```jsx
// With useState — messy for complex forms:
const [firstName, setFirstName] = useState("");
const [lastName, setLastName] = useState("");
const [email, setEmail] = useState("");
const [isLoading, setIsLoading] = useState(false);
const [error, setError] = useState(null);

// With useReducer — all in one place:
const initialState = {
  firstName: "",
  lastName: "",
  email: "",
  isLoading: false,
  error: null
};

function formReducer(state, action) {
  switch (action.type) {
    case "UPDATE_FIELD":
      return { ...state, [action.field]: action.value };
    case "SUBMIT_START":
      return { ...state, isLoading: true, error: null };
    case "SUBMIT_SUCCESS":
      return { ...state, isLoading: false };
    case "SUBMIT_ERROR":
      return { ...state, isLoading: false, error: action.error };
    default:
      return state;
  }
}

function SignupForm() {
  const [state, dispatch] = useReducer(formReducer, initialState);

  const handleChange = (e) => {
    dispatch({ type: "UPDATE_FIELD", field: e.target.name, value: e.target.value });
  };

  const handleSubmit = async (e) => {
    e.preventDefault();
    dispatch({ type: "SUBMIT_START" });
    try {
      await submitForm(state);
      dispatch({ type: "SUBMIT_SUCCESS" });
    } catch (err) {
      dispatch({ type: "SUBMIT_ERROR", error: err.message });
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <input name="firstName" value={state.firstName} onChange={handleChange} />
      <input name="email" value={state.email} onChange={handleChange} />
      {state.isLoading && <p>Submitting...</p>}
      {state.error && <p>Error: {state.error}</p>}
    </form>
  );
}
```

#### useState vs useReducer — When to Use Which?

| Situation | Use |
|---|---|
| Simple toggle (true/false) | `useState` |
| Single primitive value | `useState` |
| Multiple related state values | `useReducer` |
| Next state depends on previous | `useReducer` |
| Same state used in many places | `useReducer` |
| Complex update logic with many cases | `useReducer` |
| You want to test state logic separately | `useReducer` (reducer is a plain function) |

### Coding Questions

**Q1:** Convert this to `useReducer`:
```jsx
// useState version:
const [count, setCount] = useState(0);
const increment = () => setCount(c => c + 1);
const decrement = () => setCount(c => c - 1);

// useReducer version:
function reducer(state, action) {
  if (action.type === "INCREMENT") return state + 1;
  if (action.type === "DECREMENT") return state - 1;
  return state;
}
const [count, dispatch] = useReducer(reducer, 0);
const increment = () => dispatch({ type: "INCREMENT" });
const decrement = () => dispatch({ type: "DECREMENT" });
```

---

**Q2:** What does a reducer always return?

**Answer:** The **new state**. If no action matches, always return the current state unchanged. A reducer is a pure function — no side effects, no API calls, no mutations.

---

## 2. useLayoutEffect — The Synchronous useEffect

### Interview Question
**"What is useLayoutEffect? How is it different from useEffect?"**

### Answer (Conversational)

`useLayoutEffect` is almost identical to `useEffect` — it takes the same arguments (callback + dependency array). The **only difference is WHEN it fires**.

- `useEffect` — fires **after** the browser has painted the screen (async)
- `useLayoutEffect` — fires **after DOM mutations but BEFORE** the browser paints (sync)

Think of it this way: `useEffect` lets the user see the new render first, then runs your code. `useLayoutEffect` runs your code before the user even gets to see anything.

```
Render → DOM Update → [useLayoutEffect fires] → Browser Paint → [useEffect fires]
```

#### When Does This Actually Matter?

If your effect **reads DOM measurements** (like element size/position) and then **updates state** based on those measurements — using `useEffect` will cause a flicker: user sees the old state briefly, then the update kicks in. `useLayoutEffect` prevents this flash.

```jsx
// Problem with useEffect — tooltip position flickers:
function Tooltip({ targetRef, children }) {
  const [position, setPosition] = useState({ top: 0, left: 0 });
  const tooltipRef = useRef();

  // With useEffect — user might see tooltip at 0,0 for a frame, then it jumps
  useEffect(() => {
    const rect = targetRef.current.getBoundingClientRect();
    setPosition({ top: rect.bottom, left: rect.left });
  }, []);

  return (
    <div ref={tooltipRef} style={{ position: "absolute", top: position.top, left: position.left }}>
      {children}
    </div>
  );
}

// Fix with useLayoutEffect — position is correct from the first paint:
function Tooltip({ targetRef, children }) {
  const [position, setPosition] = useState({ top: 0, left: 0 });

  useLayoutEffect(() => {
    const rect = targetRef.current.getBoundingClientRect();
    setPosition({ top: rect.bottom, left: rect.left });
  }, []);

  // ... same JSX
}
```

#### The Warning

`useLayoutEffect` **blocks painting** — the browser can't update the screen until it finishes. If you put heavy or slow code here, your app will feel janky. Only use it for **DOM measurement and synchronous updates** that would cause visual glitches otherwise.

#### Simple Rule

- Default → `useEffect`
- Only switch to `useLayoutEffect` if you see a visual flicker/flash after a DOM measurement and state update

### Coding Questions

**Q1:** Which should you use to animate an element from its current position to a new position?

**Answer:** `useLayoutEffect` — you need to read the DOM position (getBoundingClientRect) before the paint, then set up the animation. With `useEffect`, you'd get a flash of the element at its new position before the animation starts.

---

**Q2:** What happens if you put an infinite loop inside `useLayoutEffect`?

**Answer:** It **freezes the browser immediately** — because it blocks the paint cycle. With `useEffect`, the page would at least render once before hanging. This is why you should use `useLayoutEffect` sparingly.

---

## 3. useId — Generating Unique IDs

### Interview Question
**"What is useId? When do you actually need it?"**

### Answer (Conversational)

`useId` is a hook that generates a **unique, stable ID string** that is consistent between the server (SSR) and the client. It was added in React 18.

```jsx
import { useId } from "react";

function TextField({ label }) {
  const id = useId(); // generates something like ":r0:", ":r1:", etc.

  return (
    <div>
      <label htmlFor={id}>{label}</label>
      <input id={id} type="text" />
    </div>
  );
}
```

#### Why Not Just Use Math.random() or a Counter?

Great question. Three reasons:

**1. Server-Side Rendering (SSR) Mismatch:**
If you generate IDs with `Math.random()`, the server generates one ID and the client generates a different one during hydration → React throws a hydration error (IDs don't match between server and client HTML).

`useId` generates **the same ID** on both server and client.

**2. Multiple Instances:**
If you render `<TextField label="Name" />` three times, each one needs a different ID for accessibility to work. `useId` automatically gives each instance a unique ID.

```jsx
// THREE separate instances — each gets its own ID automatically:
<TextField label="First Name" />  // id = ":r0:"
<TextField label="Last Name" />   // id = ":r1:"
<TextField label="Email" />       // id = ":r2:"
```

**3. Accessibility:**
Linking `<label>` to `<input>` via `for`/`htmlFor` requires a unique ID. Without `useId`, developers often use hardcoded strings which break when a component is reused, or `Math.random()` which breaks SSR.

#### Connecting Related Elements

```jsx
function PasswordField() {
  const id = useId();

  return (
    <>
      <label htmlFor={id}>Password</label>
      <input
        id={id}
        type="password"
        aria-describedby={`${id}-hint`} // relate to hint text
      />
      <p id={`${id}-hint`}>
        Must be at least 8 characters.
      </p>
    </>
  );
}
```

#### What useId is NOT for

- **NOT for list keys** — use your data's actual ID (`.map((item) => <div key={item.id}>)`)
- **NOT for CSS class names** — use CSS modules or Tailwind
- Only for **accessibility attributes** that link DOM elements to each other

---

## 4. useTransition — Non-Urgent State Updates

### Interview Question
**"What is useTransition? What problem does it solve in React 18?"**

### Answer (Conversational)

`useTransition` is a React 18 hook that lets you tell React: **"This state update is not urgent — don't let it block the UI."**

Without it, every state update in React is treated equally urgent. If you have a slow state update (like filtering a huge list), it blocks the whole UI — the user's typing feels laggy.

`useTransition` lets you mark slow updates as "transitions" so React can keep the UI responsive and handle them in the background.

```jsx
import { useState, useTransition } from "react";

function SearchPage() {
  const [query, setQuery] = useState("");
  const [results, setResults] = useState([]);
  const [isPending, startTransition] = useTransition();

  function handleSearch(e) {
    const value = e.target.value;
    setQuery(value); // URGENT — update input immediately

    startTransition(() => {
      // NON-URGENT — React can delay this if the user is still typing
      setResults(filterHugeList(value)); // expensive operation
    });
  }

  return (
    <div>
      <input value={query} onChange={handleSearch} placeholder="Search..." />
      {isPending && <p>Loading results...</p>}
      <ResultsList results={results} />
    </div>
  );
}
```

#### What isPending Gives You

`isPending` is `true` while the transition is running. Use it to show a loading indicator or dim the old results — so the user knows something is happening without the UI freezing.

#### Tabs Example (Classic Use Case)

```jsx
function TabContainer() {
  const [activeTab, setActiveTab] = useState("home");
  const [isPending, startTransition] = useTransition();

  function selectTab(tab) {
    startTransition(() => {
      setActiveTab(tab); // expensive tab content loads as a transition
    });
  }

  return (
    <>
      <TabButtons onSelect={selectTab} />
      <div style={{ opacity: isPending ? 0.5 : 1 }}>
        {/* Slow component — dim it while transitioning instead of freezing */}
        <TabContent tab={activeTab} />
      </div>
    </>
  );
}
```

#### useTransition vs Debounce

| | `useTransition` | `setTimeout` debounce |
|---|---|---|
| Delay | Smart — React decides | Fixed (e.g., 300ms) |
| Result | Smooth, interruptible | Fixed delay always |
| Interrupts on new input | Yes | Resets timer |
| Best for | Expensive renders | API call rate limiting |

### Coding Questions

**Q1:** What are the two values returned by `useTransition`?

**Answer:** `[isPending, startTransition]` — `isPending` is a boolean (true while transition runs), `startTransition` is a function to wrap non-urgent state updates.

---

**Q2:** Can you use async/await inside `startTransition`?

**Answer:** No — `startTransition` callback must be synchronous. For async transitions (like data fetching), React 19 introduces `useActionState`, but in React 18, you handle the async part outside and only wrap the state update inside `startTransition`.

---

## 5. useDeferredValue — Debouncing Without setTimeout

### Interview Question
**"What is useDeferredValue? How is it different from useTransition?"**

### Answer (Conversational)

`useDeferredValue` lets you defer re-rendering a part of the UI. You give it a value, and it gives back a **"deferred" copy** of that value — one that lags slightly behind during busy renders.

Think of it as React saying: "Use the old value for now while you're busy — update to the new one when you have a free moment."

```jsx
import { useState, useDeferredValue } from "react";

function SearchPage() {
  const [query, setQuery] = useState("");
  const deferredQuery = useDeferredValue(query); // lags behind query during busy renders

  return (
    <div>
      {/* Input updates immediately — never feels laggy */}
      <input
        value={query}
        onChange={(e) => setQuery(e.target.value)}
      />
      {/* Results use the deferred value — re-renders later when React is free */}
      <SearchResults query={deferredQuery} />
    </div>
  );
}
```

#### How It Works Internally

1. When `query` changes, React renders the input with the new value immediately
2. In the background, React starts re-rendering `SearchResults` with the new `deferredQuery`
3. If `query` changes again before that render finishes, React **throws away** the background render and starts fresh with the newest value
4. The user always sees a responsive input, even if results are slow

#### Showing Stale Content is Loading

```jsx
function SearchPage() {
  const [query, setQuery] = useState("");
  const deferredQuery = useDeferredValue(query);
  const isStale = query !== deferredQuery; // true while results are catching up

  return (
    <div>
      <input value={query} onChange={(e) => setQuery(e.target.value)} />
      <div style={{ opacity: isStale ? 0.5 : 1 }}>
        {/* Dimmed when stale — indicating results are updating */}
        <SearchResults query={deferredQuery} />
      </div>
    </div>
  );
}
```

#### useDeferredValue vs useTransition

| | `useDeferredValue` | `useTransition` |
|---|---|---|
| What you control | A **value** | A **state update** |
| Where you use it | Where you receive the value (child/consumer) | Where the state update happens (the event handler) |
| Use when | You don't control the state update (e.g., it's in a parent) | You control the state setter directly |
| Common use case | Wrapping a prop/value for expensive child render | Wrapping `setState` in an event handler |

---

## 6. forwardRef — Passing Refs to Child Components

### Interview Question
**"What is forwardRef in React? Why do you need it?"**

### Answer (Conversational)

Normally, `ref` is special — you can't pass it to a child component as a prop. If you do, React silently ignores it. `forwardRef` is the escape hatch that lets a parent **pass a `ref` into a child component** so the parent can directly access the child's DOM node.

#### The Problem without forwardRef

```jsx
function MyInput(props) {
  return <input {...props} />;
}

function Parent() {
  const inputRef = useRef(null);

  // This does NOT work — ref is ignored on custom components!
  return <MyInput ref={inputRef} />;
  // inputRef.current is null — can't call inputRef.current.focus()
}
```

#### The Fix with forwardRef

```jsx
import { forwardRef } from "react";

// Wrap the component with forwardRef — receives (props, ref) as arguments
const MyInput = forwardRef(function MyInput(props, ref) {
  return <input ref={ref} {...props} />; // attach the forwarded ref to the DOM element
});

function Parent() {
  const inputRef = useRef(null);

  function handleClick() {
    inputRef.current.focus(); // Now this works!
  }

  return (
    <>
      <MyInput ref={inputRef} placeholder="Type here" />
      <button onClick={handleClick}>Focus the input</button>
    </>
  );
}
```

#### Real-World Use Cases

1. **Design system components** — Button, Input, Modal components that library consumers might need to programmatically focus or scroll to
2. **Animation libraries** — need direct DOM access to animate
3. **Form libraries** — react-hook-form uses refs to register inputs
4. **Tooltip/Popover positioning** — need to measure an element's DOM position

```jsx
// A reusable Input component in a design system:
const Input = forwardRef(function Input({ label, ...props }, ref) {
  const id = useId();
  return (
    <div>
      <label htmlFor={id}>{label}</label>
      <input id={id} ref={ref} {...props} />
    </div>
  );
});

// Consumer can focus it programmatically:
const ref = useRef();
<Input ref={ref} label="Email" />
ref.current.focus();
```

### Coding Questions

**Q1:** Why can't you just pass `ref` as a regular prop named `ref`?

**Answer:** `ref` (like `key`) is a **reserved prop name** in React — it's not passed to the component's props object. You'd have to rename it (e.g., `inputRef`), which breaks the standard API. `forwardRef` is the official way to handle this.

---

## 7. useImperativeHandle — Customizing Exposed Ref API

### Interview Question
**"What is useImperativeHandle? When would you use it?"**

### Answer (Conversational)

`useImperativeHandle` is used **alongside `forwardRef`** to customize what the parent sees when it uses the `ref`. Instead of exposing the raw DOM node, you expose a **custom API** — only the methods you want to make available.

Without it, a parent using a `forwardRef` component gets the entire DOM node — which can expose too much. With `useImperativeHandle`, you control exactly what the parent can do.

```jsx
import { forwardRef, useRef, useImperativeHandle } from "react";

const FancyInput = forwardRef(function FancyInput(props, ref) {
  const inputRef = useRef(null); // actual ref to the DOM input

  // Define what the parent can do with the ref:
  useImperativeHandle(ref, () => ({
    focus() {
      inputRef.current.focus();
    },
    clear() {
      inputRef.current.value = "";
    },
    // Parent cannot access inputRef.current directly — only these two methods!
  }));

  return <input ref={inputRef} {...props} />;
});

// Parent usage:
function Parent() {
  const ref = useRef();

  return (
    <>
      <FancyInput ref={ref} />
      <button onClick={() => ref.current.focus()}>Focus</button>
      <button onClick={() => ref.current.clear()}>Clear</button>
      {/* ref.current.value won't work — not exposed! */}
    </>
  );
}
```

#### Why Limit the API?

- **Encapsulation** — internal implementation can change without breaking consumers
- **Preventing misuse** — parent can't do `ref.current.style.color = "red"` if you don't expose it
- **Component contracts** — defines a clean API surface

#### forwardRef vs useImperativeHandle

| | `forwardRef` | `useImperativeHandle` |
|---|---|---|
| Purpose | Pass a ref through to a child | Control what that ref exposes |
| Used together? | Yes — useImperativeHandle always uses forwardRef | Yes |
| Exposes | The raw DOM node | A custom object you define |

---

## 8. React.memo — Preventing Unnecessary Re-renders

### Interview Question
**"What is React.memo? How is it different from useMemo?"**

### Answer (Conversational)

`React.memo` is a **Higher Order Component (HOC)** that wraps a component and tells React: "Only re-render this component if its props actually changed. Skip the re-render if props are the same."

By default in React, when a parent re-renders, **all its children re-render too** — even if their props didn't change. This is usually fine, but for expensive components, it's wasteful.

```jsx
// WITHOUT React.memo — re-renders every time Parent renders:
function ExpensiveChild({ data }) {
  console.log("ExpensiveChild rendered"); // logs on EVERY parent render
  return <div>{/* heavy computation with data */}</div>;
}

// WITH React.memo — only re-renders if 'data' prop changes:
const ExpensiveChild = React.memo(function ExpensiveChild({ data }) {
  console.log("ExpensiveChild rendered"); // only when data changes
  return <div>{/* heavy computation with data */}</div>;
});
```

#### A Complete Example

```jsx
function Parent() {
  const [count, setCount] = useState(0);
  const [name, setName] = useState("Alice");

  return (
    <div>
      <button onClick={() => setCount(c => c + 1)}>Count: {count}</button>
      {/* ChildA re-renders on every Parent render — count changes */}
      <ChildA count={count} />
      {/* ChildB only re-renders when 'name' changes — not when count changes */}
      <ChildB name={name} />
    </div>
  );
}

const ChildB = React.memo(function ChildB({ name }) {
  console.log("ChildB rendered");
  return <p>Name: {name}</p>;
});
```

#### The Problem: Inline Functions Break React.memo

```jsx
// This BREAKS memo — new function reference on every render!
function Parent() {
  const handleClick = () => console.log("clicked"); // NEW function every render

  return <ChildB onClick={handleClick} />; // memo sees a different prop every time
}

// Fix: use useCallback to stabilize the function:
function Parent() {
  const handleClick = useCallback(() => console.log("clicked"), []); // SAME reference

  return <ChildB onClick={handleClick} />; // memo works correctly now
}
```

This is why **React.memo** and **useCallback** are often used together.

#### React.memo vs useMemo

| | `React.memo` | `useMemo` |
|---|---|---|
| What it wraps | An entire **component** | A **value / computation** |
| Returns | A new component | A cached value |
| Skips | Re-rendering the component | Re-computing the value |
| Usage | `React.memo(Component)` | `useMemo(() => compute(), [deps])` |

#### When to Use React.memo

- The component renders often with the same props
- The component is computationally expensive to render
- A parent re-renders frequently but the child's props rarely change

**Don't memoize everything** — the comparison itself has a cost. Only memo components where you've identified an actual performance problem.

---

## 9. Error Boundaries — Catching Render Errors

### Interview Question
**"What is an Error Boundary in React? Why do hooks not work inside them?"**

### Answer (Conversational)

An **Error Boundary** is a class component that catches JavaScript errors anywhere in its child component tree during rendering, in lifecycle methods, and in constructors. Without error boundaries, a single render error crashes the entire React app. With one, only the subtree with the error is replaced by a fallback UI.

**Important:** Error Boundaries must be **class components** — there's no hook equivalent yet (React team is working on it). This is one of the few remaining reasons to write class components.

```jsx
import React from "react";

class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false, error: null };
  }

  // Fired when a child throws — update state to show fallback UI
  static getDerivedStateFromError(error) {
    return { hasError: true, error };
  }

  // Fired after getDerivedStateFromError — for logging
  componentDidCatch(error, errorInfo) {
    console.error("Error caught:", error, errorInfo);
    // logToSentry(error, errorInfo); // real apps log to monitoring services
  }

  render() {
    if (this.state.hasError) {
      // Render fallback UI
      return (
        <div>
          <h2>Something went wrong.</h2>
          <button onClick={() => this.setState({ hasError: false })}>
            Try again
          </button>
        </div>
      );
    }
    return this.props.children; // render children normally
  }
}
```

#### Using an Error Boundary

Wrap any part of your component tree:

```jsx
function App() {
  return (
    <ErrorBoundary>
      <Header />
      <ErrorBoundary>
        {/* If RestaurantList crashes, only this section shows fallback */}
        <RestaurantList />
      </ErrorBoundary>
      <Footer />
    </ErrorBoundary>
  );
}
```

#### What Error Boundaries DON'T Catch

- Errors in **event handlers** (use try/catch inside them)
- Errors in **asynchronous code** (setTimeout, fetch) — use try/catch
- Errors in the **Error Boundary itself**
- Server-side rendering errors

```jsx
// Event handler errors — use try/catch, NOT error boundaries:
function MyButton() {
  function handleClick() {
    try {
      doSomethingRisky();
    } catch (error) {
      // handle it here
    }
  }
  return <button onClick={handleClick}>Click me</button>;
}
```

#### react-error-boundary Library

Because writing class components is tedious, the popular `react-error-boundary` library gives you a functional API:

```jsx
import { ErrorBoundary } from "react-error-boundary";

function App() {
  return (
    <ErrorBoundary
      fallback={<p>Something went wrong!</p>}
      onError={(error, info) => logToSentry(error, info)}
    >
      <MyComponent />
    </ErrorBoundary>
  );
}
```

### Coding Questions

**Q1:** A component crashes during render. Without an error boundary, what happens?

**Answer:** The entire React component tree **unmounts** — the user sees a blank white page. With an error boundary wrapping that component, only that subtree shows the fallback UI and the rest of the app remains functional.

---

**Q2:** Can you catch errors from `useEffect` with an error boundary?

**Answer:** No. Error boundaries only catch errors during **rendering** (JSX evaluation), constructors, and lifecycle methods. Errors thrown inside `useEffect` callbacks are not caught — use try/catch inside the effect.

---

## 10. Portals — Rendering Outside the Root

### Interview Question
**"What is a React Portal? When would you use one?"**

### Answer (Conversational)

A **Portal** lets you render a component's output **into a different DOM node** than its parent component. Normally, a component renders inside its parent's DOM subtree. With a portal, you can escape that tree and render anywhere — like directly into `document.body`.

```jsx
import { createPortal } from "react-dom";

function Modal({ isOpen, onClose, children }) {
  if (!isOpen) return null;

  // This JSX renders into document.body, NOT inside the parent div!
  return createPortal(
    <div className="modal-overlay" onClick={onClose}>
      <div className="modal-content" onClick={(e) => e.stopPropagation()}>
        {children}
        <button onClick={onClose}>Close</button>
      </div>
    </div>,
    document.body // ← target DOM node outside React root
  );
}

function App() {
  const [open, setOpen] = useState(false);

  return (
    <div style={{ overflow: "hidden" }}> {/* overflow:hidden can't trap the modal! */}
      <button onClick={() => setOpen(true)}>Open Modal</button>
      <Modal isOpen={open} onClose={() => setOpen(false)}>
        <h2>I'm in a portal!</h2>
        <p>Rendered in document.body, not inside the div above.</p>
      </Modal>
    </div>
  );
}
```

#### Why Portals Exist — The CSS Stacking Problem

When you have a modal, tooltip, or dropdown inside a component with `overflow: hidden`, `z-index`, or `position: relative`, the CSS of parent elements can **clip or hide** your modal. Since the portal renders directly in `document.body`, it escapes all those CSS constraints.

#### Key Insight: Events Still Bubble Through React Tree

Even though the portal renders in `document.body`, **React events still bubble up through the React component tree** (not the DOM tree). So if you click inside a portal, that event bubbles to the portal's parent component in React — which is exactly what you'd expect.

#### Common Use Cases

1. **Modals / Dialogs** — escape overflow:hidden parents
2. **Tooltips** — need to overflow any container
3. **Dropdown menus** — same reason
4. **Toast notifications** — render globally at the top level
5. **Lightboxes** — full-screen overlays

---

## 11. Render Props Pattern

### Interview Question
**"What is the Render Props pattern? Why has it become less common?"**

### Answer (Conversational)

The **Render Props** pattern is a technique for sharing logic between components by passing a **function as a prop** — and calling that function to render JSX. The function receives the shared data and returns JSX.

Think of it as: "I'll manage the logic and state, you tell me how to render it."

```jsx
// A component that manages mouse position logic:
function MouseTracker({ render }) {
  const [position, setPosition] = useState({ x: 0, y: 0 });

  function handleMouseMove(e) {
    setPosition({ x: e.clientX, y: e.clientY });
  }

  return (
    <div style={{ height: "100vh" }} onMouseMove={handleMouseMove}>
      {render(position)} {/* Call the render prop with the data */}
    </div>
  );
}

// Usage — you control what gets rendered:
function App() {
  return (
    <MouseTracker
      render={({ x, y }) => (
        <p>Mouse is at: {x}, {y}</p>
      )}
    />
  );
}

// Reuse with completely different rendering:
<MouseTracker
  render={({ x, y }) => (
    <div style={{ left: x, top: y, position: "absolute" }}>🎯</div>
  )}
/>
```

#### Children as a Function (Variation)

Sometimes the render prop is the `children` prop itself:

```jsx
function MouseTracker({ children }) {
  const [position, setPosition] = useState({ x: 0, y: 0 });
  // ...
  return <div onMouseMove={...}>{children(position)}</div>;
}

// Usage looks cleaner:
<MouseTracker>
  {({ x, y }) => <p>Mouse: {x}, {y}</p>}
</MouseTracker>
```

#### Why Render Props Are Less Common Now

Before hooks, render props and HOCs were the only ways to share stateful logic between components. With hooks, you can extract the same logic into a custom hook — which is cleaner:

```jsx
// Custom hook version — much simpler:
function useMousePosition() {
  const [position, setPosition] = useState({ x: 0, y: 0 });
  useEffect(() => {
    const handleMove = (e) => setPosition({ x: e.clientX, y: e.clientY });
    window.addEventListener("mousemove", handleMove);
    return () => window.removeEventListener("mousemove", handleMove);
  }, []);
  return position;
}

function App() {
  const { x, y } = useMousePosition();
  return <p>Mouse: {x}, {y}</p>;
}
```

You'll still see render props in older codebases and some libraries (like `react-final-form`, `Downshift`), so understanding the pattern is important even if you write custom hooks for new code.

---

## 12. Compound Components Pattern

### Interview Question
**"What is the Compound Components pattern in React? Give a real example."**

### Answer (Conversational)

The **Compound Components** pattern is when multiple components work together to form a single cohesive UI, sharing state implicitly through Context instead of through explicit prop passing.

The best mental model: think of how HTML's `<select>` and `<option>` work together — `<option>` doesn't need you to manually pass in which option is selected; it gets that information implicitly from the `<select>` parent.

#### Real Example — A Custom Tabs Component

```jsx
import { createContext, useContext, useState } from "react";

// 1. Create a context for sharing state between compound parts:
const TabsContext = createContext(null);

// 2. The parent component — manages state and provides it via context:
function Tabs({ children, defaultTab }) {
  const [activeTab, setActiveTab] = useState(defaultTab);

  return (
    <TabsContext.Provider value={{ activeTab, setActiveTab }}>
      <div className="tabs">{children}</div>
    </TabsContext.Provider>
  );
}

// 3. Sub-components — consume context implicitly:
function TabList({ children }) {
  return <div className="tab-list">{children}</div>;
}

function Tab({ id, children }) {
  const { activeTab, setActiveTab } = useContext(TabsContext);
  const isActive = activeTab === id;

  return (
    <button
      className={isActive ? "tab active" : "tab"}
      onClick={() => setActiveTab(id)}
    >
      {children}
    </button>
  );
}

function TabPanel({ id, children }) {
  const { activeTab } = useContext(TabsContext);
  if (activeTab !== id) return null;
  return <div className="tab-panel">{children}</div>;
}

// Attach sub-components as properties (the "dot notation" API):
Tabs.List = TabList;
Tabs.Tab = Tab;
Tabs.Panel = TabPanel;
```

#### Usage — Clean and Expressive

```jsx
function App() {
  return (
    <Tabs defaultTab="profile">
      <Tabs.List>
        <Tabs.Tab id="profile">Profile</Tabs.Tab>
        <Tabs.Tab id="settings">Settings</Tabs.Tab>
        <Tabs.Tab id="billing">Billing</Tabs.Tab>
      </Tabs.List>

      <Tabs.Panel id="profile">
        <ProfileContent />
      </Tabs.Panel>
      <Tabs.Panel id="settings">
        <SettingsContent />
      </Tabs.Panel>
      <Tabs.Panel id="billing">
        <BillingContent />
      </Tabs.Panel>
    </Tabs>
  );
}
```

#### Why This is Better Than Prop Drilling

Without the pattern, you'd have to pass `activeTab`, `onTabChange`, `tabs` array as props everywhere. With compound components, the structure itself communicates the relationship — and the state is shared invisibly through context.

**Real libraries using this pattern:** Radix UI, Headless UI, Reach UI, React Aria — all use compound components for their Tabs, Dialog, Accordion, etc.

---

## 13. Key Interview Comparison Questions

### "How many React hooks are there and which ones haven't we covered?"

The official React hooks as of React 18:

| Hook | Covered in Notes? | Topic |
|---|---|---|
| `useState` | ✅ Episode 5 | State management |
| `useEffect` | ✅ Episode 6, 8 | Side effects |
| `useContext` | ✅ Episode 11 | Context consumption |
| `useMemo` | ✅ Episode 13 | Memoize computed values |
| `useCallback` | ✅ Episode 13 | Memoize functions |
| `useRef` | ✅ Episode 13 | Persistent mutable ref |
| `useReducer` | ✅ This file, Section 1 | Complex state logic |
| `useLayoutEffect` | ✅ This file, Section 2 | Sync DOM measurement |
| `useId` | ✅ This file, Section 3 | Unique accessibility IDs |
| `useTransition` | ✅ This file, Section 4 | Non-urgent state updates |
| `useDeferredValue` | ✅ This file, Section 5 | Deferred re-renders |
| `useImperativeHandle` | ✅ This file, Section 7 | Custom ref API |
| `useDebugValue` | ⬇ See below | Dev tools label for custom hooks |
| `useSyncExternalStore` | ⬇ See below | Subscribe to external stores |

---

### useDebugValue — Quick Answer

`useDebugValue` is a hook only for **custom hooks** — it adds a label in React DevTools so you can see what your custom hook's current value is at a glance.

```jsx
function useOnlineStatus() {
  const [isOnline, setIsOnline] = useState(true);
  // Shows "Online" or "Offline" in React DevTools next to this hook:
  useDebugValue(isOnline ? "Online" : "Offline");
  // ... rest of hook
  return isOnline;
}
```

No impact on production behavior — purely a developer experience tool.

---

### useSyncExternalStore — Quick Answer

`useSyncExternalStore` is for **library authors** who need to subscribe to external data sources (browser APIs, third-party state managers) in a way that's safe with React's concurrent rendering. Most app developers will never use this directly — it's what Redux uses internally to connect to the React tree safely.

```jsx
// Reading browser online status the "correct concurrent-safe" way:
function useOnlineStatus() {
  return useSyncExternalStore(
    (callback) => {
      window.addEventListener("online", callback);
      window.addEventListener("offline", callback);
      return () => {
        window.removeEventListener("online", callback);
        window.removeEventListener("offline", callback);
      };
    },
    () => navigator.onLine,  // get current value
    () => true               // server snapshot
  );
}
```

---

### "Explain the complete re-render picture — when does React re-render?"

A component re-renders when:
1. Its **own state changes** (via `useState` or `useReducer`)
2. Its **parent re-renders** (even if props didn't change — unless wrapped in `React.memo`)
3. A **context value it consumes changes** (via `useContext`)
4. `forceUpdate()` is called (class components only)

**Ways to prevent unnecessary re-renders:**
- `React.memo` — skip re-render if props unchanged
- `useMemo` — skip re-computing a value if deps unchanged
- `useCallback` — skip creating new function reference if deps unchanged
- `useTransition` / `useDeferredValue` — defer low-priority updates

---

### "What's the difference between useMemo, useCallback, and React.memo?"

```
useMemo      → memoizes a COMPUTED VALUE
               const sorted = useMemo(() => sort(list), [list])

useCallback  → memoizes a FUNCTION DEFINITION
               const handleClick = useCallback(() => doSomething(), [dep])

React.memo   → memoizes a COMPONENT (skips re-render if props unchanged)
               const MyComp = React.memo(function MyComp({ data }) { ... })
```

They work together: `React.memo` only helps if props are stable — so you need `useCallback` for function props and `useMemo` for object/array props passed to a memoized child.

---

### "Context vs Redux — when to use which?"

| | Context API | Redux Toolkit |
|---|---|---|
| Setup | Built-in, no install | npm install required |
| Boilerplate | Low | More (slice, store, provider) |
| Dev tools | Limited | Excellent (time-travel) |
| Performance | Re-renders all consumers on any change | Selectors prevent unnecessary re-renders |
| Best for | Theme, auth, locale (rarely-changing global data) | Cart, complex app state, frequent updates |
| Scale | Small to medium apps | Large, complex apps |

---

### "Class lifecycle methods vs hooks — the mapping"

| Class Method | Hook Equivalent |
|---|---|
| `componentDidMount` | `useEffect(() => {...}, [])` — empty deps |
| `componentDidUpdate` | `useEffect(() => {...}, [dep])` — with deps |
| `componentWillUnmount` | `useEffect(() => { return () => cleanup() }, [])` |
| `shouldComponentUpdate` | `React.memo` |
| `getDerivedStateFromError` | Error Boundary (class only — no hook yet) |
| `componentDidCatch` | Error Boundary (class only — no hook yet) |
| `getSnapshotBeforeUpdate` | `useLayoutEffect` (partial equivalent) |
| `setState` callback | `useEffect` watching that state value |

---

## Summary — The Complete Hooks Map

```
STATE
  useState       → simple state
  useReducer     → complex state / multiple actions

EFFECTS
  useEffect      → side effects after paint (async)
  useLayoutEffect → side effects before paint (sync DOM ops)

REFS
  useRef         → mutable value that doesn't cause re-renders
  forwardRef     → pass ref from parent into a child component
  useImperativeHandle → customize what the forwarded ref exposes

CONTEXT
  useContext     → read a context value

PERFORMANCE
  useMemo        → memoize computed values
  useCallback    → memoize function definitions
  React.memo     → memoize entire component (skip re-render)
  useTransition  → mark state updates as non-urgent
  useDeferredValue → defer re-rendering part of the UI

ACCESSIBILITY
  useId          → stable unique IDs for aria attributes

ADVANCED
  useSyncExternalStore → subscribe to external stores (library authors)
  useDebugValue  → label custom hooks in DevTools
```
