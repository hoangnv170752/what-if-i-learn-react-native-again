# 1. TypeScript Review for React Native

> Review TypeScript essentials for writing hooks, components, and reading RN codebases.

---

## 1.1 Basic Types

```ts
// Primitive types
let name: string = "Akashi";
let age: number = 25;
let isDev: boolean = true;

// Array
let skills: string[] = ["React", "TypeScript", "Node"];

// Tuple
let pair: [string, number] = ["score", 100];

// Union type
let id: string | number = "abc-123";

// Literal type
let direction: "left" | "right" | "up" | "down" = "left";
```

---

## 1.2 Interfaces & Types

```ts
// Interface — describes the shape of an object
interface User {
  id: number;
  name: string;
  email?: string; // optional
}

// Type alias — more flexible, used for union, intersection
type Status = "idle" | "loading" | "error" | "success";

type ApiResponse<T> = {
  data: T;
  error: string | null;
};

// Extending
interface Admin extends User {
  role: "admin" | "superadmin";
}
```

---

## 1.3 Generics

> Generics allow reusable code while staying type-safe.

```ts
// Generic function
function getFirst<T>(items: T[]): T {
  return items[0];
}

const first = getFirst<string>(["a", "b", "c"]); // string
const firstNum = getFirst([1, 2, 3]); // number (inferred)

// Generic interface
interface ApiResult<T> {
  data: T;
  loading: boolean;
  error: string | null;
}
```

---

## 1.4 ES6+ Syntax Recap

```ts
// Arrow functions
const add = (a: number, b: number): number => a + b;

// Destructuring
const { name, age } = user;
const [first, ...rest] = items;

// Spread
const newUser = { ...user, name: "Updated" };
const merged = [...arr1, ...arr2];

// Optional chaining & nullish coalescing
const email = user?.profile?.email ?? "N/A";

// Template literals
const greeting = `Hello, ${name}!`;

// async/await
const fetchData = async (): Promise<User[]> => {
  const res = await fetch("/api/users");
  return res.json();
};
```

---

## 1.5 JSX / TSX Syntax

> JSX = JavaScript XML. TSX = TypeScript + JSX.

```tsx
// Component with typed props
interface ButtonProps {
  title: string;
  onPress: () => void;
  disabled?: boolean;
}

const MyButton = ({ title, onPress, disabled = false }: ButtonProps) => {
  return (
    <TouchableOpacity onPress={onPress} disabled={disabled}>
      <Text>{title}</Text>
    </TouchableOpacity>
  );
};
```

### Children typing

```tsx
interface LayoutProps {
  children: React.ReactNode;
}

const Layout = ({ children }: LayoutProps) => {
  return <View style={{ flex: 1 }}>{children}</View>;
};
```

---

## 1.6 TypeScript for React Hooks — The Complete Guide

> This is the most important section for React Native.

### Table of Contents

1. [What Hooks Are, Really](#161-what-hooks-are-really)
2. [The Rules of Hooks](#162-the-rules-of-hooks)
3. [Implicit (Unwritten) Rules of Hooks](#163-implicit-unwritten-rules-of-hooks)
4. [Typing Built-in Hooks](#164-typing-built-in-hooks)
5. [Patterns That Apply Across Hooks](#165-patterns-that-apply-across-hooks)
6. [Common Pitfalls & How to Avoid Them](#166-common-pitfalls--how-to-avoid-them)
7. [Hooks and Performance](#167-hooks-and-performance)
8. [How Hooks Work Under the Hood](#168-how-hooks-work-under-the-hood)
9. [Testing Hooks](#169-testing-hooks)

---

### 1.6.1 What Hooks Are, Really

Hooks are special functions that let you tap into React's features directly from your component code.

When you use a hook, you're telling React:

- `useState` → "I need some state here."
- `useEffect` → "I want to do something after this component renders."
- `useRef` → "I need to remember this value without re-rendering."

Key facts:

- They're just functions, but with superpowers granted by React
- They only work inside React's world (components and other hooks)
- They start with `use` — the linter enforces this
- You can **compose** hooks: built-in hooks combine into custom hooks, and custom hooks can call other custom hooks

---

### 1.6.2 The Rules of Hooks

#### Rule #1 — Only call hooks at the top level

Hooks must live in the main body of your component or custom hook. **Not** inside `if` statements, **not** inside loops, **not** inside nested functions.

React keeps track of hooks by the **order** they're called. If you move them around conditionally, React's internal tracking gets scrambled.

```tsx
// BAD
function Profile({ userId }: { userId: string | null }) {
  if (userId) {
    const [user, setUser] = useState<User | null>(null); // breaks hook order
  }
}

// GOOD
function Profile({ userId }: { userId: string | null }) {
  const [user, setUser] = useState<User | null>(null);
  // use userId conditionally inside the effect, not around the hook
}
```

#### Rule #2 — Only call hooks from React functions

That means:

- **Function components**
- **Custom hooks** (which are just functions that call hooks)

Hooks won't work from regular JavaScript functions, event handlers, or outside React. React needs to know when your component is rendering so it can hook the right data to it.

> Install `eslint-plugin-react-hooks` — it will catch violations of both rules at compile time.

---

### 1.6.3 Implicit (Unwritten) Rules of Hooks

#### Rule-ish #1 — Call hooks in the same order every render

Every render, hooks must be called in the **exact same sequence**. If you swap the order or skip one, React's internal hook "filing system" will get confused and `useState` might return the wrong value.

#### Rule-ish #2 — Custom hooks follow the same rules as built-in hooks

A custom hook is just a function that calls other hooks. If you break the rules inside your custom hook, you're still breaking the rules.

#### Rule-ish #3 — Don't skip effect dependencies

```tsx
// Tempting but dangerous
useEffect(() => {
  fetchData();
}, []); // "just run once"
```

If `fetchData` depends on props or state that can change, skipping dependencies gives you stale values and mysterious bugs.

#### Rule-ish #4 — `useRef` won't trigger re-renders

Changing `myRef.current` does **not** cause a re-render. That's the point — refs are for mutable values that don't trigger updates (DOM elements, timers, cached values). If you want the UI to update, use state.

#### Rule-ish #5 — `useMemo` and `useCallback` aren't permanent vaults

React is free to throw away memoized values and recompute them — especially in concurrent rendering. They're **performance hints**, not contracts. Use them when recomputation is expensive, not by default.

#### Rule-ish #6 — State setters replace, not merge

`useState` overwrites the value entirely (unlike class component `setState` which merged).

```tsx
// Merging manually
setUser((prev) => ({ ...prev, name: "Alex" }));
```

---

### 1.6.4 Typing Built-in Hooks

#### useState

```tsx
const [count, setCount] = useState<number>(0);
const [user, setUser] = useState<User | null>(null);

// Array state
const [items, setItems] = useState<string[]>([]);
```

#### useEffect

```tsx
useEffect(() => {
  const subscription = api.subscribe(userId);
  return () => subscription.unsubscribe(); // cleanup
}, [userId]); // dependency array
```

- First argument: effect function (optionally returns a cleanup function)
- Second argument: dependency array (when to re-run)
- Empty `[]` = run once on mount
- No array = run every render

#### useRef

```tsx
// DOM ref
const inputRef = useRef<TextInput>(null);

// Mutable value ref (no re-render on change)
const intervalRef = useRef<ReturnType<typeof setInterval> | null>(null);
```

#### useCallback

```tsx
const handlePress = useCallback(
  (id: string) => {
    navigation.navigate("Detail", { id });
  },
  [navigation]
);
```

#### useMemo

```tsx
const sortedItems = useMemo(() => {
  return items.sort((a, b) => a.value - b.value);
}, [items]);
```

#### useContext

```tsx
interface ThemeContextType {
  dark: boolean;
  toggle: () => void;
}

const ThemeContext = React.createContext<ThemeContextType>({
  dark: false,
  toggle: () => {},
});

const theme = useContext(ThemeContext);
```

#### useReducer

```tsx
interface State {
  count: number;
}

type Action = { type: "increment" } | { type: "decrement" } | { type: "reset"; payload: number };

function reducer(state: State, action: Action): State {
  switch (action.type) {
    case "increment":
      return { count: state.count + 1 };
    case "decrement":
      return { count: state.count - 1 };
    case "reset":
      return { count: action.payload };
  }
}

const [state, dispatch] = useReducer(reducer, { count: 0 });
dispatch({ type: "increment" });
dispatch({ type: "reset", payload: 10 });
```

---

### 1.6.5 Patterns That Apply Across Hooks

#### Pattern #1 — Custom Hooks for Reusability

Instead of copy-pasting fetch logic everywhere, extract it:

```tsx
function useFetch<T>(url: string): ApiResult<T> {
  const [data, setData] = useState<T | null>(null);
  const [error, setError] = useState<string | null>(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    let ignore = false;
    fetch(url)
      .then((res) => res.json())
      .then((json) => {
        if (!ignore) setData(json);
      })
      .catch((err) => {
        if (!ignore) setError(err.message);
      })
      .finally(() => {
        if (!ignore) setLoading(false);
      });
    return () => {
      ignore = true;
    };
  }, [url]);

  return { data: data as T, error, loading };
}

// Usage
const { data, error, loading } = useFetch<User[]>("/api/users");
```

#### Pattern #2 — Co-Locate Related State & Effects

Keep related state and effects near each other so the connection is obvious:

```tsx
// GOOD: query and results live together
function Search() {
  const [query, setQuery] = useState("");
  const [results, setResults] = useState<Item[]>([]);

  useEffect(() => {
    fetch(`/search?q=${query}`)
      .then((res) => res.json())
      .then(setResults);
  }, [query]);
}
```

#### Pattern #3 — Split Big Hooks Into Smaller Hooks

```tsx
// Instead of one massive hook...
function useUser() { /* fetch user */ }
function useNotifications() { /* fetch notifications */ }
function useLiveUpdates() { /* websocket */ }

// ...compose them
function useDashboardData() {
  const user = useUser();
  const notifications = useNotifications();
  useLiveUpdates();
  return { user, notifications };
}
```

#### Pattern #4 — Hook Composition (Hooks Calling Hooks)

Hooks can and should call other hooks:

```tsx
function useAuth() {
  const [user, setUser] = useState<User | null>(null);
  useEffect(() => {
    authApi.getCurrentUser().then(setUser);
  }, []);
  return user;
}

function useUserProfile() {
  const user = useAuth();
  const profile = useFetch<Profile>(user ? `/profile/${user.id}` : "");
  return profile;
}
```

#### Pattern #5 — Event Hooks

Hooks that wire up and tear down event listeners cleanly:

```tsx
function useKeyPress(targetKey: string, handler: (e: KeyboardEvent) => void) {
  useEffect(() => {
    function onKeyDown(e: KeyboardEvent) {
      if (e.key === targetKey) handler(e);
    }
    window.addEventListener("keydown", onKeyDown);
    return () => window.removeEventListener("keydown", onKeyDown);
  }, [targetKey, handler]);
}
```

---

### 1.6.6 Common Pitfalls & How to Avoid Them

#### Pitfall #1 — The Infinite `useEffect` Loop

```tsx
// BAD: effect updates state that is also a dependency
useEffect(() => {
  setCount(count + 1);
}, [count]); // render -> effect -> setState -> render -> forever
```

**Fix:** Only update state inside an effect intentionally. Use functional updates or rethink where the state update belongs.

#### Pitfall #2 — Missing Dependencies in Effects

```tsx
// BAD: user.id is used but not listed
useEffect(() => {
  fetchProfile(user.id);
}, []); // stale user.id
```

**Fix:** List all dependencies. If the effect runs too often, restructure — don't just suppress the warning.

#### Pitfall #3 — Stale Closures

```tsx
// BAD: count is captured once and never updates
useEffect(() => {
  const interval = setInterval(() => {
    console.log(count); // always logs the initial value
  }, 1000);
  return () => clearInterval(interval);
}, []); // count is missing

// FIX: use a ref or include count in dependencies
const countRef = useRef(count);
countRef.current = count;

useEffect(() => {
  const interval = setInterval(() => {
    console.log(countRef.current); // always fresh
  }, 1000);
  return () => clearInterval(interval);
}, []);
```

#### Pitfall #4 — Over-Memoizing Everything

`useMemo` and `useCallback` have a cost. Only use them when:

- The computation is genuinely expensive
- You're passing a function/value to a `React.memo` child that would re-render otherwise

#### Pitfall #5 — Breaking Hook Order in Custom Hooks

Never call hooks conditionally — not in components, not in custom hooks, nowhere.

```tsx
// BAD
function useData(shouldFetch: boolean) {
  if (shouldFetch) {
    const [data, setData] = useState(null); // hook order breaks
  }
}

// GOOD
function useData(shouldFetch: boolean) {
  const [data, setData] = useState(null);
  useEffect(() => {
    if (shouldFetch) { /* fetch... */ }
  }, [shouldFetch]);
}
```

#### Pitfall #6 — State Setters Are Asynchronous

```tsx
// BAD: both read the same stale count
setCount(count + 1);
setCount(count + 1); // still only +1

// GOOD: use functional updates for sequential updates
setCount((c) => c + 1);
setCount((c) => c + 1); // now +2
```

---

### 1.6.7 Hooks and Performance

#### Tool #1 — `React.memo` + `useCallback` = Fewer Re-Renders

```tsx
const Child = React.memo(({ onPress }: { onPress: () => void }) => {
  return <Pressable onPress={onPress}><Text>Tap</Text></Pressable>;
});

function Parent() {
  const [count, setCount] = useState(0);
  const handlePress = useCallback(() => setCount((c) => c + 1), []);
  return <Child onPress={handlePress} />;
}
```

Child only re-renders when `onPress` identity changes — which it won't, thanks to `useCallback`.

#### Tool #2 — `useMemo` for Expensive Calculations

```tsx
const sortedData = useMemo(() => {
  return data.sort((a, b) => a.value - b.value);
}, [data]);
```

Without `useMemo`, the sort runs on every render. With it, only when `data` changes.

#### Tool #3 — Splitting Components & Hooks

Smaller components re-render independently. Smaller hooks keep logic isolated and testable. This is often a bigger win than any memoization trick.

#### Tool #4 — Avoid Unnecessary State

```tsx
// BAD: storing derived data in state
const [filtered, setFiltered] = useState<Item[]>([]);
useEffect(() => setFiltered(list.filter(/* ... */)), [list]);

// GOOD: compute on the fly
const filtered = useMemo(() => list.filter(/* ... */), [list]);
```

#### Myth — "Always Optimize Early"

Premature optimization is a time sink. **Profile first, then optimize.** Use the React DevTools Profiler to see what's actually slow.

---

### 1.6.8 How Hooks Work Under the Hood

#### A Hook is just a function call in a list

React keeps an internal array (or linked list) of hook states for each component.

- **First render:** runs your function top to bottom, stores each hook's initial value by position
- **Re-renders:** runs your function again, matches each hook call to its position in the list

That's why "only call hooks at the top level" exists — if you skip a hook call, React's indexing gets out of sync.

#### The Hook Slot Machine

Think of each hook call as pulling a lever:

1. `useState("Hello")` → slot #1
2. `useEffect(...)` → slot #2
3. `useState(42)` → slot #3

Next render, React expects the same order. Skip slot #2 and slot #3's state gets assigned to #2.

#### Where is this stored?

React stores hook state in its internal **Fiber tree**:

- Each component has a **Fiber node**
- Each Fiber has a **linked list of hook objects**
- Each hook object stores its state, effect callbacks, refs, and bookkeeping

#### What about effects?

`useEffect` doesn't run immediately. React stores the effect function, then schedules it to run **after the paint**. `useLayoutEffect` runs synchronously after DOM updates but **before** the browser paints.

This is why effects don't block rendering and cleanup runs before the next effect or unmount.

#### Concurrent Rendering

With concurrent features, a render can be started, paused, thrown away, or replayed — hooks still work because the state lives in the Fiber tree, not your function. React always matches the correct hook state to the correct render attempt.

---

### 1.6.9 Testing Hooks

Test hooks indirectly through component behavior with React Testing Library. For isolated custom hook tests, use `renderHook`:

```tsx
import { renderHook, act } from "@testing-library/react";
import { useCounter } from "./useCounter";

test("increments the counter", () => {
  const { result } = renderHook(() => useCounter());

  act(() => {
    result.current.increment();
  });

  expect(result.current.count).toBe(1);
});
```

Key tips:

- Always wrap state updates in `act()` so React flushes effects before assertions
- Mock API calls or timers if your hook depends on them
- Test the **outcome**, not the effect itself
- If a hook is tightly coupled to UI, test it through the component instead

---

## Checklist

- [ ] Understand basic types, union, literal
- [ ] Write interface / type for props & state
- [ ] Use generics for hooks & utility functions
- [ ] Typed useState, useRef, useCallback, useMemo, useReducer, useContext
- [ ] Write custom hooks with generic types
- [ ] Understand JSX/TSX syntax for components
- [ ] Master ES6+ (destructuring, spread, async/await)
- [ ] Know the 2 official rules + 6 implicit rules of hooks
- [ ] Recognize the 5 hook patterns (custom, co-locate, split, compose, event)
- [ ] Identify and avoid the 6 common pitfalls
- [ ] Apply performance tools deliberately (memo, callback, splitting)
- [ ] Understand how hooks work under the hood (Fiber, slot order, scheduling)
- [ ] Test custom hooks with renderHook + act

---

## References

- [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/)
- [React TypeScript Cheatsheet](https://react-typescript-cheatsheet.netlify.app/)
- [TypeScript for React Native](https://reactnative.dev/docs/typescript)
- [Rules of Hooks — React Docs](https://react.dev/reference/rules/rules-of-hooks)
- [React Deep Dive: Hooks — DEV.to](https://dev.to/a1guy)
