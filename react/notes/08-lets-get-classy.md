# Episode 8: Let's Get Classy — Class-Based Components

---

## Table of Contents
1. [Why Learn Class Components?](#1-why-learn-class-components)
2. [Class Component Syntax](#2-class-component-syntax)
3. [The constructor and super(props)](#3-the-constructor-and-superprops)
4. [The render() Method](#4-the-render-method)
5. [State in Class Components — this.state and this.setState](#5-state-in-class-components--thisstate-and-thissetstate)
6. [Lifecycle Methods Overview](#6-lifecycle-methods-overview)
7. [componentDidMount — The API Call Spot](#7-componentdidmount--the-api-call-spot)
8. [Parent-Child Lifecycle Order](#8-parent-child-lifecycle-order)
9. [componentDidUpdate — Watching for Changes](#9-componentdidupdate--watching-for-changes)
10. [componentWillUnmount — Cleanup](#10-componentwillunmount--cleanup)
11. [The setInterval SPA Memory Leak Problem](#11-the-setinterval-spa-memory-leak-problem)
12. [useEffect Equivalent of Class Lifecycle Methods](#12-useeffect-equivalent-of-class-lifecycle-methods)
13. [Why the Dependency Array is an Array](#13-why-the-dependency-array-is-an-array)
14. [Batching in the Render Phase](#14-batching-in-the-render-phase)
15. [Summary Interview Q&A](#15-summary-interview-qa)
16. [Coding Exercises](#16-coding-exercises)
17. [Quick Quiz](#17-quick-quiz)

---

## 1. Why Learn Class Components?

You might be thinking — "We already know functional components and hooks, why bother with class components?"

Great question. Here's why:

1. **Legacy code**: Most large companies have years of code written with class components. You WILL encounter class components in the real world. If you can't read them, you can't fix bugs in them.
2. **Interviews**: Companies ask about class component lifecycle methods ALL the time.
3. **Understanding hooks better**: Once you understand what `componentDidMount` does, you understand exactly why `useEffect` with an empty `[]` works the way it does.

Class components were the only way to have state and lifecycle in React before React 16.8 (hooks were introduced in 2018). Everything before that is class components.

---

## 2. Class Component Syntax

A functional component is just a JavaScript function. A class component is a JavaScript class that **extends React.Component**.

```jsx
// Functional Component (what we've been doing)
const MyComponent = () => {
  return <h1>Hello</h1>;
};

// Class Component (old way)
import React from "react";

class MyComponent extends React.Component {
  render() {
    return <h1>Hello</h1>;
  }
}

export default MyComponent;
```

Key things to notice:
1. Uses the `class` keyword
2. Extends `React.Component` — this gives it all the React magic
3. Has a `render()` method that returns JSX
4. No arrow functions for the component itself — just a class definition

---

## 3. The constructor and super(props)

When you need state or need to use `this.props` in a class component, you define a `constructor`:

```jsx
class UserClass extends React.Component {
  constructor(props) {
    super(props);          // MANDATORY — must call this first!
    this.state = {
      userInfo: {
        name: "Dummy",
        location: "Default",
      },
    };
  }

  render() {
    const { name, location } = this.state.userInfo;
    return (
      <div>
        <h1>Name: {name}</h1>
        <h2>Location: {location}</h2>
      </div>
    );
  }
}
```

**Why `super(props)`?**

`super(props)` calls the constructor of the parent class — which is `React.Component`. This is required because:
- `React.Component` sets up all the internal React machinery
- Until you call `super(props)`, `this` is not initialized — you can't use `this.state`, `this.props`, anything
- If you try to use `this` before calling `super()`, JavaScript throws an error

It's like saying: "Hey React, do your setup first, then I'll do mine."

**Rule:** If you define a constructor in a class component, the FIRST LINE must be `super(props)`.

---

## 4. The render() Method

Every class component MUST have a `render()` method. This is what React calls to get the JSX to put on screen.

```jsx
class ProfileCard extends React.Component {
  render() {
    // You can write any JavaScript here
    const greeting = "Hello!";

    return (
      <div>
        <h1>{greeting}</h1>
        <p>This is a class component</p>
      </div>
    );
  }
}
```

**Accessing props in render:**
```jsx
class ProfileCard extends React.Component {
  render() {
    const { name, email } = this.props;  // ← use this.props in class components

    return (
      <div>
        <h1>{name}</h1>
        <p>{email}</p>
      </div>
    );
  }
}
```

In functional components you just write `props.name` or destructure in the function params. In class components, it's `this.props.name`.

---

## 5. State in Class Components — this.state and this.setState

In functional components, state is created with `useState`. In class components, state lives in `this.state` (an object) and is updated with `this.setState()`.

**Declaring state (in constructor):**
```jsx
constructor(props) {
  super(props);
  this.state = {
    count: 0,
    name: "Alice",
  };
}
```

**Accessing state (in render):**
```jsx
render() {
  return (
    <div>
      <h1>Count: {this.state.count}</h1>
      <h2>Name: {this.state.name}</h2>
    </div>
  );
}
```

**Updating state:**
```jsx
// WRONG — never mutate state directly!
this.state.count = this.state.count + 1;   // ❌

// RIGHT — use setState
this.setState({ count: this.state.count + 1 });  // ✅
```

When you call `this.setState()`:
1. React merges the new values into `this.state`
2. React triggers a re-render
3. The component re-renders with the new state values

**Note:** `setState` does a **shallow merge** — it only updates the keys you provide, leaving other keys untouched. So `this.setState({ count: 5 })` will update `count` but leave `name` as-is.

---

## 6. Lifecycle Methods Overview

Class components have **lifecycle methods** — special methods that React calls automatically at specific points in a component's life.

```
MOUNTING (component appears on screen)
  → constructor()
  → render()
  → componentDidMount()

UPDATING (state or props change)
  → render()          (called again with new state/props)
  → componentDidUpdate()

UNMOUNTING (component is removed from screen)
  → componentWillUnmount()
```

Think of it as birth, life, and death of a component:
- **Birth (Mounting):** constructor → render → componentDidMount
- **Life (Updating):** render → componentDidUpdate (repeated as many times as state changes)
- **Death (Unmounting):** componentWillUnmount

---

## 7. componentDidMount — The API Call Spot

`componentDidMount` is called **once**, immediately after the component's first render puts the component on the DOM.

```jsx
class UserProfile extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      userInfo: {},
    };
  }

  async componentDidMount() {
    // This runs after the first render
    // Perfect place for API calls!
    const data = await fetch("https://api.github.com/users/yourname");
    const json = await data.json();

    this.setState({ userInfo: json });  // triggers re-render with real data
  }

  render() {
    const { name, avatar_url, bio } = this.state.userInfo;
    return (
      <div>
        <img src={avatar_url} alt="avatar" />
        <h1>{name}</h1>
        <p>{bio}</p>
      </div>
    );
  }
}
```

**Flow:**
1. `constructor` — sets up initial state `{ userInfo: {} }`
2. `render()` — renders with empty data (shows loading or default UI)
3. `componentDidMount` — fetches data from GitHub API
4. `this.setState({ userInfo: json })` — triggers re-render
5. `render()` — renders again with the real data

This is **identical** to the pattern we use in functional components:
- `useState` = `this.state`
- `useEffect` with empty `[]` = `componentDidMount`

---

## 8. Parent-Child Lifecycle Order

This is a favorite interview question! When you have a parent and child class component, what is the exact order of lifecycle calls?

```jsx
// Parent Component
class Parent extends React.Component {
  componentDidMount() {
    console.log("Parent componentDidMount");
  }

  render() {
    console.log("Parent render");
    return (
      <div>
        <h1>Parent</h1>
        <Child />
      </div>
    );
  }
}

// Child Component
class Child extends React.Component {
  componentDidMount() {
    console.log("Child componentDidMount");
  }

  render() {
    console.log("Child render");
    return <h2>Child</h2>;
  }
}
```

**Output order:**
```
Parent render
Child render
Child componentDidMount
Parent componentDidMount
```

**Why this order?**

The render phase goes top-down:
1. Parent renders first (constructs the component tree)
2. During Parent's render, it encounters `<Child />` and renders Child

The commit phase (componentDidMount) goes bottom-up:
3. Child componentDidMount fires first (child is on DOM)
4. Parent componentDidMount fires after (parent's whole subtree is on DOM)

This makes sense because React wants to ensure ALL children are fully mounted before the parent's `componentDidMount` fires. Otherwise, parent code might try to interact with DOM nodes that don't exist yet.

**With multiple children:**
```jsx
// If Parent renders <Child1 /> and <Child2 />:
// Parent render
// Child1 render
// Child2 render
// Child1 componentDidMount
// Child2 componentDidMount
// Parent componentDidMount
```

React batches all the renders, THEN does all the mounts bottom-up.

---

## 9. componentDidUpdate — Watching for Changes

`componentDidUpdate` is called after every re-render (i.e., after every state or props update). It receives the **previous props and state** as arguments.

```jsx
componentDidUpdate(prevProps, prevState) {
  // prevProps = props from BEFORE the update
  // prevState = state from BEFORE the update
  // this.props = current props
  // this.state = current state

  // Example: make API call only when a specific prop changes
  if (this.props.userId !== prevProps.userId) {
    // userId changed — fetch new data
    this.fetchUserData(this.props.userId);
  }
}
```

**Without the if-check, you get an infinite loop:**
```jsx
componentDidUpdate() {
  this.setState({ something: "value" }); // ❌ setState triggers re-render
                                          // re-render triggers componentDidUpdate
                                          // infinite loop!
}
```

Always guard `setState` calls in `componentDidUpdate` with a condition comparing prev values to current values.

**This is the "old, painful" way** — the comparison logic scattered across the component. Hooks replaced this with the clean dependency array pattern.

---

## 10. componentWillUnmount — Cleanup

`componentWillUnmount` is called right before a component is removed from the DOM. Use it to clean up any ongoing tasks — intervals, timeouts, event listeners, subscriptions.

```jsx
componentWillUnmount() {
  // Clean up here!
  clearInterval(this.timer);
  window.removeEventListener("resize", this.handleResize);
}
```

Think of it as the component saying "I'm about to die — let me clean up after myself."

---

## 11. The setInterval SPA Memory Leak Problem

This is a critical concept that trips up many developers. Here's the problem:

Imagine you have a class component that updates a clock every second:

```jsx
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = { time: new Date().toLocaleTimeString() };
  }

  componentDidMount() {
    // Start interval when component mounts
    this.timer = setInterval(() => {
      this.setState({ time: new Date().toLocaleTimeString() });
      console.log("Interval running...");
    }, 1000);
  }

  render() {
    return <h1>Time: {this.state.time}</h1>;
  }
}
```

**The Problem in a Multi-Page SPA:**

```
User navigates to /clock page
  → Clock mounts
  → componentDidMount fires
  → Interval starts: ticking every second

User navigates away to /about page
  → Clock component is UNMOUNTED from DOM
  → But... the interval is STILL running in memory!
  → "Interval running..." still logs every second

User navigates back to /clock page
  → Clock mounts AGAIN
  → componentDidMount fires AGAIN
  → A SECOND interval starts
  → Now TWO intervals running simultaneously

User navigates away and back 5 more times
  → 7 intervals all running at once — memory leak!
```

In a multi-page app, each page navigation would destroy and recreate the HTML file, which would kill the interval. But in an SPA, the JavaScript keeps running — intervals don't die just because a component unmounts!

**The Fix — componentWillUnmount:**

```jsx
componentWillUnmount() {
  clearInterval(this.timer);  // Kill the interval when component dies
  console.log("Clock unmounted, interval cleared!");
}
```

Now the interval stops when you navigate away. When you navigate back, a fresh interval starts. No memory leak.

---

## 12. useEffect Equivalent of Class Lifecycle Methods

This is the most important conceptual bridge between class components and functional components:

| Class Component | Functional Component (hooks) |
|---|---|
| `constructor` | `useState` initialization |
| `componentDidMount` | `useEffect(() => {...}, [])` — empty dependency array |
| `componentDidUpdate` | `useEffect(() => {...}, [dep])` — with dependency |
| `componentWillUnmount` | Return function inside `useEffect` |

**componentWillUnmount = useEffect return function:**

```jsx
// Class component
componentWillUnmount() {
  clearInterval(this.timer);
}

// Functional component equivalent
useEffect(() => {
  const timer = setInterval(() => {
    setTime(new Date().toLocaleTimeString());
  }, 1000);

  // This return function = componentWillUnmount
  return () => {
    clearInterval(timer);   // Runs when component unmounts
    console.log("Cleanup called");
  };
}, []);
```

The function you return from `useEffect` is called the **cleanup function**. React calls it:
1. When the component is about to unmount
2. Before the effect runs again (if dependencies change)

---

## 13. Why the Dependency Array is an Array

Ever wondered why `useEffect` takes an array as the second argument instead of just a single value?

```jsx
useEffect(() => {
  // ... effect
}, [userId, theme, language]);  // WHY an array?
```

Because `useEffect` can watch **multiple** dependencies at once! If ANY of them change, the effect re-runs.

**The old class component way:**

```jsx
componentDidUpdate(prevProps) {
  if (prevProps.userId !== this.props.userId) {
    this.fetchUser(this.props.userId);
  }
  if (prevProps.theme !== this.props.theme) {
    this.applyTheme(this.props.theme);
  }
  if (prevProps.language !== this.props.language) {
    this.setLanguage(this.props.language);
  }
}
```

Three separate `if` blocks — messy and error-prone.

**The new hooks way:**

```jsx
useEffect(() => {
  this.fetchUser(userId);
}, [userId]);

useEffect(() => {
  applyTheme(theme);
}, [theme]);

useEffect(() => {
  setLanguage(language);
}, [language]);
```

Or even combine them:

```jsx
useEffect(() => {
  // runs when ANY of these change
  fetchAndApplySettings(userId, theme, language);
}, [userId, theme, language]);
```

The array design is elegant — it replaces multiple manual `if (prevProp !== currentProp)` checks with a declarative list of dependencies.

---

## 14. Batching in the Render Phase

React is smart about re-renders. When multiple `setState` calls happen together, React **batches** them and does only ONE re-render instead of one per `setState`.

```jsx
// In class components, React batches these:
handleClick() {
  this.setState({ count: this.state.count + 1 });
  this.setState({ name: "Bob" });
  // React does ONE re-render, not two!
}
```

This is the same optimization we saw with functional components — if you call `setCount` and `setName` together inside an event handler, React batches them into a single re-render.

**Why does this matter?**

Without batching, updating two pieces of state would cause two renders. With batching, React collects all the `setState` calls, merges them, and renders once. This is a performance optimization.

---

## 15. Summary Interview Q&A

**Q1: What is a class component and how is it different from a functional component?**

A class component is a JavaScript class that extends `React.Component` and has a mandatory `render()` method that returns JSX. Unlike functional components (plain functions), class components had their own lifecycle methods (`componentDidMount`, `componentDidUpdate`, `componentWillUnmount`) before hooks were introduced. Functional components with hooks are now preferred for new code, but class components are still widespread in older codebases.

---

**Q2: Why do you call `super(props)` in the constructor?**

`super(props)` calls the constructor of the parent class `React.Component`. This is required because JavaScript's class inheritance requires calling the parent constructor before you can use `this`. Until `super(props)` is called, `this` is uninitialized and you can't set `this.state` or access `this.props`. React uses `props` in its internal setup, so we pass `props` to `super`.

---

**Q3: What is the order of lifecycle methods when a parent and child component mount?**

Parent constructor → Parent render → Child constructor → Child render → **Child componentDidMount → Parent componentDidMount**. The render phase goes top-down (parent then child). The commit phase (componentDidMount) goes bottom-up (child first, then parent). This ensures that when the parent's `componentDidMount` runs, all children are already fully mounted.

---

**Q4: What is componentWillUnmount used for and why is it important in SPAs?**

`componentWillUnmount` is called right before a component is removed from the DOM. It's used for cleanup — clearing intervals, canceling API calls, removing event listeners. In SPAs, JavaScript keeps running between page navigations. A `setInterval` started in `componentDidMount` will keep running even after the component unmounts. If you navigate back to the page, a second interval starts, then a third — creating a memory leak. `componentWillUnmount` clears the interval, preventing this.

---

**Q5: What is the useEffect equivalent of componentDidMount, componentDidUpdate, and componentWillUnmount?**

- `componentDidMount` → `useEffect(() => {...}, [])` (empty array runs once after mount)
- `componentDidUpdate` → `useEffect(() => {...}, [dep])` (runs when dep changes)
- `componentWillUnmount` → the return function inside `useEffect`

```jsx
useEffect(() => {
  // componentDidMount logic here
  const timer = setInterval(doSomething, 1000);

  return () => {
    // componentWillUnmount logic here
    clearInterval(timer);
  };
}, []);
```

---

**Q6: Why is the dependency array in useEffect an array (not a single value)?**

Because you might want to watch multiple values. The array replaces the manual `if (prevProp !== currentProp)` checks that were needed in `componentDidUpdate`. With an array, React automatically re-runs the effect when ANY listed dependency changes. Having it as an array allows watching one, two, or many values in a clean declarative way.

---

## 16. Coding Exercises

### Exercise 1: Convert Functional to Class Component
Convert this functional component to a class component:

```jsx
// Functional
const Greeting = ({ name }) => {
  return <h1>Hello, {name}!</h1>;
};

// Your task — write as a class component:
class Greeting extends React.Component {
  render() {
    const { name } = this.props;
    return <h1>Hello, {name}!</h1>;
  }
}
```

---

### Exercise 2: Add State to a Class Component
Create a class component with a counter that increments on button click:

```jsx
class Counter extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0,
    };
  }

  render() {
    return (
      <div>
        <h1>Count: {this.state.count}</h1>
        <button
          onClick={() => this.setState({ count: this.state.count + 1 })}
        >
          Increment
        </button>
      </div>
    );
  }
}
```

---

### Exercise 3: API Call in componentDidMount
Fetch user data from an API when the component mounts:

```jsx
class UserProfile extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      user: null,
      loading: true,
    };
  }

  async componentDidMount() {
    const response = await fetch(`https://api.github.com/users/${this.props.username}`);
    const user = await response.json();
    this.setState({ user, loading: false });
  }

  render() {
    if (this.state.loading) return <p>Loading...</p>;

    const { name, bio, avatar_url } = this.state.user;
    return (
      <div>
        <img src={avatar_url} alt={name} width="100" />
        <h1>{name}</h1>
        <p>{bio}</p>
      </div>
    );
  }
}
```

---

### Exercise 4: Fix the Memory Leak
This code has a memory leak. Fix it:

```jsx
// BROKEN — memory leak!
class Timer extends React.Component {
  componentDidMount() {
    setInterval(() => {
      console.log("Tick...");
    }, 1000);
  }

  render() {
    return <h1>Timer Running</h1>;
  }
}

// FIXED — add componentWillUnmount
class Timer extends React.Component {
  componentDidMount() {
    this.interval = setInterval(() => {
      console.log("Tick...");
    }, 1000);
  }

  componentWillUnmount() {
    clearInterval(this.interval);
    console.log("Timer cleaned up!");
  }

  render() {
    return <h1>Timer Running</h1>;
  }
}
```

---

### Exercise 5: Lifecycle Order Prediction
What is the console.log output order for this code?

```jsx
class Parent extends React.Component {
  constructor(props) {
    super(props);
    console.log("Parent constructor");
  }
  componentDidMount() { console.log("Parent componentDidMount"); }
  render() {
    console.log("Parent render");
    return <Child />;
  }
}

class Child extends React.Component {
  constructor(props) {
    super(props);
    console.log("Child constructor");
  }
  componentDidMount() { console.log("Child componentDidMount"); }
  render() {
    console.log("Child render");
    return <h1>Child</h1>;
  }
}
```

**Answer:**
```
Parent constructor
Parent render
Child constructor
Child render
Child componentDidMount
Parent componentDidMount
```

---

### Exercise 6: useEffect Equivalent
Rewrite this class component as a functional component using hooks:

```jsx
// Class component
class DataFetcher extends React.Component {
  constructor(props) {
    super(props);
    this.state = { data: null };
  }

  async componentDidMount() {
    const response = await fetch(`https://api.example.com/data/${this.props.id}`);
    const data = await response.json();
    this.setState({ data });
  }

  componentWillUnmount() {
    console.log("DataFetcher unmounting, cleanup if needed");
  }

  render() {
    if (!this.state.data) return <p>Loading...</p>;
    return <div>{this.state.data.title}</div>;
  }
}

// Functional equivalent:
const DataFetcher = ({ id }) => {
  const [data, setData] = useState(null);

  useEffect(() => {
    const fetchData = async () => {
      const response = await fetch(`https://api.example.com/data/${id}`);
      const result = await response.json();
      setData(result);
    };

    fetchData();

    return () => {
      console.log("DataFetcher unmounting, cleanup if needed");
    };
  }, []);

  if (!data) return <p>Loading...</p>;
  return <div>{data.title}</div>;
};
```

---

## 17. Quick Quiz

1. What does `extends React.Component` give a class?
2. What is the FIRST thing you must call in a class component's constructor?
3. Name the three main lifecycle methods in order.
4. Where is the best place to make API calls in a class component?
5. What is `this.state` vs `this.setState()`?
6. Why does `componentDidMount` fire after `componentDidUpdate` in parent-child?
7. What is the danger of calling `setState` in `componentDidUpdate` without a condition?
8. What does the return function in `useEffect` correspond to in class components?
9. Why is `clearInterval` needed in SPAs but not in traditional multi-page apps?
10. Why is the dependency array in useEffect an array?

**Answers:**
1. All the React Component methods and properties (`render`, lifecycle methods, `setState`, etc.)
2. `super(props)`
3. `componentDidMount` → `componentDidUpdate` → `componentWillUnmount`
4. `componentDidMount` — it runs once after the first render, perfect for API calls
5. `this.state` holds the current state object; `this.setState()` is the method to update state and trigger re-render
6. `componentDidMount` fires bottom-up — children first, then parent. `componentDidUpdate` fires after re-renders, not on initial mount.
7. Infinite loop — `setState` triggers re-render, which triggers `componentDidUpdate` again, which calls `setState` again, etc.
8. `componentWillUnmount`
9. Traditional multi-page apps reload the entire JavaScript on each navigation, killing all running intervals. In SPAs, JavaScript persists across navigation, so intervals must be explicitly cleared.
10. Because you can watch multiple dependencies at once — it replaces multiple manual `if (prevProp !== currentProp)` checks from `componentDidUpdate`.
