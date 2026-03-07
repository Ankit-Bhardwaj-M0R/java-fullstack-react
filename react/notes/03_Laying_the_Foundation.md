# Episode 03 – Laying the Foundation
### React Interview Prep + Coding Questions

---

## Table of Contents

1. [npm Scripts – What are they and why do we use them?](#1-npm-scripts)
2. [What is a React Element?](#2-what-is-a-react-element)
3. [What is JSX?](#3-what-is-jsx)
4. [JSX is NOT HTML – Key Differences](#4-jsx-is-not-html)
5. [How Does JSX Actually Work? (Babel + Parcel)](#5-how-does-jsx-work-babel--parcel)
6. [Single Line vs Multi-Line JSX](#6-single-line-vs-multi-line-jsx)
7. [What is a React Component?](#7-what-is-a-react-component)
8. [Functional Components – The New Way](#8-functional-components)
9. [How to Render a Component](#9-how-to-render-a-component)
10. [Component Composition](#10-component-composition)
11. [JavaScript Inside JSX – The Superpower](#11-javascript-inside-jsx)
12. [Putting a React Element Inside a Component](#12-react-element-inside-a-component)
13. [Three Ways to Use a Component](#13-three-ways-to-use-a-component)
14. [JSX Prevents Cross-Site Scripting (XSS)](#14-jsx-prevents-cross-site-scripting)
15. [VS Code Extensions Recommended](#15-vs-code-extensions)
16. [Coding Questions](#16-coding-questions)

---

## 1. npm Scripts

### 🎤 Interview Question:
**"What are npm scripts? Why do we use them instead of running commands directly?"**

### 💬 Conversational Answer:

So imagine every day you go to your office and you have to type a huge password to unlock your computer. After a few days, you'd just save that as a shortcut, right? That's exactly what npm scripts are.

When we run our React app using Parcel, we normally type:
```
npx parcel index.html
```

That's fine once or twice. But imagine typing that 50 times a day. That's annoying. So instead, we **define scripts inside `package.json`** and give them short, memorable names.

Here's what it looks like in your `package.json`:

```json
{
  "scripts": {
    "start": "parcel index.html",
    "build": "parcel build index.html"
  }
}
```

Now instead of the long command, you just type:
- `npm run start` → starts the app in development mode
- `npm run build` → creates a production-ready build

There's also a shortcut: `npm start` works the same as `npm run start`. But `npm build` does NOT work — only `npm run build` works for custom scripts. `start` is a special keyword reserved by npm.

### 🏭 Industry Tip:
If you join a new company and don't know how to run their project — just open `package.json`, look at the `scripts` section, and you'll find commands like `start`, `build`, `test`, etc. This is an **industry standard** across all modern JavaScript projects.

---

## 2. What is a React Element?

### 🎤 Interview Question:
**"What is a React element? Is it the same as an HTML element?"**

### 💬 Conversational Answer:

No, a React element is NOT an HTML element. This confuses a lot of developers.

Think of a React element as a **blueprint**. Just like an architect draws a blueprint of a house — the blueprint is not a house, but it describes what the house should look like. When you actually build the house from the blueprint, that's when it becomes real.

Same thing with React:
- A **React element** is just a **JavaScript object** — a blueprint of what you want on the screen.
- When you **render** that object onto the DOM, React converts it into an actual **HTML element** that the browser shows.

Here's how you create a React element the "old core way":

```javascript
const heading = React.createElement(
  "h1",                    // what tag to create
  { id: "heading" },       // attributes (like id, class, etc.)
  "Namaste React"          // content / children
);
```

When you `console.log(heading)`, you'll see it's just a JavaScript object. It only becomes an HTML element when you render it.

To render it, you first create a root and then render inside it:

```javascript
const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(heading);
```

Note: `ReactDOM.createRoot` — not `React.createRoot`. We use `ReactDOM` because we're dealing with the browser's DOM. React's core handles element creation, ReactDOM handles displaying it in the browser.

### ⚠️ Common mistake:
People think `React.createElement` creates an HTML element. No — it creates a JavaScript object. The conversion to HTML happens only when `root.render()` is called.

---

## 3. What is JSX?

### 🎤 Interview Question:
**"What is JSX? Is it HTML? Is it part of React?"**

### 💬 Conversational Answer:

JSX is one of the most misunderstood things in React. Let's clear the air.

**JSX stands for JavaScript XML.** It's a special syntax that lets you write what *looks like* HTML inside your JavaScript code. But it is NOT HTML.

Here's the thing: writing React elements using `React.createElement()` is very messy, especially when your UI gets complex. So Facebook developers created JSX to make developers' lives easier.

Compare these two — they do the exact same thing:

**Without JSX (ugly, hard to read):**
```javascript
const heading = React.createElement("h1", { id: "heading" }, "Namaste React");
```

**With JSX (clean, easy to read):**
```javascript
const heading = <h1 id="heading">Namaste React</h1>;
```

Both of these produce the exact same JavaScript object. If you `console.log` both, the output is identical.

**Key myths to bust:**
- ❌ "JSX is HTML" → Wrong. It just *looks* like HTML.
- ❌ "JSX is part of React" → Wrong. JSX and React are separate things.
- ❌ "You need JSX to use React" → Wrong. You can write React without JSX. But JSX makes it much easier.
- ✅ JSX is an **HTML-like (or XML-like) syntax** that makes writing React elements developer-friendly.

---

## 4. JSX is NOT HTML – Key Differences

### 🎤 Interview Question:
**"What are the differences between JSX and HTML?"**

### 💬 Conversational Answer:

Even though JSX looks a lot like HTML, there are important differences. The moment you try to do HTML things inside JSX and get an error — that's JSX reminding you it's not HTML.

**Difference 1: `class` becomes `className`**

In HTML:
```html
<h1 class="heading">Hello</h1>
```

In JSX:
```jsx
<h1 className="heading">Hello</h1>
```

Why? Because `class` is a reserved keyword in JavaScript. JSX runs inside JavaScript, so it uses `className` instead.

**Difference 2: Attributes use camelCase**

In HTML:
```html
<div tabindex="1">...</div>
```

In JSX:
```jsx
<div tabIndex="1">...</div>
```

The general rule: any attribute with a hyphen in HTML becomes camelCase in JSX.

**Homework from the episode:** Explore how to write `src` in an image tag, `href` in anchor tags, and other common attributes in JSX. It's very similar to HTML but uses camelCase where needed.

**Difference 3: Self-closing tags must be closed**

In JSX, every tag must be properly closed:
```jsx
<img src="photo.jpg" />   // must close with />
<input type="text" />     // must close with />
```

---

## 5. How Does JSX Work? (Babel + Parcel)

### 🎤 Interview Question:
**"Does the browser understand JSX? If not, how does JSX code run?"**

### 💬 Conversational Answer:

No, browsers do not understand JSX at all. Your browser only understands standard JavaScript — specifically ECMAScript (ES6 and above). JSX is not part of that standard.

So when you write JSX and save your file, your browser would normally throw a syntax error. But it doesn't — because of two superheroes: **Parcel** and **Babel**.

Here's the flow of what happens:

```
You write JSX code
        ↓
Parcel detects the save
        ↓
Parcel hands JSX to Babel
        ↓
Babel converts JSX → React.createElement()
        ↓
React.createElement() creates a JS object (React element)
        ↓
root.render() converts that object to HTML
        ↓
Browser shows it on screen
```

**What is Babel?**

Babel is a JavaScript compiler (also called a transpiler). Its job is to take code that browsers can't understand and convert it to code that they can. It reads your JSX line by line, character by character, builds an Abstract Syntax Tree (AST), and outputs `React.createElement()` calls.

You didn't install Babel manually — Parcel installed it for you automatically. If you look in your `node_modules` folder, Babel is already sitting there.

**Fun fact:** Babel is not made by Facebook. It's an independent open-source tool. You can even play with it at [babeljs.io](https://babeljs.io) — paste JSX on the left, see what it converts to on the right.

**What is Parcel doing here?**

Parcel is like a manager. It doesn't do the transpilation itself — it delegates that job to Babel. Parcel's job is to coordinate everything and give you a fast development experience with hot reloading.

**Why does this matter for interviews?**
When an interviewer asks "how does JSX work?", most candidates say "React converts JSX." That's incomplete. The correct answer is: **Babel converts JSX to React.createElement(), and Parcel manages Babel as part of the build process.**

---

## 6. Single Line vs Multi-Line JSX

### 🎤 Interview Question:
**"Do you need to wrap JSX in parentheses?"**

### 💬 Conversational Answer:

It depends on whether your JSX is one line or multiple lines.

**Single line JSX — parentheses optional:**
```javascript
const heading = <h1>Namaste React</h1>;
```

**Multi-line JSX — parentheses required:**
```javascript
const heading = (
  <div>
    <h1>Namaste React</h1>
    <p>Episode 3</p>
  </div>
);
```

Why? Because when JSX is split across multiple lines, Babel needs to know exactly where the JSX starts and where it ends. The parentheses act as a clear boundary marker. Without them, Babel can get confused and throw errors.

---

## 7. What is a React Component?

### 🎤 Interview Question:
**"What is a React component? What are the two types of components?"**

### 💬 Conversational Answer:

You've heard "everything in React is a component." But what actually IS a component?

Think of a webpage. It has a header, a footer, a search bar, cards, buttons, a sidebar. Each of those is a **component** — a reusable, self-contained piece of UI.

There are **two types of components** in React:

**1. Class-based Components (old way)**
```javascript
class Heading extends React.Component {
  render() {
    return <h1>Namaste React</h1>;
  }
}
```

**2. Functional Components (new way — what we use today)**
```javascript
const Heading = () => {
  return <h1>Namaste React</h1>;
};
```

**Which one should you use?**

Always use **functional components**. Class-based components are the old way of doing things. In 2024, 99.99% of new React projects use functional components. However, you should still be aware of class-based components because:
- Many legacy (older) projects still use them
- Interviewers sometimes still ask about them

We'll cover class-based components later in this series, but the main focus is functional components.

---

## 8. Functional Components

### 🎤 Interview Question:
**"What exactly is a React functional component?"**

### 💬 Conversational Answer:

A React functional component is simply a **normal JavaScript function that returns some JSX**.

That's it. No magic. No mystery.

```javascript
const HeadingComponent = () => {
  return (
    <h1 className="heading">Namaste React - Functional Component</h1>
  );
};
```

**Three things to remember:**
1. The name must start with a **Capital Letter** (`HeadingComponent`, not `headingComponent`). React uses this to distinguish components from regular HTML tags.
2. It must **return JSX** (or a React element).
3. It's a **regular JavaScript function** — you can use arrow functions or regular `function` keyword.

**Arrow function (common industry style):**
```javascript
const HeadingComponent = () => (
  <h1>Namaste React</h1>
);
```

**Regular function (also valid):**
```javascript
function HeadingComponent() {
  return <h1>Namaste React</h1>;
}
```

**Shorthand (no return keyword — for single-line JSX returns):**
```javascript
// With return
const HeadingComponent = () => {
  return <h1>Namaste React</h1>;
};

// Without return (shorthand arrow function)
const HeadingComponent = () => <h1>Namaste React</h1>;

// Both are identical!
```

The instructor prefers the explicit `return` style because it's clearer. But you'll see all three styles in the industry — don't panic when you see the shorthand version.

**What's the relationship between a React element and a functional component?**

| | React Element | Functional Component |
|---|---|---|
| What is it? | A JSX expression assigned to a variable | A JavaScript function that returns JSX |
| Example | `const el = <h1>Hi</h1>` | `const El = () => <h1>Hi</h1>` |
| Name convention | lowercase | PascalCase (Capital first letter) |

---

## 9. How to Render a Component

### 🎤 Interview Question:
**"How do you render a React component? Is it different from rendering a React element?"**

### 💬 Conversational Answer:

Yes, there's a difference in syntax.

**Rendering a React element:**
```javascript
const heading = <h1>Namaste React</h1>;
root.render(heading);  // pass the variable directly
```

**Rendering a functional component:**
```javascript
const HeadingComponent = () => <h1>Namaste React</h1>;
root.render(<HeadingComponent />);  // use it like an HTML tag
```

You wrap the component name in angle brackets (`< />`) to render it. This is the syntax Babel understands — when it sees a capital-letter tag in JSX, it knows it's a component and looks for a matching function definition.

You can also use a self-closing tag if the component has no children:
```jsx
<HeadingComponent />
// or
<HeadingComponent></HeadingComponent>
// Both are the same!
```

---

## 10. Component Composition

### 🎤 Interview Question:
**"What is component composition in React?"**

### 💬 Conversational Answer:

Component composition simply means **putting one component inside another component**. That's it. It's a fancy term interviewers love to use for something very simple.

```javascript
const Title = () => (
  <h2>I am the Title Component</h2>
);

const HeadingComponent = () => (
  <div className="container">
    <Title />       {/* Title component used inside HeadingComponent */}
    <h1>Namaste React</h1>
  </div>
);
```

Here, `Title` is composed inside `HeadingComponent`. This is **component composition**.

Why is this powerful? Because you can:
- Build small, focused components
- Reuse them anywhere by including them inside other components
- Build complex UIs by composing simple pieces together

Think of it like LEGO blocks — each component is a block, and you compose them to build something bigger.

**Interview tip:** If an interviewer asks "what is component composition?" — they're asking about using one component inside another. Now you know!

---

## 11. JavaScript Inside JSX – The Superpower

### 🎤 Interview Question:
**"Can you write JavaScript inside JSX? How?"**

### 💬 Conversational Answer:

Yes! This is one of JSX's biggest superpowers. Inside JSX, **anywhere you want**, you can use curly braces `{}` to write any valid JavaScript expression.

```javascript
const number = 10000;

const HeadingComponent = () => (
  <div>
    <h1>Namaste React</h1>
    <h2>{number}</h2>           {/* variable */}
    <h2>{100 + 200}</h2>        {/* math → shows 300 */}
    <h2>{2 * 5}</h2>            {/* expression → shows 10 */}
  </div>
);
```

You can put:
- Variables
- Math expressions
- Function calls
- Ternary operators (we'll see these later)
- Even `console.log()` — it will run when the component renders

```javascript
const HeadingComponent = () => (
  <div>
    {console.log("Component is rendering!")}
    <h1>Hello</h1>
  </div>
);
```

**Why is this so powerful?**

Because it means your UI and your logic can live together. Data from an API? Put it in curly braces and it shows up on screen. Want to show a different message based on a condition? Use a ternary inside curly braces. This is the bridge between JavaScript logic and the visual output.

You'll use this constantly as you build real apps.

---

## 12. React Element Inside a Component

### 🎤 Interview Question:
**"Can you use a React element inside a functional component? How?"**

### 💬 Conversational Answer:

Yes! Remember — a React element is just a JavaScript variable containing an object. And since you can write any JavaScript inside JSX curly braces, you can just put that variable in there.

```javascript
// A React element (just a JSX expression stored in a variable)
const title = <h2>I am a React Element</h2>;

// A functional component that uses that element
const HeadingComponent = () => (
  <div>
    {title}       {/* inject the React element */}
    <h1>Namaste React</h1>
  </div>
);
```

You can also mix and match:
- React element inside a React element
- React element inside a component
- Component inside a React element
- Component inside a component

React and JSX are very flexible. As long as you understand that everything is ultimately JavaScript, you'll know what's possible.

---

## 13. Three Ways to Use a Component

### 🎤 Interview Question:
**"What are the different ways to use/render a component inside JSX?"**

### 💬 Conversational Answer:

There are actually three ways to use a component inside JSX, and they all produce the same result. This is something many experienced React developers don't even know.

```javascript
const Title = () => <h2>I am Title</h2>;

const HeadingComponent = () => (
  <div>
    {/* Way 1: Self-closing tag */}
    <Title />

    {/* Way 2: Opening and closing tag */}
    <Title></Title>

    {/* Way 3: Call it as a regular JavaScript function */}
    {Title()}
  </div>
);
```

All three produce identical output. Why?

Because a functional component is just a regular JavaScript function. And inside JSX curly braces, you can call any JavaScript function. So calling `{Title()}` is perfectly valid — it executes the `Title` function and injects whatever JSX it returns.

The first two ways (`<Title />` and `<Title></Title>`) are the conventional React way. The third way (`{Title()}`) is the JavaScript-native way. They're all the same under the hood.

This is why understanding the foundations matters — once you know it's all JavaScript, everything makes sense.

---

## 14. JSX Prevents Cross-Site Scripting (XSS)

### 🎤 Interview Question:
**"How does JSX handle malicious data? What is cross-site scripting?"**

### 💬 Conversational Answer:

This is a security concept that most developers don't think about — but JSX handles it automatically.

Imagine your React app calls an API to get some data to display on screen. You write:

```javascript
const data = getData(); // data comes from an API
const HeadingComponent = () => (
  <div>{data}</div>
);
```

Now imagine a hacker finds a way to make that API return malicious JavaScript code instead of normal data. If your app blindly runs whatever comes from the API, the attacker could:
- Steal cookies (session tokens, login credentials)
- Access localStorage
- Run harmful scripts in the user's browser

This attack is called **Cross-Site Scripting (XSS)**.

**How does JSX protect you?**

JSX automatically **sanitizes (escapes) any data** you put inside curly braces. It does NOT blindly execute whatever comes in. Instead, it treats incoming data as plain text, not as code. So even if an attacker injects `<script>stealYourCookies()</script>` through an API, JSX will display it as plain text on screen — it won't run it.

This is a huge advantage of using JSX. You get XSS protection for free without writing any extra code.

**Interview gold:** Most developers say "JSX is just nice syntax." The correct, deep answer includes: JSX also provides built-in protection against cross-site scripting attacks.

---

## 15. VS Code Extensions Recommended

The instructor recommends installing these extensions to make your React development smoother:

| Extension | What it does |
|---|---|
| **Prettier - Code Formatter** | Automatically formats your code on save. Fixes indentation, line breaks, etc. |
| **Bracket Pair Colorizer** | Colors matching brackets in different colors so you can easily see where each bracket opens and closes |
| **ESLint** | Highlights potential bugs and code style issues as you type |
| **Better Comments** | Makes your comments look nicer and more readable |

**How to use Prettier:** Once installed, go to VS Code settings and enable "Format on Save." Every time you hit Ctrl+S, your code will auto-format beautifully.

---

## 16. Coding Questions

These are simple coding exercises based on everything covered in this episode. Try writing them yourself before looking at the answer.

---

### Q1. Create a React element using `React.createElement` and render it

```javascript
import React from "react";
import ReactDOM from "react-dom/client";

// Create a React element (an h1 tag with an id and text)
const heading = React.createElement(
  "h1",
  { id: "main-heading" },
  "Hello from React.createElement"
);

// Create the root and render
const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(heading);
```

**What to observe:** Open your browser console and `console.log(heading)` — you'll see it's a JavaScript object, not an HTML element.

---

### Q2. Create the same h1 using JSX

```javascript
import React from "react";
import ReactDOM from "react-dom/client";

// JSX way — much cleaner!
const heading = <h1 id="main-heading">Hello from JSX</h1>;

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(heading);
```

**Console log both** — they produce the same object!

---

### Q3. Write a functional component

```javascript
// A basic functional component
const MyComponent = () => {
  return (
    <div>
      <h1>I am a Functional Component</h1>
      <p>This is a paragraph inside it.</p>
    </div>
  );
};

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(<MyComponent />);
```

---

### Q4. Use className (not class) in JSX

```javascript
const StyledHeading = () => {
  return (
    <h1 className="main-title" tabIndex="1">
      JSX uses className, not class!
    </h1>
  );
};
```

---

### Q5. Inject a JavaScript variable inside JSX

```javascript
const userName = "Kabir";
const score = 95;

const ProfileCard = () => {
  return (
    <div>
      <h1>Hello, {userName}!</h1>
      <p>Your score is: {score}</p>
      <p>Double your score: {score * 2}</p>
    </div>
  );
};
```

---

### Q6. Component Composition — use one component inside another

```javascript
// Small component
const Title = () => {
  return <h2>I am the Title</h2>;
};

// Bigger component that uses Title inside it
const Page = () => {
  return (
    <div>
      <Title />
      <p>This is the main content of the page.</p>
    </div>
  );
};

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(<Page />);
```

---

### Q7. Use a React element inside a component using curly braces

```javascript
// React element (not a component — lowercase variable)
const banner = <h3>I am a React Element (just a variable)</h3>;

// Component that uses the above element
const App = () => {
  return (
    <div>
      {banner}
      <p>Above is a React element injected into a component.</p>
    </div>
  );
};
```

---

### Q8. Use all three ways to render a component (for curiosity)

```javascript
const Badge = () => <span>⭐ Badge</span>;

const App = () => {
  return (
    <div>
      <Badge />           {/* Way 1: Self-closing */}
      <Badge></Badge>     {/* Way 2: Open + Close */}
      {Badge()}           {/* Way 3: Call as function */}
    </div>
  );
};
```

All three produce identical output. This works because `Badge` is just a JavaScript function.

---

### Q9. npm script setup (package.json)

```json
{
  "name": "my-react-app",
  "scripts": {
    "start": "parcel index.html",
    "build": "parcel build index.html"
  },
  "dependencies": {
    "react": "^18.0.0",
    "react-dom": "^18.0.0"
  },
  "devDependencies": {
    "parcel": "^2.0.0"
  }
}
```

To run:
- Development: `npm start` or `npm run start`
- Production build: `npm run build`

---

### Q10. Multi-line JSX with parentheses

```javascript
// ❌ Wrong — multi-line without parentheses (Babel might misread this)
const El = () =>
  <div>
    <h1>Hello</h1>
  </div>;

// ✅ Correct — wrap multi-line JSX in parentheses
const El = () => (
  <div>
    <h1>Hello</h1>
  </div>
);
```

---

## 🗒️ Quick Revision Card

| Concept | Key Point |
|---|---|
| npm scripts | Shortcuts defined in `package.json`; `npm start` = `npm run start` |
| React element | A JavaScript object created by `React.createElement()` or JSX |
| JSX | HTML-like syntax; NOT HTML; NOT part of React |
| Babel | Transpiler that converts JSX → `React.createElement()` |
| Parcel | Build tool that manages Babel and hosts your dev server |
| Functional component | A JavaScript function that returns JSX |
| Component name | Must start with Capital letter |
| `className` | Use instead of `class` in JSX |
| camelCase attributes | `tabIndex`, `className`, `onClick`, etc. |
| `{}` in JSX | Runs any JavaScript expression |
| Component composition | Using one component inside another |
| XSS protection | JSX automatically sanitizes data inside `{}` |
| 3 ways to use a component | `<Comp />`, `<Comp></Comp>`, `{Comp()}` |

---

*Episode 03 – Namaste React Series | Laying the Foundation*
