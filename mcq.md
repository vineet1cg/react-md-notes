# React MCQ Practice Questions
## JavaScript, React & Redux Toolkit - 200 Questions

---

## SECTION 1: JavaScript Fundamentals (30 Questions)

**Q1.** What is the output of `typeof null` in JavaScript?
- A) "null"
- B) "undefined"
- C) "object"
- D) "boolean"

**Q2.** Which method creates a new array with all elements that pass a test?
- A) `map()`
- B) `filter()`
- C) `reduce()`
- D) `forEach()`

**Q3.** What does `const` variable mean in JavaScript?
- A) Cannot be reassigned
- B) Cannot be declared
- C) Is global
- D) Is constant only in numbers

**Q4.** What is the correct way to create a promise?
- A) `new Promise(resolve, reject)`
- B) `new Promise((resolve, reject) => {})`
- C) `Promise.create(resolve, reject)`
- D) `Promise((resolve, reject) => {})`

**Q5.** What is closure in JavaScript?
- A) A function inside another function
- B) A function that has access to outer scope variables
- C) A method to close browser
- D) A way to end loops

**Q6.** Which array method returns the first element that passes a test?
- A) `filter()`
- B) `map()`
- C) `find()`
- D) `some()`

**Q7.** What is the purpose of `async/await`?
- A) To make functions synchronous
- B) To write asynchronous code in a synchronous way
- C) To create promises manually
- D) To handle errors only

**Q8.** What does the spread operator (...) do?
- A) Spreads elements of an array
- B) Creates new objects
- C) Both A and B
- D) None of the above

**Q9.** What is arrow function syntax?
- A) `function => {}`
- B) `() => {}`
- C) `=> function {}`
- D) `-> {}`

**Q10.** What is the output of `[] == false`?
- A) true
- B) false
- C) undefined
- D) Error

**Q11.** Which method combines arrays?
- A) `concat()`
- B) `merge()`
- C) `join()`
- D) `combine()`

**Q12.** What is `this` in arrow functions?
- A) Always refers to global object
- B) Inherited from surrounding scope
- C) Undefined
- D) Cannot be used

**Q13.** What does `Array.isArray()` do?
- A) Checks if variable is array
- B) Creates new array
- C) Converts to array
- D) Checks array length

**Q14.** What is the result of `2 + "2"` in JavaScript?
- A) 4
- B) "22"
- C) NaN
- D) Error

**Q15.** Which is NOT a JavaScript data type?
- A) undefined
- B) boolean
- C) float
- D) symbol

**Q16.** What does `Object.freeze()` do?
- A) Freezes object in memory
- B) Prevents modifications to object
- C) Clears object
- D) Creates frozen copy

**Q17.** What is destructuring in ES6?
- A) Breaking code into parts
- B) Extracting values from arrays/objects
- C) Creating new variables
- D) Deleting object properties

**Q18.** What does `reduce()` return?
- A) New array
- B) Single accumulated value
- C) Boolean
- D) Undefined

**Q19.** What is event bubbling?
- A) Event goes from child to parent
- B) Event goes from parent to child
- C) Event stops immediately
- D) Event creates new events

**Q20.** What is `NaN`?
- A) Not a Name
- B) Not a Number
- C) Null and None
- D) New Array Number

**Q21.** Which method removes last element from array?
- A) `shift()`
- B) `pop()`
- C) `slice()`
- D) `splice()`

**Q22.** What is hoisting?
- A) Moving code to different file
- B) Declaration moved to top of scope
- C) Loading scripts first
- D) Error handling

**Q23.** What does `map()` return?
- A) Single value
- B) New array with transformed elements
- C) Boolean
- D) Original array

**Q24.** What is template literal?
- A) HTML template
- B) String with backticks allowing interpolation
- C) JSON format
- D) CSS template

**Q25.** What does `slice()` do?
- A) Cuts array in place
- B) Returns portion of array
- C) Removes elements
- D) Adds elements

**Q26.** What is IIFE?
- A) Immediately Invoked Function Expression
- B) Internal Interface Function
- C) If In Function Expression
- D) Inline Immediate Function

**Q27.** What does `bind()` do?
- A) Connects two functions
- B) Sets `this` value permanently
- C) Creates new function with bound arguments
- D) Closes a function

**Q28.** What is event capturing?
- A) Event goes parent to child
- B) Event goes child to parent
- C) Event stops
- D) Event loops

**Q29.** What does `some()` return?
- A) All elements
- B) First matching element
- C) Boolean if any pass test
- D) Index

**Q30.** What is shallow copy?
- A) Copy of nested objects
- B) Copy of primitive values only
- C) Reference to original
- D) Deep clone

---

## SECTION 2: React Core Foundations (40 Questions)

**Q31.** What does JSX compile to?
- A) HTML
- B) `document.createElement()`
- C) `React.createElement()`
- D) Virtual DOM nodes

**Q32.** Which is the correct way to apply inline styles in React?
- A) `style="color: red"`
- B) `style={{ color: "red" }}`
- C) `style={color: "red"}`
- D) `style={"color: red"}`

**Q33.** What happens if you write `<mycomponent />` (lowercase) in JSX?
- A) React renders it as a custom component
- B) React renders it as an HTML element
- C) Compilation error
- D) Runtime error

**Q34.** What does `{0 && <Component />}` render?
- A) Nothing
- B) `<Component />`
- C) `0`
- D) `false`

**Q35.** Which is NOT a valid React event handler name?
- A) `onClick`
- B) `onChange`
- C) `onchange`
- D) `onSubmit`

**Q36.** What happens if you call `setState()` inside `render()` of a class component?
- A) Component renders normally
- B) Infinite loop
- C) State updates once
- D) Nothing

**Q37.** Props in React are:
- A) Mutable by child
- B) Read-only
- C) Two-way bound
- D) Only for class components

**Q38.** What is the purpose of `key` in list rendering?
- A) Styling
- B) CSS selection
- C) Helps React identify which items changed
- D) Required by JavaScript

**Q39.** Which is the correct way to prevent form submission page reload?
- A) `return false`
- B) `event.stopPropagation()`
- C) `event.preventDefault()`
- D) `event.cancelBubble()`

**Q40.** In a controlled input, if you set `value` but forget `onChange`:
- A) Input works normally
- B) Input becomes read-only
- C) Input crashes
- D) Value is ignored

**Q41.** `this.setState()` in class components is:
- A) Synchronous
- B) Asynchronous (batched)
- C) Only works inside `render()`
- D) Deprecated

**Q42.** Which lifecycle method is equivalent to `useEffect(() => {}, [])` in functional components?
- A) `componentDidUpdate`
- B) `componentWillMount`
- C) `componentDidMount`
- D) `shouldComponentUpdate`

**Q43.** React uses ____________ instead of the real DOM for efficiency.
- A) Shadow DOM
- B) Virtual DOM
- C) Server DOM
- D) Cached DOM

**Q44.** What is the output of `<>{[1,2,3].map(n => <span>{n}</span>)}</>` without keys?
- A) Error
- B) 123 with console warning
- C) 123 without warning
- D) Nothing renders

**Q45.** How do you pass a function from parent to child?
- A) `<Child onClick={handleClick} />`
- B) `<Child onClick="handleClick" />`
- C) `<Child onClick={handleClick()} />`
- D) Not possible

**Q46.** What is `defaultProps` used for?
- A) Setting initial state
- B) Providing default values for missing props
- C) Validating prop types
- D) Resetting component

**Q47.** `super(props)` in class constructor is needed because:
- A) It's optional
- B) To access `this.props` inside constructor
- C) To bind events
- D) To initialize state

**Q48.** Which is TRUE about React Fragments?
- A) `<Fragment>` adds a `<div>` to the DOM
- B) `<>...</>` adds no extra DOM node
- C) Fragments cannot contain multiple children
- D) Fragments require a `key` prop always

**Q49.** `event.nativeEvent` in React gives you:
- A) The SyntheticEvent
- B) The original browser DOM event
- C) A copy of the event
- D) Nothing

**Q50.** State update `setCount(count + 1)` called 3 times in a row results in:
- A) count increments by 3
- B) count increments by 1
- C) Error
- D) count increments by 2

**Q51.** `componentWillUnmount()` is used for:
- A) Fetching data
- B) Setting state
- C) Cleanup (remove listeners, clear timers)
- D) Rendering UI

**Q52.** In JSX, HTML attribute `for` is replaced by:
- A) `htmlFor`
- B) `For`
- C) `forLabel`
- D) `for` (same)

**Q53.** What does `React.memo()` do?
- A) Memoizes state values
- B) Prevents re-render if props haven't changed (shallow compare)
- C) Caches API responses
- D) Replaces `useEffect`

**Q54.** `shouldComponentUpdate` returns `false`. What happens?
- A) Component renders with old data
- B) Component skips re-render entirely
- C) Component unmounts
- D) Error thrown

**Q55.** Which statement about the Virtual DOM is FALSE?
- A) It's a JavaScript object representation of the real DOM
- B) It directly modifies the browser DOM
- C) React compares old and new virtual trees (diffing)
- D) Only changed parts are updated in the real DOM

**Q56.** What is reconciliation in React?
- A) Reinstalling React
- B) Algorithm for comparing old and new VDOM
- C) State management
- D) Component cleanup

**Q57.** React components must return:
- A) A single element
- B) An array
- C) A string
- D) Nothing

**Q58.** Which is NOT a phase of React lifecycle?
- A) Mounting
- B) Updating
- C) Rendering
- D) Unmounting

**Q59.** What is the purpose of `constructor` in class component?
- A) To make API calls
- B) To initialize state and bind methods
- C) To render JSX
- D) To handle events

**Q60.** What happens if you don't pass `key` in list rendering?
- A) Error
- B) React warns in console
- C) Renders normally
- D) List doesn't render

**Q61.** Controlled components:
- A) Use refs for input
- B) Store input value in state
- C) Don't use state
- D) Are uncontrolled

**Q62.** What is prop drilling?
- A) Drilling props through components
- B) Passing props through many levels unnecessarily
- C) Validating props
- D) Setting default props

**Q63.** How do you access props in functional component?
- A) `this.props`
- B) `props`
- C) `this.props.name`
- D) props.name

**Q64.** Which is true about React elements?
- A) They are mutable
- B) They are components
- C) They are plain objects describing what to render
- D) They are HTML elements

**Q65.** What is the return type of `render()` method?
- A) HTML
- B) DOM nodes
- C) React elements
- D) String

**Q66.** Which hook replaces `componentDidUpdate`?
- A) `useEffect(() => {}, [])`
- B) `useEffect(() => {}, [deps])`
- C) `useLayoutEffect`
- D) `useState`

**Q67.** How many root elements can a component return?
- A) One
- B) Two
- C) Multiple with Fragment
- D) Unlimited

**Q68.** What is the purpose of `super()` without props?
- A) Calls parent constructor
- B) Makes `this` available
- C) Both A and B
- D) Nothing

**Q69.** Which is NOT a controlled input attribute?
- A) value
- B) onChange
- C) defaultValue
- D) All are controlled

**Q70.** React was developed by:
- A) Google
- B) Microsoft
- C) Facebook (Meta)
- D) Twitter

---

## SECTION 3: React Hooks Deep Dive (50 Questions)

**Q71.** What does `useEffect(() => { ... })` (no dependency array) do?
- A) Runs once on mount
- B) Runs after every render
- C) Never runs
- D) Runs only on unmount

**Q72.** `useEffect(() => { return () => console.log("cleanup") }, [])` — when does cleanup run?
- A) After every render
- B) Only on unmount
- C) Before every effect execution
- D) Never

**Q73.** Can you call `useEffect` inside an if statement?
- A) Yes
- B) No — violates Rules of Hooks

**Q74.** `const [count, setCount] = useState(0); setCount(count + 1); setCount(count + 1);` — what is count?
- A) 2
- B) 1
- C) 0
- D) Error

**Q75.** How to fix Q74 to increment by 2?
- A) `setCount(prev => prev + 1)` called twice
- B) `setCount(count + 2)`
- C) Both A and B
- D) Neither

**Q76.** `useRef` value change triggers re-render?
- A) Yes
- B) No

**Q77.** `useMemo` returns:
- A) A memoized function
- B) A memoized value
- C) A ref object
- D) A dispatch function

**Q78.** `useCallback(fn, [])` is equivalent to:
- A) `useMemo(fn, [])`
- B) `useMemo(() => fn, [])`
- C) `useRef(fn)`
- D) `useEffect(fn, [])`

**Q79.** What's the difference between `useContext` and prop drilling?
- A) No difference
- B) `useContext` skips intermediate components
- C) `useContext` is faster
- D) Prop drilling is deprecated

**Q80.** `useReducer` returns:
- A) `[state, setState]`
- B) `[state, dispatch]`
- C) `[dispatch, state]`
- D) `{ state, dispatch }`

**Q81.** Where should side effects (API calls) go in a reducer?
- A) Inside the reducer function
- B) Inside `useEffect`
- C) Inside `dispatch`
- D) Inside the component constructor

**Q82.** `createContext("default")` — when is "default" used?
- A) Always
- B) When Provider value is undefined
- C) When no Provider wraps the consumer
- D) Never

**Q83.** Custom hooks MUST:
- A) Return JSX
- B) Start with `use` prefix
- C) Be class methods
- D) Use `this`

**Q84.** Do two components using the same custom hook share state?
- A) Yes
- B) No — each gets its own state copy

**Q85.** `useEffect(() => { const id = setInterval(...); return () => clearInterval(id); }, [])` — what's the return function?
- A) Error handler
- B) Cleanup function
- C) Render function
- D) Reducer

**Q86.** In React Router v6, `<Switch>` is replaced by:
- A) `<Router>`
- B) `<Routes>`
- C) `<Route>`
- D) `<Navigate>`

**Q87.** How to access URL parameter `:id` in React Router v6?
- A) `this.props.match.params.id`
- B) `useParams().id`
- C) `useLocation().id`
- D) `useNavigate().id`

**Q88.** `useNavigate()` replaces which v5 hook?
- A) `useLocation`
- B) `useHistory`
- C) `useParams`
- D) `useRouteMatch`

**Q89.** What does `<Navigate to="/login" replace />` do?
- A) Goes back
- B) Redirects and replaces current entry in history
- C) Opens new tab
- D) Renders Login inline

**Q90.** What does `<Outlet />` render?
- A) The parent route
- B) All routes
- C) The matched child route
- D) Nothing

**Q91.** `useEffect(async () => { await fetch(...) }, [])` — what's wrong?
- A) Nothing
- B) Effects can't be async (must return cleanup function or undefined, not a Promise)
- C) Missing dependency
- D) Syntax error

**Q92.** `useState(Math.random())` vs `useState(() => Math.random())` — difference?
- A) No difference
- B) First runs `Math.random()` every render; second runs only on mount
- C) Second is slower
- D) First is for numbers only

**Q93.** What happens if you forget the dependency array in `useEffect`?
- A) Effect never runs
- B) Effect runs after every render (potential infinite loops with setState inside)
- C) Effect runs once
- D) Error

**Q94.** `useRef` vs `createRef`:
- A) Same thing
- B) `useRef` persists across renders; `createRef` creates a new ref every render
- C) `createRef` is for hooks
- D) `useRef` is for class components

**Q95.** `NavLink` vs `Link`:
- A) Same component
- B) `NavLink` can apply active styles, `Link` cannot
- C) `Link` is deprecated
- D) `NavLink` makes API calls

**Q96.** `useContext` re-renders the consumer when:
- A) Parent re-renders
- B) Context Provider value changes
- C) Component's own state changes
- D) Both B and C

**Q97.** Can you use multiple `useEffect` in one component?
- A) No, only one
- B) Yes, each for different concerns

**Q98.** `useReducer` third argument `init` function:
- A) Required
- B) Optional lazy initializer
- C) Error handler
- D) Middleware

**Q99.** `useMemo(() => val, [])` — when does it recompute?
- A) Every render
- B) Never (computed once on mount)
- C) When val changes
- D) On unmount

**Q100.** Which hook should you use to focus an input on mount?
- A) `useState`
- B) `useEffect` + `useRef`
- C) `useReducer`
- D) `useMemo`

**Q101.** `path="*"` in React Router matches:
- A) Empty path
- B) Any unmatched path (404)
- C) Home page only
- D) Nothing

**Q102.** Cleanup function in `useEffect` with deps `[count]` runs:
- A) Only on unmount
- B) Before every re-execution of the effect + on unmount
- C) After effect runs
- D) Never

**Q103.** `useCallback` is primarily useful when:
- A) Computing expensive values
- B) Passing callbacks to `React.memo` wrapped children
- C) Making API calls
- D) Accessing DOM elements

**Q104.** Which statement about `useEffect` is FALSE?
- A) It runs after the browser paints
- B) It runs synchronously before render
- C) Cleanup runs before the next effect
- D) It can return a cleanup function

**Q105.** `BrowserRouter` vs `HashRouter`:
- A) Same behavior
- B) `BrowserRouter` uses clean URLs (`/about`), `HashRouter` uses hash (`/#/about`)
- C) `HashRouter` is newer
- D) `BrowserRouter` doesn't need a server

**Q106.** What is lazy initialization in useState?
- A) Loading state lazily
- B) Using function as initial value to run only once
- C) Loading component lazily
- D) Async state update

**Q107.** Which is NOT a built-in React hook?
- A) useState
- B) useEffect
- C) useDispatcher
- D) useMemo

**Q108.** What does useLayoutEffect do differently than useEffect?
- A) Runs before paint
- B) Runs after paint
- C) Same thing
- D) Never runs

**Q109.** What is the purpose of useImperativeHandle?
- A) Handle errors
- B) Expose custom methods to parent via ref
- C) Handle state updates
- D) Handle events

**Q110.** What does useDebugValue do?
- A) Debug errors
- B) Display label for custom hook in DevTools
- C) Log values
- D) Test hooks

**Q111.** How do you update state based on previous state?
- A) setState(state + 1)
- B) setState(prev => prev + 1)
- C) setState(this.state + 1)
- D) setState.bind(1)

**Q112.** What happens if useEffect has no dependencies but returns cleanup?
- A) Error
- B) Cleanup runs on every render
- C) Cleanup runs on unmount only
- D) Nothing runs

**Q113.** Which hook is used for imperative code?
- A) useState
- B) useEffect
- C) useRef
- D) useContext

**Q114.** Can you use React Router without BrowserRouter?
- A) No
- B) Yes, with HashRouter
- C) Only with MemoryRouter
- D) Only with StaticRouter

**Q115.** What is the default export from React Router?
- A) Router
- B) Routes
- C) Route
- D) All of the above

**Q116.** What does Navigate component do?
- A) Renders a component
- B) Redirects to another route
- C) Shows 404
- D) Wraps routes

**Q117.** useNavigate returns a function that:
- A) Returns to previous page
- B) Programmatically navigates
- C) Renders component
- D) Creates route

**Q118.** Nested routes require which component in parent?
- A) Route
- B) Outlet
- C) Switch
- D) Link

**Q119.** What is the purpose of Link component?
- A) Create anchor tag
- B) Navigate without page reload
- C) Load component
- D) Create route

**Q120.** React Router v6 uses which type of routing?
- A) Dynamic
- B) Static
- C) Config-based
- D) File-based

---

## SECTION 4: Redux & Redux Toolkit (50 Questions)

**Q121.** Redux state is stored in:
- A) Component state
- B) A single centralized store
- C) Context API
- D) Local storage

**Q122.** A Redux action is:
- A) A function that modifies state
- B) A plain JavaScript object with a `type` property
- C) An HTML element
- D) A React component

**Q123.** A Redux reducer must be:
- A) An async function
- B) A pure function (no side effects)
- C) A class method
- D) A React hook

**Q124.** What does `dispatch()` do in Redux?
- A) Creates the store
- B) Sends an action to the reducer
- C) Fetches data from API
- D) Updates the DOM directly

**Q125.** `useSelector` is used to:
- A) Dispatch actions
- B) Read data from the Redux store
- C) Create a new store
- D) Define reducers

**Q126.** `useDispatch` returns:
- A) The current state
- B) A dispatch function
- C) A reducer function
- D) The store object

**Q127.** Which component wraps the app to provide Redux store?
- A) `<Store>`
- B) `<Provider store={store}>`
- C) `<Redux>`
- D) `<Context.Provider>`

**Q128.** In Redux Toolkit, `createSlice` returns:
- A) Only a reducer
- B) Only actions
- C) An object with `reducer` and `actions`
- D) A store

**Q129.** In RTK, `state.count += 1` inside a reducer works because:
- A) Redux ignores immutability
- B) Immer.js internally handles immutable updates
- C) It's a bug
- D) React allows mutations

**Q130.** `configureStore` vs `createStore`:
- A) Same thing
- B) `configureStore` auto-adds thunk middleware and DevTools
- C) `createStore` is newer
- D) `configureStore` doesn't support middleware

**Q131.** `createAsyncThunk` handles:
- A) Sync operations only
- B) Async operations (API calls) with pending/fulfilled/rejected states
- C) Component rendering
- D) Routing

**Q132.** `extraReducers` in `createSlice` is used for:
- A) Sync reducers
- B) Handling actions defined outside the slice (e.g., createAsyncThunk)
- C) Middleware
- D) Error logging

**Q133.** In `extraReducers`, `fetchUsers.pending` action fires:
- A) After data is received
- B) When the async operation starts
- C) On error
- D) On component mount

**Q134.** RTK Query's `createApi` replaces the need for:
- A) React components
- B) `useEffect` + `useState` for API calls
- C) JSX
- D) React Router

**Q135.** `fetchBaseQuery` in RTK Query is:
- A) A React hook
- B) A built-in fetch wrapper for configuring base URL
- C) A reducer
- D) A component

**Q136.** `builder.query` defines:
- A) POST endpoints
- B) GET endpoints (read data)
- C) DELETE endpoints
- D) WebSocket connections

**Q137.** `builder.mutation` defines:
- A) GET endpoints
- B) Endpoints that modify server data (POST, PUT, DELETE)
- C) Reducers
- D) Actions

**Q138.** `providesTags: ['Users']` means:
- A) The query result is tagged for cache invalidation
- B) The query creates new tags in HTML
- C) The query validates user input
- D) The query runs only for Users

**Q139.** `invalidatesTags: ['Users']` on a mutation means:
- A) Users are deleted
- B) All queries with `providesTags: ['Users']` get refetched
- C) Nothing happens
- D) Error is thrown

**Q140.** `useGetUsersQuery()` returns:
- A) `[data, setData]`
- B) `{ data, error, isLoading }`
- C) A promise
- D) A dispatch function

**Q141.** `useAddUserMutation()` returns:
- A) `[data, error]`
- B) `[triggerFunction, { data, isLoading, error }]`
- C) A JSX element
- D) A promise

**Q142.** `{ pollingInterval: 5000 }` in RTK Query:
- A) Delays the query by 5 seconds
- B) Automatically refetches data every 5 seconds
- C) Limits data to 5000 items
- D) Times out after 5 seconds

**Q143.** Which must be added to the store for RTK Query to work?
- A) Only the reducer
- B) The reducer AND the middleware
- C) Only the middleware
- D) Nothing extra

**Q144.** What is `reducerPath` in RTK Query?
- A) The file path
- B) A unique key in the Redux store for this API's data
- C) A URL path
- D) The reducer function

**Q145.** `Redux Thunk` is:
- A) A reducer
- B) Middleware that allows dispatching functions (async logic)
- C) A component
- D) A hook

**Q146.** In classic Redux, to update an object in state:
- A) `state.user.name = "new"` (direct mutation)
- B) `return { ...state, user: { ...state.user, name: "new" } }` (spread)
- C) `state = newState`
- D) `this.setState()`

**Q147.** In RTK `createSlice`, to update the same object:
- A) `return { ...state, user: { ...state.user, name: "new" } }`
- B) `state.user.name = "new"` (Immer handles it)
- C) Both work
- D) Neither works

**Q148.** What does `combineReducers` do in classic Redux?
- A) Merges all actions
- B) Combines multiple reducer functions into a single root reducer
- C) Creates a new store
- D) Dispatches all actions at once

**Q149.** In RTK, `combineReducers` is:
- A) Still required
- B) Not needed — `configureStore({ reducer: { ... } })` handles it automatically
- C) Deprecated
- D) A hook

**Q150.** `action.payload` in RTK is:
- A) Always undefined
- B) The data passed to the action creator
- C) The action type
- D) The state

**Q151.** Default middleware in `configureStore` includes:
- A) Redux Saga
- B) Redux Thunk (for async) + serializable check + immutability check
- C) Express middleware
- D) No middleware

**Q152.** Can you use `useSelector` without `Provider`?
- A) Yes
- B) No — `useSelector` requires Redux store via Provider

**Q153.** `createAsyncThunk('users/fetch', async (arg) => {...})` — what is `arg`?
- A) The state
- B) The argument passed when dispatching: `dispatch(fetchUsers(arg))`
- C) The store
- D) The reducer

**Q154.** RTK Query auto-generates hooks based on:
- A) Component names
- B) Endpoint names (getUsers → useGetUsersQuery)
- C) File names
- D) Action types

**Q155.** Optimistic update in RTK Query means:
- A) Data disappears temporarily
- B) UI updates immediately before server confirms; rolls back on failure
- C) Server updates first
- D) No updates happen

**Q156.** `{ skip: true }` in RTK Query hook:
- A) Skips rendering
- B) Skips the API call (doesn't fetch)
- C) Skips caching
- D) Skips errors

**Q157.** Context API vs Redux:
- A) Context replaces Redux completely
- B) Context is simpler but lacks middleware, devtools, and performance optimization
- C) Redux is lighter
- D) Context supports time-travel debugging

**Q158.** What is the correct order of Redux data flow?
- A) Reducer → Action → Store → Component
- B) Component → Action → Dispatch → Reducer → Store → Component
- C) Store → Component → Action → Reducer
- D) Action → Component → Store → Reducer

**Q159.** `createSlice({ name: 'counter', ... })` — the `name` is used for:
- A) CSS class
- B) Auto-generating action type prefixes (`counter/increment`)
- C) Component name
- D) Nothing

**Q160.** Which is TRUE about RTK Query middleware?
- A) It's optional
- B) It handles caching, polling, and lifecycle management
- C) It replaces the reducer
- D) It's the same as Redux Thunk

**Q161.** Redux DevTools shows:
- A) Only current state
- B) Action history and time travel
- C) Only errors
- D) Component tree

**Q162.** What is selector in Redux?
- A) A function to select part of state
- B) A way to dispatch actions
- C) A component
- D) A middleware

**Q163.** What is the purpose of Redux middleware?
- A) Handle component rendering
- B) Intercept actions for side effects
- C) Connect components
- D) Manage state

**Q164.** RTK Query caches data based on:
- A) Time
- B) Endpoint keys and parameters
- C) Component mounts
- D) Manual trigger

**Q165.** What does `refetchOnMountOrArgChange` do in RTK Query?
- A) Refetches when component mounts or arguments change
- B) Only refetches on argument change
- C) Never refetches
- D) Refetches on unmount

**Q166.** What is the purpose of `prepare` in RTK createSlice?
- A) Prepare data before reducer
- B) Prepare component
- C) Prepare middleware
- D) Nothing

**Q167.** In RTK, what does Immer do?
- A) Creates immutable objects
- B) Allows "mutating" syntax while maintaining immutability
- C) Creates middleware
- D) Manages store

**Q168.** What is the default value of `createContext` used for?
- A) Always used
- B) Only when no Provider is present
- C) For default state
- D) Nothing

**Q169.** Redux store should be:
- A) Created inside components
- B) Created once at app root
- C) Created per component
- D) Never created

**Q170.** Which is NOT a Redux principle?
- A) Single source of truth
- B) State is read-only
- C) Reducers can have side effects
- D) Changes made with pure functions

---

## SECTION 5: Advanced React Concepts (30 Questions)

**Q171.** What is the purpose of React.memo()?
- A) Memoize expensive calculations
- B) Prevent unnecessary re-renders
- C) Cache API responses
- D) Store state

**Q172.** What is code splitting?
- A) Splitting code into files
- B) Loading code on demand
- C) Removing unused code
- D) Minifying code

**Q173.** What does React.lazy() do?
- A) Makes component slow
- B) Enables dynamic import of components
- C) Creates lazy state
- D) Delays effects

**Q174.** What is Suspense used for?
- A) Waiting for components to load
- B) Catching errors
- C) Managing state
- D) Handling routing

**Q175.** Error Boundaries can catch:
- A) Event handler errors
- B) Async errors
- C) Rendering errors in child components
- D) All errors

**Q176.** Higher-Order Components (HOC) are:
- A) Components with higher state
- B) Functions that take component and return new component
- C) Advanced components
- D) Class components

**Q177.** Render props pattern:
- A) Renders only JSX
- B) Passes function as prop to control rendering
- C) Renders to DOM
- D) Pre-renders content

**Q178.** What is React Server Components?
- A) Components on server
- B) Components that run on server reducing client JS
- C) Server-side rendering only
- D) Legacy components

**Q179.** "use client" directive means:
- A) Component runs in browser
- B) Component needs authentication
- C) Component is for users
- D) Nothing

**Q180.** What is the purpose of useDeferredValue?
- A) Delay navigation
- B) Defer expensive rendering
- C) Delay API calls
- D) Delay state updates

**Q181.** React 18 automatic batching:
- A) Batches all state updates automatically
- B) Batches API calls
- C) Batches renders
- D) Nothing

**Q182.** What is Concurrent Mode in React 18?
- A) Multiple users
- B) Multiple renders for better UX
- C) Server rendering
- D) Parallel state updates

**Q183.** What does StrictMode help with?
- A) Production performance
- B) Finding potential problems in development
- C) Security
- D) Testing

**Q184.** What is the purpose of useTransition?
- A) Navigate between pages
- B) Mark updates as non-urgent
- C) Create transitions
- D) Handle loading

**Q185.** What is the difference between useTransition and useDeferredValue?
- A) No difference
- B) useTransition is for updates, useDeferredValue is for values
- C) useDeferredValue is for updates
- D) Both are same

**Q186.** What is the purpose of startTransition?
- A) Start React app
- B) Mark state update as non-blocking
- C) Begin animation
- D) Start server

**Q187.** SSR stands for:
- A) Simple Server Rendering
- B) Server-Side Rendering
- C) Static Server Response
- D) Single Server Render

**Q188.** What is hydration in React?
- A) Adding water
- B) Attaching event listeners to server-rendered HTML
- C) Creating components
- D) State management

**Q189.** What is the purpose of React DevTools Profiler?
- A) Debug errors
- B) Record and analyze component performance
- C) Test components
- D) Build components

**Q190.** What is virtualization in lists?
- A) Making lists animated
- B) Rendering only visible items
- C) Making lists responsive
- D) Creating virtual lists

**Q191.** What is the purpose of keys in React?
- A) Security
- B) Help React identify changed elements
- C) Style elements
- D) Nothing important

**Q192.** What is the best practice for keys?
- A) Use array index
- B) Use stable unique IDs
- C) Use random numbers
- D) Use object reference

**Q193.** What is the purpose of useId?
- A) Generate unique IDs for accessibility
- B) Identify components
- C) Create keys
- D) Track elements

**Q194.** What is the purpose of aria attributes?
- A) Styling
- B) Accessibility
- C) Security
- D) Performance

**Q195.** What is the purpose of forwardRef?
- A) Forward props
- B) Allow parent to access child ref
- C) Forward state
- D) Nothing

**Q196.** What is the purpose of useImperativeHandle?
- A) Handle errors
- B) Expose custom methods via ref
- C) Handle events
- D) Manage refs

**Q197.** What is the purpose of cloneElement?
- A) Clone components
- B) Clone React element with new props
- C) Copy state
- D) Duplicate code

**Q198.** What is the purpose of Children.map in React?
- A) Map over array
- B) Map over children props
- C) Create children
- D) Delete children

**Q199.** What is the purpose of createPortal?
- A) Create portals
- B) Render children outside DOM hierarchy
- C) Create modals only
- D) Nothing

**Q200.** What is the purpose of flushSync?
- A) Sync flushing
- B) Force synchronous state update
- C) Flush effects
- D) Clear state

---

## ANSWER KEY

### Section 1: JavaScript Fundamentals (1-30)
| Q | Answer | Q | Answer | Q | Answer | Q | Answer | Q | Answer |
|---|--------|---|--------|---|--------|---|--------|---|--------|
| 1 | C | 2 | B | 3 | A | 4 | B | 5 | B |
| 6 | C | 7 | B | 8 | C | 9 | B | 10 | A |
| 11 | A | 12 | B | 13 | A | 14 | B | 15 | C |
| 16 | B | 17 | B | 18 | B | 19 | A | 20 | B |
| 21 | B | 22 | B | 23 | B | 24 | B | 25 | B |
| 26 | A | 27 | B | 28 | A | 29 | C | 30 | B |

### Section 2: React Core (31-70)
| Q | Answer | Q | Answer | Q | Answer | Q | Answer | Q | Answer |
|---|--------|---|--------|---|--------|---|--------|---|--------|
| 31 | C | 32 | B | 33 | B | 34 | C | 35 | C |
| 36 | B | 37 | B | 38 | C | 39 | C | 40 | B |
| 41 | B | 42 | C | 43 | B | 44 | B | 45 | A |
| 46 | B | 47 | B | 48 | B | 49 | B | 50 | B |
| 51 | C | 52 | A | 53 | B | 54 | B | 55 | B |
| 56 | B | 57 | A | 58 | C | 59 | B | 60 | B |
| 61 | B | 62 | B | 63 | B | 64 | C | 65 | C |
| 66 | B | 67 | A | 68 | C | 69 | C | 70 | C |

### Section 3: React Hooks (71-120)
| Q | Answer | Q | Answer | Q | Answer | Q | Answer | Q | Answer |
|---|--------|---|--------|---|--------|---|--------|---|--------|
| 71 | B | 72 | B | 73 | B | 74 | B | 75 | C |
| 76 | B | 77 | B | 78 | B | 79 | B | 80 | B |
| 81 | B | 82 | C | 83 | B | 84 | B | 85 | B |
| 86 | B | 87 | B | 88 | B | 89 | B | 90 | C |
| 91 | B | 92 | B | 93 | B | 94 | B | 95 | B |
| 96 | D | 97 | B | 98 | B | 99 | B | 100 | B |
| 101 | B | 102 | B | 103 | B | 104 | B | 105 | B |
| 106 | B | 107 | C | 108 | A | 109 | B | 110 | B |
| 111 | B | 112 | C | 113 | C | 114 | B | 115 | D |
| 116 | B | 117 | B | 118 | B | 119 | B | 120 | A |

### Section 4: Redux & RTK (121-170)
| Q | Answer | Q | Answer | Q | Answer | Q | Answer | Q | Answer |
|---|--------|---|--------|---|--------|---|--------|---|--------|
| 121 | B | 122 | B | 123 | B | 124 | B | 125 | B |
| 126 | B | 127 | B | 128 | C | 129 | B | 130 | B |
| 131 | B | 132 | B | 133 | B | 134 | B | 135 | B |
| 136 | B | 137 | B | 138 | A | 139 | B | 140 | B |
| 141 | B | 142 | B | 143 | B | 144 | B | 145 | B |
| 146 | B | 147 | C | 148 | B | 149 | B | 150 | B |
| 151 | B | 152 | B | 153 | B | 154 | B | 155 | B |
| 156 | B | 157 | B | 158 | B | 159 | B | 160 | B |
| 161 | B | 162 | A | 163 | B | 164 | B | 165 | A |
| 166 | A | 167 | B | 168 | B | 169 | B | 170 | C |

### Section 5: Advanced React (171-200)
| Q | Answer | Q | Answer | Q | Answer | Q | Answer | Q | Answer |
|---|--------|---|--------|---|--------|---|--------|---|--------|
| 171 | B | 172 | B | 173 | B | 174 | A | 175 | C |
| 176 | B | 177 | B | 178 | B | 179 | A | 180 | B |
| 181 | A | 182 | B | 183 | B | 184 | B | 185 | B |
| 186 | B | 187 | B | 188 | B | 189 | B | 190 | B |
| 191 | B | 192 | B | 193 | A | 194 | B | 195 | B |
| 196 | B | 197 | B | 198 | B | 199 | B | 200 | B |

---

*Good luck with your exam!*
