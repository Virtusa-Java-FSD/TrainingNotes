##  Introduction to TypeScript

#### What is TypeScript?
TypeScript (TS) is a **superset** of JavaScript developed and maintained by Microsoft. It adds **static typing**, modern ECMAScript features (even before browsers support them), and powerful tooling on top of JavaScript.

Key points:
- Every valid JavaScript file is also a valid TypeScript file.
- TypeScript code is **transpiled** (compiled) to plain JavaScript using the `tsc` compiler.
- It runs anywhere JavaScript runs: browsers, Node.js, Deno, etc.
- Primary goal: Catch bugs early during development by adding type checking at compile time.

Official definition:
> "TypeScript is JavaScript with syntax for types."

Website: https://www.typescriptlang.org/

---

#### TypeScript vs JavaScript – Key Differences

| Feature                  | JavaScript                          | TypeScript                                      |
|--------------------------|-------------------------------------|-------------------------------------------------|
| Typing                   | Dynamic, weak typing                | Static, strong typing (optional but recommended) |
| Type errors              | Found at runtime                    | Found at compile time                           |
| Compilation              | No compilation needed               | Must be compiled (`tsc`) to JS                  |
| Supports classes/interfaces/enums | Limited or via ES6+             | Full support with rich syntax                   |
| Browser compatibility    | Runs directly                       | Must be transpiled first                       |
| Learning curve           | Easier for beginners                | Steeper due to types, but safer in large apps   |
| Tooling (IDE support)    | Good                                | Excellent (autocomplete, refactoring, errors)   |
| Best for                 | Small scripts, quick prototypes     | Large-scale applications, teams, maintainability|

**Main advantage of TS**: You catch bugs like `undefined is not a function`, wrong argument types, or misspelled properties **before runtime**.

Example of a common JS bug caught by TS:
```js
// JavaScript – runs but crashes at runtime
function add(a, b) {
  return a + b;
}
add("hello", 5); // returns "hello5" – probably not intended!
```

```ts
// TypeScript – compile error
function add(a: number, b: number): number {
  return a + b;
}
add("hello", 5); // Error: Argument of type 'string' is not assignable to 'number'
```

---

#### Primitive Types in TypeScript

TypeScript has the following built-in primitive types:

| Type        | Description                                 | Example                              |
|-------------|---------------------------------------------|--------------------------------------|
| `number`    | Floating point and integer values           | `let age: number = 30;`              |
| `string`    | Text values                                 | `let name: string = "Alice";`        |
| `boolean`   | true or false                               | `let isActive: boolean = true;`      |
| `null`      | Intentional absence of value                | `let n: null = null;`                |
| `undefined` | Uninitialized value                         | `let u: undefined = undefined;`      |
| `symbol`    | Unique immutable identifiers (ES6)         | `let sym = Symbol("key");`           |
| `bigint`    | Arbitrary precision integers (ES2020)       | `let big: bigint = 9007199254740992n;`|

Also two special types:
- `void` – used for functions that return nothing
- `never` – represents values that never occur (e.g., infinite loops or functions that always throw)

Type inference (you don’t always need to write the type):
```ts
let price = 100;        // inferred as number
let username = "john";  // inferred as string
price = "hello";        // Error!
```

Explicit annotation:
```ts
let score: number = 95;
```

Lowercase vs Uppercase:
- Use lowercase: `number`, `string`, `boolean`
- Uppercase (`Number`, `String`) refer to wrapper objects → avoid them

---

#### Arrays in TypeScript

Two ways to declare typed arrays:

1. Using element type followed by `[]`:
```ts
let numbers: number[] = [1, 2, 3, 4];
let names: string[] = ["Alice", "Bob"];
```

2. Using generic `Array<T>`:
```ts
let scores: Array<number> = [90, 85, 88];
let fruits: Array<string> = ["apple", "banana"];
```

You can have arrays of any type:
```ts
let mixed: (string | number)[] = ["hello", 42, "world"];
let booleans: boolean[] = [true, false, true];
```

Read-only arrays:
```ts
let fixedList: readonly number[] = [1, 2, 3];
// fixedList.push(4); // Error – readonly
```

Multi-dimensional arrays:
```ts
let matrix: number[][] = [
  [1, 2],
  [3, 4]
];
```

---

#### Tuples in TypeScript

Tuples allow you to express an array with a **fixed number of elements** where each element can have a different type.

Syntax: `[type1, type2, ..., typeN]`

```ts
let person: [string, number, boolean] = ["Alice", 30, true];

// Correct order matters
let point: [number, number] = [10, 20];
// point = [20, 10, 5]; // Error – wrong length

// Optional elements (TS 4.0+)
let rgb: [number, number, number?] = [255, 128]; // third is optional

// Rest elements (TS 4.3+)
let user: [number, ...string[]] = [1, "John", "Doe", "Admin"];
```

Destructuring works perfectly:
```ts
let [id, username, isAdmin] = user;
```

Use cases:
- Returning multiple values from a function
- CSV row parsing
- Coordinates with metadata

Example function returning a tuple:
```ts
function getUserInfo(): [number, string, boolean] {
  return [42, "Alice", true];
}
const [userId, name, active] = getUserInfo();
```

---

#### Enums in TypeScript

Enums allow you to define a set of named constants.

##### 1. Numeric enums (default)
```ts
enum Direction {
  Up,    // 0
  Down,  // 1
  Left,  // 2
  Right  // 3
}

let move: Direction = Direction.Up; // 0
```

You can set starting value:
```ts
enum Status {
  Pending = 1,
  Approved,
  Rejected
}
// Pending=1, Approved=2, Rejected=3
```

##### 2. String enums
```ts
enum Color {
  Red = "RED",
  Green = "GREEN",
  Blue = "BLUE"
}

let favorite: Color = Color.Green; // "GREEN"
```

##### 3. Heterogeneous enums (rarely recommended)
```ts
enum Mixed {
  Yes = 1,
  No = "NO"
}
```

##### 4. Const enums (optimized, no runtime object)
```ts
const enum Role {
  Admin = "ADMIN",
  User = "USER",
  Guest = "GUEST"
}

let currentRole = Role.Admin; // compiled to literal "ADMIN"
```

Accessing enum members:
```ts
console.log(Direction[0]);     // "Up"   (reverse mapping for numeric)
console.log(Color.Red);        // "RED"
```

Best practices:
- Prefer **string enums** in most modern code (more readable, no reverse mapping issues)
- Use **const enums** when performance matters and you don’t need runtime reflection

---

### Summary

| Concept         | Purpose                                      | Key Benefit                     |
|-----------------|----------------------------------------------|---------------------------------|
| TypeScript      | JS + static types                            | Early bug detection             |
| vs JavaScript   | Static vs Dynamic typing                     | Safer large codebases           |
| Primitive types | number, string, boolean, etc.                | Type safety for basic values    |
| Arrays          | Typed homogeneous collections                | Prevent wrong element types     |
| Tuples          | Fixed-size, heterogeneous arrays             | Precise data structure modeling |
| Enums           | Named sets of constants                      | Self-documenting code           |
