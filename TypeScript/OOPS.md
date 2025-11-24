

Classes in TypeScript build on ES6 classes but add type safety, access modifiers, and more. Inheritance allows code reuse through hierarchies. Function typing ensures safe function signatures, while overloads handle multiple call patterns for the same function.

## 1. Classes in TypeScript

Classes define blueprints for objects, encapsulating data (properties) and behavior (methods). TypeScript adds static typing to prevent runtime errors.

##### 1.1 Basic Class Definition
- **Syntax**: Use `class` keyword, with typed properties and methods.
- **Key Rules**:
    - Properties must be declared with types.
    - Constructors initialize instances.
    - Methods can have parameters and return types.

Example:
```ts
class Animal {
  name: string;  // Property (must be initialized in constructor or directly)

  constructor(name: string) {
    this.name = name;
  }

  move(distance: number): void {
    console.log(`${this.name} moved ${distance} meters.`);
  }
}

const dog = new Animal("Buddy");
dog.move(10);  // "Buddy moved 10 meters."
// dog.name = 123;  // Error: Type 'number' not assignable to 'string'
```

##### 1.2 Access Modifiers
TypeScript supports three modifiers for properties and methods:
- `public`: Accessible everywhere (default if omitted).
- `private`: Only accessible within the class.
- `protected`: Accessible within the class and subclasses.

Example:
```ts
class Person {
  public name: string;         // Accessible anywhere
  private age: number;         // Only inside Person
  protected email: string;     // Inside Person and subclasses

  constructor(name: string, age: number, email: string) {
    this.name = name;
    this.age = age;
    this.email = email;
  }

  public getAge(): number {
    return this.age;  // OK, inside class
  }
}

const alice = new Person("Alice", 30, "alice@example.com");
console.log(alice.name);     // "Alice" (public)
// console.log(alice.age);   // Error: 'age' is private
// console.log(alice.email); // Error: 'email' is protected
console.log(alice.getAge()); // 30 (via public method)
```

##### 1.3 Readonly Properties
- Use `readonly` to make properties immutable after initialization.
- Can be set in constructor or declaration.

Example:
```ts
class Circle {
  readonly radius: number;

  constructor(radius: number) {
    this.radius = radius;
  }

  getArea(): number {
    return Math.PI * this.radius ** 2;
  }
}

const c = new Circle(5);
// c.radius = 10;  // Error: Cannot assign to 'radius' because it is readonly
console.log(c.getArea());  // ~78.54
```

##### 1.4 Parameter Properties
- Shorthand: Declare and initialize properties directly in constructor params using modifiers.
- Reduces boilerplate.

Example:
```ts
class Employee {
  constructor(
    public name: string,      // Automatically creates public 'name'
    private salary: number    // Automatically creates private 'salary'
  ) {}

  getSalary(): number {
    return this.salary;
  }
}

const emp = new Employee("Bob", 50000);
console.log(emp.name);      // "Bob"
// console.log(emp.salary); // Error: private
console.log(emp.getSalary());  // 50000
```

##### 1.5 Static Properties/Methods
- Belong to the class itself, not instances.
- Accessed via class name.

Example:
```ts
class MathUtils {
  static PI: number = 3.14159;  // Static property

  static add(a: number, b: number): number {  // Static method
    return a + b;
  }
}

console.log(MathUtils.PI);         // 3.14159
console.log(MathUtils.add(2, 3));  // 5
// const mu = new MathUtils();
// mu.PI;  // Error: Static members not on instances
```

Best Practices for Classes:
- Use access modifiers to encapsulate data (favor private/protected).
- Prefer parameter properties for concise constructors.
- Declare all properties upfront to avoid `any` inference.
- Use classes for stateful logic; functions for stateless.

## 2. Inheritance in TypeScript

Inheritance allows a subclass to extend a base class, inheriting properties/methods while adding or overriding specifics.

##### 2.1 Extending Classes
- **Syntax**: Use `extends` keyword.
- Subclass inherits all non-private members.

Example:
```ts
class Animal {
  name: string;

  constructor(name: string) {
    this.name = name;
  }

  move(distance: number): void {
    console.log(`${this.name} moved ${distance} meters.`);
  }
}

class Dog extends Animal {
  breed: string;

  constructor(name: string, breed: string) {
    super(name);  // Must call super() first
    this.breed = breed;
  }

  bark(): string {
    return "Woof!";
  }
}

const myDog = new Dog("Buddy", "Labrador");
myDog.move(5);      // "Buddy moved 5 meters." (inherited)
console.log(myDog.bark());  // "Woof!" (new)
console.log(myDog.breed);   // "Labrador"
```

##### 2.2 Overriding Methods
- Subclasses can redefine inherited methods.
- Use `super.method()` to call base version.

Example:
```ts
class Bird extends Animal {
  move(distance: number): void {
    super.move(distance);  // Call base
    console.log("But actually, I flew!");  // Override addition
  }
}

const eagle = new Bird("Eagle");
eagle.move(100);  // "Eagle moved 100 meters.\nBut actually, I flew!"
```

##### 2.3 Abstract Classes
- Cannot be instantiated directly; used as base for subclasses.
- Can have abstract methods (signatures only; must be implemented in subclasses).

Example:
```ts
abstract class Shape {
  abstract getArea(): number;  // Must be implemented by subclasses

  printArea(): void {
    console.log(`Area: ${this.getArea()}`);
  }
}

class Rectangle extends Shape {
  constructor(private width: number, private height: number) {
    super();
  }

  getArea(): number {
    return this.width * this.height;
  }
}

const rect = new Rectangle(4, 5);
rect.printArea();  // "Area: 20"

// new Shape();  // Error: Cannot create instance of abstract class
// class Circle extends Shape {}  // Error: Must implement getArea()
```

Best Practices for Inheritance:
- Favor composition over deep inheritance hierarchies (avoids fragility).
- Always call `super()` in subclass constructors.
- Use abstract classes for shared logic with required overrides.
- Protected members are ideal for inheritance.

| Feature              | Purpose                                      | Example Use Case                     |
|----------------------|----------------------------------------------|--------------------------------------|
| Basic Classes        | Encapsulate data/behavior                    | Modeling entities like User          |
| Access Modifiers     | Control visibility                           | Hiding internal state                |
| Inheritance (extends)| Reuse code in hierarchies                    | Animal → Dog, Cat                    |
| Overriding           | Customize inherited behavior                 | Specialized move() in Bird           |
| Abstract Classes     | Enforce implementation in subclasses         | Shape with required getArea()        |

### 3. Function Typing

Functions in TypeScript can be typed for parameters, returns, and even as types themselves (e.g., via interfaces or aliases).

##### 3.1 Typing Function Parameters and Returns
- Explicit types prevent mismatches.

Example:
```ts
function add(a: number, b: number): number {  // Params and return typed
  return a + b;
}

const result: number = add(2, 3);  // 5
// add("2", 3);  // Error: 'string' not assignable to 'number'
```

##### 3.2 Function Types
- Describe functions as types using aliases or interfaces.
- Useful for callbacks, higher-order functions.

Using Type Alias:
```ts
type MathOperation = (x: number, y: number) => number;

const subtract: MathOperation = (a, b) => a - b;
console.log(subtract(5, 2));  // 3
```

Using Interface:
```ts
interface Logger {
  (message: string): void;  // Call signature
}

const log: Logger = console.log;
log("Hello");  // "Hello"
```

##### 3.3 Arrow Functions and Contexts
- Arrow functions preserve `this` context.
- Type `this` explicitly if needed (advanced).

Example:
```ts
class Timer {
  seconds: number = 0;

  start() {
    setInterval(() => {  // Arrow preserves 'this'
      this.seconds++;
      console.log(this.seconds);
    }, 1000);
  }
}

const t = new Timer();
t.start();  // Logs 1, 2, 3... (this.seconds works)
```

Best Practices:
- Always type returns unless inferred correctly.
- Use function types for props in React or callbacks.
- Prefer arrows for concise, context-safe functions.

### 4. Function Overloads

Overloads allow a function to have multiple signatures, handling different argument types/counts with one implementation.

##### 4.1 Defining Overloads
- **Syntax**: Multiple signatures before the implementation.
- Implementation must be compatible with all signatures (often uses `any` or unions).

Example:
```ts
function greet(name: string): string;                // Overload 1
function greet(name: string, greeting: string): string;  // Overload 2
function greet(name: string, greeting?: string): string {  // Implementation
  return greeting ? `${greeting}, ${name}!` : `Hello, ${name}!`;
}

console.log(greet("Alice"));          // "Hello, Alice!"
console.log(greet("Bob", "Hi"));      // "Hi, Bob!"
// greet(123);                        // Error: No overload matches
```

##### 4.2 Advanced Overloads with Unions
- Use for type-specific behavior.

Example:
```ts
function double(value: number): number;
function double(value: string): string;
function double(value: number | string): number | string {
  if (typeof value === "number") {
    return value * 2;
  } else {
    return value + value;
  }
}

console.log(double(5));      // 10
console.log(double("hi"));   // "hihi"
// double(true);             // Error: No overload matches
```

Best Practices:
- Use overloads sparingly; prefer unions or generics when possible.
- Ensure implementation handles all cases (use type guards).
- Great for libraries with flexible APIs.

| Concept             | Key Benefit                          | Common Use Case                      |
|---------------------|--------------------------------------|--------------------------------------|
| Function Typing     | Ensures param/return safety          | Callbacks, APIs                      |
| Overloads           | Multiple call patterns               | Flexible functions like parse()      |

