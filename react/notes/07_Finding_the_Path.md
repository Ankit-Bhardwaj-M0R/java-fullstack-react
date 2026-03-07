# Episode 07 – Finding the Path
### React Interview Prep + Coding Questions

---

## Table of Contents

1. [useEffect – The Three Dependency Array Cases](#1-useeffect-the-three-dependency-array-cases)
2. [useState – Best Practices and Rules](#2-usestate-best-practices-and-rules)
3. [What is React Router DOM?](#3-what-is-react-router-dom)
4. [createBrowserRouter – Setting Up Routing Configuration](#4-createbrowserrouter-setting-up-routing-configuration)
5. [RouterProvider – Giving the Config to Your App](#5-routerprovider-giving-the-config-to-your-app)
6. [Types of Routers in React Router DOM](#6-types-of-routers-in-react-router-dom)
7. [Creating an Error Page with errorElement](#7-creating-an-error-page-with-errorelement)
8. [useRouteError – Reading Error Details](#8-userouteerror-reading-error-details)
9. [Children Routes and the Outlet Component](#9-children-routes-and-the-outlet-component)
10. [Link Component – Never Use Anchor Tags in React](#10-link-component-never-use-anchor-tags-in-react)
11. [Single Page Application (SPA) – What It Really Means](#11-single-page-application-spa)
12. [Client-Side Routing vs Server-Side Routing](#12-client-side-routing-vs-server-side-routing)
13. [Dynamic Routes – :resId in the Path](#13-dynamic-routes-resid-in-the-path)
14. [useParams – Reading Dynamic Route Values](#14-useparams-reading-dynamic-route-values)
15. [Building the Restaurant Menu Page with Live API Data](#15-building-the-restaurant-menu-page-with-live-api-data)
16. [Making Restaurant Cards Clickable with Dynamic Links](#16-making-restaurant-cards-clickable-with-dynamic-links)
17. [Link Under the Hood – What the Browser Actually Sees](#17-link-under-the-hood)
18. [RAFCE Shortcut – Should You Use It?](#18-rafce-shortcut)
19. [Coding Questions](#19-coding-questions)

---

## 1. useEffect – The Three Dependency Array Cases

### 🎤 Interview Question:
**"When exactly is useEffect called? What is the role of the dependency array?"**

### 💬 Conversational Answer:

This is one of the most important interview questions about hooks. A lot of developers use `useEffect` every day but can't explain *when* it actually runs. Let's fix that.

First, the definition: **`useEffect` is called after every render of the component it lives in.** That's the default behavior. But the dependency array changes that behavior in three important ways.

---

**Case 1: No dependency array at all**

```javascript
useEffect(() => {
  console.log("useEffect called");
}); // ← no second argument at all
```

When you don't pass a dependency array, `useEffect` runs **after every single render** of that component. If your Header component re-renders 10 times (say, because of a login/logout button click changing state), `useEffect` will run 10 times. Every. Single. Time.

You can actually prove this to yourself. Add a console log inside your `useEffect` with no dependency array, then click a button that changes state. You'll see the log firing again and again.

---

**Case 2: Empty dependency array `[]`**

```javascript
useEffect(() => {
  console.log("useEffect called");
}, []); // ← empty array
```

When you pass an empty array, `useEffect` runs **only once — on the initial render**. After that, even if the component re-renders many times, the `useEffect` callback will not fire again. This is the case you'll use most often, especially for API calls. You only want to fetch data once when the page loads, not every time the component re-renders.

---

**Case 3: Dependency array with a variable**

```javascript
useEffect(() => {
  console.log("btnName changed:", btnName);
}, [btnName]); // ← btnName is the dependency
```

When you pass a value inside the array, `useEffect` runs on the initial render **and then again every time that specific value changes**. In this example, whenever `btnName` changes from "Login" to "Logout", this `useEffect` fires.

---

**Summary of the three cases:**

| Dependency Array | When useEffect Runs |
|---|---|
| No array | After every render |
| Empty array `[]` | Only once, on initial render |
| Array with value `[someVar]` | On initial render + every time `someVar` changes |

> 🔑 Key point: The callback function is the **only mandatory argument** to `useEffect`. The dependency array is optional — but skipping it means it runs on every render, which is usually not what you want.

---

## 2. useState – Best Practices and Rules

### 🎤 Interview Question:
**"What are the rules and best practices for using the useState hook?"**

### 💬 Conversational Answer:

React is very specific about how you should use hooks. If you mess with React, React will mess with your app. Here are the rules you must never break:

---

**Rule 1: Never use useState outside of a component**

```javascript
// ❌ WRONG — useState called outside any component
const [count, setCount] = useState(0);

const MyComponent = () => {
  return <div>{count}</div>;
};
```

This throws an error: *"Invalid hook call. Hooks can only be called inside the body of a function component."* Hooks exist to create local state variables **inside** your functional components. That's their entire purpose. Calling one outside a component makes no sense.

---

**Rule 2: Never use useState inside an if/else condition**

```javascript
// ❌ WRONG — useState inside a condition
const MyComponent = () => {
  if (someCondition) {
    const [count, setCount] = useState(0); // Don't do this!
  }
  return <div />;
};
```

This can create inconsistencies in your program. Think about it: sometimes when the component renders, `someCondition` is true (so the state variable gets created), and sometimes it's false (so it doesn't). React internally tracks hooks in the exact order they are called. If that order changes between renders, React gets confused and your app behaves unpredictably. The React documentation itself says: never use hooks inside conditions.

---

**Rule 3: Never use useState inside a for loop**

```javascript
// ❌ WRONG — useState inside a loop
const MyComponent = () => {
  for (let i = 0; i < 5; i++) {
    const [val, setVal] = useState(0); // Don't do this!
  }
  return <div />;
};
```

Same reason as above. Loops can run a different number of times on different renders. This would break React's internal hook tracking.

---

**Rule 4: Never use useState inside a nested function**

```javascript
// ❌ WRONG — useState inside a function inside a component
const MyComponent = () => {
  const handleClick = () => {
    const [count, setCount] = useState(0); // Don't do this!
  };
  return <button onClick={handleClick}>Click</button>;
};
```

Hooks must be called at the **top level** of your component function — not nested inside any other function.

---

**The Golden Rule: Call hooks at the top of your component**

```javascript
// ✅ CORRECT — hooks at the top level, first thing
const MyComponent = () => {
  const [count, setCount] = useState(0);      // ✅
  const [name, setName] = useState("React"); // ✅

  // ... rest of your logic below
  return <div>{count} - {name}</div>;
};
```

Always create your state variables right at the top when your function starts. This way, every time the component renders, hooks are always called in the exact same order, React understands your code properly, and you will never face inconsistencies.

---

## 3. What is React Router DOM?

### 🎤 Interview Question:
**"What is React Router DOM and why do we need it?"**

### 💬 Conversational Answer:

React by itself does not give you routing. If you want your app to have different pages at different URLs (like `/about`, `/contact`, `/restaurant/123`), you need a separate library for that. That library is **React Router DOM**.

React Router DOM is one of the most popular libraries in the React ecosystem. Almost every real-world React project uses it. They recently released version 6 (specifically 6.4+), which is a big improvement over version 5.

To install it:

```bash
npm install react-router-dom
```

After installation, you'll see it added to your `package.json` as a dependency. The latest version at the time of this episode was **6.11.2**.

> ⚠️ If you're working on an older project that uses version 5, the API is different. Always prefer version 6 for new projects — it's faster, cleaner, and React Router themselves recommend it.

---

## 4. createBrowserRouter – Setting Up Routing Configuration

### 🎤 Interview Question:
**"How do you set up routing in a React application using React Router DOM v6?"**

### 💬 Conversational Answer:

To create routing, you go to your root-level file (`app.js`) and create a **routing configuration** using `createBrowserRouter`.

Think of the routing configuration as a set of instructions: "If the URL is `/`, show this component. If the URL is `/about`, show that component."

Here's how it works:

```javascript
// app.js
import { createBrowserRouter } from "react-router-dom";
import AppLayout from "./components/AppLayout";
import About from "./components/About";
import Contact from "./components/Contact";
import Error from "./components/Error";

const appRouter = createBrowserRouter([
  {
    path: "/",
    element: <AppLayout />,
    errorElement: <Error />,
    children: [
      {
        path: "/",
        element: <Body />,
      },
      {
        path: "/about",
        element: <About />,
      },
      {
        path: "/contact",
        element: <Contact />,
      },
    ],
  },
]);
```

`createBrowserRouter` takes an **array of objects**. Each object is a route. Each route object has at minimum:
- `path` — the URL path (e.g., `"/"`, `"/about"`, `"/contact"`)
- `element` — the React component to render at that path

This configuration is the brain of your routing. You create it once and it tells React what to render for every URL.

---

## 5. RouterProvider – Giving the Config to Your App

### 🎤 Interview Question:
**"After creating a routing configuration with createBrowserRouter, how do you actually connect it to your React app?"**

### 💬 Conversational Answer:

Creating the configuration with `createBrowserRouter` is just step one. You also need to **provide** that configuration to your app. For that, React Router DOM gives us a component called `RouterProvider`.

Before routing, you were rendering `AppLayout` directly:

```javascript
// Before routing — old way
const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(<AppLayout />);
```

Now, instead of rendering `AppLayout` directly, you render `RouterProvider` and give it your router configuration:

```javascript
// After routing — new way
import { createBrowserRouter, RouterProvider } from "react-router-dom";

const appRouter = createBrowserRouter([
  { path: "/", element: <AppLayout /> },
  { path: "/about", element: <About /> },
]);

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(<RouterProvider router={appRouter} />);
```

The `RouterProvider` receives your `appRouter` via the `router` prop. This is what activates routing in your entire app. Without this step, your routing configuration does nothing.

**Remember these two things in your notebook:**
- `createBrowserRouter` — creates the routing configuration
- `RouterProvider` — provides (activates) that configuration in your app

---

## 6. Types of Routers in React Router DOM

### 🎤 Interview Question:
**"Are there different types of routers in React Router DOM? Which one should you use?"**

### 💬 Conversational Answer:

Yes, React Router DOM provides several types of routers:

- `createBrowserRouter` — uses the browser's URL bar (recommended for web apps)
- `createHashRouter` — uses the `#` symbol in the URL (for environments without server support)
- `createMemoryRouter` — keeps routing in memory, not in the URL (useful for testing)
- `createStaticRouter` — for server-side rendering

**Which one to use?**

The React Router documentation itself says: **createBrowserRouter is the recommended router for all React Router web projects.** So unless you have a specific use case (like testing or server-side rendering), always go with `createBrowserRouter`. It's the modern, standard way.

---

## 7. Creating an Error Page with errorElement

### 🎤 Interview Question:
**"How do you handle routing errors in React Router DOM? How do you create a custom error page?"**

### 💬 Conversational Answer:

When a user navigates to a URL that doesn't exist (like `/xyz-random-page`), React Router DOM will throw an error. By default, it shows its own built-in error page that says "Unexpected Application Error – 404 Not Found."

That's fine, but in a real app you want your own branded "Oops, something went wrong" page. You do this with the `errorElement` property in your route configuration.

**Step 1: Create your Error component**

```javascript
// components/Error.js
import { useRouteError } from "react-router-dom";

const Error = () => {
  const err = useRouteError();

  return (
    <div>
      <h1>Oops! Something went wrong.</h1>
      <h3>{err.status}: {err.statusText}</h3>
    </div>
  );
};

export default Error;
```

**Step 2: Add errorElement to your route configuration**

```javascript
// app.js
const appRouter = createBrowserRouter([
  {
    path: "/",
    element: <AppLayout />,
    errorElement: <Error />,  // ← add this
    children: [...],
  },
]);
```

Now whenever someone visits an invalid URL, React Router DOM catches the error and renders your custom `Error` component instead of the default ugly error page. You can make this page look however you want — add a cute kitten image, a funny message, whatever fits your app.

---

## 8. useRouteError – Reading Error Details

### 🎤 Interview Question:
**"What is the useRouteError hook and what does it give you?"**

### 💬 Conversational Answer:

React Router DOM gives us a hook called `useRouteError`. This is a hook (notice the `use` prefix — that's the convention for all hooks in React), and it gives you the actual error object that was thrown.

```javascript
import { useRouteError } from "react-router-dom";

const Error = () => {
  const err = useRouteError();
  console.log(err); // See what's inside

  return (
    <div>
      <h1>Oops! Something went wrong.</h1>
      <h3>{err.status}: {err.statusText}</h3>
      {/* Example output: 404: Not Found */}
    </div>
  );
};
```

When you `console.log(err)`, you'll see an object like:
```json
{
  "status": 404,
  "statusText": "Not Found",
  "data": "Error: No route matches URL \"/xyz\""
}
```

You can surface this information on your error page to give the user (or yourself during debugging) more context about what went wrong. `err.status` gives you the HTTP status code. `err.statusText` gives you a human-readable description.

> 💡 **How do you know something is a hook?** When you see a function that starts with `use` — like `useEffect`, `useState`, `useRouteError`, `useParams` — it's a hook. This is a universal convention in React.

---

## 9. Children Routes and the Outlet Component

### 🎤 Interview Question:
**"What are children routes in React Router DOM? How does the Outlet component work?"**

### 💬 Conversational Answer:

Here's a common problem: if you have a Header that should always be visible, but you also want different pages (About, Contact, Home Body) to load below the header — how do you do that?

The naive approach is to make `About` and `Contact` as top-level routes (like `/about` → `<About />` and `/contact` → `<Contact />`). But then when you visit `/about`, you only see the About component — the Header is gone! That's because you're replacing the entire rendered component, not just swapping the body.

The solution is **children routes** with an **Outlet**.

**Step 1: Make About and Contact children of AppLayout in your config**

```javascript
const appRouter = createBrowserRouter([
  {
    path: "/",
    element: <AppLayout />,   // ← this is the parent/shell
    errorElement: <Error />,
    children: [               // ← these are the children
      {
        path: "/",
        element: <Body />,    // Home page body
      },
      {
        path: "/about",
        element: <About />,   // About page
      },
      {
        path: "/contact",
        element: <Contact />, // Contact page
      },
    ],
  },
]);
```

**Step 2: Place `<Outlet />` inside AppLayout where you want children to appear**

```javascript
// AppLayout component
import { Outlet } from "react-router-dom";

const AppLayout = () => {
  return (
    <div className="app">
      <Header />      {/* Always visible — never changes */}
      <Outlet />      {/* This is where children load */}
    </div>
  );
};
```

Now here's what happens:
- When the URL is `/`, the `Body` component fills in where `<Outlet />` is
- When the URL is `/about`, the `About` component fills in where `<Outlet />` is
- When the URL is `/contact`, the `Contact` component fills in where `<Outlet />` is

The Header always stays. Only the Outlet area changes.

**Will you see `<Outlet>` in the browser's HTML?** No. React replaces the Outlet with the actual child component. The user and the HTML inspector will only see your real component's code there.

Think of Outlet as a **tunnel** — whichever child component matches the current route gets pushed through the tunnel and appears in that spot.

---

## 10. Link Component – Never Use Anchor Tags in React

### 🎤 Interview Question:
**"Why should you use the Link component instead of anchor tags for navigation in React?"**

### 💬 Conversational Answer:

When you first think about linking to another page in React, your instinct is to use a plain HTML anchor tag:

```html
<!-- This feels natural but is WRONG in React -->
<a href="/about">About Us</a>
```

It works. It navigates. But there's a massive problem: **it reloads the entire page.**

Every time you click that anchor tag, the browser makes a full network request, fetches everything fresh, and re-renders from scratch. Your React app essentially restarts. You lose any state, the logo flickers as it reloads, and the experience feels slow and clunky.

React Router DOM gives us the `Link` component to solve this:

```javascript
// ✅ CORRECT — use Link, never anchor tag
import { Link } from "react-router-dom";

const Header = () => {
  return (
    <nav>
      <ul>
        <li><Link to="/">Home</Link></li>
        <li><Link to="/about">About Us</Link></li>
        <li><Link to="/contact">Contact Us</Link></li>
      </ul>
    </nav>
  );
};
```

**The difference:**
- Anchor tag → whole page refreshes → slow, flickers, loses state
- `Link` → only the changed component updates → instant, smooth, no refresh

You can see this live: if you use an anchor tag to go to About Us and watch your logo, it re-renders (flickers). If you use `Link`, the page never refreshes — only the body area changes smoothly.

**Note the syntax difference:**
- Anchor tag uses `href="/about"`
- Link uses `to="/about"`

---

## 11. Single Page Application (SPA)

### 🎤 Interview Question:
**"What is a Single Page Application (SPA)? How does React implement it?"**

### 💬 Conversational Answer:

A **Single Page Application** means your entire app lives on one HTML page. No matter how many "pages" your app has — Home, About, Contact, Restaurant Menu — the browser only ever loads **one HTML file** once.

After that initial load, when you navigate between pages, React isn't fetching new HTML files from a server. It's just **swapping components in and out** of the already-loaded page.

Think of it this way. In the old days:
- You had `index.html`, `about.html`, `contact.html`
- Clicking "About" would tell the browser: "go get `about.html`"
- The browser makes a network request, downloads the new HTML, and re-renders everything

In a React SPA:
- You have one `index.html` forever
- Clicking "About" just tells React: "swap the Body component for the About component"
- No network call, no page reload, no flicker

This is why when you navigate using `Link`, you'll notice the header and other shared elements never flash or reload — they're untouched. Only the specific part of the DOM that changed gets updated.

> **In React, "different pages" are really just different components. There is only one actual page — that's why it's called a Single Page Application.**

---

## 12. Client-Side Routing vs Server-Side Routing

### 🎤 Interview Question:
**"What is the difference between client-side routing and server-side routing?"**

### 💬 Conversational Answer:

This is an important interview topic. There are two fundamentally different approaches to routing in web apps:

---

**Server-Side Routing (the old way):**

1. You have multiple HTML files: `index.html`, `about.html`, `contact.html`
2. When the user clicks "About", the browser sends a **network request** to the server asking for `about.html`
3. The server sends back the full HTML file
4. The browser renders it (full page refresh)

The page comes from the **server** — that's server-side routing. Every navigation = one network call.

---

**Client-Side Routing (the React way):**

1. The browser loads the app **once** with all the component code already bundled
2. When the user clicks "About", React simply **renders the About component** in place — no network call
3. The URL in the browser updates (via the History API), but no actual page reload happens

All the routing logic runs in the browser (the **client**) — that's client-side routing. No extra network calls are made just to navigate between pages.

The *only* time a network call happens in a React SPA is when you're actually fetching data from an API (like loading restaurant data). You're not fetching a new HTML page.

---

**Why does this matter?**

Client-side routing makes apps feel instant. Users navigate between pages without any loading delay. This is the superpower of React and SPAs. When an interviewer asks "why is your React app fast?", part of the answer is client-side routing.

---

## 13. Dynamic Routes – :resId in the Path

### 🎤 Interview Question:
**"What are dynamic routes? How do you create a URL that changes based on data?"**

### 💬 Conversational Answer:

Imagine you're building a food delivery app with 100 restaurants. You can't manually create 100 separate route configurations like:
```
/restaurant/meghna-foods    → MeghnaFoodsPage
/restaurant/burger-king     → BurgerKingPage
/restaurant/kfc             → KFCPage
```

That's insane. Instead, you use **dynamic routes** — one route definition that handles all restaurants using a **parameter** in the URL.

On Swiggy for example, notice the URL when you click a restaurant: it's something like `/restaurants/megha-food-225`. The `225` is the restaurant's unique ID. Every restaurant gets its own URL based on its ID, but they all use the **same component** — just with different data loaded.

**How to create a dynamic route:**

```javascript
// In your route configuration (app.js)
import RestaurantMenu from "./components/RestaurantMenu";

const appRouter = createBrowserRouter([
  {
    path: "/",
    element: <AppLayout />,
    children: [
      {
        path: "/restaurant/:resId",  // ← :resId is the dynamic part
        element: <RestaurantMenu />,
      },
    ],
  },
]);
```

The `:resId` part is a **route parameter**. The colon `:` tells React Router: "this part of the URL is dynamic — it can be any value." So:
- `/restaurant/229` → loads `RestaurantMenu` with resId = "229"
- `/restaurant/425` → loads `RestaurantMenu` with resId = "425"
- `/restaurant/123` → loads `RestaurantMenu` with resId = "123"

Same component, different data, different URL — this is dynamic routing.

---

## 14. useParams – Reading Dynamic Route Values

### 🎤 Interview Question:
**"How do you read the dynamic part of a URL (like :resId) inside your component?"**

### 💬 Conversational Answer:

When the URL is `/restaurant/229`, you need to somehow get that `229` inside your `RestaurantMenu` component so you can fetch the right restaurant's data. React Router DOM gives you a hook for exactly this: **`useParams`**.

```javascript
import { useParams } from "react-router-dom";

const RestaurantMenu = () => {
  const { resId } = useParams();
  // If URL is /restaurant/229, resId = "229"
  // If URL is /restaurant/425, resId = "425"

  console.log(resId); // prints "229" or "425" or whatever is in the URL

  // Use resId to fetch data for this specific restaurant
  useEffect(() => {
    fetchMenu();
  }, []);

  const fetchMenu = async () => {
    const data = await fetch(MENU_API_URL + resId); // ← use the dynamic ID
    const json = await data.json();
    setResInfo(json.data);
  };

  return <div>Restaurant ID: {resId}</div>;
};
```

`useParams()` returns an **object** whose keys are the parameter names you defined in the route config. If your route was `/restaurant/:resId`, then `useParams()` gives you `{ resId: "229" }`. You can destructure it directly.

You can verify this by `console.log`-ing the params — you'll see the object clearly.

---

## 15. Building the Restaurant Menu Page with Live API Data

### 🎤 Interview Question:
**"How do you build a restaurant menu page that fetches and displays live data based on the URL?"**

### 💬 Conversational Answer:

This is where everything comes together. We create a `RestaurantMenu` component that:
1. Reads the restaurant ID from the URL using `useParams`
2. Makes an API call using `useEffect` + `fetch`
3. Stores the data in state using `useState`
4. Shows a shimmer UI while loading
5. Displays the restaurant info and menu items

```javascript
// components/RestaurantMenu.js
import { useState, useEffect } from "react";
import { useParams } from "react-router-dom";
import Shimmer from "./Shimmer";
import { MENU_API_URL } from "../utils/constants";

const RestaurantMenu = () => {
  const { resId } = useParams();         // get ID from URL
  const [resInfo, setResInfo] = useState(null); // null = still loading

  useEffect(() => {
    fetchMenu();
  }, []); // empty array = run only once on initial render

  const fetchMenu = async () => {
    const data = await fetch(MENU_API_URL + resId);
    const json = await data.json();
    setResInfo(json.data);               // fill state with API response
  };

  // Early return: show shimmer while data is being fetched
  if (resInfo === null) return <Shimmer />;

  // Destructure what you need from the API response
  const { name, cuisines, costForTwoMessage } =
    resInfo?.cards[0]?.card?.card?.info;

  const itemCards =
    resInfo?.cards[2]?.groupedCard?.cardGroupMap?.REGULAR
    ?.cards[1]?.card?.card?.itemCards;

  return (
    <div className="menu">
      <h1>{name}</h1>
      <h3>{cuisines.join(", ")}</h3>
      <h3>{costForTwoMessage}</h3>
      <h2>Menu</h2>
      <ul>
        {itemCards.map((item) => (
          <li key={item.card.info.id}>
            {item.card.info.name} - Rs.{" "}
            {(item.card.info.price || item.card.info.defaultPrice) / 100}
          </li>
        ))}
      </ul>
    </div>
  );
};

export default RestaurantMenu;
```

**Important points in this code:**

- `useParams()` gives us the restaurant ID from the URL
- Empty `[]` in `useEffect` ensures we only fetch once (not on every re-render)
- `resInfo === null` check at the top is the "early return" pattern — if data hasn't loaded yet, show Shimmer and stop executing the rest of the function (this prevents errors from trying to destructure a null object)
- `cuisines.join(", ")` — cuisines is an array, so we join it with commas
- Some items have `price`, others only have `defaultPrice` — use the `||` operator to handle both cases
- Divide the price by 100 because Swiggy sends prices in paise (Indian currency subunit), not rupees
- Always add a `key` when using `.map()` — use a unique value like the item's ID

> ⚠️ **Note about live APIs:** Swiggy's API structure can change. If you're watching this months later and the data path looks different, don't panic. Open the browser console, inspect the network tab on Swiggy, find the current API and its current data structure, and adapt. The knowledge stays the same — only the exact key path changes.

---

## 16. Making Restaurant Cards Clickable with Dynamic Links

### 🎤 Interview Question:
**"How do you make each restaurant card on the home page link to that restaurant's specific menu page?"**

### 💬 Conversational Answer:

In your `Body` component, where you're rendering restaurant cards in a `.map()`, wrap each card with a `Link` component that points to the dynamic restaurant URL:

```javascript
// Body.js
import { Link } from "react-router-dom";

const Body = () => {
  return (
    <div className="rest-container">
      {filteredRestaurants.map((restaurant) => (
        <Link
          key={restaurant.data.id}                    // key goes on the outermost mapped element
          to={"/restaurant/" + restaurant.data.id}    // dynamic URL
        >
          <RestaurantCard resData={restaurant} />
        </Link>
      ))}
    </div>
  );
};
```

**Two important things here:**

1. **The `key` prop moves to `Link`** — whenever you're mapping and the outermost JSX element changes (here it's `Link` instead of `RestaurantCard`), the `key` prop must be on that outermost element, not on the child inside it.

2. **The URL is dynamic** — `"/restaurant/" + restaurant.data.id` builds the full URL like `/restaurant/229`. Since this is inside JSX (curly braces), you can do string concatenation with `+`.

This means clicking Meghna Foods goes to `/restaurant/229`, clicking Hotel Empire goes to `/restaurant/425`, and so on. The same `RestaurantMenu` component handles all of them — it just reads a different `resId` from the URL each time and fetches accordingly.

When this all works, you'll see that navigating between restaurant pages doesn't reload the whole page. The header stays intact, only the menu content swaps out. This is the beauty of client-side routing in action.

---

## 17. Link Under the Hood

### 🎤 Interview Question:
**"What is the Link component at the HTML level? What does the browser actually render?"**

### 💬 Conversational Answer:

This is a really interesting piece of information that most developers who use `Link` every day don't actually know.

If you open the browser's DevTools (Elements tab) and look for a `Link` component in the HTML, you won't find `<Link>` — because browsers don't understand React's `Link`. What you'll find is a perfectly ordinary **`<a>` anchor tag**.

React Router DOM takes your `<Link to="/about">About</Link>` and renders it as `<a href="/about">About</a>` in the actual HTML.

So why bother with `Link` at all if it just becomes an anchor tag?

Because it's a **special** anchor tag. Behind the scenes, React Router DOM:
1. Renders it as an anchor tag (so browsers understand it)
2. Intercepts the click event before the browser can act on it
3. Prevents the default page reload behavior
4. Updates the URL and renders the correct component — all without a full page refresh

The browser sees `<a href="/about">` but the click is hijacked by React Router DOM to do client-side navigation instead of a real page load.

> 💡 A great habit: always keep your browser's Developer Console open. When you explore what's happening under the hood (like discovering that `Link` is really just an anchor tag), you learn things that most developers don't know even after years of coding.

---

## 18. RAFCE Shortcut – Should You Use It?

### 🎤 Interview Question:
**"What is the RAFCE shortcut in VS Code? Should beginners use it?"**

### 💬 Conversational Answer:

In VS Code, if you type `rafce` and hit Enter (with the ES7+ React snippets extension), it automatically generates a complete functional component boilerplate for you:

```javascript
// What RAFCE generates automatically:
import React from 'react'

const Contact = () => {
  return (
    <div>Contact</div>
  )
}

export default Contact
```

It's fast and useful when you need to scaffold components quickly in a real project at a company.

**But should you use it while learning?**

No. Here's why: the more you type manually, the better each line sticks in your memory. If you rely on a shortcut, you're letting the tool do the thinking. In an interview, you won't have the shortcut. In a new editor or environment, you won't have the shortcut. And most importantly: when you type each line, you're reinforcing *why* you're writing it — what is an arrow function, what does `export default` do, what is JSX.

The recommendation: for your first 4–6 months of learning React, write every single letter manually. After you've built a few projects and the patterns are second nature, then you can start using tools like `rafce` to speed up your workflow.

> ✍️ "The more you write, the better it sticks to your mind."

---

## 19. Coding Questions

---

### Q1. Three cases of useEffect dependency array

```javascript
import { useState, useEffect } from "react";

const Header = () => {
  const [btnName, setBtnName] = useState("Login");

  // CASE 1: No dependency array — runs after EVERY render
  useEffect(() => {
    console.log("Runs every time Header renders");
  });

  // CASE 2: Empty dependency array — runs ONLY ONCE on initial render
  useEffect(() => {
    console.log("Runs only once when Header first mounts");
  }, []);

  // CASE 3: Dependency array with a value — runs when that value changes
  useEffect(() => {
    console.log("Runs whenever btnName changes:", btnName);
  }, [btnName]);

  return (
    <div>
      <button onClick={() => setBtnName(btnName === "Login" ? "Logout" : "Login")}>
        {btnName}
      </button>
    </div>
  );
};
```

---

### Q2. useState rules — correct vs incorrect usage

```javascript
// ❌ WRONG: useState outside component
const [count, setCount] = useState(0); // throws "Invalid hook call" error

// ❌ WRONG: useState inside a condition
const MyComponent = () => {
  if (true) {
    const [count, setCount] = useState(0); // never do this
  }
};

// ❌ WRONG: useState inside a for loop
const MyComponent = () => {
  for (let i = 0; i < 3; i++) {
    const [val, setVal] = useState(0); // never do this
  }
};

// ❌ WRONG: useState inside a nested function
const MyComponent = () => {
  const handleClick = () => {
    const [count, setCount] = useState(0); // never do this
  };
};

// ✅ CORRECT: useState at the top level of the component
const MyComponent = () => {
  const [count, setCount] = useState(0);      // ✅
  const [name, setName] = useState("React");  // ✅

  return <div>{count} - {name}</div>;
};
```

---

### Q3. Install React Router DOM and set up basic routing

```bash
# Install
npm install react-router-dom
```

```javascript
// app.js — full routing setup
import { createBrowserRouter, RouterProvider } from "react-router-dom";
import AppLayout from "./components/AppLayout";
import About from "./components/About";
import Contact from "./components/Contact";
import Error from "./components/Error";
import Body from "./components/Body";

const appRouter = createBrowserRouter([
  {
    path: "/",
    element: <AppLayout />,
    errorElement: <Error />,
    children: [
      { path: "/", element: <Body /> },
      { path: "/about", element: <About /> },
      { path: "/contact", element: <Contact /> },
    ],
  },
]);

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(<RouterProvider router={appRouter} />);
```

---

### Q4. AppLayout with Outlet

```javascript
// components/AppLayout.js
import { Outlet } from "react-router-dom";
import Header from "./Header";

const AppLayout = () => {
  return (
    <div className="app">
      <Header />   {/* Always visible */}
      <Outlet />   {/* Children render here based on the current route */}
    </div>
  );
};

export default AppLayout;
```

---

### Q5. Custom error page using useRouteError

```javascript
// components/Error.js
import { useRouteError } from "react-router-dom";

const Error = () => {
  const err = useRouteError(); // gives you the error object

  return (
    <div>
      <h1>Oops! Something went wrong 😕</h1>
      <h3>
        {err.status}: {err.statusText}
      </h3>
    </div>
  );
};

export default Error;
```

---

### Q6. Link vs anchor tag — showing the difference

```javascript
// ❌ WRONG: anchor tag causes full page reload
import React from "react";

const Header = () => {
  return (
    <nav>
      <a href="/about">About Us</a>      {/* reloads the whole page */}
      <a href="/contact">Contact Us</a>  {/* reloads the whole page */}
    </nav>
  );
};

// ✅ CORRECT: Link navigates without page reload
import { Link } from "react-router-dom";

const Header = () => {
  return (
    <nav>
      <ul>
        <li><Link to="/">Home</Link></li>
        <li><Link to="/about">About Us</Link></li>
        <li><Link to="/contact">Contact Us</Link></li>
      </ul>
    </nav>
  );
};
```

---

### Q7. Dynamic route configuration and useParams

```javascript
// Step 1: Define the dynamic route in app.js
import RestaurantMenu from "./components/RestaurantMenu";

const appRouter = createBrowserRouter([
  {
    path: "/",
    element: <AppLayout />,
    children: [
      { path: "/", element: <Body /> },
      { path: "/restaurant/:resId", element: <RestaurantMenu /> }, // ← dynamic
    ],
  },
]);

// Step 2: Read the dynamic value inside the component
import { useParams } from "react-router-dom";

const RestaurantMenu = () => {
  const { resId } = useParams(); // reads :resId from the URL

  // If URL is /restaurant/229, resId = "229"
  // If URL is /restaurant/425, resId = "425"

  return <h1>Loading menu for restaurant ID: {resId}</h1>;
};
```

---

### Q8. Full RestaurantMenu component with API call

```javascript
// components/RestaurantMenu.js
import { useState, useEffect } from "react";
import { useParams } from "react-router-dom";
import Shimmer from "./Shimmer";

const MENU_API_URL = "https://www.swiggy.com/dapi/menu/pl?page-type=REGULAR_MENU&complete-menu=true&lat=12.9351929&lng=77.6244476&restaurantId=";

const RestaurantMenu = () => {
  const { resId } = useParams();
  const [resInfo, setResInfo] = useState(null);

  useEffect(() => {
    fetchMenu();
  }, []); // empty array: only call once on initial render

  const fetchMenu = async () => {
    const data = await fetch(MENU_API_URL + resId);
    const json = await data.json();
    setResInfo(json.data); // store full API response in state
  };

  // Early return — don't try to render data that hasn't arrived yet
  if (resInfo === null) return <Shimmer />;

  // Destructure info from the deeply nested API response
  const { name, cuisines, costForTwoMessage } =
    resInfo?.cards[0]?.card?.card?.info;

  const itemCards =
    resInfo?.cards[2]?.groupedCard?.cardGroupMap?.REGULAR
    ?.cards[1]?.card?.card?.itemCards;

  return (
    <div className="menu">
      <h1>{name}</h1>
      <h3>Cuisines: {cuisines.join(", ")}</h3>
      <h3>{costForTwoMessage}</h3>

      <h2>Menu</h2>
      <ul>
        {itemCards?.map((item) => (
          <li key={item.card.info.id}>
            {item.card.info.name} — Rs.{" "}
            {(item.card.info.price || item.card.info.defaultPrice) / 100}
          </li>
        ))}
      </ul>
    </div>
  );
};

export default RestaurantMenu;
```

---

### Q9. Making restaurant cards clickable with dynamic Link

```javascript
// Body.js — wrapping each card in a dynamic Link
import { Link } from "react-router-dom";
import RestaurantCard from "./RestaurantCard";

const Body = () => {
  return (
    <div className="rest-container">
      {filteredRestaurants.map((restaurant) => (
        // Key goes on Link (the outermost mapped element), not on RestaurantCard
        <Link
          key={restaurant.data.id}
          to={"/restaurant/" + restaurant.data.id}
        >
          <RestaurantCard resData={restaurant} />
        </Link>
      ))}
    </div>
  );
};
```

---

### Q10. Demonstrating that Link renders as an anchor tag

```javascript
// In your component (for demonstration):
const Header = () => {
  return (
    <nav>
      <Link to="/">Home</Link>      {/* React component */}
    </nav>
  );
};

/* What the browser's HTML inspector shows:
   <nav>
     <a href="/">Home</a>          ← just a regular anchor tag!
   </nav>

   But React Router DOM intercepts the click, prevents the page
   reload, and handles the navigation client-side.
*/
```

---

## 🗒️ Quick Revision Card

| Concept | Key Point |
|---|---|
| useEffect – no dependency array | Runs after **every** render of the component |
| useEffect – empty `[]` | Runs **only once**, on initial render |
| useEffect – `[someVar]` | Runs on initial render + every time `someVar` changes |
| useState outside component | ❌ Invalid hook call error — hooks only inside functional components |
| useState inside if/else | ❌ Creates inconsistencies — React tracks hooks by order |
| useState inside for loop | ❌ Same problem — order can change between renders |
| useState inside nested function | ❌ Not allowed — must be at the top level of the component |
| Best practice for useState | Call all hooks at the very top of your component |
| React Router DOM | NPM library for routing in React apps — install with `npm install react-router-dom` |
| Version | Always use v6 (latest) — v5 has a different API |
| createBrowserRouter | Creates the routing configuration (array of path + element objects) |
| RouterProvider | Provides/activates the routing config in your app — wraps the root render |
| errorElement | Adds a custom error page to a route — shown when the path doesn't match |
| useRouteError | Hook from React Router DOM — gives you the error object (status, statusText) |
| Children routes | Nest routes inside a parent — parent layout (Header) stays, children swap |
| Outlet | Placeholder component — replaced by the matching child route's component |
| Link component | React Router DOM's navigation component — use instead of `<a>` |
| Link vs anchor tag | Anchor tag = full page reload. Link = client-side navigation, no reload |
| Link's `to` prop | Like `href` but for `Link` — e.g., `<Link to="/about">` |
| SPA (Single Page Application) | One HTML file, components swap in and out — no new pages ever fetched |
| Client-side routing | Navigation happens in the browser — no network call for new pages |
| Server-side routing | Navigation makes a network request to fetch a new HTML file from the server |
| Dynamic route | `:resId` in path — one route handles many URLs with different IDs |
| useParams | Hook from React Router DOM — reads the dynamic `:param` values from the URL |
| Early return pattern | `if (data === null) return <Shimmer />;` — prevents errors on first render |
| Price handling | Swiggy sends prices in paise — divide by 100 to get rupees |
| key on Link | When mapping, key goes on the outermost JSX element (Link), not the child |
| Link under the hood | Link renders as a plain `<a>` anchor tag in the browser HTML |
| RAFCE | VS Code shortcut to generate component boilerplate — avoid while learning |
| Speak while you code | Practice explaining what you're writing — impresses interviewers |

---

*Episode 07 – Namaste React Series | Finding the Path*
