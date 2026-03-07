# Episode 05 – Let's Get Hooked
### React Interview Prep + Coding Questions

---

## Table of Contents

1. [Why React? What Problem Does It Solve?](#1-why-react-what-problem-does-it-solve)
2. [Folder Structure – src, components, utils](#2-folder-structure)
3. [.js vs .jsx – Which Extension to Use?](#3-js-vs-jsx)
4. [Separate Files for Separate Components – Best Practice](#4-separate-files-for-components)
5. [Default Export vs Named Export](#5-default-export-vs-named-export)
6. [Hard-Coded Data Belongs in utils/constants – Not Components](#6-hard-coded-data-in-utils)
7. [Event Handlers in React – onClick and Others](#7-event-handlers-in-react)
8. [The Problem: Data Changes but UI Doesn't](#8-the-problem-data-changes-but-ui-doesnt)
9. [What is a React Hook?](#9-what-is-a-react-hook)
10. [useState – Creating a State Variable](#10-usestate--creating-a-state-variable)
11. [How to Update a State Variable (the setter function)](#11-how-to-update-a-state-variable)
12. [Why a Setter Function? Why Not Just Reassign?](#12-why-a-setter-function)
13. [useState Returns an Array – Array Destructuring Explained](#13-usestate-returns-an-array)
14. [Virtual DOM – What It Is and Why It Matters](#14-virtual-dom)
15. [Diff Algorithm and Reconciliation](#15-diff-algorithm-and-reconciliation)
16. [React Fiber – The Core Algorithm](#16-react-fiber)
17. [The Full Picture – How React Updates the UI](#17-the-full-picture)
18. [Coding Questions](#18-coding-questions)

---

## 1. Why React? What Problem Does It Solve?

### 🎤 Interview Question:
**"Can't we build everything with plain HTML, CSS, and JavaScript? Why do we need React?"**

### 💬 Conversational Answer:

Yes — technically, you can do everything in React using plain HTML, CSS, and JavaScript. React does not add new capabilities that were previously impossible. This is an important myth to clear up.

So then why React? Two reasons:

**Reason 1: Developer experience.**
React makes it much easier and faster to write complex UIs. You write less code to achieve the same result. Instead of manually querying the DOM, tracking what changed, and updating elements one by one — React handles all of that for you.

**Reason 2: Efficient UI updates (the real power).**
The real superpower of React is keeping your **UI layer** and your **data layer** in sync — automatically, efficiently, and fast. When your data changes, your UI should update. In plain JavaScript, you have to write all that update logic yourself. In React, it happens automatically.

Think about filtering a list of 100 restaurants. In vanilla JavaScript, you'd manually find the elements on the page and remove/add them. In React, you just update a variable — and React figures out what changed and updates only what's needed.

This is the core problem React solves: **efficient DOM manipulation**.

---

## 2. Folder Structure

### 🎤 Interview Question:
**"How should you structure files and folders in a React project?"**

### 💬 Conversational Answer:

React itself has no opinion on folder structure — the official documentation says React doesn't care how you organize your files. But the industry follows conventions that make large projects manageable.

**Standard structure used in this series:**

```
project-root/
├── index.html
├── package.json
├── src/
│   ├── app.js              ← root-level component (AppLayout)
│   ├── components/
│   │   ├── Header.js       ← Header component
│   │   ├── Body.js         ← Body component
│   │   └── RestaurantCard.js ← RestaurantCard component
│   └── utils/
│       ├── constants.js    ← CDN URLs, logo URLs, other constants
│       └── mockData.js     ← hardcoded data / mock API data
```

**Key rules:**

1. **`src/` folder** — all your source code lives here. Industry standard across almost every project.

2. **`components/` folder** — all your React components go here. Each component gets its own file.

3. **`utils/` folder** — utility files that are shared across the app. Hardcoded data, constants, helper functions.

4. **File names match component names** — if the component is `Header`, the file is `Header.js`. Capital letter for component files, lowercase for utility files (`constants.js`, `mockData.js`).

5. **Keep files small** — a good rule of thumb (used at companies like Uber): no file should exceed 100 lines. If it does, break it into smaller components.

**Two popular ways to organize:**
- **By file type**: All components in `components/`, all API files in `api/`, etc.
- **By feature**: All files related to "Restaurant" in a `restaurant/` folder (JS + CSS + tests together).

Both are valid. Pick one and be consistent. Don't overthink it early — you can always restructure later.

---

## 3. .js vs .jsx

### 🎤 Interview Question:
**"Should component files use .js or .jsx extension?"**

### 💬 Conversational Answer:

This is a commonly debated question with no single right answer. The short version: **it makes zero functional difference**.

- `.js` — plain JavaScript files. Works fine with JSX inside.
- `.jsx` — explicitly signals "this file contains JSX." Some code editors show a different icon for it.
- `.ts` / `.tsx` — same idea but for TypeScript.

The recommendation in this series (and what was used at Uber): **use `.js` for everything**. It's simpler, consistent, and React will handle JSX either way.

Similarly, when importing, you can include or omit the extension:

```javascript
import Header from "./components/Header.js";  // explicit
import Header from "./components/Header";      // no extension — React treats it as JS automatically
```

Both work. The instructor prefers no extension for cleanliness. Pick one style and stick to it across your project.

---

## 4. Separate Files for Components

### 🎤 Interview Question:
**"Why should each component have its own file? Is it mandatory?"**

### 💬 Conversational Answer:

It's not mandatory. You can write your entire React app in a single file. Parcel will still bundle it into one optimized file for the browser. It works.

But it's a terrible idea for any real project. Here's why:

- A real app has **hundreds or thousands of components**. One file would be unreadable.
- Multiple developers work on the same project. If everything is in one file, conflicts happen constantly.
- Finding a bug in `RestaurantCard` is much easier if it's in `RestaurantCard.js` — not buried in 2000 lines.

**The rule:** One component = one file. Name the file exactly after the component.

```
Header component    →   Header.js
RestaurantCard      →   RestaurantCard.js
Body component      →   Body.js
```

When another developer opens your project and sees `Header.js`, they instantly know it contains the `Header` component. That's readable, maintainable code — which is what professionals write.

---

## 5. Default Export vs Named Export

### 🎤 Interview Question:
**"What is the difference between default export and named export in JavaScript/React?"**

### 💬 Conversational Answer:

This is a core JavaScript concept that React uses heavily. There are two ways to export something from a file.

**Default Export**

Used when a file exports **one main thing** (usually one component).

```javascript
// Header.js
const Header = () => {
  return <div>Header</div>;
};

export default Header;       // ← default export

// --- OR inline ---
export default function Header() {
  return <div>Header</div>;
}
```

Importing a default export — **no curly braces, and you can use any name**:

```javascript
// app.js
import Header from "./components/Header";         // ✅ correct
import MyHeader from "./components/Header";       // ✅ also valid — name doesn't matter
```

**One file can only have ONE default export.**

---

**Named Export**

Used when a file exports **multiple things** (like multiple constants or utility functions).

```javascript
// constants.js
export const CDN_URL = "https://res.cloudinary.com/...";
export const LOGO_URL = "https://some-logo.png";
```

Importing a named export — **curly braces are required, and the name must match exactly**:

```javascript
import { CDN_URL, LOGO_URL } from "../utils/constants";
```

**A file can have as many named exports as it needs.**

---

**Summary table:**

| | Default Export | Named Export |
|---|---|---|
| How many per file? | Only 1 | As many as you want |
| Syntax (export) | `export default Name` | `export const Name = ...` |
| Syntax (import) | `import Name from "./file"` | `import { Name } from "./file"` |
| Curly braces on import? | No | Yes |
| Name must match? | No (you can rename) | Yes (must match exactly) |
| Common use | Components | Constants, utilities |

**Can you mix both in one file?**

Yes! You can have one default export AND multiple named exports in the same file. This is your homework from the episode — try it out.

---

## 6. Hard-Coded Data in utils

### 🎤 Interview Question:
**"Where should you keep hardcoded data and constant values in a React project?"**

### 💬 Conversational Answer:

**Never keep hardcoded data inside your component files.** This is an industry-wide best practice.

Why? Because:
- Constants like CDN URLs, logo URLs, or mock data might be used by many components. If they're inside one component file, you'd have to copy-paste them — leading to duplication.
- If the CDN URL changes, you'd have to update it in every component that uses it. That's a bug waiting to happen.
- Component files should only contain component logic — keeping them clean and focused.

**Where to keep it:**

Create a `utils/` folder (also called `common/` or `config/` in some projects) and put constants in a `constants.js` file and mock data in a `mockData.js` file.

```javascript
// utils/constants.js
export const CDN_URL = "https://res.cloudinary.com/swiggy/image/upload/";
export const LOGO_URL = "https://your-logo-url.png";

// utils/mockData.js
export default restaurantList;   // OR: export { restaurantList };
```

**Naming convention for constants:**

Use ALL_CAPS with underscores (called SCREAMING_SNAKE_CASE):

```javascript
const CDN_URL = "...";       // ✅ good
const logoUrl = "...";       // ⚠️ okay but not conventional for constants
const cdnurl = "...";        // ❌ bad
```

This immediately tells any developer reading the code: "this is a constant — it won't change."

---

## 7. Event Handlers in React

### 🎤 Interview Question:
**"How do you handle click events in React? What about other events?"**

### 💬 Conversational Answer:

In React, event handlers are added as props directly on JSX elements. The most common one is `onClick`.

**Adding a click handler:**

```jsx
<button onClick={() => console.log("Button clicked!")}>
  Click Me
</button>
```

The value passed to `onClick` is a **callback function** — a function that gets called when the event happens. Since it's JavaScript inside JSX, you wrap it in `{}`.

**Using a named function (cleaner for complex logic):**

```jsx
const handleClick = () => {
  console.log("Button was clicked!");
  // do more things here
};

<button onClick={handleClick}>Click Me</button>

// Note: write handleClick, NOT handleClick() ← this would call it immediately!
```

**Other common events:**

```jsx
<input onChange={(e) => console.log(e.target.value)} />   // text input change
<input onBlur={() => console.log("Input lost focus")} />   // when user clicks away
<div onMouseEnter={() => console.log("Mouse entered")} />  // mouse hover enter
<div onMouseLeave={() => console.log("Mouse left")} />     // mouse hover leave
```

In plain JavaScript you'd write `onmouseenter` (lowercase). In React, all event names are **camelCase**: `onClick`, `onChange`, `onBlur`, `onMouseEnter`, `onMouseLeave`, etc.

There is no `onHover` in React. The equivalent is `onMouseEnter` / `onMouseLeave`.

---

## 8. The Problem: Data Changes but UI Doesn't

### 🎤 Interview Question:
**"What happens when you update a regular JavaScript variable in React? Does the UI update?"**

### 💬 Conversational Answer:

No. And this is the exact problem that React's state system solves.

Let's say you have a list of restaurants and a button. When clicked, it should filter to only top-rated ones:

```javascript
// ❌ This does NOT work — UI doesn't update
let listOfRestaurants = [restaurant1, restaurant2, restaurant3];

const handleClick = () => {
  listOfRestaurants = listOfRestaurants.filter(
    (res) => res.data.avgRating > 4
  );
  console.log(listOfRestaurants); // filtered! But UI doesn't change
};
```

The data IS being filtered — you can see it in `console.log`. But the UI still shows all 3 restaurants.

**Why?**

Because React has no idea that `listOfRestaurants` changed. React is not "watching" regular JavaScript variables. It doesn't know when to re-render.

This is the exact problem that `useState` solves.

**The fundamental idea:**

```
Data changes → UI should automatically update
```

For this to happen, React needs to be notified when data changes. That's what state variables do — they give React the ability to track changes.

---

## 9. What is a React Hook?

### 🎤 Interview Question:
**"What is a React hook? Explain it simply."**

### 💬 Conversational Answer:

Just like everything else in React, hooks are simpler than they sound.

**A React hook is just a normal JavaScript function — written by Facebook developers and included inside the React library.**

When you do `npm install react`, you get all these utility functions along with it. These utility functions are called "hooks." They're called hooks because they "hook into" React's internal system.

```javascript
// This is what's happening behind the scenes in React's source code:
// (simplified example)
export const useState = (initialValue) => {
  // Facebook wrote this function with special React magic inside
  // ...
};
```

When you `import { useState } from "react"`, you're importing this utility function that Facebook wrote.

**Key points about hooks:**

- They are normal JavaScript functions — not magic.
- They are pre-built by the React team.
- They give you access to React's internal features (state, lifecycle, context, etc.).
- There are many hooks: `useState`, `useEffect`, `useContext`, `useRef`, `useMemo`, `useCallback`, and more.
- The two most important ones (that you'll use 99% of the time): **`useState`** and **`useEffect`**.

**Important rule:** Hooks must be called **inside** a functional component. You can't call them outside components or inside regular functions.

---

## 10. useState – Creating a State Variable

### 🎤 Interview Question:
**"What is useState? How do you create a state variable?"**

### 💬 Conversational Answer:

`useState` is a React hook that creates a **state variable** — a special React-tracked variable that, when updated, automatically re-renders the component.

**Step 1: Import it**

```javascript
import { useState } from "react";
// OR
import React, { useState } from "react";
```

`useState` is a **named export** from React — that's why you use curly braces.

**Step 2: Create a state variable inside your component**

```javascript
const Body = () => {
  const [listOfRestaurants, setListOfRestaurants] = useState([]);
  //          ↑                      ↑                    ↑
  //    the variable         function to update it   default value

  return (/* JSX */);
};
```

**What is this syntax?**

`useState` returns an **array** with two elements:
1. `listOfRestaurants` — the current value of the state variable
2. `setListOfRestaurants` — a function to update it

You destructure this array on the fly. This is just JavaScript array destructuring:

```javascript
// What useState returns:
const arr = useState([]);
// arr[0] = the state variable
// arr[1] = the setter function

// Array destructuring is just shorthand for:
const listOfRestaurants = arr[0];
const setListOfRestaurants = arr[1];

// Which we write as (cleaner):
const [listOfRestaurants, setListOfRestaurants] = useState([]);
```

**Passing a default value:**

Whatever you pass into `useState()` becomes the initial value:

```javascript
useState([])           // starts as empty array
useState(0)            // starts as 0
useState(null)         // starts as null
useState(restaurantList)  // starts with all 15 restaurants
```

**Naming convention:**

If your variable is `count`, the setter is `setCount`. If it's `listOfRestaurants`, the setter is `setListOfRestaurants`. The `set` prefix is the industry convention — not enforced, but always followed.

---

## 11. How to Update a State Variable

### 🎤 Interview Question:
**"How do you update a state variable in React?"**

### 💬 Conversational Answer:

You use the **setter function** — the second element returned by `useState`.

```javascript
const [listOfRestaurants, setListOfRestaurants] = useState(restaurantList);

const handleTopRated = () => {
  const filteredList = listOfRestaurants.filter(
    (res) => res.data.avgRating > 4
  );
  setListOfRestaurants(filteredList);  // ← update the state
};
```

When `setListOfRestaurants(filteredList)` is called:
1. React updates `listOfRestaurants` to `filteredList`
2. React re-renders the Body component
3. The UI automatically shows only top-rated restaurants

**You cannot update a state variable by direct assignment:**

```javascript
// ❌ WRONG — this does NOT trigger a re-render
listOfRestaurants = filteredList;

// ✅ CORRECT — this triggers re-render
setListOfRestaurants(filteredList);
```

**The golden rule:**
> Whenever a state variable updates, React re-renders the component.

This is the entire magic. The `set` function is the trigger that tells React: "Something changed — please re-render."

---

## 12. Why a Setter Function?

### 🎤 Interview Question:
**"Why can't we just directly modify the state variable? Why do we need a setter function?"**

### 💬 Conversational Answer:

Great question. Here's the real reason:

React needs to **know when to start its update process** (re-rendering, finding the diff, updating the DOM). It can't watch all variables in your JavaScript — that would be impossibly expensive.

So React made a deal: **"Use my setter function to update state, and I will watch for those calls. Whenever you call the setter, I'll know something changed and start re-rendering."**

The setter function is the **trigger** for React's internal algorithm. Without it, React has no way to know your data changed.

Think of it like this analogy:
- Regular variable: You change the text on a whiteboard. Nobody is watching, so nobody knows it changed.
- State variable setter: You press a buzzer on the whiteboard. React is listening for that buzzer. When it hears it, it looks at what changed and updates the screen.

Also — state variables are created with `const`:

```javascript
const [count, setCount] = useState(0);
```

You can't reassign a `const`. If you tried `count = 5`, JavaScript would throw a `TypeError: Assignment to constant variable`. The setter function is the only valid way to update it.

---

## 13. useState Returns an Array

### 🎤 Interview Question:
**"What does useState actually return? Explain the array destructuring syntax."**

### 💬 Conversational Answer:

`useState` returns an **array with exactly two elements**:
- Element 0: the current state value
- Element 1: the function to update that value

```javascript
const result = useState(0);
// result = [0, function]
// result[0] = 0 (the value)
// result[1] = function (the setter)
```

We use **array destructuring** to pull these out cleanly:

```javascript
// Without destructuring (works but ugly):
const result = useState(0);
const count = result[0];
const setCount = result[1];

// With array destructuring (clean, standard way):
const [count, setCount] = useState(0);
```

Array destructuring is just regular JavaScript. The position matters — first element becomes `count`, second becomes `setCount`.

```javascript
// Proof that it's just array destructuring:
const arr = [42, "hello", true];
const [num, str, bool] = arr;
// num = 42, str = "hello", bool = true
```

**Why an array and not an object?**

Arrays allow you to **name the variables whatever you want**. If it were an object with fixed keys like `{ value, setter }`, you'd have to use those exact names or use aliasing syntax. With arrays, you name them freely:

```javascript
const [count, setCount] = useState(0);
const [name, setName] = useState("");
const [restaurants, setRestaurants] = useState([]);
```

This is a deliberate design decision by the React team for flexibility.

---

## 14. Virtual DOM

### 🎤 Interview Question:
**"What is the Virtual DOM in React?"**

### 💬 Conversational Answer:

Virtual DOM is one of the most commonly asked interview questions about React. Let's break it down properly.

**What is the actual DOM?**

The actual DOM (Document Object Model) is the tree of HTML elements your browser renders. When you inspect a webpage and see `<div>`, `<h1>`, `<p>` tags — that's the actual DOM.

**What is the Virtual DOM?**

Virtual DOM is a **JavaScript object representation of the actual DOM**. It's not an actual DOM — it's a plain JavaScript object (or a tree of objects) that describes what the DOM looks like.

Remember React elements? When you write JSX, it becomes a `React.createElement()` call, which creates a JavaScript object. That object is what we call the Virtual DOM.

```javascript
// This JSX:
const heading = <h1 className="title">Hello</h1>;

// Becomes this JS object (the Virtual DOM):
{
  type: "h1",
  props: {
    className: "title",
    children: "Hello"
  }
}
```

When your component has a complex nested structure, the Virtual DOM is a big nested object that represents all of it.

**Key point:**

Virtual DOM is not a React invention. It's a general software engineering concept. React took this concept and built its update algorithm on top of it.

**Why is Virtual DOM useful?**

Comparing two JavaScript objects is fast. Comparing two HTML trees (the actual DOM) is slow. React compares Virtual DOM objects to figure out what changed — then makes minimal, targeted updates to the actual DOM.

---

## 15. Diff Algorithm and Reconciliation

### 🎤 Interview Question:
**"What is the Diff algorithm? What is reconciliation in React?"**

### 💬 Conversational Answer:

**Reconciliation** is React's process of keeping the UI in sync with the state. Every time state changes, React needs to figure out what changed and update the DOM accordingly.

**The Diff Algorithm** is the algorithm React uses to find the difference between the **old Virtual DOM** and the **new Virtual DOM**. "Diff" is short for "difference" — just like `git diff` shows you what changed in code.

**How it works:**

1. Before the state change, React has the **old Virtual DOM** (e.g., 15 restaurant cards).
2. State changes (filtered list now has 7 cards).
3. React creates a **new Virtual DOM** (7 restaurant cards).
4. The diff algorithm compares old vs new: "What's different? 8 nodes were removed."
5. React updates **only those 8 nodes** in the actual DOM — not the entire page.

```
Old Virtual DOM (15 restaurants)   →   React compares   →   New Virtual DOM (7 restaurants)
                                           ↓
                                   Diff: 8 nodes removed
                                           ↓
                            Update actual DOM — remove those 8 nodes
```

**Why is this fast?**

- Comparing two JS objects (Virtual DOMs) is much faster than comparing or traversing actual HTML DOM nodes.
- React doesn't re-render everything — only what changed.
- The actual DOM is only touched for the minimal required changes.

This is why React is known for fast UI updates. Not because of some magic, but because it's doing smart, minimal DOM manipulation.

---

## 16. React Fiber

### 🎤 Interview Question:
**"What is React Fiber? How is it different from the old reconciliation algorithm?"**

### 💬 Conversational Answer:

React Fiber is the **name of React's core algorithm** for reconciliation, introduced in **React 16**.

Before React 16, the old reconciliation algorithm had a limitation: it was synchronous and blocking. If there were many updates happening at once, the browser could freeze while React finished processing.

**React Fiber introduced:**

1. **Incremental rendering** — the ability to split rendering work into chunks and spread it over multiple frames. Instead of doing everything at once (which could block the browser), Fiber can pause work and come back to it.

2. **Ability to prioritize updates** — some updates are more urgent than others. Fiber can prioritize high-priority updates (like user input) over lower-priority ones (like data fetching animations).

3. **More efficient diffing** — better algorithm for finding what changed between Virtual DOMs.

**Simple analogy:**

Old algorithm: Imagine a chef who, once they start cooking a dish, cannot stop until it's done — even if a customer urgently needs something. The whole kitchen freezes.

React Fiber: The chef can pause cooking, handle the urgent request, and then come back to finish the dish. The kitchen runs smoothly.

**Reference for further reading:**

Search for "acdlite/react-fiber-architecture" on GitHub. This is the original spec document for React Fiber. Read it when you want to go deep.

---

## 17. The Full Picture – How React Updates the UI

### 🎤 Interview Question:
**"Walk me through exactly what happens in React when a state variable changes."**

### 💬 Conversational Answer:

This is the complete end-to-end picture. Understanding this makes you a stronger React developer than most.

```
1. You create a state variable using useState
        ↓
2. React starts "watching" that variable
        ↓
3. User interacts (clicks a button)
        ↓
4. The setter function is called (e.g., setListOfRestaurants(filteredList))
        ↓
5. React knows the state changed (because the setter was called)
        ↓
6. React starts the reconciliation process (React Fiber algorithm)
        ↓
7. React re-renders the component (calls the function again with new state)
        ↓
8. A new Virtual DOM (JS object) is created with the updated data
        ↓
9. Diff algorithm compares old Virtual DOM vs new Virtual DOM
        ↓
10. React finds the minimal difference (e.g., "8 nodes to remove")
        ↓
11. React updates only those specific nodes in the actual DOM
        ↓
12. Browser renders the updated UI — user sees the change
```

**Why state variables and not regular variables?**

Because React only starts this whole process (step 5 onward) when the **setter function** is called. If you change a regular variable, React never starts step 5 — it has no idea anything changed. The setter is the trigger.

**The one sentence answer:**

> When a state variable updates (via its setter function), React re-renders the component by running the diff algorithm between old and new Virtual DOMs, and updates only the changed parts of the actual DOM.

---

## 18. Coding Questions

---

### Q1. Create a proper folder structure (conceptual)

```
src/
├── app.js
├── components/
│   ├── Header.js
│   ├── Body.js
│   └── RestaurantCard.js
└── utils/
    ├── constants.js
    └── mockData.js
```

---

### Q2. Default export and import

```javascript
// Header.js — exporting with default export
const Header = () => {
  return <div className="header">I am Header</div>;
};

export default Header;


// app.js — importing default export (no curly braces)
import Header from "./components/Header";

const AppLayout = () => {
  return (
    <div>
      <Header />
    </div>
  );
};
```

---

### Q3. Named export and import

```javascript
// utils/constants.js — named exports
export const CDN_URL = "https://res.cloudinary.com/swiggy/image/upload/";
export const LOGO_URL = "https://some-logo-url.png";
export const API_URL = "https://api.example.com";


// components/RestaurantCard.js — named import (must use curly braces)
import { CDN_URL } from "../utils/constants";

const RestaurantCard = ({ resData }) => {
  const { cloudinaryImageId, name } = resData.data;
  return (
    <div>
      <img src={CDN_URL + cloudinaryImageId} alt={name} />
      <h3>{name}</h3>
    </div>
  );
};

export default RestaurantCard;
```

---

### Q4. Mix default and named exports in one file

```javascript
// utils/constants.js
export const CDN_URL = "https://cdn.example.com/";   // named export
export const LOGO_URL = "https://logo.example.com/"; // named export

const API_URL = "https://api.example.com";
export default API_URL;                               // default export

// Importing both:
import API_URL, { CDN_URL, LOGO_URL } from "../utils/constants";
//      ↑ default           ↑ named exports
```

---

### Q5. Add an onClick event handler to a button

```javascript
const Body = () => {
  const handleFilterClick = () => {
    console.log("Filter button clicked!");
    // filter logic will go here
  };

  return (
    <div>
      <button className="filter-btn" onClick={handleFilterClick}>
        Top Rated Restaurants
      </button>
    </div>
  );
};
```

---

### Q6. Show the problem — regular variable doesn't update UI

```javascript
// ❌ This does NOT work — UI stays the same after click
const Body = () => {
  let restaurants = [restaurant1, restaurant2, restaurant3];

  const handleClick = () => {
    restaurants = restaurants.filter((r) => r.data.avgRating > 4);
    console.log(restaurants); // data IS filtered — but UI won't update
  };

  return (
    <div>
      <button onClick={handleClick}>Filter</button>
      {restaurants.map((r) => <RestaurantCard key={r.data.id} resData={r} />)}
    </div>
  );
};
```

---

### Q7. Fix it with useState

```javascript
import { useState } from "react";
import restaurantList from "../utils/mockData";

// ✅ This WORKS — UI updates when state changes
const Body = () => {
  const [listOfRestaurants, setListOfRestaurants] = useState(restaurantList);

  const handleTopRated = () => {
    const filteredList = listOfRestaurants.filter(
      (res) => res.data.avgRating > 4
    );
    setListOfRestaurants(filteredList); // triggers re-render!
  };

  return (
    <div>
      <button onClick={handleTopRated}>Top Rated Restaurants</button>
      <div className="rest-container">
        {listOfRestaurants.map((restaurant) => (
          <RestaurantCard
            key={restaurant.data.id}
            resData={restaurant}
          />
        ))}
      </div>
    </div>
  );
};
```

---

### Q8. Demonstrate useState with a simple counter

```javascript
import { useState } from "react";

const Counter = () => {
  const [count, setCount] = useState(0);   // default value is 0

  return (
    <div>
      <h2>Count: {count}</h2>
      <button onClick={() => setCount(count + 1)}>Increase</button>
      <button onClick={() => setCount(count - 1)}>Decrease</button>
      <button onClick={() => setCount(0)}>Reset</button>
    </div>
  );
};

export default Counter;
```

Every time a button is clicked, `setCount` is called → React re-renders `Counter` → UI shows updated count.

---

### Q9. Understand array destructuring from useState

```javascript
// What useState returns:
const result = useState("Hello");
// result[0] = "Hello"  (the current value)
// result[1] = function  (the setter)

// Array destructuring syntax:
const [name, setName] = useState("Hello");
// name = "Hello"
// setName = function to update name

// Proof: same thing without useState, just array destructuring
const arr = [10, 20, 30];
const [a, b, c] = arr;
// a = 10, b = 20, c = 30

// You can name them anything:
const [x, setX] = useState(0);
const [myList, updateMyList] = useState([]);   // naming is flexible
```

---

### Q10. useMouseEnter instead of hover (bonus)

```javascript
const HoverCard = () => {
  const [isHovered, setIsHovered] = useState(false);

  return (
    <div
      onMouseEnter={() => setIsHovered(true)}
      onMouseLeave={() => setIsHovered(false)}
      style={{ backgroundColor: isHovered ? "lightblue" : "white", padding: "20px" }}
    >
      {isHovered ? "You are hovering!" : "Hover over me"}
    </div>
  );
};
```

---

## 🗒️ Quick Revision Card

| Concept | Key Point |
|---|---|
| Why React? | Efficient DOM updates; keeps UI in sync with data automatically |
| src/ folder | Industry standard location for all source code |
| components/ folder | One file per component; file name = component name |
| utils/ folder | Hardcoded data, constants, helper functions |
| .js vs .jsx | No functional difference; use .js for simplicity |
| Default export | One per file; import without curly braces; name can differ |
| Named export | Many per file; import with curly braces; name must match |
| Constants naming | ALL_CAPS with underscores (CDN_URL, LOGO_URL) |
| Hard-coded data | Never in component files — keep in utils/constants.js or mockData.js |
| onClick | `<button onClick={() => doSomething()}>` |
| onMouseEnter | There is no `onHover` in React — use `onMouseEnter` |
| Regular variable | Changing it does NOT update the UI |
| State variable | Created with `useState`; changing it DOES update the UI |
| useState syntax | `const [value, setValue] = useState(defaultValue)` |
| useState returns | An array: [currentValue, setterFunction] |
| Setter function | The only valid way to update state; triggers re-render |
| The golden rule | **Whenever a state variable updates, React re-renders the component** |
| Virtual DOM | JS object representation of the actual DOM |
| Diff algorithm | Finds difference between old and new Virtual DOM |
| Reconciliation | React's process of updating UI when state changes |
| React Fiber | New reconciliation algorithm introduced in React 16; enables incremental rendering |
| Array destructuring | `const [a, b] = useState(...)` — standard JS, not React magic |

---

*Episode 05 – Namaste React Series | Let's Get Hooked*
