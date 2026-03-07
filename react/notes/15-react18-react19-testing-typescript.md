# React — Missing Topics Part 2

> **What the existing 14 notes covered:** All core React concepts, all 14 hooks, patterns
>
> **What this file covers:**
> React 18 New Features · React 19 New Features · Testing with Jest + RTL · TypeScript with React · TanStack Query · Zustand · React Synthetic Event System · Performance: List Virtualization

---

## Table of Contents
1. [React 18 — What Changed?](#1-react-18--what-changed)
2. [React 19 — What's New?](#2-react-19--whats-new)
3. [Testing with Jest + React Testing Library](#3-testing-with-jest--react-testing-library)
4. [TypeScript with React](#4-typescript-with-react)
5. [TanStack Query (React Query)](#5-tanstack-query-react-query)
6. [Zustand — Lightweight State Management](#6-zustand--lightweight-state-management)
7. [React Synthetic Event System](#7-react-synthetic-event-system)
8. [Performance: List Virtualization](#8-performance-list-virtualization)

---

## 1. React 18 — What Changed?

### Interview Question
**"What are the major new features introduced in React 18? What is Automatic Batching?"**

### Answer (Conversational)

React 18 was a landmark release (March 2022) built around **Concurrent React** — the ability for React to prepare multiple versions of the UI simultaneously and interrupt, pause, or restart rendering. Here are the key changes:

---

#### Feature 1: Automatic Batching

**Batching** means React groups multiple `setState` calls into a single re-render instead of re-rendering after each one.

**Before React 18** — batching only worked inside React event handlers:
```jsx
// React 17 — inside click handler: batched (1 re-render) ✓
function handleClick() {
  setCount(c => c + 1); // doesn't re-render yet
  setFlag(f => !f);     // doesn't re-render yet
  // React re-renders once here ✓
}

// React 17 — inside setTimeout/fetch: NOT batched (2 re-renders) ✗
setTimeout(() => {
  setCount(c => c + 1); // re-renders immediately
  setFlag(f => !f);     // re-renders again — 2 total!
}, 1000);
```

**React 18 — Automatic Batching** everywhere:
```jsx
// React 18 — batched everywhere automatically ✓
setTimeout(() => {
  setCount(c => c + 1); // batched
  setFlag(f => !f);     // batched — only 1 re-render!
}, 1000);

// Also batched inside fetch callbacks, Promises, native event handlers
fetch("/api").then(() => {
  setCount(c => c + 1); // batched
  setData(d => newData); // batched — 1 re-render total
});
```

To **opt out** of batching for a specific update, use `flushSync`:
```jsx
import { flushSync } from "react-dom";

flushSync(() => {
  setCount(c => c + 1); // re-renders immediately
});
// DOM is updated here
flushSync(() => {
  setFlag(f => !f); // re-renders immediately again
});
```

---

#### Feature 2: createRoot (New Rendering API)

React 18 changed how you bootstrap the app:
```jsx
// React 17 (old):
import ReactDOM from "react-dom";
ReactDOM.render(<App />, document.getElementById("root"));

// React 18 (new — unlocks all concurrent features):
import { createRoot } from "react-dom/client";
const root = createRoot(document.getElementById("root"));
root.render(<App />);
```

If you use the old `ReactDOM.render` in React 18, everything still works but you get a warning and **none of the concurrent features work**. `createRoot` is required to unlock them.

---

#### Feature 3: Transitions (useTransition + startTransition)

Already covered in file 14, but the key point: React 18 introduced the concept of **urgent vs non-urgent updates**. Typing in a search box is urgent. Filtering 10,000 items based on that input is not. `useTransition` and `startTransition` let you mark the non-urgent work so React can keep the UI responsive.

---

#### Feature 4: Suspense on the Server (Streaming SSR)

React 18 allows **Suspense boundaries to work on the server**, enabling streaming HTML. The server can send HTML in chunks — the parts that are ready go first, while slower parts stream in later. This massively improves Time to First Byte (TTFB) for SSR apps.

---

#### Feature 5: New Hooks

React 18 added: `useId`, `useTransition`, `useDeferredValue`, `useSyncExternalStore`, `useInsertionEffect` (for CSS-in-JS libraries).

### Coding Questions

**Q1:** What is the output — how many re-renders in React 18?
```jsx
function Component() {
  const [a, setA] = useState(0);
  const [b, setB] = useState(0);

  function handleClick() {
    setTimeout(() => {
      setA(1);
      setB(1);
    }, 0);
  }
  // How many times does this component re-render when button is clicked?
}
```

**Answer:** **Once** in React 18 (automatic batching). Would be **twice** in React 17 (no batching inside setTimeout).

---

**Q2:** What do you need to change to upgrade from React 17 to React 18?

**Answer:** Update `ReactDOM.render()` to `createRoot().render()`. That's the minimum required change to unlock all React 18 features.

---

## 2. React 19 — What's New?

### Interview Question
**"What are the major additions in React 19? What is the `use()` hook?"**

### Answer (Conversational)

React 19 (stable December 2024) built on React 18's concurrent foundation and added a set of features aimed at **simplifying data fetching, form handling, and server-client integration**.

---

#### Feature 1: The `use()` Hook

`use()` is a new hook that can **read the value of a Promise or Context inside a component** — and it works conditionally (unlike other hooks).

```jsx
import { use, Suspense } from "react";

// Create a promise outside the component (e.g., from fetch):
const userPromise = fetch("/api/user").then(r => r.json());

function UserProfile() {
  // use() suspends the component until the promise resolves:
  const user = use(userPromise);
  return <h1>Hello, {user.name}</h1>;
}

// Wrap with Suspense to handle loading state:
function App() {
  return (
    <Suspense fallback={<p>Loading...</p>}>
      <UserProfile />
    </Suspense>
  );
}
```

Unlike `useEffect` + `useState` for data fetching, `use()` works with Suspense natively — no loading state variable needed.

**`use()` with Context** (can be conditional — a game changer):
```jsx
function Button({ showTheme }) {
  // Regular useContext cannot be inside an if — use() can!
  if (showTheme) {
    const theme = use(ThemeContext); // ✓ conditional hook usage!
    return <button style={{ color: theme.color }}>Click</button>;
  }
  return <button>Click</button>;
}
```

---

#### Feature 2: Actions & useActionState

React 19 formalizes the concept of **Actions** — async functions that handle form submissions. Instead of managing `isPending`, `error`, and `data` manually, `useActionState` does it for you:

```jsx
import { useActionState } from "react";

async function submitForm(prevState, formData) {
  const name = formData.get("name");
  try {
    await saveUser({ name });
    return { success: true, message: "Saved!" };
  } catch (err) {
    return { success: false, message: err.message };
  }
}

function SignupForm() {
  const [state, formAction, isPending] = useActionState(submitForm, null);

  return (
    <form action={formAction}>
      <input name="name" required />
      <button type="submit" disabled={isPending}>
        {isPending ? "Saving..." : "Save"}
      </button>
      {state?.message && <p>{state.message}</p>}
    </form>
  );
}
```

Notice: `action={formAction}` on the `<form>` — React 19 supports passing async functions directly to form `action`. No `onSubmit` + `preventDefault` needed!

---

#### Feature 3: useOptimistic

`useOptimistic` lets you show an **optimistic (assumed-success) UI update** immediately, then reconcile with the real server response.

Think of it like: "Assume the action will succeed, show the result now, and if it fails, roll back."

```jsx
import { useOptimistic, useState } from "react";

function MessageList({ messages }) {
  const [optimisticMessages, addOptimisticMessage] = useOptimistic(
    messages,
    (currentMessages, newText) => [
      ...currentMessages,
      { text: newText, sending: true } // mark as "sending"
    ]
  );

  async function sendMessage(formData) {
    const text = formData.get("message");
    // Show immediately (optimistic):
    addOptimisticMessage(text);
    // Actually send (may fail):
    await sendToServer(text);
    // If it fails, React automatically reverts the optimistic state
  }

  return (
    <>
      {optimisticMessages.map((msg, i) => (
        <p key={i} style={{ opacity: msg.sending ? 0.5 : 1 }}>
          {msg.text}
        </p>
      ))}
      <form action={sendMessage}>
        <input name="message" />
        <button type="submit">Send</button>
      </form>
    </>
  );
}
```

---

#### Feature 4: useFormStatus

`useFormStatus` gives a child component information about the **form it's inside** — without prop drilling. Very useful for submit buttons inside complex forms:

```jsx
import { useFormStatus } from "react-dom";

function SubmitButton() {
  const { pending } = useFormStatus(); // knows if parent form is submitting!

  return (
    <button disabled={pending}>
      {pending ? "Submitting..." : "Submit"}
    </button>
  );
}

function MyForm() {
  return (
    <form action={serverAction}>
      <input name="email" />
      <SubmitButton /> {/* No props needed! */}
    </form>
  );
}
```

---

#### Feature 5: Server Components (RSC)

React Server Components run **exclusively on the server** — they can directly access databases, file systems, and APIs without shipping that code to the browser.

```jsx
// This component runs on the server ONLY — never sent to browser:
async function ProductList() {
  // Direct database access — no fetch, no API route needed!
  const products = await db.query("SELECT * FROM products");

  return (
    <ul>
      {products.map(p => <li key={p.id}>{p.name}</li>)}
    </ul>
  );
}
```

**Key rules:**
- Server components can `async/await` directly
- Server components **cannot** use hooks (useState, useEffect, etc.)
- Server components **cannot** use browser APIs
- Server components reduce JS bundle size — they're not included in client JS

**Client components** use the `"use client"` directive at the top:
```jsx
"use client"; // marks this as a client component

function Counter() {
  const [count, setCount] = useState(0); // hooks work here
  return <button onClick={() => setCount(c => c + 1)}>{count}</button>;
}
```

> **Note:** Server Components are available today through **Next.js 13+ App Router**, which is the primary way most developers use them.

### Coding Questions

**Q1:** What's the difference between `useActionState` and `useTransition`?

**Answer:** `useTransition` marks synchronous state updates as non-urgent. `useActionState` is specifically for **async actions** (like form submissions) — it manages the `isPending`, result state, and form action wiring automatically. They solve different problems: transition = UI responsiveness; actionState = form/async action lifecycle.

---

**Q2:** Can you use `useState` inside a Server Component?

**Answer:** No. Server Components have no lifecycle, no hooks, and run only on the server. If you need interactivity (hooks, event handlers), mark the component with `"use client"`.

---

## 3. Testing with Jest + React Testing Library

### Interview Question
**"How do you test React components? What is React Testing Library and what's its philosophy?"**

### Answer (Conversational)

**React Testing Library (RTL)** is the standard library for testing React components. Its philosophy is: **"Test the way users use your app"** — not implementation details. You test what renders on screen, not internal state or method calls.

**Jest** is the test runner — it runs your test files, provides `expect`, `describe`, `it`, mocking, and code coverage.

---

#### Setting Up

```bash
# Create React App includes these by default
# For Vite or custom setups:
npm install --save-dev jest @testing-library/react @testing-library/jest-dom @testing-library/user-event
```

---

#### The Core API: render, screen, userEvent

```jsx
// Button.test.jsx
import { render, screen } from "@testing-library/react";
import userEvent from "@testing-library/user-event";
import Button from "./Button";

test("renders a button with the correct label", () => {
  render(<Button label="Click me" />);

  // screen.getBy* — throws if not found (for elements that should exist)
  const button = screen.getByRole("button", { name: /click me/i });
  expect(button).toBeInTheDocument();
});

test("calls onClick when clicked", async () => {
  const handleClick = jest.fn(); // mock function
  render(<Button label="Submit" onClick={handleClick} />);

  await userEvent.click(screen.getByRole("button"));
  expect(handleClick).toHaveBeenCalledTimes(1);
});
```

---

#### Query Types — Priority Order

RTL gives you several query methods. Use them in this priority order (most accessible-friendly first):

```jsx
// 1. getByRole — preferred! Matches by ARIA role
screen.getByRole("button", { name: /submit/i });
screen.getByRole("textbox", { name: /email/i });
screen.getByRole("heading", { name: /welcome/i });

// 2. getByLabelText — for form inputs linked to labels
screen.getByLabelText("Email address");

// 3. getByPlaceholderText — for inputs with placeholder
screen.getByPlaceholderText("Enter your email");

// 4. getByText — for non-interactive elements
screen.getByText("Welcome back!");

// 5. getByTestId — last resort (add data-testid to element)
screen.getByTestId("custom-element");
```

#### Query Variants: get vs query vs find

```jsx
// getBy* — throws if NOT found. Use when element MUST exist.
screen.getByRole("button");

// queryBy* — returns null if not found. Use when element MIGHT not exist.
expect(screen.queryByText("Error")).not.toBeInTheDocument();

// findBy* — returns Promise, waits for element to appear. Use for async.
const button = await screen.findByRole("button"); // waits up to 1000ms
```

---

#### Testing Async Components

```jsx
// Component that fetches data:
function UserCard({ userId }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    fetch(`/api/users/${userId}`)
      .then(r => r.json())
      .then(setUser);
  }, [userId]);

  if (!user) return <p>Loading...</p>;
  return <p>{user.name}</p>;
}

// Test — mock the fetch and wait for the result:
import { render, screen } from "@testing-library/react";

beforeEach(() => {
  global.fetch = jest.fn(() =>
    Promise.resolve({
      json: () => Promise.resolve({ name: "Alice" })
    })
  );
});

test("displays user name after loading", async () => {
  render(<UserCard userId={1} />);

  // Initially shows loading:
  expect(screen.getByText("Loading...")).toBeInTheDocument();

  // Wait for the user name to appear:
  const name = await screen.findByText("Alice");
  expect(name).toBeInTheDocument();
});
```

---

#### Testing User Interactions

```jsx
test("filters list when user types in search", async () => {
  render(<SearchableList items={["Apple", "Banana", "Apricot"]} />);

  const input = screen.getByRole("textbox");
  await userEvent.type(input, "ap");

  // Only "Apple" and "Apricot" should remain:
  expect(screen.getByText("Apple")).toBeInTheDocument();
  expect(screen.getByText("Apricot")).toBeInTheDocument();
  expect(screen.queryByText("Banana")).not.toBeInTheDocument();
});
```

---

#### Testing Custom Hooks with renderHook

```jsx
import { renderHook, act } from "@testing-library/react";
import useCounter from "./useCounter";

test("increments counter", () => {
  const { result } = renderHook(() => useCounter());

  expect(result.current.count).toBe(0);

  act(() => {
    result.current.increment();
  });

  expect(result.current.count).toBe(1);
});
```

---

#### RTL Philosophy vs Enzyme

| | React Testing Library | Enzyme (old) |
|---|---|---|
| Tests | What the user sees | Component internals |
| Queries | By role, text, label | By component name, state |
| Encourages | Accessible HTML | Testing implementation details |
| Status | Industry standard | Deprecated for React 17+ |

### Coding Questions

**Q1:** What's wrong with this test?
```jsx
test("counter increments", () => {
  const { result } = renderHook(() => useState(0));
  result.current[1](5); // call setState directly
  expect(result.current[0]).toBe(5); // check state
});
```

**Answer:** State updates in hooks must be wrapped in `act()`. Without it, React may not process the update synchronously. Use `act(() => { result.current[1](5); })`.

---

**Q2:** Which query should you use to find a submit button?

**Answer:** `screen.getByRole("button", { name: /submit/i })` — using role is the most accessible and RTL-recommended approach.

---

## 4. TypeScript with React

### Interview Question
**"How do you use TypeScript with React? Show how to type props, state, events, and refs."**

### Answer (Conversational)

TypeScript adds **static type checking** to React — catching type errors at compile time instead of runtime. It's the industry standard in professional React development.

---

#### Typing Props

```tsx
// Define the props interface:
interface ButtonProps {
  label: string;
  onClick: () => void;
  disabled?: boolean;      // optional prop
  variant?: "primary" | "secondary"; // union type
  children?: React.ReactNode; // anything that can be rendered
}

// Use it:
function Button({ label, onClick, disabled = false, variant = "primary" }: ButtonProps) {
  return (
    <button
      onClick={onClick}
      disabled={disabled}
      className={`btn btn-${variant}`}
    >
      {label}
    </button>
  );
}

// React.FC alternative (older style — less preferred now):
const Button: React.FC<ButtonProps> = ({ label, onClick }) => {
  return <button onClick={onClick}>{label}</button>;
};
```

---

#### Typing useState

```tsx
// TypeScript infers simple types automatically:
const [count, setCount] = useState(0);       // number
const [name, setName] = useState("");         // string
const [active, setActive] = useState(false); // boolean

// For complex types or when initial value is null/undefined:
interface User {
  id: number;
  name: string;
  email: string;
}

const [user, setUser] = useState<User | null>(null);
// Now TypeScript knows user can be User or null

// Array state:
const [items, setItems] = useState<string[]>([]);
const [users, setUsers] = useState<User[]>([]);
```

---

#### Typing useRef

```tsx
// Ref to a DOM element — use the specific HTML element type:
const inputRef = useRef<HTMLInputElement>(null);
const divRef = useRef<HTMLDivElement>(null);
const buttonRef = useRef<HTMLButtonElement>(null);

function MyForm() {
  const inputRef = useRef<HTMLInputElement>(null);

  function focusInput() {
    inputRef.current?.focus(); // optional chaining because current can be null
  }

  return <input ref={inputRef} />;
}

// Ref to store a mutable value (not a DOM element) — no null:
const intervalRef = useRef<ReturnType<typeof setInterval> | null>(null);
const counterRef = useRef<number>(0);
```

---

#### Typing Event Handlers

```tsx
// The event type follows the pattern: React.[EventName]Event<HTMLElement>
function MyInput() {
  function handleChange(e: React.ChangeEvent<HTMLInputElement>) {
    console.log(e.target.value); // TypeScript knows .value exists
  }

  function handleSubmit(e: React.FormEvent<HTMLFormElement>) {
    e.preventDefault();
  }

  function handleClick(e: React.MouseEvent<HTMLButtonElement>) {
    console.log(e.currentTarget); // the button
  }

  function handleKeyDown(e: React.KeyboardEvent<HTMLInputElement>) {
    if (e.key === "Enter") {
      console.log("Enter pressed");
    }
  }

  return (
    <form onSubmit={handleSubmit}>
      <input onChange={handleChange} onKeyDown={handleKeyDown} />
      <button onClick={handleClick}>Submit</button>
    </form>
  );
}
```

---

#### Typing useReducer

```tsx
interface State {
  count: number;
  error: string | null;
}

type Action =
  | { type: "INCREMENT" }
  | { type: "DECREMENT" }
  | { type: "SET_ERROR"; payload: string }; // discriminated union

function reducer(state: State, action: Action): State {
  switch (action.type) {
    case "INCREMENT":
      return { ...state, count: state.count + 1 };
    case "DECREMENT":
      return { ...state, count: state.count - 1 };
    case "SET_ERROR":
      return { ...state, error: action.payload }; // TypeScript knows payload exists
    default:
      return state;
  }
}
```

---

#### Typing Context

```tsx
interface ThemeContextType {
  theme: "light" | "dark";
  toggleTheme: () => void;
}

// createContext needs a type parameter — provide a default or use assertion:
const ThemeContext = createContext<ThemeContextType | undefined>(undefined);

// Safe custom hook that throws if used outside provider:
function useTheme(): ThemeContextType {
  const context = useContext(ThemeContext);
  if (!context) {
    throw new Error("useTheme must be used within a ThemeProvider");
  }
  return context;
}
```

---

#### Generic Components

```tsx
// A List component that works with ANY type of data:
interface ListProps<T> {
  items: T[];
  renderItem: (item: T) => React.ReactNode;
  keyExtractor: (item: T) => string;
}

function List<T>({ items, renderItem, keyExtractor }: ListProps<T>) {
  return (
    <ul>
      {items.map(item => (
        <li key={keyExtractor(item)}>{renderItem(item)}</li>
      ))}
    </ul>
  );
}

// Usage — TypeScript infers T automatically:
<List
  items={[{ id: 1, name: "Alice" }, { id: 2, name: "Bob" }]}
  keyExtractor={(user) => String(user.id)}  // TypeScript knows user has .id
  renderItem={(user) => <span>{user.name}</span>}
/>
```

### Coding Questions

**Q1:** What TypeScript type should you use for a prop that accepts anything React can render (string, number, JSX, arrays, etc.)?

**Answer:** `React.ReactNode` — the most permissive type for renderable content.

---

**Q2:** What's the difference between `React.FC<Props>` and just typing props inline?

**Answer:** `React.FC<Props>` is the older style — it implicitly adds `children` to props (which is now considered a bad default) and adds some return type checking. Modern React + TypeScript style is to just type the destructured props inline: `function MyComp({ name }: { name: string })`. Explicit is better.

---

## 5. TanStack Query (React Query)

### Interview Question
**"What is TanStack Query? What problems does it solve that useEffect + useState don't?"**

### Answer (Conversational)

**TanStack Query** (formerly React Query) is a library for **server state management** — fetching, caching, syncing, and updating data from APIs. It solves the repetitive, error-prone pattern of `useEffect` + `useState` for data fetching.

```bash
npm install @tanstack/react-query
```

---

#### The Problem with useEffect + useState for Data Fetching

Every data fetch with raw hooks means writing the same boilerplate:

```jsx
// Every. Single. Time. ↓
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    setLoading(true);
    fetch(`/api/users/${userId}`)
      .then(r => r.json())
      .then(data => { setUser(data); setLoading(false); })
      .catch(err => { setError(err); setLoading(false); });
  }, [userId]);

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error.message}</p>;
  return <p>{user.name}</p>;
}
```

No caching. No deduplication. No background refetching. No retry on failure. You build all of this yourself every time.

---

#### TanStack Query — Setup

```jsx
// main.jsx — wrap app with QueryClientProvider once:
import { QueryClient, QueryClientProvider } from "@tanstack/react-query";

const queryClient = new QueryClient();

function App() {
  return (
    <QueryClientProvider client={queryClient}>
      <Router />
    </QueryClientProvider>
  );
}
```

---

#### useQuery — Fetching Data

```jsx
import { useQuery } from "@tanstack/react-query";

function UserProfile({ userId }) {
  const { data: user, isLoading, error, isError } = useQuery({
    queryKey: ["user", userId],  // cache key — unique identifier for this data
    queryFn: () => fetch(`/api/users/${userId}`).then(r => r.json()),
  });

  if (isLoading) return <p>Loading...</p>;
  if (isError) return <p>Error: {error.message}</p>;
  return <p>{user.name}</p>;
}
```

That's it. No `useState`, no `useEffect`. And you get for free:
- **Caching** — same `queryKey` data is cached and shared across components
- **Deduplication** — two components with the same query key don't make two requests
- **Background refetching** — when user focuses the tab, data refreshes automatically
- **Retry on failure** — failed requests are retried 3 times by default
- **Stale-while-revalidate** — shows cached data immediately, fetches fresh data in background

---

#### queryKey — The Cache Key

```jsx
// Different queryKeys = different cache entries:
useQuery({ queryKey: ["users"], ... })            // all users
useQuery({ queryKey: ["user", 1], ... })          // user with id 1
useQuery({ queryKey: ["user", 1, "posts"], ... }) // posts for user 1

// When userId changes, React Query automatically refetches:
useQuery({ queryKey: ["user", userId], queryFn: fetchUser });
```

---

#### useMutation — Creating / Updating / Deleting Data

```jsx
import { useMutation, useQueryClient } from "@tanstack/react-query";

function AddTodo() {
  const queryClient = useQueryClient();

  const mutation = useMutation({
    mutationFn: (newTodo) => fetch("/api/todos", {
      method: "POST",
      body: JSON.stringify(newTodo),
    }).then(r => r.json()),

    // After success — invalidate the todos cache to refetch:
    onSuccess: () => {
      queryClient.invalidateQueries({ queryKey: ["todos"] });
    },
  });

  return (
    <button
      onClick={() => mutation.mutate({ title: "New Todo" })}
      disabled={mutation.isPending}
    >
      {mutation.isPending ? "Adding..." : "Add Todo"}
    </button>
  );
}
```

---

#### Key TanStack Query Concepts

| Concept | Meaning |
|---|---|
| `queryKey` | Unique identifier for cached data |
| `queryFn` | Function that fetches data (must return Promise) |
| `staleTime` | How long data is considered fresh (default: 0) |
| `gcTime` | How long unused cache is kept (default: 5 min) |
| `isLoading` | True on first fetch with no cached data |
| `isFetching` | True anytime a fetch is in progress (including background) |
| `refetchOnWindowFocus` | Auto-refetch when user returns to tab (default: true) |

---

#### TanStack Query vs Redux for Server State

| | TanStack Query | Redux |
|---|---|---|
| Purpose | Server state (API data) | Client/UI state |
| Cache built-in | Yes | No |
| Background sync | Yes | No |
| Boilerplate | Minimal | More |
| Best for | User data, API resources | Cart, UI state, auth |

> **The golden rule:** Use TanStack Query for data that comes from a server. Use Redux/Zustand for client-side UI state.

---

## 6. Zustand — Lightweight State Management

### Interview Question
**"What is Zustand? How does it compare to Redux?"**

### Answer (Conversational)

**Zustand** (German for "state") is a small, fast, unopinionated state management library. Where Redux requires actions, reducers, slices, and a store setup with multiple files, Zustand does the same thing in one simple function.

```bash
npm install zustand
```

---

#### Creating a Store

```jsx
import { create } from "zustand";

// One function — that's the entire store setup:
const useCartStore = create((set, get) => ({
  // State:
  items: [],
  totalItems: 0,

  // Actions (methods that update state):
  addItem: (item) => set((state) => ({
    items: [...state.items, item],
    totalItems: state.totalItems + 1,
  })),

  removeItem: (itemId) => set((state) => ({
    items: state.items.filter(i => i.id !== itemId),
    totalItems: state.totalItems - 1,
  })),

  clearCart: () => set({ items: [], totalItems: 0 }),

  // Derived/computed value using get():
  getTotal: () => get().items.reduce((sum, item) => sum + item.price, 0),
}));
```

---

#### Using the Store in Components

```jsx
// Any component — no Provider needed! (Unlike Redux and Context)
function CartIcon() {
  const totalItems = useCartStore((state) => state.totalItems);
  return <span>{totalItems} items</span>;
}

function Cart() {
  const { items, removeItem, clearCart } = useCartStore((state) => ({
    items: state.items,
    removeItem: state.removeItem,
    clearCart: state.clearCart,
  }));

  return (
    <div>
      {items.map(item => (
        <div key={item.id}>
          {item.name}
          <button onClick={() => removeItem(item.id)}>Remove</button>
        </div>
      ))}
      <button onClick={clearCart}>Clear Cart</button>
    </div>
  );
}
```

---

#### Selector for Performance

Use a **selector function** to subscribe to only the slice of state your component needs. The component only re-renders when that specific slice changes:

```jsx
// ✓ Only re-renders when totalItems changes:
const totalItems = useCartStore((state) => state.totalItems);

// ✗ Re-renders whenever ANYTHING in the store changes:
const store = useCartStore();
```

---

#### Zustand vs Redux Toolkit Comparison

| | Zustand | Redux Toolkit |
|---|---|---|
| Setup lines | ~10 | ~50+ (slice + store + provider) |
| Provider required | No | Yes |
| Boilerplate | Minimal | Moderate |
| DevTools | Plugin available | Excellent built-in |
| Learning curve | Low | Medium |
| When to use | Small-medium apps | Large apps with complex state |
| Async actions | Just `async/await` in actions | Need thunks or RTK Query |

---

#### Async Actions in Zustand

```jsx
const useUserStore = create((set) => ({
  user: null,
  loading: false,
  error: null,

  fetchUser: async (id) => {
    set({ loading: true, error: null });
    try {
      const user = await fetch(`/api/users/${id}`).then(r => r.json());
      set({ user, loading: false });
    } catch (err) {
      set({ error: err.message, loading: false });
    }
  },
}));

// In a component:
function Profile({ userId }) {
  const { user, loading, fetchUser } = useUserStore();
  useEffect(() => { fetchUser(userId); }, [userId]);

  if (loading) return <p>Loading...</p>;
  return <p>{user?.name}</p>;
}
```

---

## 7. React Synthetic Event System

### Interview Question
**"How does React handle events? What is a SyntheticEvent?"**

### Answer (Conversational)

React doesn't attach event listeners directly to individual DOM elements the way you'd expect. Instead, React uses a single event listener at the **root of the React tree** and handles all events there. This is event delegation at the framework level.

---

#### What is a SyntheticEvent?

When you handle an event in React (like `onClick`), the handler receives a **SyntheticEvent** — not a native browser event. It's a wrapper around the native event that:

- Has the **same interface** as a native event (`e.target`, `e.preventDefault()`, `e.stopPropagation()`, etc.)
- Is **cross-browser consistent** — React normalizes differences between browsers
- Used to be **pooled** (reused for performance) in React 16 and earlier

```jsx
function MyInput() {
  function handleChange(e) {
    // e is a SyntheticEvent
    console.log(e.type);          // "change"
    console.log(e.target.value);  // the input value
    console.log(e.nativeEvent);   // the actual browser event underneath
    e.preventDefault();           // works exactly like native
    e.stopPropagation();          // works exactly like native
  }

  return <input onChange={handleChange} />;
}
```

---

#### Event Delegation — How React Attaches Events

React doesn't do this:
```html
<!-- NOT what React does: -->
<button onclick="handleClick()">Click</button>
```

React does this internally:
```javascript
// React attaches ONE listener to the root:
document.getElementById("root").addEventListener("click", reactEventHandler);

// Then reactEventHandler figures out which React component to notify
```

This is why:
- React is efficient with many event handlers — just one listener on the root
- Events in Portals still bubble through the **React component tree** (not DOM tree)

---

#### React 17 Change: Events Now on Root, Not Document

Before React 17, React attached events to `document`. In React 17+, they're attached to the **root DOM container** (`#root`). This allows multiple versions of React to coexist on one page (important for gradual migration) and makes `e.stopPropagation()` work predictably.

---

#### Common Event Types and Their React Names

| DOM Event | React Event Prop |
|---|---|
| `click` | `onClick` |
| `change` | `onChange` |
| `submit` | `onSubmit` |
| `keydown` | `onKeyDown` |
| `keyup` | `onKeyUp` |
| `keypress` | `onKeyPress` (deprecated) |
| `focus` | `onFocus` |
| `blur` | `onBlur` |
| `mouseenter` | `onMouseEnter` |
| `mouseleave` | `onMouseLeave` |
| `scroll` | `onScroll` |
| `input` | `onChange` (React uses onChange for inputs, not oninput) |

---

#### Event Pooling (Historical Knowledge)

In React 16 and earlier, SyntheticEvents were **pooled** — the event object was reused after the handler finished, resetting all properties to null. This caused bugs when accessing events asynchronously:

```jsx
// React 16 — this was a bug!
function handleChange(e) {
  setTimeout(() => {
    console.log(e.target.value); // null! Event was pooled and reset
  }, 100);
}

// React 16 fix: call e.persist() to keep the event alive
function handleChange(e) {
  e.persist();
  setTimeout(() => {
    console.log(e.target.value); // works!
  }, 100);
}
```

**React 17 removed event pooling** entirely. Events are no longer reused. `e.persist()` still exists but does nothing. You don't need to worry about this in modern React.

### Coding Questions

**Q1:** What's the difference between `e.stopPropagation()` and `e.preventDefault()`?

**Answer:**
- `e.stopPropagation()` — stops the event from **bubbling up** to parent elements
- `e.preventDefault()` — stops the **browser's default behavior** (like a form submitting or a link navigating)
- They're independent — you can call one, both, or neither

---

**Q2:** Why does React use `onChange` for inputs instead of the native `oninput`?

**Answer:** React's `onChange` actually fires on every keystroke (like native `input` event), not just when the element loses focus (like native `change` event). React normalized the behavior to be more intuitive — the name `onChange` is misleading but it fires continuously while typing.

---

## 8. Performance: List Virtualization

### Interview Question
**"What is list virtualization? Why do you need it and how do you implement it in React?"**

### Answer (Conversational)

**List virtualization** (also called "windowing") is a technique where you only render the DOM nodes that are **currently visible in the viewport** — not all items in a list. For a list of 10,000 items, you might only render 20 at any given time.

---

#### The Problem — Rendering 10,000 Items

```jsx
// This creates 10,000 DOM nodes — very slow!
function HugeList({ items }) {
  return (
    <ul>
      {items.map(item => (
        <li key={item.id}>
          <UserCard user={item} /> {/* Complex component */}
        </li>
      ))}
    </ul>
  );
}
```

With 10,000 items:
- Initial render is slow (creating 10,000 DOM nodes)
- Scrolling is janky (browser keeps all nodes in memory)
- Memory usage is huge

---

#### The Solution — react-window

```bash
npm install react-window
```

```jsx
import { FixedSizeList } from "react-window";

function VirtualList({ items }) {
  const Row = ({ index, style }) => (
    // style has the absolute positioning to place the row correctly:
    <div style={style}>
      <UserCard user={items[index]} />
    </div>
  );

  return (
    <FixedSizeList
      height={600}       // height of the visible window
      width="100%"       // width
      itemCount={items.length}  // total number of items
      itemSize={80}      // height of each row in px
    >
      {Row}
    </FixedSizeList>
  );
}
```

Now instead of 10,000 DOM nodes, only ~10 rows are ever in the DOM at a time. As the user scrolls, rows are recycled.

---

#### When to Virtualize

- Lists with **50+ items** where each item has complex rendering
- Tables with hundreds of rows
- Infinite scroll feeds
- Any list where you notice jank while scrolling

**Don't virtualize everything** — for short lists (< 50 items), virtualization adds complexity without noticeable benefit.

---

## Summary — The Complete React Interview Coverage Map

### What was in the original 13 notes files (Episodes 1–13):
useState, useEffect, useContext, useMemo, useCallback, useRef, custom hooks, React Router, Redux Toolkit, lazy/Suspense for code splitting, class components + lifecycle, Tailwind CSS, HOC, Context API, Virtual DOM, Fiber, reconciliation, props drilling

### What was added in file 14 (Missing Hooks & Patterns):
useReducer, useLayoutEffect, useId, useTransition, useDeferredValue, forwardRef, useImperativeHandle, React.memo, Error Boundaries, Portals, Render Props, Compound Components, hooks comparison tables

### What this file adds (Part 2):
React 18 (Automatic Batching, createRoot, Concurrent Mode, Streaming SSR), React 19 (use(), useActionState, useOptimistic, useFormStatus, Server Components), Testing (Jest + RTL, queries, async testing, renderHook), TypeScript with React (props, state, events, refs, generics), TanStack Query (useQuery, useMutation, cache invalidation), Zustand (store creation, selectors, async actions), Synthetic Event System (delegation, SyntheticEvent, pooling history), List Virtualization (react-window)
