
### 1. Generics 

**Generics** let you create reusable components that work with **any type** while preserving type safety.

#### Basic Syntax
```ts
function identity<T>(arg: T): T {
  return arg;
}

let output1 = identity<string>("hello");  // T = string
let output2 = identity(42);               // T inferred as number
```

#### Why Generics > `any`
```ts
// BAD – loses type info
function bad(arg: any): any { return arg; }
let str = bad("hello");
str.toUpperCase(); // No error at compile time → runtime bug possible

// GOOD – preserves type
function good<T>(arg: T): T { return arg; }
let str2 = good("hello");
str2.toUpperCase(); // OK, str2 is string
```

#### Generic Interfaces & Classes
```ts
interface Box<T> {
  value: T;
}

const stringBox: Box<string> = { value: "secret" };
const numBox: Box<number> = { value: 100 };

// Generic class
class Stack<T> {
  private items: T[] = [];
  push(item: T) { this.items.push(item); }
  pop(): T | undefined { return this.items.pop(); }
}

const numberStack = new Stack<number>();
numberStack.push(1);
// numberStack.push("oops"); // Error!
```

### 2. Generic Constraints (`extends`)

Without constraints, `T` can be **anything**, even primitives without methods.

#### Basic Constraint
```ts
function logLength<T extends { length: number }>(arg: T): T {
  console.log(arg.length);  // OK now
  return arg;
}

logLength("hello");        // OK
logLength([1, 2, 3]);      // OK
logLength({ length: 10 }); // OK
// logLength(42);          // Error: number has no .length
```

#### Multiple Constraints
```ts
function merge<T extends object, U extends object>(obj1: T, obj2: U): T & U {
  return { ...obj1, ...obj2 };
}

const merged = merge({ name: "Alice" }, { age: 30 });
// merged has { name: string; age: number }
```

#### Keyof Constraint (very common!)
```ts
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

const person = { name: "Bob", age: 35 };
const name = getProperty(person, "name"); // OK
// getProperty(person, "email"); // Error: "email" not in person
```

### 3. Built-in Utility Types (The Real Power)

TypeScript ships with extremely useful generic utility types.

| Utility Type       | Purpose                                                      | Example |
|--------------------|--------------------------------------------------------------|---------|
| `Partial<T>`       | Make all properties optional                                 | `Partial<User>` |
| `Required<T>`      | Make all properties required                                 | `Required<PartialUser>` |
| `Pick<T, K>`       | Pick only selected keys                                      | `Pick<User, 'id' | 'name'>` |
| `Omit<T, K>`       | Remove selected keys                                         | `Omit<User, 'password'>` |
| `Record<K, T>`     | Object with keys of type K and values of type T              | `Record<string, number>` |
| `Readonly<T>`      | Make all properties readonly                                 | `Readonly<User>` |
| `Exclude<T, U>`    | Exclude types from a union                                   | `Exclude<'a'|'b'|'c', 'b'>` → `'a'|'c'` |
| `Extract<T, U>`    | Extract matching types from a union                          | `Extract<'a'|'b', 'a'|'c'>` → `'a'` |
| `NonNullable<T>`   | Remove `null` and `undefined` from union                     | `NonNullable<string|null>` → `string` |
| `ReturnType<T>`    | Get return type of a function                                | `ReturnType<typeof fetchUser>` |
| `InstanceType<T>`  | Get instance type of a class constructor                     | `InstanceType<typeof UserModel>` |

#### Real-World Examples

Let’s define a base type:
```ts
interface User {
  id: number;
  name: string;
  email: string;
  password: string;
  createdAt: Date;
  isActive?: boolean;
}
```

##### 1. `Partial<T>` – Great for updates/patches
```ts
function updateUser(id: number, updates: Partial<User>): void {
  // Only pass fields you want to change
}

updateUser(1, { name: "Alice", isActive: true });
// No need to pass password, id, etc.
```

##### 2. `Required<T>` – Force all fields (rare, but useful)
```ts
type StrictUser = Required<User>;
// isActive is now required (no longer optional)
```

##### 3. `Pick<T, Keys>` – Create smaller types
```ts
type UserPreview = Pick<User, "id" | "name" | "email">;
// Only these three fields

function getUserPreview(user: User): UserPreview {
  return {
    id: user.id,
    name: user.name,
    email: user.email
  };
}
```

##### 4. `Omit<T, Keys>` – Remove sensitive fields
```ts
type PublicUser = Omit<User, "password" | "createdAt">;
// Perfect for API responses

const safeUser: PublicUser = {
  id: 1,
  name: "Bob",
  email: "bob@example.com",
  isActive: true
};
```

##### 5. `Record<K, T>` – Dictionary/Map types
```ts
// User permissions
type Permission = "read" | "write" | "delete";

const permissions: Record<Permission, boolean> = {
  read: true,
  write: false,
  delete: false
};

// Dynamic keys
type Scores = Record<string, number>;
const examScores: Scores = {
  alice: 95,
  bob: 88,
  charlie: 72
};
```

##### 6. `Readonly<T>` – Immutable data
```ts
type Config = Readonly<{
  apiUrl: string;
  timeout: number;
}>;

const config: Config = {
  apiUrl: "https://api.example.com",
  timeout: 5000
};

// config.timeout = 10000; // Error: cannot assign to readonly
```

##### 7. `ReturnType<T>` – Extract function return types
```ts
function createUser(name: string) {
  return {
    id: Math.random(),
    name,
    createdAt: new Date()
  };
}

type CreatedUser = ReturnType<typeof createUser>;
// Same as { id: number; name: string; createdAt: Date }

const user: CreatedUser = createUser("Alice");
```

##### 8. `InstanceType<T>` – Get class instance type
```ts
class ApiClient {
  constructor(public baseUrl: string) {}
  get<T>(path: string): Promise<T> { /* ... */ }
}

type ClientInstance = InstanceType<typeof ApiClient>;
// = ApiClient

const client: ClientInstance = new ApiClient("https://api.com");
```

### 4. Combining Utility Types (Real Power Moves)

```ts
// User form data (only some fields, all optional)
type UserFormData = Partial<Pick<User, "name" | "email" | "isActive">>;

// API response without password
type UserResponse = Omit<User, "password"> & {
  token: string;
};

// Settings object: string keys → specific config shape
type AppSettings = Record<string, {
  value: string | number | boolean;
  readonly label: string;
}>;
```

### 5. Practical Generic Functions with Constraints

```ts
// Deep partial (for nested updates)
type DeepPartial<T> = {
  [P in keyof T]?: T[P] extends object ? DeepPartial<T[P]> : T[P];
};

function deepUpdate<T>(original: T, updates: DeepPartial<T>): T {
  // implementation with Object.assign, recursion, etc.
  return { ...original, ...updates };
}
```

```ts
// Safe JSON parser
function parseJson<T>(json: string, validator: (data: unknown) => data is T): T | null {
  try {
    const data = JSON.parse(json);
    return validator(data) ? data : null;
  } catch {
    return null;
  }
}
```

### Summary Cheat Sheet

| Use Case                            | Recommended Type                                 |
|-------------------------------------|--------------------------------------------------|
| Optional updates                    | `Partial<T>`                                     |
| Public API response                 | `Omit<T, "password" | "secretField">`            |
| Pick only name + email              | `Pick<T, "name" | "email">`                      |
| Dictionary / map                    | `Record<KeyType, ValueType>`                     |
| Immutable config                    | `Readonly<T>`                                    |
| Extract function return type        | `ReturnType<typeof fn>`                          |
| Make optional → required            | `Required<T>`                                    |
| Remove null/undefined               | `NonNullable<T>`                                 |

Master these 4 utility types first:
1. `Partial<T>`
2. `Pick<T, K>`
3. `Omit<T, K>`
4. `Record<K, T>`

They cover 90% of real-world type transformation needs