# Ultimate React Study Guide
## Complete Reference for JavaScript, React & Redux Toolkit

---

# PART 1: JAVASCRIPT FUNDAMENTALS

## ES6+ Features You Must Know

### Arrow Functions
```javascript
// Traditional
function add(a, b) { return a + b; }

// Arrow
const add = (a, b) => a + b;
const greet = name => `Hello, ${name}!`;
```

### Destructuring
```javascript
// Array
const [first, second] = [1, 2, 3];

// Object
const { name, age } = { name: 'John', age: 25 };

// Function parameters
function greet({ name, age }) { }
```

### Spread Operator
```javascript
const arr1 = [1, 2];
const arr2 = [...arr1, 3, 4]; // [1, 2, 3, 4]

const obj1 = { a: 1 };
const obj2 = { ...obj1, b: 2 }; // { a: 1, b: 2 }
```

### Template Literals
```javascript
const msg = `Hello, ${name}! You are ${age} years old.`;
```

### Promises & Async/Await
```javascript
// Promise
fetch(url).then(res => res.json()).then(data => console.log(data));

// Async/Await
async function getData() {
  try {
    const res = await fetch(url);
    const data = await res.json();
    console.log(data);
  } catch (err) {
    console.error(err);
  }
}
```

### Array Methods
```javascript
// map - transform each element
[1, 2, 3].map(x => x * 2); // [2, 4, 6]

// filter - keep matching elements
[1, 2, 3, 4].filter(x => x > 2); // [3, 4]

// reduce - accumulate to single value
[1, 2, 3].reduce((sum, x) => sum + x, 0); // 6

// find - first matching element
[1, 2, 3].find(x => x > 1); // 2

// some/every - check conditions
[1, 2, 3].some(x => x > 2); // true
[1, 2, 3].every(x => x > 0); // true
```

### Modules (Import/Export)
```javascript
// Named exports
export const PI = 3.14;
export function add(a, b) { return a + b; }
import { PI, add } from './math';

// Default export
export default function App() { }
import App from './App';
```

---

# PART 2: REACT FUNDAMENTALS

## What is React?
- **JavaScript library** for building UIs (not a full framework)
- **Component-based** - build encapsulated components
- **Declarative** - describe what UI should look like
- **Virtual DOM** - efficient DOM updates through reconciliation
- **Unidirectional data flow** - data flows parent to child

## Virtual DOM
1. State change triggers new Virtual DOM tree
2. Diffing algorithm compares old vs new tree
3. Minimal updates calculated
4. Batch updates applied to real DOM

## JSX Deep Dive

### JSX Rules
- Return ONE root element (use Fragment `<>`)
- Use `className` instead of `class`
- Use `htmlFor` instead of `for`
- All tags must be closed (`<img />`)
- camelCase for attributes (`onClick`, `tabIndex`)

### Expressions in JSX
```jsx
const name = "Alice";
const isLoggedIn = true;

<div>
  <h1>Hello, {name}!</h1>
  <p>2 + 2 = {2 + 2}</p>
  {isLoggedIn && <button>Logout</button>}
  {isLoggedIn ? <Dashboard /> : <Login />}
</div>
```

### Inline Styles
```jsx
<div style={{
  backgroundColor: "#f0f0f0",
  fontSize: 16,
  padding: "10px 20px"
}}>Content</div>
```

## Components

### Functional Components (Modern)
```jsx
function Welcome({ name }) {
  return <h1>Hello, {name}!</h1>;
}

// Arrow function
const Welcome = ({ name }) => <h1>Hello, {name}!</h1>;
```

### Class Components (Legacy)
```jsx
class Welcome extends Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
  }
  
  render() {
    return <h1>Hello, {this.props.name}!</h1>;
  }
}
```

### Component Naming
- MUST start with uppercase (`MyComponent`, not `mycomponent`)
- Lowercase becomes HTML element

## Props

### What are Props?
- Read-only data passed parent to child
- One-way data flow (parent → child)
- Can be any value: strings, numbers, arrays, objects, functions, JSX

```jsx
// Passing props
<UserCard name="Alice" age={25} isActive={true} />

// Receiving props
function UserCard({ name, age, isActive }) {
  return (
    <div className={isActive ? "active" : "inactive"}>
      <h2>{name}</h2>
      <p>Age: {age}</p>
    </div>
  );
}
```

### Default Props
```jsx
function Button({ label = "Submit" }) {
  return <button>{label}</button>;
}
```

### Children Prop
```jsx
function Card({ title, children }) {
  return (
    <div className="card">
      <h2>{title}</h2>
      <div className="content">{children}</div>
    </div>
  );
}

<Card title="Welcome">
  <p>This is children content!</p>
</Card>
```

## State

### useState Hook
```jsx
const [count, setCount] = useState(0);
const [user, setUser] = useState({ name: "", email: "" });
const [items, setItems] = useState([]);
```

### Updating State
```jsx
// Basic
setCount(count + 1);

// Functional update (when depends on previous)
setCount(prev => prev + 1);

// Object - always spread
setUser(prev => ({ ...prev, name: "Alice" }));

// Array
setItems(prev => [...prev, newItem]);
setItems(prev => prev.filter(item => item.id !== id));
```

### Lazy Initialization
```jsx
// Only runs on first render
const [data, setData] = useState(() => expensiveCalculation());
```

## Event Handling

### Common Events
- `onClick`, `onChange`, `onSubmit`
- `onFocus`, `onBlur`
- `onKeyDown`, `onKeyUp`
- `onMouseEnter`, `onMouseLeave`

### Event Handler
```jsx
function EventDemo() {
  const handleClick = (e) => {
    e.preventDefault();
    console.log(e.target.value);
  };
  
  return (
    <button onClick={handleClick}>Click</button>
    <input onChange={(e) => console.log(e.target.value)} />
  );
}
```

### Common Mistakes
```jsx
// ❌ Wrong - calls immediately
<button onClick={handleClick()}>Click</button>

// ✅ Correct - passes reference
<button onClick={handleClick}>Click</button>
<button onClick={() => handleClick()}>Click</button>
```

## Conditional Rendering

```jsx
// Ternary operator
{isLoggedIn ? <Dashboard /> : <Login />}

// Logical AND
{unreadCount > 0 && <span>{unreadCount}</span>}

// Early return
if (!user) return <Login />;
return <Dashboard />;
```

### ⚠️ Trap: Logical AND with 0
```jsx
// ❌ Renders "0" on screen!
{count && <Component />}

// ✅ Use this instead
{count > 0 && <Component />}
```

## Lists and Keys

```jsx
function UserList({ users }) {
  return (
    <ul>
      {users.map(user => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

### Keys Rules
- Must be unique among siblings
- Use stable IDs (not array index if list can reorder)
- Keys help React identify changed elements

## Forms

### Controlled Component
```jsx
function LoginForm() {
  const [form, setForm] = useState({ email: "", password: "" });
  
  const handleChange = (e) => {
    const { name, value } = e.target;
    setForm(prev => ({ ...prev, [name]: value }));
  };
  
  const handleSubmit = (e) => {
    e.preventDefault();
    console.log(form);
  };
  
  return (
    <form onSubmit={handleSubmit}>
      <input name="email" value={form.email} onChange={handleChange} />
      <input name="password" type="password" value={form.password} onChange={handleChange} />
      <button type="submit">Login</button>
    </form>
  );
}
```

---

# PART 3: REACT HOOKS DEEP DIVE

## Rules of Hooks
1. Only call hooks at the top level (not in loops, conditions, nested functions)
2. Only call hooks from React functions (components or custom hooks)

## useState
- Adds state to functional components
- Returns `[stateValue, setterFunction]`
- Lazy initialization: `useState(() => expensiveCalc())`
- Functional update: `setCount(prev => prev + 1)`

## useEffect
Performs side effects after render.

```jsx
// Runs every render
useEffect(() => { console.log("Every render"); });

// Runs once on mount
useEffect(() => { console.log("Mounted"); }, []);

// Runs when dependency changes
useEffect(() => { console.log(count); }, [count]);

// Cleanup (on unmount)
useEffect(() => {
  const timer = setInterval(() => {}, 1000);
  return () => clearInterval(timer);
}, []);
```

### Common Mistakes
- ❌ `useEffect(async () => {})` - cannot be async directly
- ✅ Define async function inside

```jsx
useEffect(() => {
  const fetchData = async () => {
    const data = await api.get();
    setData(data);
  };
  fetchData();
}, []);
```

## useContext
Consumes context value without prop drilling.

```jsx
const ThemeContext = createContext("light");

function App() {
  return (
    <ThemeContext.Provider value="dark">
      <Child />
    </ThemeContext.Provider>
  );
}

function Child() {
  const theme = useContext(ThemeContext);
  return <div className={theme}>Theme: {theme}</div>;
}
```

## useReducer
For complex state logic.

```jsx
function reducer(state, action) {
  switch (action.type) {
    case "increment": return { count: state.count + 1 };
    case "decrement": return { count: state.count - 1 };
    default: return state;
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

## useRef
Mutable reference that persists across renders WITHOUT triggering re-render.

```jsx
const inputRef = useRef(null);
const countRef = useRef(0);

// DOM access
<input ref={inputRef} />
inputRef.current.focus();

// Mutable value without re-render
countRef.current += 1;
```

## useMemo & useCallback

### useMemo - Memoizes computed value
```jsx
const expensive = useMemo(() => {
  return heavyComputation(a, b);
}, [a, b]);
```

### useCallback - Memoizes function reference
```jsx
const handleClick = useCallback(() => {
  console.log(count);
}, [count]);
```

### When to Use
- **useMemo**: Expensive calculations, object/array deps for children
- **useCallback**: Pass callbacks to memoized children (React.memo)

## Custom Hooks
Reusable functions starting with `use` that encapsulate logic.

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
const [theme, setTheme] = useLocalStorage("theme", "light");
```

---

# PART 4: REACT ROUTER (v6)

## Setup
```jsx
import { BrowserRouter, Routes, Route, Link, useNavigate, useParams } from 'react-router-dom';

function App() {
  return (
    <BrowserRouter>
      <nav>
        <Link to="/">Home</Link>
        <Link to="/about">About</Link>
      </nav>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/user/:id" element={<UserProfile />} />
        <Route path="*" element={<NotFound />} />
      </Routes>
    </BrowserRouter>
  );
}
```

## Key APIs
| API | Purpose |
|-----|---------|
| `useParams()` | Access URL parameters (`:id`) |
| `useNavigate()` | Programmatic navigation |
| `Link` | Navigation without reload |
| `Navigate` | Redirect |

```jsx
function UserProfile() {
  const { id } = useParams();
  return <p>User ID: {id}</p>;
}

function Login() {
  const navigate = useNavigate();
  const handleLogin = () => navigate("/dashboard");
}
```

---

# PART 5: STATE MANAGEMENT - REDUX

## Redux Flow
```
User Action → dispatch(action) → Reducer → New State → Re-render
```

## Redux Toolkit (Modern - Use This!)

### Create Slice
```jsx
import { createSlice, configureStore } from '@reduxjs/toolkit';

const counterSlice = createSlice({
  name: 'counter',
  initialState: { count: 0 },
  reducers: {
    increment: (state) => { state.count += 1; },
    decrement: (state) => { state.count -= 1; },
    addBy: (state, action) => { state.count += action.payload; }
  }
});

export const { increment, decrement, addBy } = counterSlice.actions;
export default counterSlice.reducer;
```

### Configure Store
```jsx
import { configureStore } from '@reduxjs/toolkit';
import counterReducer from './counterSlice';

const store = configureStore({
  reducer: {
    counter: counterReducer
  }
});

export default store;
```

### Provider
```jsx
import { Provider } from 'react-redux';
<Provider store={store}><App /></Provider>
```

### Use in Component
```jsx
import { useSelector, useDispatch } from 'react-redux';
import { increment, decrement } from './store';

function Counter() {
  const count = useSelector(state => state.counter.count);
  const dispatch = useDispatch();
  
  return (
    <div>
      <p>{count}</p>
      <button onClick={() => dispatch(increment())}>+</button>
      <button onClick={() => dispatch(decrement())}>-</button>
    </div>
  );
}
```

## Async Operations - createAsyncThunk
```jsx
export const fetchUsers = createAsyncThunk(
  'users/fetchUsers',
  async () => {
    const res = await fetch('https://api.example.com/users');
    return res.json();
  }
);

const usersSlice = createSlice({
  name: 'users',
  initialState: { users: [], loading: false, error: null },
  reducers: {},
  extraReducers: (builder) => {
    builder
      .addCase(fetchUsers.pending, (state) => {
        state.loading = true;
      })
      .addCase(fetchUsers.fulfilled, (state, action) => {
        state.loading = false;
        state.users = action.payload;
      })
      .addCase(fetchUsers.rejected, (state, action) => {
        state.loading = false;
        state.error = action.error.message;
      });
  }
});
```

## RTK Query (Data Fetching)
```jsx
import { createApi, fetchBaseQuery } from '@reduxjs/toolkit/query/react';

export const usersApi = createApi({
  reducerPath: 'usersApi',
  baseQuery: fetchBaseQuery({ baseUrl: 'https://api.example.com' }),
  tagTypes: ['Users'],
  endpoints: (builder) => ({
    getUsers: builder.query({
      query: () => '/users',
      providesTags: ['Users']
    }),
    addUser: builder.mutation({
      query: (newUser) => ({
        url: '/users',
        method: 'POST',
        body: newUser
      }),
      invalidatesTags: ['Users']
    })
  })
});

export const { useGetUsersQuery, useAddUserMutation } = usersApi;
```

### Store Setup for RTK Query
```jsx
const store = configureStore({
  reducer: {
    [usersApi.reducerPath]: usersApi.reducer
  },
  middleware: (getDefaultMiddleware) =>
    getDefaultMiddleware().concat(usersApi.middleware)
});
```

---

# PART 6: PERFORMANCE OPTIMIZATION

## React.memo
```jsx
const MemoizedComponent = React.memo(function MyComponent({ data }) {
  return <div>{data}</div>;
});
```

## Code Splitting
```jsx
const LazyComponent = React.lazy(() => import('./HeavyComponent'));

function App() {
  return (
    <Suspense fallback={<Loading />}>
      <LazyComponent />
    </Suspense>
  );
}
```

## List Virtualization
For long lists, render only visible items using libraries like `react-window`.

---

# PART 7: ADVANCED PATTERNS

## Error Boundaries (Class Component)
```jsx
class ErrorBoundary extends Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    console.error(error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return <h1>Something went wrong.</h1>;
    }
    return this.props.children;
  }
}
```

## Higher-Order Components (HOCs)
```jsx
function withAuth(WrappedComponent) {
  return function AuthWrapper(props) {
    const { user } = useAuth();
    if (!user) return <Login />;
    return <WrappedComponent {...props} />;
  };
}

const ProtectedDashboard = withAuth(Dashboard);
```

---

# PART 8: CLASSIC REDUX vs REDUX TOOLKIT

| Feature | Classic | Redux Toolkit |
|---------|---------|---------------|
| Boilerplate | Lots | Minimal |
| Immutability | Manual spread | Immer (auto) |
| Store | `createStore()` | `configureStore()` |
| Async | Manual thunk | `createAsyncThunk` |
| Recommended | ❌ Legacy | ✅ Current |

---

# PART 9: COMMON EXAM TRAPS

1. **State mutation** - Never mutate directly, always create new object/array
2. **Missing dependency in useEffect** - Causes stale closures
3. **useEffect cannot be async directly** - Define function inside
4. **Logical AND with 0** - Renders "0" on screen!
5. **Keys in lists** - Must use stable unique IDs
6. **className vs class** - Use className in JSX
7. **Functional updates** - Use when new state depends on previous
8. **Provider wrapping** - Without Provider, useSelector/useDispatch fail

---

# QUICK REFERENCE

## Hooks Comparison
| Hook | Returns | Re-renders? | Use for |
|------|---------|--------------|---------|
| useState | `[val, setter]` | Yes | UI state |
| useEffect | nothing | depends | Side effects |
| useContext | value | Yes | Context |
| useReducer | `[state, dispatch]` | Yes | Complex state |
| useRef | `{ current }` | No | DOM, mutable |
| useMemo | value | No | Expensive calc |
| useCallback | function | No | Stable callbacks |

## Redux Flow
Action → dispatch → Reducer → Store → Component re-renders

## Key Differences
- Props: Parent → Child (read-only)
- State: Component internal (mutable)
- Context: Avoid prop drilling
- Redux: Global state management

---

*Last Updated: 2026*
*Good luck with your exam!*
