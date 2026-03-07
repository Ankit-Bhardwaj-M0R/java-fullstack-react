# Episode 10: Jo Dikhta Hai, Vo Bikta Hai — Styling with Tailwind CSS

> Title translation: "What looks good, sells" — the episode is all about making your app look beautiful.

---

## Table of Contents
1. [Ways to Add CSS in React](#1-ways-to-add-css-in-react)
2. [Normal CSS](#2-normal-css)
3. [SCSS and SASS](#3-scss-and-sass)
4. [Styled Components](#4-styled-components)
5. [CSS Libraries and Frameworks](#5-css-libraries-and-frameworks)
6. [What Is Tailwind CSS?](#6-what-is-tailwind-css)
7. [Installing Tailwind CSS with Parcel](#7-installing-tailwind-css-with-parcel)
8. [What Is PostCSS?](#8-what-is-postcss)
9. [Configuration Files](#9-configuration-files)
10. [The Three Tailwind Directives in index.css](#10-the-three-tailwind-directives-in-indexcss)
11. [How Tailwind Classes Work](#11-how-tailwind-classes-work)
12. [Common Tailwind Classes — Quick Reference](#12-common-tailwind-classes--quick-reference)
13. [Tailwind IntelliSense VS Code Extension](#13-tailwind-intellisense-vs-code-extension)
14. [Dynamic / Arbitrary Values in Tailwind](#14-dynamic--arbitrary-values-in-tailwind)
15. [Hover States in Tailwind](#15-hover-states-in-tailwind)
16. [Responsive Design in Tailwind](#16-responsive-design-in-tailwind)
17. [Dark Mode in Tailwind](#17-dark-mode-in-tailwind)
18. [Pros of Tailwind CSS](#18-pros-of-tailwind-css)
19. [Cons of Tailwind CSS](#19-cons-of-tailwind-css)
20. [Tree Shaking — Tailwind's Lightweight Bundle](#20-tree-shaking--tailwinds-lightweight-bundle)
21. [Summary Interview Q&A](#21-summary-interview-qa)
22. [Coding Exercises](#22-coding-exercises)
23. [Quick Quiz](#23-quick-quiz)

---

## 1. Ways to Add CSS in React

There are **five** popular ways to add CSS to a React application:

1. **Normal CSS** — plain `.css` files (what we used for the first 9 episodes)
2. **SCSS / SASS** — CSS with superpowers (variables, nesting, mixins)
3. **Styled Components** — CSS written inside JavaScript
4. **CSS Libraries & Frameworks** — pre-built component libraries like Material UI, Bootstrap, Chakra UI, Ant Design
5. **Tailwind CSS** — a utility-first CSS framework

> The instructor's verdict: Normal CSS and SCSS/SASS don't scale well for large production apps. Styled Components are used at big companies (used at Uber). Libraries like Material UI are used at many companies. **Tailwind CSS** is what we'll learn in this episode — it's trending and gaining a lot of traction.

---

## 2. Normal CSS

This is the simplest approach — the one we used for the first 9 episodes:

```jsx
// RestaurantCard.jsx
<div className="res-card">
  <img className="res-logo" src={imgUrl} />
  <h3 className="res-name">{name}</h3>
</div>
```

```css
/* index.css */
.res-card {
  padding: 16px;
  margin: 8px;
}
.res-logo {
  width: 100%;
}
```

**Problem:** As the app grows, you constantly switch between `.jsx` and `.css` files. Multiple developers can create duplicate or conflicting CSS. It doesn't scale well.

---

## 3. SCSS and SASS

SASS stands for **Syntactically Awesome Style Sheets**. SCSS is another syntax of SASS. Think of it as CSS with superpowers:

- Variables (`$primary-color: blue;`)
- Nesting (write CSS inside CSS like HTML structure)
- Mixins (reusable CSS blocks)
- Functions and loops

```scss
$primary: #ff6b6b;

.header {
  background: $primary;

  .logo {
    width: 56px;
  }

  .nav-items {
    display: flex;

    li {
      padding: 0 16px;
    }
  }
}
```

**Why we're not using it:** The instructor says SCSS doesn't scale well in big production applications. Large companies don't use it much.

---

## 4. Styled Components

**Styled Components** is a library where you write CSS directly inside your JavaScript/JSX using template literals.

```jsx
import styled from "styled-components";

const Button = styled.button`
  background: blue;
  color: white;
  padding: 8px 16px;
  border-radius: 4px;
  border: none;
  cursor: pointer;

  &:hover {
    background: darkblue;
  }
`;

// Usage
const App = () => <Button>Click Me</Button>;
```

The instructor used styled components at **Uber** — they are widely used at big companies. Each component owns its own styles — no class name collisions ever.

Website: [styledcomponents.com](https://styledcomponents.com)

---

## 5. CSS Libraries and Frameworks

Pre-built component libraries that give you beautiful, ready-made components to import and use directly:

| Library | Description |
|---|---|
| **Material UI** | Very popular React component library. Google's Material Design. |
| **Bootstrap** | Classic HTML/CSS/JS framework. Works with React. |
| **Chakra UI** | Simple, modular, accessible component library. |
| **Ant Design** | World's 2nd most popular React UI library. |

**What they give you:**

Instead of building your own button:
```jsx
// Your ugly button
<button>Submit</button>
```

You import a pre-built, pre-styled button:
```jsx
import Button from "@mui/material/Button";
<Button variant="contained" color="primary">Submit</Button>
```

It looks beautiful with zero CSS written.

**Trade-off:** They dump ALL their CSS into your bundle — even classes you never use. This bloats your bundle size.

---

## 6. What Is Tailwind CSS?

**Tailwind CSS** is a **utility-first CSS framework**. Instead of writing CSS in separate files, you apply small CSS utility classes directly in your JSX.

Tailwind's tagline: *"Rapidly build modern websites without ever leaving your HTML."*

What does that mean? In normal CSS, you:
1. Write a class name in JSX
2. Switch to the CSS file
3. Write the CSS for that class
4. Switch back

With Tailwind, you never leave your JSX. You write class names and they have built-in CSS already.

Tailwind works with **any framework** — React, Angular, Vue, or plain HTML.

---

## 7. Installing Tailwind CSS with Parcel

Since our project uses Parcel as the bundler, we follow the Parcel installation guide on Tailwind's website.

**Step 1: Install packages**
```bash
npm install tailwindcss postcss
```

**Step 2: Initialize Tailwind (creates `tailwind.config.js`)**
```bash
npx tailwindcss init
```

**Step 3: Create `postcss.rc` file** (PostCSS configuration)
```json
{
  "plugins": {
    "tailwindcss": {}
  }
}
```

**Step 4: Edit `tailwind.config.js`** (add content array)

**Step 5: Add Tailwind directives to `index.css`**

After setting up, run `npm start`. The UI will look broken at first — that's expected! Tailwind resets all default browser styles. We'll fix it by adding Tailwind classes.

---

## 8. What Is PostCSS?

**PostCSS** is a tool for transforming CSS with JavaScript. It's a preprocessor that processes your CSS before it reaches the browser.

Tailwind CSS uses PostCSS behind the scenes. When Parcel builds your project:

```
Your JSX files
  → Parcel uses PostCSS
  → PostCSS reads tailwind.config.js
  → Tailwind generates the CSS classes you actually used
  → Outputs a clean, minimal CSS file
```

You don't need to learn PostCSS separately. Just know that:
- `postcss.rc` tells PostCSS we're using Tailwind
- Parcel uses PostCSS to understand Tailwind's classes

---

## 9. Configuration Files

**`tailwind.config.js`:**
```js
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    "./src/**/*.{html,js,ts,jsx,tsx}",
    // ↑ tells Tailwind WHERE to look for class names
    // Only CSS for classes found in these files will be included in build
  ],
  theme: {
    extend: {},
  },
  plugins: [],
};
```

The `content` array is crucial — it tells Tailwind which files to scan for class names. If a class is used in these files, its CSS gets included. If not, it's excluded from the bundle entirely.

If you're only using `.html` and `.js` files, you can simplify:
```js
content: ["./src/**/*.{html,js}"]
```

---

## 10. The Three Tailwind Directives in index.css

After installing Tailwind, delete everything in `index.css` and replace with these three lines:

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

What each one does:
- **`@tailwind base`** — Resets browser default styles (that's why everything looks broken initially)
- **`@tailwind components`** — Includes any custom component classes
- **`@tailwind utilities`** — Includes all the utility classes (flex, p-4, text-lg, etc.)

After adding these three lines, the instructor says: **"I will not touch my index.css file ever again."** All styling will happen through Tailwind class names in JSX.

---

## 11. How Tailwind Classes Work

In normal CSS:
```jsx
// JSX
<div className="header">
// ...

// index.css
.header {
  display: flex;
  justify-content: space-between;
  padding: 16px;
}
```

In Tailwind CSS:
```jsx
// JSX — no CSS file needed!
<div className="flex justify-between p-4">
```

Each class name maps to a specific CSS property. Tailwind has a class for EVERY CSS property you would ever need.

---

## 12. Common Tailwind Classes — Quick Reference

| What you want | Tailwind class | CSS it applies |
|---|---|---|
| `display: flex` | `flex` | `display: flex` |
| `justify-content: space-between` | `justify-between` | `justify-content: space-between` |
| `align-items: center` | `items-center` | `align-items: center` |
| `flex-wrap: wrap` | `flex-wrap` | `flex-wrap: wrap` |
| `width: 14rem` | `w-56` | `width: 14rem` |
| `width: 200px` | `w-[200px]` | `width: 200px` |
| `padding: 1rem` (all) | `p-4` | `padding: 1rem` |
| `padding-left + right` | `px-4` | `padding-left: 1rem; padding-right: 1rem` |
| `padding-top + bottom` | `py-2` | `padding-top: 0.5rem; padding-bottom: 0.5rem` |
| `margin: 1rem` (all) | `m-4` | `margin: 1rem` |
| `margin-bottom` | `mb-2` | `margin-bottom: 0.5rem` |
| `margin-top` | `mt-2` | `margin-top: 0.5rem` |
| `background-color: pink-100` | `bg-pink-100` | light pink background |
| `background-color: gray-50` | `bg-gray-50` | very light gray |
| `border-radius: large` | `rounded-lg` | `border-radius: 0.5rem` |
| `box-shadow: large` | `shadow-lg` | large shadow |
| `font-weight: bold` | `font-bold` | `font-weight: 700` |
| `font-size: large` | `text-lg` | `font-size: 1.125rem` |
| `font-size: extra-large` | `text-xl` | `font-size: 1.25rem` |

**The sizing scale:** `sm` = small, `md` = medium, `lg` = large, `xl` = extra large, `2xl` = double extra large

**Numbers 1-96 for padding/margin:** Each unit = 0.25rem (4px). So:
- `p-1` = 0.25rem = 4px
- `p-4` = 1rem = 16px
- `p-8` = 2rem = 32px

---

## 13. Tailwind IntelliSense VS Code Extension

This is the most important tool when working with Tailwind. Install **Tailwind CSS IntelliSense** from the VS Code extensions marketplace.

What it does:
1. **Auto-complete**: As you type class names, it suggests matching Tailwind classes
2. **Hover preview**: Hover over any class to see the actual CSS it generates
3. **Wildcard search**: Type `wrap` and it finds `flex-wrap`, `flex-wrap-reverse`, etc.

```jsx
// Type "flex" → it suggests: flex, flex-wrap, flex-col, flex-row...
// Hover over "w-56" → tooltip shows: "width: 14rem;"
// Type "shadow" → suggests: shadow-sm, shadow, shadow-md, shadow-lg, shadow-xl
```

> "If this extension was not there, I would have never used Tailwind." — Instructor

**Tip:** If suggestions stop appearing, press `Ctrl + Space` on Windows/Mac to trigger them manually.

---

## 14. Dynamic / Arbitrary Values in Tailwind

Tailwind has classes for standard sizes (w-48 = 192px, w-52 = 208px). But what if you need exactly 200px — a value not in Tailwind's scale?

Use **square brackets** to write any arbitrary value:

```jsx
{/* Standard Tailwind class */}
<div className="w-48">192px wide</div>

{/* Arbitrary value — your own specific size */}
<div className="w-[200px]">Exactly 200px wide</div>
<div className="w-[250px]">Exactly 250px wide</div>
<div className="h-[45vh]">45% viewport height</div>
<div className="bg-[#ff6b6b]">Custom hex color</div>
```

**Rule:** Whenever possible, use Tailwind's standard classes. Use arbitrary values only when you have a specific hardcoded requirement.

---

## 15. Hover States in Tailwind

To apply CSS only on hover, prefix any class with `hover:`:

```jsx
{/* Normal state: gray-50 background, hover state: gray-200 background */}
<div className="bg-gray-50 hover:bg-gray-200">
  I get darker on hover!
</div>

{/* Normal state: text-black, hover state: text-blue-600 */}
<a className="text-black hover:text-blue-600">Link</a>

{/* Combine hover with any utility */}
<button className="bg-blue-500 hover:bg-blue-700 text-white px-4 py-2 rounded-lg">
  Hover me
</button>
```

**Note:** Don't hardcode background colors when you want hover to work — let Tailwind manage both states.

---

## 16. Responsive Design in Tailwind

Tailwind makes responsive design incredibly easy. Add a breakpoint prefix to any class:

| Prefix | Breakpoint | Meaning |
|---|---|---|
| (none) | All screens | Default — applies everywhere |
| `sm:` | ≥ 640px | Small devices and up |
| `md:` | ≥ 768px | Medium devices and up |
| `lg:` | ≥ 1024px | Large devices and up |
| `xl:` | ≥ 1280px | Extra large devices and up |
| `2xl:` | ≥ 1536px | Double extra large devices |

```jsx
{/* Different background colors for different screen sizes */}
<header className="bg-pink-100 sm:bg-yellow-100 lg:bg-green-50">
  {/*
    Mobile (< 640px):  pink background
    Small+ (≥ 640px):  yellow background
    Large+ (≥ 1024px): green background
  */}
</header>

{/* Responsive grid */}
<div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4">
  {/* 1 column on mobile, 2 on tablet, 4 on desktop */}
</div>
```

This replaces writing `@media` queries by hand!

---

## 17. Dark Mode in Tailwind

Just like `hover:`, add `dark:` prefix to apply CSS only in dark mode:

```jsx
<h2 className="text-slate-500 dark:text-slate-400">
  {/* Light mode: slate-500, Dark mode: slate-400 */}
  Hello World
</h2>

<div className="bg-white dark:bg-gray-800">
  <p className="text-black dark:text-white">Content</p>
</div>
```

This is a **huge** advantage. Building dark mode with normal CSS requires:
- Separate CSS files
- Multiple `@media (prefers-color-scheme: dark)` queries
- Class toggles on `body`

With Tailwind, you just add `dark:` in front of any class. Done.

---

## 18. Pros of Tailwind CSS

**1. No file switching:**
In normal CSS, you jump between JSX and CSS files constantly. With Tailwind, you write CSS right where you write JSX. When thinking about a component, you can style it immediately.

**2. Fast development:**
The instructor styled the entire Food Villa app header, search bar, buttons, and restaurant cards in about half an hour. No switching files.

**3. No redundant CSS (tree shaking):**
Tailwind scans your files and only includes CSS for classes you actually use. If you use `flex` 100 times, only ONE `.flex { display: flex }` class ends up in the bundle. Unused classes are never shipped.

**4. Team consistency:**
In a team of 10 developers, if one writes `.btn-green`, another writes `.green-button`, you get duplicate CSS. With Tailwind, everyone uses the same class names (`bg-green-500`) — no duplication.

**5. Pre-built component library:**
Tailwind UI provides pre-built components (shopping carts, navigation bars, forms) with code you can copy-paste directly.

**6. Hover, focus, responsive, dark mode — all built in:**
Everything you can think of: `hover:`, `focus:`, `sm:`, `lg:`, `dark:` — all just prefix any class.

---

## 19. Cons of Tailwind CSS

**1. Initial learning curve:**
You need to learn class names. `w-56` means width 14rem. `px-4` means horizontal padding 1rem. It takes time to memorize these.

**2. Ugly-looking code (long class names):**
When you apply many CSS properties, your JSX becomes long and less readable:

```jsx
{/* This is hard to read */}
<div className="flex justify-between items-center px-4 py-2 bg-white shadow-lg rounded-lg hover:bg-gray-50 cursor-pointer transition-all duration-200">
```

Versus a single class name:
```jsx
{/* Normal CSS — clean to read */}
<div className="restaurant-card">
```

The instructor acknowledges: "Our code looks a little ugly with so many class names."

> Despite the cons, the instructor's conclusion: "Personally, I really like Tailwind. Initial learning curve is there, but once you get used to it, it makes life very easy. I will use it in my next personal project."

---

## 20. Tree Shaking — Tailwind's Lightweight Bundle

This is one of Tailwind's killer features. Libraries like Bootstrap ship ALL their CSS to the browser — hundreds of kilobytes of classes you'll never use.

Tailwind is different:

```
Bootstrap in your project:
  → Bundle includes: .btn-primary, .btn-secondary, .navbar, .modal, .carousel...
  → ALL 300KB even if you use only 5 classes

Tailwind in your project:
  → Tailwind scans all your files
  → Finds: flex, p-4, text-lg, rounded-lg, shadow-md
  → Bundle includes ONLY those 5 classes
  → Bundle stays tiny
```

Repeated classes are deduplicated:
```jsx
// Even if you write bg-gray-50 on 100 different elements...
<div className="bg-gray-50">...</div>
<div className="bg-gray-50">...</div>
// ...Tailwind only ships ONE .bg-gray-50 class
```

This means your CSS bundle stays small no matter how large your app gets — as long as you're reusing the same utility classes.

---

## 21. Summary Interview Q&A

**Q1: What are the different ways to add CSS to a React app?**

There are five popular approaches:
1. **Normal CSS** — plain `.css` files with class names in JSX. Simple but doesn't scale.
2. **SCSS/SASS** — CSS with superpowers like variables and nesting. Not recommended for large production apps.
3. **Styled Components** — CSS written directly in JavaScript using tagged template literals. Used at big companies like Uber.
4. **CSS libraries (Material UI, Bootstrap, Chakra UI, Ant Design)** — pre-built component libraries with beautiful components out of the box.
5. **Tailwind CSS** — utility-first framework where you apply small CSS class names directly in JSX without writing separate CSS files.

---

**Q2: What is Tailwind CSS and how is it different from normal CSS?**

Tailwind CSS is a utility-first CSS framework. Instead of writing CSS in separate files, you use pre-defined utility classes directly in your HTML/JSX. `flex` gives you `display: flex`. `p-4` gives you `padding: 1rem`. `rounded-lg` gives rounded corners.

With normal CSS: write class name in JSX → switch to CSS file → write CSS → switch back.
With Tailwind: write class names directly in JSX — never touch a CSS file again.

---

**Q3: What is PostCSS and why is it needed for Tailwind?**

PostCSS is a tool that transforms CSS using JavaScript plugins. Tailwind uses PostCSS behind the scenes to process your JSX files, find all the Tailwind class names you've used, and generate the minimal CSS file that includes only those classes. The `postcss.rc` configuration file tells PostCSS to use Tailwind, and Parcel uses PostCSS during the build.

---

**Q4: What are the advantages and disadvantages of Tailwind CSS?**

**Advantages:**
- No file switching — style in JSX directly
- Faster development once you learn the class names
- Tree shaking — only ships CSS classes you actually use (very lightweight bundle)
- No redundant/duplicate CSS across team members
- Built-in responsive design (`sm:`, `lg:`), hover states (`hover:`), and dark mode (`dark:`)

**Disadvantages:**
- Initial learning curve to memorize class names
- JSX code becomes long and harder to read when many classes are applied

---

**Q5: How does Tailwind keep the CSS bundle size small?**

Tailwind scans all files specified in the `content` array of `tailwind.config.js`. It finds only the Tailwind class names actually used in those files, and generates CSS for only those classes. Unused classes are never included in the bundle. Even if you use the same class name 100 times, it only generates the CSS rule once. This is called tree shaking and keeps bundles much smaller than traditional CSS libraries.

---

**Q6: How do you write hover states and responsive designs in Tailwind?**

Tailwind uses **prefix modifiers**:
- Hover: `hover:bg-gray-200` (gray background on hover)
- Responsive: `sm:text-lg lg:text-2xl` (different text sizes at different breakpoints)
- Dark mode: `dark:bg-gray-800` (dark background in dark mode)
- Focus: `focus:ring-2` (ring on focus)

You just prepend the modifier to any utility class with a colon: `modifier:class`.

---

## 22. Coding Exercises

### Exercise 1: Header with Tailwind
Style a header component using only Tailwind classes (no CSS file):

```jsx
const Header = () => {
  return (
    <div className="flex justify-between items-center px-6 py-4 bg-white shadow-lg">
      <img className="w-20" src={logoUrl} alt="logo" />
      <ul className="flex">
        <li className="px-4 py-2 font-medium hover:text-orange-500 cursor-pointer">Home</li>
        <li className="px-4 py-2 font-medium hover:text-orange-500 cursor-pointer">About</li>
        <li className="px-4 py-2 font-medium hover:text-orange-500 cursor-pointer">Contact</li>
      </ul>
    </div>
  );
};
```

---

### Exercise 2: Restaurant Card with Tailwind
Style a restaurant card:

```jsx
const RestaurantCard = ({ name, cuisine, rating, imgUrl }) => {
  return (
    <div className="m-4 p-4 w-[250px] rounded-lg shadow-md bg-white hover:bg-gray-50 cursor-pointer">
      <img className="rounded-lg w-full h-40 object-cover" src={imgUrl} alt={name} />
      <h3 className="font-bold text-lg py-2">{name}</h3>
      <p className="text-gray-500 text-sm">{cuisine}</p>
      <span className="text-green-600 font-semibold">{rating} ★</span>
    </div>
  );
};
```

---

### Exercise 3: Responsive Grid
Create a grid that shows 1 column on mobile, 2 on tablet, 4 on desktop:

```jsx
const RestaurantGrid = ({ restaurants }) => {
  return (
    <div className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4 p-4">
      {restaurants.map((res) => (
        <RestaurantCard key={res.id} {...res} />
      ))}
    </div>
  );
};
```

---

### Exercise 4: Dark Mode Toggle
Build a component that looks different in dark mode:

```jsx
const DarkModeCard = () => {
  return (
    <div className="bg-white dark:bg-gray-800 text-black dark:text-white p-6 rounded-lg shadow">
      <h1 className="text-2xl font-bold">Hello!</h1>
      <p className="text-gray-600 dark:text-gray-300 mt-2">
        I change colors in dark mode.
      </p>
    </div>
  );
};
```

---

### Exercise 5: Convert Normal CSS to Tailwind
Convert this:

```css
/* Normal CSS */
.search-bar {
  display: flex;
  align-items: center;
  margin: 16px;
  padding: 16px;
}
.search-input {
  border: 1px solid black;
  padding: 8px 16px;
  border-radius: 8px;
}
.search-btn {
  background-color: green;
  padding: 8px 16px;
  border-radius: 8px;
  color: white;
  margin-left: 8px;
}
```

To Tailwind:
```jsx
<div className="flex items-center m-4 p-4">
  <input
    className="border border-black px-4 py-2 rounded-lg"
    placeholder="Search..."
  />
  <button className="bg-green-500 px-4 py-2 rounded-lg text-white ml-2">
    Search
  </button>
</div>
```

---

### Exercise 6: Tailwind Class Quiz
Match the class to the CSS:

| Tailwind Class | CSS Property |
|---|---|
| `flex` | ? |
| `justify-between` | ? |
| `items-center` | ? |
| `p-4` | ? |
| `px-4` | ? |
| `rounded-lg` | ? |
| `shadow-md` | ? |
| `font-bold` | ? |
| `text-xl` | ? |
| `hover:bg-gray-100` | ? |

**Answers:**
| Tailwind Class | CSS |
|---|---|
| `flex` | `display: flex` |
| `justify-between` | `justify-content: space-between` |
| `items-center` | `align-items: center` |
| `p-4` | `padding: 1rem` |
| `px-4` | `padding-left: 1rem; padding-right: 1rem` |
| `rounded-lg` | `border-radius: 0.5rem` |
| `shadow-md` | medium box-shadow |
| `font-bold` | `font-weight: 700` |
| `text-xl` | `font-size: 1.25rem` |
| `hover:bg-gray-100` | `background-color: #f3f4f6` on hover |

---

## 23. Quick Quiz

1. What are the 5 ways to add CSS to a React app?
2. What company used styled components that the instructor worked at?
3. What does the Tailwind class `flex` do?
4. What does `justify-between` do?
5. What does `px-4` do vs `p-4`?
6. What is PostCSS and why is it needed?
7. What is the `content` array in `tailwind.config.js` for?
8. What are the three Tailwind directives written in `index.css`?
9. How do you write hover styles in Tailwind?
10. How do you write responsive styles for large screens only?
11. How do you apply an arbitrary (hardcoded) size like 200px?
12. What is tree shaking in the context of Tailwind?
13. What VS Code extension makes Tailwind development easier?
14. Name two cons of Tailwind CSS.
15. What does `dark:bg-gray-800` do?

**Answers:**
1. Normal CSS, SCSS/SASS, Styled Components, CSS Libraries (Material UI, etc.), Tailwind CSS
2. Uber
3. `display: flex`
4. `justify-content: space-between`
5. `px-4` = padding left & right only; `p-4` = padding all sides
6. A tool for transforming CSS with JavaScript; Tailwind uses it to generate only the CSS classes you use
7. Tells Tailwind which files to scan for class names — only classes found there are included in the bundle
8. `@tailwind base; @tailwind components; @tailwind utilities;`
9. Prefix the class with `hover:` — e.g., `hover:bg-gray-200`
10. Prefix with `lg:` — e.g., `lg:grid-cols-4`
11. Use square brackets: `w-[200px]`
12. Only shipping CSS for the classes actually used — unused classes are never included in the bundle
13. Tailwind CSS IntelliSense
14. Initial learning curve; long class names make JSX code harder to read
15. Applies `background-color: #1f2937` (dark gray) when dark mode is active
