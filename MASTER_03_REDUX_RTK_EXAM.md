# ⚡ MASTER FILE 3: REDUX + RTK + EXAM INTENSIVE
### 📝 Exam-Optimized Revision | 40 MCQs + 20 Coding Questions + Night-Before Rapid Revision

---

## 1. REDUX FLOW DIAGRAM EXPLANATION

```
                    ┌────────────────────────────────────┐
                    │           REDUX FLOW               │
                    │                                    │
   User clicks →  ACTION  → dispatch(action) → REDUCER  → NEW STATE → Re-render UI
                    │                                    │
                    │  action = { type, payload }        │
                    │  reducer(currentState, action)     │
                    │  returns NEW state (pure function) │
                    └────────────────────────────────────┘
```

### Step-by-Step Flow:
1. **User interaction** (click, submit, etc.) triggers an **action**
2. **Action** = plain JS object: `{ type: "INCREMENT", payload: 5 }`
3. `dispatch(action)` sends action to the **store**
4. Store forwards action to the **reducer**
5. **Reducer** = pure function: `(currentState, action) => newState`
6. Store updates with **new state**
7. All subscribed components **re-render** with new data

### Three Core Principles:
| Principle | Meaning |
|-----------|---------|
| **Single Source of Truth** | Entire app state in ONE store object |
| **State is Read-Only** | Cannot mutate directly; only through dispatching actions |
| **Pure Reducers** | Reducers are pure functions (no side effects, no API calls) |

---

## 2. SYNTAX CHEAT SHEET

### Classic Redux Setup (Verbose)
```jsx
// 1. ACTION TYPES
const INCREMENT = "INCREMENT";
const DECREMENT = "DECREMENT";

// 2. ACTION CREATORS
const increment = () => ({ type: INCREMENT });
const decrement = () => ({ type: DECREMENT });

// 3. REDUCER
const initialState = { count: 0 };
function counterReducer(state = initialState, action) {
  switch (action.type) {
    case INCREMENT: return { ...state, count: state.count + 1 };
    case DECREMENT: return { ...state, count: state.count - 1 };
    default: return state;
  }
}

// 4. STORE
import { createStore } from 'redux';
const store = createStore(counterReducer);

// 5. PROVIDER (wrap app)
import { Provider } from 'react-redux';
<Provider store={store}><App /></Provider>

// 6. CONNECT COMPONENT
import { useSelector, useDispatch } from 'react-redux';
function Counter() {
  const count = useSelector(state => state.count);
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

### Redux Toolkit Setup (Modern — Use This!)
```jsx
// 1. SLICE (replaces action types + creators + reducer)
import { createSlice, configureStore } from '@reduxjs/toolkit';

const counterSlice = createSlice({
  name: 'counter',
  initialState: { count: 0 },
  reducers: {
    increment: (state) => { state.count += 1; },      // Immer allows "mutation"
    decrement: (state) => { state.count -= 1; },
    addBy: (state, action) => { state.count += action.payload; },
    reset: (state) => { state.count = 0; }
  }
});

export const { increment, decrement, addBy, reset } = counterSlice.actions;

// 2. STORE
const store = configureStore({
  reducer: {
    counter: counterSlice.reducer  // key = slice name
  }
});
export default store;

// 3. PROVIDER (same as classic)
import { Provider } from 'react-redux';
<Provider store={store}><App /></Provider>

// 4. COMPONENT
import { useSelector, useDispatch } from 'react-redux';
import { increment, decrement, addBy } from './store';

function Counter() {
  const count = useSelector(state => state.counter.count); // note: state.counter
  const dispatch = useDispatch();
  return (
    <div>
      <p>{count}</p>
      <button onClick={() => dispatch(increment())}>+</button>
      <button onClick={() => dispatch(decrement())}>-</button>
      <button onClick={() => dispatch(addBy(5))}>+5</button>
    </div>
  );
}
```

### createAsyncThunk (Async Operations)
```jsx
import { createSlice, createAsyncThunk, configureStore } from '@reduxjs/toolkit';

// ASYNC THUNK
export const fetchUsers = createAsyncThunk(
  'users/fetchUsers',           // action type prefix
  async () => {
    const res = await fetch('https://jsonplaceholder.typicode.com/users');
    return res.json();           // return value becomes action.payload (fulfilled)
  }
);

// SLICE with extraReducers
const usersSlice = createSlice({
  name: 'users',
  initialState: { users: [], loading: false, error: null },
  reducers: {},                  // no sync reducers needed
  extraReducers: (builder) => {
    builder
      .addCase(fetchUsers.pending, (state) => {
        state.loading = true;
        state.error = null;
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

// STORE
const store = configureStore({
  reducer: { users: usersSlice.reducer }
});

// COMPONENT
function Users() {
  const { users, loading, error } = useSelector(state => state.users);
  const dispatch = useDispatch();

  useEffect(() => {
    dispatch(fetchUsers());
  }, [dispatch]);

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error}</p>;
  return <ul>{users.map(u => <li key={u.id}>{u.name}</li>)}</ul>;
}
```

### RTK Query Setup
```jsx
// 1. API SLICE
import { createApi, fetchBaseQuery } from '@reduxjs/toolkit/query/react';

export const usersApi = createApi({
  reducerPath: 'usersApi',
  baseQuery: fetchBaseQuery({ baseUrl: 'https://jsonplaceholder.typicode.com' }),
  tagTypes: ['Users'],
  endpoints: (builder) => ({
    // GET request (query)
    getUsers: builder.query({
      query: () => '/users',
      providesTags: ['Users']
    }),
    // GET by ID
    getUserById: builder.query({
      query: (id) => `/users/${id}`
    }),
    // POST request (mutation)
    addUser: builder.mutation({
      query: (newUser) => ({
        url: '/users',
        method: 'POST',
        body: newUser
      }),
      invalidatesTags: ['Users']     // refetch getUsers after mutation
    })
  })
});

export const { useGetUsersQuery, useGetUserByIdQuery, useAddUserMutation } = usersApi;

// 2. STORE (include API middleware)
import { configureStore } from '@reduxjs/toolkit';
import { usersApi } from './usersApi';

export const store = configureStore({
  reducer: {
    [usersApi.reducerPath]: usersApi.reducer
  },
  middleware: (getDefaultMiddleware) =>
    getDefaultMiddleware().concat(usersApi.middleware)
});

// 3. COMPONENT — Query
function UserList() {
  const { data: users, error, isLoading } = useGetUsersQuery();
  if (isLoading) return <p>Loading...</p>;
  if (error) return <p>Error!</p>;
  return <ul>{users.map(u => <li key={u.id}>{u.name}</li>)}</ul>;
}

// 4. COMPONENT — Mutation
function AddUser() {
  const [addUser] = useAddUserMutation();
  const [name, setName] = useState('');
  const handleSubmit = async (e) => {
    e.preventDefault();
    await addUser({ name });
    setName('');
  };
  return (
    <form onSubmit={handleSubmit}>
      <input value={name} onChange={e => setName(e.target.value)} />
      <button type="submit">Add</button>
    </form>
  );
}
```

---

## 3. CLASSIC REDUX vs REDUX TOOLKIT COMPARISON

| Feature | Classic Redux | Redux Toolkit (RTK) |
|---------|--------------|---------------------|
| **Boilerplate** | Lots (action types, creators, reducers separately) | Minimal (`createSlice` handles all) |
| **Immutability** | Must manually spread `{ ...state, key: val }` | Immer.js built-in (can "mutate" syntax) |
| **Store setup** | `createStore()` + manual middleware | `configureStore()` (auto includes thunk) |
| **Action creators** | Write manually | Auto-generated from `reducers` object |
| **Action types** | String constants | Auto-generated as `sliceName/reducerName` |
| **Async** | Need `redux-thunk` middleware manually | `createAsyncThunk` built-in |
| **DevTools** | Manual setup | Enabled by default |
| **File structure** | Multiple files (actions, reducers, store) | Single slice file |
| **State access** | `useSelector(state => state.count)` | `useSelector(state => state.sliceName.count)` |
| **Recommended** | ❌ Legacy | ✅ Official recommendation |

### Key Exam Differences:
- ❌ Classic: `const store = createStore(reducer)`
- ✅ RTK: `const store = configureStore({ reducer: { name: slice.reducer } })`
- ❌ Classic: `return { ...state, count: state.count + 1 }` (must spread)
- ✅ RTK: `state.count += 1` (Immer handles immutability)
- ❌ Classic: separate files for actions/reducers
- ✅ RTK: one `createSlice` handles everything

---

## 4. RTK QUERY SIMPLIFIED EXPLANATION

### What RTK Query Does:
RTK Query **automates everything** you'd manually do with `useEffect` + `fetch`:
- ✅ Fetching data
- ✅ Caching responses
- ✅ Loading states (`isLoading`)
- ✅ Error states
- ✅ Automatic refetching
- ✅ Cache invalidation on mutations
- ✅ Polling (auto-refresh)
- ✅ Optimistic updates

### Core Concepts:

| Concept | Description |
|---------|-------------|
| `createApi` | Defines all API endpoints in one place |
| `fetchBaseQuery` | Built-in fetch wrapper (base URL config) |
| `builder.query` | GET requests (read data) |
| `builder.mutation` | POST/PUT/DELETE requests (modify data) |
| `useXxxQuery` | Auto-generated hook for queries |
| `useXxxMutation` | Auto-generated hook for mutations |
| `providesTags` | Marks data as cacheable with tag |
| `invalidatesTags` | Forces refetch of tagged data after mutation |
| `reducerPath` | Unique key in Redux store |
| `middleware` | Must add API middleware to store |

### RTK Query vs createAsyncThunk:
| Feature | createAsyncThunk | RTK Query |
|---------|-----------------|-----------|
| **Setup** | Manual slice + extraReducers | Single API definition |
| **Caching** | Manual | Automatic |
| **Loading/Error** | Manual state management | Built-in (`isLoading`, `error`) |
| **Refetching** | Manual | Automatic (tags, polling) |
| **Code amount** | More | Much less |
| **Best for** | Custom async logic | Standard CRUD API calls |

### RTK Query vs useEffect + fetch:
| Feature | useEffect + fetch | RTK Query |
|---------|------------------|-----------|
| Duplicate API calls | ❌ Each component fetches | ✅ Cached, shared across components |
| Loading/Error state | ❌ Manual per component | ✅ Built-in |
| Caching | ❌ None | ✅ Automatic |
| Refetch on mount | ❌ Manual | ✅ Configurable |
| Polling | ❌ Manual setInterval | ✅ `pollingInterval` option |
| Cache invalidation | ❌ Not possible | ✅ `invalidatesTags` |

### Advanced RTK Query Features:
```jsx
// Polling (auto-refresh every 5 seconds)
const { data } = useGetUsersQuery(undefined, { pollingInterval: 5000 });

// Refetch on mount
const { data } = useGetUsersQuery(undefined, { refetchOnMountOrArgChange: true });

// Skip query conditionally
const { data } = useGetUserByIdQuery(id, { skip: !id });
```

---

## 5. 40 HARD MCQs (WITH ANSWERS)

**Q1.** Redux state is stored in:
- A) Component state
- B) A single centralized store
- C) Context API
- D) Local storage

**Answer: B** — Redux uses a single store to hold all application state.

---

**Q2.** A Redux action is:
- A) A function that modifies state
- B) A plain JavaScript object with a `type` property
- C) An HTML element
- D) A React component

**Answer: B** — Actions are plain objects: `{ type: "ACTION_TYPE", payload: data }`.

---

**Q3.** A Redux reducer must be:
- A) An async function
- B) A pure function (no side effects)
- C) A class method
- D) A React hook

**Answer: B** — Reducers are pure: same input → same output, no API calls, no mutations.

---

**Q4.** What does `dispatch()` do in Redux?
- A) Creates the store
- B) Sends an action to the reducer
- C) Fetches data from API
- D) Updates the DOM directly

**Answer: B** — `dispatch(action)` sends the action to the store's reducer for processing.

---

**Q5.** `useSelector` is used to:
- A) Dispatch actions
- B) Read data from the Redux store
- C) Create a new store
- D) Define reducers

**Answer: B** — `useSelector(state => state.value)` reads store state.

---

**Q6.** `useDispatch` returns:
- A) The current state
- B) A dispatch function
- C) A reducer function
- D) The store object

**Answer: B** — `const dispatch = useDispatch()` → used to dispatch actions.

---

**Q7.** Which component wraps the app to provide Redux store?
- A) `<Store>`
- B) `<Provider store={store}>`
- C) `<Redux>`
- D) `<Context.Provider>`

**Answer: B** — `<Provider store={store}>` from `react-redux`.

---

**Q8.** In Redux Toolkit, `createSlice` returns:
- A) Only a reducer
- B) Only actions
- C) An object with `reducer` and `actions`
- D) A store

**Answer: C** — `createSlice` returns `{ reducer, actions, name }`.

---

**Q9.** In RTK, `state.count += 1` inside a reducer works because:
- A) Redux ignores immutability
- B) Immer.js internally handles immutable updates
- C) It's a bug
- D) React allows mutations

**Answer: B** — RTK uses Immer under the hood, so "mutating" syntax produces immutable updates.

---

**Q10.** `configureStore` vs `createStore`:
- A) Same thing
- B) `configureStore` auto-adds thunk middleware and DevTools
- C) `createStore` is newer
- D) `configureStore` doesn't support middleware

**Answer: B** — `configureStore` is the RTK replacement with built-in thunk + DevTools.

---

**Q11.** `createAsyncThunk` handles:
- A) Sync operations only
- B) Async operations (API calls) with pending/fulfilled/rejected states
- C) Component rendering
- D) Routing

**Answer: B** — It creates three action types: `pending`, `fulfilled`, `rejected`.

---

**Q12.** `extraReducers` in `createSlice` is used for:
- A) Sync reducers
- B) Handling actions defined outside the slice (e.g., createAsyncThunk)
- C) Middleware
- D) Error logging

**Answer: B** — `extraReducers` handles external action types like thunk lifecycle actions.

---

**Q13.** In `extraReducers`, `fetchUsers.pending` action fires:
- A) After data is received
- B) When the async operation starts
- C) On error
- D) On component mount

**Answer: B** — `pending` fires immediately when the thunk is dispatched (before API response).

---

**Q14.** RTK Query's `createApi` replaces the need for:
- A) React components
- B) `useEffect` + `useState` for API calls
- C) JSX
- D) React Router

**Answer: B** — RTK Query automates data fetching, caching, and state management.

---

**Q15.** `fetchBaseQuery` in RTK Query is:
- A) A React hook
- B) A built-in fetch wrapper for configuring base URL
- C) A reducer
- D) A component

**Answer: B** — It wraps the native `fetch` API with a configurable base URL.

---

**Q16.** `builder.query` defines:
- A) POST endpoints
- B) GET endpoints (read data)
- C) DELETE endpoints
- D) WebSocket connections

**Answer: B** — `builder.query` = GET requests; `builder.mutation` = POST/PUT/DELETE.

---

**Q17.** `builder.mutation` defines:
- A) GET endpoints
- B) Endpoints that modify server data (POST, PUT, DELETE)
- C) Reducers
- D) Actions

**Answer: B** — Mutations modify data on the server.

---

**Q18.** `providesTags: ['Users']` means:
- A) The query result is tagged for cache invalidation
- B) The query creates new tags in HTML
- C) The query validates user input
- D) The query runs only for Users

**Answer: A** — Tags are used for cache management. `invalidatesTags` triggers refetch.

---

**Q19.** `invalidatesTags: ['Users']` on a mutation means:
- A) Users are deleted
- B) All queries with `providesTags: ['Users']` get refetched
- C) Nothing happens
- D) Error is thrown

**Answer: B** — After mutation completes, cached queries with matching tags are invalidated and refetched.

---

**Q20.** `useGetUsersQuery()` returns:
- A) `[data, setData]`
- B) `{ data, error, isLoading }`
- C) A promise
- D) A dispatch function

**Answer: B** — RTK Query hooks return an object with `data`, `error`, `isLoading`, `isFetching`, etc.

---

**Q21.** `useAddUserMutation()` returns:
- A) `[data, error]`
- B) `[triggerFunction, { data, isLoading, error }]`
- C) A JSX element
- D) A promise

**Answer: B** — First element is the trigger function; second is the result object.

---

**Q22.** `{ pollingInterval: 5000 }` in RTK Query:
- A) Delays the query by 5 seconds
- B) Automatically refetches data every 5 seconds
- C) Limits data to 5000 items
- D) Times out after 5 seconds

**Answer: B** — Polling re-executes the query at the specified interval.

---

**Q23.** Which must be added to the store for RTK Query to work?
- A) Only the reducer
- B) The reducer AND the middleware
- C) Only the middleware
- D) Nothing extra

**Answer: B** — Both `[api.reducerPath]: api.reducer` and `api.middleware` must be configured.

---

**Q24.** What is `reducerPath` in RTK Query?
- A) The file path
- B) A unique key in the Redux store for this API's data
- C) A URL path
- D) The reducer function

**Answer: B** — It determines where API cache data is stored in the Redux state tree.

---

**Q25.** `Redux Thunk` is:
- A) A reducer
- B) Middleware that allows dispatching functions (async logic)
- C) A component
- D) A hook

**Answer: B** — Thunk middleware intercepts functions passed to `dispatch` and allows async operations.

---

**Q26.** In classic Redux, to update an object in state:
- A) `state.user.name = "new"` (direct mutation)
- B) `return { ...state, user: { ...state.user, name: "new" } }` (spread)
- C) `state = newState`
- D) `this.setState()`

**Answer: B** — Must create new object (immutable update) by spreading.

---

**Q27.** In RTK `createSlice`, to update the same object:
- A) `return { ...state, user: { ...state.user, name: "new" } }`
- B) `state.user.name = "new"` (Immer handles it)
- C) Both work
- D) Neither works

**Answer: C** — Both patterns work in RTK, but B is the simpler syntax enabled by Immer.

---

**Q28.** What does `combineReducers` do in classic Redux?
- A) Merges all actions
- B) Combines multiple reducer functions into a single root reducer
- C) Creates a new store
- D) Dispatches all actions at once

**Answer: B** — `combineReducers({ counter: counterReducer, users: usersReducer })`.

---

**Q29.** In RTK, `combineReducers` is:
- A) Still required
- B) Not needed — `configureStore({ reducer: { ... } })` handles it automatically
- C) Deprecated
- D) A hook

**Answer: B** — `configureStore` accepts a reducer map and combines them internally.

---

**Q30.** `action.payload` in RTK is:
- A) Always undefined
- B) The data passed to the action creator
- C) The action type
- D) The state

**Answer: B** — `dispatch(addBy(5))` → `action.payload === 5`.

---

**Q31.** Default middleware in `configureStore` includes:
- A) Redux Saga
- B) Redux Thunk (for async) + serializable check + immutability check
- C) Express middleware
- D) No middleware

**Answer: B** — RTK includes thunk + development-only checks by default.

---

**Q32.** Can you use `useSelector` without `Provider`?
- A) Yes
- B) No — `useSelector` requires Redux store via Provider

**Answer: B** — Without `<Provider>`, hooks cannot access the store.

---

**Q33.** `createAsyncThunk('users/fetch', async (arg) => {...})` — what is `arg`?
- A) The state
- B) The argument passed when dispatching: `dispatch(fetchUsers(arg))`
- C) The store
- D) The reducer

**Answer: B** — First parameter of the payload creator is the argument from dispatch.

---

**Q34.** RTK Query auto-generates hooks based on:
- A) Component names
- B) Endpoint names (getUsers → useGetUsersQuery)
- C) File names
- D) Action types

**Answer: B** — Convention: `getUsers` → `useGetUsersQuery`, `addUser` → `useAddUserMutation`.

---

**Q35.** Optimistic update in RTK Query means:
- A) Data disappears temporarily
- B) UI updates immediately before server confirms; rolls back on failure
- C) Server updates first
- D) No updates happen

**Answer: B** — Shows the change instantly, reverts if the API call fails.

---

**Q36.** `{ skip: true }` in RTK Query hook:
- A) Skips rendering
- B) Skips the API call (doesn't fetch)
- C) Skips caching
- D) Skips errors

**Answer: B** — Useful for conditional fetching: `{ skip: !userId }`.

---

**Q37.** Context API vs Redux:
- A) Context replaces Redux completely
- B) Context is simpler but lacks middleware, devtools, and performance optimization
- C) Redux is lighter
- D) Context supports time-travel debugging

**Answer: B** — Context works for simple state but Redux excels at complex, large-scale apps.

---

**Q38.** What is the correct order of Redux data flow?
- A) Reducer → Action → Store → Component
- B) Component → Action → Dispatch → Reducer → Store → Component
- C) Store → Component → Action → Reducer
- D) Action → Component → Store → Reducer

**Answer: B** — User interaction → Action → Dispatch → Reducer processes → Store updates → Component re-renders.

---

**Q39.** `createSlice({ name: 'counter', ... })` — the `name` is used for:
- A) CSS class
- B) Auto-generating action type prefixes (`counter/increment`)
- C) Component name
- D) Nothing

**Answer: B** — Action types are `sliceName/reducerName` (e.g., `counter/increment`).

---

**Q40.** Which is TRUE about RTK Query middleware?
- A) It's optional
- B) It handles caching, polling, and lifecycle management
- C) It replaces the reducer
- D) It's the same as Redux Thunk

**Answer: B** — RTK Query middleware manages cache lifecycle, automatic refetching, and polling.

---

## 6. 20 EXAM-STYLE CODING QUESTIONS

**Q1.** Write a Redux Toolkit slice for a counter with increment, decrement, and reset.
```jsx
import { createSlice } from '@reduxjs/toolkit';

const counterSlice = createSlice({
  name: 'counter',
  initialState: { count: 0 },
  reducers: {
    increment: (state) => { state.count += 1; },
    decrement: (state) => { state.count -= 1; },
    reset: (state) => { state.count = 0; }
  }
});

export const { increment, decrement, reset } = counterSlice.actions;
export default counterSlice.reducer;
```

---

**Q2.** Configure the Redux store with the counter slice.
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

---

**Q3.** Wrap App with Provider.
```jsx
import { Provider } from 'react-redux';
import store from './store';
import App from './App';

ReactDOM.createRoot(document.getElementById('root')).render(
  <Provider store={store}>
    <App />
  </Provider>
);
```

---

**Q4.** Write a component that uses `useSelector` and `useDispatch` with the counter slice.
```jsx
import { useSelector, useDispatch } from 'react-redux';
import { increment, decrement, reset } from './counterSlice';

function Counter() {
  const count = useSelector((state) => state.counter.count);
  const dispatch = useDispatch();

  return (
    <div>
      <h1>{count}</h1>
      <button onClick={() => dispatch(increment())}>+</button>
      <button onClick={() => dispatch(decrement())}>-</button>
      <button onClick={() => dispatch(reset())}>Reset</button>
    </div>
  );
}
```

---

**Q5.** Write a `createAsyncThunk` to fetch posts from an API.
```jsx
import { createAsyncThunk } from '@reduxjs/toolkit';

export const fetchPosts = createAsyncThunk(
  'posts/fetchPosts',
  async () => {
    const response = await fetch('https://jsonplaceholder.typicode.com/posts');
    return response.json();
  }
);
```

---

**Q6.** Handle `fetchPosts` in a slice using `extraReducers`.
```jsx
const postsSlice = createSlice({
  name: 'posts',
  initialState: { posts: [], loading: false, error: null },
  reducers: {},
  extraReducers: (builder) => {
    builder
      .addCase(fetchPosts.pending, (state) => {
        state.loading = true;
      })
      .addCase(fetchPosts.fulfilled, (state, action) => {
        state.loading = false;
        state.posts = action.payload;
      })
      .addCase(fetchPosts.rejected, (state, action) => {
        state.loading = false;
        state.error = action.error.message;
      });
  }
});
```

---

**Q7.** Dispatch `fetchPosts` on component mount.
```jsx
function PostList() {
  const { posts, loading, error } = useSelector((state) => state.posts);
  const dispatch = useDispatch();

  useEffect(() => {
    dispatch(fetchPosts());
  }, [dispatch]);

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error}</p>;
  return (
    <ul>
      {posts.map((post) => <li key={post.id}>{post.title}</li>)}
    </ul>
  );
}
```

---

**Q8.** Write a classic Redux reducer (without RTK).
```jsx
const initialState = { todos: [] };

function todoReducer(state = initialState, action) {
  switch (action.type) {
    case 'ADD_TODO':
      return { ...state, todos: [...state.todos, action.payload] };
    case 'REMOVE_TODO':
      return { ...state, todos: state.todos.filter(t => t.id !== action.payload) };
    default:
      return state;
  }
}
```

---

**Q9.** Write classic Redux action creators for the above.
```jsx
const addTodo = (todo) => ({ type: 'ADD_TODO', payload: todo });
const removeTodo = (id) => ({ type: 'REMOVE_TODO', payload: id });
```

---

**Q10.** Create a classic Redux store with thunk middleware.
```jsx
import { createStore, applyMiddleware } from 'redux';
import thunk from 'redux-thunk';

const store = createStore(todoReducer, applyMiddleware(thunk));
```

---

**Q11.** Write an RTK Query API slice to fetch and add users.
```jsx
import { createApi, fetchBaseQuery } from '@reduxjs/toolkit/query/react';

export const usersApi = createApi({
  reducerPath: 'usersApi',
  baseQuery: fetchBaseQuery({ baseUrl: 'https://jsonplaceholder.typicode.com' }),
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

---

**Q12.** Configure store for RTK Query.
```jsx
import { configureStore } from '@reduxjs/toolkit';
import { usersApi } from './usersApi';

const store = configureStore({
  reducer: {
    [usersApi.reducerPath]: usersApi.reducer
  },
  middleware: (getDefaultMiddleware) =>
    getDefaultMiddleware().concat(usersApi.middleware)
});

export default store;
```

---

**Q13.** Use `useGetUsersQuery` in a component.
```jsx
function UserList() {
  const { data: users, isLoading, error } = useGetUsersQuery();

  if (isLoading) return <p>Loading...</p>;
  if (error) return <p>Error!</p>;

  return (
    <ul>
      {users.map(user => <li key={user.id}>{user.name}</li>)}
    </ul>
  );
}
```

---

**Q14.** Use `useAddUserMutation` in a form.
```jsx
function AddUser() {
  const [addUser, { isLoading }] = useAddUserMutation();
  const [name, setName] = useState('');

  const handleSubmit = async (e) => {
    e.preventDefault();
    await addUser({ name });
    setName('');
  };

  return (
    <form onSubmit={handleSubmit}>
      <input value={name} onChange={e => setName(e.target.value)} />
      <button type="submit" disabled={isLoading}>
        {isLoading ? 'Adding...' : 'Add User'}
      </button>
    </form>
  );
}
```

---

**Q15.** Write an RTK slice with `addBy(amount)` action that uses payload.
```jsx
const counterSlice = createSlice({
  name: 'counter',
  initialState: { value: 0 },
  reducers: {
    addBy: (state, action) => {
      state.value += action.payload; // payload = the amount passed
    }
  }
});

// Usage: dispatch(addBy(10)) → action.payload === 10
```

---

**Q16.** RTK Query: Fetch user by dynamic ID.
```jsx
// In API slice endpoints:
getUserById: builder.query({
  query: (id) => `/users/${id}`
})

// In component:
function UserProfile({ userId }) {
  const { data: user, isLoading } = useGetUserByIdQuery(userId);
  if (isLoading) return <p>Loading...</p>;
  return <p>{user.name}</p>;
}
```

---

**Q17.** Enable polling every 3 seconds with RTK Query.
```jsx
function LiveData() {
  const { data } = useGetUsersQuery(undefined, {
    pollingInterval: 3000
  });
  return <pre>{JSON.stringify(data, null, 2)}</pre>;
}
```

---

**Q18.** Write a thunk with `createAsyncThunk` that passes an argument.
```jsx
export const fetchUserById = createAsyncThunk(
  'users/fetchById',
  async (userId) => {
    const response = await fetch(`https://jsonplaceholder.typicode.com/users/${userId}`);
    return response.json();
  }
);

// Dispatch: dispatch(fetchUserById(5))
```

---

**Q19.** Multiple slices in one store.
```jsx
import { configureStore } from '@reduxjs/toolkit';
import counterReducer from './counterSlice';
import usersReducer from './usersSlice';

const store = configureStore({
  reducer: {
    counter: counterReducer,
    users: usersReducer
  }
});
```

---

**Q20.** Convert classic Redux to RTK. Classic version:
```jsx
// CLASSIC:
const INCREMENT = 'INCREMENT';
const increment = () => ({ type: INCREMENT });
function reducer(state = { count: 0 }, action) {
  switch(action.type) {
    case INCREMENT: return { ...state, count: state.count + 1 };
    default: return state;
  }
}
const store = createStore(reducer);

// RTK EQUIVALENT:
const counterSlice = createSlice({
  name: 'counter',
  initialState: { count: 0 },
  reducers: {
    increment: (state) => { state.count += 1; }
  }
});
export const { increment } = counterSlice.actions;
const store = configureStore({ reducer: { counter: counterSlice.reducer } });
```

---

## 7. PROFESSOR FAVORITE TRAPS 🎯

### Trap 1: State Mutation
```jsx
// ❌ WRONG (classic Redux)
case 'ADD': state.items.push(action.payload); return state;
// ✅ CORRECT
case 'ADD': return { ...state, items: [...state.items, action.payload] };
// ✅ IN RTK — push is fine (Immer handles it)
reducers: { add: (state, action) => { state.items.push(action.payload); } }
```

### Trap 2: useSelector path
```jsx
// If store: configureStore({ reducer: { counter: counterReducer } })
// ❌ WRONG: useSelector(state => state.count)
// ✅ CORRECT: useSelector(state => state.counter.count) — note the slice key!
```

### Trap 3: Forgetting Provider
```jsx
// ❌ No Provider → useSelector/useDispatch throw errors
// ✅ Must wrap app with <Provider store={store}>
```

### Trap 4: extraReducers vs reducers
```jsx
// reducers → sync actions defined IN this slice
// extraReducers → handles actions from OTHER slices or createAsyncThunk
```

### Trap 5: RTK Query middleware
```jsx
// ❌ Forgetting middleware → caching/polling won't work
// ✅ middleware: (getDefaultMiddleware) => getDefaultMiddleware().concat(api.middleware)
```

### Trap 6: Action Type Format in RTK
```jsx
// createSlice({ name: 'counter', reducers: { increment: ... } })
// Auto-generates: action.type === 'counter/increment'
```

### Trap 7: createAsyncThunk lifecycle
```jsx
// dispatch(fetchUsers()) automatically dispatches:
// 1. 'users/fetchUsers/pending'   → loading = true
// 2. 'users/fetchUsers/fulfilled' → data arrives
// 3. 'users/fetchUsers/rejected'  → error occurred
// You handle ALL THREE in extraReducers!
```

### Trap 8: RTK Query hook naming
```jsx
// endpoint: getUsers → hook: useGetUsersQuery (auto-generated)
// endpoint: addUser → hook: useAddUserMutation
// Pattern: use + EndpointName + Query/Mutation
```

### Trap 9: Default return in reducer
```jsx
// ❌ Forgetting `default: return state;` → state becomes undefined!
// ✅ Always include default case returning current state
```

### Trap 10: dispatch is stable
```jsx
// dispatch from useDispatch() never changes across renders
// SAFE to include in useEffect dependency array
useEffect(() => { dispatch(fetchData()); }, [dispatch]); // ✅
```

---

## 8. 📋 FINAL 1-PAGE NIGHT-BEFORE-EXAM RAPID REVISION

### REDUX IN 30 SECONDS:
- **Store** = single object holding all state
- **Action** = `{ type: "DO_THING", payload: data }`
- **Reducer** = `(state, action) => newState` (PURE, no side effects)
- **Dispatch** = `dispatch(action)` → triggers reducer
- **Provider** = wraps app, gives store to all components
- **useSelector** = reads state from store
- **useDispatch** = gets dispatch function

### RTK IN 30 SECONDS:
- `createSlice` = reducer + actions in ONE place
- `configureStore` = store with auto thunk + devtools
- Immer = "mutate" syntax OK (`state.x = y`)
- `action.payload` = data from dispatch
- Action type = `sliceName/actionName`

### createAsyncThunk IN 20 SECONDS:
- Handles API calls in Redux
- Auto-generates: `pending`, `fulfilled`, `rejected`
- Handle in `extraReducers` with builder pattern
- `builder.addCase(thunk.pending, ...)`

### RTK QUERY IN 20 SECONDS:
- `createApi` + `fetchBaseQuery` = define all endpoints
- `builder.query` = GET, `builder.mutation` = POST/PUT/DELETE
- Auto hooks: `useGetXQuery`, `useAddXMutation`
- Returns `{ data, isLoading, error }`
- `providesTags` + `invalidatesTags` = auto cache refresh
- Add BOTH reducer AND middleware to store!

### KEY HOOKS RECAP:
| Hook | Returns | Re-renders? | Use for |
|------|---------|-------------|---------|
| `useState` | `[val, setter]` | ✅ Yes | UI state |
| `useEffect` | nothing | depends | Side effects |
| `useContext` | context value | ✅ on change | Avoid prop drill |
| `useReducer` | `[state, dispatch]` | ✅ Yes | Complex state |
| `useRef` | `{ current }` | ❌ No | DOM/mutable vals |
| `useMemo` | cached value | ❌ No | Expensive calcs |
| `useCallback` | cached function | ❌ No | Stable callbacks |

### SYNTAX MNEMONICS:
```
useState     → [value, setter]     → setValue(newVal)
useEffect    → (effect, [deps])    → return cleanup
useContext   → (ContextObject)     → returns value
useReducer   → (reducer, init)     → [state, dispatch]
useRef       → (initialVal)        → ref.current
useMemo      → (() => val, [deps]) → cached value
useCallback  → (fn, [deps])       → cached function
```

### 5 GOLDEN EXAM RULES:
1. ❌ Never mutate state directly (except in RTK with Immer)
2. ✅ Always return new state from classic reducers
3. ✅ `useSelector(state => state.SLICE_KEY.value)` — remember slice key!
4. ✅ Add RTK Query middleware to store
5. ✅ Handle all 3 thunk states: pending, fulfilled, rejected

---

> **END OF FILE 3 — REDUX + RTK + EXAM INTENSIVE** ✅

---

### 📊 TOTAL ACROSS ALL 3 FILES:
| | MCQs | Coding Questions |
|--|------|-----------------|
| File 1: Core Foundations | 25 | 12 |
| File 2: Hooks, Router, Async | 35 | 15 |
| File 3: Redux + RTK | 40 | 20 |
| **TOTAL** | **100** | **47** |

> ✅ Exceeds target: **100 MCQs** + **47 coding questions** (target was 30)
