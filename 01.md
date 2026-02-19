# ⚡ MASTER FILE 1: REACT CORE FOUNDATIONS
### 📝 Exam-Optimized Revision | 25 MCQs + 12 Coding Questions

---

## 1. ULTRA-COMPRESSED BULLET SUMMARY

### JSX
- JSX = syntax sugar for `React.createElement(type, props, children)`
- `<h1>Hi</h1>` compiles to `React.createElement("h1", null, "Hi")`
- **Must return ONE root element** → use `<>...</>` (Fragment) or a wrapper `<div>`
- Use `{}` for JS expressions inside JSX → `{2+2}`, `{variable}`, `{fn()}`
- **Cannot use `if/else` inside JSX** → use ternary `? :` or `&&`
- `class` → `className` (because `class` is reserved in JS)
- `for` → `htmlFor`
- Inline style = **JS object** with **camelCase** keys: `style={{ fontSize: 16, color: "red" }}`
- Numeric style values default to `px` (e.g., `padding: 10` = `10px`)
- JSX prevents XSS – values are escaped before rendering
- **Self-closing tags required**: `<img />`, `<input />`, `<br />`
- Comments: `{/* comment */}`

### Components
- **Functional**: JS function returning JSX → modern, uses hooks
- **Class**: ES6 class extending `React.Component` → has `render()`, lifecycle methods
- Component name **MUST start with uppercase** → `<MyComp />` not `<mycomp />`
- Components are **reusable, composable, independent**
- Each component manages its own **state** and receives **props**

### Props
- Props = **read-only** data passed parent → child
- **One-way data flow** (parent to child only)
- Access in functional: `function Comp({ name })` or `function Comp(props)` → `props.name`
- Access in class: `this.props.name`
- `children` prop → content between opening/closing tags: `<Comp>child</Comp>`
- **Default props**: `Comp.defaultProps = { name: "Guest" }` or `function Comp({ name = "Guest" })`
- **PropTypes** validation: `Comp.propTypes = { name: PropTypes.string.isRequired }`
- Functions can be passed as props (callbacks for child→parent communication)
- **Prop drilling** = passing props through many intermediate layers → solution: Context API

### State
- State = **mutable** private data owned by a component
- Functional: `const [val, setVal] = useState(initialValue)`
- Class: `this.state = { key: value }` in constructor, update via `this.setState({ key: newVal })`
- **State updates are asynchronous** (batched by React)
- **Never mutate state directly**: ❌ `state.push()` ✅ `setItems([...items, newItem])`
- Use **functional update** when new state depends on old: `setCount(prev => prev + 1)`
- **Lazy initialization**: `useState(() => expensiveCalc())` – function runs only on first render
- Objects/arrays: always spread to create new copy → `setUser(prev => ({ ...prev, age: 31 }))`

### Event Handling
- Events use **camelCase**: `onClick`, `onChange`, `onSubmit`, `onKeyDown`
- Pass **function reference**, not invocation: ✅ `onClick={handleClick}` ❌ `onClick={handleClick()}`
- Event object = **SyntheticEvent** (cross-browser wrapper over native event)
- Access native event: `event.nativeEvent`
- `event.preventDefault()` → stops default behavior (form submit page reload)
- `event.stopPropagation()` → stops event bubbling to parent
- **Event pooling** (React <17): event properties nullified after handler; use `event.persist()` to retain
- **Class component binding**: bind in constructor `this.handleClick = this.handleClick.bind(this)` OR use arrow functions
- **Functional components**: no binding needed (arrow functions auto-bind)

### Conditional Rendering
- `if/else` → outside JSX return
- **Ternary**: `{condition ? <A /> : <B />}`
- **Logical AND**: `{condition && <A />}` → renders A if true, nothing if false
  - ⚠️ **TRAP**: `{0 && <A />}` renders `0` on screen! (0 is falsy but renderable)
- **Switch/case** → multiple conditions (cleaner than nested ternary)
- **IIFE** → `{(() => { if(...) return <A/>; })()}`
- Conditional classes: `` className={`btn ${isActive ? "active" : ""}`} ``

### Lists & Keys (`map`)
- `array.map(item => <li key={item.id}>{item.name}</li>)`
- **`key` is MANDATORY** for list items → helps React identify which items changed
- Keys must be **unique among siblings** (not globally)
- ❌ **Don't use array index as key** if list can reorder/filter (causes bugs)
- ✅ Use **stable unique ID** (e.g., `item.id`)
- Missing key → React warning in console
- Nested lists: use nested `map()`

### Forms (Controlled vs Uncontrolled)
- **Controlled**: input value tied to state → `value={state}` + `onChange` handler
  - React is **single source of truth** for input value
  - Can validate, format, restrict input in real-time
- **Uncontrolled**: input manages own value → access via `ref` (`useRef`)
  - DOM is source of truth
- `<textarea value={text} onChange={handleChange} />` (React uses `value`, not `children`)
- `<select value={selected} onChange={handleChange}>` (React uses `value` on `<select>`)
- Checkbox: use `checked` instead of `value` → `checked={formData.agree}`
- Radio: `checked={formData.option === "A"}`
- Multiple inputs with one handler: use `name` attribute + `[e.target.name]: e.target.value`
- `onSubmit` on `<form>` → always call `e.preventDefault()`

### DOM Manipulation
- React uses **Virtual DOM** → in-memory representation of UI
- On state change: new virtual tree → **diffing** (old vs new) → **minimal real DOM updates**
- **Reconciliation** = diffing algorithm
- **Batch updates**: multiple `setState` calls batched into single re-render
- **Never use `document.getElementById`** in React → use `useRef` instead
- `ReactDOM.createRoot(document.getElementById('root')).render(<App />)` → mounts app

### Images & Media
- Import images: `import logo from './assets/logo.png'` then `<img src={logo} />`
- Public folder: `<img src="/images/logo.png" />`
- Dynamic source: `<img src={imageUrl} />`

### Lifecycle Methods (Class Components)
| Phase | Method | Purpose | Hook Equivalent |
|-------|--------|---------|-----------------|
| **Mounting** | `constructor()` | Init state, bind methods | `useState()` initial value |
| | `render()` | Returns JSX | Function body |
| | `componentDidMount()` | API calls, subscriptions | `useEffect(() => {}, [])` |
| **Updating** | `shouldComponentUpdate(nextProps, nextState)` | Control re-rendering | `React.memo()` |
| | `render()` | Re-renders JSX | Re-execution of function |
| | `componentDidUpdate(prevProps, prevState)` | Side effects after update | `useEffect(() => {}, [dep])` |
| | `static getDerivedStateFromProps()` | Sync state with props (rare) | — |
| **Unmounting** | `componentWillUnmount()` | Cleanup (timers, listeners) | `useEffect` cleanup `return () => {}` |
| **Error** | `componentDidCatch(error, info)` | Error boundaries | No hook equivalent |

---

## 2. SYNTAX TEMPLATES

### Functional Component (Modern)
```jsx
import React, { useState } from 'react';

function MyComponent({ title, count = 0 }) {
  const [value, setValue] = useState("");

  const handleChange = (e) => setValue(e.target.value);

  return (
    <div className="container">
      <h1>{title}</h1>
      <input value={value} onChange={handleChange} />
      <p>Count: {count}</p>
    </div>
  );
}

export default MyComponent;
```

### Class Component
```jsx
import React, { Component } from 'react';

class MyComponent extends Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    this.setState(prev => ({ count: prev.count + 1 }));
  }

  componentDidMount() {
    console.log("Mounted");
  }

  componentWillUnmount() {
    console.log("Unmounting");
  }

  render() {
    return (
      <div>
        <h1>{this.props.title}</h1>
        <p>Count: {this.state.count}</p>
        <button onClick={this.handleClick}>+1</button>
      </div>
    );
  }
}

export default MyComponent;
```

### Controlled Form Template
```jsx
function MyForm() {
  const [formData, setFormData] = useState({ name: "", email: "" });

  const handleChange = (e) => {
    const { name, value } = e.target;
    setFormData(prev => ({ ...prev, [name]: value }));
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log(formData);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input name="name" value={formData.name} onChange={handleChange} />
      <input name="email" value={formData.email} onChange={handleChange} />
      <button type="submit">Submit</button>
    </form>
  );
}
```

### List Rendering with Keys
```jsx
function UserList({ users }) {
  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>{user.name} - Age: {user.age}</li>
      ))}
    </ul>
  );
}
```

### Conditional Rendering Patterns
```jsx
// Ternary
{isLoggedIn ? <Dashboard /> : <Login />}

// Logical AND
{hasError && <ErrorMessage />}

// Switch-case (in function body)
function StatusMsg({ status }) {
  switch (status) {
    case "loading": return <p>Loading...</p>;
    case "error":   return <p>Error!</p>;
    case "success": return <p>Done!</p>;
    default:        return null;
  }
}
```

### Inline Style Template
```jsx
<div style={{
  backgroundColor: isActive ? "green" : "gray",
  fontSize: 18,
  padding: "10px 20px",
  borderRadius: 8
}}>
  Content
</div>
```

---

## 3. CONCEPT COMPARISONS

### Class vs Functional Components
| Feature | Class | Functional |
|---------|-------|------------|
| Syntax | `class X extends Component` | `function X()` or `const X = () =>` |
| State | `this.state` + `this.setState()` | `useState()` |
| Lifecycle | `componentDidMount`, `componentDidUpdate`, etc. | `useEffect()` |
| Props access | `this.props.name` | `props.name` or destructure `{ name }` |
| Event binding | Must bind `this` (constructor or arrow) | No binding needed |
| Performance | Heavier (class overhead) | Lighter, faster |
| Error Boundaries | ✅ Supported (`componentDidCatch`) | ❌ Not directly supported |
| `this` keyword | Required everywhere | Not used |
| Modern standard | Legacy | Current recommended |

### Controlled vs Uncontrolled Components
| Feature | Controlled | Uncontrolled |
|---------|-----------|-------------|
| Source of truth | React state | DOM itself |
| Value attribute | `value={state}` | No `value` (uses `defaultValue`) |
| Change handling | `onChange` handler required | Optional, access via `ref` |
| Validation | Real-time (on every keystroke) | On submit only |
| Use case | Most forms | File inputs, quick prototypes |
| Exam default | **This is what React recommends** | Special cases only |

### `class` vs `className`
| | HTML | JSX |
|-|------|-----|
| Attribute | `class="btn"` | `className="btn"` |
| Reason | — | `class` is reserved JS keyword |

### Inline Styles: HTML vs React
| | HTML | React |
|-|------|-------|
| Format | `style="color: red; font-size: 16px"` (string) | `style={{ color: "red", fontSize: 16 }}` (object) |
| Property names | kebab-case (`font-size`) | camelCase (`fontSize`) |
| Values | Always strings | Strings or numbers (numbers default to `px`) |

### `setState` vs Direct Mutation
| | `setState` / `setX` | Direct mutation |
|-|---------------------|-----------------|
| Triggers re-render | ✅ Yes | ❌ No |
| Correct in React | ✅ | ❌ Never do this |
| Example | `setItems([...items, x])` | ~~`items.push(x)`~~ |

---

## 4. 25 TRICKY MCQ TRAPS (WITH ANSWERS)

**Q1.** What does JSX compile to?
- A) HTML
- B) `document.createElement()`
- C) `React.createElement()`
- D) Virtual DOM nodes

**Answer: C** — JSX is syntactic sugar for `React.createElement()`.

---

**Q2.** Which is the correct way to apply inline styles in React?
- A) `style="color: red"`
- B) `style={{ color: "red" }}`
- C) `style={color: "red"}`
- D) `style={"color: red"}`

**Answer: B** — Inline styles require a JS object with double curly braces (outer = expression, inner = object).

---

**Q3.** What happens if you write `<mycomponent />` (lowercase) in JSX?
- A) React renders it as a custom component
- B) React renders it as an HTML element
- C) Compilation error
- D) Runtime error

**Answer: B** — Lowercase tags are treated as HTML elements. Custom components must start with uppercase.

---

**Q4.** What does `{0 && <Component />}` render?
- A) Nothing
- B) `<Component />`
- C) `0`
- D) `false`

**Answer: C** — `0` is falsy but React renders it as text. Use `{count > 0 && <Component />}` instead.

---

**Q5.** Which is NOT a valid React event handler name?
- A) `onClick`
- B) `onChange`
- C) `onchange`
- D) `onSubmit`

**Answer: C** — React uses camelCase (`onChange`), not lowercase (`onchange`).

---

**Q6.** What happens if you call `setState()` inside `render()` of a class component?
- A) Component renders normally
- B) Infinite loop
- C) State updates once
- D) Nothing

**Answer: B** — `setState` triggers re-render → calls `render` again → calls `setState` again → infinite loop.

---

**Q7.** Props in React are:
- A) Mutable by child
- B) Read-only
- C) Two-way bound
- D) Only for class components

**Answer: B** — Props are immutable. Child cannot modify props.

---

**Q8.** What is the purpose of `key` in list rendering?
- A) Styling
- B) CSS selection
- C) Helps React identify which items changed
- D) Required by JavaScript

**Answer: C** — Keys help React's reconciliation algorithm efficiently update the DOM.

---

**Q9.** Which is the correct way to prevent form submission page reload?
- A) `return false`
- B) `event.stopPropagation()`
- C) `event.preventDefault()`
- D) `event.cancelBubble()`

**Answer: C** — `event.preventDefault()` prevents default browser behavior.

---

**Q10.** In a controlled input, if you set `value` but forget `onChange`:
- A) Input works normally
- B) Input becomes read-only
- C) Input crashes
- D) Value is ignored

**Answer: B** — The input will be stuck at the initial value since React controls it but there's no handler to update state.

---

**Q11.** `this.setState()` in class components is:
- A) Synchronous
- B) Asynchronous (batched)
- C) Only works inside `render()`
- D) Deprecated

**Answer: B** — React batches multiple `setState` calls for performance.

---

**Q12.** Which lifecycle method is equivalent to `useEffect(() => {}, [])` in functional components?
- A) `componentDidUpdate`
- B) `componentWillMount`
- C) `componentDidMount`
- D) `shouldComponentUpdate`

**Answer: C** — Empty dependency array = runs once after mount = `componentDidMount`.

---

**Q13.** React uses ____________ instead of the real DOM for efficiency.
- A) Shadow DOM
- B) Virtual DOM
- C) Server DOM
- D) Cached DOM

**Answer: B** — Virtual DOM allows diffing and minimal real DOM updates.

---

**Q14.** What is the output of `<>{[1,2,3].map(n => <span>{n}</span>)}</>` without keys?
- A) Error
- B) 123 with console warning
- C) 123 without warning
- D) Nothing renders

**Answer: B** — It renders but React gives a "unique key" warning.

---

**Q15.** How do you pass a function from parent to child?
- A) `<Child onClick={handleClick} />`
- B) `<Child onClick="handleClick" />`
- C) `<Child onClick={handleClick()} />`
- D) Not possible

**Answer: A** — Pass function reference, not invocation. C would call it immediately during render.

---

**Q16.** What is `defaultProps` used for?
- A) Setting initial state
- B) Providing default values for missing props
- C) Validating prop types
- D) Resetting component

**Answer: B** — If a prop is not passed, `defaultProps` provides the fallback value.

---

**Q17.** `super(props)` in class constructor is needed because:
- A) It's optional
- B) To access `this.props` inside constructor
- C) To bind events
- D) To initialize state

**Answer: B** — Without `super(props)`, `this.props` is `undefined` inside the constructor.

---

**Q18.** Which is TRUE about React Fragments?
- A) `<Fragment>` adds a `<div>` to the DOM
- B) `<>...</>` adds no extra DOM node
- C) Fragments cannot contain multiple children
- D) Fragments require a `key` prop always

**Answer: B** — Fragments group elements without extra DOM nodes. Key is only needed in lists.

---

**Q19.** `event.nativeEvent` in React gives you:
- A) The SyntheticEvent
- B) The original browser DOM event
- C) A copy of the event
- D) Nothing

**Answer: B** — `nativeEvent` accesses the underlying browser event.

---

**Q20.** State update `setCount(count + 1)` called 3 times in a row results in:
- A) count increments by 3
- B) count increments by 1
- C) Error
- D) count increments by 2

**Answer: B** — All three calls reference the same stale `count` value. Use `setCount(prev => prev + 1)` to increment by 3.

---

**Q21.** `componentWillUnmount()` is used for:
- A) Fetching data
- B) Setting state
- C) Cleanup (remove listeners, clear timers)
- D) Rendering UI

**Answer: C** — It's the cleanup phase before component is removed from DOM.

---

**Q22.** In JSX, HTML attribute `for` is replaced by:
- A) `htmlFor`
- B) `For`
- C) `forLabel`
- D) `for` (same)

**Answer: A** — `for` is reserved in JS, so JSX uses `htmlFor`.

---

**Q23.** What does `React.memo()` do?
- A) Memoizes state values
- B) Prevents re-render if props haven't changed (shallow compare)
- C) Caches API responses
- D) Replaces `useEffect`

**Answer: B** — It's the functional component equivalent of `PureComponent`.

---

**Q24.** `shouldComponentUpdate` returns `false`. What happens?
- A) Component renders with old data
- B) Component skips re-render entirely
- C) Component unmounts
- D) Error thrown

**Answer: B** — Returning `false` skips the re-render for that update cycle.

---

**Q25.** Which statement about the Virtual DOM is FALSE?
- A) It's a JavaScript object representation of the real DOM
- B) It directly modifies the browser DOM
- C) React compares old and new virtual trees (diffing)
- D) Only changed parts are updated in the real DOM

**Answer: B** — The Virtual DOM does NOT directly modify the browser DOM. React applies minimal changes after diffing.

---

## 5. 12 SHORT SYNTAX CODING QUESTIONS

**Q1.** Write a functional component that displays "Hello, {name}" where name is a prop with default value "World".
```jsx
function Greeting({ name = "World" }) {
  return <h1>Hello, {name}</h1>;
}
```

---

**Q2.** Write a counter component using `useState` with Increment and Decrement buttons.
```jsx
import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);
  return (
    <div>
      <p>{count}</p>
      <button onClick={() => setCount(count + 1)}>+</button>
      <button onClick={() => setCount(count - 1)}>-</button>
    </div>
  );
}
```

---

**Q3.** Convert this class component to functional:
```jsx
// CLASS version:
class Msg extends Component {
  render() { return <p>{this.props.text}</p>; }
}

// ANSWER (Functional):
function Msg({ text }) {
  return <p>{text}</p>;
}
```

---

**Q4.** Write a controlled text input that shows typed text below it.
```jsx
function TextDisplay() {
  const [text, setText] = useState("");
  return (
    <div>
      <input value={text} onChange={(e) => setText(e.target.value)} />
      <p>{text}</p>
    </div>
  );
}
```

---

**Q5.** Render an array `["React", "Vue", "Angular"]` as an unordered list with proper keys.
```jsx
function FrameworkList() {
  const items = ["React", "Vue", "Angular"];
  return (
    <ul>
      {items.map((item, index) => (
        <li key={index}>{item}</li>
      ))}
    </ul>
  );
}
```

---

**Q6.** Show "Welcome" if `isLoggedIn` is true, else show "Please Login" using ternary.
```jsx
function Auth({ isLoggedIn }) {
  return <h1>{isLoggedIn ? "Welcome" : "Please Login"}</h1>;
}
```

---

**Q7.** Write a class component with state `{ name: "React" }` and display it.
```jsx
class NameDisplay extends Component {
  constructor(props) {
    super(props);
    this.state = { name: "React" };
  }
  render() {
    return <h1>{this.state.name}</h1>;
  }
}
```

---

**Q8.** Write a form with name and email fields using a single state object and one `handleChange`.
```jsx
function MyForm() {
  const [form, setForm] = useState({ name: "", email: "" });
  const handleChange = (e) => {
    setForm(prev => ({ ...prev, [e.target.name]: e.target.value }));
  };
  return (
    <form>
      <input name="name" value={form.name} onChange={handleChange} />
      <input name="email" value={form.email} onChange={handleChange} />
    </form>
  );
}
```

---

**Q9.** Apply dynamic inline styling: green background if `active`, red if not.
```jsx
function Box({ active }) {
  return (
    <div style={{ backgroundColor: active ? "green" : "red", padding: 20 }}>
      {active ? "Active" : "Inactive"}
    </div>
  );
}
```

---

**Q10.** Write a button that prevents form submission default behavior and alerts the input value.
```jsx
function AlertForm() {
  const [val, setVal] = useState("");
  const handleSubmit = (e) => {
    e.preventDefault();
    alert(val);
  };
  return (
    <form onSubmit={handleSubmit}>
      <input value={val} onChange={(e) => setVal(e.target.value)} />
      <button type="submit">Submit</button>
    </form>
  );
}
```

---

**Q11.** Use `componentDidMount` to log "Mounted!" (class component).
```jsx
class Logger extends Component {
  componentDidMount() {
    console.log("Mounted!");
  }
  render() {
    return <p>Check console</p>;
  }
}
```

---

**Q12.** Pass a function from Parent to Child. Child calls it on button click.
```jsx
function Parent() {
  const greet = () => alert("Hello from Parent!");
  return <Child onGreet={greet} />;
}

function Child({ onGreet }) {
  return <button onClick={onGreet}>Greet</button>;
}
```

---

> **END OF FILE 1 — REACT CORE FOUNDATIONS** ✅
