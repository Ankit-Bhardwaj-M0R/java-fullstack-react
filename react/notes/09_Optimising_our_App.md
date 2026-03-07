# Episode 09 – Optimising our App
### React Interview Prep + Coding Questions

---

## Table of Contents

1. [Single Responsibility Principle – What It Is and Why It Matters](#1-single-responsibility-principle)
2. [Modularity – Reusable, Maintainable, Testable Code](#2-modularity)
3. [What is a Custom Hook?](#3-what-is-a-custom-hook)
4. [How to Think About a Hook – The Contract](#4-how-to-think-about-a-hook-the-contract)
5. [Building useRestaurantMenu – Your First Custom Hook](#5-building-userestaurantmenu)
6. [Before and After – How Custom Hooks Clean Up Components](#6-before-and-after)
7. [Building useOnlineStatus – Detecting Internet Connection](#7-building-useonlinestatus)
8. [Using useOnlineStatus in Components – Reusability in Action](#8-using-useonlinestatus)
9. [Should Custom Hooks Always Start with "use"?](#9-should-custom-hooks-always-start-with-use)
10. [Bundling – What Parcel Does to Your Code](#10-bundling)
11. [The Problem with One Big Bundle](#11-the-problem-with-one-big-bundle)
12. [Chunking / Code Splitting / Lazy Loading – All the Names](#12-chunking-code-splitting-lazy-loading)
13. [lazy() – How to Dynamically Import a Component](#13-lazy-how-to-dynamically-import)
14. [Suspense – Handling the Loading State During Lazy Load](#14-suspense)
15. [Putting It Together – Full Lazy Loading Example](#15-full-lazy-loading-example)
16. [Coding Questions](#16-coding-questions)

---

## 1. Single Responsibility Principle

### 🎤 Interview Question:
**"What is the Single Responsibility Principle? How does it apply to React components?"**

### 💬 Conversational Answer:

The Single Responsibility Principle (SRP) is a fundamental concept in software engineering. It says: **every function, class, or component should have exactly one job — one reason to exist.**

In React, since every component is just a JavaScript function, the same rule applies. Each component should have one clear responsibility.

Think about a `RestaurantCard` component. Its one job is to display the information of a single restaurant card on the screen. It gets some data from its parent via props, and it renders a card. That's it. It doesn't fetch data. It doesn't manage routes. It doesn't handle authentication. It just displays a card. That is single responsibility done right.

Similarly, a `Header` component's only responsibility is displaying the header. It doesn't need to know how restaurants are fetched or how the menu is structured.

Now think about what happens when you have a component that does two things at once — say, fetching data from an API **and** displaying that data. That component has two responsibilities. If you later want to change how data is fetched, you have to dig into the same file that handles display logic too. If there's a bug in the API call, you have to search inside a component that also has JSX rendering code. It gets messy fast.

**The key idea:** keep your components as light and focused as possible. When a colleague opens your component, they should instantly understand what it does just from reading a few lines.

> ⚠️ Important: SRP has no hard and fast rule. You can't look at a component and say "this is SRP compliant" with a checklist. It's about good judgment — keep things focused, keep things readable. That's the spirit of it.

**Benefits you get when you follow SRP:**
- **Reusability** — a small focused component can be used anywhere in your app
- **Maintainability** — easier to update when the code is small and focused
- **Testability** — you can write test cases for one small piece at a time; if a bug is introduced, the specific test for that piece catches it immediately

---

## 2. Modularity

### 🎤 Interview Question:
**"What is modularity in software development? Why is it important?"**

### 💬 Conversational Answer:

Modularity means breaking your code into small, independent, self-contained pieces (modules). Each piece does its own job and can work and be tested on its own.

Think of it like Lego bricks. Each brick is a module. You can test each brick independently. You can assemble them in different ways. You can replace one brick without breaking the others.

In React, modularity means:
- One component per file
- Each component has a focused responsibility
- Utility logic (like API calls) goes into separate utility functions or hooks
- Constants like URLs go into a separate constants file

**Why does it matter in practice?**

Suppose your app has a bug where restaurants are showing incorrect prices. If you have one giant component that does everything, you'd have to search through 500 lines of mixed code. But if you've broken things into modules — `RestaurantCard`, `RestaurantMenu`, `useRestaurantMenu` (a hook) — you immediately know the display logic is in `RestaurantCard` and the fetch logic is in `useRestaurantMenu`. You go directly to the right module.

Modules also make your code **reusable**. If the `RestaurantCard` component is a clean module, you can drop it anywhere in your app just by importing it. You don't have to copy-paste logic because it's bundled with other unrelated things.

---

## 3. What is a Custom Hook?

### 🎤 Interview Question:
**"What is a custom hook in React? Why would you create one?"**

### 💬 Conversational Answer:

You already know hooks that React gives you — `useState`, `useEffect`, `useParams` (from React Router DOM). These are just normal JavaScript functions with a specific naming convention and behavior.

A **custom hook** is the same idea — it's a normal JavaScript function that you write yourself. The difference is you build it to serve a specific purpose in your app, and you name it starting with `use` so React (and your teammates) recognize it as a hook.

Why create one? To abstract logic out of your components.

Here's the core insight: your component should ideally only be concerned about **displaying** things. If it's also responsible for **fetching** things, it has two jobs. You can pull that fetch logic out into a custom hook, and now your component just calls the hook and gets the data — it doesn't care *how* the data was fetched.

**Custom hooks are like utility functions — they are helpers.** Just like `useParams` hides the complexity of "how do I read the URL parameter?" behind one simple function call, your custom hook hides whatever complexity you want.

You can publish custom hooks as npm packages for the whole world to use. Just like `React Router DOM` exports hooks like `useParams` and `useRouteError`, you can build and share your own hooks — like `useLocalStorage`, `useSessionStorage`, or `useDarkMode`.

---

## 4. How to Think About a Hook – The Contract

### 🎤 Interview Question:
**"When creating a custom hook, how should you think about it? How do you decide what to build?"**

### 💬 Conversational Answer:

The most common question beginners ask is: "I want to create a custom hook, but I don't know how to start."

The answer: **define the contract first.**

A contract means two things:
1. What is the **input** to this hook? (what does it need to receive?)
2. What is the **output** of this hook? (what does it return?)

Once you know the contract, writing the hook becomes much easier. The contract is like a specification — you know what goes in and what comes out. You just need to write the code in the middle.

**Example — useRestaurantMenu:**
- Input: `resId` (the restaurant's ID from the URL)
- Output: `resInfo` (the full restaurant data fetched from the API)

The hook takes a restaurant ID, fetches the data, and returns it. Done. That's the contract.

**Example — useOnlineStatus:**
- Input: nothing (you don't need any specific info from the caller)
- Output: `onlineStatus` (a boolean — `true` if online, `false` if offline)

The hook checks the browser's internet connection and returns a boolean. No input needed.

Once you have the contract clear in your mind, writing the implementation is just filling in the gap between input and output.

---

## 5. Building useRestaurantMenu – Your First Custom Hook

### 🎤 Interview Question:
**"How do you create a custom hook to fetch restaurant menu data? Show the implementation."**

### 💬 Conversational Answer:

Here's the situation: in the `RestaurantMenu` component, we have two responsibilities mixed together:
1. Fetching the restaurant data from the API
2. Displaying it on the page

We want to pull responsibility #1 into a custom hook. Let's build `useRestaurantMenu`.

**Where to create it:** Inside your `utils/` folder, in a file named exactly after the hook: `useRestaurantMenu.js`. This follows the convention: one hook per file, named after the hook.

**The hook:**

```javascript
// utils/useRestaurantMenu.js
import { useState, useEffect } from "react";
import { MENU_API_URL } from "./constants";

const useRestaurantMenu = (resId) => {
  // State variable to hold the restaurant data
  const [resInfo, setResInfo] = useState(null);

  // Fetch data once when the hook is first used
  useEffect(() => {
    fetchData();
  }, []); // empty array: run only once

  const fetchData = async () => {
    const data = await fetch(MENU_API_URL + resId);
    const json = await data.json();
    setResInfo(json.data); // store the result in state
  };

  return resInfo; // give the data back to whoever called this hook
};

export default useRestaurantMenu;
```

**Key points about this code:**
- It's just a normal JavaScript function — there's nothing magical about it
- It uses `useState` and `useEffect` exactly the same way you would in a component
- The hook maintains its own state (`resInfo`) — hooks can have their own state
- It accepts `resId` as input and returns `resInfo` as output — exactly the contract we defined
- It's exported as a default export, just like any other function/component

---

## 6. Before and After – How Custom Hooks Clean Up Components

### 🎤 Interview Question:
**"How does extracting logic into a custom hook improve a component?"**

### 💬 Conversational Answer:

Let's look at the `RestaurantMenu` component before and after creating the custom hook.

**BEFORE — doing everything in the component:**

```javascript
// RestaurantMenu.js — BEFORE (two responsibilities mixed)
import { useState, useEffect } from "react";
import { useParams } from "react-router-dom";
import Shimmer from "./Shimmer";
import { MENU_API_URL } from "../utils/constants";

const RestaurantMenu = () => {
  const { resId } = useParams();
  const [resInfo, setResInfo] = useState(null); // managing state here

  useEffect(() => {           // fetching data here
    fetchData();
  }, []);

  const fetchData = async () => {   // API logic here
    const data = await fetch(MENU_API_URL + resId);
    const json = await data.json();
    setResInfo(json.data);
  };

  if (resInfo === null) return <Shimmer />;

  const { name, cuisines } = resInfo?.cards[0]?.card?.card?.info;

  return (
    <div>
      <h1>{name}</h1>
      <h3>{cuisines.join(", ")}</h3>
    </div>
  );
};
```

This component fetches data AND displays it. Two jobs.

---

**AFTER — using the custom hook:**

```javascript
// RestaurantMenu.js — AFTER (single responsibility: display only)
import { useParams } from "react-router-dom";
import Shimmer from "./Shimmer";
import useRestaurantMenu from "../utils/useRestaurantMenu";

const RestaurantMenu = () => {
  const { resId } = useParams();

  // One line to get all the restaurant data — no idea how it happens
  const resInfo = useRestaurantMenu(resId);

  if (resInfo === null) return <Shimmer />;

  const { name, cuisines } = resInfo?.cards[0]?.card?.card?.info;

  return (
    <div>
      <h1>{name}</h1>
      <h3>{cuisines.join(", ")}</h3>
    </div>
  );
};
```

`RestaurantMenu` now has a single responsibility: **display the restaurant data**. It doesn't know or care how the data is fetched. It just gets `resInfo` and renders it.

**What changed:**
- The component doesn't manage its own state for API data
- No `useEffect` inside the component
- No `fetch()` call inside the component
- The component is shorter, cleaner, and easier to read

**Difference between junior and senior developers:** Both can make the app work. The senior developer knows how to write it in a modular, maintainable, testable way. This is what makes the difference.

---

## 7. Building useOnlineStatus – Detecting Internet Connection

### 🎤 Interview Question:
**"How do you build a custom hook to detect whether the user is online or offline?"**

### 💬 Conversational Answer:

This is a really cool hook to build, and it showcases how powerful custom hooks are. You've probably seen this feature before — Facebook Messenger shows a green dot when you're online and a red dot when you're offline. Google Chrome shows the Dino game when you lose internet. All of this is built using the same concept we're about to implement.

**The contract:**
- Input: nothing (the browser already knows its own connection status)
- Output: `onlineStatus` — a boolean (`true` = online, `false` = offline)

**How does the browser tell us if the connection changed?** The browser's `window` object gives us two event listeners:
- `"online"` — fires when internet comes back
- `"offline"` — fires when internet is lost

We add these listeners once (using `useEffect` with an empty array) and let them update our state whenever the connection changes.

```javascript
// utils/useOnlineStatus.js
import { useState, useEffect } from "react";

const useOnlineStatus = () => {
  // Start as true (assume user is online by default)
  const [onlineStatus, setOnlineStatus] = useState(true);

  useEffect(() => {
    // Add the listener once on mount
    window.addEventListener("offline", () => {
      setOnlineStatus(false); // internet went away
    });

    window.addEventListener("online", () => {
      setOnlineStatus(true); // internet came back
    });
  }, []); // empty array = run once

  return onlineStatus; // return the boolean
};

export default useOnlineStatus;
```

**How it works step by step:**
1. Hook starts, sets `onlineStatus` to `true`
2. `useEffect` runs once after first render, registers two event listeners on the window
3. Those listeners sit quietly in the background watching the connection
4. If the internet drops → `"offline"` event fires → `setOnlineStatus(false)` → component re-renders showing offline message
5. If internet comes back → `"online"` event fires → `setOnlineStatus(true)` → component re-renders showing normal content

> 💡 **How to test this in the browser:** Go to the Network tab in DevTools, find the throttling dropdown (usually set to "No throttling"), and change it to "Offline". Your app will behave exactly as if the real internet is disconnected — great for testing without actually turning off your wifi.

---

## 8. Using useOnlineStatus in Components – Reusability in Action

### 🎤 Interview Question:
**"How do you use the useOnlineStatus hook in a component? Can it be used in multiple components?"**

### 💬 Conversational Answer:

Yes — and this is the whole point of custom hooks: **build once, use anywhere**.

**Using it in the Body component (show offline message):**

```javascript
// Body.js
import useOnlineStatus from "../utils/useOnlineStatus";

const Body = () => {
  const onlineStatus = useOnlineStatus(); // just one line

  // If offline, show a message instead of restaurant cards
  if (onlineStatus === false) {
    return (
      <h1>Looks like you are offline. Please check your internet connection.</h1>
    );
  }

  // Otherwise, show the normal content
  return (
    <div className="rest-container">
      {/* restaurant cards here */}
    </div>
  );
};
```

**Using it in the Header component (show a green/red dot):**

```javascript
// Header.js
import { Link } from "react-router-dom";
import useOnlineStatus from "../utils/useOnlineStatus";

const Header = () => {
  const onlineStatus = useOnlineStatus(); // same hook, different component

  return (
    <div className="header">
      <nav>
        <ul>
          <li><Link to="/">Home</Link></li>
          <li><Link to="/about">About Us</Link></li>
          <li>
            {/* Green check if online, red dot if offline */}
            {onlineStatus ? "🟢" : "🔴"}
          </li>
        </ul>
      </nav>
    </div>
  );
};
```

You used the same `useOnlineStatus` hook in two completely different components. The logic lives in one place (`useOnlineStatus.js`). If you ever need to change how online status is detected, you change it in one file and every component that uses the hook gets the update automatically.

This is the power of custom hooks — they are like utility functions you can share across your entire application.

---

## 9. Should Custom Hooks Always Start with "use"?

### 🎤 Interview Question:
**"Is it mandatory to prefix a custom hook with 'use'? What happens if you don't?"**

### 💬 Conversational Answer:

Technically, it's **not mandatory** — your code will still work if you name your hook `getOnlineStatus` instead of `useOnlineStatus`. React won't throw an error just because of the name.

However, there are strong reasons why you should always use the `use` prefix:

**Reason 1: Linting**

Most professional React projects set up a **linter** (ESLint with React hooks rules). A linter is a tool that automatically checks your code for style and convention violations. If you don't prefix your hook with `use`, the linter will throw an error or warning, treating your function as a regular function and potentially missing rules about where it can be called.

**Reason 2: Readability**

When a developer reads your code and sees `useOnlineStatus()`, they immediately know:
- This is a React hook (not just a regular function)
- It likely manages its own state or lifecycle
- It follows React's rules about hooks

If it were named `getOnlineStatus()`, a reader might assume it's a plain utility function that can be called anywhere. But hooks can only be called at the top level of components or other hooks. The `use` prefix is a signal about behavior and placement.

**Reason 3: React's own recommendation**

The React documentation itself recommends the `use` prefix for custom hooks. Always follow what the library recommends. They've thought about it more than we have.

**The convention applies to components too:** just like hook names start with lowercase `use`, component names should always start with a capital letter. `Header`, `Body`, `RestaurantCard` — always capital. This helps React (and developers) know the difference between a component and a plain function.

> ✅ Rule of thumb: hooks start with `use` (lowercase), components start with a capital letter. Follow these conventions always.

---

## 10. Bundling – What Parcel Does to Your Code

### 🎤 Interview Question:
**"What does a bundler like Parcel do? What is the final output it produces?"**

### 💬 Conversational Answer:

When you're building a React app, you write code in many different files. You have `app.js`, `Header.js`, `Body.js`, `RestaurantCard.js`, `constants.js`, and so on — many separate files. But the browser doesn't want to make 50 separate network requests to load 50 separate files. That would be slow and messy.

**That's the bundler's job.** Parcel takes all your separate files and combines them into one single JavaScript file. This process is called **bundling**.

The final output looks something like this in your `dist` (distribution) folder:
```
dist/
  index.js       ← all your code, combined into one file
  index.js.map   ← a source map (helps with debugging, not relevant to users)
```

In development mode, this file isn't compressed — it's readable but big (could be 2MB+). In production mode, Parcel also:
- **Minifies** the code (removes spaces, comments, shortens variable names)
- **Compresses** it (makes file size smaller)
- **Obfuscates** it (renames functions to short random names to protect code and reduce size)

The result is one small file that loads into the browser. All your app's functionality — home page, about page, contact page, restaurant menu — it's all in that one file.

---

## 11. The Problem with One Big Bundle

### 🎤 Interview Question:
**"What problem arises when all your app's code is bundled into a single JavaScript file?"**

### 💬 Conversational Answer:

For a small app with 10-20 components, one bundle is perfectly fine. No problem at all.

But imagine you're building something like MakeMyTrip — a large Indian travel booking website. It has:
- A Flights section (100+ components)
- A Hotels section (100+ components)
- A Homestays section (100+ components)
- A Trains section (100+ components)
- A Cabs section (100+ components)
- A Holiday Packages section (100+ components)

That's potentially thousands of components. If you bundle them all into one JavaScript file, that file could be **10MB, 20MB, or more**.

**The problem:** when a user first visits your website, their browser has to download that entire JS file before anything works. A 10MB file on a slow 3G connection could take 30-60 seconds to load. The user is staring at a blank screen. They leave. You lose them.

**The key insight:** most users visit only a few sections of a large app. A user who wants to book flights doesn't need to download all the Homestays code, all the Holiday Packages code, all the Trains code on their first load. That code is wasted bandwidth for them.

The term for when your bundle becomes too large is **app bloating** or **bundle size increase**. When architects and frontend leads at big companies notice this, they step in and say: "We need to split our code."

---

## 12. Chunking / Code Splitting / Lazy Loading – All the Names

### 🎤 Interview Question:
**"What is code splitting / lazy loading / chunking? Why does it have so many names?"**

### 💬 Conversational Answer:

This is one of those topics where the industry hasn't settled on one term, so everyone uses different words for the same idea. When you walk into an interview, you should know all of them because your interviewer might use any one of them.

**All these names refer to the same concept:**

| Name | What it emphasizes |
|---|---|
| **Chunking** | Breaking the app into smaller chunks (pieces) |
| **Code Splitting** | Splitting your code into multiple files instead of one |
| **Lazy Loading** | Loading code lazily — only when it's needed, not all upfront |
| **On-Demand Loading** | Load only when the user demands it (visits that page) |
| **Dynamic Loading** | Load dynamically at runtime, not at startup |
| **Dynamic Import** | The technical implementation: using JavaScript's `import()` function |
| **Dynamic Bundling** | Creating separate bundles dynamically based on usage |

**The core idea:** instead of one giant JS file for everything, you create smaller logical bundles. Each bundle has the code for a specific feature or section of your app.

Using the MakeMyTrip example:
- `main.js` — core app shell, header, footer, home page
- `flights.js` — all flights components
- `hotels.js` — all hotels components
- `homestays.js` — all homestays components

When the user loads the home page, they get `main.js` (small, fast). If they click on Flights, the browser loads `flights.js` (just flights code, downloaded on demand). If they never click on Hotels, `hotels.js` is never downloaded.

**Why is this powerful?** Each user only downloads the code they actually use. Initial load time drops dramatically. Your app feels fast even though it's large.

---

## 13. lazy() – How to Dynamically Import a Component

### 🎤 Interview Question:
**"How do you use React's lazy() function to create a separate bundle for a component?"**

### 💬 Conversational Answer:

React gives us a built-in function called `lazy` to implement lazy loading. Here's how it works:

**Before lazy loading (normal import):**

```javascript
// app.js — normal way
import Grocery from "./components/Grocery"; // ← loaded at startup, always in the bundle

const appRouter = createBrowserRouter([
  {
    path: "/grocery",
    element: <Grocery />,
  },
]);
```

With this normal import, the Grocery component's code is bundled into the main JS file. It's downloaded when the user first loads the page — even if they never visit the Grocery page.

---

**After lazy loading (dynamic import):**

```javascript
// app.js — lazy loading way
import { lazy, Suspense } from "react"; // lazy is a named export from React

// Dynamic import — Grocery code is NOT in the main bundle
const Grocery = lazy(() => import("./components/Grocery"));

const appRouter = createBrowserRouter([
  {
    path: "/grocery",
    element: (
      <Suspense fallback={<h1>Loading...</h1>}>
        <Grocery />
      </Suspense>
    ),
  },
]);
```

**What changed:**
- `import Grocery from "./components/Grocery"` → gone from the top
- `lazy(() => import("./components/Grocery"))` → this is the dynamic import

**The `import()` inside `lazy()`** is not the same `import` as the one you write at the top of the file. The one at the top is a static import (happens at compile time). The `import()` inside `lazy()` is a **function** (happens at runtime when the user actually navigates to that page). This is the key difference.

**What happens in the browser:**
- Initial page load: browser downloads `main.js` (no Grocery code in it)
- User navigates to `/grocery`: browser downloads `grocery.js` (Grocery code, now available)
- User never visits `/grocery`: `grocery.js` is never downloaded

If you check the Network tab in DevTools while doing this, you'll see a separate JS file appear only when you navigate to the Grocery page. That's the separate chunk created by Parcel.

---

## 14. Suspense – Handling the Loading State During Lazy Load

### 🎤 Interview Question:
**"What is the Suspense component? Why is it needed when using lazy loading?"**

### 💬 Conversational Answer:

When you lazy load a component, there's a brief moment where:
1. The user navigates to the Grocery page
2. The browser makes a network request to download `grocery.js`
3. That request takes 10ms, 100ms, or on a slow 3G network, even 2 seconds

In that window of time, React is trying to render `<Grocery />` but the code for it hasn't arrived yet. React panics and throws an error:

> "A component suspended while responding to a synchronous input"

This is React telling you: "I tried to render this component but the code isn't here yet. I don't know what to show."

**Suspense solves this.** `Suspense` is a component built into React. You wrap your lazy-loaded component inside `<Suspense>` and give it a `fallback` prop. The `fallback` is what React should show **while waiting for the lazy component to load**.

```javascript
import { lazy, Suspense } from "react";

const Grocery = lazy(() => import("./components/Grocery"));

// In your router or JSX:
<Suspense fallback={<h1>Loading...</h1>}>
  <Grocery />
</Suspense>
```

**What happens now:**
1. User navigates to `/grocery`
2. Grocery code starts downloading
3. While downloading (could be milliseconds or seconds depending on network): React renders `<h1>Loading...</h1>` (the fallback)
4. Once `grocery.js` is fully downloaded: React swaps out the fallback and renders the real `<Grocery />` component

You can make the fallback anything — a Shimmer UI, a spinner, a loading skeleton. In production apps, you'd typically use a shimmer UI (those grey placeholder cards) as the fallback to give a polished loading experience.

> 💡 **How to see it clearly:** In DevTools → Network tab → change throttling to "Slow 3G". Then navigate to the lazy-loaded page. You'll see your fallback show for 1-2 seconds before the real component loads.

---

## 15. Putting It Together – Full Lazy Loading Example

### 🎤 Interview Question:
**"Show a complete example of lazy loading a component with Suspense, and explain how the bundle is split."**

### 💬 Conversational Answer:

Here's the complete picture, from creating the component to lazy loading it:

**Step 1: Create the Grocery component (a separate "vertical" of the app)**

```javascript
// components/Grocery.js
const Grocery = () => {
  return (
    <div>
      <h1>Grocery Online Store</h1>
      <p>Order fresh groceries delivered to your door.</p>
      {/* imagine this has 50+ child components */}
    </div>
  );
};

export default Grocery;
```

**Step 2: Add it to the router using lazy() instead of a static import**

```javascript
// app.js
import React, { lazy, Suspense } from "react";
import { createBrowserRouter, RouterProvider } from "react-router-dom";
import AppLayout from "./components/AppLayout";
import Body from "./components/Body";
import About from "./components/About";

// ✅ Lazy loaded — Grocery code is NOT in the main bundle
const Grocery = lazy(() => import("./components/Grocery"));

const appRouter = createBrowserRouter([
  {
    path: "/",
    element: <AppLayout />,
    children: [
      { path: "/", element: <Body /> },
      { path: "/about", element: <About /> },
      {
        path: "/grocery",
        element: (
          <Suspense fallback={<h1>Loading grocery store...</h1>}>
            <Grocery />
          </Suspense>
        ),
      },
    ],
  },
]);

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(<RouterProvider router={appRouter} />);
```

**Step 3: Add a link to the header**

```javascript
// Header.js
<li><Link to="/grocery">Grocery</Link></li>
```

**What the browser downloads:**

| When | What gets downloaded |
|---|---|
| First page load | `main.js` — no Grocery code |
| User clicks Grocery link | `grocery.js` — Grocery code loads on demand |
| User never visits Grocery | `grocery.js` — never downloaded |

**Key result:** your initial page load is faster because `grocery.js` is not part of the main bundle. The Grocery code is only fetched when the user actually wants it.

**The same pattern for About page:**

```javascript
const About = lazy(() => import("./components/About"));

// In your route:
<Suspense fallback={<h1>Loading...</h1>}>
  <About />
</Suspense>
```

---

**All the names to remember for your interview:**

```
Chunking
Code Splitting
Lazy Loading
On-Demand Loading
Dynamic Loading
Dynamic Import
Dynamic Bundling
```

They all mean the same thing — breaking your app into smaller logical bundles that are loaded only when needed. When a senior developer or architect says any of these words, they're talking about this exact concept.

---

## 16. Coding Questions

---

### Q1. Create the useRestaurantMenu custom hook

```javascript
// utils/useRestaurantMenu.js
import { useState, useEffect } from "react";
import { MENU_API_URL } from "./constants";

const useRestaurantMenu = (resId) => {
  const [resInfo, setResInfo] = useState(null);

  useEffect(() => {
    fetchData();
  }, []); // fetch only once on mount

  const fetchData = async () => {
    const data = await fetch(MENU_API_URL + resId);
    const json = await data.json();
    setResInfo(json.data); // ← note: json.data, not just json
  };

  return resInfo; // return the data to whoever calls this hook
};

export default useRestaurantMenu;
```

---

### Q2. Use useRestaurantMenu inside RestaurantMenu component

```javascript
// components/RestaurantMenu.js
import { useParams } from "react-router-dom";
import Shimmer from "./Shimmer";
import useRestaurantMenu from "../utils/useRestaurantMenu";

const RestaurantMenu = () => {
  const { resId } = useParams();

  // Custom hook handles all the fetching logic
  const resInfo = useRestaurantMenu(resId);

  // Early return while data is loading
  if (resInfo === null) return <Shimmer />;

  const { name, cuisines, costForTwoMessage } =
    resInfo?.cards[0]?.card?.card?.info;

  return (
    <div className="menu">
      <h1>{name}</h1>
      <h3>{cuisines.join(", ")}</h3>
      <h3>{costForTwoMessage}</h3>
    </div>
  );
};

export default RestaurantMenu;
```

---

### Q3. Create the useOnlineStatus custom hook

```javascript
// utils/useOnlineStatus.js
import { useState, useEffect } from "react";

const useOnlineStatus = () => {
  const [onlineStatus, setOnlineStatus] = useState(true); // assume online by default

  useEffect(() => {
    // Listen for internet going offline
    window.addEventListener("offline", () => {
      setOnlineStatus(false);
    });

    // Listen for internet coming back online
    window.addEventListener("online", () => {
      setOnlineStatus(true);
    });
  }, []); // add listeners once

  return onlineStatus; // return boolean: true or false
};

export default useOnlineStatus;
```

---

### Q4. Use useOnlineStatus in Body to show offline message

```javascript
// Body.js
import useOnlineStatus from "../utils/useOnlineStatus";

const Body = () => {
  const onlineStatus = useOnlineStatus();

  // If offline, show message instead of content
  if (onlineStatus === false) {
    return (
      <h1>
        Looks like you are offline. Please check your internet connection.
      </h1>
    );
  }

  // Normal render when online
  return (
    <div className="rest-container">
      {/* restaurant cards */}
    </div>
  );
};
```

---

### Q5. Use useOnlineStatus in Header to show green/red status indicator

```javascript
// Header.js
import { Link } from "react-router-dom";
import useOnlineStatus from "../utils/useOnlineStatus";

const Header = () => {
  const onlineStatus = useOnlineStatus(); // reusing the same hook

  return (
    <div className="header">
      <nav>
        <ul>
          <li><Link to="/">Home</Link></li>
          <li><Link to="/about">About Us</Link></li>
          <li><Link to="/contact">Contact</Link></li>
          <li>
            {/* Shows green check when online, red dot when offline */}
            {onlineStatus ? "🟢" : "🔴"}
          </li>
        </ul>
      </nav>
    </div>
  );
};

export default Header;
```

---

### Q6. Lazy load the Grocery component

```javascript
// app.js

// ❌ BEFORE — normal static import (Grocery code in main bundle)
import Grocery from "./components/Grocery";

// ✅ AFTER — lazy import (Grocery gets its own separate bundle)
import { lazy, Suspense } from "react";
const Grocery = lazy(() => import("./components/Grocery"));

// In the router config:
{
  path: "/grocery",
  element: (
    <Suspense fallback={<h1>Loading...</h1>}>
      <Grocery />
    </Suspense>
  ),
}
```

---

### Q7. Lazy load multiple components (About and Grocery)

```javascript
// app.js
import React, { lazy, Suspense } from "react";
import { createBrowserRouter, RouterProvider } from "react-router-dom";
import AppLayout from "./components/AppLayout";
import Body from "./components/Body";

// Both About and Grocery are lazy loaded — each gets its own bundle
const About   = lazy(() => import("./components/About"));
const Grocery = lazy(() => import("./components/Grocery"));

const appRouter = createBrowserRouter([
  {
    path: "/",
    element: <AppLayout />,
    children: [
      {
        path: "/",
        element: <Body />,
      },
      {
        path: "/about",
        element: (
          <Suspense fallback={<h1>Loading About...</h1>}>
            <About />
          </Suspense>
        ),
      },
      {
        path: "/grocery",
        element: (
          <Suspense fallback={<h1>Loading Grocery...</h1>}>
            <Grocery />
          </Suspense>
        ),
      },
    ],
  },
]);

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(<RouterProvider router={appRouter} />);
```

Each lazy-loaded component produces its own JS file in the dist folder:
- `index.js` — main bundle (no About, no Grocery code)
- `About.js` — only loaded when user visits /about
- `Grocery.js` — only loaded when user visits /grocery

---

### Q8. Using Shimmer as the Suspense fallback

```javascript
import Shimmer from "./components/Shimmer";

// Use Shimmer as the loading placeholder instead of plain text
<Suspense fallback={<Shimmer />}>
  <Grocery />
</Suspense>
```

This gives a much more polished loading experience than `<h1>Loading...</h1>`.

---

### Q9. Showing all the alternative names in comments

```javascript
// All of these refer to the same concept:

// Chunking
// Code Splitting
// Lazy Loading          ← most commonly used term in React
// On-Demand Loading     ← loads only when demanded/needed
// Dynamic Loading       ← loaded dynamically at runtime
// Dynamic Import        ← the technical implementation (import() function)
// Dynamic Bundling      ← creating separate bundles dynamically

// Implementation in React:
const Grocery = lazy(() => import("./components/Grocery"));
//              ^^^^          ^^^^^^
//              React's       JavaScript's dynamic import function
//              lazy()        (this is NOT the same as top-level import)
```

---

### Q10. Complete example of a custom hook with useState and useEffect

```javascript
// A custom hook that counts how many times a button is clicked
// (demonstrates that hooks can have their own state and effects)

// utils/useClickCounter.js
import { useState, useEffect } from "react";

const useClickCounter = () => {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `Clicked ${count} times`; // side effect
  }, [count]); // re-run whenever count changes

  const increment = () => setCount(count + 1);

  return { count, increment }; // hooks can return objects too, not just single values
};

export default useClickCounter;

// Using the hook in a component:
import useClickCounter from "../utils/useClickCounter";

const MyButton = () => {
  const { count, increment } = useClickCounter();

  return (
    <div>
      <p>Clicked {count} times</p>
      <button onClick={increment}>Click me</button>
    </div>
  );
};
```

---

## 🗒️ Quick Revision Card

| Concept | Key Point |
|---|---|
| Single Responsibility Principle | Each function/component/class should have exactly one job |
| Benefits of SRP | Code becomes reusable, maintainable, and testable |
| SRP has no hard rule | It's about judgment — keep things as focused and light as possible |
| Modularity | Breaking code into small independent pieces; each can be tested and reused separately |
| Hook | A normal JavaScript function that starts with `use`; given to us by React or written by us |
| Custom hook | A hook you write yourself; used to abstract logic out of components |
| Why create custom hooks? | Make components focused on display; put data/side-effect logic in the hook |
| Where to put hook files | Inside `utils/` folder; one file per hook, file named same as hook |
| Hook naming convention | Always start with lowercase `use` (e.g., `useOnlineStatus`) |
| Component naming convention | Always start with capital letter (e.g., `RestaurantCard`) |
| Hook contract | Define input and output first; makes writing the hook much easier |
| useRestaurantMenu | Takes `resId`, fetches restaurant data, returns `resInfo` |
| useOnlineStatus | Takes nothing, listens to browser events, returns `true`/`false` |
| window "online"/"offline" events | Browser events that fire when internet connection changes |
| Testing offline in browser | DevTools → Network tab → change throttling to "Offline" |
| `use` prefix — mandatory? | Not technically mandatory, but recommended by React docs; linters enforce it |
| Why use `use` prefix | Signals the function is a React hook (has state/lifecycle), not a plain utility function |
| Bundler (Parcel) | Takes all your files and combines them into one JS file |
| Minification | Removing spaces, comments, shortening names to reduce file size |
| Bundle size problem | One big bundle with thousands of components is too large to load fast |
| App bloating | When bundle size grows too large due to many components |
| Code splitting / Chunking | Breaking app into smaller logical bundles to reduce initial load size |
| Lazy loading | Loading code only when it's needed, not all upfront |
| On-demand loading | Same as lazy loading — download code only when user navigates there |
| Dynamic import | `import()` function (not the top-level import) — runs at runtime |
| `lazy()` | React function — takes a callback with `import()` and creates a dynamic bundle |
| Suspense | React component — wraps lazy-loaded component; shows fallback while loading |
| Suspense `fallback` | The JSX shown while lazy component is downloading (e.g., Shimmer UI) |
| Why Suspense is needed | Without it, React panics when lazy component isn't loaded yet |
| Slow 3G test | DevTools → Network → Slow 3G → see fallback loading UI |
| Build for scale | Large apps MUST use code splitting; one bundle with 1000s of components = too slow |
| Interview tip | When asked about performance, mention: lazy loading, code splitting, chunking, dynamic import |

---

*Episode 09 – Namaste React Series | Optimising our App*
