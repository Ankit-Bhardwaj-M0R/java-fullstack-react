# Episode 12 – Let's Build Our Store
### React Interview Prep + Coding Questions

---

## Table of Contents

1. [Redux is Not Mandatory – Use It Wisely](#1-redux-is-not-mandatory)
2. [Redux vs React – They Are Not the Same Thing](#2-redux-vs-react)
3. [Redux Toolkit (RTK) – The Modern Way to Write Redux](#3-redux-toolkit)
4. [The Redux Store – A Big Global Object](#4-the-redux-store)
5. [Slices – Logical Partitions Inside the Store](#5-slices)
6. [The Full Redux Flow – Dispatch → Action → Reducer → Slice](#6-the-full-redux-flow)
7. [Selectors – How to Read from the Store (Subscribing)](#7-selectors)
8. [Step-by-Step: Setting Up Redux in Your App](#8-setting-up-redux)
9. [Creating a Cart Slice with createSlice](#9-creating-a-cart-slice)
10. [Connecting the Store to the App with Provider](#10-connecting-the-store)
11. [Reading from the Store with useSelector](#11-reading-with-useselector)
12. [Writing to the Store with useDispatch](#12-writing-with-usedispatch)
13. [Building the Cart Page](#13-building-the-cart-page)
14. [Mutating State in RTK – The Immer Library Behind the Scenes](#14-mutating-state-and-immer)
15. [A Critical Performance Warning – Don't Over-Subscribe](#15-performance-warning)
16. [reducer vs reducers – A Common Point of Confusion](#16-reducer-vs-reducers)
17. [Redux DevTools – Time-Travel Debugging](#17-redux-devtools)
18. [RTK Query and Older Redux Concepts (Thunk, Middleware)](#18-rtk-query-and-thunk)
19. [Coding Questions](#19-coding-questions)

---

## 1. Redux is Not Mandatory

### 🎤 Interview Question:
**"Is Redux mandatory in a React application? When should you use it?"**

### 💬 Conversational Answer:

No. Redux is not mandatory. This is one of the biggest misconceptions in the industry.

Think about it this way — this Namaste React series built a full food ordering app across 11 episodes without using Redux once. It still worked. That's proof that Redux is optional.

**When you do NOT need Redux:**
- Small apps with just a few components
- Medium-sized apps where React Context handles your data sharing needs well

**When Redux starts to make sense:**
- Large-scale applications where data is heavily shared across many components
- Apps with a lot of read/write operations on shared data
- Applications where managing the data layer with just props and context becomes painful

The key rule: **use Redux only when you actually need it.** A lot of developers — and even companies — make the mistake of reaching for Redux on day one of a new project, without thinking whether the complexity it adds is justified. Don't be that developer.

> ⚠️ Also worth knowing: Redux is not the only state management library. **Zustand** is another library gaining a lot of popularity right now — it's lightweight and developer-friendly. It's worth checking out on your own.

---

## 2. Redux vs React – They Are Not the Same Thing

### 🎤 Interview Question:
**"Is Redux part of React? What is the relationship between them?"**

### 💬 Conversational Answer:

Redux is a completely **separate library**. It is not part of React. You install it separately. You import it separately. React exists without Redux — and Redux actually works with other frameworks and libraries too, not just React.

The reason people confuse the two is simply that React + Redux is an extremely popular combination. A lot of companies use them together, so in casual conversation, people blur the lines.

The Redux team actually provides **two libraries** to use when combining Redux with React:

1. **`@reduxjs/toolkit`** (Redux Toolkit / RTK) — the Redux library itself, now with a simplified modern API for creating stores, slices, and reducers
2. **`react-redux`** — the bridge library that connects Redux to your React app; it gives you hooks like `useSelector` and `useDispatch`, and a `<Provider>` component to wrap your app

Think of it this way: RTK is the engine. React Redux is the adapter that plugs that engine into a React car.

---

## 3. Redux Toolkit (RTK) – The Modern Way to Write Redux

### 🎤 Interview Question:
**"What is Redux Toolkit? How is it different from older 'vanilla' Redux?"**

### 💬 Conversational Answer:

Redux Toolkit (RTK) is the **current standard way to write Redux**. It was created specifically to solve three major problems that developers had with older Redux (often called "vanilla Redux"):

**Problem 1 — Too complicated:** Vanilla Redux had a huge learning curve. You had to understand a lot of concepts and strict rules before you could do anything useful.

**Problem 2 — Too many extra packages:** To get Redux to do practical things in older versions, you had to install and wire together several different packages. RTK bundles those capabilities in.

**Problem 3 — Too much boilerplate:** Vanilla Redux required a lot of repetitive code. So much that developers used to copy-paste Redux setup code from old projects just to get started. RTK dramatically reduces that.

If you're building a project today and need Redux, always use RTK. Vanilla Redux tutorials still exist all over the internet, but they're teaching the old way — don't follow them.

---

## 4. The Redux Store – A Big Global Object

### 🎤 Interview Question:
**"What is a Redux Store?"**

### 💬 Conversational Answer:

The Redux Store is essentially a **big JavaScript object** kept in a central, global place in your app. Every component in your React application can access it — they can read from it and write to it.

The store holds the major data of your application. Think of it as the single source of truth for your app's data layer.

Instead of passing data from parent to child to grandchild through props, you put shared data in the store and let any component access it directly.

---

## 5. Slices – Logical Partitions Inside the Store

### 🎤 Interview Question:
**"What is a Redux slice? Why do we use slices?"**

### 💬 Conversational Answer:

A slice is a **small, logical portion of the Redux Store**.

The store is one big object, which is fine. But to keep it organized, we divide it into named logical sections called slices. Each slice is responsible for one concern in your app.

For a food ordering app, you might have:

- A **cart slice** — holds items currently in the cart
- A **user slice** — holds logged-in user information
- A **theme slice** — holds UI preferences like dark/light mode

Each slice manages its own piece of state. When you update the cart, only the cart slice changes. The user slice and theme slice are untouched. This separation keeps your store manageable as your app grows.

---

## 6. The Full Redux Flow – Dispatch → Action → Reducer → Slice

### 🎤 Interview Question:
**"How does data flow in Redux? Explain the relationship between actions, reducers, dispatch, and the store."**

### 💬 Conversational Answer:

This is **the most important thing to understand about Redux**. Once this clicks, writing code is easy.

Here's the full flow:

> **When you click the "Add" button → it dispatches an action → which calls a reducer function → which updates the slice of the store → and because the component is subscribed to the store using a selector, it automatically re-renders.**

Let's break down each term:

**Dispatch** — the act of firing off a signal that something happened. "I want to add an item." You *dispatch* an action to make that happen.

**Action** — a description of *what* you want to do. In RTK, each action has a name (like `addItem`, `removeItem`, `clearCart`). Think of actions like small internal APIs that the reducer responds to.

**Reducer function** — the function that actually *does the work* of updating the state. When an action is dispatched, the matching reducer function runs and modifies the slice data.

**Selector** — how a component *reads* from the store. A component "subscribes" to a specific portion of the store using a selector. Whenever that portion of the store changes, the component automatically re-renders with the new data.

Draw this diagram and memorise the flow — it will make every Redux code example immediately understandable.

```
[User clicks Add button]
        ↓
  dispatch(addItem(item))
        ↓
  reducer function runs → modifies cart slice
        ↓
  Header component (subscribed via selector) → auto re-renders
```

---

## 7. Selectors – How to Read from the Store (Subscribing)

### 🎤 Interview Question:
**"What is a selector in Redux? What does it mean to 'subscribe to the store'?"**

### 💬 Conversational Answer:

A **selector** is the mechanism by which a component reads data from the Redux Store. In React with RTK, you use the `useSelector` hook (from the `react-redux` library) to do this.

When you use `useSelector`, your component is said to be **subscribed** to the store. That means: whenever the data your selector is watching changes, your component automatically re-renders to reflect the new value. It's always in sync.

The key thing to understand about selectors is that you should always subscribe to the **smallest portion of the store you actually need**:

```javascript
// ✅ Good — subscribe only to the items you care about
const cartItems = useSelector((store) => store.cart.items);

// ❌ Bad — subscribes to the ENTIRE store
const store = useSelector((store) => store);
const cartItems = store.cart.items;
```

Both produce the same result. But the second version is a **performance problem** — your component will re-render whenever *anything* changes in the entire store, even things completely unrelated to the cart (like a user logging in). Always be precise with your selectors.

---

## 8. Step-by-Step: Setting Up Redux in Your App

### 🎤 Interview Question:
**"What are the steps to add Redux Toolkit to a React app?"**

### 💬 Conversational Answer:

There are five clear steps:

1. **Install the two required libraries**
2. **Create the app store** using `configureStore`
3. **Provide the store to your app** using `<Provider>`
4. **Create slices** for each piece of data you want to manage
5. **Read and write** using `useSelector` and `useDispatch`

Let's walk through each step in code.

---

## 9. Creating a Cart Slice with createSlice

### 🎤 Interview Question:
**"How do you create a Redux slice? Show a real example with a cart slice."**

### 💬 Conversational Answer:

A slice is created with the `createSlice` function from `@reduxjs/toolkit`. You give it:
- A **name** (the slice's identifier)
- An **initialState** (what the data looks like before anything happens)
- **Reducers** — an object where each key is an action name and the value is the reducer function that handles it

**The cart slice:**

```javascript
// utils/cartSlice.js
import { createSlice } from "@reduxjs/toolkit";

const cartSlice = createSlice({
  name: "cart",

  initialState: {
    items: [], // cart starts empty
  },

  reducers: {
    // Reducer function for the "addItem" action
    // state = current slice state, action = what was dispatched
    addItem: (state, action) => {
      state.items.push(action.payload); // add the item to the cart
    },

    // Reducer function for the "removeItem" action
    removeItem: (state) => {
      state.items.pop(); // removes last item (simple version)
    },

    // Reducer function for the "clearCart" action
    clearCart: (state) => {
      state.items.length = 0; // empties the array in place
      // Note: don't do `state = []` — that won't work (see section 14)
    },
  },
});

// Export the actions — these are what components will dispatch
export const { addItem, removeItem, clearCart } = cartSlice.actions;

// Export the reducer — this goes into the store
export default cartSlice.reducer;
```

**What `createSlice` returns behind the scenes:**

`cartSlice` is an object that looks like:
```javascript
{
  actions: {
    addItem: [function],
    removeItem: [function],
    clearCart: [function],
  },
  reducer: [function],
}
```

That's why we export `cartSlice.actions` (destructured) and `cartSlice.reducer` (as default).

---

## 10. Connecting the Store to the App with Provider

### 🎤 Interview Question:
**"How do you create the Redux store and connect it to your React app?"**

### 💬 Conversational Answer:

**Step 1 — Create the store:**

```javascript
// utils/appStore.js
import { configureStore } from "@reduxjs/toolkit";
import cartReducer from "./cartSlice";

// configureStore comes from @reduxjs/toolkit (a Redux job)
const appStore = configureStore({
  reducer: {
    // Each key here becomes a slice name in the store
    // store.cart will now exist and be managed by cartReducer
    cart: cartReducer,

    // If you had more slices:
    // user: userReducer,
    // theme: themeReducer,
  },
});

export default appStore;
```

> **Why does `configureStore` come from `@reduxjs/toolkit` and not `react-redux`?**
> Because creating a store is a pure Redux job. Providing it to React is a React–Redux bridge job. That's why the next step uses `react-redux`.

**Step 2 — Wrap your app in `<Provider>`:**

```javascript
// app.js
import { Provider } from "react-redux";    // ← from react-redux (the bridge)
import appStore from "./utils/appStore";

const AppLayout = () => {
  return (
    // Provider makes the store available to every component inside it
    <Provider store={appStore}>
      <div className="app">
        <Header />
        <Outlet />
      </div>
    </Provider>
  );
};
```

Just like `React Context` had a `Provider`, Redux has a `Provider` too. Wrap your entire app in it and every component inside now has access to the store.

---

## 11. Reading from the Store with useSelector

### 🎤 Interview Question:
**"How do you read data from the Redux Store in a component?"**

### 💬 Conversational Answer:

Use the `useSelector` hook from `react-redux`. You pass it a callback that receives the entire store and returns the specific piece you want.

**Example — showing cart count in the Header:**

```javascript
// Header.js
import { useSelector } from "react-redux";

const Header = () => {
  // Subscribe to only the cart items — nothing else
  const cartItems = useSelector((store) => store.cart.items);

  return (
    <div className="header">
      <nav>
        <ul>
          <li>Home</li>
          <li>About</li>
          {/* cartItems.length updates in real time as items are added */}
          <li className="font-bold">Cart - {cartItems.length} items</li>
        </ul>
      </nav>
    </div>
  );
};
```

Now whenever `store.cart.items` changes (because someone dispatched `addItem`), this component re-renders and the count updates automatically — with no props passed, no callbacks needed.

> 💡 **Debugging tip:** If you `console.log(state)` inside a reducer, Redux will show you a Proxy object that's hard to read. Use `console.log(current(state))` instead — `current` comes from `@reduxjs/toolkit` and gives you the readable version of the state.

---

## 12. Writing to the Store with useDispatch

### 🎤 Interview Question:
**"How do you update the Redux Store from a component? Show how to dispatch an action."**

### 💬 Conversational Answer:

Use the `useDispatch` hook from `react-redux`. It gives you a `dispatch` function. You call `dispatch(actionCreator(payload))` to trigger a reducer.

**Example — Add to Cart button in ItemList:**

```javascript
// ItemList.js
import { useDispatch } from "react-redux";
import { addItem } from "../utils/cartSlice";

const ItemList = ({ items }) => {
  // Get the dispatch function
  const dispatch = useDispatch();

  const handleAddItem = (item) => {
    // Dispatch the addItem action, passing the item as the payload
    // item will be available as action.payload inside the reducer
    dispatch(addItem(item));
  };

  return (
    <div>
      {items.map((item) => (
        <div key={item.card.info.id} className="item-card">
          <p>{item.card.info.name}</p>
          <p>Rs. {item.card.info.price / 100 || item.card.info.defaultPrice / 100}</p>

          {/*
            ⚠️ Common interview question — what's the difference between these three?
            onClick={handleAddItem(item)}         ← WRONG: calls immediately on render
            onClick={() => handleAddItem(item)}   ← CORRECT: passes a callback
            onClick={handleAddItem}               ← Works only if no argument is needed
          */}
          <button onClick={() => handleAddItem(item)}>Add +</button>
        </div>
      ))}
    </div>
  );
};
```

**What happens behind the scenes when you dispatch:**

When you call `dispatch(addItem(item))`, Redux internally creates an action object that looks like:
```javascript
{
  type: "cart/addItem",
  payload: item  // ← whatever you passed in becomes the payload
}
```
This object is then passed to the reducer function as the second argument. That's why inside the reducer you access `action.payload`.

---

## 13. Building the Cart Page

### 🎤 Interview Question:
**"How do you build a cart page that reads items from the Redux Store?"**

### 💬 Conversational Answer:

The cart page is just another component that subscribes to the store. We can even reuse the `ItemList` component to display the items — that's the power of building modular components.

```javascript
// components/Cart.js
import { useSelector, useDispatch } from "react-redux";
import ItemList from "./ItemList";
import { clearCart } from "../utils/cartSlice";

const Cart = () => {
  // Subscribe to cart items from the store
  const cartItems = useSelector((store) => store.cart.items);
  const dispatch = useDispatch();

  const handleClearCart = () => {
    dispatch(clearCart()); // dispatch the clearCart action
  };

  return (
    <div className="text-center m-4 p-4">
      <h1 className="text-2xl font-bold">Cart</h1>

      <button
        className="p-2 m-2 bg-black text-white rounded-lg"
        onClick={handleClearCart}
      >
        Clear Cart
      </button>

      {/* If cart is empty, show a friendly message */}
      {cartItems.length === 0 && (
        <h1>Cart is empty. Add items to the cart!</h1>
      )}

      {/* Reuse ItemList component — just pass cartItems as the items prop */}
      <div className="w-6/12 m-auto">
        <ItemList items={cartItems} />
      </div>
    </div>
  );
};

export default Cart;
```

Then add the route in your router:

```javascript
// app.js — inside createBrowserRouter
{
  path: "/cart",
  element: <Cart />,
}
```

And a Link in the Header:

```javascript
// Header.js
<Link to="/cart">Cart - {cartItems.length} items</Link>
```

---

## 14. Mutating State in RTK – The Immer Library Behind the Scenes

### 🎤 Interview Question:
**"In older Redux, we were told never to mutate state. But in Redux Toolkit we do mutate it. Why? What changed?"**

### 💬 Conversational Answer:

This is one of the most important things to understand if you're coming from vanilla Redux or if your interviewer is familiar with the old way.

**Old Redux rule:** Never mutate state. Always return a new copy.

```javascript
// Old Redux — the painful way
const cartReducer = (state, action) => {
  const newState = { ...state }; // create a copy first
  newState.items.push(action.payload);
  return newState; // must return the new state
};
```

This was error-prone. Many developers — especially those who didn't fully understand JavaScript's reference vs value semantics — would make mistakes. Deep-cloning a deeply nested object correctly is non-trivial, and a lot of developers got it wrong.

**RTK rule:** Mutate the state directly. Don't return anything (unless replacing the state entirely).

```javascript
// RTK — the modern way
addItem: (state, action) => {
  state.items.push(action.payload); // mutate directly — RTK handles the rest
}
```

**Why is this safe now?** RTK uses a library called **Immer** under the hood. When your reducer runs, Immer:
1. Tracks all changes you made to the `state` object
2. Calculates the diff between the original state and the mutated version
3. Produces a new immutable state object from that diff

So behind the scenes, immutability is still maintained. But as a developer, you get to write the much simpler mutation syntax.

**One critical thing to know — `state = []` will NOT work:**

```javascript
clearCart: (state) => {
  state = []; // ❌ This does NOT work
  // You're just reassigning a local variable, not mutating the original state
}
```

This is plain JavaScript: `state` is a local variable inside the function. Reassigning it doesn't change what it points to externally. Immer never sees the change.

**What you should do instead:**

```javascript
clearCart: (state) => {
  state.items.length = 0; // ✅ Mutates in place — Immer sees this change
}

// Alternatively, return a brand new state object:
clearCart: (state) => {
  return { items: [] }; // ✅ Return replaces the entire state
}
```

The rule in RTK: **either mutate the existing state OR return a brand new state object. Never both.**

---

## 15. A Critical Performance Warning – Don't Over-Subscribe

### 🎤 Interview Question:
**"What is a common performance mistake when using useSelector in Redux?"**

### 💬 Conversational Answer:

Subscribing to more of the store than you actually need.

```javascript
// ❌ Bad — subscribes to the entire store
const store = useSelector((store) => store);
const cartItems = store.cart.items;

// ✅ Good — subscribes to only the specific slice you need
const cartItems = useSelector((store) => store.cart.items);
```

Both give you `cartItems`. But they behave very differently.

With the first version, your component is subscribed to **every single change in the store**. If a user logs in (updating the `user` slice), your cart component will re-render — even though nothing about the cart changed. In a large app with dozens of slices and hundreds of actions, this is a massive and unnecessary performance drain.

With the second version, your component only re-renders when `store.cart.items` specifically changes. Nothing else triggers it.

This is why the hook is called `useSelector` — you're **selecting** a precise portion of the store. Be precise with what you select.

---

## 16. reducer vs reducers – A Common Point of Confusion

### 🎤 Interview Question:
**"In Redux Toolkit, what is the difference between `reducer` (singular) and `reducers` (plural)?"**

### 💬 Conversational Answer:

This trips up a lot of developers because the same word appears in two different places with two different meanings.

**In `configureStore` — it's `reducer` (singular):**

```javascript
const appStore = configureStore({
  reducer: {          // ← singular "reducer"
    cart: cartReducer,
    user: userReducer,
  }
});
```

Here `reducer` refers to the **one big root reducer** for the entire app. It's the combination of all the slice reducers. One reducer to rule them all.

**In `createSlice` — it's `reducers` (plural):**

```javascript
const cartSlice = createSlice({
  reducers: {         // ← plural "reducers"
    addItem: (state, action) => { ... },
    removeItem: (state) => { ... },
    clearCart: (state) => { ... },
  }
});
```

Here `reducers` refers to the **multiple individual reducer functions** inside the slice — one per action. This slice has several reducer functions, so the key is plural.

**The mental model:**
- `configureStore.reducer` = the one app-level reducer (a combination of all slice reducers)
- `createSlice.reducers` = the many action-level reducer functions inside a single slice

---

## 17. Redux DevTools – Time-Travel Debugging

### 🎤 Interview Question:
**"What is Redux DevTools and why is it useful?"**

### 💬 Conversational Answer:

Redux DevTools is a **Chrome (and browser) extension** that becomes active whenever you're running a Redux app. It's one of the most powerful debugging tools in frontend development.

Install it from the Chrome Web Store, open your browser DevTools, and you'll find a new Redux tab.

**What it shows you:**

- A **live log** of every single action that was dispatched, in order (e.g., `cart/addItem`, `cart/clearCart`)
- For each action: the **state before**, the **state after**, and the **diff** (exactly what changed)
- A **trace** showing you the exact line of code where the action was dispatched
- A **tree view** of your current store state

**The killer feature — time travel:**

You can click "Jump" next to any previous action in the log and your app's UI will snap back to exactly what it looked like at that point. You can replay user actions, skip specific actions, and see how your UI would have looked at each step. This is especially powerful for large apps where dozens of components are modifying the store and it's hard to figure out what action caused a bug.

> **Homework:** Install Redux DevTools, add items to your cart, and explore every button in the extension. Pay special attention to the Jump, Skip, and Replay features — they will save you hours of debugging time in real projects.

---

## 18. RTK Query and Older Redux Concepts (Thunk, Middleware)

### 🎤 Interview Question:
**"What are Redux Thunk and Middleware? What replaces them in RTK?"**

### 💬 Conversational Answer:

In older Redux, when you needed to make an asynchronous operation (like an API call) and store the result in Redux, the pattern involved **middleware** and something called **Redux Thunk**. These were special tools for handling side effects inside Redux. The setup was complex and the concepts added yet another layer of required knowledge.

In Redux Toolkit, the equivalent modern tool is **RTK Query**.

RTK Query is a data-fetching and caching solution that comes bundled right inside Redux Toolkit. It allows you to make API calls and manage the result — including loading and error states — in a Redux-friendly way, with far less boilerplate than the old Thunk pattern.

> **Homework:** Go to the Redux Toolkit documentation and read the **RTK Query Quick Start** guide. Follow the example end to end. With your understanding of `configureStore` and slices from this episode, the RTK Query pattern will make sense quickly.

**Key terms to know for interviews:**
- Older way: **Middleware** + **Redux Thunk** — for async operations in vanilla Redux
- Modern way: **RTK Query** — built into Redux Toolkit, cleaner and more powerful

---

## 19. Coding Questions

### Q1. Install the two required Redux libraries

```bash
npm install @reduxjs/toolkit
npm install react-redux
```

---

### Q2. Create the Redux Store with configureStore

```javascript
// utils/appStore.js
import { configureStore } from "@reduxjs/toolkit";
import cartReducer from "./cartSlice";

const appStore = configureStore({
  reducer: {
    cart: cartReducer,   // "cart" becomes the key in the store: store.cart
    // user: userReducer,
    // theme: themeReducer,
  },
});

export default appStore;
```

---

### Q3. Provide the store to the React app

```javascript
// app.js
import React from "react";
import { Provider } from "react-redux";      // from react-redux
import appStore from "./utils/appStore";

const AppLayout = () => {
  return (
    <Provider store={appStore}>
      <div className="app">
        <Header />
        <Outlet />
      </div>
    </Provider>
  );
};

export default AppLayout;
```

---

### Q4. Create a cart slice with addItem, removeItem, and clearCart

```javascript
// utils/cartSlice.js
import { createSlice } from "@reduxjs/toolkit";

const cartSlice = createSlice({
  name: "cart",

  initialState: {
    items: [],
  },

  reducers: {
    addItem: (state, action) => {
      // Mutate directly — Immer handles immutability behind the scenes
      state.items.push(action.payload);
    },

    removeItem: (state) => {
      state.items.pop();
    },

    clearCart: (state) => {
      state.items.length = 0;  // ✅ Mutates in place
      // NOT: state = []       // ❌ Just reassigns a local variable
    },
  },
});

// Export actions for dispatching
export const { addItem, removeItem, clearCart } = cartSlice.actions;

// Export reducer for the store
export default cartSlice.reducer;
```

---

### Q5. Read from the store using useSelector

```javascript
// Header.js
import { useSelector } from "react-redux";

const Header = () => {
  // Subscribe to only the specific slice we need
  const cartItems = useSelector((store) => store.cart.items);

  return (
    <div className="header">
      <nav>
        <ul>
          <li>Home</li>
          <li>About</li>
          <li className="font-bold">
            Cart - {cartItems.length} items   {/* updates automatically */}
          </li>
        </ul>
      </nav>
    </div>
  );
};
```

---

### Q6. Write to the store using useDispatch

```javascript
// ItemList.js
import { useDispatch } from "react-redux";
import { addItem } from "../utils/cartSlice";

const ItemList = ({ items }) => {
  const dispatch = useDispatch();

  const handleAddItem = (item) => {
    dispatch(addItem(item));  // item becomes action.payload in the reducer
  };

  return (
    <div>
      {items.map((item) => (
        <div key={item.card.info.id} className="flex justify-between border-b p-2">
          <div>
            <p className="font-bold">{item.card.info.name}</p>
            <p>
              Rs.{" "}
              {(item.card.info.price || item.card.info.defaultPrice) / 100}
            </p>
          </div>

          {/* Always pass a callback — never call the function directly */}
          <button
            className="p-2 bg-black text-white rounded-lg"
            onClick={() => handleAddItem(item)}
          >
            Add +
          </button>
        </div>
      ))}
    </div>
  );
};

export default ItemList;
```

---

### Q7. Build the Cart page component

```javascript
// components/Cart.js
import { useSelector, useDispatch } from "react-redux";
import ItemList from "./ItemList";
import { clearCart } from "../utils/cartSlice";

const Cart = () => {
  const cartItems = useSelector((store) => store.cart.items);
  const dispatch = useDispatch();

  const handleClearCart = () => {
    dispatch(clearCart());
  };

  return (
    <div className="text-center m-4 p-4">
      <h1 className="text-2xl font-bold">Cart</h1>

      <button
        className="p-2 m-2 bg-black text-white rounded-lg"
        onClick={handleClearCart}
      >
        Clear Cart
      </button>

      {cartItems.length === 0 && (
        <h1 className="text-xl mt-4">
          Cart is empty. Add items to get started!
        </h1>
      )}

      <div className="w-6/12 m-auto">
        <ItemList items={cartItems} />
      </div>
    </div>
  );
};

export default Cart;
```

---

### Q8. Add the cart route to your router

```javascript
// app.js
import { createBrowserRouter } from "react-router-dom";
import Cart from "./components/Cart";

const appRouter = createBrowserRouter([
  {
    path: "/",
    element: <AppLayout />,
    children: [
      { path: "/", element: <Body /> },
      { path: "/about", element: <About /> },
      { path: "/cart", element: <Cart /> },   // ← add cart route
    ],
  },
]);
```

---

### Q9. Debug a reducer using current()

```javascript
// Inside a reducer function — state is a Proxy, not directly console.log-able
import { createSlice, current } from "@reduxjs/toolkit";

clearCart: (state) => {
  // ❌ console.log(state) — shows a Proxy object, unreadable
  console.log(current(state)); // ✅ current() gives you the readable version
  state.items.length = 0;
},
```

---

### Q10. Why `state = []` does NOT clear the cart (and what does)

```javascript
clearCart: (state) => {
  // ❌ Does NOT work — only reassigns the local variable
  // The original state object is never touched, Immer sees no change
  state = [];

  // ✅ Option 1 — mutate in place (recommended)
  state.items.length = 0;

  // ✅ Option 2 — return a new state (replaces original state with this object)
  return { items: [] };
}
```

---

### Q11. Performance: right vs wrong way to use useSelector

```javascript
// ❌ Wrong — subscribes component to the entire store
// Any change in any slice (user, theme, cart...) causes a re-render
const store = useSelector((store) => store);
const cartItems = store.cart.items;

// ✅ Right — subscribes only to cart.items
// Only re-renders when cart items actually change
const cartItems = useSelector((store) => store.cart.items);
```

---

## 🗒️ Quick Revision Card

| Concept | Key Point |
|---|---|
| Redux is mandatory? | No. Use it only when your app grows large and data management becomes complex |
| Redux vs React | Separate libraries — Redux works without React; `react-redux` is the bridge |
| Vanilla Redux vs RTK | RTK is the modern standard — less boilerplate, simpler API, better defaults |
| RTK nickname | RTK = Redux Toolkit |
| Two libraries needed | `@reduxjs/toolkit` (Redux logic) + `react-redux` (bridge to React) |
| Redux Store | A big global JavaScript object holding your app's shared data |
| Slice | A logical partition of the store; one slice per concern (cart, user, theme) |
| `createSlice` | RTK function that creates a slice — takes `name`, `initialState`, `reducers` |
| `configureStore` | RTK function that creates the app store — comes from `@reduxjs/toolkit` |
| `Provider` | React-Redux component that wraps your app and gives all components access to the store |
| The full Redux flow | Click button → dispatch action → reducer function → update slice → component re-renders |
| Dispatch | The act of firing an action to update the store |
| Action | A named event describing what you want to do (e.g., `addItem`, `clearCart`) |
| `action.payload` | Whatever data you pass into `dispatch(addItem(data))` — arrives as `action.payload` in the reducer |
| Reducer function | The function that actually modifies the slice state based on the dispatched action |
| Selector | The mechanism for reading from the store; uses `useSelector` hook |
| Subscribing to the store | When a component uses `useSelector` — it will re-render whenever that data changes |
| `useSelector` | React-Redux hook for reading from the store |
| `useDispatch` | React-Redux hook that gives you the `dispatch` function |
| Mutating state in RTK | RTK says: mutate directly. Immer handles immutability behind the scenes |
| Immer | Library used by RTK internally — diffs old and new state, produces new immutable copy |
| Why `state = []` fails | Reassigns a local variable; the original state object is never touched by Immer |
| `state.items.length = 0` | Correctly empties the array by mutating in place |
| Returning from reducer | Optional in RTK; if you return, it replaces the entire state with the returned value |
| `current(state)` | RTK utility to console.log readable state inside a reducer (bypasses Proxy) |
| Performance: selector | Always subscribe to the smallest portion of the store you need — never the whole store |
| `reducer` (singular) | The one root reducer in `configureStore` — a combination of all slice reducers |
| `reducers` (plural) | The multiple action-handler functions inside a single `createSlice` call |
| Redux DevTools | Chrome extension — shows action log, state diffs, traces, and time-travel debugging |
| Time-travel debugging | Jump to any past action and see how your app looked at that exact point |
| Skip action | DevTools lets you skip an action and replay to see how app behaves without it |
| Vanilla Redux async | Used Middleware + Redux Thunk for async operations |
| Modern async with RTK | Use RTK Query — bundled with Redux Toolkit, read the docs as homework |
| Zustand | Alternative state management library — lightweight, gaining popularity |
| When to avoid Redux | Small/medium apps — use React Context instead; Redux adds unnecessary complexity |

---

*Episode 12 – Namaste React Series | Let's Build Our Store*