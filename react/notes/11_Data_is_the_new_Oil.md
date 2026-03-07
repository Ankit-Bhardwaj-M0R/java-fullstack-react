# Episode 11 – Data is the New Oil
### React Interview Prep + Coding Questions

---

## Table of Contents

1. [UI Layer vs Data Layer – The Two Layers of a React App](#1-ui-layer-vs-data-layer)
2. [Higher Order Components (HOC) – What, Why, and How](#2-higher-order-components)
3. [Building withPromotedLabel – A Real HOC Example](#3-building-withpromotedlabel)
4. [HOC Are Pure Functions – Never Modify the Input Component](#4-hoc-are-pure-functions)
5. [Accordion UI – Building Restaurant Category with Collapsible Sections](#5-accordion-ui)
6. [Filtering API Data – Getting Only What You Need](#6-filtering-api-data)
7. [Building the Item List Component](#7-building-item-list)
8. [Handling Inconsistent API Data – price vs defaultPrice](#8-handling-inconsistent-api-data)
9. [Controlled vs Uncontrolled Components](#9-controlled-vs-uncontrolled-components)
10. [Lifting State Up – Making a Parent Control Its Children](#10-lifting-state-up)
11. [Passing Functions as Props – How Children Talk to Parents](#11-passing-functions-as-props)
12. [React DevTools – Components Tab and Profiler](#12-react-devtools)
13. [Props Drilling – The Problem with Passing Props Down](#13-props-drilling)
14. [React Context – createContext and useContext](#14-react-context)
15. [Context.Consumer – Using Context in Class Components](#15-context-consumer-in-class-components)
16. [Context.Provider – Overriding Default Values](#16-context-provider)
17. [Nested Providers – Scope of Context Values](#17-nested-providers)
18. [Updating Context Dynamically – Connecting State to Context](#18-updating-context-dynamically)
19. [Context vs Redux – When to Use What](#19-context-vs-redux)
20. [Coding Questions](#20-coding-questions)

---

## 1. UI Layer vs Data Layer

### 🎤 Interview Question:
**"What are the two layers of a React application? Why is the data layer important?"**

### 💬 Conversational Answer:

Every React application is really made of two separate concerns — the **UI layer** and the **data layer**.

**The UI layer** is everything you see — the JSX you write, the components, the HTML elements that appear on the screen. It's static. JSX on its own doesn't do anything — it just sits there and describes what the page looks like.

**The data layer** is everything that powers the UI — your `state`, your `props`, your local variables, the data you get from APIs, and the JavaScript logic inside your curly braces `{}` in JSX. The data layer is what makes the UI dynamic.

**The key relationship:** the UI layer is *powered by* the data layer. You change a piece of state, and the UI re-renders to reflect it. The UI doesn't have logic of its own — it just displays whatever the data layer tells it to display.

**Why does this matter?**

If your data layer is a mess — if you're randomly passing props everywhere, if your state is all over the place, if you can't figure out where a piece of data is coming from — your app will be fragile, hard to debug, and slow to build.

Senior developers are excellent at managing the data layer. They know where to keep data, how to pass it around efficiently, and when to use which tool (state, props, context, or Redux). That's what separates them from junior developers who can make the app work but struggle when it grows.

The entire topic of Episode 11 — Higher Order Components, Lifting State Up, Context — is all about managing the data layer better.

---

## 2. Higher Order Components (HOC) – What, Why, and How

### 🎤 Interview Question:
**"What is a Higher Order Component in React? When would you use one?"**

### 💬 Conversational Answer:

A Higher Order Component (HOC) is a function that:
- **Takes a component as input**
- **Returns a new, enhanced component as output**

That's the entire definition. It sounds fancy but at the end of the day, it's just a normal JavaScript function.

Think of it like this: you have an existing component, and you want to add some extra feature or UI to it — but you don't want to modify the original component. So you wrap it in a function that returns a new version of it with that extra feature added. That wrapping function is the HOC.

**Real-world analogy:** imagine you have a plain t-shirt (your existing component). A HOC is like a machine that takes your t-shirt, stitches a logo on it, and hands back a new t-shirt with the logo. The original t-shirt design didn't change — it just got enhanced.

**Where did we use this?**

On Swiggy, some restaurant cards have a small "Promoted" label on the top-left corner. Other cards look identical but without that label. Both use the same `RestaurantCard` component — the promoted ones just have an extra badge.

Instead of building two separate card components (one with the label, one without), we used a HOC called `withPromotedLabel` that takes `RestaurantCard` and returns a new component that is `RestaurantCard` + a promoted badge on top of it.

**The pattern:**
```
input:  RestaurantCard
HOC:    withPromotedLabel
output: RestaurantCardPromoted (RestaurantCard with a badge)
```

**Other common uses for HOCs:**
- Adding authentication checking to a component (redirect if not logged in)
- Adding extra props to a component
- Wrapping a component with error boundary logic
- Adding analytics tracking to any component

---

## 3. Building withPromotedLabel – A Real HOC Example

### 🎤 Interview Question:
**"Show how to write and use a Higher Order Component with a real example."**

### 💬 Conversational Answer:

Here's the full implementation of the promoted label feature using a HOC.

**Step 1 — The data tells us which restaurant is promoted:**

When you log the restaurant list from the Swiggy API, each restaurant object has a `promoted` field:
```javascript
{
  data: {
    name: "Kanur Food Point",
    promoted: true,    // ← this tells us to show the badge
    ...
  }
}
```

**Step 2 — Write the HOC:**

```javascript
// RestaurantCard.js

// --- The original restaurant card (no changes to this) ---
const RestaurantCard = (props) => {
  const { resData } = props;
  const { name, cuisines, avgRating, costForTwo } = resData?.info;

  return (
    <div className="res-card">
      <img src={CDN_URL + resData?.info?.cloudinaryImageId} alt={name} />
      <h3>{name}</h3>
      <h4>{cuisines.join(", ")}</h4>
      <h4>{avgRating} stars</h4>
      <h4>{costForTwo}</h4>
    </div>
  );
};

// --- The Higher Order Component ---
// Takes RestaurantCard as input, returns an enhanced version
export const withPromotedLabel = (WrappedComponent) => {
  // This is the new enhanced component we are returning
  return (props) => {
    return (
      <div>
        {/* The extra "Promoted" label we are adding */}
        <label
          className="absolute bg-black text-white m-2 p-2 rounded-lg"
        >
          Promoted
        </label>

        {/* The original component — we pass ALL props through using spread */}
        <WrappedComponent {...props} />
      </div>
    );
  };
};

export default RestaurantCard;
```

**Step 3 — Use it in the Body component:**

```javascript
// Body.js
import RestaurantCard, { withPromotedLabel } from "./RestaurantCard";

// Create the promoted version using the HOC
const RestaurantCardPromoted = withPromotedLabel(RestaurantCard);

const Body = () => {
  return (
    <div className="rest-container">
      {restaurants.map((restaurant) =>
        // If promoted → show the promoted card
        // Otherwise  → show the normal card
        restaurant.data.promoted ? (
          <RestaurantCardPromoted
            key={restaurant.data.id}
            resData={restaurant}
          />
        ) : (
          <RestaurantCard
            key={restaurant.data.id}
            resData={restaurant}
          />
        )
      )}
    </div>
  );
};
```

**What the `{...props}` spread operator does:**

When your HOC receives props (like `resData`), it needs to pass those props down to the wrapped component. The spread operator `{...props}` passes ALL received props directly to `WrappedComponent` without you listing them one by one. This is the standard pattern in HOCs.

**Result:** Promoted restaurants show the card with a black "Promoted" label in the top corner. Non-promoted restaurants show the plain card. Same `RestaurantCard` component — HOC handles the enhancement.

---

## 4. HOC Are Pure Functions – Never Modify the Input Component

### 🎤 Interview Question:
**"Why should Higher Order Components be pure functions? What does that mean?"**

### 💬 Conversational Answer:

When we say an HOC should be a **pure function**, it means:

> The HOC should **not modify** the component it receives. It should **use** it as-is and just add something on top.

In our `withPromotedLabel` HOC, we took `RestaurantCard` as input. We never went inside `RestaurantCard.js` and edited its code. We never changed what it renders, what props it accepts, or how it behaves. We just wrapped it — rendering the original `<WrappedComponent {...props} />` exactly as it was, and putting the promoted label on top of it.

**Why is this important?**

- The original `RestaurantCard` can still be used everywhere else without the label — you haven't broken it
- If tomorrow you want to remove the promoted label feature, you delete the HOC — `RestaurantCard` is untouched
- The HOC is reusable — you can wrap ANY component with `withPromotedLabel`, not just `RestaurantCard`
- Testing is easier — you test `RestaurantCard` independently, and the HOC independently

**The rule:** when writing HOCs, always pass all props through using `{...props}`. Never mutate the input component directly.

---

## 5. Accordion UI – Building Restaurant Category with Collapsible Sections

### 🎤 Interview Question:
**"How do you build a collapsible accordion in React from scratch?"**

### 💬 Conversational Answer:

An accordion is a UI pattern where you have multiple sections. Each section has a header (you can click it) and a body (the list of items inside). Clicking the header toggles the body open and closed.

In our app, the restaurant menu page has sections like "Recommended", "Newly Added", "Lassi & Smoothies". Each is an accordion item.

**How we structured this:**
- `RestaurantMenu` → the page-level component; maps over categories
- `RestaurantCategory` → one accordion item (header + collapsible body)
- `ItemList` → the body of the accordion (list of menu items)

**The RestaurantCategory component:**

```javascript
// RestaurantCategory.js
import ItemList from "./ItemList";
import { useState } from "react";

const RestaurantCategory = ({ data }) => {
  const [showItems, setShowItems] = useState(false); // accordion closed by default

  const handleClick = () => {
    setShowItems(!showItems); // toggle open/close
  };

  return (
    <div className="w-6/12 mx-auto my-4 bg-gray-50 shadow-lg p-4">
      {/* Accordion Header — clicking this opens/closes the body */}
      <div
        className="flex justify-between cursor-pointer"
        onClick={handleClick}
      >
        <span className="font-bold text-lg">
          {data.title} ({data.itemCards.length})
        </span>
        <span>⬇️</span>
      </div>

      {/* Accordion Body — only shown when showItems is true */}
      {showItems && <ItemList items={data.itemCards} />}
    </div>
  );
};

export default RestaurantCategory;
```

**The toggle logic explained:**

```javascript
setShowItems(!showItems);
// If showItems is false → make it true  (open the accordion)
// If showItems is true  → make it false (close the accordion)
```

This is a standard **toggle** pattern — flip the boolean every time the user clicks.

---

## 6. Filtering API Data – Getting Only What You Need

### 🎤 Interview Question:
**"How do you filter Swiggy API data to get only the menu categories?"**

### 💬 Conversational Answer:

When you fetch a restaurant's menu from the Swiggy API, you get back a huge array of cards (24+ objects). Not all of them are menu categories — some are:
- Restaurant address information
- "Veg only" filter badge
- Deal banners
- And then the actual item categories (like "Recommended", "Newly Added", etc.)

We need to filter out only the actual item categories. How do we identify them? Each card has a field called `"@type"` (with an `@` symbol — unusual for JavaScript property names). The item categories have `"@type": "type.googleapis.com/swiggy.presentation.food.v2.ItemCategory"`.

**How to filter in JavaScript:**

Normal dot notation won't work for property names with `@`:
```javascript
card["@type"]  // ✅ must use bracket notation
card.@type     // ❌ invalid JavaScript syntax
```

```javascript
// RestaurantMenu.js
const categories = resInfo?.cards
  .filter(
    (c) =>
      c?.card?.card?.["@type"] ===
      "type.googleapis.com/swiggy.presentation.food.v2.ItemCategory"
  );
```

Now `categories` is an array containing only the actual menu sections (Recommended, Newly Added, etc.). Each category object has:
- `title` — the section heading
- `itemCards` — the array of items in that section

**This teaches an important real-world lesson:** APIs return messy, mixed data. Your job as a developer is to extract exactly what you need using `.filter()`, optional chaining `?.`, and bracket notation for unusual property names.

---

## 7. Building the Item List Component

### 🎤 Interview Question:
**"How do you build the ItemList component that renders the menu items?"**

### 💬 Conversational Answer:

The `ItemList` component receives an array of `items` as a prop and renders each one with name, price, description, and image.

```javascript
// ItemList.js
import { CDN_URL } from "../utils/constants";

const ItemList = ({ items }) => {
  return (
    <div>
      {items.map((item) => (
        <div
          key={item.card.info.id}
          className="p-2 m-2 border-b-2 border-gray-200 flex justify-between"
        >
          {/* Left section: name, description, price */}
          <div className="w-9/12">
            <span className="font-semibold">
              {item.card.info.name}
            </span>

            <span className="px-2">
              ₹{" "}
              {item.card.info.price
                ? item.card.info.price / 100
                : item.card.info.defaultPrice / 100}
            </span>

            <p className="text-xs text-left">
              {item.card.info.description}
            </p>
          </div>

          {/* Right section: image + Add button */}
          <div className="w-3/12 relative">
            <img
              src={CDN_URL + item.card.info.imageId}
              className="w-full"
              alt={item.card.info.name}
            />
            <button className="absolute mx-16 bg-black text-white p-2 rounded-lg">
              Add +
            </button>
          </div>
        </div>
      ))}
    </div>
  );
};

export default ItemList;
```

**Key things in this component:**
- `key` uses the unique item `id` from the API — never use index as key in real apps
- The layout uses Tailwind's grid: `w-9/12` for text, `w-3/12` for image
- The Add button is `absolute` positioned to overlay on the image
- Price handling uses a conditional — explained in the next section

---

## 8. Handling Inconsistent API Data – price vs defaultPrice

### 🎤 Interview Question:
**"What do you do when API data is inconsistent — some items have one field, others have a different field?"**

### 💬 Conversational Answer:

This is a real problem that came up live during development! When displaying prices, some items from the Swiggy API had their price stored in `item.card.info.price`, while other items used `item.card.info.defaultPrice` instead. Some items had both, some had only one.

When you try to display `item.card.info.price / 100` for an item that doesn't have `price`, JavaScript returns `NaN` (Not a Number).

**The fix — use a conditional (ternary operator):**

```javascript
// If price exists, use it. Otherwise, fall back to defaultPrice.
{item.card.info.price
  ? item.card.info.price / 100
  : item.card.info.defaultPrice / 100}
```

**Why divide by 100?** The Swiggy API returns prices in paise (the smallest unit of Indian currency, like cents). 1 rupee = 100 paise. So `17000` in the API = ₹170.

**The bigger lesson here:** this is why the instructor specifically used real, live APIs in this course. In the real world, APIs are messy and inconsistent. Backend teams sometimes add new fields without removing old ones. Sometimes there's a bug. As a frontend developer, you must write defensive code that handles these cases gracefully. Instead of crashing with `NaN`, you fall back to the other field.

---

## 9. Controlled vs Uncontrolled Components

### 🎤 Interview Question:
**"What is the difference between controlled and uncontrolled components in React?"**

### 💬 Conversational Answer:

This is an important concept and also a common interview question.

**Uncontrolled component:**
A component that manages its own internal state. No parent is telling it what to do — it decides by itself. It is independent.

In our accordion example, each `RestaurantCategory` started as uncontrolled. Each one had its own `showItems` state. When you clicked on "Recommended", only `RestaurantCategory` for "Recommended" knew about it. "Newly Added" had no idea. They were all independent — each controlling itself.

```javascript
// Uncontrolled — manages its own state internally
const RestaurantCategory = ({ data }) => {
  const [showItems, setShowItems] = useState(false); // internal state
  
  return (
    <div onClick={() => setShowItems(!showItems)}>
      {showItems && <ItemList items={data.itemCards} />}
    </div>
  );
};
```

**Controlled component:**
A component whose behavior is completely controlled by its parent. The parent passes down the current value (as a prop) and a function to change it (also as a prop). The child has no internal state for this behavior — it just follows what the parent says.

After lifting state up (see next section), `RestaurantCategory` became controlled — the parent (`RestaurantMenu`) decided which accordion should be open, and passed that info down as a prop.

```javascript
// Controlled — parent tells it whether to show items
const RestaurantCategory = ({ data, showItems, setShowIndex }) => {
  // No internal state! The parent controls everything.
  return (
    <div onClick={() => setShowIndex()}>
      {showItems && <ItemList items={data.itemCards} />}
    </div>
  );
};
```

**The key difference:**
- Uncontrolled = child owns its state
- Controlled = parent owns the state, child just displays/reacts

HTML form inputs are the classic example: an uncontrolled `<input>` manages its own value through the DOM; a controlled `<input>` has its `value` tied to React state and an `onChange` handler.

---

## 10. Lifting State Up – Making a Parent Control Its Children

### 🎤 Interview Question:
**"What is 'lifting state up' in React? When and why would you do it?"**

### 💬 Conversational Answer:

Lifting state up means **moving a state variable from a child component up to a parent component**, so the parent can control multiple children with that single piece of state.

**The problem we were solving:**

We built accordions where each `RestaurantCategory` had its own `showItems` state. This was fine for basic open/close. But then we wanted a new feature: when you open one accordion, all the others should automatically close. Only one should be open at a time.

The problem? Each accordion only knows about *itself*. "Recommended" has no idea that "Newly Added" was opened. They don't talk to each other — they only have internal state.

**The solution — lift the state to the parent:**

Instead of each `RestaurantCategory` having `showItems`, we move a `showIndex` state to the parent `RestaurantMenu`. This single state tracks *which* accordion is currently open (by index: 0, 1, 2, etc.).

```
Before lifting state:
  RestaurantMenu
    ├── RestaurantCategory[0] — has own showItems state
    ├── RestaurantCategory[1] — has own showItems state
    └── RestaurantCategory[2] — has own showItems state

After lifting state:
  RestaurantMenu — has showIndex state (controls everything)
    ├── RestaurantCategory[0] — receives showItems as prop
    ├── RestaurantCategory[1] — receives showItems as prop
    └── RestaurantCategory[2] — receives showItems as prop
```

**Implementation:**

```javascript
// RestaurantMenu.js (the parent)
const RestaurantMenu = () => {
  const [showIndex, setShowIndex] = useState(0); // first accordion open by default

  return (
    <div>
      {categories.map((category, index) => (
        <RestaurantCategory
          key={category?.card?.card?.title}
          data={category?.card?.card}
          showItems={index === showIndex} // only this index's accordion shows items
          setShowIndex={() => setShowIndex(index)} // pass setter for this index
        />
      ))}
    </div>
  );
};
```

```javascript
// RestaurantCategory.js (the child — now controlled)
const RestaurantCategory = ({ data, showItems, setShowIndex }) => {
  return (
    <div className="w-6/12 mx-auto my-4">
      <div
        className="flex justify-between cursor-pointer"
        onClick={setShowIndex} // call parent's setter on click
      >
        <span className="font-bold">
          {data.title} ({data.itemCards.length})
        </span>
        <span>⬇️</span>
      </div>

      {showItems && <ItemList items={data.itemCards} />}
    </div>
  );
};
```

**How it works:**
- `RestaurantMenu` has state `showIndex = 0` (first accordion open)
- It passes `showItems={index === showIndex}` to each child — only the child whose index matches gets `true`
- When you click a category, it calls `setShowIndex(index)` — the parent updates `showIndex` to the clicked item's index
- React re-renders → only the newly clicked accordion has `showItems = true` → all others are `false`

Setting `showIndex` to `null` collapses all accordions (none match `null`).

---

## 11. Passing Functions as Props – How Children Talk to Parents

### 🎤 Interview Question:
**"Can a child component modify a parent's state? How?"**

### 💬 Conversational Answer:

A child **cannot directly** modify a parent's state. React's data flow is one-way: from parent to child (via props). A child can't reach up and change the parent.

But **indirectly**, a child can cause the parent's state to change — by calling a function that the parent passed down as a prop.

Here's the pattern:

1. Parent has a state variable and its setter: `const [showIndex, setShowIndex] = useState(0)`
2. Parent wraps the setter in a function and passes it as a prop to the child:
   ```javascript
   <RestaurantCategory setShowIndex={() => setShowIndex(index)} />
   ```
3. The child receives this function as a prop and calls it when needed:
   ```javascript
   <div onClick={setShowIndex}>...</div>
   ```
4. When the child calls this function, it is *actually calling the parent's setter* — the parent's state changes, and React re-renders everything

**The child doesn't know it's modifying parent state.** From the child's perspective, it's just calling a function it received. The parent is responsible for what that function does.

This is the **standard React pattern** for child-to-parent communication. It's how React keeps data flow clean — data always flows down (via props), events always flow up (via callback functions as props).

---

## 12. React DevTools – Components Tab and Profiler

### 🎤 Interview Question:
**"What is React DevTools? What are its two main features and how do you use them?"**

### 💬 Conversational Answer:

React DevTools is a Chrome (and Firefox) browser extension that adds two powerful tabs to your browser's DevTools: **Components** and **Profiler**.

**How to get it:** Go to the Chrome Web Store and install "React Developer Tools". After installing, you'll see two new tabs appear in your browser's Inspect tool.

---

**Tab 1: Components**

This tab shows you the entire React **component tree** — the hierarchy of all components currently rendered on the page. It's like a virtual DOM viewer.

What you can do:
- Hover over any component in the list → it highlights on the actual page
- Click a component → the right panel shows its **data layer**: all props it receives, its current state
- This lets you debug in real time: you can see *exactly* what state a component has at any moment

In the accordion example, when you click on a `RestaurantCategory` in the Components tab, you can see its `showItems` state. Open the accordion and watch `showItems` flip from `false` to `true`. This is incredibly powerful for debugging.

The component tree also shows components added by libraries (React Router's `RouterProvider`, `NavigationProvider`, etc.). Don't be alarmed by those — they're from external packages, not your code. Your app code starts at `AppLayout`.

---

**Tab 2: Profiler**

The Profiler lets you **record** your app's render activity and analyze performance.

How to use it:
1. Click the record button (●) — it starts recording
2. Use your app: click, navigate, open accordions, whatever
3. Stop recording
4. The profiler shows you a **flame graph** and **ranked view** of what rendered

What it tells you:
- Which components re-rendered during each action
- How many milliseconds each render took
- **Colorful (highlighted) components** = components that were affected by that action
- Grey components = didn't re-render

For example, the profiler showed that `ItemList` took 0.2ms out of 1.8ms total for one action. You can find the components that are slow and optimize them. This is what senior developers use to find performance bottlenecks in large apps.

> 💡 Install React DevTools right now. It is one of the most important tools you'll use as a React developer.

---

## 13. Props Drilling – The Problem with Passing Props Down

### 🎤 Interview Question:
**"What is props drilling? Why is it a problem?"**

### 💬 Conversational Answer:

Props drilling happens when you need to pass data from a high-level component all the way down to a deeply nested child, and you have to pass it through every component in between — even the components that don't need the data and are just forwarding it.

**Example from the app:**

Imagine you have data (`dummy`) in `RestaurantMenu` and you need it in `ItemList`. The component tree is:

```
RestaurantMenu
  └── RestaurantCategory     ← doesn't need dummy, just passes it
        └── ItemList         ← actually needs dummy
```

To get `dummy` to `ItemList`, you must:
1. Pass `dummy` as prop to `RestaurantCategory`
2. `RestaurantCategory` receives it and passes it down as prop to `ItemList`
3. `ItemList` finally receives it and uses it

`RestaurantCategory` has NOTHING to do with `dummy`. It doesn't use it. It's just a middleman.

**Why is this a problem?**

In real large applications, you might have 7 or 8 levels of nesting. Passing a prop through 8 levels — where only the bottom one uses it — means 7 components are involved in passing something they don't care about. This:
- Clutters your component code with irrelevant props
- Makes refactoring painful (changing prop names means changing it in 8 places)
- Makes it hard to understand what each component actually needs

**Code example showing the problem:**

```javascript
// RestaurantMenu.js
const dummy = "dummy data";
return (
  <RestaurantCategory dummy={dummy} />  // passing it down even though category doesn't need it
);

// RestaurantCategory.js
const RestaurantCategory = ({ data, dummy }) => {
  return (
    <ItemList items={data.itemCards} dummy={dummy} />  // just forwarding it
  );
};

// ItemList.js
const ItemList = ({ items, dummy }) => {
  console.log(dummy); // only this one actually uses it
  return (...);
};
```

This is props drilling, and React Context was created to solve exactly this problem.

---

## 14. React Context – createContext and useContext

### 🎤 Interview Question:
**"What is React Context? How do you create and use it?"**

### 💬 Conversational Answer:

React Context is a way to create a **global space** where you can store data and access it from **any component in your app**, no matter how deeply nested — without passing it through props.

Think of context like a broadcasting channel. You put data on the channel. Any component that subscribes to that channel can receive the data, regardless of where it is in the component tree.

**Good use cases for context:**
- Logged-in user information (needed in header, profile page, individual cards, footer)
- Theme (light/dark mode — needed everywhere across the app)
- Cart data
- Language/locale settings

**Step 1 — Create the context:**

Put this in a separate file inside `utils/`.

```javascript
// utils/UserContext.js
import { createContext } from "react";

// createContext() takes the DEFAULT value
// This default value is used when the context is accessed outside of a Provider
const UserContext = createContext({
  loggedInUser: "Default User",
});

export default UserContext;
```

**Step 2 — Use it in a functional component with `useContext`:**

```javascript
// Header.js
import { useContext } from "react";
import UserContext from "../utils/UserContext";

const Header = () => {
  // useContext hook: give it the context object, get back the context value
  const { loggedInUser } = useContext(UserContext);

  return (
    <div>
      <nav>
        <ul>
          <li className="font-bold">{loggedInUser}</li>
        </ul>
      </nav>
    </div>
  );
};
```

**Step 3 — Use it in a deeply nested component (no prop drilling needed):**

```javascript
// RestaurantCard.js — a deeply nested component
import { useContext } from "react";
import UserContext from "../utils/UserContext";

const RestaurantCard = ({ resData }) => {
  const { loggedInUser } = useContext(UserContext); // direct access, no props needed

  return (
    <div>
      <h3>{resData?.info?.name}</h3>
      <h4>User: {loggedInUser}</h4> {/* can display context data anywhere */}
    </div>
  );
};
```

**The important point:** `RestaurantCard` is deep in the component tree. But it got `loggedInUser` without anyone passing it down. That's the power of context — it breaks the chain of prop drilling entirely.

---

## 15. Context.Consumer – Using Context in Class Components

### 🎤 Interview Question:
**"How do you use React Context in a class-based component? Why can't you use useContext there?"**

### 💬 Conversational Answer:

The `useContext` hook is a React hook. And React's rule is: **hooks can only be used inside functional components** (or other custom hooks). You cannot use `useContext` in a class-based component.

But context still works in class components — you just use a different pattern called `Context.Consumer`.

**Using `Context.Consumer` in a class component:**

```javascript
// About.js (class-based component)
import React from "react";
import UserContext from "../utils/UserContext";

class About extends React.Component {
  render() {
    return (
      <div>
        <h1>About Us Page</h1>

        {/* UserContext.Consumer is a component. It wraps your JSX. */}
        <UserContext.Consumer>
          {/* It takes a callback function as its child */}
          {/* That callback receives the context value as its argument */}
          {(data) => (
            <h1 className="text-xl font-bold">
              Logged in User: {data.loggedInUser}
            </h1>
          )}
        </UserContext.Consumer>
      </div>
    );
  }
}

export default About;
```

**How `Context.Consumer` works:**
- It's a React component (not a hook)
- You wrap it around the JSX that needs context data
- Inside it, you write a **function** (a callback) — not JSX directly
- React calls that function and passes the context value as the argument
- You use that argument to render your JSX

**Comparison of the two approaches:**

| | Functional Component | Class Component |
|---|---|---|
| How to use context | `useContext(UserContext)` | `<UserContext.Consumer>` |
| Type | Hook | Component |
| Code | Cleaner, one line | More verbose, requires a callback function |
| Usable? | ✅ Yes | ✅ Yes |

In modern React (2020+), functional components with `useContext` are preferred. But you'll see `Context.Consumer` in older codebases, so it's important to know both.

---

## 16. Context.Provider – Overriding Default Values

### 🎤 Interview Question:
**"What is Context.Provider? How do you use it to update the value of a context?"**

### 💬 Conversational Answer:

When you first create a context with `createContext({ loggedInUser: "Default User" })`, the default value you pass in is only used when a component tries to read the context but **is not inside a Provider**. Think of it as a fallback.

In a real app, you want to supply actual data (like the real logged-in user from your auth system) to the context. You do this with `Context.Provider`.

**How to set up a Provider:**

The Provider wraps a portion of your component tree. Every component inside the Provider (at any depth) will receive the Provider's value — not the default value.

```javascript
// app.js — the root of the app
import React, { useState, useEffect } from "react";
import UserContext from "./utils/UserContext";

const AppLayout = () => {
  const [userName, setUserName] = useState("Default User");

  useEffect(() => {
    // Simulating an API call for authentication
    const fetchUserData = async () => {
      const data = await fetch("/api/auth");
      const json = await data.json();
      setUserName(json.data.name); // e.g., "Akshay Saini"
    };
    fetchUserData();
  }, []);

  return (
    // Wrap the ENTIRE app in the Provider
    <UserContext.Provider value={{ loggedInUser: userName, setUserName }}>
      <div className="app">
        <Header />
        <Outlet />
      </div>
    </UserContext.Provider>
  );
};
```

**What's happening here:**
1. `AppLayout` has a state variable `userName`
2. On mount, it simulates an API call and sets `userName` to "Akshay Saini"
3. It wraps the entire app in `UserContext.Provider` and passes `{ loggedInUser: userName }` as the value
4. Every component in the app now gets `loggedInUser = "Akshay Saini"` when they use `useContext(UserContext)`

**Important:** the default value from `createContext()` is now overridden everywhere inside the Provider. The default is only used outside the Provider.

**Partial wrapping — scoping the provider:**

You can also wrap just a part of your app:

```javascript
// Only Header gets the special value
<UserContext.Provider value={{ loggedInUser: "Elon Musk" }}>
  <Header />
</UserContext.Provider>
<Body /> {/* Body gets the outer Provider's value (or default if no outer provider) */}
```

This lets you scope context values to specific parts of your app.

---

## 17. Nested Providers – Scope of Context Values

### 🎤 Interview Question:
**"What happens when you nest multiple Context.Providers with different values?"**

### 💬 Conversational Answer:

You can absolutely nest providers, and each one overrides the context value for the components inside it. This is valid React code and a powerful feature.

**Example:**

```javascript
// Outer provider wraps the whole app
<UserContext.Provider value={{ loggedInUser: "Akshay Saini" }}>
  
  {/* Inner provider wraps only the Header */}
  <UserContext.Provider value={{ loggedInUser: "Elon Musk" }}>
    <Header />   {/* ← gets "Elon Musk" */}
  </UserContext.Provider>
  
  <Body />       {/* ← gets "Akshay Saini" from outer provider */}
  
</UserContext.Provider>

// Components outside ALL providers get the createContext default value
```

**The rules of context value precedence:**

1. If a component is **outside all Providers** → gets the `createContext()` default value
2. If a component is **inside an outer Provider** → gets that Provider's value
3. If a component is **inside a nested inner Provider** → gets the inner Provider's value (nearest ancestor wins)

**What you'll see:**
- Header → "Elon Musk" (from inner provider)
- Body, RestaurantCard, ItemList → "Akshay Saini" (from outer provider)

This is advanced behavior, but it's useful when you want to override context values for a specific sub-tree of your app (like showing a different theme in a modal, or a different user in an admin section).

---

## 18. Updating Context Dynamically – Connecting State to Context

### 🎤 Interview Question:
**"How do you dynamically update a React context value and have it reflect everywhere in the app?"**

### 💬 Conversational Answer:

The key insight is: **tie your context value to a state variable**. When the state changes, the context value changes, and everything subscribed to that context re-renders.

In the example, we built a live input box on the home page. When you type in it, the header, all cards, and the About page all update simultaneously — because all of them are reading from the same context.

**Step 1 — Pass both the value AND the setter through context:**

```javascript
// app.js
const AppLayout = () => {
  const [userName, setUserName] = useState("Akshay Saini");

  return (
    // Pass both loggedInUser AND setUserName in the context value
    <UserContext.Provider value={{ loggedInUser: userName, setUserName }}>
      <Header />
      <Outlet />
    </UserContext.Provider>
  );
};
```

**Step 2 — In the component that needs to update it, extract setUserName from context:**

```javascript
// Body.js
import { useContext } from "react";
import UserContext from "../utils/UserContext";

const Body = () => {
  // Extract both the value and the setter from context
  const { loggedInUser, setUserName } = useContext(UserContext);

  return (
    <div>
      {/* Input box to live-update the context */}
      <div>
        <label>Username: </label>
        <input
          className="border border-black p-2"
          value={loggedInUser}
          onChange={(e) => setUserName(e.target.value)} // updates context on every keystroke
        />
      </div>

      {/* rest of the body */}
    </div>
  );
};
```

**Step 3 — Every component reading this context updates automatically:**

Header, RestaurantCard, About — any component that reads `loggedInUser` via `useContext(UserContext)` will re-render the moment you type in the input box.

**The amazing thing shown live in the video:**

The About page was lazy-loaded — meaning its code wasn't even downloaded yet when the home page loaded. But when you changed the username on the home page and then clicked About, the About page loaded AND showed the updated username (e.g., "Sachin Tendulkar"). Context is a global space — when About loads, it reads from the global context and finds the updated value.

**Why this works:**

You tied `userName` state to the context value: `value={{ loggedInUser: userName }}`. When `setUserName()` is called, React updates `userName`, which updates the context value, which triggers re-renders in every component using that context. React handles all of this efficiently and performantly.

---

## 19. Context vs Redux – When to Use What

### 🎤 Interview Question:
**"What is the difference between React Context and Redux? When should you use each?"**

### 💬 Conversational Answer:

This is an extremely common interview question.

**React Context:**
- Built into React — no installation needed
- Creates a global space for data that can be accessed anywhere
- Simple API: `createContext`, `useContext`, `Context.Provider`
- Works well for small to medium-sized applications
- Can create multiple contexts: `UserContext`, `ThemeContext`, `CartContext`, etc.
- No middleware, no extra tooling

**Redux:**
- An **external library** — needs `npm install redux react-redux`
- Also creates a global store
- Has extra concepts: actions, reducers, dispatch, middleware (Redux Thunk for async)
- More boilerplate code to set up
- Best for large-scale applications where state management gets complex
- Has powerful developer tools (time-travel debugging, action history)
- The **modern way** is Redux Toolkit (RTK) — reduces the boilerplate significantly

**The honest comparison:**

| | Context | Redux |
|---|---|---|
| Installation | None (built-in) | npm install |
| Learning curve | Low | Higher |
| Boilerplate | Minimal | More (less with RTK) |
| Best for | Small to medium apps | Large-scale apps |
| Middleware support | No | Yes (Thunk, Saga, etc.) |
| Dev tools | React DevTools | Dedicated Redux DevTools |

**A common mistake:**

Many beginners jump straight to Redux for every project. This is wrong. If you're building a small or medium-sized app, Context is perfectly capable. You can even build large applications with Context — it scales well. Redux becomes valuable when:
- Your state management logic gets very complex
- You need middleware for async operations
- You want the Redux DevTools for debugging
- Your team is already using Redux as a standard

**What the instructor said:** "I want you to know context deeply before jumping to Redux. If you know context, you know how to manage data. Redux does the same thing — just in a more structured, scalable way. In the next episode, we'll cover Redux Toolkit."

---

## 20. Coding Questions

---

### Q1. Write a complete Higher Order Component: withPromotedLabel

```javascript
// RestaurantCard.js
import { CDN_URL } from "../utils/constants";

// Base component — no changes
const RestaurantCard = (props) => {
  const { resData } = props;
  const { name, cuisines, avgRating } = resData?.info;
  return (
    <div className="res-card w-[250px] m-4 p-4 rounded-lg bg-gray-100">
      <img
        className="rounded-lg"
        src={CDN_URL + resData?.info?.cloudinaryImageId}
        alt={name}
      />
      <h3 className="font-bold text-lg py-2">{name}</h3>
      <h4>{cuisines.join(", ")}</h4>
      <h4>{avgRating} ⭐</h4>
    </div>
  );
};

// HOC — takes RestaurantCard, returns enhanced version with Promoted label
export const withPromotedLabel = (WrappedComponent) => {
  return (props) => {
    return (
      <div>
        <label className="absolute bg-black text-white m-2 p-2 rounded-lg text-sm">
          Promoted
        </label>
        {/* Spread all props through to the original component — do NOT modify it */}
        <WrappedComponent {...props} />
      </div>
    );
  };
};

export default RestaurantCard;
```

---

### Q2. Use the HOC in Body — show promoted card for promoted restaurants

```javascript
// Body.js
import RestaurantCard, { withPromotedLabel } from "./RestaurantCard";

// Create the enhanced version using the HOC
const RestaurantCardPromoted = withPromotedLabel(RestaurantCard);

const Body = () => {
  return (
    <div className="flex flex-wrap justify-center">
      {restaurants.map((restaurant) =>
        restaurant?.info?.promoted ? (
          // Promoted restaurant → show enhanced card
          <RestaurantCardPromoted
            key={restaurant.info.id}
            resData={restaurant}
          />
        ) : (
          // Normal restaurant → show plain card
          <RestaurantCard
            key={restaurant.info.id}
            resData={restaurant}
          />
        )
      )}
    </div>
  );
};
```

---

### Q3. Build a basic accordion (uncontrolled — manages own state)

```javascript
// RestaurantCategory.js — uncontrolled version
import { useState } from "react";
import ItemList from "./ItemList";

const RestaurantCategory = ({ data }) => {
  const [showItems, setShowItems] = useState(false);

  return (
    <div className="w-6/12 mx-auto my-4 bg-gray-50 shadow-lg p-4">
      {/* Header — click to toggle */}
      <div
        className="flex justify-between cursor-pointer"
        onClick={() => setShowItems(!showItems)}
      >
        <span className="font-bold text-lg">
          {data.title} ({data.itemCards.length})
        </span>
        <span>⬇️</span>
      </div>

      {/* Body — only shown when showItems is true */}
      {showItems && <ItemList items={data.itemCards} />}
    </div>
  );
};

export default RestaurantCategory;
```

---

### Q4. Lift state up — only one accordion open at a time (controlled)

```javascript
// RestaurantMenu.js (parent — controls which accordion is open)
import { useState } from "react";
import RestaurantCategory from "./RestaurantCategory";

const RestaurantMenu = () => {
  const [showIndex, setShowIndex] = useState(0); // 0 = first accordion open by default
  // Use null to have all collapsed initially

  return (
    <div>
      {categories.map((category, index) => (
        <RestaurantCategory
          key={category?.card?.card?.title}
          data={category?.card?.card}
          showItems={index === showIndex}       // only matching index is true
          setShowIndex={() => setShowIndex(index)} // setter for this specific index
        />
      ))}
    </div>
  );
};
```

```javascript
// RestaurantCategory.js (child — controlled, no internal state)
const RestaurantCategory = ({ data, showItems, setShowIndex }) => {
  return (
    <div className="w-6/12 mx-auto my-4 bg-gray-50 shadow-lg p-4">
      <div
        className="flex justify-between cursor-pointer"
        onClick={setShowIndex} // call parent's function on click
      >
        <span className="font-bold text-lg">
          {data.title} ({data.itemCards.length})
        </span>
        <span>{showItems ? "⬆️" : "⬇️"}</span>
      </div>

      {showItems && <ItemList items={data.itemCards} />}
    </div>
  );
};
```

---

### Q5. Filter menu data to get only item categories

```javascript
// RestaurantMenu.js
const RestaurantMenu = () => {
  const resInfo = useRestaurantMenu(resId);

  if (resInfo === null) return <Shimmer />;

  const categories = resInfo?.cards?.filter(
    (c) =>
      c?.card?.card?.["@type"] ===
      "type.googleapis.com/swiggy.presentation.food.v2.ItemCategory"
    // Note: bracket notation ["@type"] is needed because @ is not valid in dot notation
  );

  return (
    <div>
      {categories.map((category, index) => (
        <RestaurantCategory
          key={category?.card?.card?.title}
          data={category?.card?.card}
        />
      ))}
    </div>
  );
};
```

---

### Q6. Handle inconsistent API data — price vs defaultPrice

```javascript
// ItemList.js — handle both price fields
const ItemList = ({ items }) => {
  return (
    <div>
      {items.map((item) => {
        const info = item?.card?.info;

        // Some items have "price", some have "defaultPrice"
        // Both are in paise (divide by 100 to get rupees)
        const price = info?.price
          ? info.price / 100
          : info?.defaultPrice / 100;

        return (
          <div key={info?.id} className="border-b p-2 flex justify-between">
            <div className="w-9/12">
              <span className="font-semibold">{info?.name}</span>
              <span className="px-2">₹{price}</span>
              <p className="text-xs text-gray-500">{info?.description}</p>
            </div>
            <div className="w-3/12">
              <img
                src={CDN_URL + info?.imageId}
                className="w-full"
                alt={info?.name}
              />
            </div>
          </div>
        );
      })}
    </div>
  );
};
```

---

### Q7. Create a UserContext

```javascript
// utils/UserContext.js
import { createContext } from "react";

// Pass default value to createContext
// This is used when a component accesses context OUTSIDE any Provider
const UserContext = createContext({
  loggedInUser: "Default User",
});

export default UserContext;
```

---

### Q8. Read context in a functional component using useContext

```javascript
// Header.js
import { useContext } from "react";
import UserContext from "../utils/UserContext";

const Header = () => {
  // useContext gives you the current context value
  const { loggedInUser } = useContext(UserContext);

  return (
    <div className="header">
      <nav>
        <ul>
          <li>Home</li>
          <li>About Us</li>
          <li className="font-bold">{loggedInUser}</li> {/* from context */}
        </ul>
      </nav>
    </div>
  );
};
```

---

### Q9. Read context in a class component using Context.Consumer

```javascript
// About.js (class-based)
import React from "react";
import UserContext from "../utils/UserContext";

class About extends React.Component {
  render() {
    return (
      <div>
        <h1>About Us</h1>

        {/* UserContext.Consumer wraps the JSX that needs context */}
        <UserContext.Consumer>
          {/* Must be a function — React calls it with the context value */}
          {(data) => (
            <h2 className="font-bold text-xl">
              Logged in as: {data.loggedInUser}
            </h2>
          )}
        </UserContext.Consumer>
      </div>
    );
  }
}

export default About;
```

---

### Q10. Provide context with Provider and connect it to state

```javascript
// app.js — wrapping the app in Provider and connecting context to state
import React, { useState, useEffect } from "react";
import UserContext from "./utils/UserContext";

const AppLayout = () => {
  const [userName, setUserName] = useState("");

  useEffect(() => {
    // Simulate authentication API call
    const data = { name: "Akshay Saini" }; // pretend this came from an API
    setUserName(data.name);
  }, []);

  return (
    // Provide BOTH the value and the setter
    // Any component can now read loggedInUser OR call setUserName
    <UserContext.Provider value={{ loggedInUser: userName, setUserName }}>
      <div className="app">
        <Header />
        <Outlet />
      </div>
    </UserContext.Provider>
  );
};
```

---

### Q11. Update context dynamically using an input box

```javascript
// Body.js — live update context from an input field
import { useContext } from "react";
import UserContext from "../utils/UserContext";

const Body = () => {
  // Extract both the current value AND the setter from context
  const { loggedInUser, setUserName } = useContext(UserContext);

  return (
    <div>
      <div className="m-4">
        <label>Username: </label>
        <input
          className="border border-black p-2"
          value={loggedInUser}              // controlled input — value from context
          onChange={(e) => setUserName(e.target.value)} // update context on every keystroke
        />
      </div>

      {/* rest of body content */}
    </div>
  );
};
```

When you type, `setUserName` updates the state in `AppLayout` → the Provider value changes → every component reading `loggedInUser` re-renders with the new value. All in real time.

---

### Q12. Nested Providers — scope context values to specific sub-trees

```javascript
// Outer provider: whole app gets "Akshay Saini"
// Inner provider: only Header gets "Elon Musk"

<UserContext.Provider value={{ loggedInUser: "Akshay Saini" }}>
  
  {/* Inner override — only Header sees "Elon Musk" */}
  <UserContext.Provider value={{ loggedInUser: "Elon Musk" }}>
    <Header />
  </UserContext.Provider>

  {/* Everything else sees "Akshay Saini" from the outer provider */}
  <Body />
  <Footer />

</UserContext.Provider>
```

---

## 🗒️ Quick Revision Card

| Concept | Key Point |
|---|---|
| UI Layer | JSX, components — what you see on screen; static by itself |
| Data Layer | state, props, local variables, API data — powers the UI layer |
| Senior vs Junior | Senior engineers are excellent at managing the data layer |
| HOC definition | A function that takes a component and returns an enhanced component |
| HOC naming | Prefix with `with` → `withPromotedLabel`, `withAuth`, `withLogger` |
| HOC pure function | Never modify the input component; just wrap it and add to it |
| `{...props}` in HOC | Spread operator passes all received props to the wrapped component |
| Promoted label feature | Use HOC to add a badge to some cards based on API `promoted` flag |
| Accordion | Header (clickable) + Body (conditionally shown based on state) |
| Toggle pattern | `setShowItems(!showItems)` — flips true↔false on each click |
| Bracket notation | Use `obj["@type"]` when property name has special characters like `@` |
| `@type` filter | Filter API cards by `"type.googleapis.com/..."` to get only categories |
| price vs defaultPrice | Swiggy API inconsistency — handle with ternary: use price if exists, else defaultPrice |
| Prices in paise | Divide by 100 to convert to rupees |
| Uncontrolled component | Manages its own state internally; parent doesn't control it |
| Controlled component | Parent controls it via props; child has no internal state for that behavior |
| Lifting state up | Moving state from child to parent so parent can coordinate multiple children |
| When to lift state | When multiple siblings need to share/coordinate state |
| Child to parent communication | Parent passes a callback function as prop; child calls it |
| React DevTools | Chrome extension — adds Components and Profiler tabs to DevTools |
| Components tab | Shows component tree + real-time data layer (props and state) of each component |
| Profiler tab | Records render activity — shows which components re-rendered and how long they took |
| Props drilling | Passing props through intermediate components that don't need it |
| Problem with props drilling | Clutters code; makes refactoring painful; intermediate components become middlemen |
| React Context | A global space to store data that any component can access without prop drilling |
| `createContext()` | Creates the context object; takes a default value |
| `useContext()` | Hook used in functional components to read context value |
| `Context.Consumer` | Component used in class components to read context value; takes a callback function |
| `Context.Provider` | Wraps components; overrides default context value for everything inside it |
| Context default value | Used only when component is OUTSIDE any Provider |
| Nested providers | Inner provider's value overrides outer provider's value for components inside it |
| Tying context to state | Pass state variable as context value — when state changes, context updates everywhere |
| Passing setter in context | Pass `setUserName` through context so any component can update it |
| Context scope | You can provide to whole app OR just a sub-tree |
| Multiple contexts | Can create `UserContext`, `ThemeContext`, `CartContext` — one for each concern |
| Context vs Redux | Context: built-in, simple, great for small/medium apps; Redux: external, scalable, with middleware, better for large apps |
| Redux extras | Redux Thunk (middleware), time-travel debugging, action history |
| Modern Redux | Redux Toolkit (RTK) — less boilerplate than classic Redux |
| Common mistake | Using Redux for small apps — Context is sufficient and simpler |
| Context with lazy loading | Even lazy-loaded components get updated context values when they load |

---

*Episode 11 – Namaste React Series | Data is the New Oil*
