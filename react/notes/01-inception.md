# Episode 1 — Inception: What Is React and How Does It Actually Work?

> **Who this is for:** Complete beginners to React and JavaScript. Every concept is explained from scratch with the exact examples from the lecture.

---

## Table of Contents
1. [What Is React? Library vs. Framework](#1-what-is-react-library-vs-framework)
2. [Tools You Need](#2-tools-you-need)
3. [Hello World — Three Ways](#3-hello-world--three-ways)
4. [How the Browser Runs JavaScript](#4-how-the-browser-runs-javascript)
5. [What Is a CDN?](#5-what-is-a-cdn)
6. [Injecting React via CDN — The Shortest React Program](#6-injecting-react-via-cdn--the-shortest-react-program)
7. [Two React Files — react.js and react-dom.js](#7-two-react-files--reactjs-and-react-domjs)
8. [React.createElement — Creating Elements the React Way](#8-reactcreateelement--creating-elements-the-react-way)
9. [A React Element Is Just a JavaScript Object](#9-a-react-element-is-just-a-javascript-object)
10. [ReactDOM.createRoot and root.render](#10-reactdomcreateroot-and-rootrender)
11. [React Overrides Everything Inside Root](#11-react-overrides-everything-inside-root)
12. [The "not rendered" Pattern](#12-the-not-rendered-pattern)
13. [Script Tag Order Matters](#13-script-tag-order-matters)
14. [Props — Passing Attributes](#14-props--passing-attributes)
15. [Nested Elements — Children as an Array](#15-nested-elements--children-as-an-array)
16. [Development vs. Production CDN Files](#16-development-vs-production-cdn-files)
17. [Splitting Code into Separate Files](#17-splitting-code-into-separate-files)
18. [Why CDN Is Not the Preferred Way (Preview)](#18-why-cdn-is-not-the-preferred-way-preview)
19. [Homework from This Episode](#19-homework-from-this-episode)
20. [Simple Coding Exercises](#20-simple-coding-exercises)

---

## 1. What Is React? Library vs. Framework

### Interview Question
**"Is React a library or a framework? What is the difference?"**

**Answer (conversational):**

React is a **library**, not a framework. The instructor makes this very clear right from the start.

Think of it this way: when you want to add an **image carousel** (those sliders you see on websites where images move left and right) to your website, you just grab a small JavaScript library like "Owl Carousel" and drop it in. That's a library — it does one specific thing, it's lightweight, and it takes minimum effort to plug it in.

A **framework**, on the other hand, is a full-fledged, opinionated structure. It controls how your whole application is organized. Angular is a framework — it comes with routing, state management, forms, and everything baked in. You follow *its* rules.

React is more like the carousel example. You can literally just drop two script tags into any existing HTML page and you're using React. You don't need to restructure your whole project. This is why the instructor says:

> *"React is nothing but a piece of JavaScript code that Facebook engineers wrote for us and gave us as a library."*

---

## 2. Tools You Need

### Interview Question
**"What development tools and VS Code extensions are useful when working with React?"**

**Answer:**

The instructor starts with an empty folder and builds from zero. Here are the tools used:

| Tool | Purpose |
|------|---------|
| **Google Chrome** | Best browser for debugging React (use Chrome DevTools) |
| **VS Code** | Code editor — works on Mac, Windows, Linux |
| **Better Comments** | Makes your code comments look better / coloured |
| **Bracket Pair Colorizer** | Colours matching brackets so you don't get lost in nested code |
| **React Redux (extension)** | Helpful for React development (more on Redux later) |
| **Git Lens** | Makes using Git inside VS Code much easier |
| **Prettier** | Auto-formats your code every time you save the file |
| **VS Code Icons** | Gives colourful icons to different file types |
| **Emmet** | Built into VS Code — type a short abbreviation and it expands into full HTML boilerplate |

> **What is Emmet?** (Homework from the instructor) — It is a shorthand tool inside VS Code. For example, if you type `!` and press `Tab` inside an `.html` file, it generates a full HTML boilerplate automatically. The spelling is **E-M-M-E-T**.

---

## 3. Hello World — Three Ways

### Interview Question
**"How is creating a heading in plain HTML different from doing it in JavaScript, and then in React?"**

**Answer (with the exact example from the lecture):**

The instructor shows this progression to make React feel non-scary:

### Way 1 — Plain HTML
```html
<div id="root">
  <h1>Namaste everyone</h1>
</div>
```
You just write the tag. Simple.

### Way 2 — JavaScript (DOM Manipulation)
```html
<div id="root"></div>

<script>
  // Create the element
  const heading = document.createElement('h1');

  // Add text content
  heading.innerHTML = 'Namaste everyone — From JavaScript';

  // Get the root div
  const root = document.getElementById('root');

  // Put the heading inside the root
  root.appendChild(heading);
</script>
```
Here we use browser APIs like `document.createElement` and `appendChild`. The browser understands these because of its built-in JavaScript engine.

### Way 3 — React (pure, no JSX)
```html
<div id="root">not rendered</div>

<!-- React CDN links -->
<script src="https://unpkg.com/react@18/umd/react.development.js" crossorigin></script>
<script src="https://unpkg.com/react-dom@18/umd/react-dom.development.js" crossorigin></script>

<script>
  // Step 1: Create a React element
  const heading = React.createElement('h1', {}, 'Namaste everyone — From React');

  // Step 2: Tell React where to render
  const root = ReactDOM.createRoot(document.getElementById('root'));

  // Step 3: Render the element into the root
  root.render(heading);
</script>
```

The instructor's point: the *output* is the same, but now React is doing it. And React is not magic — it's just JavaScript code that Facebook wrote.

---

## 4. How the Browser Runs JavaScript

### Interview Question
**"Where do browser APIs like `document` and `window` come from? Who gives them to us?"**

**Answer:**

When you write `document.createElement('h1')`, you might wonder — what is `document`? Where does it come from? We didn't define it anywhere.

The browser has a built-in **JavaScript engine** (Chrome uses V8). This JS engine gives you a global object called `window`. Everything inside the browser environment lives on `window`.

```javascript
// These are the same thing:
document.getElementById('root')
window.document.getElementById('root')
```

The instructor summarizes: *"The browser knows what is `document`, what is `window`. This all comes from the browser's JS engine. These are browser APIs — superpowers the browser gives us."*

This is the same concept from Namaste JavaScript — if you've seen that series, this will click immediately.

---

## 5. What Is a CDN?

### Interview Question
**"What is a CDN and why is it used?"**

**Answer (Homework — read more yourself):**

CDN stands for **Content Delivery Network**. The instructor tells you to Google this as homework, but here is the basic idea:

A CDN is a network of servers distributed around the world. When you include a library (like React) via a CDN link, the file is served from the server closest to the user's physical location, making it load faster.

Example CDN link for React:
```html
<script src="https://unpkg.com/react@18/umd/react.development.js"></script>
```

`unpkg.com` is a CDN. It hosts npm packages and serves them over the internet.

> **Key insight:** When you use a CDN, the library lives on *someone else's server*, not yours. This matters for production (covered in Episode 2).

---

## 6. Injecting React via CDN — The Shortest React Program

### Interview Question
**"What is the simplest possible React program?"**

**Answer:**

The instructor says: *"This is the shortest program in React"* — and it is just injecting the two CDN script tags:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Namaste React</title>
</head>
<body>
  <div id="root">not rendered</div>

  <script src="https://unpkg.com/react@18/umd/react.development.js" crossorigin></script>
  <script src="https://unpkg.com/react-dom@18/umd/react-dom.development.js" crossorigin></script>
</body>
</html>
```

Once these two lines are included, open your browser console and type `React` — you will see a big object. This object has methods like `React.createElement`, `React.createContext`, etc.

The instructor's point: *"React is just some JavaScript code which someone else wrote for us. Once we inject it, we get access to all its superpowers — just like jQuery, just like any other library."*

It is a **global object** at this point — accessible anywhere in your code.

---

## 7. Two React Files — react.js and react-dom.js

### Interview Question
**"Why are there two separate packages — `react` and `react-dom`? What does each one do?"**

**Answer:**

This is a great interview question. The instructor explains it this way:

When React was created, the developers had a vision that React should not be limited to just web browsers. React should be able to run on:

- **Web browsers** (using `react-dom`)
- **Mobile devices** → React Native
- **3D environments** → React Three Fiber
- **PDF generation**, **command line apps**, etc.

So they split it into two packages:

| Package | What it does |
|---------|-------------|
| `react` | The **core library** — the algorithm that figures out *what* to render, creates React elements |
| `react-dom` | The **web-specific** part — takes React elements and puts them into the browser's DOM |

Think of it this way:
- `react` = the brain (knows about components, elements, state logic)
- `react-dom` = the hands that reach into the browser and modify the actual webpage

On a mobile app, you would use `react` + `react-native` instead of `react-dom`.

---

## 8. React.createElement — Creating Elements the React Way

### Interview Question
**"How does `React.createElement` work? What arguments does it take?"**

**Answer:**

`React.createElement` is the fundamental building block. It takes **three arguments**:

```javascript
React.createElement(
  type,       // 1st arg: the HTML tag name as a string, e.g. 'h1', 'div', 'p'
  props,      // 2nd arg: an object of attributes, e.g. { id: 'title', className: 'header' }
  children    // 3rd arg: what goes INSIDE the tag — text, or another React element
);
```

**Example from the lecture:**

```javascript
// Creates: <h1>Namaste everyone</h1>
const heading = React.createElement('h1', {}, 'Namaste everyone');
```

```javascript
// Creates: <h1 id="title">Namaste everyone</h1>
const heading = React.createElement('h1', { id: 'title' }, 'Namaste everyone');
```

The second argument (props) is where you pass **HTML attributes** like `id`, `className`, `style`, etc. If you don't need any attributes, pass an empty object `{}` or `null`.

The third argument is the **content** inside the tag — it can be a string of text, a number, or even another React element.

> The instructor says: *"React gives us this API known as `React.createElement`. And now see how intuitive it is — you are creating an element, so it's called `createElement`. React APIs are very easy and intuitive — that is one reason why React is so popular."*

---

## 9. A React Element Is Just a JavaScript Object

### Interview Question
**"What does `React.createElement` actually return? Is it HTML?"**

**Answer (the most important concept in this episode):**

This is one of the biggest "aha!" moments in this lecture. The instructor asks:

> *"If I do `console.log(heading)` after creating a React element, what will I see?"*

Many students guess "an H1 tag." The answer is: **it's a plain JavaScript object**.

```javascript
const heading = React.createElement('h1', { id: 'title' }, 'Namaste everyone');
console.log(heading);

// Output in browser console:
// {
//   $$typeof: Symbol(react.element),
//   type: 'h1',
//   key: null,
//   ref: null,
//   props: {
//     id: 'title',
//     children: 'Namaste everyone'
//   },
//   _owner: null,
//   _store: {}
// }
```

So a **React element is just a JavaScript object**. The browser does not see any HTML at this point. React is internally building a description of what the UI should look like, stored as a plain JS object.

Then when you call `root.render(heading)`, React takes this object and *converts* it into real HTML nodes that the browser can display.

The instructor says: *"If somebody asks you what is a React element at the end of the day — it's an object. This is what React does behind the scenes."*

---

## 10. ReactDOM.createRoot and root.render

### Interview Question
**"What is `ReactDOM.createRoot` and what does `root.render` do?"**

**Answer:**

These two methods are how you connect React to your HTML page.

### Step 1: `ReactDOM.createRoot(domElement)`

```javascript
const root = ReactDOM.createRoot(document.getElementById('root'));
```

This tells React: *"Hey, this `<div id="root">` is where I want you to take control. Everything I render should go inside this div."*

`createRoot` comes from `react-dom` (the web package). It returns a **root object** that has a `.render()` method.

> **Why is it called "root"?** Because in React, your whole application has one entry point — one root. Everything in your app lives inside this one root.

### Step 2: `root.render(reactElement)`

```javascript
root.render(heading);
```

This is where the magic happens. React takes the JavaScript object (`heading`) and converts it into actual DOM nodes (real HTML) that appear in the browser.

**Full working example:**

```javascript
const heading = React.createElement('h1', { id: 'title' }, 'Namaste everyone');
const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(heading);
```

---

## 11. React Overrides Everything Inside Root

### Interview Question
**"What happens if there is existing content inside the `<div id="root">` when React renders something?"**

**Answer:**

This is an interview trap question. The instructor demonstrates this live.

Suppose your HTML has:

```html
<div id="root">
  <h1>Old content 1</h1>
  <h2>Old content 2</h2>
</div>
```

And your React code renders:

```javascript
const heading = React.createElement('h1', {}, 'New React Content');
root.render(heading);
```

**What happens?** React does **not** append to the existing content. It **completely replaces** everything inside the root div with whatever you pass to `render()`.

The output will be:
```html
<div id="root">
  <h1>New React Content</h1>
</div>
```

The old content is gone. This is why `root.render` is powerful — React takes full ownership of whatever DOM element you pass to `createRoot`.

---

## 12. The "not rendered" Pattern

### Interview Question
**"What does 'not rendered' mean in a React project? Why do developers put this text inside the root div?"**

**Answer:**

In the lecture, the instructor writes the root div like this:

```html
<div id="root">not rendered</div>
```

This is a smart developer trick. If React fails to load (maybe there's a JavaScript error, the CDN is down, or the root ID is wrong), the `root.render()` method never runs, and the user will see the text "not rendered" on screen.

This acts as a **fallback message** and also a **debugging clue** — if you see "not rendered" on the page, it means:
1. React failed to load
2. There's an error in your JavaScript
3. The `getElementById` is using the wrong ID

The instructor demonstrates: if you change `getElementById('root')` to `getElementById('root1')` (wrong ID), the page shows "not rendered" because React can't find the element to take over.

```javascript
// This will fail silently — root1 doesn't exist
const root = ReactDOM.createRoot(document.getElementById('root1'));
// 'not rendered' text stays visible in the browser
```

---

## 13. Script Tag Order Matters

### Interview Question
**"What happens if you load your app's script before the React CDN scripts? Why does the order of script tags matter?"**

**Answer:**

The instructor deliberately moves the script tags around to show what happens. If you load `app.js` **before** the React CDN links:

```html
<!-- WRONG ORDER -->
<script src="./app.js"></script>   <!-- app.js uses React — but React isn't loaded yet! -->
<script src="https://unpkg.com/react@18/..."></script>
<script src="https://unpkg.com/react-dom@18/..."></script>
```

You get the error: **"React is not defined"**

Why? Because the browser reads HTML from top to bottom. When it gets to `app.js`, it tries to run the code which calls `React.createElement`. But at that point, the React library hasn't been loaded yet, so `React` is `undefined`.

**Correct order:**

```html
<!-- CORRECT ORDER -->
<script src="https://unpkg.com/react@18/..."></script>       <!-- React first -->
<script src="https://unpkg.com/react-dom@18/..."></script>   <!-- ReactDOM second -->
<script src="./app.js"></script>                             <!-- Your app last -->
```

> The instructor says: *"Order matters a lot. Try to play with your code. You will get the 'React is not defined' error if you load things in the wrong order."*

**Homework:** Read about `async` and `defer` attributes on script tags. There are different ways to load scripts, and they affect when the browser executes them. The instructor has a YouTube video about this topic.

---

## 14. Props — Passing Attributes

### Interview Question
**"What are props in the context of `React.createElement`? How do you pass HTML attributes like `id` or `className`?"**

**Answer:**

The second argument to `React.createElement` is the **props object**. Every HTML attribute you want on your element goes here.

```javascript
// Creates: <h1 id="title" className="main-heading">Hello</h1>
const heading = React.createElement(
  'h1',
  { id: 'title', className: 'main-heading' },  // props = HTML attributes
  'Hello'
);
```

> **Important:** In React, use `className` instead of `class` (because `class` is a reserved word in JavaScript). Use `htmlFor` instead of `for`.

When you `console.log` this element, you'll see the attributes inside the `props` object:

```javascript
// The React element object has:
{
  type: 'h1',
  props: {
    id: 'title',
    className: 'main-heading',
    children: 'Hello'
  }
}
```

Notice: `children` is automatically added to `props` — the text 'Hello' becomes `props.children`.

---

## 15. Nested Elements — Children as an Array

### Interview Question
**"How do you create nested HTML structures using `React.createElement`? How do you pass multiple children?"**

**Answer:**

In the lecture, the instructor creates this HTML structure:

```html
<div id="container">
  <h1>Heading 1</h1>
  <h2>Heading 2</h2>
</div>
```

Here's how to do it in React:

```javascript
// Step 1: Create child elements
const heading1 = React.createElement('h1', {}, 'Heading 1');
const heading2 = React.createElement('h2', {}, 'Heading 2');

// Step 2: Create parent element — pass children as an ARRAY
const container = React.createElement(
  'div',
  { id: 'container' },
  [heading1, heading2]   // <-- array of children
);

// Step 3: Render the parent
const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(container);
```

**Why an array?** When you have multiple children, you pass them as an array in the third argument. When there's only one child, you can pass it directly as a string or element.

You can also pass children as additional arguments (not just one array):

```javascript
// These two are equivalent:
React.createElement('div', {id: 'container'}, [heading1, heading2]);
React.createElement('div', {id: 'container'}, heading1, heading2);
```

> The instructor's note: *"This is getting very complicated. Imagine if you had to build a whole website like this! That is why JSX was invented — to make writing React elements much more developer-friendly. We'll cover JSX in the next session."*

---

## 16. Development vs. Production CDN Files

### Interview Question
**"What is the difference between `react.development.js` and `react.production.min.js`?"**

**Answer:**

When you go to React's CDN, you'll see two versions:

| File | Purpose |
|------|---------|
| `react.development.js` | For local development — includes helpful error messages, warnings, and debugging info. Larger file size. |
| `react.production.min.js` | For production (live website) — minified (variable names shortened, whitespace removed), no debug messages. Much smaller file size. |

The actual React logic is the same in both files. The difference is:
- Development build: human-readable, has helpful console warnings
- Production build: machine-optimized, faster to download for users

> The instructor says: *"If you are using React on production, you should use the production file. If you are on local, use the development file. But honestly, we generally won't be injecting React this way at all — there's a much better way we'll learn in the next episode."*

---

## 17. Splitting Code into Separate Files

### Interview Question
**"How do you organize React code across multiple files?"**

**Answer:**

The instructor demonstrates how to move the JavaScript code out of `index.html` into a separate `app.js` file, and link a `index.css` file.

**index.html:**
```html
<!DOCTYPE html>
<html>
<head>
  <title>Namaste React</title>
  <link rel="stylesheet" href="./index.css">  <!-- CSS in head -->
</head>
<body>
  <div id="root">not rendered</div>

  <!-- React must come BEFORE your app.js -->
  <script src="https://unpkg.com/react@18/umd/react.development.js" crossorigin></script>
  <script src="https://unpkg.com/react-dom@18/umd/react-dom.development.js" crossorigin></script>
  <script src="./app.js"></script>  <!-- Your code last -->
</body>
</html>
```

**app.js:**
```javascript
const heading1 = React.createElement('h1', {}, 'Heading 1');
const heading2 = React.createElement('h2', {}, 'Heading 2');

const container = React.createElement(
  'div',
  { id: 'container' },
  [heading1, heading2]
);

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(container);
```

**index.css:**
```css
#container {
  background-color: lightblue;
}
```

> The instructor explains: *"CSS should go in the `<head>` of the document. Your JS scripts should go at the bottom of `<body>`. If you're going to have thousands of lines of code, you don't want it all in one file."*

**Important note about React's philosophy:** React was built with the idea of writing your HTML *inside* JavaScript — not in HTML files. Once we start building real React apps, we'll barely touch `index.html`. Everything happens in JS files.

---

## 18. Why CDN Is Not the Preferred Way (Preview)

### Interview Question
**"Why is using CDN links to import React not recommended for real projects?"**

**Answer:**

The instructor briefly previews this topic (Episode 2 covers it in full):

1. **Version management is hard** — Every time React releases a new version, you have to manually update the CDN URL in your HTML. Error-prone.

2. **Performance** — The React files live on an external CDN server (`unpkg.com`), not your server. In production, it's better (and faster) to bundle React with your own code so it all comes from the same server.

3. **No modern JS features** — CDN approach doesn't let you use `import`/`export` or other modern JavaScript features easily.

The solution: **NPM + a bundler (Parcel/Webpack)** — which is the topic of Episode 2.

---

## 19. Homework from This Episode

The instructor gives several research tasks:

| Homework | Why it matters |
|----------|---------------|
| **What is Emmet?** | VS Code shortcut for HTML boilerplate |
| **What is a CDN?** | Content Delivery Network — how static files are served |
| **What is `crossorigin`?** | Attribute on script tags — security feature for cross-origin requests |
| **What is `async` vs `defer`?** | Different ways to load script tags — affects when browser executes them |
| **What is the DOM?** | Document Object Model — how browsers represent HTML internally |
| **Read React Documentation** | Go to react.dev and read about `createElement` |

> The instructor's advice: *"Whenever you get curious about something I say but don't fully explain, go to MDN docs (developer.mozilla.org) — that's the best reference. Don't go to random websites. Go to the official documentation."*

---

## 20. Simple Coding Exercises

These exercises use only `React.createElement` — **no JSX** — to match what this episode teaches.

---

### Exercise 1 — Create a simple heading

Create an `<h1>` with the text "Hello, React!" and render it inside a `<div id="root">`.

```html
<!DOCTYPE html>
<html>
<body>
  <div id="root">not rendered</div>

  <script src="https://unpkg.com/react@18/umd/react.development.js" crossorigin></script>
  <script src="https://unpkg.com/react-dom@18/umd/react-dom.development.js" crossorigin></script>

  <script>
    // YOUR CODE HERE
    const heading = React.createElement('h1', {}, 'Hello, React!');
    const root = ReactDOM.createRoot(document.getElementById('root'));
    root.render(heading);
  </script>
</body>
</html>
```

---

### Exercise 2 — Add attributes (id and className)

Create a `<p>` tag with `id="intro"` and `className="text-main"` with the content "I am learning React."

```javascript
const para = React.createElement(
  'p',
  { id: 'intro', className: 'text-main' },
  'I am learning React.'
);

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(para);
```

---

### Exercise 3 — Nested structure (parent-child)

Create this HTML using `React.createElement`:

```html
<div id="card">
  <h2>My Card</h2>
  <p>This is my first React card.</p>
</div>
```

```javascript
const title   = React.createElement('h2', {}, 'My Card');
const content = React.createElement('p',  {}, 'This is my first React card.');

const card = React.createElement(
  'div',
  { id: 'card' },
  [title, content]   // array because we have multiple children
);

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(card);
```

---

### Exercise 4 — Console log a React element

Run this and look at the output in your browser console:

```javascript
const el = React.createElement('h1', { id: 'myHeading' }, 'Hello');
console.log(el);
console.log(typeof el);  // What is this? An object!
```

**Expected output:** An object with `type: 'h1'`, `props: { id: 'myHeading', children: 'Hello' }`, etc.

**Key learning:** A React element is **NOT** an HTML element. It's a plain JavaScript object that *describes* what should be rendered.

---

### Exercise 5 — The "not rendered" pattern

Try this deliberately broken code to understand the pattern:

```html
<div id="root">not rendered</div>

<script>
  // This uses a WRONG id ('root1' doesn't exist)
  const root = ReactDOM.createRoot(document.getElementById('root1'));
  root.render(React.createElement('h1', {}, 'This will not show'));
</script>
```

Open the browser — you'll see "not rendered" on the page. Now fix the ID to `'root'` and it works.

---

### Exercise 6 — Script order experiment

1. Create an `app.js` file with your React code.
2. In `index.html`, try loading `app.js` BEFORE the React CDN scripts.
3. Open the browser console — you'll see: **"React is not defined"**
4. Move `app.js` to after the CDN scripts.
5. Everything works again.

This proves the instructor's point: **order of script tags matters**.

---

### Quick Concept Quiz

**Q1:** What are the 3 arguments of `React.createElement`?
> Answer: (1) tag name as string, (2) props object with attributes, (3) children (content inside the tag)

**Q2:** What does `root.render()` do to any existing content inside the root div?
> Answer: It completely replaces it — React overrides everything inside root.

**Q3:** Is React a library or a framework?
> Answer: Library. It can be injected into any part of an existing web page with minimal setup.

**Q4:** What is `react-dom` for? Why is it separate from `react`?
> Answer: `react-dom` handles DOM-specific operations (for web browsers). React itself is platform-agnostic — it can also run on mobile (React Native), so the DOM-specific code lives in a separate package.

**Q5:** What JavaScript type is a React element?
> Answer: An **object** (a plain JavaScript object).

---

> **Coming up in Episode 2:** Why the CDN approach is not enough for production apps. We'll learn about NPM, bundlers, Parcel, and how to "ignite" a React app properly.
