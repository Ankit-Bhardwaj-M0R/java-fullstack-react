# Episode 06 – Exploring the World
### React Interview Prep + Coding Questions

---

## Table of Contents

1. [Monolith vs Microservice Architecture](#1-monolith-vs-microservice-architecture)
2. [How Microservices Talk to Each Other](#2-how-microservices-talk-to-each-other)
3. [Our React App as a UI Microservice](#3-our-react-app-as-a-ui-microservice)
4. [Two Approaches to Fetching Data](#4-two-approaches-to-fetching-data)
5. [Why React Uses the Second Approach](#5-why-react-uses-the-second-approach)
6. [useEffect Hook – What It Is and How It Works](#6-useeffect-hook)
7. [useEffect Callback Fires AFTER Render – Proof](#7-useeffect-fires-after-render)
8. [Making an API Call with fetch()](#8-making-an-api-call-with-fetch)
9. [CORS Error – What It Is and How to Bypass It](#9-cors-error)
10. [Updating State with Live API Data](#10-updating-state-with-live-api-data)
11. [Optional Chaining in JavaScript](#11-optional-chaining)
12. [Shimmer UI – What It Is and Why It's Better Than a Spinner](#12-shimmer-ui)
13. [Building a Shimmer Component](#13-building-a-shimmer-component)
14. [Conditional Rendering](#14-conditional-rendering)
15. [Login/Logout Toggle – useState Deep Dive](#15-loginlogout-toggle)
16. [Why Does the Whole Component Re-Render When State Changes?](#16-why-whole-component-re-renders)
17. [How Can a const Variable Change? – The Magic Explained](#17-how-can-const-variable-change)
18. [Controlled Input and onChange Handler](#18-controlled-input-and-onchange-handler)
19. [Search Functionality with Filter](#19-search-functionality-with-filter)
20. [The Filter Bug and How to Fix It with Two State Variables](#20-the-filter-bug-and-the-fix)
21. [Life Lesson: Code Slow](#21-life-lesson-code-slow)
22. [Coding Questions](#22-coding-questions)

---

## 1. Monolith vs Microservice Architecture

### 🎤 Interview Question:
**"What is the difference between monolith and microservice architecture? Which one does React relate to?"**

### 💬 Conversational Answer:

**Monolith Architecture:**

In the early days of web development, everything lived in a single big project. One codebase had everything — the API (backend logic), the UI (frontend), the authentication, the database connectivity, the SMS notifications — all of it.

The instructor's example: When working at LendingKart, there was one giant Java project. APIs were written in it, JSP pages for UI were inside it, authentication was inside it, the DB connection was inside it. If you had to change the color of a button, you had to:
1. Build the entire project
2. Compile the entire project
3. Deploy the entire project

That's a big, bulky, slow process. This is **monolith architecture** — one single project does everything.

---

**Microservice Architecture:**

The modern world is moving toward microservices. Instead of one big project, you break everything into **separate, independent services** that each do one specific job:

- **UI Service** — handles the frontend (React, Vue, Angular)
- **Backend Service** — handles the API and business logic
- **Auth Service** — handles login, tokens, authentication
- **Database Service** — manages data
- **SMS Service** — sends text messages
- **Email Notification Service** — handles emails

Each service runs independently, has its own codebase, its own deployment cycle, and communicates with the others when needed.

The instructor's example from Uber: Uber follows microservice architecture. Separate UI project, separate backend project, separate notification project, separate database service — all talking to each other via API calls.

This is called **Separation of Concerns** and follows the **Single Responsibility Principle** — each service does one job and does it well.

---

**Key advantage of microservices:**

Different services can use **different tech stacks**:
- UI in React
- Backend in Java
- Database in Python
- SMS service in Golang

This is why it's silly to ask "what language does Uber use?" — they use multiple languages for different services.

---

**Where does our React app fit in?**

Our Namaste Food React project is the **UI microservice**. It's one piece of the larger application. It will talk to the backend service (which provides restaurant data) via API calls.

---

## 2. How Microservices Talk to Each Other

### 🎤 Interview Question:
**"How do microservices communicate with each other?"**

### 💬 Conversational Answer:

Each microservice is deployed on its own **port** and communicates via **URLs (HTTP API calls)**.

For example:
```
Port 1234  →  UI Service (React app)
Port 1000  →  Backend/API Service
Port 3000  →  SMS Service
```

These ports can be mapped to domain names:
```
namastedev.com/          → UI Service (port 1234)
namastedev.com/api       → Backend Service (port 1000)
namastedev.com/sms       → SMS Service (port 3000)
```

When the UI wants data from the backend, it makes an HTTP call to `/api`. When the backend needs to send an SMS, it calls `/sms`. Services are connected purely through network calls — they don't share code or memory.

---

## 3. Our React App as a UI Microservice

### 🎤 Interview Question:
**"Where does the React project fit in a microservice architecture?"**

### 💬 Conversational Answer:

Our React app is the **UI microservice**. It runs on port 1234 (or wherever Parcel starts it). Its job is to:
1. Render the user interface
2. Make API calls to the backend microservice to get data
3. Display that data to the user

In this episode, we will see how our React UI microservice "explores the world" — meaning it makes calls to an external API (Swiggy's backend) to fetch live restaurant data and render it on screen.

---

## 4. Two Approaches to Fetching Data

### 🎤 Interview Question:
**"What are the two approaches to fetching API data when a page loads? Which does React use and why?"**

### 💬 Conversational Answer:

**Approach 1 (Page Load → Wait → Render):**

1. Page loads
2. Make the API call immediately
3. Wait for the data to come back (say, 500ms)
4. Only then render the UI

Result: The user sees a completely blank page for 500ms, and then suddenly everything pops up at once. **This is bad UX** — the page feels frozen and the sudden appearance of content is jarring.

---

**Approach 2 (Page Load → Render → API Call → Re-Render):**

1. Page loads
2. Immediately render whatever you can (skeleton/shimmer)
3. In the background, make the API call
4. When data returns, re-render with real data

Result: The user sees something immediately (the skeleton/shimmer UI) and then it smoothly fills in with real data. **This is much better UX** — the user doesn't feel lag, and the transition feels natural.

---

**Which does React use?**

React always uses **Approach 2**. Two reasons:

1. **Better user experience** — users see something immediately instead of a blank screen.
2. **React is optimized for this** — React's render cycles are extremely fast. Two renders is not a performance problem; the reconciliation algorithm handles it efficiently.

You might wonder: isn't rendering twice wasteful? Not in React. React is specifically built to handle frequent re-renders efficiently. The second render costs very little compared to the UX gain.

---

## 5. Why React Uses the Second Approach

### 🎤 Interview Question:
**"Why does React prefer rendering first and then fetching data, instead of fetching data first?"**

### 💬 Conversational Answer:

The core reason is **user experience and React's performance model**.

React's biggest strength is efficient, fast DOM manipulation. Its reconciliation algorithm (React Fiber) means it can re-render components with minimal actual DOM changes. So the cost of rendering twice is very low.

Meanwhile, API calls can take 200ms, 500ms, or even several seconds. Making the user wait behind a blank page during that time is a poor experience.

By rendering a skeleton immediately, the user feels the app is responsive. Psychologically, a user who sees something — even a placeholder — is much more patient than one who sees a blank screen.

This is why modern apps (Swiggy, YouTube, LinkedIn) all use shimmer/skeleton UIs rather than blank screens or simple loading spinners.

---

## 6. useEffect Hook

### 🎤 Interview Question:
**"What is the useEffect hook? What is its purpose and syntax?"**

### 💬 Conversational Answer:

Just like `useState`, `useEffect` is a normal JavaScript utility function provided by React (a hook). But while `useState` is for creating reactive variables, `useEffect` is for running code **after a component renders**.

**Syntax:**

```javascript
import { useEffect } from "react";

const Body = () => {
  useEffect(() => {
    // This code runs AFTER the component renders
    console.log("useEffect called");
  }, []);  // ← dependency array (empty = run once after first render)

  return <div>Body Component</div>;
};
```

`useEffect` takes **two arguments**:
1. **Callback function** — the code you want to run after render
2. **Dependency array** — controls when the callback runs (we'll cover this more in later episodes)

**Key rule:**
> The callback function inside `useEffect` is called **after** the component renders.

**Why is this useful for API calls?**

This is exactly what we need for Approach 2:
1. The component renders (shows skeleton)
2. `useEffect` fires after render
3. Inside `useEffect`, we make the API call
4. When data arrives, we update state
5. State update triggers a re-render with real data

---

## 7. useEffect Fires AFTER Render

### 🎤 Interview Question:
**"What is the execution order — does useEffect run before or after the component renders? How would you prove it?"**

### 💬 Conversational Answer:

`useEffect`'s callback always runs **after** the component has rendered. This is the fundamental guarantee React makes about `useEffect`.

**Proof with console.log:**

```javascript
const Body = () => {
  useEffect(() => {
    console.log("useEffect called");   // prints SECOND
  }, []);

  console.log("Body rendered");        // prints FIRST

  return <div>Body</div>;
};
```

**Output order:**
```
Body rendered        ← happens first (during render)
useEffect called     ← happens second (after render completes)
```

**Why does this matter?**

If `useEffect` ran before the render, nothing would be on screen when it fires. The whole point of `useEffect` is to do side effects (like API calls) **after** the component is visible to the user. This way, the user sees the UI (even if it's a shimmer/skeleton) before the API call starts.

The instructor demonstrated this with a browser debugger:
1. Added a breakpoint before and inside `useEffect`
2. On page load, the body component rendered with dummy data first
3. Only after the render was complete did the `useEffect` callback fire

---

## 8. Making an API Call with fetch()

### 🎤 Interview Question:
**"How do you make an API call inside a React component? Explain the fetch API and async/await."**

### 💬 Conversational Answer:

Making an API call in React is just plain JavaScript — React doesn't add anything special here.

**`fetch()` — what is it?**

`fetch` is a built-in function provided by the **browser** (not JavaScript itself, not React). It allows you to make HTTP requests to any URL and get back data.

**What does fetch return?** A **Promise** — meaning the data doesn't come back immediately; it comes back asynchronously.

**Two ways to handle a Promise:**

**Method 1: `.then()` / `.catch()`** (older style)
```javascript
fetch(URL)
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error(error));
```

**Method 2: `async / await`** (modern, recommended)
```javascript
const fetchData = async () => {
  const response = await fetch(URL);       // wait for response
  const data = await response.json();      // wait for JSON conversion
  console.log(data);
};
```

**Industry standard:** Use `async/await` — it's cleaner, easier to read, and the standard practice in most modern projects.

**Full example inside useEffect:**

```javascript
useEffect(() => {
  fetchRestaurants();
}, []);

const fetchRestaurants = async () => {
  const response = await fetch("https://www.swiggy.com/api/restaurants");
  const data = await response.json();
  console.log(data);
};
```

**Why two `await`s?**

1. First `await` waits for the HTTP response to arrive (headers, status code)
2. Second `await` waits for the response body to be fully read and parsed as JSON

The response body is a "readable stream" — calling `.json()` on it also returns a Promise, which is why you need a second `await`.

---

## 9. CORS Error

### 🎤 Interview Question:
**"What is a CORS error? Why does it happen and how can you bypass it during development?"**

### 💬 Conversational Answer:

**CORS = Cross-Origin Resource Sharing**

**The error:**
```
Access to fetch at 'https://www.swiggy.com/...' from origin 
'http://localhost:1234' has been blocked by CORS policy.
```

**Why does this happen?**

Your **browser** enforces a security rule: JavaScript running on one origin (e.g., `localhost:1234`) cannot make requests to a different origin (e.g., `swiggy.com`) unless that other origin explicitly allows it.

**Who is blocking you?**

The browser — not the server, not React. Chrome, Firefox, Safari all enforce this rule. It's a security feature to prevent malicious websites from making requests on your behalf to other websites you might be logged into.

**Origin mismatch example:**
```
Your app:    http://localhost:1234     ← origin 1
Swiggy API:  https://www.swiggy.com   ← origin 2 (different!)
```

Different protocol (`http` vs `https`), different domain (`localhost` vs `swiggy.com`) — these are different origins. Browser blocks it.

**How to bypass during development:**

Install a **CORS browser extension**:
- Chrome: Search "CORS Chrome extension" in Chrome Web Store
- Firefox: Search for an equivalent CORS extension for Firefox

This extension adds the appropriate headers to make the browser allow cross-origin requests. Turn it on while developing, turn it off when not needed.

**Important note:**

This is a development-only solution. In a real production app, the backend server sets proper CORS headers (`Access-Control-Allow-Origin`) to explicitly allow your frontend domain. You'd never use a browser extension in production.

**What happens in real companies:**

When your UI (say `app.namastedev.com`) calls your backend (`api.namastedev.com`), the backend includes CORS headers that say: "I allow requests from `app.namastedev.com`." The browser sees this, trusts it, and allows the call.

---

## 10. Updating State with Live API Data

### 🎤 Interview Question:
**"Once you fetch data from an API inside useEffect, how do you display it on screen?"**

### 💬 Conversational Answer:

The pattern is always the same:
1. Fetch the data
2. Call the state setter (`setListOfRestaurants(data)`)
3. React re-renders the component with the new data

```javascript
const Body = () => {
  const [listOfRestaurants, setListOfRestaurants] = useState([]);

  useEffect(() => {
    fetchData();
  }, []);

  const fetchData = async () => {
    const response = await fetch(SWIGGY_API_URL);
    const data = await response.json();

    // Navigate the nested JSON to find the restaurant array
    setListOfRestaurants(
      data?.data?.cards[2]?.data?.data?.cards
    );
  };

  return (
    <div>
      {listOfRestaurants.map((restaurant) => (
        <RestaurantCard key={restaurant.data.id} resData={restaurant} />
      ))}
    </div>
  );
};
```

**What happens step by step:**

1. Component renders with empty `listOfRestaurants` → shows shimmer
2. `useEffect` fires → `fetchData()` is called
3. `fetch()` makes the HTTP call to Swiggy's API
4. Data comes back → `setListOfRestaurants(data)` is called
5. State variable updates → React re-renders Body
6. Body now renders all 15 restaurant cards from live data

**Important:** The live API JSON is deeply nested. In the episode, the path to the restaurant array was:
```javascript
data?.data?.cards[2]?.data?.data?.cards
```

In a real project, you always inspect the API response in the browser's Network tab, understand the structure, and navigate to the array you need.

**Remove mock data:**

Once you're using live data, delete your `mockData.js` file. You no longer need it. The initial state for `listOfRestaurants` should be an empty array `[]` — the shimmer will show until the API responds.

---

## 11. Optional Chaining

### 🎤 Interview Question:
**"What is optional chaining in JavaScript? Why is it useful with API data?"**

### 💬 Conversational Answer:

Optional chaining (`?.`) is a JavaScript operator that safely navigates deeply nested objects without throwing an error if something in the middle is `null` or `undefined`.

**Without optional chaining (dangerous):**
```javascript
const cards = data.data.cards[2].data.data.cards;
// If data.data is null or data.data.cards[2] is undefined → throws TypeError!
```

**With optional chaining (safe):**
```javascript
const cards = data?.data?.cards[2]?.data?.data?.cards;
// If anything in the chain is null/undefined → returns undefined instead of crashing
```

**Why it matters with API data:**

APIs can sometimes return null values, missing fields, or unexpected structure. Optional chaining prevents your entire app from crashing just because one nested field was missing. It's a defensive coding practice.

**Pattern:**
```
object?.property?.nestedProperty?.[index]?.deeperProperty
```

Each `?.` says: "If the thing before me is null or undefined, stop here and return undefined. Don't throw an error."

---

## 12. Shimmer UI

### 🎤 Interview Question:
**"What is Shimmer UI? Why is it better than a loading spinner? Which major apps use it?"**

### 💬 Conversational Answer:

**Shimmer UI** is a technique where, while the actual data is loading, you show **fake skeleton cards** that look like the real content but are just gray placeholder shapes. This gives users a preview of the layout before the data arrives.

**Why it's better than a spinner:**

With a spinner or "Loading..." text:
- User sees a blank page or spinning icon
- The experience feels jarring when content suddenly appears
- User has no idea what the content structure looks like

With Shimmer UI:
- User immediately sees the shape and layout of the page
- Psychologically, the user anticipates what's coming
- Transition from shimmer to real content feels smooth, not abrupt
- User never feels like the site is "not loading"

**The psychological effect:**

When you see shimmer cards, your brain registers: "Ah, there will be cards here. They're loading." This sets the right expectation. When the real cards fill in, it feels natural. But when nothing loads and then suddenly 15 cards appear? That's jarring.

**Apps that use Shimmer UI:**

- **Swiggy** — shows fake restaurant cards with gray boxes while the API loads
- **YouTube** — shows gray circles and rectangles for thumbnails, titles, channel names
- **LinkedIn** — shows skeleton profiles and posts while the feed loads
- **Netflix, Amazon, Facebook** — all use some form of skeleton/shimmer loading

This is the **industry standard** today. If you're building a UI app with API calls, always implement shimmer UI.

---

## 13. Building a Shimmer Component

### 🎤 Interview Question:
**"How do you build a Shimmer UI component in React?"**

### 💬 Conversational Answer:

A Shimmer component is just a functional component that returns fake placeholder cards styled with gray backgrounds.

**Shimmer.js:**

```javascript
// src/components/Shimmer.js

const Shimmer = () => {
  return (
    <div className="shimmer-container">
      <div className="shimmer-card"></div>
      <div className="shimmer-card"></div>
      <div className="shimmer-card"></div>
      <div className="shimmer-card"></div>
      <div className="shimmer-card"></div>
      <div className="shimmer-card"></div>
      <div className="shimmer-card"></div>
      <div className="shimmer-card"></div>
      <div className="shimmer-card"></div>
      <div className="shimmer-card"></div>
      <div className="shimmer-card"></div>
      <div className="shimmer-card"></div>
      <div className="shimmer-card"></div>
      <div className="shimmer-card"></div>
      <div className="shimmer-card"></div>
    </div>
  );
};

export default Shimmer;
```

**CSS for shimmer cards:**

```css
.shimmer-container {
  display: flex;
  flex-wrap: wrap;
}

.shimmer-card {
  width: 200px;
  height: 400px;
  background-color: #f0f0f0;
  margin: 20px;
}
```

**Using it in Body.js:**

```javascript
import Shimmer from "./Shimmer";

const Body = () => {
  const [listOfRestaurants, setListOfRestaurants] = useState([]);

  // Show Shimmer while data is loading
  if (listOfRestaurants.length === 0) {
    return <Shimmer />;
  }

  return (
    <div className="body">
      {/* real restaurant cards */}
    </div>
  );
};
```

**Making it fancier:**

You can add a CSS animation to give the shimmer a subtle "wave" effect (the actual shimmer/sparkle). Basic CSS animation:

```css
@keyframes shimmer {
  0%   { background-position: -1000px 0; }
  100% { background-position: 1000px 0; }
}

.shimmer-card {
  background: linear-gradient(to right, #f0f0f0 8%, #e0e0e0 18%, #f0f0f0 33%);
  background-size: 2000px 100%;
  animation: shimmer 1.5s infinite linear;
}
```

---

## 14. Conditional Rendering

### 🎤 Interview Question:
**"What is conditional rendering in React? How do you implement it?"**

### 💬 Conversational Answer:

**Conditional rendering** is just a fancy name for rendering different JSX based on a condition. It's exactly what it sounds like — render this if condition A, render that if condition B.

**Why the fancy name?**

Interviewers love to ask "what is conditional rendering" — but if you know it, it's just an `if` statement or a ternary operator inside your component.

**Method 1: if/return (early return pattern)**

```javascript
const Body = () => {
  const [listOfRestaurants, setListOfRestaurants] = useState([]);

  // Condition: if no data yet, show shimmer
  if (listOfRestaurants.length === 0) {
    return <Shimmer />;
  }

  // Otherwise, show the real content
  return (
    <div className="body">
      {listOfRestaurants.map(res => (
        <RestaurantCard key={res.data.id} resData={res} />
      ))}
    </div>
  );
};
```

**Method 2: Ternary operator (inline, single return)**

```javascript
const Body = () => {
  const [listOfRestaurants, setListOfRestaurants] = useState([]);

  return listOfRestaurants.length === 0
    ? <Shimmer />
    : (
      <div className="body">
        {listOfRestaurants.map(res => (
          <RestaurantCard key={res.data.id} resData={res} />
        ))}
      </div>
    );
};
```

Both work identically. The ternary version is more compact. The if/return version is more readable for beginners.

**Ternary operator syntax:**
```
condition ? valueIfTrue : valueIfFalse
```

Think of it as a shorthand if-else on one line.

**Other common use cases for conditional rendering:**

```jsx
{/* Show element only if logged in */}
{isLoggedIn && <UserProfile />}

{/* Show different message based on count */}
{count === 0 ? <p>No items</p> : <p>{count} items</p>}

{/* Show error message if it exists */}
{error && <p className="error">{error}</p>}
```

---

## 15. Login/Logout Toggle

### 🎤 Interview Question:
**"Walk me through how you'd build a login/logout toggle button using useState."**

### 💬 Conversational Answer:

This is a classic example that shows **why we need state variables** and why regular JavaScript variables don't work for dynamic UI.

**First attempt — regular variable (BROKEN):**

```javascript
// ❌ Does NOT work — UI never updates
const Header = () => {
  let btnName = "Login";

  return (
    <div className="header">
      <button onClick={() => {
        btnName = "Logout";  // variable changes, but UI doesn't update!
        console.log(btnName); // logs "Logout" but button still says "Login"
      }}>
        {btnName}
      </button>
    </div>
  );
};
```

The variable does get updated (you can see it in `console.log`), but the UI doesn't refresh because React has no idea the variable changed. React is not "watching" regular JavaScript variables.

**Fixed with useState:**

```javascript
// ✅ Works correctly
import { useState } from "react";

const Header = () => {
  const [btnNameReact, setBtnNameReact] = useState("Login");

  return (
    <div className="header">
      <nav>
        <ul>
          <li>Home</li>
          <li>About Us</li>
          <li>Cart</li>
        </ul>
      </nav>
      <button
        className="login-btn"
        onClick={() => {
          setBtnNameReact(
            btnNameReact === "Login" ? "Logout" : "Login"
          );
        }}
      >
        {btnNameReact}
      </button>
    </div>
  );
};
```

**What happens when you click:**

1. `onClick` fires
2. `setBtnNameReact("Logout")` is called
3. React updates `btnNameReact` state
4. React re-renders the `Header` component
5. `useEffect` is not involved — it's just state change → re-render
6. The button now shows "Logout"
7. Click again → "Login" → click again → "Logout" — it toggles!

**Proof it works — Header renders log:**

```javascript
const Header = () => {
  console.log("Header rendered");  // ← watch this in console
  const [btnNameReact, setBtnNameReact] = useState("Login");
  // ...
};
```

Every click prints "Header rendered" — proving the entire Header component re-renders on each click.

---

## 16. Why Does the Whole Component Re-Render?

### 🎤 Interview Question:
**"When state changes, does React re-render the whole component or just the part that changed? How does it stay fast?"**

### 💬 Conversational Answer:

This is one of the most important questions in React — and most developers don't know the full answer.

**Short answer:** React re-renders the **whole component** (calls the entire function again). But it only updates the **specific DOM nodes that actually changed**.

**How?**

This is where the **Diff Algorithm and Reconciliation** (React Fiber) come in:

1. State changes (e.g., `setBtnNameReact("Logout")`)
2. React calls the Header function again → creates a new Virtual DOM
3. React compares new Virtual DOM vs old Virtual DOM (diff algorithm)
4. React finds: only the button text changed. Everything else (logo, nav items) is identical.
5. React updates **only that button** in the actual DOM. Everything else is untouched.

**Proof in the browser:**

In DevTools → Elements tab, watch the DOM while clicking the login/logout button:
- The entire header's HTML is NOT re-painted
- Only the button text flickers (indicating it was updated)
- The logo image does NOT reload
- The nav items do NOT update

This is why React is fast. It renders the full component conceptually (calls the function again), but through reconciliation, it makes minimal, surgical DOM changes.

**The line to remember:**
> React re-renders the whole component, but only updates what changed in the DOM.

---

## 17. How Can a `const` Variable Change?

### 🎤 Interview Question:
**"If state variables are declared as const, how can they ever change? This seems to violate JavaScript's rules."**

### 💬 Conversational Answer:

This is a subtle but brilliant question. 99.99% of React developers use `useState` but never think about this. Let's break it down.

**The confusion:**

```javascript
const [btnNameReact, setBtnNameReact] = useState("Login");
```

`btnNameReact` is declared with `const`. In normal JavaScript, `const` means you can never reassign it. So how does calling `setBtnNameReact("Logout")` seem to "change" `btnNameReact`?

**The answer:**

`btnNameReact` is NOT being reassigned. What's actually happening is:

1. You call `setBtnNameReact("Logout")`
2. React schedules a re-render
3. React calls the `Header` **function again** (from scratch)
4. Inside this **new function call**, `useState("Login")` runs again
5. But React knows the state was updated, so it returns `"Logout"` instead of `"Login"`
6. This **new execution** creates a **new `const btnNameReact`** = `"Logout"`

It's not the same `const` being mutated. It's a brand new `const` in a brand new function invocation, initialized with the updated value that React tracked internally.

**Analogy:**

Think of it like calling a function twice:
```javascript
function getGreeting(name) {
  const greeting = "Hello, " + name;
  return greeting;
}

getGreeting("Alice");  // greeting = "Hello, Alice"
getGreeting("Bob");    // greeting = "Hello, Bob" (new const, not mutation)
```

Each call creates its own `greeting`. The second `greeting` isn't the first one mutated — it's a brand new variable. React works the same way with `useState`.

**The key insight:**

When you call `setBtnNameReact`, React:
1. Notes the new value internally
2. Re-renders the component (calls the function again)
3. On the re-render, `useState` returns the new value
4. A new `const` is created with the new value in this new render

The `const` is never mutated. A new `const` is created on each render with the current state value. This is why the `setBtnName` setter function is necessary — without it, React would never know to re-render.

---

## 18. Controlled Input and onChange Handler

### 🎤 Interview Question:
**"Why does an input box stop working when you bind its value to a state variable? How do you fix it?"**

### 💬 Conversational Answer:

This is a very common bug that catches almost every React beginner.

**The bug:**

```javascript
const Body = () => {
  const [searchText, setSearchText] = useState("");

  return (
    <input
      type="text"
      value={searchText}    // ← BOUND to state
      // Missing: onChange handler!
    />
  );
};
```

If you run this, you can click the input box and try to type — but **nothing appears**. The input seems frozen.

**Why?**

Because you've tied the `value` of the input to `searchText`. Whatever `searchText` holds — that's what the input shows. Always.

Right now `searchText` is `""`. You type "cafe". But `searchText` is still `""` because you never updated it. React re-renders, sees `value=""`, and resets the input to empty. Your typing is immediately overwritten.

**The fix — add an onChange handler:**

```javascript
const [searchText, setSearchText] = useState("");

<input
  type="text"
  value={searchText}
  onChange={(e) => setSearchText(e.target.value)}
/>
```

**How it works now:**

1. User presses `C`
2. `onChange` fires with event `e`
3. `e.target.value` = `"C"` (the current value of the input)
4. `setSearchText("C")` is called
5. React re-renders, `searchText` = `"C"`, input shows `"C"`
6. User presses `A` → `onChange` fires → `setSearchText("CA")` → re-render → shows `"CA"`
7. And so on for each keystroke

This pattern — binding input `value` to state AND having `onChange` update that state — is called a **Controlled Component**.

**The component re-renders on every keystroke!**

This seems alarming — typing "Cafe" means 4 re-renders of the entire Body component. But React is so efficient at finding what changed (just the input value) that 4, 10, or even 50 re-renders feel instant. The diff algorithm ensures only the input box updates in the actual DOM.

**The goosebump moment (instructor's words):**

The instructor was genuinely amazed when he first discovered that React re-renders the whole Body component on every single keystroke. Typing one word triggers dozens of full component re-renders. And yet the user feels zero lag. This is the magic of React's reconciliation algorithm — React renders fast enough that this level of re-rendering is completely acceptable.

---

## 19. Search Functionality with Filter

### 🎤 Interview Question:
**"How do you build a search/filter functionality in React using state and event handlers?"**

### 💬 Conversational Answer:

The full search implementation ties together everything we've learned: state, event handlers, controlled inputs, and the reconciliation cycle.

**Full implementation:**

```javascript
const Body = () => {
  const [listOfRestaurants, setListOfRestaurants] = useState([]);
  const [searchText, setSearchText] = useState("");

  useEffect(() => {
    fetchData();
  }, []);

  const fetchData = async () => {
    const response = await fetch(SWIGGY_API_URL);
    const data = await response.json();
    setListOfRestaurants(data?.data?.cards[2]?.data?.data?.cards);
  };

  return (
    <div className="body">
      <div className="filter">

        {/* Search section */}
        <div className="search">
          <input
            type="text"
            className="search-box"
            value={searchText}
            onChange={(e) => setSearchText(e.target.value)}
          />
          <button
            onClick={() => {
              const filteredRestaurants = listOfRestaurants.filter(
                (res) =>
                  res.data.name
                    .toLowerCase()
                    .includes(searchText.toLowerCase())
              );
              setListOfRestaurants(filteredRestaurants);
            }}
          >
            Search
          </button>
        </div>

        {/* Top rated filter button */}
        <button
          className="filter-btn"
          onClick={() => {
            const topRated = listOfRestaurants.filter(
              (res) => res.data.avgRating > 4
            );
            setListOfRestaurants(topRated);
          }}
        >
          Top Rated Restaurants
        </button>

      </div>

      {/* Restaurant cards */}
      <div className="rest-container">
        {listOfRestaurants.map((restaurant) => (
          <RestaurantCard key={restaurant.data.id} resData={restaurant} />
        ))}
      </div>
    </div>
  );
};
```

**Case insensitivity fix:**

A common bug: searching "COFFEE" won't find "Coffee" because of case mismatch.

Fix: convert both strings to lowercase before comparing:
```javascript
res.data.name.toLowerCase().includes(searchText.toLowerCase())
```

Now "COFFEE", "coffee", "CoFfEe" — all match "Coffee" or "Third Wave Coffee".

---

## 20. The Filter Bug and the Fix

### 🎤 Interview Question:
**"Describe a common bug with search/filter in React and how to fix it using two state variables."**

### 💬 Conversational Answer:

**The Bug:**

After searching "coffee" (shows 2 results), if you then search "cafe" — you get nothing. Why?

Because we're filtering `listOfRestaurants` and then **storing the result back into `listOfRestaurants`**. So after the first search:

```
listOfRestaurants = [coffee1, coffee2]  ← we've lost the other 13 restaurants!
```

Now when you search "cafe", you're searching only these 2 coffee restaurants — there's no cafe in that list. The original 15 restaurant data is gone.

**The fix — use TWO state variables:**

```javascript
const Body = () => {
  // Original, never-modified list (source of truth)
  const [listOfRestaurants, setListOfRestaurants] = useState([]);

  // Filtered list — what we actually display
  const [filteredRestaurants, setFilteredRestaurants] = useState([]);

  const fetchData = async () => {
    const response = await fetch(SWIGGY_API_URL);
    const data = await response.json();
    const restaurants = data?.data?.cards[2]?.data?.data?.cards;

    // Update BOTH state variables when data arrives
    setListOfRestaurants(restaurants);
    setFilteredRestaurants(restaurants);
  };

  const handleSearch = () => {
    // Filter from the ORIGINAL list, update the FILTERED list
    const filtered = listOfRestaurants.filter((res) =>
      res.data.name.toLowerCase().includes(searchText.toLowerCase())
    );
    setFilteredRestaurants(filtered);  // ← update filtered, NOT the original
  };

  return (
    <div>
      <input
        value={searchText}
        onChange={(e) => setSearchText(e.target.value)}
      />
      <button onClick={handleSearch}>Search</button>

      {/* Display filtered restaurants, not the original list */}
      {filteredRestaurants.map((res) => (
        <RestaurantCard key={res.data.id} resData={res} />
      ))}
    </div>
  );
};
```

**The key insight:**

- `listOfRestaurants` — the original data from the API. **Never modified after initial load.** This is your source of truth.
- `filteredRestaurants` — what you display. Changes every time the user searches.
- Search always runs `listOfRestaurants.filter(...)` — so it always searches the full dataset.
- Display always renders `filteredRestaurants` — so it shows only the filtered results.

**Behavior after fix:**

1. Page loads → both lists = 15 restaurants
2. Search "coffee" → filteredRestaurants = 2, listOfRestaurants still = 15
3. Search "cafe" → filters from the original 15 → finds cafe restaurants correctly
4. Clear search and search "" → all 15 show again

**The bug comes down to this principle:**

Never use your original data as the variable you modify. Always keep a clean copy and work from that.

---

## 21. Life Lesson: Code Slow

### 🎤 Interview Question (Conceptual):
**"What does 'code slow' mean as a developer philosophy?"**

### 💬 Conversational Answer:

The instructor closes the episode with a life lesson that applies beyond React:

**"Code slow. It is very underrated."**

**What code slow means:**

As you write each line, ask yourself:
- **Why** am I writing this?
- **Is this really required?**
- **Can this be better?**
- **What happens if this line is wrong?**

For example, when writing:
```javascript
res.data.name.toLowerCase().includes(searchText.toLowerCase())
```

Ask yourself:
- Why `toLowerCase()` on both? (Because case-insensitive matching)
- Why `includes()` instead of `===`? (Because we want partial matches, not exact)
- What if `res.data.name` is null? (Add optional chaining if needed)

**People who code fast vs people who code slow:**

> People who code faster will debug a lot. People who code slow don't need to debug, or spend very less time debugging.

When you rush, you introduce bugs you don't understand. When you go slow and question everything, you write code you understand completely — so when a bug does appear, you know exactly where to look.

**Analogy:** Learning to drive

You can't learn to drive by watching YouTube videos. You have to actually sit in the car, make mistakes, hit curbs, stall the engine. Only then do you learn.

Same with coding — watching the instructor code won't make you a React developer. You have to type it yourself, get the bugs, debug them, go back to the video to understand why, and code again.

**Practical exercise the instructor recommends:**

After watching the episode, go to the Elements tab in DevTools and watch what changes in the HTML as you:
- Type each character in the search box
- Click the search button
- Toggle the login button

See with your own eyes that React only changes the minimum necessary DOM nodes. This will cement your understanding of reconciliation more than any explanation.

---

## 22. Coding Questions

---

### Q1. useEffect basic syntax — runs once after render

```javascript
import { useState, useEffect } from "react";

const Body = () => {
  const [restaurants, setRestaurants] = useState([]);

  useEffect(() => {
    console.log("useEffect called — runs AFTER render");
    // API call goes here
  }, []);  // empty array = run once after first render

  console.log("Body rendered");  // runs BEFORE useEffect

  return <div>Body</div>;
};

// Console output order:
// 1. "Body rendered"      ← during render
// 2. "useEffect called"   ← after render
```

---

### Q2. Fetch data from API inside useEffect

```javascript
import { useState, useEffect } from "react";

const Body = () => {
  const [restaurants, setRestaurants] = useState([]);

  useEffect(() => {
    fetchData();
  }, []);

  const fetchData = async () => {
    const response = await fetch("https://api.example.com/restaurants");
    const data = await response.json();
    setRestaurants(data.restaurants);  // triggers re-render
  };

  return (
    <div>
      {restaurants.map((r) => (
        <div key={r.id}>{r.name}</div>
      ))}
    </div>
  );
};
```

---

### Q3. Conditional rendering — shimmer until data loads

```javascript
import { useState, useEffect } from "react";
import Shimmer from "./Shimmer";

const Body = () => {
  const [restaurants, setRestaurants] = useState([]);

  useEffect(() => {
    fetchData();
  }, []);

  const fetchData = async () => {
    const response = await fetch(API_URL);
    const data = await response.json();
    setRestaurants(data?.restaurants);
  };

  // Method 1: if/return (early return)
  if (restaurants.length === 0) {
    return <Shimmer />;
  }

  return (
    <div className="body">
      {restaurants.map((r) => (
        <div key={r.id}>{r.name}</div>
      ))}
    </div>
  );
};
```

---

### Q4. Conditional rendering with ternary operator

```javascript
const Body = () => {
  const [restaurants, setRestaurants] = useState([]);

  // Single return using ternary
  return restaurants.length === 0
    ? <Shimmer />
    : (
      <div className="body">
        {restaurants.map((r) => (
          <div key={r.id}>{r.name}</div>
        ))}
      </div>
    );
};
```

---

### Q5. Build a Shimmer component

```javascript
// components/Shimmer.js
const Shimmer = () => {
  return (
    <div className="shimmer-container">
      {/* Render 15 fake cards */}
      {Array(15).fill("").map((_, i) => (
        <div key={i} className="shimmer-card"></div>
      ))}
    </div>
  );
};

export default Shimmer;
```

```css
/* index.css */
.shimmer-container {
  display: flex;
  flex-wrap: wrap;
}

.shimmer-card {
  width: 200px;
  height: 400px;
  background-color: #f0f0f0;
  margin: 20px;
  border-radius: 8px;
}
```

---

### Q6. Login/Logout toggle with useState

```javascript
import { useState } from "react";

const Header = () => {
  const [btnName, setBtnName] = useState("Login");

  return (
    <div className="header">
      <nav>
        <ul>
          <li>Home</li>
          <li>About</li>
          <li>Cart</li>
        </ul>
      </nav>
      <button
        className="login-btn"
        onClick={() => {
          setBtnName(btnName === "Login" ? "Logout" : "Login");
        }}
      >
        {btnName}
      </button>
    </div>
  );
};
```

---

### Q7. Why regular variable doesn't update UI (the bug)

```javascript
// ❌ BROKEN — UI never updates
const Header = () => {
  let btnName = "Login";  // regular variable, NOT state

  return (
    <button onClick={() => {
      btnName = "Logout";
      console.log(btnName); // logs "Logout" — variable changed
      // BUT THE UI STILL SHOWS "Login" — no re-render!
    }}>
      {btnName}
    </button>
  );
};
```

---

### Q8. Controlled input — bind value to state with onChange

```javascript
import { useState } from "react";

const SearchBar = () => {
  const [searchText, setSearchText] = useState("");

  return (
    <div>
      <input
        type="text"
        value={searchText}              // bind to state
        onChange={(e) => setSearchText(e.target.value)}  // update state on change
        placeholder="Search restaurants..."
      />
      <p>You typed: {searchText}</p>
    </div>
  );
};
```

---

### Q9. Search filter — the buggy version (DO NOT use)

```javascript
// ❌ BUGGY — destroys original data on first search
const handleSearch = () => {
  const filtered = listOfRestaurants.filter(
    (res) => res.data.name.toLowerCase().includes(searchText.toLowerCase())
  );
  setListOfRestaurants(filtered);  // ← WRONG: modifies original list!
  // After this, listOfRestaurants only has filtered results
  // Second search will search WITHIN filtered results, not all 15
};
```

---

### Q10. Search filter — the correct version (two state variables)

```javascript
import { useState, useEffect } from "react";

const Body = () => {
  const [listOfRestaurants, setListOfRestaurants] = useState([]);   // original
  const [filteredRestaurants, setFilteredRestaurants] = useState([]); // display
  const [searchText, setSearchText] = useState("");

  useEffect(() => {
    fetchData();
  }, []);

  const fetchData = async () => {
    const response = await fetch(API_URL);
    const data = await response.json();
    const restaurants = data?.data?.cards[2]?.data?.data?.cards;
    setListOfRestaurants(restaurants);     // original copy — never changes after this
    setFilteredRestaurants(restaurants);   // display copy — changes on each search
  };

  const handleSearch = () => {
    // Always filter from ORIGINAL list
    const filtered = listOfRestaurants.filter((res) =>
      res.data.name.toLowerCase().includes(searchText.toLowerCase())
    );
    setFilteredRestaurants(filtered);  // only update filtered list
  };

  return (
    <div>
      <div className="filter">
        <input
          type="text"
          value={searchText}
          onChange={(e) => setSearchText(e.target.value)}
        />
        <button onClick={handleSearch}>Search</button>
      </div>

      <div className="rest-container">
        {/* Render from filteredRestaurants, NOT listOfRestaurants */}
        {filteredRestaurants.map((res) => (
          <RestaurantCard key={res.data.id} resData={res} />
        ))}
      </div>
    </div>
  );
};
```

---

### Q11. Optional chaining with API data

```javascript
const fetchData = async () => {
  const response = await fetch(API_URL);
  const data = await response.json();

  // Without optional chaining (dangerous — crashes if any level is null)
  const restaurants = data.data.cards[2].data.data.cards;  // ❌

  // With optional chaining (safe — returns undefined instead of crashing)
  const restaurantsSafe = data?.data?.cards[2]?.data?.data?.cards;  // ✅

  setListOfRestaurants(restaurantsSafe || []);
};
```

---

## 🗒️ Quick Revision Card

| Concept | Key Point |
|---|---|
| Monolith | One big project with everything (API, UI, auth, DB all together) |
| Microservice | Separate services for separate jobs; each with its own codebase |
| Separation of Concerns | Each service does one job; single responsibility principle |
| React app in microservices | Our React app IS the UI microservice |
| Microservices communication | Via HTTP calls to different ports/URLs |
| Approach 1 (data-first) | Wait for API → then render. Blank page until data arrives. Bad UX |
| Approach 2 (render-first) | Render first (shimmer) → fetch API → re-render with data. Good UX |
| React uses | Always Approach 2 — render first, fetch after |
| useEffect | Normal JS utility function from React; runs callback AFTER component renders |
| useEffect syntax | `useEffect(() => { /* code */ }, [])` |
| Callback vs render order | Body rendered first, THEN useEffect callback fires |
| fetch() | Browser-provided function to make HTTP requests; returns a Promise |
| async/await | Modern way to handle Promises; industry standard |
| Two awaits in fetch | First for response, second for `.json()` — both are Promises |
| CORS | Browser blocks cross-origin requests; development bypass = CORS extension |
| Shimmer UI | Fake placeholder cards shown while API loads; better UX than spinner |
| Apps using Shimmer | YouTube, Swiggy, LinkedIn, Netflix — industry standard |
| Conditional rendering | Render different JSX based on condition; just an if/ternary |
| Early return pattern | `if (loading) return <Shimmer />;` before the main return |
| Ternary rendering | `condition ? <Shimmer /> : <RealContent />` |
| Login/Logout toggle | useState("Login") + onClick toggle using ternary |
| Whole component re-renders | Yes, but reconciliation only updates changed DOM nodes |
| const state variable | Not mutated — new const created on each re-render with updated value |
| Controlled input | input `value` bound to state + `onChange` updates that state |
| Why input freezes | If you bind value to state but forget onChange, input can't update |
| onChange event | `(e) => setSearchText(e.target.value)` |
| Filter bug | Overwriting original list with filtered results destroys data |
| Filter fix | Two state variables: one for original (never change), one for display |
| Case insensitive search | `.toLowerCase()` on both the name and the search text |
| Code slow | Question every line; coders who code slow debug less |

---

*Episode 06 – Namaste React Series | Exploring the World*
