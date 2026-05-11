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

## 1.4 TypeScript for React Hooks

> This is the most important section for React Native.

### useState

```tsx
const [count, setCount] = useState<number>(0);
const [user, setUser] = useState<User | null>(null);
```

### useRef

```tsx
const inputRef = useRef<TextInput>(null);
```

### useCallback & useMemo

```tsx
const handlePress = useCallback((id: string) => {
  console.log(id);
}, []);

const filtered = useMemo(() => {
  return items.filter((item) => item.active);
}, [items]);
```

### Custom Hook with Generics

```tsx
function useFetch<T>(url: string): ApiResult<T> {
  const [data, setData] = useState<T | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    fetch(url)
      .then((res) => res.json())
      .then((json) => setData(json))
      .catch((err) => setError(err.message))
      .finally(() => setLoading(false));
  }, [url]);

  return { data: data as T, loading, error };
}
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

## 1.6 ES6+ Syntax Recap

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

## Checklist

- [ ] Understand basic types, union, literal
- [ ] Write interface / type for props & state
- [ ] Use generics for hooks & utility functions
- [ ] Typed useState, useRef, useCallback, useMemo
- [ ] Write custom hooks with generic types
- [ ] Understand JSX/TSX syntax for components
- [ ] Master ES6+ (destructuring, spread, async/await)

---

## References

- [TypeScript Handbook](https://www.typescriptlang.org/docs/handbook/)
- [React TypeScript Cheatsheet](https://react-typescript-cheatsheet.netlify.app/)
- [TypeScript for React Native](https://reactnative.dev/docs/typescript)
