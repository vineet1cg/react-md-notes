# React Intermediate - Comprehensive Guide

> Level up your React skills with hooks mastery, state management patterns, routing, and advanced component patterns.

---

## Table of Contents
1. [React Hooks Deep Dive](#1-react-hooks-deep-dive)
2. [useState Mastery](#2-usestate-mastery)
3. [useEffect Mastery](#3-useeffect-mastery)
4. [useContext](#4-usecontext)
5. [useReducer](#5-usereducer)
6. [useRef](#6-useref)
7. [useMemo and useCallback](#7-usememo-and-usecallback)
8. [Custom Hooks](#8-custom-hooks)
9. [React Router v6](#9-react-router-v6)
10. [State Management Patterns](#10-state-management-patterns)

---

## 1. React Hooks Deep Dive

### What are Hooks?

**Hooks** are functions that let you "hook into" React state and lifecycle features from functional components. They were introduced in React 16.8 and revolutionized React development.

```
┌─────────────────────────────────────────────────────────────────┐
│                       HOOKS OVERVIEW                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  BASIC HOOKS                 ADDITIONAL HOOKS                     │
│  ├─ useState                 ├─ useReducer                       │
│  ├─ useEffect               ├─ useCallback                      │
│  └─ useContext              ├─ useMemo                          │
│                             ├─ useRef                           │
│                             ├─ useImperativeHandle              │
│                             ├─ useLayoutEffect                  │
│                             ├─ useDebugValue                    │
│                                                                  │
│  CUSTOM HOOKS                                                    │
│  └─ Your own hooks starting with "use" (useFetch, useLocalStorage, etc.)│
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Rules of Hooks

```jsx
// RULE 1: Only call hooks at the TOP LEVEL
// ❌ DON'T call hooks inside loops, conditions, or nested functions
function BadComponent({ showDetails }) {
  if (showDetails) {
    const [data, setData] = useState(null);  // ❌ VIOLATION!
  }
  // ...
}

// ✅ DO call hooks at the top level
function GoodComponent({ showDetails }) {
  const [data, setData] = useState(null);  // ✅ Always called
  // ...
}

// RULE 2: Only call hooks from React FUNCTIONS
// ✅ From functional components
function MyComponent() {
  const [state, setState] = useState();
  // ...
}

// ✅ From custom hooks
function useCustomHook() {
  const [state, setState] = useState();
  // ...
}

// ❌ DON'T call hooks from regular functions
function regularFunction() {
  const [state, setState] = useState();  // ❌ VIOLATION!
}
```

### Why These Rules Exist

React relies on the **order of hook calls** to maintain state:

```
┌─────────────────────────────────────────────────────────────────┐
│            HOW REACT TRACKS HOOK STATE                          │
│                                                                  │
│  Component renders:                                              │
│                                                                  │
│  const [a, setA] = useState(0);   // Hook #1 → manages 'a'     │
│  const [b, setB] = useState(0);   // Hook #2 → manages 'b'     │
│  const [c, setC] = useState(0);   // Hook #3 → manages 'c'     │
│                                                                  │
│  React stores: hooks[0] = { state: 0, setState: fn }           │
│                hooks[1] = { state: 0, setState: fn }           │
│                hooks[2] = { state: 0, setState: fn }           │
│                                                                  │
│  On re-render, React calls hooks in same order to get state     │
│  If order changes (due to conditionals), state gets confused!   │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## 2. useState Mastery

### Complete useState Reference

```jsx
// SYNTAX
const [state, setState] = useState(initialValue);

// VARIATIONS
const [count, setCount] = useState(0);                    // Number
const [name, setName] = useState("");                      // String
const [items, setItems] = useState([]);                    // Array
const [user, setUser] = useState({ name: "", age: 0 });   // Object
const [isLoading, setIsLoading] = useState(true);         // Boolean
const [data, setData] = useState(() => expensiveCalc());  // Lazy init
```

### Lazy Initialization

Use a function when initial state requires expensive computation:

```jsx
// ❌ WRONG - expensiveCalc runs on EVERY render
function SlowComponent() {
  const [data, setData] = useState(expensiveCalculation());  // Runs each render!
}

// ✅ CORRECT - function only runs ONCE on mount
function FastComponent() {
  const [data, setData] = useState(() => expensiveCalculation());
}

// Common lazy init use cases:
function App() {
  // Read from localStorage (only once)
  const [theme, setTheme] = useState(() => {
    const saved = localStorage.getItem("theme");
    return saved || "light";
  });

  // Parse JSON (only once)
  const [config, setConfig] = useState(() => {
    try {
      return JSON.parse(savedConfig) || defaultConfig;
    } catch {
      return defaultConfig;
    }
  });

  // Complex initial state
  const [matrix, setMatrix] = useState(() => createLargeMatrix());
}
```

### Object and Array State - Common Pitfalls

```jsx
function UserProfile() {
  const [user, setUser] = useState({
    name: "Alice",
    age: 25,
    address: {
      city: "NYC",
      country: "USA"
    }
  });

  // ❌ WRONG - Don't mutate state directly
  const updateName = () => {
    user.name = "Bob";        // Mutates state directly
    setUser(user);           // React sees same reference, no re-render!
  };

  // ❌ WRONG - Partial object replacement
  const updateAge = () => {
    setUser({ age: 26 });     // Lost name and address!
  };

  // ✅ CORRECT - Always spread previous state
  const updateName = () => {
    setUser(prev => ({
      ...prev,
      name: "Bob"
    }));
  };

  // ✅ CORRECT - Nested object update
  const updateCity = () => {
    setUser(prev => ({
      ...prev,
      address: {
        ...prev.address,
        city: "LA"
      }
    }));
  };

  // ✅ CORRECT - Array operations
  const addItem = (item) => {
    setItems(prev => [...prev, item]);
  };

  const removeItem = (index) => {
    setItems(prev => prev.filter((_, i) => i !== index));
  };

  const updateItem = (index, newValue) => {
    setItems(prev => prev.map((item, i) => 
      i === index ? newValue : item
    ));
  };
}
```

### Multiple useState vs Single useState

```jsx
// PATTERN 1: Multiple useState calls
function MultiState() {
  const [firstName, setFirstName] = useState("");
  const [lastName, setLastName] = useState("");
  const [email, setEmail] = useState("");
  const [phone, setPhone] = useState("");

  // ✅ PROS: Easy to read, update one at a time
  // ❌ CONS: Many setter functions
}

// PATTERN 2: Single useState with object
function SingleState() {
  const [form, setForm] = useState({
    firstName: "",
    lastName: "",
    email: "",
    phone: ""
  });

  const handleChange = (e) => {
    setForm(prev => ({
      ...prev,
      [e.target.name]: e.target.value
    }));
  };

  // ✅ PROS: One handler for all fields
  // ❌ CONS: More verbose updates for single fields
}

// BEST PRACTICE: Use whichever makes code clearer
// - Related data (form fields) → single object state
// - Unrelated data (modal open/close, theme) → separate states
```

### State Batching

React automatically batches multiple `setState` calls:

```jsx
function BatchingDemo() {
  const [x, setX] = useState(0);
  const [y, setY] = useState(0);
  const [name, setName] = useState("");

  // All 3 updates happen together in ONE re-render
  const handleClick = () => {
    setX(100);
    setY(200);
    setName("Alice");
    // React will batch these and re-render ONCE
  };

  // But if NEW state depends on OLD:
  const handleIncrement = () => {
    // ❌ WRONG - both use stale x value
    setX(x + 1);  // Schedules: x = oldX + 1
    setX(x + 1);  // Schedules: x = oldX + 1 (same!)
    // Result: x increases by 1, not 2

    // ✅ CORRECT - use functional update
    setX(prev => prev + 1);  // Schedule: x = prevX + 1
    setX(prev => prev + 1);  // Schedule: x = (prevX + 1) + 1 = prevX + 2
    // Result: x increases by 2
  };
}
```

---

## 3. useEffect Mastery

### Understanding useEffect

`useEffect` is React's way of handling **side effects** - operations that affect something outside the scope of the current function.

```
┌─────────────────────────────────────────────────────────────────┐
│                    WHAT ARE SIDE EFFECTS?                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  SIDE EFFECTS (outside the component)                           │
│  ├─ API calls / data fetching                                   │
│  ├─ Setting up subscriptions (WebSocket, event listeners)        │
│  ├─ Timer functions (setTimeout, setInterval)                  │
│  ├─ Manually changing the DOM                                   │
│  ├─ Logging                                                     │
│  ├─ Accessing external systems                                  │
│  └─ Updating global variables                                    │
│                                                                  │
│  NOT side effects (pure, synchronous)                           │
│  ├─ Calculations based on props/state                           │
│  ├─ Data transformations                                        │
│  ├─ Conditional returns based on props                          │
│  └─ Pure rendering                                              │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### useEffect Syntax

```jsx
useEffect(() => {
  // Effect logic runs after render
  return () => {
    // Cleanup function (optional)
  };
}, [dependencies]);
```

### Dependency Array Patterns

```jsx
function EffectPatterns() {
  const [count, setCount] = useState(0);
  const [userId, setUserId] = useState(1);

  // 1. NO dependency array - runs EVERY render
  useEffect(() => {
    console.log("Runs after EVERY render");
  });

  // 2. EMPTY array - runs ONCE after mount
  useEffect(() => {
    console.log("Runs once on mount");
    document.title = "App loaded";
  }, []);

  // 3. WITH dependencies - runs when ANY dependency changes
  useEffect(() => {
    console.log(`Count changed to: ${count}`);
  }, [count]);

  // 4. MULTIPLE dependencies
  useEffect(() => {
    console.log(`User ${userId}, Count ${count}`);
  }, [userId, count]);

  // 5. ALL dependencies (no array) - equivalent to no array
  useEffect(() => {
    console.log("Runs every render");
  });
}
```

### Cleanup Functions

Cleanup functions prevent **memory leaks** and **stale data**:

```jsx
function CleanupDemo() {
  useEffect(() => {
    // Setup: Run once on mount
    console.log("Setting up...");
    
    const interval = setInterval(() => {
      console.log("Tick");
    }, 1000);

    // Cleanup: Run on unmount and before effect re-runs
    return () => {
      console.log("Cleaning up...");
      clearInterval(interval);
    };
  }, []);

  // What happens when component unmounts:
  // 1. Cleanup runs (clears interval)
  // 2. Component is removed from DOM
  // 3. No memory leaks!
}
```

### Data Fetching with useEffect

```jsx
function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    let cancelled = false;

    async function fetchUser() {
      try {
        setLoading(true);
        const response = await fetch(`/api/users/${userId}`);
        
        if (!response.ok) throw new Error("Failed to fetch");
        
        const data = await response.json();
        
        // Don't update state if component is unmounted
        if (!cancelled) {
          setUser(data);
          setError(null);
        }
      } catch (err) {
        if (!cancelled) {
          setError(err.message);
        }
      } finally {
        if (!cancelled) {
          setLoading(false);
        }
      }
    }

    fetchUser();

    // Cleanup to prevent state updates on unmounted component
    return () => {
      cancelled = true;
    };
  }, [userId]);

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error}</p>;
  if (!user) return null;
  
  return <div>{user.name}</div>;
}
```

### AbortController for Fetch Cancellation

Modern way to cancel fetches (React 18+):

```jsx
function DataFetcher({ url }) {
  const [data, setData] = useState(null);

  useEffect(() => {
    const controller = new AbortController();
    const { signal } = controller;

    async function fetchData() {
      try {
        const response = await fetch(url, { signal });
        const result = await response.json();
        setData(result);
      } catch (err) {
        // Ignore abort errors
        if (err.name !== "AbortError") {
          console.error("Fetch error:", err);
        }
      }
    }

    fetchData();

    // Cleanup: Abort the fetch
    return () => controller.abort();
  }, [url]);

  return <div>{data ? JSON.stringify(data) : "Loading..."}</div>;
}
```

### Common useEffect Patterns

```jsx
// Pattern 1: One-time initialization
useEffect(() => {
  initializeApp();
  loadUserPreferences();
  setupEventListeners();
  // No cleanup needed if it's truly one-time
}, []);

// Pattern 2: Subscription pattern
useEffect(() => {
  const subscription = subscribeToEvents(handleEvent);
  
  return () => {
    subscription.unsubscribe();
  };
}, [dependency]);

// Pattern 3: Document title update
useEffect(() => {
  document.title = `${count} items`;
}, [count]);

// Pattern 4: Window resize handler
useEffect(() => {
  const handleResize = () => {
    setWindowWidth(window.innerWidth);
  };
  
  window.addEventListener("resize", handleResize);
  
  return () => window.removeEventListener("resize", handleResize);
}, []);

// Pattern 5: Event listener with cleanup
useEffect(() => {
  const handleKeyDown = (e) => {
    if (e.key === "Escape") handleClose();
  };
  
  document.addEventListener("keydown", handleKeyDown);
  
  return () => document.removeEventListener("keydown", handleKeyDown);
}, [handleClose]);
```

### Async in useEffect

```jsx
function AsyncEffect() {
  const [data, setData] = useState(null);

  // ❌ WRONG - useEffect can't be async directly
  useEffect(async () => {
    const data = await fetchData();
    setData(data);
  }, []);

  // ✅ CORRECT - Define async function inside
  useEffect(() => {
    const fetchData = async () => {
      const result = await fetch("/api/data");
      const json = await result.json();
      setData(json);
    };
    
    fetchData();
  }, []);

  // ✅ CORRECT - Using .then() instead
  useEffect(() => {
    fetch("/api/data")
      .then(res => res.json())
      .then(data => setData(data))
      .catch(err => console.error(err));
  }, []);
}
```

### Dependency Array Mistakes

```jsx
function DependencyMistakes() {
  const [count, setCount] = useState(0);
  const [name, setName] = useState("Alice");

  // ❌ WRONG - Missing dependency causes stale closure
  useEffect(() => {
    console.log(name);  // Always sees initial name!
  }, []);  // Missing name in deps

  // ✅ CORRECT
  useEffect(() => {
    console.log(name);
  }, [name]);

  // ❌ WRONG - Including function that's recreated every render
  const handleClick = () => {
    console.log("Clicked");
  };
  useEffect(() => {
    handleClick();
  }, [handleClick]);  // handleClick changes every render!

  // ✅ CORRECT - Define function inside or use useCallback
  useEffect(() => {
    const handleClick = () => {
      console.log("Clicked");
    };
    document.addEventListener("click", handleClick);
    return () => document.removeEventListener("click", handleClick);
  }, []);
}
```

---

## 4. useContext

### What is Context?

Context provides a way to pass data through the component tree without having to pass props down manually at every level.

```
┌─────────────────────────────────────────────────────────────────┐
│                    CONTEXT VS PROPS                             │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  PROPS DRILLING (Without Context)                               │
│  ┌────────────────────────────────────────────────────────────┐ │
│  │ App → Level1 → Level2 → Level3 → Level4 → Target          │ │
│  │              ↑        ↑       ↑       ↑                    │ │
│  │              └────────┴───────┴───────┘  Must pass theme   │ │
│  │                                        through every level │ │
│  └────────────────────────────────────────────────────────────┘ │
│                                                                  │
│  WITH CONTEXT                                                    │
│  ┌────────────────────────────────────────────────────────────┐ │
│  │ Provider wraps app                                           │ │
│  │              │                                               │ │
│  │              ▼                                               │ │
│  │ App → Level1 → Level2 → Level3 → Target                     │ │
│  │                           ↑                                  │ │
│  │                    useContext() grabs value                  │ │
│  │                    directly here (no drilling!)              │ │
│  └────────────────────────────────────────────────────────────┘ │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Creating and Using Context

```jsx
import { createContext, useContext, useState } from 'react';

// 1. CREATE CONTEXT
const ThemeContext = createContext();

// 2. PROVIDER COMPONENT
function ThemeProvider({ children }) {
  const [theme, setTheme] = useState("light");

  const toggleTheme = () => {
    setTheme(prev => prev === "light" ? "dark" : "light");
  };

  const value = {
    theme,
    setTheme,
    toggleTheme
  };

  return (
    <ThemeContext.Provider value={value}>
      {children}
    </ThemeContext.Provider>
  );
}

// 3. CONSUMER COMPONENT
function ThemeDisplay() {
  const { theme, toggleTheme } = useContext(ThemeContext);

  return (
    <div className={theme}>
      <p>Current theme: {theme}</p>
      <button onClick={toggleTheme}>Toggle Theme</button>
    </div>
  );
}

// 4. USE IN APP
function App() {
  return (
    <ThemeProvider>
      <Header />
      <Main />
      <Footer />
    </ThemeProvider>
  );
}
```

### Context with Multiple Values

```jsx
// Context for user authentication
const AuthContext = createContext();

function AuthProvider({ children }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  const login = async (credentials) => {
    setLoading(true);
    const userData = await api.login(credentials);
    setUser(userData);
    setLoading(false);
  };

  const logout = () => {
    setUser(null);
  };

  // Memoize value to prevent unnecessary re-renders
  const value = useMemo(() => ({
    user,
    loading,
    login,
    logout
  }), [user, loading]);

  return (
    <AuthContext.Provider value={value}>
      {children}
    </AuthContext.Provider>
  );
}

// Custom hook for consuming auth context
function useAuth() {
  const context = useContext(AuthContext);
  if (!context) {
    throw new Error("useAuth must be used within AuthProvider");
  }
  return context;
}

// Usage in components
function UserProfile() {
  const { user, logout, loading } = useAuth();
  
  if (loading) return <p>Loading...</p>;
  if (!user) return <p>Please log in</p>;
  
  return (
    <div>
      <p>Welcome, {user.name}!</p>
      <button onClick={logout}>Logout</button>
    </div>
  );
}
```

### Multiple Contexts

```jsx
// Theme context
const ThemeContext = createContext();
const ThemeProvider = ({ children }) => {
  const [theme, setTheme] = useState("light");
  return (
    <ThemeContext.Provider value={{ theme, setTheme }}>
      {children}
    </ThemeContext.Provider>
  );
};

// Locale context
const LocaleContext = createContext();
const LocaleProvider = ({ children }) => {
  const [locale, setLocale] = useState("en");
  return (
    <LocaleContext.Provider value={{ locale, setLocale }}>
      {children}
    </LocaleContext.Provider>
  );
};

// Combine providers
function AppProviders({ children }) {
  return (
    <ThemeProvider>
      <LocaleProvider>
        {children}
      </LocaleProvider>
    </ThemeProvider>
  );
}

// Consuming multiple contexts
function SettingsPage() {
  const { theme, setTheme } = useContext(ThemeContext);
  const { locale, setLocale } = useContext(LocaleContext);
  
  // ...
}
```

### Context Performance

```jsx
// ❌ PROBLEM: New object created every render
function BadProvider() {
  const [count, setCount] = useState(0);
  
  return (
    <Context.Provider value={{ count, setCount }}>
      {children}
    </Context.Provider>
  );
  // Every render creates new object → ALL consumers re-render
}

// ✅ SOLUTION 1: Memoize the context value
function GoodProvider() {
  const [count, setCount] = useState(0);
  
  const value = useMemo(() => ({
    count,
    setCount
  }), [count]);
  
  return (
    <Context.Provider value={value}>
      {children}
    </Context.Provider>
  );
}

// ✅ SOLUTION 2: Split contexts (one per piece of state)
const CountContext = createContext();
const SetCountContext = createContext();

function Provider() {
  const [count, setCount] = useState(0);
  
  return (
    <SetCountContext.Provider value={setCount}>
      <CountContext.Provider value={count}>
        {children}
      </CountContext.Provider>
    </SetCountContext.Provider>
  );
}
```

### When to Use Context

```
┌─────────────────────────────────────────────────────────────────┐
│                    WHEN TO USE CONTEXT                           │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ✅ GOOD USE CASES:                                              │
│  ├─ Theme (light/dark mode)                                     │
│  ├─ User authentication state                                    │
│  ├─ Language/locale preferences                                  │
│  ├─ Global settings                                              │
│  └─ Data shared across many components                          │
│                                                                  │
│  ❌ AVOID FOR:                                                   │
│  ├─ Frequently changing data                                    │
│  ├─ Data only needed by a few components (use props)            │
│  ├─ State that's local to one component                         │
│  └─ As a "global variable" for everything                       │
│                                                                  │
│  ⚠️ Context is NOT a replacement for:                            │
│  ├─ Redux (complex state with middleware needs)                 │
│  ├─ Component composition (passing JSX as children)             │
│  └─ URL state (use React Router's params)                      │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## 5. useReducer

### When to Use useReducer

```jsx
// useState is fine for:
const [count, setCount] = useState(0);
const [name, setName] = useState("");

// useReducer is better for:
// - Complex state logic with multiple sub-values
// - Related state that changes together
// - State transitions that are predictable
// - When next state depends on previous state
```

### useReducer Syntax

```jsx
const [state, dispatch] = useReducer(reducer, initialState, init);

// THREE parameters:
// 1. reducer: (state, action) => newState
// 2. initialState: the initial state
// 3. init (optional): function to compute initial state
```

### Complete useReducer Example

```jsx
// Define initial state
const initialState = {
  tasks: [],
  filter: "all",
  loading: false,
  error: null
};

// Define reducer - MUST be a PURE function
function taskReducer(state, action) {
  switch (action.type) {
    case "ADD_TASK":
      return {
        ...state,
        tasks: [
          ...state.tasks,
          {
            id: Date.now(),
            text: action.payload,
            completed: false
          }
        ]
      };

    case "TOGGLE_TASK":
      return {
        ...state,
        tasks: state.tasks.map(task =>
          task.id === action.payload
            ? { ...task, completed: !task.completed }
            : task
        )
      };

    case "DELETE_TASK":
      return {
        ...state,
        tasks: state.tasks.filter(task => task.id !== action.payload)
      };

    case "SET_FILTER":
      return {
        ...state,
        filter: action.payload
      };

    case "SET_LOADING":
      return {
        ...state,
        loading: action.payload
      };

    case "SET_ERROR":
      return {
        ...state,
        error: action.payload,
        loading: false
      };

    default:
      return state;
  }
}

// Component using reducer
function TodoApp() {
  const [state, dispatch] = useReducer(taskReducer, initialState);
  const [inputText, setInputText] = useState("");

  const handleSubmit = (e) => {
    e.preventDefault();
    if (!inputText.trim()) return;
    dispatch({ type: "ADD_TASK", payload: inputText });
    setInputText("");
  };

  const filteredTasks = state.tasks.filter(task => {
    if (state.filter === "active") return !task.completed;
    if (state.filter === "completed") return task.completed;
    return true;
  });

  return (
    <div>
      <form onSubmit={handleSubmit}>
        <input
          value={inputText}
          onChange={(e) => setInputText(e.target.value)}
          placeholder="Add task..."
        />
        <button type="submit">Add</button>
      </form>

      <select
        value={state.filter}
        onChange={(e) =>
          dispatch({ type: "SET_FILTER", payload: e.target.value })
        }
      >
        <option value="all">All</option>
        <option value="active">Active</option>
        <option value="completed">Completed</option>
      </select>

      <ul>
        {filteredTasks.map(task => (
          <li key={task.id}>
            <span
              style={{
                textDecoration: task.completed ? "line-through" : "none"
              }}
            >
              {task.text}
            </span>
            <button onClick={() =>
              dispatch({ type: "TOGGLE_TASK", payload: task.id })
            }>
              {task.completed ? "Undo" : "Complete"}
            </button>
            <button onClick={() =>
              dispatch({ type: "DELETE_TASK", payload: task.id })
            }>
              Delete
            </button>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

### Lazy Initialization

```jsx
// init function computes initial state
const initialState = { count: 0 };

function init(initialCount) {
  return { count: initialCount };
}

function reducer(state, action) {
  switch (action.type) {
    case "increment":
      return { count: state.count + 1 };
    case "decrement":
      return { count: state.count - 1 };
    case "reset":
      return init(action.payload);
    default:
      throw new Error("Unknown action");
  }
}

function Counter({ initialCount = 0 }) {
  const [state, dispatch] = useReducer(reducer, initialCount, init);
  // When you dispatch reset with payload, init() is called
  
  return (
    <div>
      <p>Count: {state.count}</p>
      <button onClick={() => dispatch({ type: "increment" })}>+</button>
      <button onClick={() => dispatch({ type: "reset", payload: initialCount })}>
        Reset
      </button>
    </div>
  );
}
```

### useReducer with Context

```jsx
// Combine useReducer with Context for global state
const TaskContext = createContext();
const TaskDispatchContext = createContext();

function TaskProvider({ children }) {
  const [state, dispatch] = useReducer(taskReducer, initialState);

  return (
    <TaskContext.Provider value={state}>
      <TaskDispatchContext.Provider value={dispatch}>
        {children}
      </TaskDispatchContext.Provider>
    </TaskContext.Provider>
  );
}

// Components only re-render when state changes
function TaskList() {
  const tasks = useContext(TaskContext);
  // Re-renders when tasks change
}

function AddTaskButton() {
  const dispatch = useContext(TaskDispatchContext);
  // Never re-renders from state changes (dispatch is stable)
}
```

---

## 6. useRef

### What is useRef?

`useRef` returns a mutable ref object whose `.current` property is initialized to the passed argument. Unlike state, changes to a ref do NOT trigger re-renders.

```
┌─────────────────────────────────────────────────────────────────┐
│                    useRef VS useState                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  useState                     useRef                            │
│  ├─ Changes trigger          ├─ Changes DON'T trigger           │
│  │  re-render                 │  re-render                     │
│  ├─ Tracked by React         ├─ Not tracked by React           │
│  ├─ For visible data         ├─ For non-visible data           │
│  └─ Immutable (via setter)  └─ Mutable (.current)             │
│                                                                  │
│  useState: { count: 0 }    →    useRef: { current: 0 }         │
│  setCount(5)               →    countRef.current = 5           │
│  Component re-renders       →    NO re-render                   │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### DOM Access with useRef

```jsx
import { useRef, useEffect } from 'react';

function InputFocus() {
  const inputRef = useRef(null);

  useEffect(() => {
    // Focus input on mount
    inputRef.current.focus();
  }, []);

  return (
    <div>
      <input ref={inputRef} type="text" />
      <button onClick={() => inputRef.current.focus()}>
        Focus Input
      </button>
      <button onClick={() => {
        inputRef.current.value = "";  // Direct DOM manipulation
        inputRef.current.focus();
      }}>
        Clear & Focus
      </button>
    </div>
  );
}

// Multiple refs
function MultiRefs() {
  const firstRef = useRef(null);
  const lastRef = useRef(null);
  const formRef = useRef(null);

  const focusFirst = () => firstRef.current.focus();
  const focusLast = () => lastRef.current.focus();
  const resetForm = () => {
    formRef.current.reset();
  };

  return (
    <form ref={formRef}>
      <input ref={firstRef} />
      <input ref={lastRef} />
    </form>
  );
}
```

### Persisting Values Across Renders

```jsx
function PreviousValueTracker() {
  const [count, setCount] = useState(0);
  const prevCountRef = useRef();

  // Store previous value
  useEffect(() => {
    prevCountRef.current = count;
  }, [count]);

  return (
    <div>
      <p>Current: {count}</p>
      <p>Previous: {prevCountRef.current}</p>
      <button onClick={() => setCount(c => c + 1)}>Increment</button>
    </div>
  );
}

// Tracking render count
function RenderCounter() {
  const renderCount = useRef(0);

  useEffect(() => {
    renderCount.current += 1;
    console.log(`Component rendered ${renderCount.current} times`);
  });

  return <p>Check console for render count</p>;
}

// Storing interval ID
function Timer() {
  const [seconds, setSeconds] = useState(0);
  const intervalRef = useRef();

  useEffect(() => {
    intervalRef.current = setInterval(() => {
      setSeconds(s => s + 1);
    }, 1000);

    return () => clearInterval(intervalRef.current);
  }, []);

  const stopTimer = () => clearInterval(intervalRef.current);

  return (
    <div>
      <p>{seconds} seconds</p>
      <button onClick={stopTimer}>Stop</button>
    </div>
  );
}
```

### useRef vs createRef

```jsx
// useRef - PERSISTS across renders (same object)
function ComponentWithUseRef() {
  const inputRef = useRef(null);  // Created once, reused
  
  console.log(inputRef);  // Same object every render
  // ✅ USE THIS in functional components
}

// createRef - NEW object each render
class ComponentWithCreateRef extends Component {
  render() {
    this.inputRef = createRef();  // NEW object each render!
    console.log(this.inputRef);  // Different object each render
    // Only for class components
  }
}
```

### Common useRef Patterns

```jsx
// Pattern 1: Previous state/props
function usePrevious(value) {
  const ref = useRef();
  useEffect(() => {
    ref.current = value;
  });
  return ref.current;
}

// Pattern 2: Click outside detection
function useClickOutside(ref, handler) {
  useEffect(() => {
    const listener = (event) => {
      if (!ref.current || ref.current.contains(event.target)) {
        return;
      }
      handler(event);
    };
    
    document.addEventListener("mousedown", listener);
    return () => document.removeEventListener("mousedown", listener);
  }, [ref, handler]);
}

// Pattern 3: Debounced value
function useDebounce(value, delay) {
  const [debouncedValue, setDebouncedValue] = useState(value);
  
  useEffect(() => {
    const handler = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);
    
    return () => clearTimeout(handler);
  }, [value, delay]);
  
  return debouncedValue;
}
```

---

## 7. useMemo and useCallback

### Why Memoization?

```
┌─────────────────────────────────────────────────────────────────┐
│                WHY MEMOIZATION MATTERS                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  WITHOUT MEMOIZATION:                                            │
│                                                                  │
│  Parent re-renders                                               │
│       │                                                         │
│       ▼                                                         │
│  All children re-render                                          │
│       │                                                         │
│       ├── ChildA (uses "hello")    → Re-renders (expensive!)   │
│       ├── ChildB (uses "world")    → Re-renders (expensive!)   │
│       └── ChildC (uses "42")       → Re-renders (expensive!)   │
│                                                                  │
│  WITH MEMOIZATION:                                               │
│                                                                  │
│  Parent re-renders                                               │
│       │                                                         │
│       ▼                                                         │
│  Children check: "Did my props change?"                          │
│       │                                                         │
│       ├── ChildA ("hello" unchanged)  → SKIP re-render         │
│       ├── ChildB ("world" unchanged)  → SKIP re-render         │
│       └── ChildC ("42" changed)      → Re-render               │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### useMemo

Memoizes a **computed value**:

```jsx
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);

// Example
function SortedList({ items, filter }) {
  // Only recalculates when items or filter changes
  const sortedItems = useMemo(() => {
    console.log("Sorting...");  // Only runs when needed
    return items
      .filter(item => item.type === filter)
      .sort((a, b) => a.name.localeCompare(b.name));
  }, [items, filter]);

  return (
    <ul>
      {sortedItems.map(item => (
        <li key={item.id}>{item.name}</li>
      ))}
    </ul>
  );
}

// When to use useMemo:
// ✅ Expensive calculations
// ✅ Referential equality for objects/arrays (for useCallback deps)
// ✅ Preventing recalculation on every render
```

### useCallback

Memoizes a **function reference**:

```jsx
const memoizedCallback = useCallback(() => {
  doSomething(a, b);
}, [a, b]);

// Example with React.memo
const Button = React.memo(({ onClick, children }) => {
  console.log("Button rendered");
  return <button onClick={onClick}>{children}</button>;
});

function Parent() {
  const [count, setCount] = useState(0);

  // ❌ Without useCallback - new function every render
  const handleClick = () => {
    console.log("Clicked");
  };
  // Button re-renders every time count changes!

  // ✅ With useCallback - same function until deps change
  const handleClick = useCallback(() => {
    console.log("Clicked");
  }, []);
  // Button only re-renders if onClick changes

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(c => c + 1)}>Increment</button>
      <Button onClick={handleClick}>Click me</Button>
    </div>
  );
}
```

### useMemo vs useCallback

```jsx
// useMemo returns a VALUE
const value = useMemo(() => expensiveCalculation(a, b), [a, b]);

// useCallback returns a FUNCTION (syntactic sugar)
const callback = useCallback((x) => doSomething(x, a), [a]);

// These are equivalent:
const memoizedValue = useMemo(() => fn, deps);
const memoizedCallback = useCallback(fn, deps);
```

### When to Use (and Not Use) Memoization

```
┌─────────────────────────────────────────────────────────────────┐
│                    MEMOIZATION GUIDELINES                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ✅ USE MEMO when:                                               │
│  ├─ Calculations are genuinely expensive                         │
│  ├─ Passing objects/arrays as props to memoized children        │
│  ├─ Passing functions to components wrapped in React.memo       │
│  ├─ Values used as useEffect dependencies (prevents infinite   │
│  │  loops)                                                       │
│  └─ Large lists being filtered/sorted                           │
│                                                                  │
│  ❌ DON'T USE MEMO when:                                          │
│  ├─ Calculation is simple (adding numbers, string ops)         │
│  ├─ Value is primitive (number, string, boolean)                │
│  ├─ Component renders rarely                                     │
│  ├─ Child isn't wrapped in React.memo                           │
│  └─ You're just starting to optimize (profile first!)          │
│                                                                  │
│  ⚠️ MEMOIZATION HAS COSTS:                                      │
│  ├─ Memory overhead for storing cached values                  │
│  ├─ Comparison check on every render (small but not zero)      │
│  └─ Code complexity                                             │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## 8. Custom Hooks

### What are Custom Hooks?

Custom hooks are JavaScript functions that:
1. Start with `use` prefix
2. Can call other hooks
3. Encapsulate and reuse stateful logic

```
┌─────────────────────────────────────────────────────────────────┐
│                    CUSTOM HOOK PATTERN                          │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  WITHOUT CUSTOM HOOK:                                            │
│  ┌────────────────────────────────────────────────────────────┐ │
│  │ function UserProfile() {                                  │ │
│  │   const [user, setUser] = useState(null);                │ │
│  │   const [loading, setLoading] = useState(true);          │ │
│  │   const [error, setError] = useState(null);              │ │
│  │   // ... fetch logic repeated everywhere                  │ │
│  │ }                                                        │ │
│  │ function UserList() {                                    │ │
│  │   const [user, setUser] = useState(null);  // REPEATED!  │ │
│  │   const [loading, setLoading] = useState(true); // REPEATED!│ │
│  │   const [error, setError] = useState(null);      // REPEATED!│ │
│  │ }                                                        │ │
│  └────────────────────────────────────────────────────────────┘ │
│                                                                  │
│  WITH CUSTOM HOOK:                                               │
│  ┌────────────────────────────────────────────────────────────┐ │
│  │ function useUser(userId) {                                 │ │
│  │   const [user, setUser] = useState(null);                │ │
│  │   const [loading, setLoading] = useState(true);          │ │
│  │   const [error, setError] = useState(null);              │ │
│  │   // ... fetch logic once                                  │ │
│  │   return { user, loading, error };                       │ │
│  │ }                                                        │ │
│  │                                                           │ │
│  │ function UserProfile() {                                  │ │
│  │   const { user, loading, error } = useUser(userId);     │ │
│  │ }                                                        │ │
│  │ function UserList() {                                    │ │
│  │   const { user, loading, error } = useUser(userId);     │ │
│  │ }                                                        │ │
│  └────────────────────────────────────────────────────────────┘ │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Creating Custom Hooks

```jsx
// Custom hook for localStorage
function useLocalStorage(key, initialValue) {
  // Get initial value from localStorage or use default
  const [storedValue, setStoredValue] = useState(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      console.error(error);
      return initialValue;
    }
  });

  // Update localStorage when state changes
  const setValue = (value) => {
    try {
      const valueToStore = value instanceof Function
        ? value(storedValue)
        : value;
      setStoredValue(valueToStore);
      window.localStorage.setItem(key, JSON.stringify(valueToStore));
    } catch (error) {
      console.error(error);
    }
  };

  return [storedValue, setValue];
}

// Usage
function Settings() {
  const [theme, setTheme] = useLocalStorage("theme", "light");
  const [name, setName] = useLocalStorage("name", "");

  return (
    <div>
      <input value={name} onChange={e => setName(e.target.value)} />
      <select value={theme} onChange={e => setTheme(e.target.value)}>
        <option value="light">Light</option>
        <option value="dark">Dark</option>
      </select>
    </div>
  );
}
```

### More Custom Hook Examples

```jsx
// useFetch - data fetching hook
function useFetch(url, options = {}) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const controller = new AbortController();
    
    async function fetchData() {
      try {
        setLoading(true);
        const response = await fetch(url, {
          ...options,
          signal: controller.signal
        });
        
        if (!response.ok) throw new Error("Failed to fetch");
        
        const json = await response.json();
        setData(json);
        setError(null);
      } catch (err) {
        if (err.name !== "AbortError") {
          setError(err.message);
        }
      } finally {
        setLoading(false);
      }
    }

    fetchData();

    return () => controller.abort();
  }, [url]);

  return { data, loading, error };
}

// Usage
function UserList() {
  const { data: users, loading, error } = useFetch("/api/users");
  
  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error}</p>;
  
  return (
    <ul>
      {users.map(user => <li key={user.id}>{user.name}</li>)}
    </ul>
  );
}

// useDebounce - debounce a value
function useDebounce(value, delay = 500) {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const timer = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);

    return () => clearTimeout(timer);
  }, [value, delay]);

  return debouncedValue;
}

// Usage in search
function Search() {
  const [query, setQuery] = useState("");
  const debouncedQuery = useDebounce(query, 300);
  
  useEffect(() => {
    if (debouncedQuery) {
      searchAPI(debouncedQuery);
    }
  }, [debouncedQuery]);

  return <input value={query} onChange={e => setQuery(e.target.value)} />;
}

// useToggle - boolean toggle
function useToggle(initialValue = false) {
  const [value, setValue] = useState(initialValue);
  
  const toggle = useCallback(() => setValue(v => !v), []);
  
  return [value, toggle];
}

// Usage
function Modal() {
  const [isOpen, toggleIsOpen] = useToggle(false);
  
  return (
    <div>
      <button onClick={toggleIsOpen}>Open Modal</button>
      {isOpen && <div>Modal Content</div>}
    </div>
  );
}

// useClickOutside - detect clicks outside element
function useClickOutside(ref, handler) {
  useEffect(() => {
    const listener = (event) => {
      if (!ref.current || ref.current.contains(event.target)) {
        return;
      }
      handler(event);
    };
    
    document.addEventListener("mousedown", listener);
    document.addEventListener("touchstart", listener);
    
    return () => {
      document.removeEventListener("mousedown", listener);
      document.removeEventListener("touchstart", listener);
    };
  }, [ref, handler]);
}

// Usage
function Dropdown() {
  const ref = useRef();
  const [isOpen, setIsOpen] = useToggle(false);
  
  useClickOutside(ref, () => setIsOpen(false));

  return (
    <div ref={ref}>
      <button onClick={toggle}>Dropdown</button>
      {isOpen && <div className="dropdown">Content</div>}
    </div>
  );
}
```

### Rules for Custom Hooks

```jsx
// ✅ Custom hooks can:
// - Call other hooks
// - Accept any arguments
// - Return any values
// - Be used multiple times in one component (each gets own state)

function useCounter(initialValue = 0) {
  const [count, setCount] = useState(initialValue);
  const increment = () => setCount(c => c + 1);
  const decrement = () => setCount(c => c - 1);
  const reset = () => setCount(initialValue);
  return { count, increment, decrement, reset };
}

function Dashboard() {
  // Each call gets its own independent state
  const counterA = useCounter(10);
  const counterB = useCounter(100);
  
  return (
    <div>
      <p>Counter A: {counterA.count}</p>
      <p>Counter B: {counterB.count}</p>
    </div>
  );
}
```

---

## 9. React Router v6

### Setup

```bash
npm install react-router-dom
```

### Basic Routing

```jsx
import { BrowserRouter, Routes, Route, Link } from 'react-router-dom';

function App() {
  return (
    <BrowserRouter>
      <nav>
        <Link to="/">Home</Link>
        <Link to="/about">About</Link>
        <Link to="/users">Users</Link>
      </nav>

      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/users" element={<Users />} />
      </Routes>
    </BrowserRouter>
  );
}
```

### Route Parameters

```jsx
function App() {
  return (
    <Routes>
      {/* Dynamic route parameter */}
      <Route path="/users/:userId" element={<UserProfile />} />
      
      {/* Multiple parameters */}
      <Route path="/posts/:year/:month" element={<Archive />} />
      
      {/* Optional parameter */}
      <Route path="/product/:id?" element={<Product />} />
      
      {/* Wildcard */}
      <Route path="*" element={<NotFound />} />
    </Routes>
  );
}

// Accessing parameters
function UserProfile() {
  const { userId } = useParams();
  return <h1>User ID: {userId}</h1>;
}

function Archive() {
  const { year, month } = useParams();
  return <h1>{month}/{year}</h1>;
}
```

### Programmatic Navigation

```jsx
import { useNavigate } from 'react-router-dom';

function LoginForm() {
  const navigate = useNavigate();

  const handleLogin = async (credentials) => {
    await login(credentials);
    navigate("/dashboard");           // Navigate to dashboard
    // OR: navigate("/dashboard", { replace: true });  // Replace current entry
  };

  const handleGoBack = () => {
    navigate(-1);  // Go back one page
  };

  return (
    <form>
      {/* form fields */}
      <button onClick={handleLogin}>Login</button>
      <button onClick={handleGoBack}>Back</button>
    </form>
  );
}
```

### Navigate Component

```jsx
import { Navigate } from 'react-router-dom';

// Redirect
function OldPage() {
  return <Navigate to="/new-page" replace />;
}

// Protected route pattern
function ProtectedRoute({ isAuthenticated, children }) {
  if (!isAuthenticated) {
    return <Navigate to="/login" replace />;
  }
  return children;
}

// Usage
<Route path="/dashboard" element={
  <ProtectedRoute isAuthenticated={user}>
    <Dashboard />
  </ProtectedRoute>
} />
```

### Nested Routes

```jsx
function App() {
  return (
    <Routes>
      <Route path="/" element={<Layout />}>
        <Route index element={<Home />} />
        <Route path="about" element={<About />} />
        <Route path="dashboard" element={<Dashboard />}>
          <Route index element={<DashboardOverview />} />
          <Route path="settings" element={<Settings />} />
          <Route path="profile" element={<Profile />} />
        </Route>
      </Route>
    </Routes>
  );
}

// Layout with Outlet
function Layout() {
  return (
    <div>
      <nav>Navigation</nav>
      <main>
        {/* Child routes render here */}
        <Outlet />
      </main>
      <footer>Footer</footer>
    </div>
  );
}
```

### Link vs NavLink

```jsx
// Link - basic navigation
<Link to="/about">About</Link>
<Link to="/users/123">User Profile</Link>

// NavLink - link with active state styling
<NavLink 
  to="/dashboard"
  className={({ isActive }) => isActive ? "active" : ""}
>
  Dashboard
</NavLink>

// Multiple styles
<NavLink 
  to="/settings"
  style={({ isActive }) => ({
    color: isActive ? "red" : "blue",
    fontWeight: isActive ? "bold" : "normal"
  })}
>
  Settings
</NavLink>
```

### Search Params

```jsx
import { useSearchParams } from 'react-router-dom';

function SearchResults() {
  const [searchParams, setSearchParams] = useSearchParams();
  
  const query = searchParams.get("q") || "";
  const category = searchParams.get("category") || "all";

  const updateSearch = (newQuery) => {
    const params = new URLSearchParams(searchParams);
    params.set("q", newQuery);
    setSearchParams(params);
  };

  const filterByCategory = (cat) => {
    const params = new URLSearchParams(searchParams);
    params.set("category", cat);
    setSearchParams(params);
  };

  return (
    <div>
      <input 
        value={query}
        onChange={(e) => updateSearch(e.target.value)}
      />
      <button onClick={() => filterByCategory("books")}>Books</button>
      
      <p>Query: {query}</p>
      <p>Category: {category}</p>
    </div>
  );
}
```

### useLocation

```jsx
import { useLocation } from 'react-router-dom';

function PageTracker() {
  const location = useLocation();

  useEffect(() => {
    // Track page views
    analytics.track("page_view", {
      path: location.pathname,
      search: location.search
    });
  }, [location]);

  return <p>Current path: {location.pathname}</p>;
}
```

### v5 vs v6 Comparison

| Feature | v5 | v6 |
|---------|----|----|
| Routes | `<Switch>` | `<Routes>` |
| Route element | `component={Comp}` | `element={<Comp />}` |
| Redirect | `<Redirect to="/path" />` | `<Navigate to="/path" />` |
| Navigation | `useHistory()` | `useNavigate()` |
| Parameters | `this.props.match.params.id` | `useParams().id` |
| 404 route | `<Route component={NotFound} />` | `<Route path="*" element={<NotFound />} />` |
| Index route | `<IndexRoute component={Home} />` | `<Route index element={<Home />} />` |

---

## 10. State Management Patterns

### State Colocation

```jsx
// ❌ BAD - State higher than needed
function App() {
  const [count, setCount] = useState(0);  // Used only in ChildB!
  
  return (
    <div>
      <Header />
      <ChildA />
      <ChildB count={count} setCount={setCount} />
    </div>
  );
}

// ✅ GOOD - State where it's needed
function ChildB() {
  const [count, setCount] = useState(0);
  return <div>{count}</div>;
}
```

### Lifting State Up

```jsx
// When siblings need to share state, lift it to parent
function Parent() {
  const [sharedData, setSharedData] = useState(null);
  
  return (
    <>
      <SiblingA data={sharedData} onUpdate={setSharedData} />
      <SiblingB data={sharedData} />
    </>
  );
}
```

### Compound Components

```jsx
// Pattern for components that work together
const TabsContext = createContext();

function Tabs({ defaultTab, children }) {
  const [activeTab, setActiveTab] = useState(defaultTab);
  
  return (
    <TabsContext.Provider value={{ activeTab, setActiveTab }}>
      <div className="tabs">{children}</div>
    </TabsContext.Provider>
  );
}

function TabList({ children }) {
  return <div className="tab-list">{children}</div>;
}

function Tab({ value, children }) {
  const { activeTab, setActiveTab } = useContext(TabsContext);
  const isActive = activeTab === value;
  
  return (
    <button
      className={isActive ? "active" : ""}
      onClick={() => setActiveTab(value)}
    >
      {children}
    </button>
  );
}

function TabPanel({ value, children }) {
  const { activeTab } = useContext(TabsContext);
  
  return activeTab === value ? <div>{children}</div> : null;
}

// Usage
function App() {
  return (
    <Tabs defaultTab="home">
      <TabList>
        <Tab value="home">Home</Tab>
        <Tab value="about">About</Tab>
      </TabList>
      <TabPanel value="home">Home content</TabPanel>
      <TabPanel value="about">About content</TabPanel>
    </Tabs>
  );
}
```

### Provider Pattern

```jsx
// Create feature-specific providers
function AuthProvider({ children }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);
  
  useEffect(() => {
    checkAuth().then(setUser).finally(() => setLoading(false));
  }, []);

  const login = async (credentials) => {
    const user = await api.login(credentials);
    setUser(user);
  };

  const logout = () => setUser(null);

  return (
    <AuthContext.Provider value={{ user, loading, login, logout }}>
      {children}
    </AuthContext.Provider>
  );
}

function ThemeProvider({ children }) {
  // Theme logic
}

function DataProvider({ children }) {
  // Data fetching logic
}

// Compose providers
function AppProviders({ children }) {
  return (
    <AuthProvider>
      <ThemeProvider>
        <DataProvider>
          {children}
        </DataProvider>
      </ThemeProvider>
    </AuthProvider>
  );
}
```

---

## Quick Reference

### Hook Decision Tree

```
DO YOU NEED TO TRIGGER RE-RENDER?
        │
        ├── YES
        │     │
        │     ├── Is it simple value? → useState
        │     │
        │     ├── Complex state logic? → useReducer
        │     │
        │     └── Need to avoid prop drilling? → useContext
        │
        └── NO
              │
              ├── DOM access? → useRef
              │
              ├── Persist value? → useRef
              │
              └── Timer/Interval ID? → useRef
```

### useEffect Decision Tree

```
WHAT ARE YOU DOING?
        │
        ├── Data fetching → useEffect + cleanup
        │
        ├── Subscriptions → useEffect + cleanup
        │
        ├── DOM manipulation → useLayoutEffect (sync) or useEffect (async)
        │
        ├── Set timers → useEffect + cleanup
        │
        └── Side effects after render → useEffect
```

---

## Next Steps

With intermediate concepts mastered, proceed to:
- **[React_Advanced.md](React_Advanced.md)** - Performance optimization, testing, SSR, and production patterns
- **[React_Interview_QA.md](React_Interview_QA.md)** - Common interview questions with detailed answers
