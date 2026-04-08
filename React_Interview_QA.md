# React Interview Questions & Answers

> Comprehensive guide for React interviews, vivas, and technical discussions. Questions range from beginner to expert level.

---

## Table of Contents
1. [Fundamentals](#1-fundamentals)
2. [Components & Props](#2-components--props)
3. [State & Lifecycle](#3-state--lifecycle)
4. [Hooks Deep Dive](#4-hooks-deep-dive)
5. [Context & State Management](#5-context--state-management)
6. [Performance Optimization](#6-performance-optimization)
7. [React Router](#7-react-router)
8. [Testing & Debugging](#8-testing--debugging)
9. [Advanced Concepts](#9-advanced-concepts)
10. [System Design Questions](#10-system-design-questions)

---

## 1. Fundamentals

### Q1: What is React and why use it?

**Answer:**
React is a JavaScript library for building user interfaces, developed by Facebook. Key reasons to use React:

1. **Component-based architecture**: Build encapsulated components that manage their own state
2. **Declarative**: Describe what the UI should look like; React handles updates efficiently
3. **Virtual DOM**: Lightweight representation of the real DOM; React calculates minimal changes needed
4. **Unidirectional data flow**: Data flows down from parent to child; actions flow up
5. **Ecosystem**: Rich ecosystem with React Router, Redux, React Native, etc.
6. **Developer experience**: Great tooling (React DevTools), hot reloading, strong community

---

### Q2: What is JSX and why use it?

**Answer:**
JSX is a syntax extension for JavaScript that allows you to write HTML-like code in your JavaScript files.

**Why JSX:**
- More intuitive than `React.createElement()`
- Shows the exact UI structure
- Prevents XSS attacks by escaping values
- Familiar syntax for web developers

**Example:**
```jsx
// JSX
const element = <h1 className="greeting">Hello, {name}!</h1>;

// Compiles to:
const element = React.createElement('h1', { className: 'greeting' }, 'Hello, ', name, '!');
```

**Rules:**
- Must return ONE root element (or use Fragment `<>`)
- Use `className` instead of `class`
- Use `htmlFor` instead of `for`
- Expressions in `{}`, conditions using ternary/logical operators

---

### Q3: What is the Virtual DOM and how does it work?

**Answer:**
The Virtual DOM is a lightweight JavaScript representation of the real DOM.

**How it works:**
1. **State change** triggers new Virtual DOM tree
2. **Diffing algorithm** compares new tree with previous
3. **Minimal updates** calculated (what changed?)
4. **Batch updates** applied to real DOM efficiently

**Why it's fast:**
- Direct DOM manipulation is expensive (repaints/reflows)
- JavaScript operations are cheap
- React batches updates
- Diffing minimizes operations

```
State Change → New VDOM Tree → Diff with Old → Calculate Changes → Update DOM
```

---

### Q4: What is the difference between Real DOM and Virtual DOM?

| Aspect | Real DOM | Virtual DOM |
|--------|----------|-------------|
| Updates | Direct, slow | Indirect, fast |
| Memory | High usage | Low usage |
| Update mechanism | Creates new DOM | Creates new VDOM |
| Abstraction | None | Complete |
| Updates | Too many (unnecessary) | Only necessary |
| Speed | Slower | Faster |
| JavaScript cost | High | Low |

---

### Q5: What are the limitations of React?

**Answer:**
1. **Not a full framework**: Missing routing, HTTP client, etc. (needs additional libraries)
2. **Learning Curve**: JSX, hooks, and concepts like state management take time
3. **Boilerplate**: Some patterns require more code than simpler frameworks
4. **SEO**: Client-side rendering can be challenging for SEO (mitigated by SSR)
5. **Bundle Size**: React library adds ~40KB to bundle
6. **Frequent Updates**: Breaking changes in new versions require adaptation

---

## 2. Components & Props

### Q6: What is the difference between elements and components?

**Answer:**

**React Elements:**
- Smallest building blocks
- Plain objects describing what to render
- Immutable (cannot change)
- Created by `React.createElement()` or JSX

```jsx
const element = <h1>Hello</h1>;  // React element
```

**React Components:**
- Functions or classes that return elements
- Can accept inputs (props)
- Can maintain internal state
- Reusable and composable

```jsx
function Greeting({ name }) {  // React component
  return <h1>Hello, {name}</h1>;
}
```

---

### Q7: What are functional components vs class components?

**Answer:**

| Feature | Functional | Class |
|---------|-----------|-------|
| **Definition** | JavaScript function | ES6 class extending Component |
| **State** | useState hook | this.state + this.setState |
| **Lifecycle** | useEffect hook | Lifecycle methods |
| **this keyword** | Not used | Required throughout |
| **Boilerplate** | Less | More |
| **Performance** | Slightly faster | Slightly heavier |
| **Error Boundaries** | Not supported | Supported |
| **Code style** | Modern, hook-based | Legacy |

**Functional Component Example:**
```jsx
function Welcome({ name }) {
  return <h1>Hello, {name}</h1>;
}
```

**Class Component Example:**
```jsx
class Welcome extends Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

**Recommendation:** Use functional components with hooks for all new code.

---

### Q8: What are props in React?

**Answer:**
Props (properties) are the mechanism for passing data from parent to child components.

**Key characteristics:**
- **Read-only**: Components must never modify their props
- **One-way data flow**: Parent → Child only
- **Can be any value**: Strings, numbers, arrays, objects, functions, JSX

```jsx
// Passing props
<UserCard name="Alice" age={25} isActive={true} />

// Receiving props
function UserCard({ name, age, isActive }) {
  return (
    <div className={isActive ? 'active' : 'inactive'}>
      <h2>{name}</h2>
      <p>Age: {age}</p>
    </div>
  );
}
```

**Special prop - children:**
```jsx
function Card({ children }) {
  return <div className="card">{children}</div>;
}

<Card>
  <h2>Title</h2>
  <p>Content</p>
</Card>
```

---

### Q9: What is prop drilling and how to avoid it?

**Answer:**
Prop drilling is passing props through multiple levels of components that don't need them, just to reach a deep component that does.

**Problem:**
```jsx
<App theme="dark" userId={123} />           // App has theme, userId
  <Layout theme="dark" userId={123} />      // Layout passes through
    <Sidebar theme="dark" userId={123} />   // Sidebar passes through
      <UserProfile theme="dark" userId={123} />  // Finally uses them!
```

**Solutions:**

1. **Context API** (for theme, auth, locale):
```jsx
const ThemeContext = createContext();
<ThemeContext.Provider value="dark">
  <App />
</ThemeContext.Provider>

// Deep component accesses directly
const { theme } = useContext(ThemeContext);
```

2. **Component Composition** (pass JSX as children):
```jsx
<Layout>
  <UserProfile userId={123} />
</Layout>
```

3. **State Management** (Redux, Zustand):
```jsx
const { userId } = useSelector(state => state.user);
```

4. **Custom Hooks** (for logic sharing):
```jsx
const userId = useUserId();  // Hook provides the value
```

---

### Q10: Can props have default values? How?

**Answer:**
Yes, there are multiple ways to set default props:

**1. Destructuring with defaults (Recommended):**
```jsx
function Button({ label = 'Submit', variant = 'primary' }) {
  return <button className={variant}>{label}</button>;
}
```

**2. defaultProps (older approach):**
```jsx
function Button({ label, variant }) {
  return <button className={variant}>{label}</button>;
}

Button.defaultProps = {
  label: 'Submit',
  variant: 'primary'
};
```

**3. For PropTypes validation:**
```jsx
Component.propTypes = {
  name: PropTypes.string,
  count: PropTypes.number.isRequired
};
```

---

## 3. State & Lifecycle

### Q11: What is state in React?

**Answer:**
State is data that a component **owns and manages** internally. Unlike props, state is controlled by the component itself and can change over time.

**Key characteristics:**
- **Mutable**: Can be changed by the component
- **Private**: Managed exclusively by the component
- **Triggers re-render**: When state changes, component re-renders
- **Asynchronous**: State updates are batched and async

```jsx
function Counter() {
  const [count, setCount] = useState(0);  // count is state
  
  return (
    <button onClick={() => setCount(count + 1)}>
      Clicked {count} times
    </button>
  );
}
```

**When to use state:**
- Data that changes from user interactions
- Data from API responses
- Data that affects rendering
- Data that should trigger updates

**When NOT to use state:**
- Data that never changes (use constants)
- Data that comes from props (don't duplicate)
- Data that can be computed from existing state

---

### Q12: Why should state not be mutated directly?

**Answer:**
Directly mutating state bypasses React's change detection mechanism, preventing re-renders.

**Problem with direct mutation:**
```jsx
// ❌ WRONG - Mutates state directly
function BadCounter() {
  const [count, setCount] = useState(0);
  
  const handleClick = () => {
    count = count + 1;  // Mutates state directly
    setCount(count);    // React sees same reference, no re-render!
  };
  
  return <button onClick={handleClick}>{count}</button>;
}

// ❌ WRONG - Mutating array
const [items, setItems] = useState(['a', 'b']);
items.push('c');  // Direct mutation - won't cause re-render!
setItems(items);  // Same reference, no re-render!
```

**Correct approach - create new reference:**
```jsx
// ✅ CORRECT - Create new state
const [count, setCount] = useState(0);
setCount(count + 1);  // New number value

// ✅ CORRECT - Create new array
setItems([...items, 'c']);  // New array reference

// ✅ CORRECT - Create new object
setUser({ ...user, name: 'Bob' });  // New object reference
```

**Why immutability matters:**
1. React uses reference equality to detect changes
2. Enables performance optimizations (React.memo, useMemo)
3. Enables time-travel debugging (Redux DevTools)
4. Prevents bugs from stale data

---

### Q13: How does useState work internally?

**Answer:**
`useState` is implemented as a linked list of "hook objects" stored on the component instance.

**How it works:**
1. React stores hooks in an array per component
2. Each `useState` call gets the next hook in the array
3. The hook object contains: `{ state, queue }`
4. `setState` adds an action to the queue
5. React processes the queue on next render

```
Hook #1: { state: count, queue: [setCount actions] }
Hook #2: { state: name, queue: [setName actions] }
Hook #3: { state: items, queue: [setItems actions] }
```

**Why Rules of Hooks exist:**
- Hooks rely on consistent call order
- Changing order would mix up which hook gets which state
- This is why hooks can't be in conditions or loops

---

### Q14: What is the difference between state and props?

| Aspect | Props | State |
|--------|-------|-------|
| **Who manages** | Parent component | Component itself |
| **Mutability** | Immutable (read-only) | Mutable via setState |
| **Changes trigger render** | Yes (when parent passes new props) | Yes |
| **Default values** | Can have defaults | Initial value only |
| **Purpose** | Configure child components | Hold dynamic data |
| **Data flow** | Parent → Child | Internal |

---

### Q15: Explain the component lifecycle in class components.

**Answer:**

**Three phases:**

**1. Mounting (Birth)**
- `constructor()` - Initialize state, bind methods
- `render()` - Return JSX
- `componentDidMount()` - Run after first render (API calls, subscriptions)

**2. Updating (Growth)**
- `render()` - Re-render with new state/props
- `componentDidUpdate()` - Run after updates (side effects)

**3. Unmounting (Death)**
- `componentWillUnmount()` - Cleanup (timers, listeners, subscriptions)

```jsx
class MyComponent extends Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
    this.handleClick = this.handleClick.bind(this);  // Bind once
  }

  componentDidMount() {
    console.log('Component mounted');
    this.fetchData();
  }

  componentDidUpdate(prevProps, prevState) {
    if (prevProps.id !== this.props.id) {
      this.fetchData();
    }
  }

  componentWillUnmount() {
    console.log('Component will unmount');
    clearInterval(this.timer);
  }

  render() {
    return <div>{this.state.count}</div>;
  }
}
```

**Modern approach (Hooks):**
```jsx
function MyComponent({ id }) {
  useEffect(() => {
    fetchData();
    return () => cleanup();
  }, [id]);
  
  return <div />;
}
```

---

## 4. Hooks Deep Dive

### Q16: What are the rules of hooks?

**Answer:**
Two fundamental rules:

**Rule 1: Only call hooks at the top level**
- Don't call hooks inside loops (`for`, `while`)
- Don't call hooks inside conditions (`if`)
- Don't call hooks inside nested functions
- Always call hooks at the top level of your component

**Why:** React relies on hook call order to associate state with each hook.

**Rule 2: Only call hooks from React functions**
- Call hooks from functional components
- Call hooks from custom hooks
- Don't call hooks from regular JavaScript functions

```jsx
// ❌ WRONG
if (isLoggedIn) {
  useEffect(() => {});  // Violates Rule 1
}

// ✅ CORRECT
useEffect(() => {
  if (isLoggedIn) {}  // Logic inside, not hook
}, [isLoggedIn]);
```

---

### Q17: Explain useEffect in detail.

**Answer:**
`useEffect` performs side effects in functional components after render.

**Syntax:**
```jsx
useEffect(() => {
  // Effect logic
  return () => {
    // Cleanup (optional)
  };
}, [dependencies]);
```

**Dependency array variations:**

```jsx
// 1. No array - runs EVERY render
useEffect(() => {
  console.log('Every render');
});

// 2. Empty array - runs ONCE on mount
useEffect(() => {
  console.log('Once on mount');
  return () => cleanup();
}, []);

// 3. With deps - runs when deps change
useEffect(() => {
  console.log('Runs when count changes');
}, [count]);
```

**Common use cases:**
```jsx
// Data fetching
useEffect(() => {
  fetch(url).then(res => setData(res));
}, [url]);

// Subscriptions
useEffect(() => {
  const subscription = subscribe(handler);
  return () => subscription.unsubscribe();
}, []);

// DOM manipulation
useEffect(() => {
  document.title = `${count} items`;
}, [count]);

// Cleanup prevents memory leaks
useEffect(() => {
  const timer = setInterval(tick, 1000);
  return () => clearInterval(timer);  // Clean up!
}, []);
```

**Important points:**
- Runs AFTER render (paint), not before
- Can't be async directly (define async function inside)
- Cleanup runs before re-execution and unmount

---

### Q18: What is the difference between useEffect and useLayoutEffect?

| Aspect | useEffect | useLayoutEffect |
|--------|-----------|-----------------|
| **Timing** | After paint (async) | Before paint (sync) |
| **Blocks visual update** | No | Yes |
| **Use for** | Most side effects | DOM measurements, sync |
| **Performance** | Non-blocking | Can block rendering |
| **When DOM changes** | Runs after visible | Runs before visible |

**When to use useLayoutEffect:**
- You need to measure DOM elements
- You need to synchronously update the DOM
- You need to prevent visual flicker

```jsx
// useLayoutEffect - for DOM measurements
function Measure() {
  const [height, setHeight] = useState(0);
  const ref = useRef();
  
  useLayoutEffect(() => {
    setHeight(ref.current.clientHeight);
  }, []);
  
  return <div ref={ref}>{height}</div>;
}
```

---

### Q19: When would you use useReducer over useState?

**Answer:**
Use `useReducer` when:

1. **Complex state logic**: When state updates involve multiple sub-values or complex transitions
2. **Predictable state transitions**: When state changes follow a pattern (like reducers)
3. **State depends on previous state**: Complex logic where new state depends on complex previous state
4. **Related state**: Multiple values that change together
5. **Testing**: Reducers are pure functions, easier to test

**Example comparison:**
```jsx
// useState - good for simple state
const [count, setCount] = useState(0);
setCount(count + 1);

// useReducer - better for complex state
const initialState = { count: 0, step: 1 };

function reducer(state, action) {
  switch (action.type) {
    case 'INCREMENT':
      return { ...state, count: state.count + state.step };
    case 'DECREMENT':
      return { ...state, count: state.count - state.step };
    case 'SET_STEP':
      return { ...state, step: action.payload };
    default:
      return state;
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);
  
  return (
    <>
      <p>{state.count}</p>
      <button onClick={() => dispatch({ type: 'INCREMENT' })}>+</button>
      <button onClick={() => dispatch({ type: 'SET_STEP', payload: 5 })}>Set Step: 5</button>
    </>
  );
}
```

**Rule of thumb:** Start with `useState`. Move to `useReducer` when state logic becomes complex or unwieldy.

---

### Q20: Explain useRef and its use cases.

**Answer:**
`useRef` returns a mutable ref object with a `.current` property that persists across renders WITHOUT triggering re-renders.

**Key characteristics:**
- `.current` is mutable (unlike state)
- Changes don't trigger re-render
- Value persists across renders
- Perfect for DOM access and values you don't want in render

**Use cases:**

**1. DOM Access:**
```jsx
function AutoFocus() {
  const inputRef = useRef(null);
  
  useEffect(() => {
    inputRef.current.focus();
  }, []);
  
  return <input ref={inputRef} />;
}
```

**2. Storing previous values:**
```jsx
function PreviousValue() {
  const [count, setCount] = useState(0);
  const prevCountRef = useRef();
  
  useEffect(() => {
    prevCountRef.current = count;
  }, [count]);
  
  return (
    <p>Current: {count}, Previous: {prevCountRef.current}</p>
  );
}
```

**3. Mutable values without re-render:**
```jsx
function Timer() {
  const [seconds, setSeconds] = useState(0);
  const intervalRef = useRef();

  useEffect(() => {
    intervalRef.current = setInterval(() => {
      setSeconds(s => s + 1);
    }, 1000);
    return () => clearInterval(intervalRef.current);
  }, []);

  return <p>{seconds} seconds</p>;
}
```

**4. useRef vs useState:**
```jsx
// useState: changes trigger re-render
const [value, setValue] = useState(initial);

// useRef: changes don't trigger re-render
const valueRef = useRef(initial);
valueRef.current = newValue;  // No re-render!
```

---

### Q21: What are useMemo and useCallback? When to use them?

**Answer:**
Both are memoization hooks that optimize performance by preventing unnecessary calculations/rerenders.

**useMemo** - Memoizes computed values:
```jsx
const memoizedValue = useMemo(() => {
  return expensiveCalculation(a, b);
}, [a, b]);

// Only recalculates when a or b changes
```

**useCallback** - Memoizes function references:
```jsx
const memoizedCallback = useCallback(() => {
  doSomething(a, b);
}, [a, b]);

// Only creates new function when a or b changes
```

**When to use:**
```jsx
function Parent({ items, filter }) {
  const [count, setCount] = useState(0);

  // ✅ Expensive calculation - use useMemo
  const filteredItems = useMemo(() => {
    return items.filter(item => item.type === filter);
  }, [items, filter]);

  // ✅ For React.memo children - use useCallback
  const handleClick = useCallback(() => {
    console.log('Clicked');
  }, []);

  // ✅ Stable value for useEffect deps - use useMemo
  const config = useMemo(() => ({
    theme: 'dark',
    locale: 'en'
  }), []);

  return (
    <MemoizedList items={filteredItems} onClick={handleClick} />
  );
}
```

**When NOT to use:**
- For simple calculations
- For primitive values
- When component doesn't re-render often
- Prematurely (always profile first!)

---

### Q22: What are custom hooks? Provide examples.

**Answer:**
Custom hooks are JavaScript functions starting with `use` that encapsulate and reuse stateful logic.

**Why custom hooks:**
- Share logic between components
- Extract complex logic from components
- Keep components focused on UI

**Examples:**

**useLocalStorage:**
```jsx
function useLocalStorage(key, initialValue) {
  const [value, setValue] = useState(() => {
    const stored = localStorage.getItem(key);
    return stored ? JSON.parse(stored) : initialValue;
  });

  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(value));
  }, [key, value]);

  return [value, setValue];
}

// Usage
function Settings() {
  const [theme, setTheme] = useLocalStorage('theme', 'light');
  return <button onClick={() => setTheme('dark')}>{theme}</button>;
}
```

**useFetch:**
```jsx
function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const controller = new AbortController();
    
    fetch(url, { signal: controller.signal })
      .then(res => res.json())
      .then(setData)
      .catch(err => err.name !== 'AbortError' && setError(err))
      .finally(() => setLoading(false));

    return () => controller.abort();
  }, [url]);

  return { data, loading, error };
}
```

**useClickOutside:**
```jsx
function useClickOutside(ref, handler) {
  useEffect(() => {
    const listener = (e) => {
      if (!ref.current || ref.current.contains(e.target)) return;
      handler(e);
    };
    document.addEventListener('mousedown', listener);
    return () => document.removeEventListener('mousedown', listener);
  }, [ref, handler]);
}
```

---

## 5. Context & State Management

### Q23: What is Context API and when use it?

**Answer:**
Context provides a way to share data across the component tree without passing props manually at every level.

**When to use:**
- Theme data (light/dark mode)
- User authentication state
- Locale/language preferences
- Global settings
- Data needed by many components at different nesting levels

**When NOT to use:**
- Frequently changing data (causes many re-renders)
- Data only needed by a few components (use props)
- State management replacement (use Redux for complex apps)

**API:**
```jsx
// 1. Create context
const ThemeContext = createContext('light');

// 2. Provider
<ThemeContext.Provider value="dark">
  <App />
</ThemeContext.Provider>

// 3. Consumer (useContext is preferred)
const theme = useContext(ThemeContext);
```

**Performance consideration:**
```jsx
// ❌ Problem: New object every render
<Context.Provider value={{ theme, setTheme }}>

// ✅ Solution: Memoize
const value = useMemo(() => ({ theme, setTheme }), [theme]);
<Context.Provider value={value}>
```

---

### Q24: Redux vs Context API vs useState - When to use what?

| Scenario | Solution | Why |
|----------|----------|-----|
| Local component state | useState | Simple, ephemeral |
| Related local state | useReducer | Complex but local |
| Prop drilling | Context or composition | Avoid unnecessary props |
| Global theme/auth | Context | Low update frequency |
| Complex global state | Redux/Zustand | Middleware, devtools |
| Server state | React Query/SWR | Caching, refetching |

**Decision tree:**
```
Is state needed by many components globally?
  ├── YES → Is it complex (middleware, time-travel)?
  │         ├── YES → Redux Toolkit
  │         └── NO → Context API
  └── NO → Is state complex?
            ├── YES → useReducer
            └── NO → useState
```

---

### Q25: How does React Redux work?

**Answer:**
React Redux connects Redux store to React components.

**Core concepts:**
1. **Store**: Single source of truth (createStore/configureStore)
2. **Reducers**: Pure functions that update state
3. **Actions**: Plain objects describing what happened
4. **Dispatch**: Sends actions to the store

**Usage:**
```jsx
// Store setup
import { configureStore } from '@reduxjs/toolkit';
const store = configureStore({ reducer: rootReducer });

// Provider
import { Provider } from 'react-redux';
<Provider store={store}>
  <App />
</Provider>

// Connect component
import { useSelector, useDispatch } from 'react-redux';

function Counter() {
  const count = useSelector(state => state.counter.count);
  const dispatch = useDispatch();
  
  return (
    <button onClick={() => dispatch({ type: 'INCREMENT' })}>
      {count}
    </button>
  );
}
```

---

## 6. Performance Optimization

### Q26: How do you optimize React performance?

**Answer:**

**1. Prevent unnecessary re-renders:**
```jsx
// React.memo for components
const MemoizedComponent = React.memo(Component);

// useMemo for computed values
const expensive = useMemo(() => compute(items), [items]);

// useCallback for callbacks
const handleClick = useCallback(() => doSomething(), []);
```

**2. Code splitting:**
```jsx
const HeavyComponent = lazy(() => import('./HeavyComponent'));

<Suspense fallback={<Loading />}>
  <HeavyComponent />
</Suspense>
```

**3. List virtualization:**
```jsx
// For long lists, render only visible items
import { FixedSizeList } from 'react-window';
```

**4. State colocation:**
```jsx
// ❌ State higher than needed
function Parent() {
  const [count, setCount] = useState(0);  // Used only in ChildB
  return <><ChildA /><ChildB count={count} /></>;
}

// ✅ State where it's needed
function ChildB() {
  const [count, setCount] = useState(0);  // Right here!
  return <div>{count}</div>;
}
```

**5. Lazy initialization:**
```jsx
// ❌ Expensive on every render
const [data, setData] = useState(expensiveInit());

// ✅ Only runs once
const [data, setData] = useState(() => expensiveInit());
```

**6. Batch updates:**
```jsx
// React 18+ auto-batches
setCount(1);
setName('Alice');  // Single re-render
```

---

### Q27: What is React.memo and when use it?

**Answer:**
`React.memo` is a higher-order component that memoizes a component, preventing re-renders when props haven't changed.

**Syntax:**
```jsx
const MemoizedComponent = React.memo(MyComponent);

// With custom comparison
const MemoizedWithCompare = React.memo(
  MyComponent,
  (prevProps, nextProps) => prevProps.id === nextProps.id
);
```

**When to use:**
- Pure functional components
- Components that re-render often with same props
- Components with expensive rendering logic
- Components receiving object/array/function props

**When NOT to use:**
- Components that always receive new props
- Simple, cheap components
- Class components (use PureComponent instead)

**Common mistake:**
```jsx
// ❌ Won't work - new prop every render
<MemoizedButton onClick={() => console.log('click')} />

// ✅ Works - stable reference
const handleClick = useCallback(() => console.log('click'), []);
<MemoizedButton onClick={handleClick} />
```

---

### Q28: Explain the React reconciliation algorithm.

**Answer:**
Reconciliation is how React determines which parts of the DOM to update.

**Key concepts:**

**1. Element types:**
- Different root elements trigger full subtree teardown
- Same type elements update attributes

```jsx
// Full re-render of subtree
<div><Profile /></div>  →  <span><Profile /></span>

// Only update attributes
<div className="old" />  →  <div className="new" />
```

**2. Keys:**
- Keys help React identify which elements changed
- Should be stable, unique among siblings

```jsx
// ❌ Keys don't help with reordering
items.map((item, index) => <li key={index}>)

// ✅ Stable keys work well
items.map(item => <li key={item.id}>)
```

**3. Diffing algorithm:**
- Two elements of different types → destroy old, create new
- Elements of same type → compare attributes
- Recurse on children with keys

---

## 7. React Router

### Q29: What is React Router and how do you use it?

**Answer:**
React Router is the standard routing library for React.

**Setup:**
```bash
npm install react-router-dom
```

**Basic routing (v6):**
```jsx
import { BrowserRouter, Routes, Route } from 'react-router-dom';

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/users/:id" element={<UserProfile />} />
        <Route path="*" element={<NotFound />} />
      </Routes>
    </BrowserRouter>
  );
}
```

**Navigation:**
```jsx
import { Link, NavLink, useNavigate } from 'react-router-dom';

// Link - basic navigation
<Link to="/about">About</Link>

// NavLink - with active state
<NavLink to="/dashboard" className={({ isActive }) => isActive ? 'active' : ''}>
  Dashboard
</NavLink>

// Programmatic navigation
const navigate = useNavigate();
navigate('/dashboard');
navigate(-1);  // Go back
```

**Route parameters:**
```jsx
// Route definition
<Route path="/users/:id" element={<UserProfile />} />

// Access in component
const { id } = useParams();
```

---

### Q30: Difference between React Router v5 and v6?

| Feature | v5 | v6 |
|---------|----|----|
| Routes | `<Switch>` | `<Routes>` |
| Route element | `component={Comp}` | `element={<Comp />}` |
| Redirect | `<Redirect to="/path" />` | `<Navigate to="/path" />` |
| Navigation | `useHistory()` | `useNavigate()` |
| Parameters | `useParams()` | `useParams()` |
| Index route | `<IndexRoute>` | `<Route index>` |
| 404 route | `<Route component={NF} />` | `<Route path="*" element={<NF>} />` |

---

## 8. Testing & Debugging

### Q31: How do you test React components?

**Answer:**

**Using React Testing Library:**
```jsx
import { render, screen, fireEvent } from '@testing-library/react';

test('button click increments counter', () => {
  render(<Counter />);
  
  const button = screen.getByRole('button', { name: /increment/i });
  fireEvent.click(button);
  
  expect(screen.getByText('1')).toBeInTheDocument();
});
```

**Testing form submission:**
```jsx
test('form submits with correct data', async () => {
  const handleSubmit = jest.fn();
  render(<LoginForm onSubmit={handleSubmit} />);
  
  fireEvent.change(screen.getByLabelText(/email/i), {
    target: { value: 'test@example.com' }
  });
  fireEvent.click(screen.getByRole('button', { name: /submit/i }));
  
  expect(handleSubmit).toHaveBeenCalledWith({
    email: 'test@example.com'
  });
});
```

**Testing async operations:**
```jsx
test('displays user data after loading', async () => {
  render(<UserProfile userId={1} />);
  
  expect(screen.getByText(/loading/i)).toBeInTheDocument();
  
  await waitFor(() => {
    expect(screen.getByText('Alice')).toBeInTheDocument();
  });
});
```

---

### Q32: How do you debug React applications?

**Answer:**

**1. React DevTools:**
- Components tab: Inspect component tree, props, state
- Profiler tab: Record and analyze renders

**2. Browser DevTools:**
- Elements tab: Inspect actual DOM
- Console: View logs and errors

**3. Debugging techniques:**
```jsx
// Add debugger in render
function MyComponent() {
  debugger;
  return <div />;
}

// Use console.log wisely
function MyComponent({ data }) {
  console.log('Props:', data);  // Temporary debugging
  return <div>{data}</div>;
}

// Error boundaries catch errors
class ErrorBoundary extends Component {
  componentDidCatch(error, info) {
    console.error('Error:', error);
    console.error('Info:', info);
  }
}
```

**4. Common issues and fixes:**
```jsx
// Problem: Component not updating
// Check: State/props actually changing?

// Problem: Infinite loop
// Check: useEffect missing dependency, setState in render

// Problem: Stale closure
// Check: Using functional update setState(prev => ...)

// Problem: Memory leak
// Check: Cleanup in useEffect, AbortController for fetches
```

---

## 9. Advanced Concepts

### Q33: What are Error Boundaries and how implement them?

**Answer:**
Error boundaries are React components that catch JavaScript errors anywhere in their child component tree and display a fallback UI.

**Class-based implementation:**
```jsx
class ErrorBoundary extends Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false, error: null };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true, error };
  }

  componentDidCatch(error, errorInfo) {
    console.error('Error:', error, errorInfo);
    // Send to error tracking service
  }

  render() {
    if (this.state.hasError) {
      return this.props.fallback || <DefaultError />;
    }
    return this.props.children;
  }
}

// Usage
<ErrorBoundary>
  <MyComponent />
</ErrorBoundary>
```

**What error boundaries DON'T catch:**
- Event handlers (use try/catch)
- Async code (setTimeout, Promises)
- Server-side rendering
- Errors in the boundary itself

---

### Q34: What is Suspense and how use it?

**Answer:**
Suspense lets components "wait" for something before rendering, primarily used with code splitting (React.lazy).

**Code splitting:**
```jsx
const OtherComponent = React.lazy(() => import('./OtherComponent'));

function MyComponent() {
  return (
    <Suspense fallback={<Loading />}>
      <OtherComponent />
    </Suspense>
  );
}
```

**Multiple Suspense boundaries:**
```jsx
<Suspense fallback={<SidebarSkeleton />}>
  <Sidebar />
</Suspense>
<Suspense fallback={<ContentSkeleton />}>
  <MainContent />
</Suspense>
```

**With React Router:**
```jsx
const Dashboard = lazy(() => import('./pages/Dashboard'));
const Settings = lazy(() => import('./pages/Settings'));

<Suspense fallback={<Loading />}>
  <Routes>
    <Route path="/" element={<Dashboard />} />
    <Route path="/settings" element={<Settings />} />
  </Routes>
</Suspense>
```

---

### Q35: What are Higher-Order Components (HOCs)?

**Answer:**
HOCs are functions that take a component and return a new component with enhanced functionality.

**Pattern:**
```jsx
function withAuthentication(WrappedComponent) {
  return function AuthenticatedComponent(props) {
    const { user, loading } = useAuth();

    if (loading) return <LoadingSpinner />;
    if (!user) return <Navigate to="/login" />;

    return <WrappedComponent {...props} user={user} />;
  };
}

// Usage
const ProtectedDashboard = withAuthentication(Dashboard);
```

**Common HOC examples:**
- `withRouter`: Provides router props
- `connect` (Redux): Connects to store
- `withStyles`: Provides styled components

**HOC vs Render Props vs Hooks:**
- HOCs: Good for cross-cutting concerns, but can cause prop confusion
- Render Props: Good for reusable render logic
- Hooks: Modern, preferred for most cases

---

### Q36: What are Render Props?

**Answer:**
A render prop is a function prop that a component uses to know what to render.

**Pattern:**
```jsx
function MouseTracker({ render }) {
  const [position, setPosition] = useState({ x: 0, y: 0 });
  
  useEffect(() => {
    const handleMouseMove = (e) => {
      setPosition({ x: e.clientX, y: e.clientY });
    };
    window.addEventListener('mousemove', handleMouseMove);
    return () => window.removeEventListener('mousemove', handleMouseMove);
  }, []);

  return render(position);
}

// Usage
<MouseTracker
  render={({ x, y }) => (
    <div>Mouse position: {x}, {y}</div>
  )}
/>
```

**Children as render prop:**
```jsx
function Container({ children }) {
  return <div className="container">{children({ title: 'Hello' })}</div>;
}

<Container>
  {({ title }) => <h1>{title}</h1>}
</Container>
```

---

### Q37: Explain React Server Components (RSC).

**Answer:**
React Server Components run on the server, reducing client-side JavaScript and improving performance.

**Key differences:**

| Aspect | Server Components | Client Components |
|--------|------------------|-------------------|
| Execution | Server | Browser |
| Bundle | No client JS | Includes in bundle |
| Hooks | ❌ | ✅ |
| Event handlers | ❌ | ✅ |
| Access to browser | ❌ | ✅ |
| Direct DB access | ✅ | ❌ |

**Server Component:**
```jsx
// page.server.js (runs on server)
async function UserList() {
  const users = await db.getUsers();  // Direct database access!
  return (
    <ul>
      {users.map(user => <li key={user.id}>{user.name}</li>)}
    </ul>
  );
}
```

**Client Component:**
```jsx
// button.client.js (runs in browser)
"use client";
import { useState } from 'react';

function LikeButton() {
  const [liked, setLiked] = useState(false);
  return <button onClick={() => setLiked(true)}>{liked ? '❤️' : '🤍'}</button>;
}
```

---

### Q38: What is the difference between SSR, SSG, and CSR?

**Answer:**

| Aspect | SSR | SSG | CSR |
|--------|-----|-----|-----|
| Rendering | Server | Server (build) | Client |
| Fresh data | ✅ Yes | ❌ No (unless ISR) | ✅ Yes |
| Performance | Fast FCP | Fastest | Slower FCP |
| SEO | ✅ Excellent | ✅ Excellent | ❌ Poor initially |
| Server load | Every request | Build only | None |
| Dynamic content | ✅ | Limited | ✅ |

**When to use:**
- **SSR**: E-commerce, dashboards, user-specific content
- **SSG**: Blogs, documentation, marketing sites
- **CSR**: SPAs, authenticated apps, highly interactive UIs

---

## 10. System Design Questions

### Q39: How would you design a large React application?

**Answer:**

**1. Project Structure:**
```
src/
├── components/       # Reusable UI components
│   ├── Button/
│   ├── Modal/
│   └── ...
├── features/        # Feature-based modules
│   ├── auth/
│   ├── dashboard/
│   └── ...
├── hooks/           # Custom hooks
├── contexts/        # React contexts
├── services/        # API calls
├── utils/           # Utilities
├── pages/           # Route pages
└── App.jsx
```

**2. State Management Strategy:**
- Local state: `useState`, `useReducer`
- Server state: React Query or SWR
- Global UI state: Context or Zustand
- Form state: React Hook Form

**3. Component Patterns:**
- Compound components for complex UI
- Container/presentational separation
- Custom hooks for logic extraction

**4. Performance Considerations:**
- Route-based code splitting
- Component lazy loading
- Virtualization for lists
- Memoization where needed
- Proper state colocation

**5. Testing Strategy:**
- Unit tests for hooks and utilities
- Component tests with Testing Library
- Integration tests for features
- E2E tests for critical paths

---

### Q40: How would you handle form validation in React?

**Answer:**

**Approach 1: Controlled components with validation:**
```jsx
function LoginForm() {
  const [values, setValues] = useState({ email: '', password: '' });
  const [errors, setErrors] = useState({});
  const [touched, setTouched] = useState({});

  const validate = () => {
    const newErrors = {};
    if (!values.email) {
      newErrors.email = 'Email is required';
    } else if (!/\S+@\S+\.\S+/.test(values.email)) {
      newErrors.email = 'Invalid email format';
    }
    if (!values.password) {
      newErrors.password = 'Password is required';
    } else if (values.password.length < 8) {
      newErrors.password = 'Password must be 8+ characters';
    }
    return newErrors;
  };

  const handleBlur = (e) => {
    setTouched({ ...touched, [e.target.name]: true });
    setErrors(validate());
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    const validationErrors = validate();
    setErrors(validationErrors);
    if (Object.keys(validationErrors).length === 0) {
      // Submit form
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        name="email"
        value={values.email}
        onChange={(e) => setValues({ ...values, email: e.target.value })}
        onBlur={handleBlur}
      />
      {touched.email && errors.email && <span>{errors.email}</span>}
      
      <button type="submit">Submit</button>
    </form>
  );
}
```

**Approach 2: Using React Hook Form (recommended):**
```jsx
import { useForm } from 'react-hook-form';

function LoginForm() {
  const { register, handleSubmit, formState: { errors } } = useForm();

  const onSubmit = (data) => console.log(data);

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <input {...register('email', { 
        required: 'Email is required',
        pattern: { value: /\S+@\S+\.\S+/, message: 'Invalid email' }
      })} />
      {errors.email && <span>{errors.email.message}</span>}
      
      <input {...register('password', { 
        required: 'Password is required',
        minLength: { value: 8, message: 'Min 8 characters' }
      })} />
      {errors.password && <span>{errors.password.message}</span>}
      
      <button type="submit">Submit</button>
    </form>
  );
}
```

---

### Q41: How would you implement optimistic updates?

**Answer:**

**What are optimistic updates?**
Show the user the result immediately (before server confirms), then revert if the server fails.

**Implementation:**
```jsx
function TodoApp() {
  const [todos, setTodos] = useState([]);
  const [error, setError] = useState(null);

  const deleteTodo = async (id) => {
    const previousTodos = [...todos];
    
    // Optimistically remove from UI
    setTodos(todos.filter(todo => todo.id !== id));
    
    try {
      await api.deleteTodo(id);
    } catch (err) {
      // Revert on failure
      setTodos(previousTodos);
      setError('Failed to delete. Please try again.');
    }
  };

  const toggleTodo = async (id) => {
    const todo = todos.find(t => t.id === id);
    const previousTodos = [...todos];
    
    // Optimistically toggle
    setTodos(todos.map(t => 
      t.id === id ? { ...t, completed: !t.completed } : t
    ));
    
    try {
      await api.toggleTodo(id, !todo.completed);
    } catch (err) {
      setTodos(previousTodos);
      setError('Failed to update. Please try again.');
    }
  };

  return (
    <div>
      {error && <div className="error">{error}</div>}
      {todos.map(todo => (
        <TodoItem
          key={todo.id}
          todo={todo}
          onDelete={() => deleteTodo(todo.id)}
          onToggle={() => toggleTodo(todo.id)}
        />
      ))}
    </div>
  );
}
```

---

### Q42: What is your approach to React performance optimization?

**Answer:**

**1. Measure first:**
- Use React DevTools Profiler
- Identify actual bottlenecks
- Set baseline metrics

**2. Code-level optimizations:**
```jsx
// Memoize expensive components
const ExpensiveList = React.memo(ListComponent);

// Memoize computed values
const filteredData = useMemo(() => 
  data.filter(item => item.active), [data]);

// Memoize callbacks passed to children
const handleClick = useCallback(() => doSomething(), []);
```

**3. Code splitting:**
```jsx
const Dashboard = lazy(() => import('./pages/Dashboard'));
```

**4. List optimization:**
```jsx
// Virtualize long lists
import { FixedSizeList } from 'react-window';

// Use stable keys
items.map(item => <Item key={item.id} />);
```

**5. State optimization:**
- Keep state as local as possible
- Separate unrelated state
- Use derived state where appropriate

**6. Bundle optimization:**
- Tree shake unused code
- Use specific imports (lodash/fp vs lodash)
- Consider lighter alternatives

---

## Quick Review Checklist

Before your interview, make sure you can explain:

- [ ] Virtual DOM and reconciliation
- [ ] Component lifecycle (both class and hooks)
- [ ] State vs Props
- [ ] useState vs useReducer
- [ ] useEffect cleanup and dependencies
- [ ] useMemo vs useCallback
- [ ] Context API and when to use it
- [ ] React.memo and PureComponent
- [ ] Error Boundaries
- [ ] Code splitting with lazy/Suspense
- [ ] React Router (v6)
- [ ] Common performance pitfalls
- [ ] Testing strategies
- [ ] State management options

---

**Good luck with your interview!** 🎉
