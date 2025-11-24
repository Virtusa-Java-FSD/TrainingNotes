
### 1.  Interfaces

Interfaces in TypeScript define the structure (or "shape") of an object, including properties, methods, and their types. They act as a blueprint, ensuring objects conform to a specific format.

- **Key Benefits**:
  - Enforce type safety for objects.
  - Improve code readability and maintainability.
  - Support inheritance (extending) for reusable shapes.
  - No runtime impact—interfaces are erased during compilation.
- **Use Cases**: API responses, function params, class blueprints, config objects.

Unlike classes, interfaces don't provide implementation; they only describe types.

Basic Syntax:
```ts
interface InterfaceName {
  property: Type;
  method?(): ReturnType;  // Optional method
}
```

### 2. Declaring and Using Interfaces

Declare an interface with the `interface` keyword, then use it to type variables, params, or returns.

Example: Simple Object Shape
```ts
interface Person {
  name: string;
  age: number;
  greet(): string;  // Method signature (no body)
}

const alice: Person = {
  name: "Alice",
  age: 30,
  greet() {
    return `Hello, I'm ${this.name}`;
  }
};

console.log(alice.greet());  // "Hello, I'm Alice"

// Errors:
// const bob: Person = { name: "Bob" };  // Error: Missing 'age' and 'greet'
// alice.age = "thirty";                 // Error: Type 'string' not assignable to 'number'
```

Interfaces can type function parameters:
```ts
function printPerson(person: Person): void {
  console.log(`${person.name} is ${person.age} years old.`);
}

printPerson(alice);  // OK
// printPerson({ name: "Eve", age: 25 });  // Error: Missing 'greet'
```

### 3. Extending Interfaces

Interfaces can inherit from others using `extends`, promoting reuse and hierarchy.

Example:
```ts
interface Animal {
  name: string;
  move(): void;
}

interface Dog extends Animal {
  breed: string;
  bark(): string;
}

const myDog: Dog = {
  name: "Buddy",
  breed: "Labrador",
  move() { console.log("Running"); },
  bark() { return "Woof!"; }
};

console.log(myDog.bark());  // "Woof!"

// myDog has all Animal properties + Dog's
```

Multiple Inheritance:
```ts
interface Flyer {
  fly(): void;
}

interface Swimmer {
  swim(): void;
}

interface Bird extends Flyer, Swimmer {
  name: string;
}

const duck: Bird = {
  name: "Duck",
  fly() { console.log("Flying"); },
  swim() { console.log("Swimming"); }
};
```

### 4. Implementing Interfaces with Classes

Classes can `implement` interfaces, ensuring they match the shape. This is like a contract for class instances.

Example:
```ts
interface Vehicle {
  speed: number;
  accelerate(): void;
}

class Car implements Vehicle {
  speed: number = 0;  // Must match interface

  accelerate() {
    this.speed += 10;
  }
}

const myCar = new Car();
myCar.accelerate();
console.log(myCar.speed);  // 10

// class Bike implements Vehicle { }  // Error: Missing 'speed' and 'accelerate'
```

Interfaces can also extend classes (advanced, mixes types with implementation details).

### 5. Optional and Readonly Properties

- **Optional Properties**: Use `?` to make properties non-required.
- **Readonly Properties**: Use `readonly` to prevent reassignment after initialization.

Example:
```ts
interface Book {
  title: string;
  author: string;
  pages?: number;       // Optional
  readonly isbn: string;  // Immutable after creation
}

const myBook: Book = {
  title: "TypeScript Guide",
  author: "Jane Doe",
  isbn: "123-456"
};

console.log(myBook.pages);  // undefined (OK, since optional)
myBook.pages = 200;         // OK

// myBook.isbn = "new";     // Error: Cannot assign to 'isbn' because it is readonly
```

Use Case: Flexible configs where some fields are optional but others are fixed.

### 6. Index Signatures

For objects with dynamic keys (like dictionaries), use index signatures.

Syntax: `[key: KeyType]: ValueType;`

Example:
```ts
interface StringDictionary {
  [key: string]: string;  // Any string key maps to string value
}

const colors: StringDictionary = {
  red: "#FF0000",
  green: "#00FF00"
};

colors.blue = "#0000FF";  // OK
console.log(colors["red"]);  // "#FF0000"

// colors[0] = "black";     // Error: Key must be string (implicit coercion fails)
```

Numeric Indexes:
```ts
interface NumberArray {
  [index: number]: number;
}

const scores: NumberArray = [90, 85, 88];
console.log(scores[1]);  // 85
```

Mix with fixed properties (fixed must be compatible with index type).

### 7. Function Interfaces

Interfaces can describe function shapes (call signatures).

Example:
```ts
interface AddFunction {
  (a: number, b: number): number;  // Call signature
}

const add: AddFunction = (x, y) => x + y;
console.log(add(2, 3));  // 5

// const subtract: AddFunction = (x, y) => x - y;  // OK, but if return type mismatched: Error
```

Hybrid (object with properties and callable):
```ts
interface Counter {
  (start: number): number;  // Callable
  increment: number;        // Property
}

function createCounter(): Counter {
  const counter = (start: number) => start + counter.increment;
  counter.increment = 1;
  return counter;
}

const c = createCounter();
console.log(c(10));  // 11
c.increment = 2;
console.log(c(10));  // 12
```

#### 8. Interfaces vs. Type Aliases

Interfaces and type aliases both define types, but they differ in capabilities, syntax, and use cases. Type aliases are more flexible for non-object types, while interfaces excel at object shapes and extensibility.

##### Key Differences

| Feature                  | Interfaces                          | Type Aliases                                |
|--------------------------|-------------------------------------|---------------------------------------------|
| Syntax                   | `interface Name { ... }`            | `type Name = ...;`                          |
| Extensibility            | Can be extended/reopened            | Cannot be extended (use intersections)      |
| Merging                  | Declaration merging (reopen)        | No merging; duplicates error                |
| Primitives/Unions        | Limited (mostly objects)            | Full support (primitives, unions, tuples)   |
| Computed Properties      | No                                  | Yes (e.g., mapped types)                    |
| Implements in Classes    | Yes                                 | No (classes can't implement aliases)        |
| Performance/Readability  | Better for objects/classes          | Better for complex/composed types           |
| Error Messages           | Often more descriptive              | Can be more concise                         |

- **When to Use Interfaces**:
  - For object shapes that might be extended (e.g., in libraries).
  - When working with classes (implements).
  - For declaration merging (e.g., augmenting global types).

- **When to Use Type Aliases**:
  - For unions, intersections, primitives, or tuples.
  - Function types or generics.
  - When you need computed/mapped types.

##### Examples Showing Contrasts

1. **Basic Object Shape** (Similar, but interfaces allow merging):
   ```ts
   // Interface
   interface User {
     id: number;
     name: string;
   }

   // Can merge (reopen)
   interface User {
     email?: string;  // Adds to existing
   }

   const userI: User = { id: 1, name: "Alice", email: "a@example.com" };

   // Type Alias
   type UserType = {
     id: number;
     name: string;
   };

   // Cannot merge: type UserType = { email: string };  // Error: Duplicate identifier

   // Instead, intersect for extension
   type ExtendedUser = UserType & { email?: string };
   const userT: ExtendedUser = { id: 1, name: "Alice", email: "a@example.com" };
   ```

2. **Union Types** (Aliases win):
   ```ts
   // Type Alias: Easy
   type ID = number | string;
   let myId: ID = "abc";  // OK

   // Interface: Not directly possible for primitives/unions
   // interface ID = number | string;  // Error
   // Workaround: interface ID { value: number | string; }  // Clunky
   ```

3. **Extending/Intersecting**:
   ```ts
   // Interface Extend
   interface Animal { name: string; }
   interface Cat extends Animal { meow(): void; }

   // Type Alias Intersect
   type AnimalType = { name: string; };
   type CatType = AnimalType & { meow(): void; };
   ```

4. **Classes Implementing** (Interfaces only):
   ```ts
   // Works with Interface
   interface Printable { print(): void; }
   class Document implements Printable {
     print() { console.log("Printing"); }
   }

   // Error with Type Alias
   type PrintableType = { print(): void; };
   // class Document implements PrintableType { ... }  // Error: Can only implement interface
   ```

5. **Mapped/Computed Types** (Aliases only):
   ```ts
   type Keys = "one" | "two";
   type Mapped = { [K in Keys]: number };  // { one: number; two: number; }

   // Interface can't do this dynamically
   // interface Mapped { [K in Keys]: number; }  // Error
   ```

In practice, use interfaces for 80% of object typing needs, and aliases for the rest. Microsoft (TS creators) recommends interfaces for public APIs.

#### Best Practices and Tips
- Name interfaces with PascalCase (e.g., `UserProfile`); prefix with `I` if your team prefers (e.g., `IUser`—debated style).
- Keep interfaces focused (single responsibility).
- Use `extends` for DRY code.
- Enable `strictNullChecks` in tsconfig for safer optionals.
- Avoid overusing index signatures (prefer explicit props for type safety).
- For large projects, interfaces aid in refactoring due to better IDE support.
