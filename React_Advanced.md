# React Advanced - Master Reference

> Expert-level React concepts for senior developers, architects, and those preparing for technical leadership roles.

---

## Table of Contents
1. [Performance Optimization](#1-performance-optimization)
2. [React.memo and PureComponent](#2-reactmemo-and-purecomponent)
3. [Higher-Order Components (HOCs)](#3-higher-order-components-hocs)
4. [Render Props Pattern](#4-render-props-pattern)
5. [Error Boundaries](#5-error-boundaries)
6. [Suspense and Lazy Loading](#6-suspense-and-lazy-loading)
7. [React Server Components](#7-react-server-components)
8. [Advanced Patterns](#8-advanced-patterns)
9. [Testing React Applications](#9-testing-react-applications)
10. [Production Best Practices](#10-production-best-practices)

---

## 1. Performance Optimization

### When to Optimize

```
┌─────────────────────────────────────────────────────────────────┐
│                    OPTIMIZATION HIERARCHY                        │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  BEFORE OPTIMIZING:                                              │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │  1. Ensure app works correctly first                      │  │
│  │  2. Measure baseline performance (React DevTools Profiler)│  │
│  │  3. Identify actual bottlenecks                           │  │
│  │  4. Optimize the specific bottleneck                      │  │
│  │  5. Measure again to confirm improvement                  │  │
│  └───────────────────────────────────────────────────────────┘  │
│                                                                  │
│  DON'T OPTIMIZE:                                                │
│  ├─ Components that render rarely                               │
│  ├─ Simple calculations                                         │
│  ├─ Before confirming there's a problem                         │
│  └─ Prematurely (YAGNI principle)                               │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Profiling with React DevTools

```jsx
// 1. Install React DevTools browser extension
// 2. Open your app
// 3. Open DevTools → Profiler tab
// 4. Click "Record" → interact with app → click "Stop"
// 5. Analyze:
//    - Which components rendered?
//    - Why did they render? (what changed?)
//    - How long did rendering take?

// 6. Highlight what caused re-renders:
//    - In Profiler, click on a component
//    - See "Render reason" in the sidebar
```

### Memoization Strategies

```jsx
// React.memo - prevents re-render if props haven't changed
const ExpensiveComponent = React.memo(function ExpensiveComponent({ data }) {
  // Only re-renders when 'data' reference changes
  return <div>{/* expensive rendering */}</div>;
});

// Custom comparison function
const ComponentWithCustomCompare = React.memo(
  ComponentA,
  (prevProps, nextProps) => {
    // Return true if should NOT re-render
    return prevProps.id === nextProps.id &&
           prevProps.name === nextProps.name;
  }
);

// useMemo - memoize expensive calculations
function DataTable({ items, filter }) {
  const filteredAndSorted = useMemo(() => {
    console.log("Expensive calculation running...");
    return items
      .filter(item => item.name.includes(filter))
      .sort((a, b) => a.name.localeCompare(b.name));
  }, [items, filter]);

  return <Table data={filteredAndSorted} />;
}

// useCallback - memoize function references
function Parent() {
  const [count, setCount] = useState(0);

  const handleClick = useCallback(() => {
    console.log("Button clicked");
  }, []);

  const handleIncrement = useCallback(() => {
    setCount(c => c + 1);
  }, []);

  return (
    <>
      <MemoizedButton onClick={handleClick} />
      <Counter count={count} onIncrement={handleIncrement} />
    </>
  );
}
```

### List Virtualization

For long lists, render only visible items:

```jsx
// Manual virtualization concept
function VirtualList({ items, height, itemHeight }) {
  const [scrollTop, setScrollTop] = useState(0);
  const containerHeight = height;

  const startIndex = Math.floor(scrollTop / itemHeight);
  const endIndex = Math.min(
    items.length - 1,
    Math.floor((scrollTop + containerHeight) / itemHeight)
  );

  const visibleItems = items.slice(startIndex, endIndex + 1);

  return (
    <div 
      style={{ height, overflow: "auto" }}
      onScroll={(e) => setScrollTop(e.target.scrollTop)}
    >
      <div style={{ height: items.length * itemHeight }}>
        <div style={{ transform: `translateY(${startIndex * itemHeight}px)` }}>
          {visibleItems.map((item, index) => (
            <div key={item.id} style={{ height: itemHeight }}>
              <ListItem item={item} />
            </div>
          ))}
        </div>
      </div>
    </div>
  );
}

// Or use react-window library
import { FixedSizeList } from 'react-window';

function VirtualizedList({ items }) {
  const Row = ({ index, style }) => (
    <div style={style}>
      <ListItem item={items[index]} />
    </div>
  );

  return (
    <FixedSizeList
      height={400}
      width={300}
      itemCount={items.length}
      itemSize={50}
    >
      {Row}
    </FixedSizeList>
  );
}
```

### Code Splitting

```jsx
// Dynamic import for code splitting
const LazyComponent = React.lazy(() => import('./HeavyComponent'));

function App() {
  return (
    <div>
      <Suspense fallback={<LoadingSpinner />}>
        <LazyComponent />
      </Suspense>
    </div>
  );
}

// Route-based splitting
const Home = lazy(() => import('./pages/Home'));
const About = lazy(() => import('./pages/About'));
const Contact = lazy(() => import('./pages/Contact'));

function App() {
  return (
    <BrowserRouter>
      <Suspense fallback={<Loading />}>
        <Routes>
          <Route path="/" element={<Home />} />
          <Route path="/about" element={<About />} />
          <Route path="/contact" element={<Contact />} />
        </Routes>
      </Suspense>
    </BrowserRouter>
  );
}

// Named exports
const { HeavyFunction } = lazy(() => import('./utils').then(module => ({
  default: () => module.HeavyFunction
})));
```

### State Optimization

```jsx
// Separate state for unrelated concerns
function BadForm() {
  // ❌ All in one state - changes to name re-renders everything
  const [form, setForm] = useState({ name: "", email: "", address: "" });
}

function GoodForm() {
  // ✅ Separate states - only affected components re-render
  const [name, setName] = useState("");
  const [email, setEmail] = useState("");
  const [address, setAddress] = useState("");
}

// Use derived state carefully
function DerivedState() {
  const [items, setItems] = useState([]);
  const [filter, setFilter] = useState("all");

  // ✅ Derived (computed) state - no need to store
  const filteredItems = useMemo(() => {
    if (filter === "all") return items;
    return items.filter(item => item.type === filter);
  }, [items, filter]);

  return <List items={filteredItems} />;
}

// Batch updates
function BatchingDemo() {
  const [x, setX] = useState(0);
  const [y, setY] = useState(0);
  const [data, setData] = useState(null);

  // React 18+ auto-batches all state updates
  const handleClick = () => {
    setX(1);      // Batch
    setY(2);      // Batch
    fetchData().then(setData);  // Batch with Promise
    // Single re-render!
  };
}
```

### Stable References

```jsx
// ❌ Problem: New object/array/function on every render
function ProblematicParent() {
  const [count, setCount] = useState(0);

  const config = {  // New object every render
    limit: 10,
    offset: 0
  };

  const handleClick = () => {  // New function every render
    console.log("clicked");
  };

  return <Child config={config} onClick={handleClick} />;
}

// ✅ Solution 1: useMemo and useCallback
function FixedParent() {
  const [count, setCount] = useState(0);

  const config = useMemo(() => ({
    limit: 10,
    offset: 0
  }), []);

  const handleClick = useCallback(() => {
    console.log("clicked");
  }, []);

  return <Child config={config} onClick={handleClick} />;
}

// ✅ Solution 2: Move state down
function FixedParent() {
  return (
    <>
      <Counter />
      <Child />
    </>
  );
}
```

---

## 2. React.memo and PureComponent

### React.memo

```jsx
// Basic React.memo
const MemoizedComponent = React.memo(function MyComponent({ name, value }) {
  console.log("Rendered");
  return <div>{name}: {value}</div>;
});

// With custom comparison
const MemoizedWithCompare = React.memo(
  function MyComponent({ user, items }) {
    return <div>{/* ... */}</div>;
  },
  (prevProps, nextProps) => {
    // Return true if should NOT re-render
    // Compare specific props that matter
    return prevProps.user.id === nextProps.user.id &&
           prevProps.items.length === nextProps.items.length;
  }
);
```

### When React.memo Re-renders

```jsx
// React.memo compares props using shallow comparison
// Primitives: a === b
// Objects/Arrays/Functions: a === b (reference equality)

// ❌ Problem: Props are always "different"
function Parent() {
  const [count, setCount] = useState(0);

  return (
    <>
      <CountDisplay count={count} />
      {/* ❌ This re-renders every time because config is new object */}
      <ConfigDisplay config={{ theme: "dark" }} />
      {/* ❌ This re-renders every time because handleClick is new function */}
      <Button onClick={() => console.log("clicked")} />
    </>
  );
}

// ✅ Solution
function FixedParent() {
  const [count, setCount] = useState(0);
  
  const config = useMemo(() => ({ theme: "dark" }), []);
  const handleClick = useCallback(() => console.log("clicked"), []);

  return (
    <>
      <CountDisplay count={count} />
      <ConfigDisplay config={config} />
      <Button onClick={handleClick} />
    </>
  );
}
```

### PureComponent (Class Components)

```jsx
// PureComponent does shallow comparison of state and props
class MyComponent extends PureComponent {
  render() {
    // Only re-renders if props or state actually changed
    return <div>{this.props.name}</div>;
  }
}

// Equivalent to:
class MyComponent extends Component {
  shouldComponentUpdate(nextProps, nextState) {
    return !shallowEqual(this.props, nextProps) ||
           !shallowEqual(this.state, nextState);
  }
  
  render() {
    return <div>{this.props.name}</div>;
  }
}
```

### Memoization Comparison

| Technique | What it memoizes | Triggers re-render when |
|-----------|-----------------|------------------------|
| React.memo | Component | Props change (shallow comparison) |
| useMemo | Computed value | Dependencies change |
| useCallback | Function reference | Dependencies change |
| PureComponent | Class component | State/props change |

---

## 3. Higher-Order Components (HOCs)

### What is a HOC?

A Higher-Order Component is a function that takes a component and returns a new component with enhanced functionality.

```
┌─────────────────────────────────────────────────────────────────┐
│                    HOC PATTERN                                   │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  Higher-Order Component                                          │
│       │                                                         │
│       ▼                                                         │
│  ┌────────────────────────────────────────────────────────────┐ │
│  │ function withSubscription(WrappedComponent) {            │ │
│  │   return function EnhancedComponent(props) {               │ │
│  │     const data = useSubscription();                       │ │
│  │     return <WrappedComponent {...props} data={data} />;  │ │
│  │   }                                                        │ │
│  │ }                                                          │ │
│  └────────────────────────────────────────────────────────────┘ │
│       │                                                         │
│       ▼                                                         │
│  Usage:                                                          │
│  const DataComponent = withSubscription(DataDisplay);           │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### HOC Examples

```jsx
// HOC 1: Authentication wrapper
function withAuth(WrappedComponent) {
  return function AuthenticatedComponent(props) {
    const { user, loading } = useAuth();

    if (loading) return <LoadingSpinner />;
    if (!user) return <Navigate to="/login" />;

    return <WrappedComponent {...props} user={user} />;
  };
}

// Usage
const ProtectedDashboard = withAuth(Dashboard);
const ProtectedSettings = withAuth(Settings);

// HOC 2: Loading state
function withLoading(WrappedComponent) {
  return function LoadingWrapper({ isLoading, ...props }) {
    if (isLoading) return <Skeleton />;
    return <WrappedComponent {...props} />;
  };
}

// Usage
const LoadingUserProfile = withLoading(UserProfile);

// HOC 3: Logging
function withLogger(WrappedComponent) {
  return function Logger(props) {
    useEffect(() => {
      console.log(`${WrappedComponent.name} mounted`);
      return () => console.log(`${WrappedComponent.name} unmounted`);
    }, []);

    return <WrappedComponent {...props} />;
  };
}
```

### HOC with Configuration

```jsx
function withTiming(WrappedComponent, options = {}) {
  const {
    enableLogging = false,
    trackRender = false
  } = options;

  return function TimedComponent(props) {
    const renderTimeRef = useRef(0);

    useEffect(() => {
      if (enableLogging) {
        console.log(`${WrappedComponent.name} rendered`);
      }
    });

    if (trackRender) {
      renderTimeRef.current += 1;
    }

    return <WrappedComponent {...props} renderCount={renderTimeRef.current} />;
  };
}

// Usage
const TrackedDashboard = withTiming(Dashboard, {
  enableLogging: true,
  trackRender: true
});
```

### HOC vs Custom Hooks

```
┌─────────────────────────────────────────────────────────────────┐
│                    HOC vs CUSTOM HOOK                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  USE HOC WHEN:                                                   │
│  ├─ You need to wrap the component (add JSX)                     │
│  ├─ You need to modify the component's behavior                  │
│  ├─ You need to inject props into the component                  │
│  └─ You're enhancing class components                            │
│                                                                  │
│  USE CUSTOM HOOK WHEN:                                           │
│  ├─ You just need to share stateful logic                       │
│  ├─ You don't need to modify the component                       │
│  ├─ Hooks pattern is sufficient (modern approach)               │
│  └─ You want simpler, more readable code                        │
│                                                                  │
│  In modern React, prefer custom hooks over HOCs for most cases! │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## 4. Render Props Pattern

### What are Render Props?

A render prop is a function prop that a component uses to know what to render.

```jsx
// Basic render prop pattern
function MouseTracker({ render }) {
  const [position, setPosition] = useState({ x: 0, y: 0 });

  useEffect(() => {
    const handleMouseMove = (e) => {
      setPosition({ x: e.clientX, y: e.clientY });
    };
    window.addEventListener("mousemove", handleMouseMove);
    return () => window.removeEventListener("mousemove", handleMouseMove);
  }, []);

  return render(position);
}

// Usage
<MouseTracker
  render={({ x, y }) => (
    <p>Mouse position: {x}, {y}</p>
  )}
/>
```

### Render Props vs Children

```jsx
// Children as render prop
function Container({ children }) {
  return <div className="container">{children}</div>;
}

// Explicit render prop
function Container({ render }) {
  return <div className="container">{render()}</div>;
}

// HOC disguised as render prop
function withHOC(WrappedComponent) {
  return function EnhancedComponent(props) {
    return <WrappedComponent {...props} enhanced={true} />;
  };
}
```

### Real-World Render Props

```jsx
// DataFetcher with render prop
function DataFetcher({ url, render }) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    fetch(url)
      .then(res => res.json())
      .then(setData)
      .catch(setError)
      .finally(() => setLoading(false));
  }, [url]);

  return render({ data, loading, error });
}

// Usage
<DataFetcher 
  url="/api/users"
  render={({ data, loading, error }) => {
    if (loading) return <p>Loading...</p>;
    if (error) return <p>Error: {error}</p>;
    return <UserList users={data} />;
  }}
/>

// Compound component with render props
function Tabs({ children }) {
  const [activeTab, setActiveTab] = useState();
  
  const value = { activeTab, setActiveTab };
  
  return children(value);
}

Tabs.Panel = function Panel({ tab, children }) {
  const { activeTab } = useContext(TabsContext);
  return activeTab === tab ? children : null;
};

// Usage
<Tabs>
  {({ activeTab, setActiveTab }) => (
    <>
      <button onClick={() => setActiveTab("home")}>Home</button>
      <button onClick={() => setActiveTab("about")}>About</button>
      <Tabs.Panel tab="home">Home Content</Tabs.Panel>
      <Tabs.Panel tab="about">About Content</Tabs.Panel>
    </>
  )}
</Tabs>
```

### Render Props vs HOC vs Custom Hooks

| Pattern | Pros | Cons |
|---------|------|------|
| **Render Props** | Explicit data flow, flexible | Can lead to "prop soup" |
| **HOC** | Clean component tree | Hard to understand origin of props |
| **Custom Hooks** | Simple, modern, reusable logic | Can't modify component rendering |
| **Context + Hooks** | Avoid prop drilling | Context has overhead |

---

## 5. Error Boundaries

### What are Error Boundaries?

Error boundaries are React components that catch JavaScript errors anywhere in their child component tree, log those errors, and display a fallback UI.

```jsx
// Error Boundary Class Component
class ErrorBoundary extends Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false, error: null, errorInfo: null };
  }

  static getDerivedStateFromError(error) {
    // Update state to show fallback UI
    return { hasError: true, error };
  }

  componentDidCatch(error, errorInfo) {
    // Log error to an error reporting service
    logErrorToService(error, errorInfo);
    console.error("ErrorBoundary caught:", error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return this.props.fallback || (
        <div>
          <h1>Something went wrong.</h1>
          <details>
            <summary>Error details</summary>
            {this.state.error?.toString()}
          </details>
        </div>
      );
    }

    return this.props.children;
  }
}

// Usage
function App() {
  return (
    <ErrorBoundary>
      <MyComponent />
    </ErrorBoundary>
  );
}

// Multiple error boundaries
function App() {
  return (
    <ErrorBoundary>
      <Header />
    </ErrorBoundary>
    <ErrorBoundary>
      <Sidebar />
    </ErrorBoundary>
    <ErrorBoundary>
      <MainContent />
    </ErrorBoundary>
  );
}
```

### Error Boundary Limitations

```
┌─────────────────────────────────────────────────────────────────┐
│                  ERROR BOUNDARY LIMITATIONS                      │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  Error boundaries DO catch:                                      │
│  ├─ JavaScript errors in components                             │
│  ├─ Errors in render phase                                      │
│  ├─ Errors in lifecycle methods                                 │
│  ├─ Errors in constructors                                     │
│  └─ Errors in child components                                  │
│                                                                  │
│  Error boundaries DON'T catch:                                   │
│  ├─ Event handlers (use try/catch)                             │
│  ├─ Async code (setTimeout, promises)                          │
│  ├─ Server-side rendering (SSR)                                │
│  ├─ Errors in the boundary itself                               │
│  └─ TypeScript compilation errors                               │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Combining Error Boundaries with React Router

```jsx
// Global error boundary
class GlobalErrorBoundary extends Component {
  render() {
    return (
      <ErrorBoundary>
        {this.props.children}
      </ErrorBoundary>
    );
  }
}

// Route-level error boundaries
function App() {
  return (
    <GlobalErrorBoundary>
      <Routes>
        <Route path="/" element={<Home />} />
        <Route 
          path="/dashboard" 
          element={
            <ErrorBoundary fallback={<DashboardError />}>
              <Dashboard />
            </ErrorBoundary>
          } 
        />
      </Routes>
    </GlobalErrorBoundary>
  );
}
```

---

## 6. Suspense and Lazy Loading

### React.lazy and Suspense

```jsx
import { Suspense, lazy } from 'react';

// Lazy load components
const Dashboard = lazy(() => import('./pages/Dashboard'));
const Settings = lazy(() => import('./pages/Settings'));
const Profile = lazy(() => import('./pages/Profile'));

// Code splitting with error handling
const AsyncComponent = lazy(() => 
  import('./HeavyComponent')
    .catch(err => {
      console.error("Failed to load:", err);
      return { default: () => <ErrorFallback /> };
    })
);

function App() {
  return (
    <Suspense fallback={<LoadingSpinner />}>
      <Routes>
        <Route path="/" element={<Dashboard />} />
        <Route path="/settings" element={<Settings />} />
        <Route path="/profile" element={<Profile />} />
      </Routes>
    </Suspense>
  );
}
```

### Suspense with Data Fetching

```jsx
// Using Suspense for data fetching (React 18+)
import { Suspense } from 'react';

// Resource-based data fetching
function fetchUser(id) {
  let status = "pending";
  let result;
  let promise = fetch(`/api/users/${id}`)
    .then(res => res.json())
    .then(
      data => {
        status = "fulfilled";
        result = data;
      },
      err => {
        status = "rejected";
        result = err;
      }
    );

  return () => {
    if (status === "pending") throw promise;
    if (status === "rejected") throw result;
    return result;
  };
}

function UserProfile({ userId }) {
  const user = fetchUser(userId);  // Throws promise if loading
  
  return <div>{user.name}</div>;
}

function App() {
  return (
    <ErrorBoundary fallback={<ErrorPage />}>
      <Suspense fallback={<UserSkeleton />}>
        <UserProfile userId={1} />
      </Suspense>
    </ErrorBoundary>
  );
}
```

### Multiple Suspense Boundaries

```jsx
function App() {
  return (
    <div>
      <Header />
      
      {/* Separate boundary for sidebar */}
      <Suspense fallback={<SidebarSkeleton />}>
        <Sidebar />
      </Suspense>
      
      {/* Separate boundary for main content */}
      <Suspense fallback={<ContentSkeleton />}>
        <MainContent />
      </Suspense>
      
      {/* If sidebar fails, main content still works */}
      <Footer />
    </div>
  );
}
```

### useDeferredValue with Suspense

```jsx
import { useState, useDeferredValue, Suspense } from 'react';

function SearchApp() {
  const [query, setQuery] = useState("");
  const deferredQuery = useDeferredValue(query);

  return (
    <div>
      <input 
        value={query}
        onChange={(e) => setQuery(e.target.value)}
        placeholder="Search..."
      />
      
      <Suspense fallback={<ResultsSkeleton />}>
        <SearchResults query={deferredQuery} />
      </Suspense>
    </div>
  );
}
```

---

## 7. React Server Components

### Overview

React Server Components (RSC) allow components to run on the server, reducing client-side JavaScript and improving performance.

```
┌─────────────────────────────────────────────────────────────────┐
│                 SERVER vs CLIENT COMPONENTS                      │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  SERVER COMPONENTS                    CLIENT COMPONENTS         │
│  ├─ Run on server                    ├─ Run in browser         │
│  ├─ Can access database directly     ├─ Can use hooks          │
│  ├─ No client-side JS bundle         ├─ Can have interactivity│
│  ├─ Cannot use useState/useEffect    ├─ Can use event handlers │
│  ├─ Cannot use browser APIs          ├─ Limited to client     │
│  └─ Default in Next.js App Router    └─ Marked with "use client"│
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Server Components

```jsx
// ServerComponent.js (runs on server)
import { db } from './database';

async function ServerComponent() {
  // Direct database access
  const data = await db.query('SELECT * FROM posts');
  
  return (
    <div>
      {data.map(post => (
        <Post key={post.id} {...post} />
      ))}
    </div>
  );
}

export default ServerComponent;
```

### Client Components

```jsx
// ClientComponent.js (runs in browser)
"use client";

import { useState } from 'react';

function ClientComponent() {
  const [count, setCount] = useState(0);
  
  return (
    <button onClick={() => setCount(c => c + 1)}>
      Clicked {count} times
    </button>
  );
}

export default ClientComponent;
```

### Mixing Server and Client Components

```jsx
// Server Component (parent)
async function Page() {
  const data = await fetchData();  // Server-side fetch
  
  return (
    <div>
      {/* Server component - renders data */}
      <DataDisplay data={data} />
      
      {/* Client component - handles interactivity */}
      <InteractiveCounter />
    </div>
  );
}

// InteractiveCounter.js
"use client";
import { useState } from 'react';

export function InteractiveCounter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(c => c + 1)}>{count}</button>;
}
```

---

## 8. Advanced Patterns

### Compound Components

```jsx
// Building a compound component
const SelectContext = createContext();

function Select({ children, value, onChange }) {
  return (
    <SelectContext.Provider value={{ value, onChange }}>
      <div className="select">{children}</div>
    </SelectContext.Provider>
  );
}

Select.Option = function Option({ value, children }) {
  const { value: selectedValue, onChange } = useContext(SelectContext);
  const isSelected = selectedValue === value;

  return (
    <div
      className={`option ${isSelected ? 'selected' : ''}`}
      onClick={() => onChange(value)}
    >
      {children}
    </div>
  );
};

// Usage
<Select value={color} onChange={setColor}>
  <Select.Option value="red">Red</Select.Option>
  <Select.Option value="blue">Blue</Select.Option>
</Select>
```

### Provider Pattern

```jsx
// Creating a feature-focused provider
function ThemeProvider({ children, initialTheme = "light" }) {
  const [theme, setTheme] = useState(initialTheme);
  const [fontSize, setFontSize] = useState(16);

  const toggleTheme = () => {
    setTheme(t => t === "light" ? "dark" : "light");
  };

  const value = useMemo(() => ({
    theme,
    setTheme,
    toggleTheme,
    fontSize,
    setFontSize
  }), [theme, fontSize]);

  return (
    <ThemeContext.Provider value={value}>
      <div data-theme={theme} style={{ fontSize }}>
        {children}
      </div>
    </ThemeContext.Provider>
  );
}

// Custom hook for theme
function useTheme() {
  const context = useContext(ThemeContext);
  if (!context) {
    throw new Error("useTheme must be used within ThemeProvider");
  }
  return context;
}
```

### State Reducer Pattern

```jsx
// State reducer for custom state logic
function useToggle(initialState = false) {
  const [on, setOn] = useState(initialState);
  
  const toggle = (newState) => {
    if (typeof newState === 'function') {
      setOn(prev => {
        const result = newState(prev);
        return result;
      });
    } else {
      setOn(newState);
    }
  };

  return {
    on,
    toggle,
    setOn,
    reset: () => setOn(initialState)
  };
}

// Custom hook that accepts state reducer
function useToggleWithReducer({ initialState = false, reducer = (state) => state } = {}) {
  const [state, dispatch] = useReducer(reducer, initialState);

  const toggle = () => dispatch({ type: "TOGGLE" });
  const setOn = () => dispatch({ type: "SET_ON" });
  const setOff = () => dispatch({ type: "SET_OFF" });

  return { on: state, toggle, setOn, setOff };
}
```

### Control Props Pattern

```jsx
// Controlled + uncontrolled component
function Combobox({ 
  value: controlledValue, 
  onChange: onChangeHandler,
  options,
  ...props 
}) {
  const [internalValue, setInternalValue] = useState("");
  
  // Determine if controlled or uncontrolled
  const isControlled = controlledValue !== undefined;
  const value = isControlled ? controlledValue : internalValue;
  
  const handleChange = (newValue) => {
    if (!isControlled) {
      setInternalValue(newValue);
    }
    onChangeHandler?.(newValue);
  };

  return (
    <div {...props}>
      {options.map(opt => (
        <div 
          key={opt.value}
          onClick={() => handleChange(opt.value)}
          className={opt.value === value ? 'selected' : ''}
        >
          {opt.label}
        </div>
      ))}
    </div>
  );
}
```

### Recursive Components

```jsx
// Recursive tree rendering
function TreeNode({ node }) {
  const hasChildren = node.children && node.children.length > 0;

  return (
    <div>
      <div className="node">
        {node.name}
        {hasChildren && (
          <button onClick={() => toggle(node.id)}>
            {node.isOpen ? '▼' : '▶'}
          </button>
        )}
      </div>
      
      {hasChildren && node.isOpen && (
        <div className="children">
          {node.children.map(child => (
            <TreeNode key={child.id} node={child} />
          ))}
        </div>
      )}
    </div>
  );
}

// Recursive component with render prop
function RecursiveList({ items, children, keyExtractor }) {
  if (!items || items.length === 0) return null;

  return (
    <ul>
      {items.map(item => (
        <li key={keyExtractor(item)}>
          {children(item)}
          {item.children && (
            <RecursiveList 
              items={item.children} 
              children={children}
              keyExtractor={keyExtractor}
            />
          )}
        </li>
      ))}
    </ul>
  );
}
```

### State Machine Pattern

```jsx
// Simple state machine for UI states
const STATES = {
  IDLE: 'idle',
  LOADING: 'loading',
  SUCCESS: 'success',
  ERROR: 'error'
};

const TRANSITIONS = {
  [STATES.IDLE]: { FETCH: STATES.LOADING },
  [STATES.LOADING]: { 
    SUCCESS: STATES.SUCCESS, 
    ERROR: STATES.ERROR 
  },
  [STATES.SUCCESS]: { RESET: STATES.IDLE },
  [STATES.ERROR]: { RETRY: STATES.LOADING, RESET: STATES.IDLE }
};

function useStateMachine(initialState) {
  const [state, setState] = useState(initialState);

  const dispatch = (actionType) => {
    const nextState = TRANSITIONS[state]?.[actionType];
    if (nextState) {
      setState(nextState);
    }
  };

  return [state, dispatch];
}

// Usage
function DataFetcher({ url }) {
  const [state, dispatch] = useStateMachine(STATES.IDLE);

  const fetchData = async () => {
    dispatch('FETCH');
    try {
      await fetch(url);
      dispatch('SUCCESS');
    } catch {
      dispatch('ERROR');
    }
  };

  return (
    <div>
      <p>State: {state}</p>
      {state === STATES.IDLE && <button onClick={fetchData}>Load</button>}
      {state === STATES.LOADING && <p>Loading...</p>}
      {state === STATES.SUCCESS && <p>Success!</p>}
      {state === STATES.ERROR && (
        <div>
          <p>Error!</p>
          <button onClick={() => dispatch('RETRY')}>Retry</button>
        </div>
      )}
    </div>
  );
}
```

---

## 9. Testing React Applications

### Testing Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                    TESTING PYRAMID                               │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│                         ┌───────┐                               │
│                        │  E2E  │  Few, slow                    │
│                       ┌┴───────┴┐                               │
│                      │ Integration │                            │
│                     ┌┴───────────┴┐                             │
│                    │   Unit Tests  │ Many, fast                 │
│                   ┌┴─────────────┴┐                             │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### Testing Library Setup

```bash
npm install --save-dev @testing-library/react @testing-library/jest-dom
```

### Basic Tests

```jsx
// Button.test.js
import { render, screen, fireEvent } from '@testing-library/react';
import Button from './Button';

test('renders button with text', () => {
  render(<Button>Click me</Button>);
  expect(screen.getByText('Click me')).toBeInTheDocument();
});

test('calls onClick when clicked', () => {
  const handleClick = jest.fn();
  render(<Button onClick={handleClick}>Click</Button>);
  
  fireEvent.click(screen.getByText('Click'));
  
  expect(handleClick).toHaveBeenCalledTimes(1);
});

test('is disabled when disabled prop is true', () => {
  render(<Button disabled>Disabled</Button>);
  
  expect(screen.getByText('Disabled')).toBeDisabled();
});
```

### Testing Form Components

```jsx
// LoginForm.test.js
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import LoginForm from './LoginForm';

test('submits form with correct values', async () => {
  const handleSubmit = jest.fn();
  render(<LoginForm onSubmit={handleSubmit} />);
  
  // Type into inputs
  userEvent.type(screen.getByLabelText(/email/i), 'test@example.com');
  userEvent.type(screen.getByLabelText(/password/i), 'password123');
  
  // Submit form
  fireEvent.click(screen.getByRole('button', { name: /submit/i }));
  
  expect(handleSubmit).toHaveBeenCalledWith({
    email: 'test@example.com',
    password: 'password123'
  });
});

test('shows validation error for invalid email', async () => {
  render(<LoginForm />);
  
  userEvent.type(screen.getByLabelText(/email/i), 'invalid-email');
  fireEvent.blur(screen.getByLabelText(/email/i));
  
  expect(await screen.findByText(/valid email/i)).toBeInTheDocument();
});
```

### Testing Async Operations

```jsx
// UserList.test.js
import { render, screen, waitFor } from '@testing-library/react';
import { rest } from 'msw';
import { setupServer } from 'msw/node';
import UserList from './UserList';

// Mock API server
const server = setupServer(
  rest.get('/api/users', (req, res, ctx) => {
    return res(ctx.json([
      { id: 1, name: 'Alice' },
      { id: 2, name: 'Bob' }
    ]));
  })
);

beforeAll(() => server.listen());
afterEach(() => server.resetHandlers());
afterAll(() => server.close());

test('loads and displays users', async () => {
  render(<UserList />);
  
  // Wait for loading to finish
  await waitFor(() => {
    expect(screen.getByText('Alice')).toBeInTheDocument();
  });
  
  expect(screen.getByText('Bob')).toBeInTheDocument();
});

test('handles server error', async () => {
  server.use(
    rest.get('/api/users', (req, res, ctx) => {
      return res(ctx.status(500), ctx.json({ message: 'Server Error' }));
    })
  );
  
  render(<UserList />);
  
  await waitFor(() => {
    expect(screen.getByText(/error/i)).toBeInTheDocument();
  });
});
```

### Testing Custom Hooks

```jsx
// useCounter.test.js
import { renderHook, act } from '@testing-library/react';
import { useCounter } from './useCounter';

test('increments counter', () => {
  const { result } = renderHook(() => useCounter());
  
  act(() => {
    result.current.increment();
  });
  
  expect(result.current.count).toBe(1);
});

test('decrements counter', () => {
  const { result } = renderHook(() => useCounter({ initial: 5 }));
  
  act(() => {
    result.current.decrement();
  });
  
  expect(result.current.count).toBe(4);
});

test('resets counter', () => {
  const { result } = renderHook(() => useCounter({ initial: 10 }));
  
  act(() => {
    result.current.increment();
    result.current.increment();
    result.current.reset();
  });
  
  expect(result.current.count).toBe(10);
});
```

### Testing Best Practices

```jsx
// DO:
// ✅ Test behavior, not implementation
// ✅ Use getByRole, getByLabelText over getByTestId when possible
// ✅ Test accessibility (screen readers)
// ✅ Test edge cases and error states
// ✅ Keep tests independent

// DON'T:
// ❌ Test implementation details (internal state, methods)
// ❌ Over-specify tests (test one thing per test)
// ❌ Use getByTestId for everything (flaky)
// ❌ Test third-party libraries
```

---

## 10. Production Best Practices

### Code Splitting Strategy

```jsx
// Route-based splitting (automatic with React.lazy)
const Dashboard = lazy(() => import('./pages/Dashboard'));
const Settings = lazy(() => import('./pages/Settings'));

// Component-based splitting for heavy components
const RichTextEditor = lazy(() => import('./components/RichTextEditor'));
const Chart = lazy(() => import('./components/Chart'));

// Conditional splitting
const isAdmin = user.role === 'admin';
const AdminPanel = lazy(() => import('./AdminPanel'));
```

### Bundle Size Optimization

```jsx
// Check bundle size with source-map-explorer
// npm install --save-dev source-map-explorer
// npx source-map-explorer dist/*.js

// Common size issues:
import _ from 'lodash';           // ❌ Imports entire library (~70kb)
import { groupBy } from 'lodash'; // ✅ Import specific function (~5kb)
import moment from 'moment';       // ❌ Full moment (~300kb)
import dayjs from 'dayjs';        // ✅ Smaller alternative (~2kb)
```

### Error Monitoring

```jsx
// Global error handler for production
class ErrorBoundary extends Component {
  componentDidCatch(error, errorInfo) {
    // Send to error tracking service
    errorTrackingService.log({
      error: error.toString(),
      stack: error.stack,
      componentStack: errorInfo.componentStack,
      timestamp: new Date().toISOString(),
      userId: getCurrentUserId(),
      url: window.location.href
    });
  }
}

// Unhandled promise rejection handler
window.addEventListener('unhandledrejection', (event) => {
  analytics.track('unhandled_rejection', {
    error: event.reason?.message,
    stack: event.reason?.stack
  });
});
```

### Performance Monitoring

```jsx
// Core Web Vitals tracking
import { getCLS, getFID, getLCP } from 'web-vitals';

function sendToAnalytics({ name, delta, id }) {
  analytics.track(name, {
    metric_delta: delta,
    metric_id: id
  });
}

getCLS(sendToAnalytics);
getFID(sendToAnalytics);
getLCP(sendToAnalytics);

// React Profiler for internal metrics
import { Profiler } from 'react';

function onRender(
  id,
  phase,
  actualDuration,
  baseDuration,
  startTime,
  commitTime,
  interactions
) {
  if (actualDuration > baseDuration) {
    console.log(`${id} is slow: ${actualDuration}ms`);
  }
}

<Profiler id="MyComponent" onRender={onRender}>
  <MyComponent />
</Profiler>
```

### Accessibility (a11y)

```jsx
// Semantic HTML
function Nav() {
  return (
    <nav aria-label="Main">
      <ul>
        <li><a href="/" aria-current="page">Home</a></li>
      </ul>
    </nav>
  );
}

// Focus management
function Modal({ isOpen, onClose, children }) {
  const modalRef = useRef(null);
  
  useEffect(() => {
    if (isOpen) {
      modalRef.current?.focus();
      document.body.style.overflow = 'hidden';
    } else {
      document.body.style.overflow = '';
    }
  }, [isOpen]);

  return (
    <div 
      role="dialog"
      aria-modal="true"
      aria-labelledby="modal-title"
      ref={modalRef}
      tabIndex={-1}
    >
      <h2 id="modal-title">Modal Title</h2>
      {children}
      <button onClick={onClose} aria-label="Close modal">×</button>
    </div>
  );
}

// Keyboard navigation
function Dropdown({ options, value, onChange }) {
  const [isOpen, setIsOpen] = useState(false);
  const [focusedIndex, setFocusedIndex] = useState(0);

  const handleKeyDown = (e) => {
    switch (e.key) {
      case 'ArrowDown':
        setFocusedIndex(i => Math.min(i + 1, options.length - 1));
        break;
      case 'ArrowUp':
        setFocusedIndex(i => Math.max(i - 1, 0));
        break;
      case 'Enter':
        onChange(options[focusedIndex].value);
        setIsOpen(false);
        break;
      case 'Escape':
        setIsOpen(false);
        break;
    }
  };

  return (
    <div onKeyDown={handleKeyDown}>
      {/* Dropdown implementation */}
    </div>
  );
}
```

---

## Quick Reference

### Optimization Checklist

```
□ Profile first before optimizing
□ Use React.memo for pure components that re-render often
□ Use useMemo for expensive calculations
□ Use useCallback for callbacks passed to memoized children
□ Lazy load components with React.lazy
□ Virtualize long lists
□ Separate state that changes at different rates
□ Memoize Context value if causing unnecessary renders
□ Avoid creating new objects/functions in render
□ Consider useDeferredValue for non-urgent updates
```

### React 18+ Features

```
□ Automatic batching with useTransition
□ useDeferredValue for debouncing values
□ Concurrent rendering
□ Suspense for data fetching
□ Server Components
□ New root API (createRoot)
□ Improved Strict Mode
```

---

## Next Steps

You're now equipped with expert-level React knowledge. To solidify your understanding:
- **[React_Interview_QA.md](React_Interview_QA.md)** - Common interview questions with detailed answers
- Practice building complex applications
- Contribute to open-source React projects
- Read React source code for deeper understanding
