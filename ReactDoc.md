# What is React? What does "declarative UI library" mean?

**React** is an open-source **JavaScript library** used to build fast, interactive, and reusable **user interfaces (UI)**, mainly for web applications.

React is a **declarative UI library**, it means:

* you declare **what** (UI should look like for a given state.)
* React handles the **how** (re-renders the UI when the state changes, keeping it in sync with the data).
<br><br>


# Library vs Framework: React’s place in the ecosystem

### **Library**

* A collection of reusable code (functions, components, utilities) you can **call when needed**.
* **You control the flow** of the application and decide **when & how** to use the library.

### **Framework**

* Provides a **complete structure & rules** for building applications.
* The framework usually **controls the flow**, and you plug your code into it.
<br><br>


**React is a library** for building UI.

  * It focuses only on rendering views.
  * You decide how to handle routing, state management, data fetching, etc.

To build a full-fledged application, React is often combined with other tools/libraries (e.g., **React Router, Redux, Next.js**).
<br>
<br>

# What is JSX? How is it transpiled (by Babel) and rendered?

### **What is JSX?**

* **JSX (JavaScript XML)** is a syntax extension for JavaScript that looks like HTML.
* It allows you to **write UI structure (tags)** directly inside JavaScript code.
* Example:

  ```jsx
  const element = <h1>Hello, React!</h1>;
  ```

### **How JSX is Transpiled (by Babel)?**

* Browsers **don’t understand JSX** natively.
* **Babel (a transpiler)** converts JSX into plain JavaScript `React.createElement` calls.
* Example:

  ```jsx
  const element = <h1>Hello</h1>;
  ```

  becomes:

  ```js
  const element = React.createElement("h1", null, "Hello");
  ```

### **How it is Rendered (by React)?**

1. `React.createElement` returns a **React Element** (a plain JS object describing the UI).

   ```js
   {
     type: "h1",
     props: { children: "Hello" }
   }
   ```
2. React’s **reconciler** builds a virtual DOM tree from these objects.
3. React’s **renderer (ReactDOM)** compares it with the real DOM (diffing) and updates only what changed.

👉 In short:
**JSX → Babel → React.createElement → Virtual DOM → Real DOM update**
<br>
<br>


# How the browser reads JSX ?

The browser **does not understand JSX directly**. The flow looks like this:

1. **Developer writes JSX**

   ```jsx
   const element = <h1>Hello World</h1>;
   ```

2. **Build tools (Babel + Webpack/Vite)** transpile JSX → plain JavaScript.

   ```js
   const element = React.createElement("h1", null, "Hello World");
   ```

3. **React creates a Virtual DOM object** from that code:

   ```js
   {
     type: "h1",
     props: { children: "Hello World" }
   }
   ```

4. **ReactDOM renderer** takes this Virtual DOM and updates the **real DOM** in the browser:

   ```html
   <h1>Hello World</h1>
   ```

👉 So, the **browser never reads JSX directly**.
It only ever sees **JavaScript + DOM instructions** after Babel/React transpilation.
<br>
<br>

# JSX data sanitization and XSS concerns

### **JSX curly braces `{}`**

* When you write `{}` inside JSX, it means **embed JavaScript expressions**.
  Example:

  ```jsx
  const name = "<img src=x onerror=alert('hacked') />";
  <div>{name}</div>
  ```


### **How JSX Sanitizes Data**

* React **automatically escapes**(Sanitizes) values inserted inside `{}`.
* Escaping means: converts special HTML characters (`<`, `>`, `&`, `"`, `'`) into safe text (e.g., `&lt;`, `&gt;`).
* So the above becomes:

  ```html
  <div>&lt;img src=x onerror=alert('hacked') /&gt;</div>
  ```
* This prevents malicious code (XSS attacks) from executing.


### **How it works internally**

1. **JSX transpilation (via Babel)** → `React.createElement(type, props, ...children)`

   ```jsx
   <div>{name}</div>
   ```

   becomes:

   ```js
   React.createElement("div", null, name);
   ```

2. React builds a **virtual DOM node** where `children = name` (a plain string).

3. During rendering:

   * React uses the DOM API methods like `textContent` or `setAttribute` (NOT `innerHTML`).
   * These methods **insert text safely** (browser escapes it).

4. Result: Content is displayed as text, not executed as code.

---

⚠️ **Exception:** If you explicitly use `dangerouslySetInnerHTML`, React skips sanitization and directly injects HTML. This is risky if data is not trusted.

---

👉 In short:

* `{}` in JSX is **safe by default**.
* React escapes inserted values before rendering by relying on the **DOM APIs instead of raw HTML injection**.
<br>
<br>

# What is a CDN and how does it enhance performance?

### **What is a CDN?**

A **Content Delivery Network (CDN)** is a globally distributed network of servers that delivers web content (like images, CSS, JS files, videos) from the **server closest to the user’s location** instead of always hitting the origin server.

### **How CDN Enhances Performance**

1. **Reduced Latency** – Content comes from a nearby server (edge location), so pages load faster.
2. **Faster Asset Delivery** – Static files (JS, CSS, images) are cached across multiple servers worldwide.
3. **Offloading Origin Server** – Less load on your main server since CDNs handle most static requests.
4. **Parallel Downloads** – Browsers can fetch files from CDN domains while also fetching from your origin.
5. **Scalability & Reliability** – During high traffic or DDoS attacks, CDNs balance load across multiple servers.

---

👉 Example:
Without CDN → User in India requests your US-hosted site → Long round trip.
With CDN → User in India gets content from a nearby CDN server in Mumbai → Much faster.
<br>
<br>

# Script tag attributes (async, defer, crossOrigin, type, etc ) — purpose and use cases ?
`<script>` tag attributes:

### 1. **`src`**

* URL of the external JavaScript file.
* Example:

  ```html
  <script src="app.js"></script>
  ```

### 2. **`async`**

* Loads the script **asynchronously** (in parallel with HTML parsing).
* Executes **immediately when loaded** (pause HTML parsing, can block DOM if it finishes early).
* Good for **independent scripts** (ads, analytics, tracking).
* Example:

  ```html
  <script src="analytics.js" async></script>
  ```

### 3. **`defer`**

* Loads script in parallel but **executes after HTML parsing is done**.
* Maintains order of multiple deferred scripts.
* Best for **main app scripts**.
* Example:

  ```html
  <script src="main.js" defer></script>
  ```

### 4. **`type`**

* Defines the script’s MIME type.
* Default = `"text/javascript"` (usually omitted).
* Other uses:

  * `"module"` → ES Modules (supports `import/export`).
  * `"application/ld+json"` → JSON-LD structured data.
* Example:

  ```html
  <script type="module" src="app.js"></script>
  ```

### 5. **`crossorigin`**

* Controls cross-origin requests (important for CDNs + CORS).
* Values:

  * `"anonymous"` → Sends no credentials (cookies, headers).
  * `"use-credentials"` → Sends credentials.
* Used with **SRI (Subresource Integrity)**.
* Example:

  ```html
  <script src="cdn.js" crossorigin="anonymous"></script>
  ```

### 6. **`integrity`**

* Works with `crossorigin` for **Subresource Integrity**.
* Ensures file hasn’t been tampered with (checksum validation).
* Example:

  ```html
  <script src="cdn.js" 
          integrity="sha384-abc123" 
          crossorigin="anonymous"></script>
  ```

### 7. **`nomodule`**

* Used to provide fallback for browsers **without ES Module support**.
* Example:

  ```html
  <script nomodule src="legacy.js"></script>
  ```


### 8. **`referrerpolicy`**

* Controls how much referrer info is sent with requests.
* Example:

  ```html
  <script src="cdn.js" referrerpolicy="no-referrer"></script>
  ```

---

### 9. **`charset`** (rarely used now)

* Specifies character encoding of the script file.
* Example:

  ```html
  <script src="app.js" charset="UTF-8"></script>
  ```
<br>
<br>

# Functional vs Class based component

**Functional components** are simple JavaScript functions that return JSX and use **React Hooks** like `useState` and `useEffect` for state and lifecycle management. They’re lightweight, easier to read, and are the **modern standard** in React.

**Class components**, on the other hand, are ES6 classes that use `this.state` and lifecycle methods like `componentDidMount`. They require more boilerplate and are mostly used in **legacy codebases**.

In modern React, **functional components are preferred** because they’re cleaner, more efficient, and fully support hooks.”
<br>
<br>

# 🚀 Component composition

**Component Composition** is a **React design pattern** where you **combine smaller, reusable components** together to build complex UIs instead of relying on **inheritance**.

> React prefers **composition over inheritance**.

Example:

```jsx
function Card({ children }) {
  return <div className="card">{children}</div>;
}

function App() {
  return (
    <Card>
      <h2>Hello World</h2>
      <p>This is inside the card!</p>
    </Card>
  );
}
```

Here, `<Card>` **composes** `<h2>` and `<p>` inside it.


## **🔹 Why Use Composition?**

* Encourages **reusability** of components
* Avoids **prop drilling** and deep inheritance
* Keeps code **cleaner and modular**
* Makes components more **flexible and customizable**


## **🔹 Types of Component Composition**

### **1. Children-based Composition (Most Common)**

* Uses `props.children` to pass UI from **parent** to **child**.

```jsx
function Modal({ children }) {
  return <div className="modal">{children}</div>;
}

<Modal>
  <h1>Title</h1>
  <p>Description</p>
</Modal>
```

**Use case:** Wrappers, cards, layouts, modals, etc.


### **2. Component Injection (Passing Components as Props)**

Instead of JSX, pass **components themselves** as props.

```jsx
function Layout({ Header, Content }) {
  return (
    <div>
      <header>{Header}</header>
      <main>{Content}</main>
    </div>
  );
}

<Layout
  Header={<h1>Dashboard</h1>}
  Content={<p>Welcome back!</p>}
/>
```

**Use case:** Highly customizable layouts and dashboards.

---

### **3. Special Props Composition**

Use **named props** instead of `children` for multiple “slots.”

```jsx
function SplitScreen({ left, right }) {
  return (
    <div className="split">
      <div>{left}</div>
      <div>{right}</div>
    </div>
  );
}

<SplitScreen
  left={<Sidebar />}
  right={<MainContent />}
/>
```

**Use case:** When you need **multiple flexible regions**.

---

### **4. Higher-Order Components (HOCs)** *(Older Pattern)*

A function that **takes a component** and **returns a new one**.
Used for cross-cutting concerns like auth, logging, analytics.

```jsx
function withLogger(WrappedComponent) {
  return function Logger(props) {
    console.log('Props:', props);
    return <WrappedComponent {...props} />;
  };
}

const ProfileWithLogger = withLogger(Profile);
```

**Use case:** Enhancing components without modifying them.

---

### **5. Render Props Pattern** *(Pre-Hooks)*

Instead of passing JSX, pass a **function** as a prop.

```jsx
function DataFetcher({ render }) {
  const data = { name: "Divas" };
  return render(data);
}

<DataFetcher render={(data) => <h1>{data.name}</h1>} />
```

**Use case:** Sharing complex logic between components.

---

## **🔹 Best Practices**

✅ Use **children** for simple wrappers.<br>
✅ Use **named slots (props)** when multiple regions exist.<br>
✅ Use **custom hooks** instead of HOCs or render props (modern approach).<br>
✅ Avoid deep inheritance — **composition is preferred**.<br>


## **🔹 Quick Summary Table**

| **Pattern**         | **How It Works**             | **Best Use Case**                |
| ------------------- | ---------------------------- | -------------------------------- |
| Children-based      | Use `props.children`         | Wrappers, layouts, modals        |
| Component injection | Pass components as props     | Flexible UIs                     |
| Named slots         | Use multiple named props     | Dashboards, multi-region layouts |
| HOCs                | Function returning component | Logging, auth, analytics         |
| Render props        | Pass function as prop        | Sharing complex logic            |
---
<br>
<br>

# Presentational vs Container components (Smart/Dumb, Stateless/Stateful)

* Presentational Components → Focus on how things look
* Container Components → Focus on how things work

### 1. **`Presentational Components`**
* Concerned with UI and layout only.
* Receive data and callbacks via props.
* Do not manage state (except minor UI state like a toggle).

### 2. **`Container Components`**
* Concerned with data fetching, state management, and business logic.
* Decide what data to pass to presentational components.
<br>
<br>

# Children props, createElement, cloneElement


### **1. `props.children`**
`props.children` is a special React prop automatically passed to components, representing the **content inside** the component's opening and closing tags.

### **Example**

```jsx
function Card({ children }) {
  return <div className="card">{children}</div>;
}

<Card>
  <h2>Hello</h2>
  <p>This is inside the card</p>
</Card>
```

**How it works internally:**

* React converts the nested JSX (`<h2>` and `<p>`) into an **array of React elements**.
* Inside `Card`, `props.children` will be:

```js
[
  { type: 'h2', props: { children: 'Hello' }, ... },
  { type: 'p', props: { children: 'This is inside the card' }, ... }
]
```

### **Use Cases**

<br>
✅ Building **wrappers** (cards, modals, layouts)
<br>
✅ Composing **complex UIs** dynamically
<br>
✅ Passing **any JSX or components** as children
<br>

### **2. `React.createElement()`**
JSX is just **syntactic sugar** for `React.createElement`.
React uses this function internally to **create a virtual DOM node**.

### **Example**

```jsx
const element = <h1 className="title">Hello</h1>;
```

Babel transpiles this into:

```js
const element = React.createElement("h1", { className: "title" }, "Hello");
```

**How it works internally:**

* Returns a **React element object**, not an HTML element.

```js
{
  type: "h1",
  props: { className: "title", children: "Hello" },
  key: null,
  ref: null,
  ...
}
```

* Later, **ReactDOM** takes this object, compares it with the **Virtual DOM**, and updates the **real DOM** efficiently.

### **Use Cases**

✅ Creating elements **without JSX**<br>
✅ Dynamically generating components<br>
✅ Useful for frameworks/libraries that manipulate React elements programmatically<br>
<br>

### **3. `React.cloneElement()`**
`React.cloneElement()` creates a **new React element** by **cloning an existing one** and optionally **overriding or adding props**.

### **Syntax**

```js
React.cloneElement(element, [props], [...children])
```

### **Example**

```jsx
const Button = ({ children }) => <button>{children}</button>;

const original = <Button>Click Me</Button>;
const cloned = React.cloneElement(original, { className: "primary" }, "Press Me");
```

**Resulting JSX**

```jsx
<button class="primary">Press Me</button>
```

### **Why use it?**

* Modify a child **without changing the original**.
* Useful when you don’t control child components but need to inject props.


### **4. `React.Children` Utility (Bonus)**

React provides a few helper methods to work with `props.children` safely:

* `React.Children.map(children, fn)` → Iterate over children
* `React.Children.count(children)` → Count total children
* `React.Children.only(children)` → Ensure there’s exactly **one** child
* `React.Children.toArray(children)` → Convert children to a flat array

**Example**

```jsx
function List({ children }) {
  return (
    <ul>
      {React.Children.map(children, child => (
        <li>{child}</li>
      ))}
    </ul>
  );
}

<List>
  <span>A</span>
  <span>B</span>
</List>
```

**Output:**

```html
<ul>
  <li><span>A</span></li>
  <li><span>B</span></li>
</ul>
```


### **5. Summary Table**

| Feature              | Purpose                            | Accepts                          | Returns              | Use Case                 |
| -------------------- | ---------------------------------- | -------------------------------- | -------------------- | ------------------------ |
| **`props.children`** | Access elements inside a component | Any JSX/element                  | Whatever was passed  | Composition              |
| **`createElement`**  | Create React element objects       | Type, props, children            | React element object | JSX under the hood       |
| **`cloneElement`**   | Clone & modify existing elements   | Element, new props, new children | New React element    | Inject props dynamically |

---
<br>
<br>

# Controlled vs Uncontrolled Components

### **1. What Are Controlled Components?** ✅

In **controlled components**, the **form data** (input value, checkbox, textarea, etc.) is **fully controlled by React state**.
* The **source of truth** = **React state**
* You **set the value** via `state` and **update it** via `onChange`
* React always **knows** the current value.

### **Example: Controlled Input**

```jsx
import { useState } from "react";

function ControlledForm() {
  const [name, setName] = useState("");

  const handleChange = (e) => {
    setName(e.target.value); // Update state
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    alert(`Submitted: ${name}`);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input type="text" value={name} onChange={handleChange} />
      <button type="submit">Submit</button>
    </form>
  );
}
```

### **How It Works Internally**

* The `<input>` does **not** manage its own value.
* React stores the value in `state`.
* Whenever you type:

  1. `onChange` fires
  2. `setState()` updates the value
  3. Component **re-renders**
  4. `value={state}` sets the latest input value

### **2. What Are Uncontrolled Components?** ⚡
In **uncontrolled components**, the **form data** is handled by the **DOM itself**, **not React**.

* The **source of truth** = **DOM**
* We use a **ref** to read the value **when needed**.

### **Example: Uncontrolled Input**

```jsx
import { useRef } from "react";

function UncontrolledForm() {
  const inputRef = useRef();

  const handleSubmit = (e) => {
    e.preventDefault();
    alert(`Submitted: ${inputRef.current.value}`); // Read value from DOM
  };

  return (
    <form onSubmit={handleSubmit}>
      <input type="text" ref={inputRef} />
      <button type="submit">Submit</button>
    </form>
  );
}
```

### **How It Works Internally**

* The browser manages the `<input>`’s value.
* React **doesn’t track** state changes on each keystroke.
* We only **read** the value **on-demand** using `ref`.


### <u>Key Differences</u>

| Feature             | **Controlled Components** ✅         | **Uncontrolled Components** ⚡                   |
| ------------------- | ----------------------------------- | ----------------------------------------------- |
| **Source of Truth** | React `state`                       | DOM                                             |
| **Value Binding**   | `value={state}`                     | No binding, uses `defaultValue` or direct input |
| **Event Handling**  | Requires `onChange`                 | Optional                                        |
| **Accessing Value** | Directly from `state`               | Using `ref` or `document.querySelector`         |
| **Re-renders**      | On every keystroke                  | No re-render on typing                          |
| **Performance**     | Slightly slower for huge forms      | Faster for simple forms                         |
| **Validation**      | Easy to validate on change          | Harder, need manual checks                      |
| **Best For**        | Dynamic forms, real-time UI updates | Simple, static forms                            |

---
### **Mixing Controlled & Uncontrolled** ✅

Sometimes we combine both approaches for better performance.

### **Example: Hybrid**

```jsx
function HybridForm() {
  const [name, setName] = useState("Divas");
  const ageRef = useRef();

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log("Name:", name);          // Controlled
    console.log("Age:", ageRef.current.value); // Uncontrolled
  };

  return (
    <form onSubmit={handleSubmit}>
      <input value={name} onChange={(e) => setName(e.target.value)} />
      <input type="number" ref={ageRef} defaultValue={25} />
      <button>Submit</button>
    </form>
  );
}
```

### **5. When to Use Which?**

### ✅ **Use Controlled Components When:**

* You need **real-time validation**
* You want to **manipulate input values** (e.g., auto-capitalize)
* You’re building **dynamic forms**
* You need to sync values across components

### ⚡ **Use Uncontrolled Components When:**

* You don’t need frequent updates
* You want **better performance** for large forms
* You only read values **on submit**

---

# **6. Internal Working — Behind the Scenes**

### **Controlled**

1. JSX → Babel → `React.createElement()`
2. Virtual DOM stores `props.value`
3. On every keystroke:

   * React calls `onChange`
   * Updates `state`
   * Re-renders component
   * DOM gets updated → **Single source of truth**

### **Uncontrolled**

1. JSX creates `<input>` normally.
2. Browser manages its own internal state.
3. React only uses `ref` to **read** or **set** value when required.

### **Summary📌**
* **Controlled = React controls the value**
* **Uncontrolled = DOM controls the value**
* Controlled gives **more power** but causes **more re-renders**
* Uncontrolled is **simpler** and **faster** but less flexible
<br>
<br>

# Refs, Forwarded Refs and useImperativeHandle

### **1. What Are Refs in React?** 📌

**Refs** give us a way to directly **access and manipulate DOM elements** or **store mutable values** without triggering re-renders.

### **Creating a Ref**

```jsx
import { useRef } from "react";

function Example() {
  const inputRef = useRef(null);

  const focusInput = () => {
    inputRef.current.focus(); // Directly focus input element
  };

  return (
    <>
      <input ref={inputRef} type="text" />
      <button onClick={focusInput}>Focus</button>
    </>
  );
}
```

### **How It Works Internally**

* `useRef(initialValue)` → returns `{ current: initialValue }`
* `ref.current` → mutable object, **not reactive** (no re-render when updated)
* React attaches the `ref` to the **DOM node** during **rendering**.

### **When to Use useRef** ⚡

### ✅ **Use Cases**

1. **Accessing DOM elements** (focus, scroll, etc.)
2. **Storing mutable values** without re-rendering
3. **Persisting values between renders**
4. **Integrating with third-party libraries** (Canvas, charts, etc.)

### **Example: Storing a Mutable Counter**

```jsx
function Counter() {
  const count = useRef(0);

  const handleClick = () => {
    count.current += 1;   // Won't cause re-render
    console.log("Count:", count.current);
  };

  return <button onClick={handleClick}>Click</button>;
}
```

> ⚡ Unlike `useState`, updating `ref.current` does **not** trigger a re-render.


## **2. Forwarding Refs (forwardRef)** 🔄

Normally, **refs** don’t work with **custom components**, because refs only work on **DOM nodes** by default.
**forwardRef** solves this by **passing the ref down** to a child component.

### **Example Without forwardRef** ❌

```jsx
function Child() {
  return <input type="text" />;
}

export default function Parent() {
  const inputRef = useRef();
  return <Child ref={inputRef} />; // ❌ Won't work
}
```

### **Example With forwardRef** ✅

```jsx
import { forwardRef, useRef } from "react";

const Child = forwardRef((props, ref) => {
  return <input ref={ref} type="text" />;
});

export default function Parent() {
  const inputRef = useRef();

  const focusInput = () => {
    inputRef.current.focus();
  };

  return (
    <>
      <Child ref={inputRef} />
      <button onClick={focusInput}>Focus</button>
    </>
  );
}
```

**How It Works Internally**

* `forwardRef` wraps the child component.
* React **automatically forwards** the parent’s `ref` → child’s `ref`.
* `ref.current` now points to the child’s **DOM node**.

## **3. useImperativeHandle** 🔧

`useImperativeHandle` lets you **customize** what is exposed via a **ref**.
It’s used **inside forwardRef components**.

### **Example: Exposing Custom Methods**

```jsx
import { forwardRef, useRef, useImperativeHandle } from "react";

const Child = forwardRef((props, ref) => {
  const inputRef = useRef();

  useImperativeHandle(ref, () => ({
    focus: () => inputRef.current.focus(),
    clear: () => (inputRef.current.value = "")
  }));

  return <input ref={inputRef} type="text" />;
});

export default function Parent() {
  const childRef = useRef();

  return (
    <>
      <Child ref={childRef} />
      <button onClick={() => childRef.current.focus()}>Focus</button>
      <button onClick={() => childRef.current.clear()}>Clear</button>
    </>
  );
}
```

## **5. Key Differences Between useRef, forwardRef & useImperativeHandle**

| Feature                | **useRef**                          | **forwardRef**                  | **useImperativeHandle**          |
| ---------------------- | ----------------------------------- | ------------------------------- | -------------------------------- |
| **Purpose**            | Creates a mutable ref               | Passes ref from parent to child | Customizes ref exposed to parent |
| **Scope**              | Local to component                  | Connects parent & child         | Inside `forwardRef`              |
| **Triggers Re-render** | ❌ No                                | ❌ No                            | ❌ No                             |
| **Works On**           | DOM elements & values               | Custom components               | Ref object returned to parent    |
| **Use Case**           | Focus, scroll, store mutable values | Access child's DOM              | Expose custom functions & props  |

---

## **6. Real-Life Example — Modal Control**

### **Child Component (Modal.js)**

```jsx
import { forwardRef, useImperativeHandle, useState } from "react";

const Modal = forwardRef((props, ref) => {
  const [isOpen, setIsOpen] = useState(false);

  useImperativeHandle(ref, () => ({
    open: () => setIsOpen(true),
    close: () => setIsOpen(false)
  }));

  return (
    isOpen && (
      <div style={{ border: "1px solid black", padding: "10px" }}>
        <p>This is a modal!</p>
        <button onClick={() => setIsOpen(false)}>Close</button>
      </div>
    )
  );
});

export default Modal;
```

### **Parent Component**

```jsx
import { useRef } from "react";
import Modal from "./Modal";

export default function App() {
  const modalRef = useRef();

  return (
    <>
      <button onClick={() => modalRef.current.open()}>Open Modal</button>
      <Modal ref={modalRef} />
    </>
  );
}
```

✅ Now, the parent **controls** the modal using **custom methods** exposed via **useImperativeHandle**.

### **7. Best Practices**

* Prefer **controlled components** instead of refs wherever possible.
* Use `useRef` for **DOM manipulation** and **persistent values**.
* Use `forwardRef` only when you **must** expose refs to parents.
* Use `useImperativeHandle` **sparingly** — only when custom methods are needed.

### **8. Data Flow Diagram**

```
Parent Component
   |
   | ---- ref ----> forwardRef ----> Child Component
   |                                |
   |                                └─ DOM node or custom methods (via useImperativeHandle)
   |
   <-- ref.current <-- Child's exposed API
```

<br>
<br>

# useState: workings, advanced patterns, constant value changes 🧠

### **What it does**

`useState` is a type of React Hook that helps to manage the state in functional components.

```jsx
const [state, setState] = useState(initialValue);
```

### **How It Works Internally**

* When you call `useState(initialValue)`, React **registers a state slot** for that component.
* On **initial render**, `state = initialValue`.
* When you call `setState(newValue)`, React:

  1. Stores the **new value** in the state slot.
  2. **Schedules a re-render** of the component.
* On the next render, React returns the **latest state**.

```
React internally maintains a linked list of hooks for each component. During every render, hooks are called sequentially in the same order.

If the order changes — for example, calling a hook inside a condition — React mismatches the state and throws errors. That’s why hooks must always be called unconditionally and in the same sequence.”
```

###  **Lazy Initialization** ⚡

If computing the initial state is **expensive**, pass a **function** instead of a direct value:

```jsx
const [count, setCount] = useState(() => {
  console.log("Heavy computation..."); // runs only once!
  return expensiveCalculation();
});
```

✅ React will call the function **only on the first render**, **not on every re-render**.

### **Functional Updates** ✅

When your **new state** depends on the **previous state**, use the **functional form**:

```jsx
setCount(prevCount => prevCount + 1);
```

Why?
Because `setState` is **asynchronous** — multiple updates **batch together**.

Example:

```jsx
setCount(count + 1); // ❌ Might be wrong
setCount(count + 1); // ❌ Might not increment properly

setCount(prev => prev + 1); // ✅ Correct
setCount(prev => prev + 1); // ✅ Works reliably
```

### **Avoiding Constant Re-Creation of State Values**

### ❌ Problematic Code:

```jsx
const [filters, setFilters] = useState({ sort: "asc", search: "" });
```

This recreates the object **on every render** if used wrongly with `useEffect`.

### ✅ Solution — use **lazy init** + **memoization**:

```jsx
const initialFilters = useMemo(() => ({ sort: "asc", search: "" }), []);
const [filters, setFilters] = useState(initialFilters);
```

### **Advanced Patterns**

### **a) State Reducer Pattern** (Alternative to `useReducer`)

When you have **complex state transitions**:

```jsx
const [state, setState] = useState({ count: 0, theme: "light" });

const increment = () => setState(prev => ({ ...prev, count: prev.count + 1 }));
const toggleTheme = () => setState(prev => ({ ...prev, theme: prev.theme === "light" ? "dark" : "light" }));
```


### **b) Derived State Optimization**

Instead of storing derived values in state, **compute them on the fly**.

```jsx
const [items] = useState([1, 2, 3, 4, 5]);
const evenItems = useMemo(() => items.filter(i => i % 2 === 0), [items]);
```

### **c) Use State Updater for Performance**

If you need to update the state **many times** in a single render cycle:

```jsx
for (let i = 0; i < 5; i++) {
  setCount(prev => prev + 1); // ✅ Runs only 1 re-render
}
```

## **1.7 Common Pitfalls**

| ❌ **Mistake**                                             | ✅ **Solution**                       |
| --------------------------------------------------------- | ------------------------------------ |
| Calling `setState` inside render                          | Move to `useEffect` or event handler |
| Using `setState(value)` without prev state when dependent | Always use functional updates        |
| Initializing state inline for expensive operations        | Use lazy initialization              |

---
<br>
<br>

# UseEffect: workings, advanced patterns 🔄

### **What it does**

`useEffect` is a type of hook which is used to perform  **side effects** in React functional components.

```jsx
useEffect(() => {
  // Side effect: API call, event listener, subscription
  return () => {
    // Cleanup: remove listener, cancel subscription
  };
}, [dependencies]);
```

### **How It Works Internally**

* Runs **after render**, **not during**.
* React compares **dependencies**:

  * If **none provided** → Runs **after every render**.
  * If `[]` → Runs **once** (on mount/unmount).
  * If `[deps]` → Runs **only when deps change**.

### **Types of Effects**

### **a) Mount Only (`[]`)**

```jsx
useEffect(() => {
  console.log("Mounted");
  return () => console.log("Unmounted");
}, []);
```

### **b) Dependent on State/Props**

```jsx
useEffect(() => {
  console.log("Count changed:", count);
}, [count]);
```

### **c) No Dependencies — Runs Every Render**

```jsx
useEffect(() => {
  console.log("Runs on every render");
});
```

### **Cleanup Functions**

Whenever you set up **listeners, timers, subscriptions**, always clean them up.

```jsx
useEffect(() => {
  const id = setInterval(() => console.log("Running..."), 1000);
  return () => clearInterval(id);
}, []);
```

## **Advanced Patterns**

### **a) Debouncing with `useEffect`**

```jsx
const [search, setSearch] = useState("");

useEffect(() => {
  const handler = setTimeout(() => {
    console.log("Searching:", search);
  }, 500);
  return () => clearTimeout(handler);
}, [search]);
```

### **b) Fetching Data**

```jsx
useEffect(() => {
  let isMounted = true;

  fetch("/api/data")
    .then(res => res.json())
    .then(data => isMounted && setData(data));

  return () => { isMounted = false };
}, []);
```

### **c) Syncing State with Local Storage**

```jsx
const [theme, setTheme] = useState(() => localStorage.getItem("theme") || "light");

useEffect(() => {
  localStorage.setItem("theme", theme);
}, [theme]);
```

## **2.6 Common Pitfalls**

| ❌ **Mistake**                              | ✅ **Solution**                   |
| ------------------------------------------ | -------------------------------- |
| Infinite loops due to missing dependencies | Always add required deps in `[]` |
| Using `async` directly in `useEffect`      | Wrap async logic in a function   |
| Forgetting cleanup                         | Always return cleanup function   |

---
<br>
<br>

# **`useState` + `useEffect` Together** ⚡

Example: **Auto-Save Form**

```jsx
const [formData, setFormData] = useState("");

useEffect(() => {
  const handler = setTimeout(() => {
    console.log("Auto-saving:", formData);
  }, 800);
  return () => clearTimeout(handler);
}, [formData]);
```

### **Performance Tips**

| **Problem**                      | **Solution**                                 |
| -------------------------------- | -------------------------------------------- |
| Expensive state init             | Use lazy `useState(() => ...)`               |
| Re-renders due to objects/arrays | Use `useMemo` or `useCallback`               |
| Multiple state updates           | Use functional updates to batch changes      |
| Heavy effects                    | Optimize dependency arrays, cleanup properly |

---
<br>
<br>

# Why keys are required in lists? Why not use index as key?

### **1. Why Keys Are Required in React Lists** 🔑

In React, when you render a **list of components** using `.map()`, React needs a way to **uniquely identify each item** between **re-renders**.

Example:

```jsx
const items = ["Apple", "Banana", "Mango"];

return (
  <ul>
    {items.map(item => <li>{item}</li>)}
  </ul>
);
```

⚠️ React will show a **warning**:

> "Each child in a list should have a unique "key" prop."

### **Reason:**

React uses a **diffing algorithm** called **Reconciliation** to decide:

* Which items are **new** ✅
* Which items are **updated** 🔄
* Which items are **removed** ❌

If you **don’t provide keys**, React assumes **everything has changed** and **re-renders the entire list** → **performance loss**.

### **2. How React Uses Keys Internally** ⚙️

React’s **Virtual DOM** compares **previous** and **next** render trees.

Imagine this list:

```jsx
const items = ["A", "B", "C"];
```

### **First Render**

Virtual DOM assigns:

```
A → position 0
B → position 1
C → position 2
```

---

### **If You Add "X" at the Top**

```jsx
const items = ["X", "A", "B", "C"];
```

If **unique keys** are provided:

```
Before: A,B,C
After:  X,A,B,C
React compares keys:
 ✅ X → New → Add at top
 ✅ A,B,C → Same keys → Reuse existing components
```

🚀 Efficient update — **only "X" is mounted**.


## **what If You Use Index as Key**

```jsx
{items.map((item, index) => <li key={index}>{item}</li>)}
```

Then React compares **positions** instead of **actual items**:

**Before:**

```
0:A, 1:B, 2:C
```

**After adding "X" at top:**

```
0:X, 1:A, 2:B, 3:C
```

React thinks:

* Position 0 changed → Replace **A** with **X**
* Position 1 changed → Replace **B** with **A**
* Position 2 changed → Replace **C** with **B**
* Add **C** at position 3

😱 **Unnecessary re-renders** → Performance hit + Wrong UI behavior.


## **Why Using Index as Key Is Risky** ⚠️

### **Scenario 1 — Inserting Items**

```jsx
const [items, setItems] = useState(["Apple", "Banana", "Mango"]);
```

If you **insert** "Orange" at index `1`:

* **Index keys** → React thinks everything **after index 1** changed → unnecessary re-renders.
* **Unique keys** → React **reuses unaffected items** → efficient.

---

### **Scenario 2 — Deleting Items**

If you **delete** "Banana":

* **Index keys** → React mismatches "Mango" → wrong UI or animations break.
* **Unique keys** → React knows "Banana" is gone → safely removes it.

---

### **Scenario 3 — Input Fields (Big Problem!)**

```jsx
function App() {
  const [items, setItems] = useState(["A", "B", "C"]);

  return (
    <ul>
      {items.map((item, index) => (
        <li key={index}>
          <input defaultValue={item} />
        </li>
      ))}
      <button onClick={() => setItems(["X", ...items])}>Add Top</button>
    </ul>
  );
}
```

**Issue with index keys** 😬:

* You type in "B" → insert "X" → inputs shift → your text **jumps to wrong inputs**.
* With **unique keys**, inputs stay **mapped to correct items**.


### **When Using Index as Key Is Acceptable ✅**

Use index **only when**:

* List **never changes** (static list)
* No item reordering, insertion, or deletion
* No dynamic inputs or animations

Example:

```jsx
const days = ["Mon", "Tue", "Wed"];
{days.map((day, i) => <li key={i}>{day}</li>);}
```

⚡ Safe because days **never change**.
<br>
<br>

# Class component lifecycle methods and their order

### **1. <u>React Class Component Lifecycle Overview**</u> ⚡

React class components have **three main lifecycle phases**:

| **Phase**          | **Purpose**                                                   | **Key Methods**                                                                                                    |
| ------------------ | ------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------ |
| **Mounting**       | When a component is **created** and **inserted** into the DOM | `constructor` → `getDerivedStateFromProps` → `render` → `componentDidMount`                                        |
| **Updating**       | When props or state **change** and component **re-renders**   | `getDerivedStateFromProps` → `shouldComponentUpdate` → `render` → `getSnapshotBeforeUpdate` → `componentDidUpdate` |
| **Unmounting**     | When component is **removed** from the DOM                    | `componentWillUnmount`                                                                                             |
| **Error Handling** | When any child component **throws an error**                  | `getDerivedStateFromError` → `componentDidCatch`                                                                   |

### **2. <u>Mounting Phase (Initial Render)**</u> 🟢

When a class component is first **created** and **added to the DOM**.

**Order of Execution:**

```
constructor → getDerivedStateFromProps → render → componentDidMount
```

### **2.1 constructor(props)**

* Called **first** when the component is created.
* Used to:

  * Initialize **state**
  * Bind **methods**
  * Avoid side-effects (don’t fetch data here!)

```jsx
constructor(props) {
  super(props);
  this.state = { count: 0 };
}
```

### **2.2 static getDerivedStateFromProps(props, state)**

* **Rarely used**.
* Called **before every render** (both mount + update).
* Sync **state** with **props** if needed.
* **Static method → no access to `this`.**

```jsx
static getDerivedStateFromProps(props, state) {
  if (props.defaultCount !== state.count) {
    return { count: props.defaultCount };
  }
  return null; // No state change
}
```


### **2.3 render()**

* **Pure function** — returns JSX.
* **No side-effects** here.
* Runs **every time** props or state change.

```jsx
render() {
  return <h1>Count: {this.state.count}</h1>;
}
```

### **2.4 componentDidMount()**

* Runs **once** after the first render. [**perform side effect in class based component**]
* Best place for:

  * Fetching API data
  * Subscribing to events
  * Setting timers

```jsx
componentDidMount() {
  fetch('/api/users')
    .then(res => res.json())
    .then(data => this.setState({ users: data }));
}
```

### **3. <u>Updating Phase (Re-render)**</u> 🟡

Triggered when:

* **Props change**
* **State changes**
* **Parent re-renders**

**Order of Execution:**

```
getDerivedStateFromProps → shouldComponentUpdate → render → getSnapshotBeforeUpdate → componentDidUpdate
```

### **3.1 static getDerivedStateFromProps(props, state)**

* Called **before every render** — same as mounting.
* Used for **state sync**.


### **3.2 shouldComponentUpdate(nextProps, nextState)**

* **Performance optimization method**.
* Default: returns **true** → always re-renders.
* Return **false** to prevent unnecessary re-renders.

```jsx
shouldComponentUpdate(nextProps, nextState) {
  return nextState.count !== this.state.count;
}
```

### **3.3 render()**

* Renders the updated JSX again.


### **3.4 getSnapshotBeforeUpdate(prevProps, prevState)**

* Called **right before React updates the DOM**.
* Use when you need to **capture something** from the **current DOM** before it changes.

**Example: Auto-scroll chat window**

```jsx
getSnapshotBeforeUpdate(prevProps, prevState) {
  if (prevProps.messages.length < this.props.messages.length) {
    return this.chatBox.scrollHeight;
  }
  return null;
}
componentDidUpdate(prevProps, prevState, snapshot) {
  if (snapshot !== null) {
    this.chatBox.scrollTop = snapshot;
  }
}
```

### **3.5 componentDidUpdate(prevProps, prevState, snapshot)**

* Runs **after DOM is updated**.
* Best place for:

  * Network requests based on **state changes**
  * DOM manipulations
  * Comparing previous vs current props

```jsx
componentDidUpdate(prevProps, prevState) {
  if (prevState.count !== this.state.count) {
    console.log("Count updated!");
  }
}
```

### **4. <u>Unmounting Phase**</u> 🔴

**Method:** `componentWillUnmount()`

* Called **just before** the component is removed from the DOM.
* Best place for **cleanups**:

  * Removing event listeners
  * Clearing timers
  * Canceling API requests

```jsx
componentWillUnmount() {
  clearInterval(this.timer);
}
```

### **5. <u>Error Handling Phase**</u> ⚠️

React introduced **Error Boundaries** in React 16.

### **Methods:**

#### **getDerivedStateFromError(error)**

* Update state to show a **fallback UI**.

#### **componentDidCatch(error, info)**

* Log error details.

```jsx
class ErrorBoundary extends React.Component {
  state = { hasError: false };

  static getDerivedStateFromError(error) {
    return { hasError: true };
  }

  componentDidCatch(error, info) {
    console.log("Error:", error, info);
  }

  render() {
    if (this.state.hasError) return <h1>Something went wrong</h1>;
    return this.props.children;
  }
}
```

### **6. <u>Class Lifecycle vs Functional Hooks**</u> 🔄

| **Lifecycle Method**     | **Functional Hook Equivalent**           |
| ------------------------ | ---------------------------------------- |
| constructor              | `useState`, `useRef`                     |
| getDerivedStateFromProps | Rarely needed, manage inside `useEffect` |
| render                   | Function body itself                     |
| componentDidMount        | `useEffect(() => {...}, [])`             |
| shouldComponentUpdate    | `React.memo` / `useMemo` / `useCallback` |
| getSnapshotBeforeUpdate  | `useLayoutEffect`                        |
| componentDidUpdate       | `useEffect(() => {...}, [deps])`         |
| componentWillUnmount     | Cleanup inside `useEffect`               |
| componentDidCatch        | `ErrorBoundary` (still class only)       |

---

### **7. <u>Full Lifecycle Execution Order**</u> 🔄

### **On Mount**

```
constructor →
getDerivedStateFromProps →
render →
componentDidMount
```

### **On Update**

```
getDerivedStateFromProps →
shouldComponentUpdate →
render →
getSnapshotBeforeUpdate →
componentDidUpdate
```

### **On Unmount**

```
componentWillUnmount
```

---

### **8. <u>Visual Diagram**</u> 🧩

```
            ┌──────────────────────┐
            │      MOUNTING        │
            ├──────────────────────┤
            │ constructor          │
            │ getDerivedState...   │
            │ render               │
            │ componentDidMount    │
            └──────────────────────┘
                     ↓
            ┌──────────────────────┐
            │      UPDATING        │
            ├──────────────────────┤
            │ getDerivedState...   │
            │ shouldComponentUpdate│
            │ render               │
            │ getSnapshotBeforeUpd │
            │ componentDidUpdate   │
            └──────────────────────┘
                     ↓
            ┌──────────────────────┐
            │     UNMOUNTING       │
            ├──────────────────────┤
            │ componentWillUnmount │
            └──────────────────────┘
```

### **9. <u>Key Takeaways**</u>

* **Mounting** → `constructor → render → componentDidMount`
* **Updating** → `getDerivedStateFromProps → shouldComponentUpdate → render → getSnapshotBeforeUpdate → componentDidUpdate`
* **Unmounting** → `componentWillUnmount`
* Prefer **functional components + hooks** in modern React.
* Use **error boundaries** for error handling.
<br>
<br>

# What exactly happens when state/props change?

### **React’s Render Cycle on State/Props Change**

Whenever you call `setState` (in class) or `setSomething` (in functional components), or when a parent passes new **props**, React goes through **five major phases**:


### **Step 1 — <u>State/Props Change → Re-render Triggered**</u>

* When you call `setState` or `setCount`, **React marks the component as dirty**.
* For props, when the parent re-renders and passes **new props**, the child component **may also re-render** (depending on memoization).

```jsx
const [count, setCount] = useState(0);
setCount(count + 1); // Marks component as dirty → triggers reconciliation
```

### **Step 2 — <u>Component Re-render (Virtual DOM Generation)**</u>

* React calls the **component function** again (functional components) or **`render()` method** (class components).
* New **React elements** are created using `React.createElement`.
* These elements form a **new Virtual DOM tree** for this component.

### **Step 3 — <u>Virtual DOM Diffing (Reconciliation Phase)**</u>

React compares:

* **Previous Virtual DOM tree** vs **New Virtual DOM tree**
* Uses a **diffing algorithm** (O(n)) with:

  * **Type comparison** → If element type changes, React destroys the old one & creates a new one.
  * **Props comparison** → If props change, React updates them.
  * **Children comparison** → Uses **keys** to track child positions.

> ⚡ **Why keys matter**: Without keys, React may unnecessarily **re-create DOM nodes**, hurting performance.

### **Step 4 — <u>Fiber Reconciliation & Scheduling**</u>

React uses the **Fiber architecture** to:

* Break work into **small units** → avoids blocking the main thread.
* Schedule updates based on **priority**:

  * High priority: User input, animations.
  * Low priority: Background rendering, lazy loading.
* React batches state updates within the same event loop tick for better performance.

### **Step 5 — <u>Commit Phase (Real DOM Update)**</u>

After reconciliation, React:

* Finds the **minimum set of changes** required.
* Updates the **real DOM** efficiently.
* Runs **side effects** (`useEffect`, `componentDidMount`, etc.).



## **2. State Change vs Props Change**

| Aspect                  | **State Change**       | **Props Change**                                   |
| ----------------------- | ---------------------- | -------------------------------------------------- |
| **Who controls it?**    | Internal to component  | Passed by parent                                   |
| **Triggers re-render?** | ✅ Yes                  | ✅ Yes (if value changes)                           |
| **Can skip re-render?** | ❌ No (unless memoized) | ✅ Yes with `React.memo` or `shouldComponentUpdate` |
---

## **3. How React Optimizes Rendering**

React **does not always re-render everything**. It uses multiple techniques:

### **a) Batching Updates**

```jsx
setCount(c => c + 1);
setCount(c => c + 1);
// React batches these → Single re-render instead of two
```

### **b) Memoization (`React.memo`)**

```jsx
const Child = React.memo(({ value }) => {
  console.log("Child rendered");
  return <p>{value}</p>;
});
```

### **c) Skipping Expensive Re-renders**

Using `useMemo` & `useCallback` to memoize computations and functions.


## **4. Lifecycle Hooks During State/Props Change**

### **Functional Components**

| Phase          | Hook                                 | When it Runs                 |
| -------------- | ------------------------------------ | ---------------------------- |
| Before Paint   | **Nothing** (React schedules render) | Before DOM update            |
| After Render   | `useEffect(() => {}, [])`            | Runs after first render      |
| After Update   | `useEffect(() => {}, [deps])`        | Runs when state/props change |
| Before Unmount | Cleanup in `useEffect`               | Before DOM removal           |

### **Class Components**

| Phase            | Lifecycle Method                    |
| ---------------- | ----------------------------------- |
| Before Render    | `static getDerivedStateFromProps()` |
| During Render    | `render()`                          |
| After DOM Update | `componentDidUpdate()`              |
| Before Unmount   | `componentWillUnmount()`            |
---

## **5. Key Takeaways**

* **State/props change → React marks component dirty → Re-renders Virtual DOM → Diffs with old tree → Updates real DOM efficiently.**
* React **doesn’t refresh the entire page**, it only updates **necessary parts**.
* Keys are crucial for efficient **list reconciliation**.
* Batching & memoization help avoid **unnecessary re-renders**.
<br>
<br>

# [What is React Fiber? How does reconciliation work ?](https://sunnychopper.medium.com/what-is-react-fiber-and-how-it-helps-you-build-a-high-performing-react-applications-57bceb706ff3)

### **1. <u>What is React Fiber</u> ?**

React Fiber is the **new reconciler algorithm** introduced in **React 16** to improve rendering performance and make React **concurrent**.

### **Before React Fiber (React 15 and earlier)**

* React used **stack-based reconciliation**.
* Rendering was **synchronous** → once React started updating, **it couldn’t pause** until finished.
* Bad for performance in **large trees** → UI froze when heavy updates occurred.

### **After React Fiber**

* Fiber introduced an **asynchronous, interruptible, incremental rendering** mechanism.
* React can now **pause, reuse, and resume work**.
* Allows **prioritization** → user interactions (high priority) are processed before less critical updates.


### **2. <u>Fiber: The Core Data Structure</u>**

A **Fiber** is just a **plain JavaScript object** representing a React element **in the Virtual DOM**.

Each Fiber node contains:

* **Type** → What component is this? (`div`, `Button`, `MyComponent`)
* **Key** → Used for list reconciliation
* **Props** → New props to apply
* **State** → Component’s internal state
* **Effect tags** → What needs to be done? (insert, update, delete)
* **Child / Sibling / Return pointers** → For traversing the tree efficiently

This makes the Fiber tree **linked** and **traversable**, unlike the old flat Virtual DOM.

### **3. <u>React Rendering Phases with Fiber</u>**

React’s rendering now happens in **two phases**:

### **A) Render Phase** 🧠 *(Reconciliation Phase)*

* Goal: Build a **work-in-progress Fiber tree**.
* React compares the **old Fiber tree** (previous render) with the **new one** (next render).
* Uses the **diffing algorithm** to decide:

  * Which nodes to **update**
  * Which nodes to **create**
  * Which nodes to **remove**
* **Interruptible** → React can pause here for high-priority updates.

### **B) Commit Phase** ⚡ *(DOM Update Phase)*
* Applies all changes to the **real DOM**.
* Runs **layout effects** (`useLayoutEffect`, `componentDidMount`).
* This phase is **synchronous** and **non-interruptible**.


### **4. <u>How Reconciliation Works Internally</u>**

React’s **reconciliation algorithm** decides **how to efficiently update the UI**.

### **Step 1 — Comparing Root Elements**

```jsx
<Header /> → <Header />
```

* If the **type** matches → React **reuses** the Fiber and updates props.
* If the **type** changes → React **destroys** the old Fiber and **creates a new one**.

### **Step 2 — Diffing Children**

React uses **keys** to efficiently compare children:

```jsx
<ul>
  <li key="1">A</li>
  <li key="2">B</li>
</ul>
```

If we change it to:

```jsx
<ul>
  <li key="2">B</li>
  <li key="1">A</li>
</ul>
```

* React **doesn’t recreate both nodes**.
* It matches keys and just **moves them in the DOM**.
* **If keys are missing** → React falls back to **index-based diffing** → less efficient.


### **Step 3 — Scheduling Priorities**

React Fiber assigns **priority levels**:

* **Immediate** → User input, animations.
* **High** → Visible content changes.
* **Low** → Background data fetching.
* **Idle** → Preloading, non-blocking work.

This lets React **pause low-priority work** and **focus on urgent tasks** → **Concurrent Mode**.

### **5. <u>React Fiber in Action</u>**

Let's simulate:

```jsx
function App() {
  const [count, setCount] = useState(0);
  return (
    <div>
      <Header title="My App" />
      <button onClick={() => setCount(count + 1)}>{count}</button>
    </div>
  );
}
```

### **Step-by-step**:

1. **setCount** → Marks component as “dirty”.
2. React **creates a new Fiber tree** for `App`.
3. Compares new tree with old tree → **diffing**.
4. Marks `<button>` for **text update**.
5. Commit Phase → React **updates DOM text** only.
6. UI updates **without repainting the entire page**.

---

# **6. Fiber vs Old Reconciliation**

| Feature               | **Old React (Stack)** | **React Fiber**           |
| --------------------- | --------------------- | ------------------------- |
| Rendering             | Synchronous           | Asynchronous              |
| Can pause rendering?  | ❌ No                  | ✅ Yes                     |
| Prioritization        | ❌ No                  | ✅ Yes                     |
| Incremental rendering | ❌ No                  | ✅ Yes                     |
| Core goal             | Correctness           | Performance + Flexibility |

---
<br>
<br>

# Shallow vs Deep rendering

## What is Shallow Rendering? 🧩
Shallow rendering means rendering **only the current component** and **not its child components**.
It creates a lightweight, isolated render of a component.

This is commonly used in **unit testing** to test a single component in isolation.

### **Example**

```jsx
// Button.jsx
export default function Button({ label }) {
  return <button>{label}</button>;
}

// App.jsx
import Button from "./Button";
export default function App() {
  return <div><Button label="Click Me" /></div>;
}
```

#### **Shallow Render App**

```js
import { shallow } from "enzyme";
import App from "./App";

const wrapper = shallow(<App />);
console.log(wrapper.debug());
```

**Output:**

```html
<div>
  <Button label="Click Me" />
</div>
```

🔹 **Child components are NOT rendered** → only `<Button />` reference is shown.

## **2. What is Deep Rendering?** 🌊

Deep rendering (also called **full rendering**) means rendering the component **along with all of its children**, recursively, until the **entire component tree** is rendered.

This is useful for **integration testing** when you want to test how multiple components work together.

### **Example**

Using the same **App.jsx**:

```js
import { mount } from "enzyme";
import App from "./App";

const wrapper = mount(<App />);
console.log(wrapper.debug());
```

**Output:**

```html
<div>
  <button>Click Me</button>
</div>
```

🔹 Here, `<Button />` is **fully rendered** into its real DOM output.


## **3. Shallow vs Deep Rendering — Key Differences**

| **Feature**          | **Shallow Rendering** 🟢  | **Deep Rendering** 🔵   |
| -------------------- | ------------------------- | ----------------------- |
| **Component depth**  | Renders **only 1 level**  | Renders **entire tree** |
| **Child components** | Skipped                   | Fully rendered          |
| **Speed**            | **Fast**                  | Slower                  |
| **Testing style**    | **Unit tests**            | **Integration tests**   |
| **Debugging**        | Easier                    | Harder                  |
| **Tooling**          | `shallow()` (Enzyme)      | `mount()` / `render()`  |
| **DOM creation**     | Does **not** mount in DOM | Mounts into real DOM    |

---

## **4. When to Use Shallow vs Deep Rendering**

### ✅ **Use Shallow Rendering When:**

* You’re writing **unit tests**.
* You want to **isolate a single component**.
* You don’t care about child components’ behavior.
* You need **faster tests**.

### ✅ **Use Deep Rendering When:**

* You’re writing **integration tests**.
* You want to test **parent-child interaction**.
* You rely on **hooks, context, portals, or refs** that need the **real DOM**.
* You want **full user interaction testing**.
<br>
<br>

# [React Rendering: when, why, and how components render](https://www.joshwcomeau.com/react/why-react-re-renders/)

Every re-render in React starts with a state change. It's the only “trigger” in React for a component to re-render.

Re-renders only affect the component that owns the state + its descendants (if any). 

React's “main job” is to keep the application UI in sync with the React state. The point of a re-render is to figure out **what needs to change**.

```
🎯 By default, when a component’s state changes, all its descendants re-render, regardless of whether we pass that state via props or Context.
Context doesn’t optimize rendering; it only removes prop drilling.
To prevent unnecessary re-renders, we need optimizations like React.memo, useMemo, useCallback, or context selectors (use-context-selector library -> It allows components to subscribe only to specific parts of the context, preventing unnecessary re-renders).
```

### **1. <u>Why Context Forces Re-rendering</u>** 🔄

React Context works like a **subscription**:

* Any component that **uses** the context (`useContext` or `Context.Consumer`) **subscribes** to it.
* **Whenever the context value changes**, React **forces all subscribed components** to re-render.
* `React.memo` **cannot prevent this** because React sees the context value as **internal state** — it's **not a prop** you control.

### **Example: `React.memo` is Ignored**

```jsx
const ThemeContext = React.createContext();

const Child = React.memo(() => {
  const theme = React.useContext(ThemeContext);
  console.log("Child re-rendered");
  return <div style={{ color: theme }}>Child</div>;
});

export default function Parent() {
  const [color, setColor] = useState("red");

  return (
    <ThemeContext.Provider value={color}>
      <button onClick={() => setColor(color === "red" ? "blue" : "red")}>
        Change Theme
      </button>
      <Child />
    </ThemeContext.Provider>
  );
}
```

### **Behavior**

* Change the theme color → `Parent` re-renders ✅
* `Child` re-renders ❌ **even though wrapped in `React.memo`**.

---

### **2. <u>Why `React.memo` Doesn't Work Here</u>**

`React.memo` only skips re-renders when **props** are the same.
But `useContext` is **not a prop** → it's a **direct dependency**.

* If the **context value** changes → React **must re-render** components using it.
* No amount of memoization can stop it.


### **3. <u>How to Optimize Context-related Re-renders</u>** 🚀

### **A. Memoize Context Value in the Provider**

If your `Provider` value is an **object or function**, React treats it as **new** on every render:

```jsx
<ThemeContext.Provider value={{ color }}> // ❌ Bad
```

Fix it with `useMemo`:

```jsx
const theme = useMemo(() => ({ color }), [color]);
<ThemeContext.Provider value={theme}> // ✅ Good
```

This reduces **unnecessary renders**, but **children will still re-render if the actual value changes**.

### **B. Split Contexts**

If your context holds **multiple values**, **all consumers re-render** when **any value changes**.

```jsx
const AppContext = createContext({ theme: "dark", language: "en" });
```

If a component **only cares about `theme`**, but `language` changes → **it still re-renders** ❌.

**Fix:** Create **separate contexts**:

```jsx
const ThemeContext = createContext();
const LanguageContext = createContext();
```

Now, components consuming only `ThemeContext` won’t re-render when `language` updates ✅.

---

### **C. Use `use-context-selector` Library (Best Fix)** 🏆

React’s built-in Context **doesn’t support partial subscriptions** yet, but [**use-context-selector**](https://github.com/dai-shi/use-context-selector) does:

```jsx
import { createContext, useContextSelector } from "use-context-selector";

const ThemeContext = createContext();

const Child = React.memo(() => {
  const theme = useContextSelector(ThemeContext, v => v.theme);
  console.log("Child re-rendered");
  return <div style={{ color: theme }}>Child</div>;
});

function Parent() {
  const [theme, setTheme] = useState("red");
  const [lang, setLang] = useState("en");

  return (
    <ThemeContext.Provider value={{ theme, lang }}>
      <Child />
    </ThemeContext.Provider>
  );
}
```

✅ Now `Child` **only re-renders when `theme` changes**, **not when `lang` changes**.


### **D. Move Context Consumers Lower in the Tree**

If possible, don’t make a deeply nested component subscribe to a **high-level provider** unnecessarily.
Instead, wrap only the **needed subtree** with the `Provider`.

---

## **4. Summary Table**

| **Case**                         | **Context Value Changes** | **Child Uses `useContext`** | **Memoized?** | **Re-render?**              |
| -------------------------------- | ------------------------- | --------------------------- | ------------- | --------------------------- |
| Props only                       | ❌ No                      | ❌ No                        | ✅ Yes         | ❌ No                        |
| Context value changes            | ✅ Yes                     | ✅ Yes                       | ❌ No          | ✅ Yes                       |
| Context value stable (`useMemo`) | ❌ No                      | ✅ Yes                       | ✅ Yes         | ❌ No                        |
| Multiple values in one context   | ✅ Any value               | ✅ All consumers             | ❌ No          | ✅ All re-render             |
| Split contexts per value         | ✅ Specific value          | ✅ Specific consumers        | ✅ Yes         | ❌ Unrelated components skip |

---

## **5. Interview Tip**

**Q:** “If a child uses `useContext`, will `React.memo` prevent its re-render when the context value changes?”
**A:** ❌ **No.**

* Context subscription bypasses props comparison.
* To optimize, **memoize provider values**, **split contexts**, or use **`use-context-selector`**.

<br>
<br>

# React.memo & PureComponent

### **1. React.memo vs PureComponent — At a Glance**

| Feature               | **React.memo** 🟢                                | **PureComponent** 🔵                                     |
| --------------------- | ------------------------------------------------ | -------------------------------------------------------- |
| **Used with**         | Functional components                            | Class components                                         |
| **How it works**      | Skips re-render if **props** are shallowly equal | Skips re-render if **props + state** are shallowly equal |
| **Comparison Type**   | Shallow                                          | Shallow                                                  |
| **Re-render Trigger** | If props **change** → re-render                  | If props **or state** change → re-render                 |
| **Performance**       | Faster for functional components                 | Useful only in class components                          |
| **Custom Comparison** | ✅ Yes → `React.memo(Component, areEqual)`        | ❌ No built-in support                                    |
| **Introduced In**     | React 16.6+                                      | React 16 (before Hooks)                                  |

---

### **2. How `React.memo` Works**

### **Basic Example**

```jsx
const Child = React.memo(({ name }) => {
  console.log("Child rendered");
  return <div>Hello {name}</div>;
});

export default function Parent() {
  const [count, setCount] = useState(0);

  return (
    <>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <Child name="Divas" />
    </>
  );
}
```

### **Behavior**

* Clicking **Increment** re-renders `Parent`
* `Child` **does NOT re-render** ✅ because props didn’t change

---

### **Custom Comparison Function**

```jsx
const Child = React.memo(
  ({ user }) => {
    console.log("Child rendered");
    return <div>{user.name}</div>;
  },
  (prevProps, nextProps) => prevProps.user.id === nextProps.user.id
);
```

* By default, `React.memo` does **shallow comparison**.
* If your props are **nested objects**, you need a custom comparison.


### **3. How `PureComponent` Works**

`PureComponent` is the **class component equivalent** of `React.memo`.

```jsx
class Child extends React.PureComponent {
  render() {
    console.log("Child rendered");
    return <div>Hello {this.props.name}</div>;
  }
}

export default function Parent() {
  const [count, setCount] = useState(0);
  return (
    <>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <Child name="Divas" />
    </>
  );
}
```

* Works the same way:
  If props don’t change → `Child` won’t re-render ✅.
* Also compares **state** automatically.

---

## **4. Shallow Comparison Behavior**

Both **React.memo** and **PureComponent** do **shallow equality checks**.

### **Example of Shallow Comparison**

```jsx
const Child = React.memo(({ obj }) => {
  console.log("Child rendered");
  return <div>{obj.name}</div>;
});

export default function Parent() {
  const [count, setCount] = useState(0);
  const obj = { name: "Divas" };

  return (
    <>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <Child obj={obj} />
    </>
  );
}
```

**Behavior**

* Every time `Parent` renders → `obj` gets a **new reference**.
* `React.memo` sees props **changed** → `Child` re-renders ❌.

**Fix: Use `useMemo`** ✅

```jsx
const obj = useMemo(() => ({ name: "Divas" }), []);
```

### **5. Key Differences**

### **A. Functional vs Class Components**

* `React.memo` → **functional components** ✅
* `PureComponent` → **class components** ✅
* In modern React, `React.memo` is preferred since hooks are standard.

### **B. State Comparison**

* `React.memo` → **does not compare state** (only props)
* `PureComponent` → compares **both props + state**

### **C. Custom Comparison**

* `React.memo` → supports `areEqual(prevProps, nextProps)`
* `PureComponent` → ❌ no custom comparison, shallow only

### **6. When to Use React.memo / PureComponent**

✅ **Use When:**

* Component is **pure** (output depends only on props)
* Props are **primitive** (string, number, boolean)
* Component is **heavy to render**

❌ **Avoid When:**

* Component is **small & cheap to render** → memoization overhead is worse
* Props are **always changing** → `React.memo` is useless
* Passing **nested objects/arrays** without `useMemo` or `useCallback`

---

## **7. React.memo + Context Pitfall**

If a component uses **`useContext`**, `React.memo` **cannot prevent re-renders**
→ we discussed this earlier ✅.

```jsx
const Child = React.memo(() => {
  const theme = useContext(ThemeContext);
  console.log("Child rendered");
  return <div>{theme}</div>;
});
```

* **If the context value changes**, `Child` **always re-renders** ❌.
* Solution: **split contexts** or use `use-context-selector`.


### **8. Performance Tip**

| **Case**                  | **Should You Use React.memo / PureComponent?** | **Why**                            |
| ------------------------- | ---------------------------------------------- | ---------------------------------- |
| Static UI                 | ✅ Yes                                          | Prevents useless re-renders        |
| Dynamic UI                | ❌ No                                           | Props/state change anyway          |
| Expensive child rendering | ✅ Yes                                          | Big performance gains              |
| Context consumers         | ⚠️ Maybe                                       | Use `use-context-selector` instead |
| Deep objects/arrays       | ✅ With `useMemo`                               | Prevents reference changes         |


### **9. Summary**

* **React.memo** → Functional components
* **PureComponent** → Class components
* Both prevent **unnecessary re-renders** using **shallow comparison**
* For **nested props**, use `useMemo` / `useCallback`
* For **context consumers**, memoization doesn’t help unless optimized

<br>
<br>

# Virtual DOM, Diffing algorithm, and React Fiber

## **1. Virtual DOM (V-DOM)** 🌿
* The **Virtual DOM** is a **lightweight JavaScript representation** of the **Real DOM**.
* React **never directly manipulates the real DOM**; instead, it:

  1. Maintains a **virtual copy** of the UI.
  2. Compares the new virtual tree with the old one.
  3. Applies **minimal updates** to the real DOM.


### **<u>Why React Uses VDOM</u>**

* **Real DOM updates are expensive** ⚡️

  * Recalculating styles, layouts, and painting can be slow.
* **Virtual DOM is fast** ✅

  * React batches updates and applies **only what changed**.

### **<u>How It Works</u>**

**Step 1:** Initial render → React creates a VDOM tree

**Step 2:** When state/props change → React creates a new VDOM tree

**Step 3:** React compares **old vs new VDOM** → finds differences using the **Diffing Algorithm**

**Step 4:** Only the changed nodes are updated in the **real DOM**.

## **2. Diffing Algorithm** 🔍

It's part of the reconciliation process, which efficiently updates the user interface (UI) to reflect changes in state or props without re-rendering the entire page. This is achieved by comparing a new Virtual DOM tree with the previous one to identify the minimal set of changes needed for the real DOM

### **2.1 Naive Approach (O(n³))**

* Comparing **two entire DOM trees** node by node is **very slow**.
* React optimizes this using **heuristics** → O(n).

### **2.2 React’s Heuristics**

React assumes:

1. **Different element types → Replace node entirely**

   ```jsx
   <div> → <span>   // replaced, not diffed
   ```
2. **Same type → Update in place**

   ```jsx
   <div class="red"> → <div class="blue">  
   // Only updates class, not re-creates the node
   ```
3. **Keys help identify elements**

   ```jsx
   items.map(item => <li key={item.id}>{item.name}</li>)
   ```

   * Keys help React **track moved elements** efficiently.
   * Without keys, React **re-renders all children** unnecessarily.

## **3. React Fiber** 🧵 *(Introduced in React 16)*

React Fiber is the **new reconciliation engine** behind React.
Before React 16, React used **stack-based reconciliation** → **blocking & synchronous**.<br>
Fiber introduced **incremental, interruptible rendering** → **non-blocking & faster**.

### **3.1 Problems Before Fiber**

* **Old React**:
  * Reconciliation was synchronous
  * Big components blocked the main thread
  * Resulted in **janky UI** when rendering large trees.


### **3.2 Goals of React Fiber**

* **Make rendering interruptible** ✅
* **Prioritize updates** ✅
* **Split work into small chunks** ✅
* **Prepare for concurrent features** ✅ *(React 18 uses this)*

### **3.3 How Fiber Works**

React Fiber introduces **two phases**:

#### **A. Render Phase (Reconciliation)**

* Work is split into **small units** called **fibers**.
* React builds a **work-in-progress tree** in memory.
* Can be **paused, aborted, or restarted**.
* Non-blocking ✅.

#### **B. Commit Phase**

* Once the **work-in-progress tree** is ready → React **commits** updates to the real DOM.
* Always **synchronous** ✅.


### **3.4 Fiber Priorities**

React assigns **priority levels** to updates:

| **Priority**    | **Use Case**                   | **Fiber Behavior** |
| --------------- | ------------------------------ | ------------------ |
| High Priority   | User typing, animations        | Render immediately |
| Medium Priority | Visible UI updates             | Render soon        |
| Low Priority    | Background data fetching       | Render when free   |
| Idle            | Pre-fetching, non-urgent tasks | Render later       |

---

### **3.5 Concurrent Rendering (React 18+)**

React Fiber enables features like:

* **useTransition** → low-priority background updates
* **Suspense** → loading states without blocking UI
* **Selective hydration** → stream UI gradually

## **4. Virtual DOM + Diffing + Fiber Together**

### **Flow**

```
State/Props change
       ↓
React creates new Virtual DOM
       ↓
Diffing Algorithm compares old vs new VDOM
       ↓
Fiber breaks rendering into small tasks
       ↓
Prioritizes updates → commits only necessary changes
       ↓
Minimal DOM updates → Faster UI
```

## **5. Interview-Focused Key Takeaways**

| **Topic**           | **What to Remember**                                                     |
| ------------------- | ------------------------------------------------------------------------ |
| **Virtual DOM**     | A lightweight JS tree; React uses it to optimize real DOM updates.       |
| **Diffing**         | Uses heuristics & keys; compares efficiently in O(n).                    |
| **Keys**            | Crucial for stable identity; avoid using `index` as key.                 |
| **React Fiber**     | New reconciliation engine in React 16+; enables interruptible rendering. |
| **Prioritization**  | Fiber schedules updates based on importance for smooth UIs.              |
| **Concurrent Mode** | React 18+ leverages Fiber for concurrent features.                       |
---
<br>
<br>

# Mounting APIs: ReactDOM.render, createRoot

## **1. What Are Mounting APIs?**

Mounting APIs are responsible for **attaching a React application** to a **DOM node**.
This is the **entry point** of a React app.

Before **React 18**, we used **`ReactDOM.render`**.
From **React 18 onwards**, we use **`createRoot`** (concurrent mode enabled).

## **2. `ReactDOM.render()` (React 17 and below)** ✅ *(Deprecated in React 18)*

### **Syntax**

```jsx
import React from "react";
import ReactDOM from "react-dom";
import App from "./App";

ReactDOM.render(<App />, document.getElementById("root"));
```

### **How It Works Internally**

* **Step 1:** React creates a **root Fiber tree**.
* **Step 2:** Converts JSX → `React.createElement` → React Elements.
* **Step 3:** Performs **synchronous rendering** → blocks the main thread.
* **Step 4:** Updates the **DOM** with computed changes.

### **Limitations**

* ❌ No **concurrent rendering**
* ❌ No **automatic batching** for updates
* ❌ No **suspense streaming**
* ❌ Deprecated in React 18

## **3. `createRoot()` (React 18+)** ✅ *(Preferred API)*

### **Syntax**

```jsx
import React from "react";
import { createRoot } from "react-dom/client";
import App from "./App";

const root = createRoot(document.getElementById("root"));
root.render(<App />);
```

### **How It Works Internally**

* Creates a **Concurrent Root** instead of a Legacy Root.
* Uses **React Fiber** to **schedule** and **prioritize rendering**.
* Supports **concurrent rendering** → React can pause, resume, or abandon renders.
* Uses **automatic batching** → multiple `setState()` calls are batched into one render.
* Enables **Streaming SSR + Hydration** (faster initial loads).


## **4. Key Differences Between `ReactDOM.render` vs `createRoot`**

| Feature                     | **ReactDOM.render** (Legacy) | **createRoot** (Concurrent, React 18) |
| --------------------------- | ---------------------------- | ------------------------------------- |
| **Rendering mode**          | Synchronous                  | Concurrent (non-blocking)             |
| **API location**            | `react-dom`                  | `react-dom/client`                    |
| **Automatic batching**      | ❌ No                         | ✅ Yes                                 |
| **Suspense for SSR**        | ❌ Limited                    | ✅ Full support                        |
| **Streaming SSR**           | ❌ No                         | ✅ Yes                                 |
| **Hydration**               | Manual, less efficient       | Optimized with concurrent features    |
| **Recommended in React 18** | ❌ Deprecated                 | ✅ Preferred                           |

---

## **5. What Happens During Mounting?**

Let’s understand what happens **step by step** when we call `createRoot().render()`:

### **Step 1 — JSX Conversion**

```jsx
<App />
```

↓ Babel transpiles it to:

```js
React.createElement(App, null);
```

### **Step 2 — Fiber Tree Creation**

* React creates a **Fiber node** for `<App />`.
* A **root fiber** is created and linked to the DOM container.

### **Step 3 — Reconciliation**

* React compares the **previous virtual DOM** and **new virtual DOM**.
* It calculates a **diff** and schedules updates.

### **Step 4 — Commit Phase**

* React updates the **real DOM** efficiently.
* Triggers **effects** (`useEffect`, `componentDidMount`).

### **Step 5 — Concurrent Features (React 18 only)**

* React can **pause and resume rendering**.
* Uses **time slicing** → high-priority tasks like user inputs are not blocked.

## **6. Migrating from `ReactDOM.render` to `createRoot`**

If your React 17 code looks like this:

```js
ReactDOM.render(<App />, document.getElementById("root"));
```

Update to React 18:

```js
import { createRoot } from "react-dom/client";

const root = createRoot(document.getElementById("root"));
root.render(<App />);
```

⚡ That’s it — React automatically enables concurrent features.


## **7. When to Use Which**

* ✅ **React 18+** → Always use **`createRoot`**.
* 🟡 **React 17 or below** → Use **`ReactDOM.render`**.
* ❌ Avoid mixing them — they have different internal architectures.

---

## **8. Hydration API in React 18**

For **SSR** apps (Next.js, Remix, etc.), use `hydrateRoot` instead of `createRoot`:

```js
import { hydrateRoot } from "react-dom/client";

hydrateRoot(document.getElementById("root"), <App />);
```

This optimizes performance when **rehydrating server-rendered HTML**.

<br>
<br>

# what is createRoot method ? How it works internally

Let’s dive deep into **`createRoot`** in React, how it works internally, and how it differs from the older **`ReactDOM.render`** API.

---

## **1. What is `createRoot`?**

`createRoot` is a React 18+ API from the **ReactDOM** package that enables the **new concurrent rendering** capabilities introduced by **React Fiber**.

```javascript
import ReactDOM from "react-dom/client";
import App from "./App";

const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(<App />);
```

### **Purpose:**

* Supports **concurrent features** like **automatic batching**, **Suspense for data fetching**, and **Transitions**.
* Optimizes rendering for **performance** and **smooth user experience**.
* Replaces `ReactDOM.render()` (legacy from React 17 and below).


## **2. How it works internally**

### **Step 1: Creates a Root Fiber Tree**

When you call:

```js
const root = ReactDOM.createRoot(container);
```

* React internally creates a **Root Fiber Node** linked to the DOM `container`.
* It initializes the **Fiber tree** structure:

  * A **Root Fiber** → points to the **current Fiber tree**.
  * Prepares an **alternate Fiber tree** for reconciliation.
  * Sets up a **scheduler** for concurrent rendering.

---

### **Step 2: Rendering starts**

```js
root.render(<App />);
```

* React converts your JSX into **React elements** using `React.createElement()`.
* These elements are attached to the **Fiber tree**.
* React schedules an **update** and marks the root as **pending**.


### **Step 3: React Fiber’s Concurrent Rendering**

React Fiber uses a **cooperative scheduling model**:

* Work is split into **units of work** (one per Fiber node).
* React processes them **asynchronously** instead of blocking the main thread.
* If the browser needs to handle **high-priority tasks** (e.g., animations, input), React pauses rendering and resumes later.

This is why React 18 feels smoother compared to older versions.

### **Step 4: Commit Phase**

Once all fibers are reconciled:

* React **commits** the changes to the DOM in a **single batch**.
* It updates:

  * DOM nodes
  * Event listeners
  * Ref assignments
* Then calls lifecycle methods/hooks like `useEffect`.

## **4. Internal Flowchart**

**Steps when `createRoot` is called:**

```
createRoot(container)
   ↓
Create Root Fiber → Initialize Fiber tree
   ↓
root.render(<App />)
   ↓
Convert JSX → React elements → Fiber nodes
   ↓
Schedule updates via React Fiber
   ↓
Concurrent reconciliation (pauses if needed)
   ↓
Commit phase → Update DOM in batches
   ↓
Trigger effects & refs
```

<br>
<br>

# What is React.createElement ?

`React.createElement` is the **core React API** that **creates a React element object** — the building block of React applications.
When you write **JSX**, it **compiles** into `React.createElement()` calls under the hood.

### **Example**

```jsx
const element = <h1 className="title">Hello React</h1>;
console.log(element);
```

After Babel transpilation, JSX becomes:

```js
const element = React.createElement(
  "h1",
  { className: "title" },
  "Hello React"
);
```

So, **JSX is just syntactic sugar** for `React.createElement()`.

## **2. Syntax**

```js
React.createElement(
  type,
  props,
  ...children
)
```

| **Parameter** | **Description**                                           | **Example**                     |
| ------------- | --------------------------------------------------------- | ------------------------------- |
| `type`        | The HTML tag (string) or React component (function/class) | `"div"` or `MyComponent`        |
| `props`       | An object of attributes and properties                    | `{ className: "box", id: "x" }` |
| `...children` | Nested elements or text inside the component              | `"Hello"`, `<span>World</span>` |

## **3. What it Returns**

`React.createElement()` **does not** create a **real DOM node**.
It returns a **plain JavaScript object** called a **React Element**:

```js
{
  type: 'h1',
  key: null,
  ref: null,
  props: {
    className: 'title',
    children: 'Hello React'
  },
  _owner: null,
  _store: {}
}
```

This **React Element object** is later used by **React Fiber** during **reconciliation** to decide **what to render or update** in the **real DOM**.

---

## **4. How It Works Internally**

Let's break it down step by step.

### **Step 1 — JSX → createElement (via Babel)**

```jsx
<App title="Hello" />
```

Babel converts it into:

```js
React.createElement(App, { title: "Hello" });
```

---

### **Step 2 — Create React Element Object**

Inside `react/src/ReactElement.js`, the **internal implementation** looks like:

```js
function createElement(type, config, ...children) {
  let props = {};
  let key = null;
  let ref = null;

  // Handle special props
  if (config != null) {
    if (config.key !== undefined) key = '' + config.key;
    if (config.ref !== undefined) ref = config.ref;
    props = { ...config };
  }

  // Handle children
  props.children = children.length === 1 ? children[0] : children;

  return {
    $$typeof: Symbol.for('react.element'),
    type,
    key,
    ref,
    props,
    _owner: null
  };
}
```

### **Step 3 — React Fiber Uses It**

* These React Element objects are stored in the **Fiber tree**.
* During **reconciliation**, React compares the **old elements** vs **new elements**.
* Based on differences, React **updates the real DOM efficiently**.

## **5. `React.createElement` vs Real DOM Creation**

| Aspect          | **React.createElement**        | **document.createElement** |
| --------------- | ------------------------------ | -------------------------- |
| **Output**      | Virtual React Element (object) | Actual DOM node            |
| **When Used**   | During render/reconciliation   | Direct DOM manipulation    |
| **Performance** | Optimized diffing via Fiber    | No diffing, direct updates |


## **6. Why `React.createElement` Exists**

* JSX is **not understood** by browsers → Babel transpiles JSX to `React.createElement`.
* Without JSX, you'd manually write:

```js
const element = React.createElement(
  'button',
  { className: 'btn' },
  'Click Me'
);
```

Instead of:

```jsx
<button className="btn">Click Me</button>
```

This is **cleaner**, but under the hood **both are the same**.


## **7. How Children Are Handled**

Example:

```jsx
<div>
  <h1>Hello</h1>
  <p>World</p>
</div>
```

Transpiles to:

```js
React.createElement(
  "div",
  null,
  React.createElement("h1", null, "Hello"),
  React.createElement("p", null, "World")
);
```

* Each child becomes its own React Element.
* React maintains a **tree structure** using these elements.
<br>
<br>

# 