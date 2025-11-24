
## 1. Functions: Default, Rest, and Optional Parameters

TypeScript enhances JavaScript functions with type safety for parameters and return values. Here, we'll explore **optional parameters** (params that can be omitted), **default parameters** (params with fallback values), and **rest parameters** (for variable-length arguments).

##### 1.1 Optional Parameters
- **Syntax**: Mark a parameter as optional with `?` after its name.
- **Key Rules**:
    - Optional params must come after required ones.
    - When calling, you can omit them (they become `undefined`).
    - TypeScript checks for type mismatches but allows omission.
- **Use Case**: Flexible functions where some args are not always needed.

Example:
```ts
function greet(name: string, greeting?: string): string {
  if (greeting) {
    return `${greeting}, ${name}!`;
  }
  return `Hello, ${name}!`;
}

console.log(greet("Alice"));            // "Hello, Alice!"
console.log(greet("Bob", "Welcome"));   // "Welcome, Bob!"
// greet(123); // Error: Argument of type 'number' is not assignable to 'string'
```

Advanced: Optional params can have union types including `undefined`.
```ts
function logMessage(message: string | undefined): void {
  console.log(message ?? "No message provided");  // Uses nullish coalescing
}
logMessage();        // "No message provided"
logMessage("Hi");    // "Hi"
```

##### 1.2 Default Parameters
- **Syntax**: Assign a default value with `= value` in the param list.
- **Key Rules**:
    - Defaults kick in if the arg is omitted or `undefined`.
    - Can be combined with optional params (but defaults make them effectively optional).
    - Type inference often works for the default value's type.
- **Use Case**: Provide sensible fallbacks to simplify calls.

Example:
```ts
function calculateArea(length: number, width: number = 10): number {
  return length * width;
}

console.log(calculateArea(5));      // 50 (width defaults to 10)
console.log(calculateArea(5, 20));  // 100
// calculateArea("five"); // Error: Type 'string' is not assignable to 'number'
```

Combining with Optional:
```ts
function buildUser(name: string, age: number = 30, isActive?: boolean): string {
  return `${name} is ${age} years old. Active: ${isActive ?? false}`;
}

console.log(buildUser("Alice"));          // "Alice is 30 years old. Active: false"
console.log(buildUser("Bob", 25, true));  // "Bob is 25 years old. Active: true"
```

##### 1.3 Rest Parameters
- **Syntax**: Use `...paramName: Type[]` to capture remaining args as an array.
- **Key Rules**:
    - Must be the last parameter.
    - Typed as an array (e.g., `number[]`).
    - Great for variadic functions.
- **Use Case**: Handle unlimited args, like summing numbers or concatenating strings.

Example:
```ts
function sum(...numbers: number[]): number {
  return numbers.reduce((acc, curr) => acc + curr, 0);
}

console.log(sum(1, 2, 3));       // 6
console.log(sum(10));            // 10
console.log(sum());              // 0 (empty array)
// sum("a", 1); // Error: Argument of type 'string' is not assignable to 'number[]'
```

Combining All:
```ts
function createTeam(leader: string, coLeader?: string, ...members: string[]): string[] {
  const team = [leader];
  if (coLeader) team.push(coLeader);
  team.push(...members);
  return team;
}

console.log(createTeam("Alice", "Bob", "Charlie", "Dana"));  // ["Alice", "Bob", "Charlie", "Dana"]
console.log(createTeam("Eve"));                              // ["Eve"]
```

Best Practices for Functions:
- Always type params and returns for safety.
- Use defaults over optionals when a fallback makes sense.
- Avoid overusing rest params if arg count is predictable (use fixed params for clarity).
- Destructure rest params for cleaner code: `const [first, ...rest] = args;`.

| Feature            | Syntax Example                  | When to Use                          | Pros                          | Cons                          |
|--------------------|---------------------------------|--------------------------------------|-------------------------------|-------------------------------|
| Optional Params    | `param?: Type`                  | When arg might be omitted            | Flexibility                   | Can lead to `undefined` bugs |
| Default Params     | `param: Type = defaultValue`    | Provide fallback                     | Simpler calls                 | Hides omissions               |
| Rest Params        | `...param: Type[]`              | Variable args                        | Handles any count             | Less type safety on order     |

## 2. Type Aliases

Type aliases create shorthand names for complex types, improving readability and reusability. They're not new types—just nicknames.

- **Syntax**: `type AliasName = ExistingType;`
- **Key Rules**:
    - Can alias primitives, unions, intersections, objects, functions, etc.
    - Aliases are erased at compile time (no runtime impact).
    - Cannot be extended like interfaces (use interfaces for extendable shapes).
- **Use Case**: DRY (Don't Repeat Yourself) for repeated types in large codebases.

Basic Example:
```ts
type ID = number | string;  // Union type alias

let userId: ID = 42;        // OK
userId = "abc123";          // OK
// userId = true;           // Error
```

Object Alias:
```ts
type Point = {
  x: number;
  y: number;
};

function distance(p1: Point, p2: Point): number {
  return Math.sqrt((p1.x - p2.x) ** 2 + (p1.y - p2.y) ** 2);
}

const origin: Point = { x: 0, y: 0 };
console.log(distance(origin, { x: 3, y: 4 }));  // 5
```

Function Type Alias:
```ts
type GreetFn = (name: string) => string;

const hello: GreetFn = (name) => `Hello, ${name}`;
console.log(hello("World"));  // "Hello, World"
```

Advanced: Generics in Aliases
```ts
type ArrayOf<T> = T[];  // Like built-in Array<T>

let numbers: ArrayOf<number> = [1, 2, 3];
```

Best Practices:
- Use PascalCase for aliases (e.g., `UserProfile`).
- Prefer aliases for unions/intersections; use interfaces for object shapes that might extend.
- Aliases shine in config objects or API responses with nested types.

## 3. Type Inference

TypeScript automatically infers types based on values, reducing explicit annotations.

- **How It Works**: TS analyzes assignments, returns, and usages to deduce types.
- **Key Rules**:
    - Infers from literals, operations, and contexts.
    - "Best common type" for arrays/objects.
    - Falls back to `any` if unclear (avoid with `noImplicitAny` in tsconfig).
- **Use Case**: Less boilerplate while maintaining safety.

Examples:
```ts
let age = 30;             // Inferred: number
// age = "thirty";        // Error

const colors = ["red", "green"];  // Inferred: string[]
colors.push("blue");              // OK
// colors.push(1);                // Error

function add(a: number, b: number) {  // Return inferred: number
  return a + b;
}

let mixed = [1, "two"];  // Inferred: (number | string)[]
```

Contextual Typing:
```ts
window.onkeydown = (event) => {  // event inferred as KeyboardEvent
  console.log(event.key);        // OK
};
```

Best Practices:
- Rely on inference for simple cases; annotate for clarity in complex ones.
- Use `const` to narrow inference (prevents widening).
- Check tsconfig for strictness options like `strictNullChecks`.

## 4. Type Narrowing

Type narrowing refines broad types (e.g., unions) to specific ones using checks, allowing safer access.

- **Techniques**:
    - `typeof` for primitives.
    - `instanceof` for classes.
    - Discriminated unions (with literal tags).
    - User-defined type guards.
    - Control flow analysis (e.g., after `if` or `switch`).
- **Use Case**: Handle unions like `string | number` without errors.

Examples:

##### 4.1 typeof Guard
```ts
function printValue(value: string | number): void {
  if (typeof value === "string") {
    console.log(value.toUpperCase());  // Narrowed to string
  } else {
    console.log(value.toFixed(2));     // Narrowed to number
  }
}
```

##### 4.2 instanceof Guard
```ts
class Dog { bark() {} }
class Cat { meow() {} }

function makeSound(animal: Dog | Cat): void {
  if (animal instanceof Dog) {
    animal.bark();  // Narrowed to Dog
  } else {
    animal.meow();  // Narrowed to Cat
  }
}
```

##### 4.3 Discriminated Unions
```ts
type Circle = { kind: "circle"; radius: number };
type Square = { kind: "square"; side: number };
type Shape = Circle | Square;

function area(shape: Shape): number {
  switch (shape.kind) {
    case "circle": return Math.PI * shape.radius ** 2;  // Narrowed to Circle
    case "square": return shape.side ** 2;              // Narrowed to Square
  }
}
```

##### 4.4 Type Guards
Custom function returning type predicate:
```ts
function isString(value: unknown): value is string {
  return typeof value === "string";
}

function process(input: unknown): void {
  if (isString(input)) {
    console.log(input.length);  // Narrowed to string
  }
}
```

Best Practices:
- Use narrowing to avoid `as` assertions (which bypass checks).
- Prefer discriminated unions for tagged types.
- Combine with `never` for exhaustive checks:
  ```ts
  function unreachable(shape: never): never {
    throw new Error("Unreachable");
  }
  // Add to switch: default: return unreachable(shape);
  ```

### Summary

| Concept                  | Key Benefit                          | Common Pitfall                       |
|--------------------------|--------------------------------------|--------------------------------------|
| Function Params (Opt/Default/Rest) | Flexible, type-safe functions        | Order matters; avoid too many optionals |
| Type Aliases             | Readable, reusable types             | Not extendable like interfaces       |
| Type Inference           | Less code, automatic safety          | Can infer too broadly (use annotations) |
| Type Narrowing           | Safe handling of unions              | Forgetting exhaustive checks         |
