# Episode 04 – Talk is Cheap, Show Me the Code!
### React Interview Prep + Coding Questions

---

## Table of Contents

1. [Planning Before Coding – Why It Matters](#1-planning-before-coding)
2. [App Structure – Wireframe and Component Tree](#2-app-structure)
3. [Building the AppLayout Component](#3-building-the-applayout-component)
4. [Building the Header Component](#4-building-the-header-component)
5. [Building the Body and RestaurantCard Component](#5-building-the-body-and-restaurantcard)
6. [CSS in React – Three Ways](#6-css-in-react)
7. [Inline Styles in JSX – The Double Curly Brace Syntax](#7-inline-styles-in-jsx)
8. [What are Props?](#8-what-are-props)
9. [Passing Props to a Component](#9-passing-props-to-a-component)
10. [Destructuring Props](#10-destructuring-props)
11. [Real-World API Data – How It Looks](#11-real-world-api-data)
12. [Config Driven UI – Very Important Concept](#12-config-driven-ui)
13. [UI Layer vs Data Layer](#13-ui-layer-vs-data-layer)
14. [Rendering Dynamic Lists with .map()](#14-rendering-dynamic-lists-with-map)
15. [Keys in React – Why They Are Essential](#15-keys-in-react)
16. [Index as Key – Why It's a Bad Practice](#16-index-as-key--bad-practice)
17. [Coding Questions](#17-coding-questions)

---

## 1. Planning Before Coding

### 🎤 Interview Question:
**"What is your approach when asked to build a new React application from scratch?"**

### 💬 Conversational Answer:

The biggest mistake developers make is opening their code editor and just starting to type. That's how you end up with messy, hard-to-maintain code. Senior engineers always **plan first**.

Here's the step-by-step approach:

**Step 1: Understand what you're building.**
Take a moment to look at a similar app (like Swiggy or Zomato for a food ordering app) and understand what features exist. Don't copy — just understand the mental model.

**Step 2: Draw a wireframe (even on paper).**
A wireframe is just a rough sketch of how your page should look. You don't need design tools. A pen and paper works perfectly. For a food ordering app, you'd sketch:
- A header at the top (logo on left, nav links on right)
- A body in the middle (search bar + restaurant cards)
- A footer at the bottom (links, copyright)

**Step 3: Identify your components.**
Once you have the wireframe, ask yourself: "What are the repeating or reusable pieces of this UI?" Each piece becomes a component.

For our food app:
- `AppLayout` → the top-level container
- `Header` → logo + nav items
- `Body` → search + restaurant container
- `RestaurantCard` → one card for one restaurant
- `Footer` → copyright, links

**Step 4: Code it one component at a time.**
Start from the top-level component and work your way down. Don't try to build everything at once.

**Why planning matters:** When you plan well, coding becomes mechanical. You already know what to build — you just have to build it. Without planning, you're guessing while coding, which leads to rework.

---

## 2. App Structure

### 🎤 Interview Question:
**"How would you break down a food ordering app into components?"**

### 💬 Conversational Answer:

Think of the app like a tree. The root is your main `AppLayout` component, and everything else hangs off it like branches.

```
AppLayout
├── Header
│   ├── Logo (img tag)
│   └── NavItems (ul > li: Home, About, Contact, Cart)
├── Body
│   ├── Search (input + button)
│   └── RestaurantContainer
│       ├── RestaurantCard
│       ├── RestaurantCard
│       └── RestaurantCard (... many more)
└── Footer
    ├── CopyrightText
    └── Links
```

This is called a **component tree**. When you have this in your head (or on paper), coding is just a matter of translating each box into a functional component.

**Why is this structure good?**
- Each component has one clear responsibility (single responsibility principle)
- `RestaurantCard` can be reused — you write it once and render it many times with different data
- Easy to debug — if something breaks in the header, you only look at the Header component

---

## 3. Building the AppLayout Component

### 🎤 Interview Question:
**"What is the top-level or root component in React? How do you structure it?"**

### 💬 Conversational Answer:

The top-level component is like the container for your entire app. Every other component lives inside it. We usually call it `App`, `AppLayout`, or `Root`.

```javascript
const AppLayout = () => {
  return (
    <div className="app">
      <Header />
      <Body />
      <Footer />
    </div>
  );
};

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(<AppLayout />);
```

This is component composition in action — `Header`, `Body`, and `Footer` are all separate components composed inside `AppLayout`. This makes the code clean, readable, and easy to manage.

---

## 4. Building the Header Component

### 🎤 Interview Question:
**"How do you build a header with a logo and navigation links in React?"**

### 💬 Conversational Answer:

A header is a functional component that returns JSX. It usually contains a logo image and a list of navigation links.

```javascript
const Header = () => {
  return (
    <div className="header">
      {/* Logo section */}
      <div className="logo-container">
        <img className="logo" src="https://your-logo-url.png" alt="Namaste Food Logo" />
      </div>

      {/* Navigation links */}
      <div className="nav-items">
        <ul>
          <li>Home</li>
          <li>About Us</li>
          <li>Contact Us</li>
          <li>Cart</li>
        </ul>
      </div>
    </div>
  );
};
```

**CSS to make it look like a real header:**

```css
.header {
  display: flex;
  justify-content: space-between;
  border-bottom: 1px solid black;
  padding: 0 20px;
}

.logo {
  width: 200px;
}

.nav-items ul {
  display: flex;
  list-style-type: none;
}

.nav-items ul li {
  padding: 10px;
  margin: 10px;
  font-size: 20px;
}
```

**Common mistake:** Writing `class="header"` instead of `className="header"` in JSX. Remember — JSX is not HTML. Use `className`, not `class`.

---

## 5. Building the Body and RestaurantCard

### 🎤 Interview Question:
**"How do you build a reusable card component in React?"**

### 💬 Conversational Answer:

A card component is a perfect example of reusability. Instead of writing the same HTML 15 times for 15 restaurants, you write it once and reuse it.

**The Body component:**

```javascript
const Body = () => {
  return (
    <div className="body">
      <div className="search">Search</div>
      <div className="rest-container">
        <RestaurantCard />
        <RestaurantCard />
        <RestaurantCard />
      </div>
    </div>
  );
};
```

**The RestaurantCard component (hardcoded first, dynamic later):**

```javascript
const RestaurantCard = () => {
  return (
    <div className="rest-card">
      <img
        className="res-logo"
        src="https://restaurant-image-url.jpg"
        alt="Restaurant Logo"
      />
      <h3>Meghna Foods</h3>
      <h4>Biryani, North Indian, Asian</h4>
      <h4>4.4 stars</h4>
      <h4>38 minutes</h4>
    </div>
  );
};
```

**Why create a separate component for the card?**
The same reason we create functions in programming — to avoid repeating code. You write `RestaurantCard` once. Then you reuse it 15 times, 50 times, 100 times with different data by passing **props**.

---

## 6. CSS in React – Three Ways

### 🎤 Interview Question:
**"What are the different ways to apply CSS in a React application?"**

### 💬 Conversational Answer:

There are **three main ways** to write CSS in React:

**Way 1: External CSS file (most common)**

Create a `.css` file and import it.

```css
/* index.css */
.rest-card {
  width: 200px;
  border: 1px solid black;
  padding: 10px;
  margin: 5px;
}

.rest-card:hover {
  border: 2px solid black;
  cursor: pointer;
}
```

```javascript
// app.js
import "./index.css";
```

**Way 2: Inline styles using a JavaScript object**

```javascript
const RestaurantCard = () => {
  return (
    <div style={{ backgroundColor: "#f0f0f0", padding: "10px" }}>
      <h3>Restaurant Name</h3>
    </div>
  );
};
```

Note: The double curly braces are not a special React thing — the outer `{}` is for JavaScript expression in JSX, and the inner `{}` is the JavaScript object for styles.

**Way 3: CSS frameworks like Tailwind, Material UI, etc.**

We'll cover Tailwind in a dedicated episode later. It lets you write CSS using pre-defined class names directly in your JSX.

**Which is preferred?**

Inline styles are the least preferred — they're hard to read and can't be reused easily. External CSS files or CSS frameworks are the standard in production apps.

---

## 7. Inline Styles in JSX – The Double Curly Brace Syntax

### 🎤 Interview Question:
**"Why do we write two sets of curly braces when giving inline styles in JSX? Like `style={{ color: 'red' }}`?"**

### 💬 Conversational Answer:

This confuses almost every beginner. Let's break it down:

In JSX, whenever you want to write JavaScript, you use `{ }`.

The `style` attribute in JSX doesn't take a CSS string like in HTML. It takes a **JavaScript object**. So you have:

- First `{` → "I'm going to write some JavaScript here"
- Second `{` → "This is a JavaScript object"
- `}` → closes the object
- `}` → closes the JavaScript expression

```jsx
{/* This is how it works: */}
<div style={{ backgroundColor: "red", fontSize: "20px" }}>
  Hello
</div>

{/* Equivalent to: */}
const styleObject = { backgroundColor: "red", fontSize: "20px" };
<div style={styleObject}>Hello</div>
```

Also notice: CSS property names become **camelCase** in the style object. `background-color` becomes `backgroundColor`, `font-size` becomes `fontSize`, etc.

```javascript
// HTML way (string):
<div style="background-color: red; font-size: 20px;">

// JSX way (object):
<div style={{ backgroundColor: "red", fontSize: "20px" }}>
```

---

## 8. What are Props?

### 🎤 Interview Question:
**"What are props in React? Explain with a simple analogy."**

### 💬 Conversational Answer:

Props are one of the most fundamental concepts in React. And the simplest way to understand them is:

**Props are just arguments to a function.**

Remember: A functional component is just a JavaScript function. So when you "pass props to a component", you're literally "passing arguments to a function."

```javascript
// Normal function with arguments:
function greet(name, age) {
  console.log(`Hello ${name}, you are ${age} years old.`);
}
greet("Kabir", 22);

// React component with props:
const GreetCard = (props) => {
  return <h1>Hello {props.name}, you are {props.age} years old.</h1>;
};
<GreetCard name="Kabir" age={22} />
```

React takes all the things you write inside the component tag (`name="Kabir"`, `age={22}`) and **wraps them all into one JavaScript object** called `props`. Then it passes that `props` object to your function.

**Key things to remember:**
- Props are read-only — you cannot modify props inside the component
- Props flow **one direction** — from parent component to child component (top → down)
- A component can receive any number of props
- The `props` object in the function receives all of them combined

---

## 9. Passing Props to a Component

### 🎤 Interview Question:
**"How do you make a component dynamic using props? Show with an example."**

### 💬 Conversational Answer:

Without props, every `RestaurantCard` shows the same data (hardcoded). With props, you can pass different data to each card and make them unique.

**Without props (bad — hardcoded, not reusable):**

```javascript
const RestaurantCard = () => {
  return (
    <div className="rest-card">
      <h3>Meghna Foods</h3>
      <h4>Biryani, North Indian</h4>
      <h4>4.4 stars</h4>
    </div>
  );
};

// In body:
<RestaurantCard />   {/* Always shows "Meghna Foods" */}
<RestaurantCard />   {/* Always shows "Meghna Foods" */}
```

**With props (good — dynamic and reusable):**

```javascript
const RestaurantCard = (props) => {
  return (
    <div className="rest-card">
      <h3>{props.resName}</h3>
      <h4>{props.cuisine}</h4>
      <h4>{props.rating} stars</h4>
    </div>
  );
};

// In body — each card gets different data:
<RestaurantCard resName="Meghna Foods" cuisine="Biryani, North Indian" rating="4.4" />
<RestaurantCard resName="KFC" cuisine="Burger, Fast Food" rating="4.1" />
```

You can also pass a whole JavaScript object as a prop:

```javascript
const restaurantData = {
  name: "KFC",
  cuisine: "Burger, Fast Food",
  rating: 4.1
};

<RestaurantCard resData={restaurantData} />

// Inside the component:
const RestaurantCard = (props) => {
  const { name, cuisine, rating } = props.resData;
  return (
    <div>
      <h3>{name}</h3>
      <h4>{cuisine}</h4>
    </div>
  );
};
```

---

## 10. Destructuring Props

### 🎤 Interview Question:
**"What does it mean to destructure props? Why do developers do it?"**

### 💬 Conversational Answer:

When you receive props, accessing them as `props.resName`, `props.cuisine`, `props.rating` every single time gets repetitive and hard to read.

**Destructuring** lets you pull out the values you need from the object in one clean line.

**Without destructuring (messy):**

```javascript
const RestaurantCard = (props) => {
  return (
    <div>
      <h3>{props.resData.name}</h3>
      <h4>{props.resData.cuisine}</h4>
      <h4>{props.resData.rating}</h4>
      <h4>{props.resData.deliveryTime}</h4>
    </div>
  );
};
```

**With destructuring inside the function body (cleaner):**

```javascript
const RestaurantCard = (props) => {
  const { name, cuisine, rating, deliveryTime } = props.resData;
  return (
    <div>
      <h3>{name}</h3>
      <h4>{cuisine}</h4>
      <h4>{rating}</h4>
      <h4>{deliveryTime}</h4>
    </div>
  );
};
```

**Destructuring directly in the function parameters (most concise):**

```javascript
// React wraps all props into an object and passes it.
// You can destructure that object right at the parameter level:
const RestaurantCard = ({ resName, cuisine, rating }) => {
  return (
    <div>
      <h3>{resName}</h3>
      <h4>{cuisine}</h4>
      <h4>{rating}</h4>
    </div>
  );
};
```

All three styles produce the same result. This is just JavaScript object destructuring — not a React-specific feature. Many developers do it on the fly at the parameter level. Either style is fine, but be consistent.

---

## 11. Real-World API Data – How It Looks

### 🎤 Interview Question:
**"How does real-world data from an API look compared to hardcoded data? How do you work with it?"**

### 💬 Conversational Answer:

In a real app like Swiggy, data comes from a backend API as JSON. This data is much more complex than simple hardcoded values. Let's look at what a real restaurant object might contain:

```json
{
  "data": {
    "id": "371662",
    "name": "KFC",
    "cloudinaryImageId": "some-image-id",
    "avgRating": 3.8,
    "cuisines": ["Burger", "Fast Food"],
    "costForTwo": 40000,
    "deliveryTime": 36,
    "locality": "Kormangala",
    "isVeg": false
  }
}
```

**Notice a few things from the Swiggy API:**

1. `costForTwo` is `40000` (in paise, not rupees). You divide by 100 to get ₹400. This is how you use JavaScript inside JSX: `{resData.data.costForTwo / 100}`.

2. `cuisines` is an **array** — `["Burger", "Fast Food"]`. To display it as comma-separated text, you use `.join(", ")`: `{resData.data.cuisines.join(", ")}`.

3. Images are not hosted on the app's server — they're on a **CDN** (Content Delivery Network) called Cloudinary. You build the full URL by concatenating the base CDN URL with the `cloudinaryImageId`.

```javascript
const CDN_URL = "https://res.cloudinary.com/swiggy/image/upload/";

const RestaurantCard = (props) => {
  const { name, avgRating, cuisines, costForTwo, deliveryTime, cloudinaryImageId } =
    props.resData.data;

  return (
    <div className="rest-card">
      <img
        src={CDN_URL + cloudinaryImageId}
        alt={name}
      />
      <h3>{name}</h3>
      <h4>{cuisines.join(", ")}</h4>
      <h4>{avgRating} stars</h4>
      <h4>₹{costForTwo / 100} for two</h4>
      <h4>{deliveryTime} minutes</h4>
    </div>
  );
};
```

**Tip for reading API data:** Install the **JSON Viewer** Chrome extension. It formats raw JSON in the browser into a beautiful, readable, collapsible tree. Very useful when exploring APIs.

---

## 12. Config Driven UI – Very Important Concept

### 🎤 Interview Question:
**"What is Config Driven UI? Where is it used in the industry?"**

### 💬 Conversational Answer:

This is one of the most important concepts in frontend system design interviews. Almost every large company — Amazon, Flipkart, Swiggy, Uber — uses config-driven UI.

**What is it?**

Config Driven UI means your **UI is controlled by data (config) coming from the backend**, not hardcoded in the frontend.

**Real example from Swiggy:**

When you open Swiggy in Bangalore, you see certain offer banners and carousels. When someone in Delhi opens it, they see different banners. Same app. Different UI. How?

The backend sends different data (config) based on the user's location. The frontend just renders whatever it receives — no code change needed.

```
Backend API (Bangalore) → { carousel: [...offer1, offer2], restaurants: [...] }
Backend API (Delhi)     → { carousel: [...offer3, offer4], restaurants: [...] }
```

The frontend code is the same. Only the data changes. The UI adapts.

**Why is this powerful?**

- You don't need to deploy code changes to run a campaign or show different banners in different cities.
- The backend team controls what the UI shows by sending different configs.
- If a section's data is empty, the UI section simply doesn't render.

**How does the Swiggy API show this?**

In Swiggy's real API response, they send `cards` with different `card_type` values like `"carousel"`, `"seeAllRestaurants"`, etc. The frontend reads the card type and renders the appropriate UI.

```json
{
  "cards": [
    { "card_type": "carousel", "data": [...] },
    { "card_type": "seeAllRestaurants", "data": [...] }
  ]
}
```

This way, swiggy can show carousels in one city and skip them in another — just by changing the API response.

**Interview tip:** When asked about frontend architecture or large-scale UI systems, mention config-driven UI. Say: "Our UI was config-driven — the backend controlled what sections rendered and in what order, so the product team could run A/B tests or location-based campaigns without any code deployments."

---

## 13. UI Layer vs Data Layer

### 🎤 Interview Question:
**"What is the difference between the UI layer and data layer in a React application?"**

### 💬 Conversational Answer:

A frontend React application has two distinct layers working together:

| Layer | What it is | Your job |
|---|---|---|
| **UI Layer** | The visual part — what the user sees. Built using JSX, components, CSS. | Write good components, clean UI |
| **Data Layer** | The data that powers the UI — API responses, JSON, state. | Understand data shapes, know how to work with APIs |

**Why both matter:**

A lot of frontend developers only think about the UI — making things look pretty. But a **senior frontend developer** must understand the data layer too:
- What does the API send?
- Is the data structure good or bad?
- Why is the backend sending redundant fields?
- How should the data be shaped for the frontend to use easily?

In the episode, the instructor noticed that Swiggy's API sent both `costForTwo` (a number: `40000`) and `costForTwoString` (a string: `"₹400 for two"`). These are redundant — you can generate the string from the number. A good developer notices and questions this.

**Takeaway:** You become a valuable engineer when you can discuss both what the UI should look like and how the data should be structured.

---

## 14. Rendering Dynamic Lists with .map()

### 🎤 Interview Question:
**"How do you render a list of components dynamically in React?"**

### 💬 Conversational Answer:

In real apps, you never know how many items will come from the API. Today there might be 15 restaurants; tomorrow there could be 50. You can't hardcode 50 `<RestaurantCard />` tags.

The solution: use JavaScript's **`.map()`** function inside JSX.

**The wrong way (hardcoded):**

```jsx
<div className="rest-container">
  <RestaurantCard resData={restaurantList[0]} />
  <RestaurantCard resData={restaurantList[1]} />
  <RestaurantCard resData={restaurantList[2]} />
  {/* What if there are 100? You can't hardcode this. */}
</div>
```

**The right way (dynamic with .map()):**

```jsx
<div className="rest-container">
  {restaurantList.map((restaurant) => (
    <RestaurantCard key={restaurant.data.id} resData={restaurant} />
  ))}
</div>
```

**How does this work?**

`.map()` loops through every item in `restaurantList`. For each `restaurant` object, it creates a `<RestaurantCard />` and passes that restaurant's data as a prop. The result is an array of JSX elements that React renders on screen.

**Why use `.map()` instead of a `for` loop?**

In React, you almost always use `.map()`, `.filter()`, and `.reduce()` for working with lists. These are **functional programming** methods. They're cleaner, more concise, and they return a value (which you can directly embed in JSX). A `for` loop doesn't return anything — you'd have to push to an array separately, which is messier.

```javascript
// For loop approach (messy):
const cards = [];
for (let i = 0; i < restaurantList.length; i++) {
  cards.push(<RestaurantCard key={i} resData={restaurantList[i]} />);
}
return <div>{cards}</div>;

// .map() approach (clean):
return (
  <div>
    {restaurantList.map((restaurant) => (
      <RestaurantCard key={restaurant.data.id} resData={restaurant} />
    ))}
  </div>
);
```

Both work, but `.map()` is the industry standard in React.

---

## 15. Keys in React – Why They Are Essential

### 🎤 Interview Question:
**"What are keys in React? Why do we need them when rendering lists?"**

### 💬 Conversational Answer:

Keys are a very common interview question, and most developers say "it removes the warning." That's an incomplete answer. Let's understand the real reason.

**What is a key?**

A key is a special prop you give to each item in a list to help React uniquely identify it.

```jsx
{restaurantList.map((restaurant) => (
  <RestaurantCard
    key={restaurant.data.id}   // ← this is the key
    resData={restaurant}
  />
))}
```

**Why does React need keys?**

React has an optimization system called the **reconciliation algorithm** (also called the diffing algorithm). When your data changes, React doesn't re-render the entire page — it only updates what actually changed. This is what makes React fast.

But here's the problem: when you have a list of components (like 15 restaurant cards), and one new card gets added in the middle, React needs to figure out:
- Which cards were already there?
- Which card is new?
- Which cards need to be re-rendered?

**Without keys:**

React can't tell the cards apart. So it takes the "safe" approach — it throws away all 15 cards and re-renders all of them from scratch. This is a huge performance waste, especially with large lists or infinite scroll.

**With unique keys:**

React knows exactly which card is which. If a new card is added in position 3, React knows cards 1, 2, 4, 5, ..., 15 are unchanged. It only renders the one new card. This is a massive performance win.

```
Without keys: New card added → React re-renders ALL 15 cards
With keys:    New card added → React renders ONLY the 1 new card
```

**Visual analogy:**

Imagine 15 students sitting in a class without name tags. The teacher walks in and notices one new student joined. Without name tags, the teacher can't tell who is new — so they have to check every single student. With name tags (unique IDs), the teacher immediately spots the new student.

**Always give a key when using `.map()`.**

---

## 16. Index as Key – Bad Practice

### 🎤 Interview Question:
**"Can we use the array index as the key in React? What are the problems with it?"**

### 💬 Conversational Answer:

Technically, yes — you can use the array index as the key, and React won't throw an error. But React's official documentation itself says: **"We do not recommend using indexes for keys."**

Here's why:

**The index changes when the list changes.**

Suppose you have a list:
- Index 0: KFC
- Index 1: Meghna Foods
- Index 2: Pizza Hut

Now a new restaurant "Burger King" is added at position 0 (top of the list):
- Index 0: Burger King ← NEW
- Index 1: KFC ← was index 0 before
- Index 2: Meghna Foods ← was index 1 before
- Index 3: Pizza Hut ← was index 2 before

React sees that the item at index 0 "changed" (it was KFC, now it's Burger King). It also sees that index 1 "changed" (was Meghna Foods, now KFC). And so on. So React re-renders every card — even though only one new card was added. The index approach defeats the entire purpose of having keys.

A **unique ID** (like `restaurant.data.id`) doesn't change when the list is reordered or updated. React can reliably identify each card.

**Priority for choosing keys:**

```
1. Unique ID from your data (restaurant.data.id) → BEST PRACTICE ✅
2. Array index as key                             → Last resort, not recommended ⚠️
3. No key at all                                  → Never acceptable ❌
```

| Approach | Acceptable? | Why |
|---|---|---|
| Unique ID (`restaurant.data.id`) | ✅ Best practice | Stable, doesn't change |
| Array index | ⚠️ Last resort | Changes when list reorders |
| No key at all | ❌ Never | React throws warning, bad performance |

**If your backend data doesn't have a unique ID — ask your backend developer to add one.** It's important. Every entity in a database has a primary key — use that.

---

## 17. Coding Questions

---

### Q1. Create the full AppLayout with Header, Body, Footer

```javascript
import React from "react";
import ReactDOM from "react-dom/client";

const Header = () => {
  return (
    <div className="header">
      <div className="logo-container">
        <img className="logo" src="logo.png" alt="logo" />
      </div>
      <div className="nav-items">
        <ul>
          <li>Home</li>
          <li>About Us</li>
          <li>Contact Us</li>
          <li>Cart</li>
        </ul>
      </div>
    </div>
  );
};

const Body = () => {
  return (
    <div className="body">
      <div className="search">Search</div>
      <div className="rest-container">
        <RestaurantCard />
      </div>
    </div>
  );
};

const Footer = () => {
  return (
    <div className="footer">
      <p>© 2024 Namaste Food. All rights reserved.</p>
    </div>
  );
};

const AppLayout = () => {
  return (
    <div className="app">
      <Header />
      <Body />
      <Footer />
    </div>
  );
};

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(<AppLayout />);
```

---

### Q2. Create a RestaurantCard with hardcoded data

```javascript
const RestaurantCard = () => {
  return (
    <div className="rest-card">
      <img
        className="res-logo"
        src="https://biryani-image.jpg"
        alt="restaurant"
      />
      <h3>Meghna Foods</h3>
      <h4>Biryani, North Indian, Asian</h4>
      <h4>4.4 stars</h4>
      <h4>38 minutes</h4>
      <h4>₹300 for two</h4>
    </div>
  );
};
```

---

### Q3. Add inline styles to a card

```javascript
const styleCard = {
  backgroundColor: "#f0f0f0",
  border: "1px solid gray",
  borderRadius: "10px",
  padding: "10px",
  width: "200px",
};

const RestaurantCard = () => {
  return (
    <div style={styleCard}>
      <h3>Meghna Foods</h3>
    </div>
  );
};

// OR inline directly (double curly brace):
const RestaurantCard2 = () => {
  return (
    <div style={{ backgroundColor: "#f0f0f0", padding: "10px" }}>
      <h3>Meghna Foods</h3>
    </div>
  );
};
```

---

### Q4. Make RestaurantCard dynamic using props

```javascript
const RestaurantCard = (props) => {
  return (
    <div className="rest-card">
      <h3>{props.resName}</h3>
      <h4>{props.cuisine}</h4>
      <h4>{props.rating} stars</h4>
      <h4>{props.deliveryTime} mins</h4>
    </div>
  );
};

// Usage:
<RestaurantCard
  resName="KFC"
  cuisine="Burger, Fast Food"
  rating="4.2"
  deliveryTime="30"
/>

<RestaurantCard
  resName="Meghna Foods"
  cuisine="Biryani, North Indian"
  rating="4.4"
  deliveryTime="38"
/>
```

---

### Q5. Destructure props inside the component

```javascript
// Method 1: Destructure in function body
const RestaurantCard = (props) => {
  const { resName, cuisine, rating } = props;
  return (
    <div>
      <h3>{resName}</h3>
      <h4>{cuisine}</h4>
      <h4>{rating} stars</h4>
    </div>
  );
};

// Method 2: Destructure directly in parameters
const RestaurantCard = ({ resName, cuisine, rating }) => {
  return (
    <div>
      <h3>{resName}</h3>
      <h4>{cuisine}</h4>
      <h4>{rating} stars</h4>
    </div>
  );
};
```

---

### Q6. Pass a whole object as a prop and use it

```javascript
const restaurantData = {
  name: "KFC",
  cuisines: ["Burger", "Fast Food"],
  avgRating: 4.1,
  deliveryTime: 30,
  costForTwo: 30000, // in paise
};

const RestaurantCard = (props) => {
  const { name, cuisines, avgRating, deliveryTime, costForTwo } = props.resData;
  return (
    <div className="rest-card">
      <h3>{name}</h3>
      <h4>{cuisines.join(", ")}</h4>   {/* joins array with comma */}
      <h4>{avgRating} stars</h4>
      <h4>{deliveryTime} mins</h4>
      <h4>₹{costForTwo / 100} for two</h4>   {/* convert paise to rupees */}
    </div>
  );
};

// Usage:
<RestaurantCard resData={restaurantData} />
```

---

### Q7. Render a list of restaurant cards using .map()

```javascript
const restaurantList = [
  { id: "1", name: "KFC", cuisine: "Burger", rating: "4.1" },
  { id: "2", name: "Meghna Foods", cuisine: "Biryani", rating: "4.4" },
  { id: "3", name: "Pizza Hut", cuisine: "Pizza", rating: "4.0" },
];

const Body = () => {
  return (
    <div className="rest-container">
      {restaurantList.map((restaurant) => (
        <RestaurantCard
          key={restaurant.id}        // ← unique key is mandatory
          resName={restaurant.name}
          cuisine={restaurant.cuisine}
          rating={restaurant.rating}
        />
      ))}
    </div>
  );
};
```

---

### Q8. Show the difference between no key, index as key, and unique key

```javascript
const items = [
  { id: "a1", name: "KFC" },
  { id: "a2", name: "Meghna Foods" },
  { id: "a3", name: "Pizza Hut" },
];

// ❌ No key — BAD, React throws warning
items.map((item) => <div>{item.name}</div>);

// ⚠️ Index as key — Not recommended (but no error)
items.map((item, index) => <div key={index}>{item.name}</div>);

// ✅ Unique ID as key — BEST PRACTICE
items.map((item) => <div key={item.id}>{item.name}</div>);
```

---

### Q9. Use .join() to display arrays and divide numbers in JSX

```javascript
const cuisines = ["Biryani", "North Indian", "Asian"];
const costInPaise = 40000;

const RestaurantCard = () => {
  return (
    <div>
      {/* Array to comma-separated string */}
      <h4>{cuisines.join(", ")}</h4>
      {/* → Biryani, North Indian, Asian */}

      {/* Math inside JSX */}
      <h4>₹{costInPaise / 100} for two</h4>
      {/* → ₹400 for two */}

      {/* String concatenation in JSX */}
      <h4>{"Delivery in " + 38 + " minutes"}</h4>
      {/* → Delivery in 38 minutes */}
    </div>
  );
};
```

---

### Q10. Component tree structure as JSX

```javascript
// This is the structure from the episode:

const AppLayout = () => (
  <div className="app">
    <Header />      {/* logo + nav items */}
    <Body />        {/* search + restaurant cards */}
    <Footer />      {/* copyright + links */}
  </div>
);

// Header contains:
const Header = () => (
  <div className="header">
    <div className="logo-container">
      <img src="logo.png" alt="logo" />
    </div>
    <div className="nav-items">
      <ul>
        <li>Home</li>
        <li>About Us</li>
        <li>Contact Us</li>
        <li>Cart</li>
      </ul>
    </div>
  </div>
);
```

---

## 🗒️ Quick Revision Card

| Concept | Key Point |
|---|---|
| Plan before code | Draw a wireframe, identify components, then code |
| Component tree | AppLayout → Header, Body, Footer → RestaurantCard |
| `className` in JSX | Use `className`, not `class` |
| CSS ways | External file, inline style object, CSS frameworks |
| Inline style syntax | `style={{ key: "value" }}` — outer `{}` = JS, inner `{}` = object |
| Props | Arguments to a function — React wraps them in an object |
| Passing props | `<Card name="KFC" rating="4.1" />` |
| Receiving props | `const Card = (props) => { props.name }` |
| Destructuring props | `const Card = ({ name, rating }) => { ... }` |
| `.map()` | Used to loop over lists and render components dynamically |
| `key` prop | Required on every list item; must be unique |
| Why keys matter | Helps React identify which items changed → avoids re-rendering the whole list |
| Index as key | Not recommended — index changes when list reorders |
| Unique ID as key | Best practice — use `restaurant.data.id` or similar |
| Config Driven UI | UI is controlled by backend data/config — used in PayTM, Swiggy, Uber |
| UI Layer | The visual components (JSX, CSS) |
| Data Layer | The data powering the UI (API responses, JSON) |
| `.join(", ")` | Convert array to comma-separated string |
| CDN | Images are hosted on external servers (Cloudinary in Swiggy's case) |
| `costForTwo / 100` | Paise to rupees conversion using JavaScript in JSX |

---

*Episode 04 – Namaste React Series | Talk is Cheap, Show Me the Code*
