# ⚡ MASTER FILE 2: HOOKS, ROUTER & ASYNC LOGIC
### 📝 Exam-Optimized Revision | 35 MCQs + 15 Coding Questions

---

## 1. HOOK-BY-HOOK BREAKDOWN

---

### `useState`
**Definition:** Adds state to functional components. Returns `[stateValue, setterFunction]`.

**Syntax:**
```jsx
const [count, setCount] = useState(0);
const [user, setUser] = useState({ name: "", age: 0 });
```

**Key Behaviors:**
- Initial value set once (first render only)
- **Lazy init:** `useState(() => expensiveCalc())` — function runs only on mount
- Setter triggers **re-render**
- Updates are **asynchronous** (batched)
- For objects/arrays: **must spread** → `setUser(prev => ({ ...prev, age: 25 }))`
- **Functional update** for prev-dependent state: `setCount(prev => prev + 1)`

**Common Mistakes:**
- ❌ `setItems(items.push(x))` → mutating array directly
- ❌ Calling `setCount(count + 1)` three times → still +1 (stale closure)
- ✅ `setCount(prev => prev + 1)` three times → +3

**Exam Traps:**
- `useState` does NOT merge objects like `this.setState` does
- `this.setState({ age: 25 })` merges → `useState` replaces entire value
- Initial value `useState(fn())` → fn runs **every render**! Use `useState(fn)` for lazy init

---

### `useEffect`
**Definition:** Performs side effects (API calls, timers, DOM mutations, subscriptions) after render.

**Syntax Variants:**
```jsx
// Runs after EVERY render
useEffect(() => { /* effect */ });

// Runs ONCE on mount (like componentDidMount)
useEffect(() => { /* effect */ }, []);

// Runs when `dep` changes (like componentDidUpdate for dep)
useEffect(() => { /* effect */ }, [dep]);

// Cleanup (like componentWillUnmount)
useEffect(() => {
  const timer = setInterval(() => {}, 1000);
  return () => clearInterval(timer); // cleanup
}, []);
```

**Key Behaviors:**
- Runs **after** render (paint), not during
- Cleanup runs **before** next effect execution AND on unmount
- Dependency array controls when effect re-runs
- Empty `[]` = mount only + cleanup on unmount
- No array = every render

**Common Mistakes:**
- ❌ Missing dependency → stale values (React warns via ESLint rule)
- ❌ `useEffect(async () => {})` → effect cannot be async directly
- ✅ Define async function inside: `useEffect(() => { const fn = async () => {...}; fn(); }, [])`
- ❌ Forgetting cleanup → memory leaks (timers, event listeners, subscriptions)

**Exam Traps:**
- Effect with `[]` runs AFTER first render, NOT before (different from constructor)
- Return function is NOT the same as `componentWillUnmount` — it also runs before each re-execution
- `useEffect(() => {}, [obj])` → runs every render if `obj` is recreated (reference comparison)

---

### `useContext`
**Definition:** Consumes context value without nesting `Context.Consumer`. Solves prop drilling.

**Syntax:**
```jsx
// 1. Create context
const ThemeContext = React.createContext("light");

// 2. Provide value (wrap parent)
<ThemeContext.Provider value="dark">
  <ChildComponent />
</ThemeContext.Provider>

// 3. Consume in any nested child
function ChildComponent() {
  const theme = useContext(ThemeContext);
  return <p>Theme: {theme}</p>;
}
```

**Key Behaviors:**
- **All consumers re-render** when Provider value changes
- Default value (in `createContext`) used ONLY when no Provider is found above
- Can pass objects, functions, state as context value

**Common Mistakes:**
- ❌ Forgetting to wrap with `<Provider>` → gets default value, not the intended one
- ❌ Passing a new object literal every render → causes all consumers to re-render
- ✅ Memoize context value: `const val = useMemo(() => ({...}), [deps])`

**Exam Traps:**
- `useContext` does NOT prevent re-renders — it CAUSES them when value changes
- Context is NOT a replacement for Redux in complex apps (no built-in middleware, selectors)
- Multiple contexts → nest providers, each consumed with separate `useContext`

---

### `useReducer`
**Definition:** Alternative to `useState` for complex state logic. Uses `(state, action) => newState` pattern.

**Syntax:**
```jsx
function reducer(state, action) {
  switch (action.type) {
    case "increment": return { count: state.count + 1 };
    case "decrement": return { count: state.count - 1 };
    case "reset":     return { count: 0 };
    default: throw new Error("Unknown action");
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, { count: 0 });
  return (
    <>
      <p>{state.count}</p>
      <button onClick={() => dispatch({ type: "increment" })}>+</button>
    </>
  );
}
```

**Key Behaviors:**
- Reducer must be a **pure function** (no side effects, no API calls inside)
- `dispatch` is stable (never changes between renders) → safe to pass as prop
- Action = plain object with `type` property (convention)

**Common Mistakes:**
- ❌ Mutating state inside reducer: `state.count++` → must return new object
- ❌ Side effects inside reducer (API calls) → use `useEffect` for side effects

**Exam Traps:**
- `dispatch` does NOT return a value — it triggers reducer which updates state
- Reducer runs during render phase (synchronously)
- Third argument to `useReducer`: init function for lazy initialization

---

### `useRef`
**Definition:** Creates a mutable reference that persists across renders WITHOUT triggering re-render.

**Syntax:**
```jsx
const inputRef = useRef(null);
const countRef = useRef(0);

// Access DOM element
<input ref={inputRef} />
inputRef.current.focus();

// Persist mutable value
countRef.current += 1; // NO re-render
```

**Key Behaviors:**
- `.current` is mutable — changing it does NOT trigger re-render
- Perfect for: DOM access, storing previous values, interval IDs, mutable vars
- Value survives re-renders (unlike local variables)
- Initial value set once (like `useState`)

**Common Mistakes:**
- ❌ Using `useRef` for visible UI data (changes won't display)
- ❌ Reading `.current` during render for conditionals (may be stale in first render)

**Exam Traps:**
- `useRef(0)` → `ref.current === 0` on mount, stays same object across renders
- `useRef` vs `useState`: ref changes don't re-render, state changes do
- `useRef` vs `createRef`: `createRef` creates new ref each render, `useRef` persists

---

### `useMemo`
**Definition:** Memoizes a **computed value**. Recalculates only when dependencies change.

**Syntax:**
```jsx
const expensiveResult = useMemo(() => {
  return heavyComputation(a, b);
}, [a, b]);
```

**Key Behaviors:**
- Returns the **cached result** if deps haven't changed
- Runs **during** render (synchronous)
- Use for: expensive calculations, creating objects for dependency compare

**Common Mistakes:**
- ❌ Using for everything (premature optimization — has its own overhead)
- ❌ Omitting dependencies → stale cached value
- ❌ Side effects inside `useMemo` → use `useEffect` for side effects

---

### `useCallback`
**Definition:** Memoizes a **function reference**. Returns same function unless dependencies change.

**Syntax:**
```jsx
const handleClick = useCallback(() => {
  console.log(count);
}, [count]);
```

**Key Behaviors:**
- Returns **same function reference** if deps unchanged
- Useful when passing callbacks to memoized children (`React.memo`)
- Without `useCallback`, a new function is created every render

**Common Mistakes:**
- ❌ Using `useCallback` without `React.memo` on child → no benefit
- ❌ Empty dependency array when function uses external values → stale closure

---

### Custom Hooks
**Definition:** Reusable functions that encapsulate hook logic. Must start with `use` prefix.

**Syntax:**
```jsx
function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    fetch(url)
      .then(res => res.json())
      .then(data => { setData(data); setLoading(false); })
      .catch(err => { setError(err); setLoading(false); });
  }, [url]);

  return { data, loading, error };
}

// Usage:
const { data, loading, error } = useFetch("https://api.example.com/data");
```

**Key Behaviors:**
- **Each component gets its own copy** of the hook's state (not shared)
- Can use any built-in hooks inside custom hooks
- Must follow same Rules of Hooks

**Common Mistakes:**
- ❌ Not starting name with `use` → ESLint can't check Rules of Hooks
- ❌ Thinking state is shared between components using same custom hook

---

### Rules of Hooks ⚠️ CRITICAL
1. **Only call hooks at top level** — NOT inside loops, conditions, or nested functions
2. **Only call hooks in React functions** — functional components or custom hooks
3. Hooks must be called in the **same order** every render (React relies on call order)

```jsx
// ❌ WRONG
if (loggedIn) {
  useEffect(() => { ... });
}

// ✅ CORRECT
useEffect(() => {
  if (loggedIn) { ... }
}, [loggedIn]);
```

---

## 2. HOOK COMPARISONS (VERY IMPORTANT)

### `useMemo` vs `useCallback`
| Feature | `useMemo` | `useCallback` |
|---------|-----------|--------------|
| **Returns** | Memoized **value** | Memoized **function** |
| **Use case** | Expensive calculations | Stable function reference for children |
| **Equivalent** | `useCallback(fn, deps)` = `useMemo(() => fn, deps)` | — |
| **Runs** | During render | Returns function, doesn't run it |

### `useReducer` vs `useState`
| Feature | `useState` | `useReducer` |
|---------|-----------|-------------|
| **Best for** | Simple state (boolean, number, string) | Complex state (objects, multiple actions) |
| **Update** | `setX(newValue)` | `dispatch({ type: "ACTION" })` |
| **Logic location** | Inline in component | Centralized in reducer function |
| **Related state** | Multiple `useState` calls | Single state object |
| **Predictability** | Less (scattered) | More (all logic in reducer) |

### `useRef` vs `useState`
| Feature | `useRef` | `useState` |
|---------|---------|-----------|
| **Triggers re-render** | ❌ No | ✅ Yes |
| **Stores** | Mutable value (`.current`) | Immutable (via setter) |
| **Use case** | DOM access, timers, prev values | UI-visible data |
| **Survives re-render** | ✅ Yes | ✅ Yes |

### `useEffect` Dependency Behaviors
| Dependency Array | Behavior | Equivalent Lifecycle |
|-----------------|----------|---------------------|
| None (omitted) | Runs after **every** render | `componentDidMount` + `componentDidUpdate` |
| `[]` (empty) | Runs **once** after mount | `componentDidMount` |
| `[a, b]` | Runs when `a` or `b` changes | `componentDidUpdate` with comparison |
| Cleanup `return () => {}` | Runs before re-execution + unmount | `componentWillUnmount` |

---

## 3. ROUTER CHEAT SHEET (React Router DOM v6)

### Setup
```jsx
import { BrowserRouter, Routes, Route, Link, NavLink,
         useNavigate, useParams, Navigate, Outlet } from 'react-router-dom';

function App() {
  return (
    <BrowserRouter>
      <nav>
        <Link to="/">Home</Link>
        <Link to="/about">About</Link>
        <NavLink to="/contact" className={({ isActive }) => isActive ? "active" : ""}>
          Contact
        </NavLink>
      </nav>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/user/:id" element={<UserProfile />} />
        <Route path="/dashboard" element={<Dashboard />}>
          <Route path="settings" element={<Settings />} />
          <Route path="profile" element={<Profile />} />
        </Route>
        <Route path="*" element={<NotFound />} />
      </Routes>
    </BrowserRouter>
  );
}
```

### Key APIs

| API | Purpose | Replaces (v5) |
|-----|---------|---------------|
| `<Routes>` | Renders first matching route | `<Switch>` |
| `<Route path element>` | Maps URL to component | `<Route component>` |
| `<Link to>` | Navigation without reload | Same |
| `<NavLink>` | Link with active styling | Same |
| `<Navigate to replace />` | Redirect | `<Redirect>` |
| `<Outlet />` | Renders child route | — |
| `useNavigate()` | Programmatic navigation | `useHistory()` |
| `useParams()` | Access URL params (`:id`) | Same |
| `useLocation()` | Current URL info | Same |
| `useSearchParams()` | Query parameters | — |

### Route Parameters
```jsx
// Route definition
<Route path="/user/:id" element={<UserProfile />} />

// Access in component
function UserProfile() {
  const { id } = useParams();
  return <p>User: {id}</p>;
}
```

### Programmatic Navigation
```jsx
function Login() {
  const navigate = useNavigate();
  const handleLogin = () => {
    // after auth logic...
    navigate("/dashboard");          // push
    navigate("/dashboard", { replace: true }); // replace (no back)
    navigate(-1);                    // go back
  };
  return <button onClick={handleLogin}>Login</button>;
}
```

### Protected Route Pattern
```jsx
function ProtectedRoute({ isAuth, children }) {
  return isAuth ? children : <Navigate to="/login" replace />;
}

// Usage
<Route path="/dashboard" element={
  <ProtectedRoute isAuth={isLoggedIn}>
    <Dashboard />
  </ProtectedRoute>
} />
```

### Nested Routes
```jsx
// Parent renders <Outlet /> where child routes appear
function Dashboard() {
  return (
    <div>
      <h1>Dashboard</h1>
      <Outlet /> {/* child route renders here */}
    </div>
  );
}
```

### Key Exam Points
- v6: `element={<Comp />}` not `component={Comp}`
- v6: `<Routes>` replaces `<Switch>`
- v6: `Navigate` replaces `Redirect`
- v6: `useNavigate` replaces `useHistory`
- `path="*"` = catch-all 404 route (must be last)
- `BrowserRouter` uses HTML5 history API (clean URLs)
- `HashRouter` uses URL hash (`/#/about`) — older browser support

---

## 4. ASYNC RENDERING PATTERNS

### Pattern 1: fetch with useEffect (Most Common)
```jsx
function DataFetcher() {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchData = async () => {
      try {
        const res = await fetch("https://api.example.com/data");
        const json = await res.json();
        setData(json);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };
    fetchData();
  }, []);

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error}</p>;
  return <div>{JSON.stringify(data)}</div>;
}
```

### Pattern 2: Cleanup to prevent state update on unmounted component
```jsx
useEffect(() => {
  let isCancelled = false;

  fetch(url)
    .then(res => res.json())
    .then(data => {
      if (!isCancelled) setData(data);
    });

  return () => { isCancelled = true; }; // cleanup
}, [url]);
```

### Pattern 3: AbortController for cancelling fetch
```jsx
useEffect(() => {
  const controller = new AbortController();

  fetch(url, { signal: controller.signal })
    .then(res => res.json())
    .then(data => setData(data))
    .catch(err => {
      if (err.name !== 'AbortError') setError(err);
    });

  return () => controller.abort();
}, [url]);
```

### Async Approaches Comparison
| Approach | Readability | Error Handling | Best For |
|----------|-------------|---------------|----------|
| **async/await** | ⭐⭐⭐⭐⭐ Excellent | `try/catch` | Modern React apps |
| **Promises (.then)** | ⭐⭐⭐ Good | `.catch()` | Chaining |
| **Callbacks** | ⭐ Poor | Manual per callback | Legacy only (callback hell) |

### Critical Rules
- ❌ `useEffect(async () => ...)` → effect can't be async directly (returns promise, not cleanup)
- ✅ Define async function INSIDE effect and call it
- Always handle loading + error states
- Always cleanup to avoid memory leaks

---

## 5. 35 ADVANCED MCQ TRAPS (WITH ANSWERS)

**Q1.** What does `useEffect(() => { ... })` (no dependency array) do?
- A) Runs once on mount
- B) Runs after every render
- C) Never runs
- D) Runs only on unmount

**Answer: B** — No dependency array = runs after every render.

---

**Q2.** `useEffect(() => { return () => console.log("cleanup") }, [])` — when does cleanup run?
- A) After every render
- B) Only on unmount
- C) Before every effect execution
- D) Never

**Answer: B** — With `[]`, the effect runs once on mount and cleanup runs once on unmount.

---

**Q3.** Can you call `useEffect` inside an if statement?
- A) Yes
- B) No — violates Rules of Hooks

**Answer: B** — Hooks must be at the top level, never inside conditions.

---

**Q4.** `const [count, setCount] = useState(0); setCount(count + 1); setCount(count + 1);` — what is count?
- A) 2
- B) 1
- C) 0
- D) Error

**Answer: B** — Both calls use the same stale `count` (0), so both set it to 1.

---

**Q5.** How to fix Q4 to increment by 2?
- A) `setCount(prev => prev + 1)` called twice
- B) `setCount(count + 2)`
- C) Both A and B
- D) Neither

**Answer: C** — Both work, but A is the correct pattern for multiple sequential updates.

---

**Q6.** `useRef` value change triggers re-render?
- A) Yes
- B) No

**Answer: B** — `useRef` mutations do not trigger re-renders. That's its key difference from `useState`.

---

**Q7.** `useMemo` returns:
- A) A memoized function
- B) A memoized value
- C) A ref object
- D) A dispatch function

**Answer: B** — `useMemo` returns a cached computed value. `useCallback` returns a cached function.

---

**Q8.** `useCallback(fn, [])` is equivalent to:
- A) `useMemo(fn, [])`
- B) `useMemo(() => fn, [])`
- C) `useRef(fn)`
- D) `useEffect(fn, [])`

**Answer: B** — `useCallback(fn, deps)` === `useMemo(() => fn, deps)`.

---

**Q9.** What's the difference between `useContext` and prop drilling?
- A) No difference
- B) `useContext` skips intermediate components
- C) `useContext` is faster
- D) Prop drilling is deprecated

**Answer: B** — Context lets any nested component consume value directly without passing through every level.

---

**Q10.** `useReducer` returns:
- A) `[state, setState]`
- B) `[state, dispatch]`
- C) `[dispatch, state]`
- D) `{ state, dispatch }`

**Answer: B** — `const [state, dispatch] = useReducer(reducer, initialState)`.

---

**Q11.** Where should side effects (API calls) go in a reducer?
- A) Inside the reducer function
- B) Inside `useEffect`
- C) Inside `dispatch`
- D) Inside the component constructor

**Answer: B** — Reducers must be pure. Side effects belong in `useEffect`.

---

**Q12.** `createContext("default")` — when is "default" used?
- A) Always
- B) When Provider value is undefined
- C) When no Provider wraps the consumer
- D) Never

**Answer: C** — Default value is used only when there's no matching Provider above in the tree.

---

**Q13.** Custom hooks MUST:
- A) Return JSX
- B) Start with `use` prefix
- C) Be class methods
- D) Use `this`

**Answer: B** — Custom hooks must start with `use` (e.g., `useFetch`). They return values/functions, not JSX.

---

**Q14.** Do two components using the same custom hook share state?
- A) Yes
- B) No — each gets its own state copy

**Answer: B** — Each component calling a custom hook gets independent state.

---

**Q15.** `useEffect(() => { const id = setInterval(...); return () => clearInterval(id); }, [])` — what's the return function?
- A) Error handler
- B) Cleanup function
- C) Render function
- D) Reducer

**Answer: B** — The returned function cleans up the interval on unmount.

---

**Q16.** In React Router v6, `<Switch>` is replaced by:
- A) `<Router>`
- B) `<Routes>`
- C) `<Route>`
- D) `<Navigate>`

**Answer: B** — v6 uses `<Routes>` instead of `<Switch>`.

---

**Q17.** How to access URL parameter `:id` in React Router v6?
- A) `this.props.match.params.id`
- B) `useParams().id`
- C) `useLocation().id`
- D) `useNavigate().id`

**Answer: B** — `const { id } = useParams()`.

---

**Q18.** `useNavigate()` replaces which v5 hook?
- A) `useLocation`
- B) `useHistory`
- C) `useParams`
- D) `useRouteMatch`

**Answer: B** — `useNavigate` replaces `useHistory` in v6.

---

**Q19.** What does `<Navigate to="/login" replace />` do?
- A) Goes back
- B) Redirects and replaces current entry in history
- C) Opens new tab
- D) Renders Login inline

**Answer: B** — `replace` prevents back-button returning to current page.

---

**Q20.** What does `<Outlet />` render?
- A) The parent route
- B) All routes
- C) The matched child route
- D) Nothing

**Answer: C** — `Outlet` renders the matching nested child route.

---

**Q21.** `useEffect(async () => { await fetch(...) }, [])` — what's wrong?
- A) Nothing
- B) Effects can't be async (must return cleanup function or undefined, not a Promise)
- C) Missing dependency
- D) Syntax error

**Answer: B** — React expects effect to return nothing or a cleanup function, not a Promise.

---

**Q22.** `useState(Math.random())` vs `useState(() => Math.random())` — difference?
- A) No difference
- B) First runs `Math.random()` every render; second runs only on mount
- C) Second is slower
- D) First is for numbers only

**Answer: B** — `useState(fn)` (lazy init) only calls fn on first render.

---

**Q23.** What happens if you forget the dependency array in `useEffect`?
- A) Effect never runs
- B) Effect runs after every render (potential infinite loops with setState inside)
- C) Effect runs once
- D) Error

**Answer: B** — It runs after every render, which can cause issues if it sets state.

---

**Q24.** `useRef` vs `createRef`:
- A) Same thing
- B) `useRef` persists across renders; `createRef` creates a new ref every render
- C) `createRef` is for hooks
- D) `useRef` is for class components

**Answer: B** — `useRef` is for functional components (persists); `createRef` is for class components.

---

**Q25.** `NavLink` vs `Link`:
- A) Same component
- B) `NavLink` can apply active styles, `Link` cannot
- C) `Link` is deprecated
- D) `NavLink` makes API calls

**Answer: B** — `NavLink` has `className`/`style` functions that receive `{ isActive }`.

---

**Q26.** `useContext` re-renders the consumer when:
- A) Parent re-renders
- B) Context Provider value changes
- C) Component's own state changes
- D) Both B and C

**Answer: D** — Consumer re-renders when its own state changes OR when context value changes.

---

**Q27.** Can you use multiple `useEffect` in one component?
- A) No, only one
- B) Yes, each for different concerns

**Answer: B** — Multiple `useEffect` hooks are allowed and encouraged for separation of concerns.

---

**Q28.** `useReducer` third argument `init` function:
- A) Required
- B) Optional lazy initializer
- C) Error handler
- D) Middleware

**Answer: B** — `useReducer(reducer, initialArg, init)` — `init(initialArg)` runs for lazy initialization.

---

**Q29.** `useMemo(() => val, [])` — when does it recompute?
- A) Every render
- B) Never (computed once on mount)
- C) When val changes
- D) On unmount

**Answer: B** — Empty deps = compute once and cache forever.

---

**Q30.** Which hook should you use to focus an input on mount?
- A) `useState`
- B) `useEffect` + `useRef`
- C) `useReducer`
- D) `useMemo`

**Answer: B** — `useRef` to reference the input, `useEffect` to call `.focus()` on mount.

---

**Q31.** `path="*"` in React Router matches:
- A) Empty path
- B) Any unmatched path (404)
- C) Home page only
- D) Nothing

**Answer: B** — Wildcard `*` catches all unmatched routes (404 page).

---

**Q32.** Cleanup function in `useEffect` with deps `[count]` runs:
- A) Only on unmount
- B) Before every re-execution of the effect + on unmount
- C) After effect runs
- D) Never

**Answer: B** — Cleanup runs before each re-run and on unmount.

---

**Q33.** `useCallback` is primarily useful when:
- A) Computing expensive values
- B) Passing callbacks to `React.memo` wrapped children
- C) Making API calls
- D) Accessing DOM elements

**Answer: B** — Without `useCallback`, memoized children receive a new function each render, defeating `React.memo`.

---

**Q34.** Which statement about `useEffect` is FALSE?
- A) It runs after the browser paints
- B) It runs synchronously before render
- C) Cleanup runs before the next effect
- D) It can return a cleanup function

**Answer: B** — `useEffect` runs AFTER render and paint, not before.

---

**Q35.** `BrowserRouter` vs `HashRouter`:
- A) Same behavior
- B) `BrowserRouter` uses clean URLs (`/about`), `HashRouter` uses hash (`/#/about`)
- C) `HashRouter` is newer
- D) `BrowserRouter` doesn't need a server

**Answer: B** — `BrowserRouter` uses HTML5 History API; `HashRouter` uses URL hash for compatibility.

---

## 6. 15 MEDIUM-LEVEL CODING QUESTIONS

**Q1.** Write a component that fetches and displays users from an API on mount.
```jsx
import { useState, useEffect } from 'react';

function UserList() {
  const [users, setUsers] = useState([]);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const fetchUsers = async () => {
      const res = await fetch("https://jsonplaceholder.typicode.com/users");
      const data = await res.json();
      setUsers(data);
      setLoading(false);
    };
    fetchUsers();
  }, []);

  if (loading) return <p>Loading...</p>;
  return <ul>{users.map(u => <li key={u.id}>{u.name}</li>)}</ul>;
}
```

---

**Q2.** Write a `useEffect` with cleanup that sets/clears an interval.
```jsx
function Timer() {
  const [seconds, setSeconds] = useState(0);

  useEffect(() => {
    const id = setInterval(() => setSeconds(prev => prev + 1), 1000);
    return () => clearInterval(id);
  }, []);

  return <p>Seconds: {seconds}</p>;
}
```

---

**Q3.** Create a `ThemeContext` and consume it in a nested component.
```jsx
const ThemeContext = React.createContext("light");

function App() {
  const [theme, setTheme] = useState("light");
  return (
    <ThemeContext.Provider value={theme}>
      <button onClick={() => setTheme(t => t === "light" ? "dark" : "light")}>
        Toggle
      </button>
      <Display />
    </ThemeContext.Provider>
  );
}

function Display() {
  const theme = useContext(ThemeContext);
  return <p style={{ background: theme === "dark" ? "#333" : "#fff",
                     color: theme === "dark" ? "#fff" : "#333" }}>
    Theme: {theme}
  </p>;
}
```

---

**Q4.** Build a todo app using `useReducer`.
```jsx
function reducer(state, action) {
  switch (action.type) {
    case "add":    return [...state, { id: Date.now(), text: action.text }];
    case "delete": return state.filter(t => t.id !== action.id);
    default:       throw new Error("Unknown action");
  }
}

function TodoApp() {
  const [todos, dispatch] = useReducer(reducer, []);
  const [text, setText] = useState("");

  const handleAdd = () => { dispatch({ type: "add", text }); setText(""); };

  return (
    <div>
      <input value={text} onChange={e => setText(e.target.value)} />
      <button onClick={handleAdd}>Add</button>
      <ul>
        {todos.map(t => (
          <li key={t.id}>
            {t.text} <button onClick={() => dispatch({ type: "delete", id: t.id })}>X</button>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

---

**Q5.** Use `useRef` to focus an input on mount.
```jsx
function AutoFocus() {
  const inputRef = useRef(null);

  useEffect(() => {
    inputRef.current.focus();
  }, []);

  return <input ref={inputRef} placeholder="I auto-focus!" />;
}
```

---

**Q6.** Write `useMemo` to memoize a filtered list.
```jsx
function FilteredList({ items, query }) {
  const filtered = useMemo(() => {
    return items.filter(item => item.name.toLowerCase().includes(query.toLowerCase()));
  }, [items, query]);

  return <ul>{filtered.map(i => <li key={i.id}>{i.name}</li>)}</ul>;
}
```

---

**Q7.** Demonstrate `useCallback` with a memoized child.
```jsx
const Child = React.memo(({ onClick }) => {
  console.log("Child rendered");
  return <button onClick={onClick}>Click</button>;
});

function Parent() {
  const [count, setCount] = useState(0);

  const handleClick = useCallback(() => {
    console.log("Clicked");
  }, []);

  return (
    <div>
      <p>{count}</p>
      <button onClick={() => setCount(c => c + 1)}>Increment</button>
      <Child onClick={handleClick} /> {/* won't re-render on count change */}
    </div>
  );
}
```

---

**Q8.** Write a custom hook `useLocalStorage(key, initialValue)`.
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

// Usage:
const [name, setName] = useLocalStorage("name", "Guest");
```

---

**Q9.** Set up React Router with Home, About, and a 404 page.
```jsx
import { BrowserRouter, Routes, Route, Link } from 'react-router-dom';

function App() {
  return (
    <BrowserRouter>
      <nav>
        <Link to="/">Home</Link> | <Link to="/about">About</Link>
      </nav>
      <Routes>
        <Route path="/" element={<h1>Home Page</h1>} />
        <Route path="/about" element={<h1>About Page</h1>} />
        <Route path="*" element={<h1>404 - Not Found</h1>} />
      </Routes>
    </BrowserRouter>
  );
}
```

---

**Q10.** Show a user profile based on URL param `:id`.
```jsx
import { useParams } from 'react-router-dom';

function UserProfile() {
  const { id } = useParams();
  const [user, setUser] = useState(null);

  useEffect(() => {
    fetch(`https://jsonplaceholder.typicode.com/users/${id}`)
      .then(res => res.json())
      .then(data => setUser(data));
  }, [id]);

  if (!user) return <p>Loading...</p>;
  return <div><h1>{user.name}</h1><p>{user.email}</p></div>;
}
```

---

**Q11.** Programmatically navigate after form submit using `useNavigate`.
```jsx
import { useNavigate } from 'react-router-dom';

function LoginForm() {
  const navigate = useNavigate();
  const [email, setEmail] = useState("");

  const handleSubmit = (e) => {
    e.preventDefault();
    // auth logic...
    navigate("/dashboard");
  };

  return (
    <form onSubmit={handleSubmit}>
      <input value={email} onChange={e => setEmail(e.target.value)} />
      <button type="submit">Login</button>
    </form>
  );
}
```

---

**Q12.** Write `useRef` to track previous state value.
```jsx
function usePrevious(value) {
  const ref = useRef();
  useEffect(() => {
    ref.current = value;
  }, [value]);
  return ref.current;
}

function Counter() {
  const [count, setCount] = useState(0);
  const prevCount = usePrevious(count);

  return (
    <p>Now: {count}, Before: {prevCount}</p>
  );
}
```

---

**Q13.** Fetch data with error handling and loading state using async/await.
```jsx
function DataLoader({ url }) {
  const [data, setData] = useState(null);
  const [error, setError] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    const load = async () => {
      try {
        const res = await fetch(url);
        if (!res.ok) throw new Error("Failed");
        const json = await res.json();
        setData(json);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };
    load();
  }, [url]);

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error}</p>;
  return <pre>{JSON.stringify(data, null, 2)}</pre>;
}
```

---

**Q14.** Write a protected route component.
```jsx
import { Navigate } from 'react-router-dom';

function ProtectedRoute({ isAuthenticated, children }) {
  if (!isAuthenticated) {
    return <Navigate to="/login" replace />;
  }
  return children;
}

// Usage in Routes:
<Route path="/dashboard" element={
  <ProtectedRoute isAuthenticated={loggedIn}>
    <Dashboard />
  </ProtectedRoute>
} />
```

---

**Q15.** Use `useEffect` cleanup to cancel fetch with AbortController.
```jsx
function CancellableFetch({ url }) {
  const [data, setData] = useState(null);

  useEffect(() => {
    const controller = new AbortController();

    fetch(url, { signal: controller.signal })
      .then(res => res.json())
      .then(json => setData(json))
      .catch(err => {
        if (err.name !== "AbortError") console.error(err);
      });

    return () => controller.abort(); // cancel on unmount/re-run
  }, [url]);

  return <pre>{JSON.stringify(data, null, 2)}</pre>;
}
```

---

> **END OF FILE 2 — HOOKS, ROUTER & ASYNC LOGIC** ✅
