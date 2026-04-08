# React Basics - Complete Reference Guide

> Your foundation for React mastery. Master these concepts and everything else builds on them.

---

## Table of Contents
1. [What is React?](#1-what-is-react)
2. [JSX Deep Dive](#2-jsx-deep-dive)
3. [Components](#3-components)
4. [Props](#4-props)
5. [State](#5-state)
6. [Event Handling](#6-event-handling)
7. [Conditional Rendering](#7-conditional-rendering)
8. [Lists and Keys](#8-lists-and-keys)
9. [Forms](#9-forms)
10. [Thinking in React](#10-thinking-in-react)

---

## 1. What is React?

### Definition
React is a **JavaScript library** for building user interfaces, particularly single-page applications. It was developed by Facebook (Meta) and is now maintained by Meta and a community of developers.

### Core Characteristics

| Characteristic | Explanation |
|---------------|-------------|
| **Component-Based** | Build encapsulated components that manage their own state |
| **Declarative** | Describe what UI should look like; React handles updates |
| **Learn Once, Write Anywhere** | Works with React Native for mobile apps |
| **Virtual DOM** | Efficient DOM updates through reconciliation |
| **Unidirectional Data Flow** | Data flows down from parent to child |

### What React IS and ISN'T

```
┌─────────────────────────────────────────────────────────────────┐
│                         REACT IS                                │
├─────────────────────────────────────────────────────────────────┤
│  ✅ A UI library (not a full framework)                         │
│  ✅ Component-based architecture                                │
│  ✅ Declarative (describe UI, not steps)                        │
│  ✅ Virtual DOM for efficient updates                           │
│  ✅ Strong ecosystem (React Router, Redux, etc.)                │
└─────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────┐
│                         REACT ISN'T                             │
├─────────────────────────────────────────────────────────────────┤
│  ❌ A full framework (no routing, no HTTP client built-in)       │
│  ❌ MVC (it's View only, you add Controller/Model with code)    │
│  ❌ Direct DOM manipulator (uses Virtual DOM)                    │
│  ❌ Required to use JSX (can use React.createElement)           │
└─────────────────────────────────────────────────────────────────┘
```

### Virtual DOM - The Secret Sauce

The Virtual DOM is a **lightweight JavaScript representation** of the real DOM. Here's how it works:

```
┌─────────────────────────────────────────────────────────────────────┐
│                      VIRTUAL DOM WORKFLOW                           │
│                                                                      │
│  1. STATE CHANGE                                                     │
│     User clicks → State updates → New Virtual DOM tree created        │
│                                                                      │
│  2. DIFFING (RECONCILIATION)                                         │
│     React compares:  NEW Virtual Tree  vs  OLD Virtual Tree          │
│                         │                         │                  │
│                    React elements          React elements            │
│                    with changes            that stayed same          │
│                                                                      │
│  3. MINIMAL DOM UPDATE                                               │
│     React calculates the MOST EFFICIENT way to update real DOM       │
│     Only changed elements are updated                                │
│                                                                      │
│  ┌─────────────────┐      ┌─────────────────┐      ┌──────────────┐ │
│  │  Before Update  │  →   │  DIFFING      │  →   │ After Update │ │
│  │  <div>         │      │  Compare      │      │ <div>        │ │
│  │    <h1>Hi</h1> │      │  Old vs New   │      │   <h1>Bye</h1│ │
│  │  </div>        │      │               │      │ </div>       │ │
│  └─────────────────┘      └─────────────────┘      └──────────────┘ │
└─────────────────────────────────────────────────────────────────────┘
```

**Why Virtual DOM is Fast:**
- Direct DOM manipulation is expensive (browser repaints/reflows)
- JavaScript operations are cheap
- React batches updates and applies them efficiently
- Diffing algorithm minimizes operations

### React vs Other Libraries/Frameworks

| Aspect | React | Angular | Vue |
|--------|-------|---------|-----|
| Type | Library | Framework | Framework |
| DOM | Virtual DOM | Real DOM | Virtual DOM |
| Data Binding | One-way | Two-way | Two-way |
| Learning Curve | Moderate | Steep | Easy |
| Size | Smaller | Larger | Medium |
| Flexibility | High | Low | Medium |

---

## 2. JSX Deep Dive

### What is JSX?

JSX is a **syntax extension** for JavaScript that allows you to write HTML-like code in your JavaScript files. Under the hood, JSX is compiled to `React.createElement()` calls.

```
┌─────────────────────────────────────────────────────────────────┐
│                         JSX TRANSFORMATION                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  JSX (What You Write)                                            │
│  ┌──────────────────────────────┐                                │
│  │  const element = (           │                                │
│  │    <h1 className="title">    │                                │
│  │      Hello, {name}!          │                                │
│  │    </h1>                     │                                │
│  │  );                          │                                │
│  └──────────────────────────────┘                                │
│                    │                                             │
│                    ▼                                             │
│         ┌─────────────────┐                                      │
│         │  BABEL COMPILES │                                      │
│         │      TO THIS     │                                      │
│         └─────────────────┘                                      │
│                    │                                             │
│                    ▼                                             │
│  JavaScript (What React Sees)                                    │
│  ┌──────────────────────────────┐                                │
│  │  const element = React.      │                                │
│  │    createElement(             │                                │
│  │      "h1",                    │                                │
│  │      { className: "title" },  │                                │
│  │      "Hello, ", name, "!"     │                                │
│  │    );                         │                                │
│  └──────────────────────────────┘                                │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### JSX Rules You MUST Know

#### Rule 1: Return ONE Root Element
```jsx
// ❌ INVALID - Two root elements
function BadComponent() {
  return (
    <h1>Title</h1>
    <p>Paragraph</p>
  );
}

// ✅ VALID - Wrap in div or Fragment
function GoodComponent() {
  return (
    <div>
      <h1>Title</h1>
      <p>Paragraph</p>
    </div>
  );
}

// ✅ VALID - Use Fragment (doesn't add DOM node)
function GoodComponent() {
  return (
    <>
      <h1>Title</h1>
      <p>Paragraph</p>
    </>
  );
}
```

#### Rule 2: Use `className` Instead of `class`
```jsx
// ❌ WRONG
<div class="container">Content</div>

// ✅ CORRECT
<div className="container">Content</div>

// Why? "class" is a reserved keyword in JavaScript
```

#### Rule 3: Use `htmlFor` Instead of `for`
```jsx
// ❌ WRONG
<label for="username">Username</label>

// ✅ CORRECT
<label htmlFor="username">Username</label>
```

#### Rule 4: All Tags Must Be Closed
```jsx
// Self-closing tags MUST end with />
<img src="logo.png" />
<input type="text" />
<br />
<hr />
```

#### Rule 5: camelCase for Attributes
```jsx
// Most attributes use camelCase
<div 
  onClick={handler}
  tabIndex={0}
  strokeWidth={2}
  fontSize={16}
/>

// Exception: data- and aria- attributes keep kebab-case
<div data-user-id="123" aria-label="User profile" />
```

### Expressions in JSX

Use curly braces `{}` to embed any JavaScript expression:

```jsx
function Greeting() {
  const name = "Alice";
  const isLoggedIn = true;
  const style = { color: "blue" };

  return (
    <div>
      {/* Basic expressions */}
      <h1>Hello, {name}!</h1>
      <p>2 + 2 = {2 + 2}</p>
      
      {/* Function calls */}
      <p>{getDate()}</p>
      
      {/* Ternary operator */}
      <p>{isLoggedIn ? "Welcome back!" : "Please login"}</p>
      
      {/* Logical AND */}
      {isLoggedIn && <button>Logout</button>}
      
      {/* Dynamic attributes */}
      <img src={user.avatarUrl} alt={user.name} />
      
      {/* Dynamic styles */}
      <p style={{ color: style.color, fontSize: "16px" }}>Styled text</p>
    </div>
  );
}
```

### Inline Styles in React

```jsx
// Inline styles are JavaScript objects
const styles = {
  container: {
    // camelCase instead of kebab-case
    backgroundColor: "#f0f0f0",
    paddingTop: "20px",
    paddingBottom: "20px",
    // Numbers default to px (for most properties)
    padding: 20,           // equivalent to "20px"
    margin: "10px 20px",   // vertical horizontal
    borderRadius: 8,
    fontSize: 16,
    // Hyphenated properties become camelCase
    fontFamily: "Arial",
    borderWidth: 1,
  }
};

function StyledComponent() {
  return (
    <div style={styles.container}>
      <p style={{ color: "red" }}>Red text</p>
    </div>
  );
}
```

### Comments in JSX

```jsx
function CommentSection() {
  return (
    <div>
      {/* This is a single-line comment in JSX */}
      
      {/*
       * This is a multi-line comment
       * Use it for documentation
       */}
      
      {/* Conditional comment */}
      {isLoading && (
        // Don't forget the opening brace!
        <p>Loading...</p>
      )}
    </div>
  );
}
```

### JSX Prevents XSS Attacks

React automatically escapes values embedded in JSX to prevent XSS (Cross-Site Scripting) attacks:

```jsx
function SafeComponent() {
  const maliciousInput = "<script>alert('hacked!')</script>";
  
  return (
    <div>
      {/* This renders as text, NOT as executable script */}
      <p>{maliciousInput}</p>
      
      {/* Output: &lt;script&gt;alert('hacked!')&lt;/script&gt; */}
    </div>
  );
}
```

---

## 3. Components

### What is a Component?

A **component** is a reusable, self-contained piece of UI. Think of components as custom HTML elements that can contain their own logic, styling, and structure.

```
┌─────────────────────────────────────────────────────────────────┐
│                    COMPONENT ARCHITECTURE                       │
│                                                                  │
│                        ┌─────────────┐                          │
│                        │    App       │                          │
│                        │ (Component)  │                          │
│                        └──────┬──────┘                          │
│                               │                                  │
│              ┌────────────────┼────────────────┐                 │
│              ▼                ▼                ▼                 │
│       ┌────────────┐   ┌────────────┐   ┌────────────┐        │
│       │   Header   │   │   Main     │   │   Footer   │        │
│       │            │   │            │   │            │        │
│       └────────────┘   └─────┬──────┘   └────────────┘        │
│                              │                                   │
│                    ┌─────────┼─────────┐                        │
│                    ▼         ▼         ▼                        │
│               ┌────────┐ ┌────────┐ ┌────────┐                   │
│               │ Post  │ │ Post   │ │ Post  │                   │
│               │Card 1 │ │Card 2  │ │Card 3  │                   │
│               └────────┘ └────────┘ └────────┘                   │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Two Types of Components

#### 1. Functional Components (Modern - Use These!)

```jsx
// Basic functional component
function Welcome() {
  return <h1>Welcome!</h1>;
}

// With arrow function syntax
const Welcome = () => {
  return <h1>Welcome!</h1>;
};

// Implicit return (no curly braces needed for single expression)
const Welcome = () => <h1>Welcome!</h1>;

// With props
function Greeting({ name, age }) {
  return (
    <div>
      <h1>Hello, {name}!</h1>
      <p>You are {age} years old.</p>
    </div>
  );
}
```

#### 2. Class Components (Legacy - Still Important to Know)

```jsx
import React, { Component } from 'react';

class Greeting extends Component {
  constructor(props) {
    super(props);  // Must call super(props) to access this.props
    this.state = { count: 0 };
    // Bind methods if not using arrow functions
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick() {
    this.setState({ count: this.state.count + 1 });
  }

  render() {
    return (
      <div>
        <h1>Hello, {this.props.name}!</h1>
        <p>Count: {this.state.count}</p>
        <button onClick={this.handleClick}>Click me</button>
      </div>
    );
  }
}
```

### Functional vs Class Components

| Aspect | Functional | Class |
|--------|------------|-------|
| **Syntax** | Simple function | ES6 class extending Component |
| **State** | `useState` hook | `this.state` + `this.setState` |
| **Lifecycle** | `useEffect` hook | Lifecycle methods |
| **`this` keyword** | Not needed | Required throughout |
| **Boilerplate** | Minimal | More verbose |
| **Performance** | Slightly faster | Slightly heavier |
| **Error Boundaries** | Not supported | Supported |
| **Code Splitting** | Easier | Requires Suspense |
| **Recommended** | ✅ YES | ❌ For legacy only |

### Component Naming Rules

```jsx
// ✅ MUST start with UPPERCASE
function MyComponent() { return <div />; }
function UserProfile() { return <div />; }

// ❌ Lowercase becomes HTML element (could match native element)
function mycomponent() { return <div />; }  // Renders as <div>

// React distinguishes by case:
// - Uppercase = custom component
// - Lowercase = HTML element
```

### Component Composition

The power of React comes from **composing** components:

```jsx
// Parent component
function App() {
  return (
    <div className="app">
      <Header />
      <MainContent>
        <Sidebar />
        <Article title="Getting Started">
          <p>Content here...</p>
        </Article>
      </MainContent>
      <Footer />
    </div>
  );
}

// Article component using children prop
function Article({ title, children }) {
  return (
    <article>
      <h2>{title}</h2>
      <div className="content">
        {children}
      </div>
    </article>
  );
}
```

### Component Best Practices

```jsx
// ✅ Keep components small and focused
// BAD: One massive component
function BadDashboard() {
  // 500 lines of code handling everything
}

// GOOD: Small, focused components
function Dashboard() {
  return (
    <div>
      <StatsPanel />
      <ChartsSection />
      <RecentActivity />
    </div>
  );
}

// ✅ Use meaningful names
// BAD: function Comp({ x, y, z }) 
// GOOD: function UserProfile({ userId, userName, userEmail })

// ✅ Destructure props for clarity
// BAD: function User({ props }) { return <p>{props.name}</p>; }
// GOOD: function User({ name, email }) { return <p>{name}</p>; }

// ✅ Return JSX directly (avoid unnecessary variables)
// BAD: const element = <p>Hello</p>; return element;
// GOOD: return <p>Hello</p>;
```

---

## 4. Props

### What are Props?

**Props** (short for "properties") are the mechanism for passing data from parent components to child components. They make components reusable and configurable.

```
┌─────────────────────────────────────────────────────────────────┐
│                    PROPS DATA FLOW                               │
│                                                                  │
│     ┌──────────────────────────────────────────────────┐        │
│     │                   PARENT COMPONENT                │        │
│     │                                                   │        │
│     │   const user = { name: "Alice", age: 25 };       │        │
│     │                                                   │        │
│     │   return <UserCard name="Alice" age={25} />      │        │
│     │                   │                               │        │
│     │                   │  Props passed DOWN            │        │
│     │                   ▼                               │        │
│     └──────────────────────────────────────────────────┘        │
│                         │                                        │
│                         ▼                                        │
│     ┌──────────────────────────────────────────────────┐        │
│     │                   CHILD COMPONENT                 │        │
│     │                                                   │        │
│     │   function UserCard({ name, age }) {             │        │
│     │     return (                                     │        │
│     │       <div>                                     │        │
│     │         <h1>{name}</h1>  {/* "Alice" */}       │        │
│     │         <p>{age}</p>       {/* 25 */}          │        │
│     │       </div>                                     │        │
│     │     );                                           │        │
│     │   }                                              │        │
│     └──────────────────────────────────────────────────┘        │
│                                                                  │
│     ⚠️ PROPS FLOW DOWNWARD ONLY (One-way data flow)             │
│     ⚠️ CHILDREN CANNOT MODIFY PROPS                              │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Key Properties of Props

| Property | Description |
|----------|-------------|
| **Read-only** | Props cannot be modified by the receiving component |
| **Passed from parent** | Only the parent can pass props to a child |
| **One-way data flow** | Data flows downward, actions flow upward |
| **Can be any value** | Strings, numbers, arrays, objects, functions, JSX |
| **children** | Special prop for content between component tags |

### Using Props

```jsx
// Parent component
function App() {
  const title = "My Blog Post";
  const views = 1234;
  const tags = ["react", "javascript", "web"];
  
  return (
    <BlogPost 
      title={title}
      views={views}
      tags={tags}
      isPublished={true}
    />
  );
}

// Child component - receiving props
function BlogPost({ title, views, tags, isPublished }) {
  return (
    <article>
      <h1>{title}</h1>
      <p>Views: {views.toLocaleString()}</p>
      <div className="tags">
        {tags.map(tag => <span key={tag}>{tag}</span>)}
      </div>
      {isPublished && <span className="badge">Published</span>}
    </article>
  );
}
```

### Default Props

```jsx
// Method 1: Default parameter values (RECOMMENDED)
function UserCard({ name = "Anonymous", role = "viewer" }) {
  return (
    <div>
      <p>Name: {name}</p>
      <p>Role: {role}</p>
    </div>
  );
}

// Method 2: defaultProps (older approach)
function UserCard({ name, role }) {
  return (
    <div>
      <p>Name: {name}</p>
      <p>Role: {role}</p>
    </div>
  );
}

UserCard.defaultProps = {
  name: "Anonymous",
  role: "viewer"
};

// Method 3: Default in destructuring
function UserCard({ name, role = "viewer", isActive = true }) {
  return <div>{name} - {role}</div>;
}
```

### PropTypes (Runtime Validation)

```jsx
import PropTypes from 'prop-types';

function UserCard({ name, age, email, isOnline }) {
  return (
    <div className={isOnline ? "online" : "offline"}>
      <h3>{name}</h3>
      <p>Age: {age}</p>
      <p>Email: {email}</p>
    </div>
  );
}

UserCard.propTypes = {
  name: PropTypes.string.isRequired,
  age: PropTypes.number,
  email: PropTypes.string,
  isOnline: PropTypes.bool,
  // More complex validators:
  hobbies: PropTypes.arrayOf(PropTypes.string),
  address: PropTypes.shape({
    city: PropTypes.string,
    country: PropTypes.string
  }),
  onClick: PropTypes.func,
  // Union types
  status: PropTypes.oneOf(['active', 'inactive', 'pending'])
};
```

### The `children` Prop

The `children` prop contains whatever is placed between the opening and closing tags of a component:

```jsx
// Parent using children
function Card({ title, children }) {
  return (
    <div className="card">
      <div className="card-header">
        <h2>{title}</h2>
      </div>
      <div className="card-body">
        {children}
      </div>
    </div>
  );
}

// Usage - content between tags becomes children
function App() {
  return (
    <Card title="Welcome">
      <p>This is the card content!</p>
      <button>Click me</button>
      <p>More content here...</p>
    </Card>
  );
}

// Wrapper pattern
function Modal({ children }) {
  return (
    <div className="modal-overlay">
      <div className="modal-content">
        {children}
      </div>
    </div>
  );
}

// Slot pattern - multiple children slots
function Layout({ header, main, footer }) {
  return (
    <div className="layout">
      <header>{header}</header>
      <main>{main}</main>
      <footer>{footer}</footer>
    </div>
  );
}

function App() {
  return (
    <Layout
      header={<Nav />}
      main={<Article />}
      footer={<Copyright />}
    />
  );
}
```

### Passing Functions as Props (Callbacks)

This is how child components communicate with parents:

```jsx
// Parent component
function CounterApp() {
  const [count, setCount] = useState(0);
  
  const handleIncrement = () => {
    setCount(prev => prev + 1);
  };
  
  const handleReset = () => {
    setCount(0);
  };
  
  return (
    <Counter 
      count={count}
      onIncrement={handleIncrement}
      onReset={handleReset}
    />
  );
}

// Child component
function Counter({ count, onIncrement, onReset }) {
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={onIncrement}>+1</button>
      <button onClick={onReset}>Reset</button>
    </div>
  );
}
```

### Prop Drilling and Solutions

**Prop drilling** = passing props through many levels of components:

```jsx
// ❌ PROBLEM: Props passed through every level
function App() {
  return <Level1 theme="dark" userId={123} />;
}

function Level1({ theme, userId }) {
  return <Level2 theme={theme} userId={userId} />;
}

function Level2({ theme, userId }) {
  return <Level3 theme={theme} userId={userId} />;
}

function Level3({ theme, userId }) {
  // Finally using the props!
  return <Profile theme={theme} userId={userId} />;
}
```

**Solutions:**
1. **Context API** - For theme, auth, locale data
2. **Redux/Zustand** - For complex global state
3. **Component Composition** - Pass JSX as children
4. **Custom Hooks** - Extract and share logic

```jsx
// ✅ SOLUTION 1: Context API
const ThemeContext = React.createContext();

function App() {
  return (
    <ThemeContext.Provider value="dark">
      <Level1 />
    </ThemeContext.Provider>
  );
}

function Level3() {
  const theme = useContext(ThemeContext);
  return <Profile theme={theme} />;
}

// ✅ SOLUTION 2: Component Composition
function App() {
  return (
    <Layout>
      <Profile userId={123} />
    </Layout>
  );
}

function Layout({ children }) {
  return <div className="layout">{children}</div>;
}
```

---

## 5. State

### What is State?

**State** is data that a component **owns and manages** internally. Unlike props (which come from parent), state is controlled by the component itself and can change over time.

```
┌─────────────────────────────────────────────────────────────────┐
│                     PROPS vs STATE                               │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│   ┌─────────────────────┐       ┌─────────────────────┐         │
│   │       PROPS         │       │       STATE         │         │
│   ├─────────────────────┤       ├─────────────────────┤         │
│   │ Passed from parent  │       │ Managed internally  │         │
│   │ Read-only          │       │ Can be changed      │         │
│   │ External data      │       │ Internal data       │         │
│   │ Configure behavior │       │ Manage behavior     │         │
│   │ Not in component   │       │ In component        │         │
│   │ Props ↓↓           │       │ State ↑↑            │         │
│   └─────────────────────┘       └─────────────────────┘         │
│                                                                  │
│   Think of props as "configuration" and state as "memory"        │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### useState Hook

The `useState` hook is the primary way to add state to functional components:

```jsx
import { useState } from 'react';

function Counter() {
  // Syntax: const [stateVariable, setStateFunction] = useState(initialValue)
  const [count, setCount] = useState(0);
  
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}
```

### useState Syntax Breakdown

```jsx
// Declaration
const [currentValue, updaterFunction] = useState(initialValue);

// Breaking it down:
const [count, setCount] = useState(0);
//     │       │         │        └── Initial value
//     │       │         └── Hook name
//     │       └── Setter function (convention: set + VariableName)
//     └── Current state value

// Common patterns:
const [isActive, setIsActive] = useState(false);
const [username, setUsername] = useState("");
const [user, setUser] = useState({ name: "", email: "" });
const [items, setItems] = useState([]);
const [loading, setLoading] = useState(true);
```

### Updating State

#### Basic Updates
```jsx
function BasicUpdates() {
  const [count, setCount] = useState(0);
  const [name, setName] = useState("");
  const [isEnabled, setIsEnabled] = useState(false);

  return (
    <div>
      {/* Number */}
      <button onClick={() => setCount(count + 1)}>+1</button>
      
      {/* String */}
      <input 
        value={name} 
        onChange={(e) => setName(e.target.value)} 
      />
      
      {/* Boolean */}
      <button onClick={() => setIsEnabled(!isEnabled)}>
        {isEnabled ? "ON" : "OFF"}
      </button>
    </div>
  );
}
```

#### Object State Updates
```jsx
function ObjectState() {
  const [form, setForm] = useState({
    username: "",
    email: "",
    age: 0
  });

  // ❌ WRONG - Don't mutate state directly
  const handleWrong = () => {
    form.username = "Alice";  // This won't trigger re-render!
  };

  // ✅ CORRECT - Create new object
  const handleUsernameChange = (e) => {
    setForm({
      ...form,  // Spread existing properties
      username: e.target.value  // Update specific property
    });
  };

  // ✅ Better: Use functional update with partial update
  const handleFieldChange = (field, value) => {
    setForm(prev => ({
      ...prev,
      [field]: value  // Dynamic property name
    }));
  };

  return (
    <form>
      <input 
        value={form.username}
        onChange={(e) => handleFieldChange('username', e.target.value)}
      />
      <input 
        value={form.email}
        onChange={(e) => handleFieldChange('email', e.target.value)}
      />
    </form>
  );
}
```

#### Array State Updates
```jsx
function ArrayState() {
  const [items, setItems] = useState(["apple", "banana"]);

  // Add item
  const addItem = (newItem) => {
    setItems([...items, newItem]);  // Spread + new item at end
    // OR: setItems(prev => [...prev, newItem]);
  };

  // Remove item
  const removeItem = (index) => {
    setItems(items.filter((_, i) => i !== index));
    // OR: setItems(prev => prev.filter((_, i) => i !== index));
  };

  // Update item
  const updateItem = (index, newValue) => {
    setItems(items.map((item, i) => 
      i === index ? newValue : item
    ));
  };

  return (
    <ul>
      {items.map((item, index) => (
        <li key={index}>
          {item}
          <button onClick={() => removeItem(index)}>X</button>
        </li>
      ))}
    </ul>
  );
}
```

### Functional Updates (When State Depends on Previous)

```jsx
function CounterWithPrevState() {
  const [count, setCount] = useState(0);

  // ❌ WRONG if called rapidly or in a loop
  const handleClickWrong = () => {
    setCount(count + 1);  // Uses stale count value
    setCount(count + 1);  // Still uses old count!
    setCount(count + 1);  // Still 0, not 3!
  };

  // ✅ CORRECT - Use functional update
  const handleClickCorrect = () => {
    setCount(prev => prev + 1);  // Always gets latest state
    setCount(prev => prev + 1);  // Gets updated value
    setCount(prev => prev + 1);  // Gets updated value = 3
  };

  return (
    <div>
      <p>{count}</p>
      <button onClick={handleClickCorrect}>+3</button>
    </div>
  );
}
```

### Lazy Initialization

For expensive computations, use lazy initialization to avoid running on every render:

```jsx
function ExpensiveComponent() {
  // ❌ WRONG - Computed on EVERY render
  const [data, setData] = useState(expensiveCalculation());

  // ✅ CORRECT - Function only runs on first render
  const [data, setData] = useState(() => expensiveCalculation());

  // ✅ ALSO CORRECT - With initial value (no function)
  const [count, setCount] = useState(0);  // Primitive = no lazy init needed
}

// Example of expensive calculation
function expensiveCalculation() {
  console.log("Running expensive calculation...");
  return Array.from({ length: 1000 }, (_, i) => i * 2);
}

function LazyInitDemo() {
  const [data, setData] = useState(() => {
    const saved = localStorage.getItem('myData');
    return saved ? JSON.parse(saved) : initialData;
  });
  
  // ...
}
```

### State Update Batching

React batches state updates for performance:

```jsx
function BatchingDemo() {
  const [x, setX] = useState(0);
  const [y, setY] = useState(0);

  const handleClick = () => {
    // These 3 setState calls result in ONE re-render
    setX(100);  // Queued
    setY(200);  // Queued
    setX(50);   // Queued, overwrites previous x value
    // React will batch these and re-render once
  };

  // ❌ Don't do this:
  setX(x + 1);
  setX(x + 1);  // Still adds 1, not 2! (same stale value)

  // ✅ Do this:
  setX(prev => prev + 1);
  setX(prev => prev + 1);  // Adds 2 correctly

  return <button onClick={handleClick}>Click</button>;
}
```

### When to Use State

```
┌─────────────────────────────────────────────────────────────────┐
│                   USE STATE WHEN:                               │
├─────────────────────────────────────────────────────────────────┤
│  ✅ Data changes over time (user input, timers, API responses)   │
│  ✅ Data affects what's rendered                                 │
│  ✅ Data should trigger re-render                               │
│  ✅ Data is component-local (not needed by many components)      │
│                                                                  │
│                   DON'T USE STATE WHEN:                          │
├─────────────────────────────────────────────────────────────────┤
│  ❌ Data never changes                                            │
│  ❌ Data comes from props (don't duplicate in state)              │
│  ❌ Data can be computed from existing state/props               │
│  ❌ Data is temporary (use ref instead)                          │
│  ❌ Data is for DOM manipulation (use ref instead)              │
└─────────────────────────────────────────────────────────────────┘
```

---

## 6. Event Handling

### React Events vs DOM Events

React uses **SyntheticEvent**, a cross-browser wrapper around native browser events. This ensures events work identically across all browsers.

```
┌─────────────────────────────────────────────────────────────────┐
│                    EVENT FLOW IN REACT                          │
│                                                                  │
│  User Action                                                     │
│      │                                                           │
│      ▼                                                           │
│  SyntheticEvent Created (React's wrapper)                        │
│      │                                                           │
│      ├──► Handles event (your handler)                           │
│      │                                                           │
│      └──► Event Pooled (React 16 and earlier)                    │
│              └──► Event nullified after handler                  │
│                      └──► Use event.persist() to retain         │
│                                                                  │
│  Note: React 17+ removed event pooling (no more .persist() needed)│
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Event Handler Syntax

```jsx
function EventDemo() {
  // ✅ camelCase event names
  // ✅ Pass function reference (not invocation)
  
  const handleClick = () => {
    console.log("Button clicked!");
  };

  const handleChange = (e) => {
    console.log(e.target.value);
  };

  const handleSubmit = (e) => {
    e.preventDefault();  // Prevent form submission
    console.log("Form submitted");
  };

  return (
    <div>
      {/* Click events */}
      <button onClick={handleClick}>Click Me</button>
      
      {/* Arrow function inline */}
      <button onClick={() => console.log("Inline click")}>Inline</button>
      
      {/* Form submit */}
      <form onSubmit={handleSubmit}>
        <input onChange={handleChange} />
        <button type="submit">Submit</button>
      </form>
    </div>
  );
}
```

### Common Event Types

| Event | When It Fires | Common Use |
|-------|--------------|------------|
| `onClick` | Element clicked | Buttons, links |
| `onChange` | Input value changes | Text inputs, selects |
| `onSubmit` | Form submitted | Form handling |
| `onFocus` | Element gains focus | Validation, UI feedback |
| `onBlur` | Element loses focus | Validation, saving |
| `onKeyDown` | Key pressed | Keyboard shortcuts |
| `onMouseEnter` | Mouse enters element | Hover effects |
| `onMouseLeave` | Mouse leaves element | Hover effects |
| `onScroll` | Element is scrolled | Infinite scroll, sticky headers |
| `onLoad` | Image/resource loaded | Loading states |
| `onError` | Resource fails to load | Error handling |
| `onDoubleClick` | Double click | Edit mode toggle |

### Event Object (SyntheticEvent)

```jsx
function EventObjectDemo() {
  const handleClick = (event) => {
    // Event properties (cross-browser)
    console.log(event.type);        // "click"
    console.log(event.target);       // The DOM element
    console.log(event.currentTarget);// Element with handler
    console.log(event.clientX);      // Mouse X position
    console.log(event.clientY);      // Mouse Y position
    
    // Prevent default behavior
    event.preventDefault();
    
    // Stop propagation (stop bubbling)
    event.stopPropagation();
    
    // Native event (for compatibility)
    console.log(event.nativeEvent);
  };

  const handleKeyDown = (event) => {
    console.log(event.key);          // "Enter", "a", "Shift"
    console.log(event.code);         // "Enter", "KeyA", "ShiftLeft"
    console.log(event.ctrlKey);      // Boolean
    console.log(event.shiftKey);      // Boolean
    
    if (event.key === "Enter") {
      console.log("Enter was pressed");
    }
  };

  return (
    <div>
      <button onClick={handleClick}>Click me</button>
      <input onKeyDown={handleKeyDown} />
    </div>
  );
}
```

### Accessing DOM Elements with Event

```jsx
function DOMAccessDemo() {
  const handleClick = (event) => {
    // Access DOM element through event
    const element = event.target;
    
    // Common manipulations
    element.style.color = "red";
    element.focus();
    element.classList.add("active");
    element.scrollIntoView();
    
    // Get input value
    if (event.target.tagName === "INPUT") {
      console.log(event.target.value);
    }
  };

  return (
    <div>
      <button onClick={handleClick}>Click me</button>
    </div>
  );
}
```

### Common Mistakes

```jsx
function CommonMistakes() {
  const [value, setValue] = useState("");

  // ❌ WRONG - Calling function directly
  <button onClick={handleClick()}>Click</button>
  // This calls handleClick IMMEDIATELY on render!

  // ✅ CORRECT - Pass function reference
  <button onClick={handleClick}>Click</button>

  // ✅ CORRECT - Pass arrow function
  <button onClick={() => handleClick()}>Click</button>

  // ❌ WRONG - Forgetting preventDefault for form
  const handleSubmit = (e) => {
    // Missing e.preventDefault() - form will reload page!
    console.log("Submitted");
  };

  // ✅ CORRECT
  const handleSubmit = (e) => {
    e.preventDefault();
    console.log("Submitted");
  };

  return <form onSubmit={handleSubmit}>...</form>;
}
```

### Class Component Event Binding

```jsx
class EventDemo extends Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
    
    // ❌ Traditional binding (must do this!)
    this.handleClick = this.handleClick.bind(this);
    
    // ✅ Better: Use arrow function in class field
    // this.handleClick = () => {}  // No binding needed
  }

  // ❌ This won't work without binding
  handleClick() {
    this.setState({ count: this.state.count + 1 });
  }

  // ✅ Arrow function - automatically bound
  handleIncrement = () => {
    this.setState(prev => ({ count: prev.count + 1 }));
  };

  render() {
    return (
      <div>
        {/* For methods defined with arrow, no .bind needed */}
        <button onClick={this.handleIncrement}>
          Count: {this.state.count}
        </button>
        
        {/* If not arrow, must bind */}
        <button onClick={this.handleClick}>
          Click (bound)
        </button>
      </div>
    );
  }
}
```

---

## 7. Conditional Rendering

### Overview

Conditional rendering in React means showing different UI based on certain conditions. In React, there's no `if/else` in JSX itself, so we use JavaScript expressions.

```
┌─────────────────────────────────────────────────────────────────┐
│                 CONDITIONAL RENDERING OPTIONS                    │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  1. if/else (outside JSX)    → When component changes structure │
│  2. Ternary operator (? :)   → Simple true/false choices        │
│  3. Logical AND (&&)         → Render or nothing                │
│  4. Logical OR (||)          → Default value pattern             │
│  5. Switch/case              → Multiple conditions               │
│  6. Early return             → Guard clauses                    │
│  7. IIFE                     → Complex logic inline              │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### 1. if/else Outside JSX

```jsx
function UserStatus({ isLoggedIn }) {
  // Define what to render based on condition
  if (isLoggedIn) {
    return <Dashboard />;
  } else {
    return <LoginPage />;
  }
}

// Or with multiple conditions
function Notification({ type }) {
  if (type === "success") {
    return <SuccessMessage />;
  }
  if (type === "error") {
    return <ErrorMessage />;
  }
  if (type === "warning") {
    return <WarningMessage />;
  }
  return <DefaultMessage />;
}
```

### 2. Ternary Operator

```jsx
function Greeting({ isLoggedIn }) {
  return (
    <div>
      {/* Simple ternary */}
      <h1>
        {isLoggedIn ? "Welcome back!" : "Please login"}
      </h1>
      
      {/* With elements */}
      {isLoggedIn ? (
        <button>Logout</button>
      ) : (
        <button>Login</button>
      )}
      
      {/* Nested ternary (use sparingly) */}
      <p>
        {isLoggedIn 
          ? user.isAdmin ? "Admin Panel" : "User Dashboard"
          : "Login to continue"
        }
      </p>
    </div>
  );
}
```

### 3. Logical AND (&&)

```jsx
function Notification({ messages, unreadCount }) {
  return (
    <div>
      {/* Render element if true, nothing if false */}
      {unreadCount > 0 && (
        <span className="badge">{unreadCount}</span>
      )}
      
      {/* Render list if array has items */}
      {messages.length > 0 && (
        <ul>
          {messages.map(msg => <li key={msg.id}>{msg.text}</li>)}
        </ul>
      )}
    </div>
  );
}
```

### ⚠️ Common Trap with Logical AND

```jsx
function TrapDemo({ count, items }) {
  return (
    <div>
      {/* ❌ DANGER: If count is 0, it renders "0" on screen! */}
      {count && <span>Count: {count}</span>}
      
      {/* ✅ SAFER: Ensure left side is boolean */}
      {count > 0 && <span>Count: {count}</span>}
      
      {/* ⚠️ Also a problem with arrays */}
      {items && <List items={items} />}  // If items is [], renders nothing (OK)
      // But if items is 0 or false, shows that value!
    </div>
  );
}
```

### 4. Logical OR (||) for Defaults

```jsx
function UserGreeting({ username, displayName }) {
  return (
    <div>
      {/* Show displayName or fallback to username, or "Guest" */}
      <h1>Hello, {displayName || username || "Guest"}!</h1>
      
      {/* ⚠️ But be careful with falsy values */}
      {/* This won't show 0: */}
      <p>Score: {score || 0}</p>  // If score is 0, shows "0" anyway
      
      {/* Better: ternary for numbers */}
      <p>Score: {score !== undefined ? score : 0}</p>
    </div>
  );
}
```

### 5. Switch/Case Pattern

```jsx
function StatusBadge({ status }) {
  // ❌ Don't use switch in JSX
  // ✅ Use switch in function body or helper
  
  // Helper function approach
  const getBadgeColor = (status) => {
    switch (status) {
      case "success":
        return "green";
      case "warning":
        return "yellow";
      case "error":
        return "red";
      default:
        return "gray";
    }
  };

  // Component approach
  const renderContent = () => {
    switch (status) {
      case "loading":
        return <Skeleton />;
      case "success":
        return <SuccessView />;
      case "error":
        return <ErrorView error={error} />;
      default:
        return <DefaultView />;
    }
  };

  return (
    <div className={`badge-${getBadgeColor(status)}`}>
      {renderContent()}
    </div>
  );
}
```

### 6. Early Returns (Guard Clauses)

```jsx
function Article({ user, article }) {
  // ❌ Nested conditionals
  function renderContent() {
    if (user) {
      if (article) {
        if (article.isPublished) {
          return <ArticleContent article={article} />;
        } else {
          return <DraftMessage />;
        }
      } else {
        return <NoArticleMessage />;
      }
    } else {
      return <LoginPrompt />;
    }
  }

  // ✅ Early returns (guard clauses)
  function renderContent() {
    if (!user) return <LoginPrompt />;
    if (!article) return <NoArticleMessage />;
    if (!article.isPublished) return <DraftMessage />;
    
    return <ArticleContent article={article} />;
  }
  
  // Or at component level
  if (!user) return <LoginPrompt />;
  if (!article) return <NoArticleMessage />;
  
  return <ArticleContent article={article} />;
}
```

### 7. IIFE (Immediately Invoked Function Expression)

```jsx
function ComplexConditional() {
  const [filter, setFilter] = useState("all");
  
  return (
    <div>
      {/* Complex inline logic */}
      {(() => {
        const items = allItems.filter(item => {
          if (filter === "all") return true;
          if (filter === "active") return item.active;
          if (filter === "completed") return item.completed;
          return false;
        });
        return items.map(item => <Item key={item.id} {...item} />);
      })()}
    </div>
  );
}
```

### Conditional Classes and Styles

```jsx
function DynamicStyling({ isActive, isDisabled }) {
  return (
    <div>
      {/* Conditional className */}
      <button 
        className={`btn ${
          isActive ? "btn-active" : "btn-inactive"
        } ${isDisabled ? "btn-disabled" : ""}`}
      >
        Click me
      </button>
      
      {/* Array join approach */}
      <div className={[
        "card",
        isActive && "card-active",
        isDisabled && "card-disabled"
      ].filter(Boolean).join(" ")}>
        Card content
      </div>
      
      {/* Conditional inline styles */}
      <p style={{
        color: isActive ? "green" : "gray",
        fontSize: 16,
        opacity: isDisabled ? 0.5 : 1
      }}>
        Text
      </p>
    </div>
  );
}
```

---

## 8. Lists and Keys

### Rendering Lists

Use the `map()` method to render arrays of elements:

```jsx
function FruitList() {
  const fruits = ["Apple", "Banana", "Orange", "Mango"];

  return (
    <ul>
      {fruits.map(fruit => (
        <li>{fruit}</li>
      ))}
    </ul>
  );
}

// With objects
function UserList() {
  const users = [
    { id: 1, name: "Alice", email: "alice@example.com" },
    { id: 2, name: "Bob", email: "bob@example.com" },
    { id: 3, name: "Charlie", email: "charlie@example.com" }
  ];

  return (
    <div>
      {users.map(user => (
        <div key={user.id}>
          <h3>{user.name}</h3>
          <p>{user.email}</p>
        </div>
      ))}
    </div>
  );
}
```

### Why Keys are Essential

```
┌─────────────────────────────────────────────────────────────────┐
│                    WHY KEYS MATTER                               │
│                                                                  │
│  WITHOUT KEYS (React confused):                                  │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │ Initial:   [A, B, C]           React thinks:            │    │
│  │             ↓                                            │    │
│  │ Updated:   [A, B, C, D]        "C became D, added C"?   │    │
│  │                                (Has to re-render all)    │    │
│  └─────────────────────────────────────────────────────────┘    │
│                                                                  │
│  WITH KEYS (React knows exactly what changed):                  │
│  ┌─────────────────────────────────────────────────────────┐    │
│  │ Initial:   [{id:1,A}, {id:2,B}, {id:3,C}]              │    │
│  │             ↓                                             │    │
│  │ Updated:   [{id:1,A}, {id:2,B}, {id:3,C}, {id:4,D}]     │    │
│  │                                React sees: "A,B,C same,  │    │
│  │                                New D added at end"       │    │
│  └─────────────────────────────────────────────────────────┘    │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Key Rules

```jsx
// ✅ CORRECT: Use unique, stable IDs
const items = [
  { id: 1, name: "Alice" },
  { id: 2, name: "Bob" }
];

{items.map(item => (
  <li key={item.id}>{item.name}</li>
))}

// ✅ If no unique ID, create one (last resort)
{items.map((item, index) => (
  <li key={index}>...</li>  // ⚠️ Only if list never changes!
))}

// ❌ WRONG: Duplicate keys
const items = [
  { id: 1, name: "Alice" },
  { id: 1, name: "Bob" }  // Duplicate! Problems guaranteed.
];

// ❌ WRONG: Dynamic index as key
{items.map((item, index) => (
  <li key={index}>...</li>  // ⚠️ Problem if list can be reordered/filtered
))}
```

### Key Must Be Among Siblings

```jsx
function KeyScope() {
  return (
    <div>
      {/* Each list's keys are scoped to that list */}
      <ul>
        {users.map(u => <li key={u.id}>{u.name}</li>)}
      </ul>
      
      <ul>
        {admins.map(a => <li key={a.id}>{a.name}</li>)}
      </ul>
      
      {/* Keys don't need to be globally unique */}
      {/* But they MUST be unique among siblings */}
    </div>
  );
}
```

### Nested Lists

```jsx
function NestedList({ categories }) {
  return (
    <div>
      {categories.map(category => (
        <div key={category.id} className="category">
          <h2>{category.name}</h2>
          <ul>
            {category.items.map(item => (
              <li key={item.id}>{item.name}</li>
            ))}
          </ul>
        </div>
      ))}
    </div>
  );
}
```

### Keys with Components

```jsx
function PostList({ posts }) {
  return (
    <div>
      {posts.map(post => (
        <PostCard 
          key={post.id}  // Key goes on root element of mapping
          post={post}
        />
      ))}
    </div>
  );
}

// If using Fragment, key goes on Fragment
function MixedList({ items, users }) {
  return (
    <>
      {items.map(item => (
        <React.Fragment key={item.id}>
          <div>{item.title}</div>
          <hr />
        </React.Fragment>
      ))}
      {users.map(user => (
        <UserBadge key={user.id} user={user} />
      ))}
    </>
  );
}

// Using shorthand Fragment (needs explicit key)
function ShortFragmentList({ items }) {
  return items.map(item => (
    <>
      <div key={item.id}>{item.title}</div>  // ❌ No key attribute
      <div key={`desc-${item.id}`}>{item.desc}</div>  // Workaround
    </>
  ));
}

// ✅ CORRECT
function FragmentList({ items }) {
  return items.map(item => (
    <React.Fragment key={item.id}>
      <div>{item.title}</div>
      <div>{item.desc}</div>
    </React.Fragment>
  ));
}
```

### Common List Patterns

```jsx
// Filter and map
function FilteredList({ items, filter }) {
  return (
    <ul>
      {items
        .filter(item => item.type === filter)
        .map(item => (
          <li key={item.id}>{item.name}</li>
        ))}
    </ul>
  );
}

// Map with conditional rendering
function StatusList({ items }) {
  return (
    <ul>
      {items.map(item => (
        <li key={item.id}>
          {item.isActive ? (
            <ActiveItem {...item} />
          ) : (
            <InactiveItem {...item} />
          )}
        </li>
      ))}
    </ul>
  );
}

// Sort, map, and render
function SortedUserList({ users, sortBy }) {
  const sortedUsers = [...users].sort((a, b) => {
    if (sortBy === "name") return a.name.localeCompare(b.name);
    if (sortBy === "age") return a.age - b.age;
    return 0;
  });

  return (
    <ul>
      {sortedUsers.map(user => (
        <li key={user.id}>{user.name} - {user.age}</li>
      ))}
    </ul>
  );
}
```

---

## 9. Forms

### Controlled vs Uncontrolled Components

```
┌─────────────────────────────────────────────────────────────────┐
│               CONTROLLED vs UNCONTROLLED                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  CONTROLLED                     UNCONTROLLED                    │
│  ┌───────────────────┐         ┌───────────────────┐          │
│  │   React State     │         │   DOM State       │          │
│  │       │           │         │       │           │          │
│  │   value={state}   │         │   useRef()       │          │
│  │       │           │         │       │           │          │
│  │   onChange         │         │   ref.current    │          │
│  │       │           │         │       │           │          │
│  │   State Updates    │         │   Read on submit │          │
│  └───────────────────┘         └───────────────────┘          │
│                                                                  │
│  ✅ Validate as you type           ✅ Less code                 │
│  ✅ Disable button until valid     ✅ File inputs               │
│  ✅ Transform/format input         ✅ Quick & dirty forms       │
│  ✅ Instant feedback               ❌ Can't validate inline      │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Controlled Components

```jsx
function ControlledForm() {
  const [name, setName] = useState("");
  const [email, setEmail] = useState("");
  const [country, setCountry] = useState("usa");

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log({ name, email, country });
  };

  return (
    <form onSubmit={handleSubmit}>
      {/* Text input */}
      <div>
        <label htmlFor="name">Name:</label>
        <input
          id="name"
          type="text"
          value={name}
          onChange={(e) => setName(e.target.value)}
        />
      </div>

      {/* Email input */}
      <div>
        <label htmlFor="email">Email:</label>
        <input
          id="email"
          type="email"
          value={email}
          onChange={(e) => setEmail(e.target.value)}
        />
      </div>

      {/* Select */}
      <div>
        <label htmlFor="country">Country:</label>
        <select
          id="country"
          value={country}
          onChange={(e) => setCountry(e.target.value)}
        >
          <option value="usa">United States</option>
          <option value="canada">Canada</option>
          <option value="uk">United Kingdom</option>
        </select>
      </div>

      <button type="submit">Submit</button>
    </form>
  );
}
```

### Single Handler for Multiple Inputs

```jsx
function UnifiedForm() {
  const [form, setForm] = useState({
    username: "",
    email: "",
    password: "",
    bio: "",
    country: "usa",
    agree: false
  });

  const handleChange = (e) => {
    const { name, value, type, checked } = e.target;
    
    setForm(prev => ({
      ...prev,
      [name]: type === "checkbox" ? checked : value
    }));
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log(form);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        name="username"
        value={form.username}
        onChange={handleChange}
        placeholder="Username"
      />
      
      <input
        name="email"
        type="email"
        value={form.email}
        onChange={handleChange}
        placeholder="Email"
      />
      
      <textarea
        name="bio"
        value={form.bio}
        onChange={handleChange}
        placeholder="Bio"
      />
      
      <select name="country" value={form.country} onChange={handleChange}>
        <option value="usa">USA</option>
        <option value="uk">UK</option>
      </select>
      
      <label>
        <input
          name="agree"
          type="checkbox"
          checked={form.agree}
          onChange={handleChange}
        />
        I agree to terms
      </label>
      
      <button type="submit">Submit</button>
    </form>
  );
}
```

### Checkbox and Radio Inputs

```jsx
function OptionsForm() {
  const [selectedOption, setSelectedOption] = useState("option1");
  const [interests, setInterests] = useState({
    javascript: true,
    python: false,
    rust: false
  });

  // Radio handling
  const handleRadioChange = (e) => {
    setSelectedOption(e.target.value);
  };

  // Checkbox handling
  const handleCheckboxChange = (e) => {
    setInterests(prev => ({
      ...prev,
      [e.target.name]: e.target.checked
    }));
  };

  return (
    <form>
      {/* Radio buttons */}
      <div>
        <label>
          <input
            type="radio"
            name="plan"
            value="free"
            checked={selectedOption === "free"}
            onChange={handleRadioChange}
          />
          Free Plan
        </label>
        <label>
          <input
            type="radio"
            name="plan"
            value="pro"
            checked={selectedOption === "pro"}
            onChange={handleRadioChange}
          />
          Pro Plan
        </label>
      </div>

      {/* Checkboxes */}
      <div>
        <label>
          <input
            type="checkbox"
            name="javascript"
            checked={interests.javascript}
            onChange={handleCheckboxChange}
          />
          JavaScript
        </label>
        <label>
          <input
            type="checkbox"
            name="python"
            checked={interests.python}
            onChange={handleCheckboxChange}
          />
          Python
        </label>
      </div>
    </form>
  );
}
```

### Textarea in React

```jsx
function TextareaDemo() {
  const [description, setDescription] = useState("");

  return (
    <div>
      {/* In React, textarea uses `value`, not children */}
      <textarea
        value={description}
        onChange={(e) => setDescription(e.target.value)}
        placeholder="Enter description..."
        rows={4}
        maxLength={200}
      />
      <p>{description.length}/200 characters</p>
    </div>
  );
}
```

### Select in React

```jsx
function SelectDemo() {
  const [framework, setFramework] = useState("react");

  return (
    <div>
      {/* In React, select uses `value` on the select element */}
      <select
        value={framework}
        onChange={(e) => setFramework(e.target.value)}
      >
        <option value="react">React</option>
        <option value="vue">Vue</option>
        <option value="angular">Angular</option>
        <option value="svelte">Svelte</option>
      </select>

      {/* Multi-select */}
      <select
        multiple
        value={["react", "vue"]}
        onChange={(e) => {
          const options = Array.from(e.target.selectedOptions);
          const values = options.map(opt => opt.value);
          // Handle multi-select
        }}
      >
        <option value="react">React</option>
        <option value="vue">Vue</option>
        <option value="angular">Angular</option>
      </select>
    </div>
  );
}
```

### Uncontrolled Components (refs)

```jsx
import { useRef } from 'react';

function UncontrolledForm() {
  const nameRef = useRef(null);
  const fileRef = useRef(null);

  const handleSubmit = (e) => {
    e.preventDefault();
    
    // Access values through refs
    console.log("Name:", nameRef.current.value);
    console.log("File:", fileRef.current.files[0]);
  };

  return (
    <form onSubmit={handleSubmit}>
      {/* Use defaultValue for uncontrolled */}
      <input
        ref={nameRef}
        type="text"
        defaultValue="John Doe"
      />

      {/* File inputs MUST be uncontrolled */}
      <input
        ref={fileRef}
        type="file"
      />

      <button type="submit">Submit</button>
    </form>
  );
}
```

### Form Validation

```jsx
function ValidatedForm() {
  const [form, setForm] = useState({
    email: "",
    password: "",
    confirmPassword: ""
  });
  const [errors, setErrors] = useState({});
  const [touched, setTouched] = useState({});

  const validate = () => {
    const newErrors = {};
    
    if (!form.email) {
      newErrors.email = "Email is required";
    } else if (!/\S+@\S+\.\S+/.test(form.email)) {
      newErrors.email = "Email is invalid";
    }
    
    if (!form.password) {
      newErrors.password = "Password is required";
    } else if (form.password.length < 8) {
      newErrors.password = "Password must be 8+ characters";
    }
    
    if (form.password !== form.confirmPassword) {
      newErrors.confirmPassword = "Passwords don't match";
    }
    
    return newErrors;
  };

  const handleBlur = (e) => {
    setTouched(prev => ({ ...prev, [e.target.name]: true }));
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    const validationErrors = validate();
    setErrors(validationErrors);
    
    if (Object.keys(validationErrors).length === 0) {
      // Submit form
      console.log("Form submitted:", form);
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <input
          name="email"
          type="email"
          value={form.email}
          onChange={handleChange}
          onBlur={handleBlur}
        />
        {touched.email && errors.email && (
          <span className="error">{errors.email}</span>
        )}
      </div>
      {/* ... */}
    </form>
  );
}
```

---

## 10. Thinking in React

### The Process

```
┌─────────────────────────────────────────────────────────────────┐
│                 THINKING IN REACT - STEP BY STEP                 │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  STEP 1: BREAK THE UI INTO A COMPONENT HIERARCHY                  │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │  App                                                       │  │
│  │  ├── Header (Logo, Nav)                                   │  │
│  │  ├── Sidebar                                               │  │
│  │  └── Feed                                                  │  │
│  │      ├── SearchBar                                         │  │
│  │      └── PostList                                          │  │
│  │          └── PostCard (repeated)                           │  │
│  │              ├── PostHeader (avatar, name, time)           │  │
│  │              ├── PostContent (text, image)                │  │
│  │              └── PostActions (like, comment, share)       │  │
│  └───────────────────────────────────────────────────────────┘  │
│                                                                  │
│  STEP 2: BUILD A STATIC VERSION (no state yet)                   │
│  - Render all components using props                              │
│  - No interactivity needed yet                                   │
│                                                                  │
│  STEP 3: IDENTIFY THE MINIMAL STATE UI NEEDS                     │
│  - What data changes and causes UI updates?                      │
│  - Keep state minimal and local                                  │
│                                                                  │
│  STEP 4: PLACE STATE AND IDENTIFY THE OWNER                      │
│  - Where does this state live?                                   │
│  - Which component should own it?                                │
│  - Follow the "lift state up" pattern                            │
│                                                                  │
│  STEP 5: ADD INVERSE DATA FLOW (child → parent)                  │
│  - Pass callback functions as props                              │
│  - Child components trigger state changes                        │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Example: Building a Searchable Product Table

```jsx
// STEP 1: Component Hierarchy
// App
//   ├── SearchBar
//   └── ProductTable
//       └── ProductCategoryRow
//       └── ProductRow

// STEP 2 & 3: Build static, then identify state needs
// State needed: searchText, showOnlyStocked

// STEP 4: Where does state live? App component (parent of both)

// STEP 5: Implement with data flow

const PRODUCTS = [
  { category: "Fruits", price: "$1", stocked: true, name: "Apple" },
  { category: "Fruits", price: "$3", stocked: false, name: "Dragonfruit" },
  { category: "Vegetables", price: "$2", stocked: true, name: "Spinach" },
  // ... more products
];

function App() {
  const [searchText, setSearchText] = useState("");
  const [showStocked, setShowStocked] = useState(false);

  return (
    <div>
      <SearchBar 
        searchText={searchText}
        onSearchChange={setSearchText}
        showStocked={showStocked}
        onStockedChange={setShowStocked}
      />
      <ProductTable 
        products={PRODUCTS}
        filterText={searchText}
        showStocked={showStocked}
      />
    </div>
  );
}

function SearchBar({ searchText, onSearchChange, showStocked, onStockedChange }) {
  return (
    <form>
      <input
        type="text"
        placeholder="Search..."
        value={searchText}
        onChange={(e) => onSearchChange(e.target.value)}
      />
      <label>
        <input
          type="checkbox"
          checked={showStocked}
          onChange={(e) => onStockedChange(e.target.checked)}
        />
        Only show stocked products
      </label>
    </form>
  );
}

function ProductTable({ products, filterText, showStocked }) {
  const rows = [];
  let lastCategory = null;

  products.forEach(product => {
    if (product.name.indexOf(filterText) === -1) return;
    if (showStocked && !product.stocked) return;

    if (product.category !== lastCategory) {
      rows.push(
        <ProductCategoryRow 
          key={product.category} 
          category={product.category} 
        />
      );
    }
    rows.push(<ProductRow key={product.name} product={product} />);
    lastCategory = product.category;
  });

  return (
    <table>
      <thead>
        <tr>
          <th>Name</th>
          <th>Price</th>
        </tr>
      </thead>
      <tbody>{rows}</tbody>
    </table>
  );
}
```

### Single Responsibility Principle

Each component should ideally do one thing:

```jsx
// ❌ BAD: Component doing too much
function UserDashboard() {
  const [user, setUser] = useState(null);
  const [posts, setPosts] = useState([]);
  const [notifications, setNotifications] = useState([]);
  const [loading, setLoading] = useState(true);
  // 100 more lines of various state and logic
}

// ✅ GOOD: Small, focused components
function UserDashboard({ userId }) {
  return (
    <div>
      <UserProfile userId={userId} />
      <UserPosts userId={userId} />
      <Notifications userId={userId} />
    </div>
  );
}

function UserProfile({ userId }) {
  const { user, loading } = useUser(userId);
  if (loading) return <Skeleton />;
  return <div>{user.name}</div>;
}
```

---

## Quick Reference Cheat Sheet

### JSX
```jsx
// Root element - must wrap in div or Fragment
return (
  <>
    <h1>Title</h1>
    <p>Content</p>
  </>
);

// Expressions in JSX
{2 + 2}           // 4
{variable}        // value
{fn()}            // return value
{condition && <A />}  // conditional

// Attributes
className, htmlFor, onClick, onChange
style={{ color: "red" }}
```

### Props
```jsx
// Receive props
function Comp({ name, age = 25 }) { }

// Pass props
<Comp name="Alice" age={30} />

// children prop
<Comp>Content here</Comp>  // Access via {children}
```

### State
```jsx
// Declaration
const [count, setCount] = useState(0);

// Update
setCount(count + 1);
setCount(prev => prev + 1);

// Object state - ALWAYS spread!
setUser(prev => ({ ...prev, name: "Bob" }));
```

### Events
```jsx
onClick={handleClick}           // Pass function, not invocation
onChange={(e) => setValue(e.target.value)}
onSubmit={(e) => { e.preventDefault(); }}
```

### Lists
```jsx
{items.map(item => (
  <li key={item.id}>{item.name}</li>
))}
```

---

## Next Steps

Once you've mastered these basics, proceed to:
- **[React_Intermediate.md](React_Intermediate.md)** - Deep dive into Hooks, Context, Router, and State Management
- **[React_Advanced.md](React_Advanced.md)** - Performance optimization, testing, SSR, and architecture patterns
- **[React_Interview_QA.md](React_Interview_QA.md)** - Common interview questions with detailed answers
