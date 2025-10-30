
### Wrapper Classes

Wrapper classes in Java provide a way to use primitive data types (int, char, etc.) as objects. Each primitive data type has a corresponding wrapper class in the `java.lang` package:

- `byte` -> `Byte`

- `short` -> `Short`

- `int` -> `Integer`

- `long` -> `Long`

- `float` -> `Float`

- `double` -> `Double`

- `char` -> `Character`

- `boolean` -> `Boolean`

These classes encapsulate a primitive type value in an object. Wrapper classes are useful in several scenarios such as working with collections (like `ArrayList`) that cannot store primitive types directly, needing to convert primitives to strings, or using methods that require objects.

### Autoboxing

Autoboxing is the automatic conversion that the Java compiler makes between the primitive types and their corresponding object wrapper classes. For example, converting an `int` to an `Integer`, a `double` to a `Double`, and so on. This feature was introduced in Java 5.

Here's an example of autoboxing:

```java

int num = 10;

Integer numObj = num; // Autoboxing: int to Integer

```

### Unboxing

Unboxing is the reverse process where the Java compiler automatically converts an object of a wrapper class to its corresponding primitive type. For example, converting an `Integer` to an `int`, a `Double` to a `double`, and so on.

Here's an example of unboxing:

```java

Integer numObj = 10;

int num = numObj; // Unboxing: Integer to int

```

### Examples

Here are a few examples demonstrating autoboxing and unboxing:

1\. **Autoboxing with Collections:**

  
```java

   import java.util.ArrayList;

   public class AutoboxingExample {

       public static void main(String[] args) {

           ArrayList<Integer> list = new ArrayList<>();

           list.add(10); // Autoboxing: int to Integer

           list.add(20);

           System.out.println(list);

       }

   }

```

2\. **Unboxing in Expressions:**

   
```java

   public class UnboxingExample {

       public static void main(String[] args) {

           Integer numObj = 10;

           int num = numObj; // Unboxing: Integer to int

           int result = numObj + 5; // Unboxing: Integer to int during addition

           System.out.println(result);

       }

   }

```

3\. **Autoboxing and Unboxing in Methods:**

```java

   public class BoxingUnboxingExample {

       public static void main(String[] args) {

           int num = 10;

           Integer numObj = box(num); // Autoboxing

           int result = unbox(numObj); // Unboxing

           System.out.println("Boxed: " + numObj);

           System.out.println("Unboxed: " + result);

       }

       public static Integer box(int num) {

           return num; // Autoboxing

       }

       public static int unbox(Integer numObj) {

           return numObj; // Unboxing

       }

   }

```

### Performance Considerations

While autoboxing and unboxing provide a convenient way to work with primitives and their wrapper classes, they come with some performance overhead due to the additional object creation and the possibility of `NullPointObject-Oriented Programming (OOP) is a programming paradigm based on the concept of "objects", which can contain data and code: data in the form of fields (often known as properties or attributes), and code in the form of procedures (often known as methods).

### Core Concepts of OOP

1\. **Classes and Objects**

   - **Class**: A blueprint for creating objects. It defines a datatype by bundling data and methods that work on the data into one single unit.

   - **Object**: An instance of a class. It represents an entity in the real world with attributes and behavior.

2\. **Encapsulation**

   - The concept of wrapping data (variables) and code (methods) together as a single unit. It restricts direct access to some of the object's components, which is a means of preventing accidental interference and misuse of the data.

   - Example:

```java

     public class Person {

         private String name;

         private int age;

         public String getName() {

             return name;

         }

         public void setName(String name) {

             this.name = name;

         }

         public int getAge() {

             return age;

         }

         public void setAge(int age) {

             this.age = age;

         }

     }

```

3\. **Inheritance**

   - A mechanism where one class (child class) inherits the attributes and methods of another class (parent class). It promotes code reusability.

   - Example:

```java

     public class Animal {

         public void eat() {

             System.out.println("This animal eats food.");

         }

     }

     public class Dog extends Animal {

         public void bark() {

             System.out.println("The dog barks.");

         }

     }

```

4\. **Polymorphism**

   - The ability of a variable, function, or object to take on multiple forms. It allows one interface to be used for a general class of actions.

   - Two types:

     - **Compile-time polymorphism (Method Overloading)**: Multiple methods in the same class with the same name but different parameters.

```java

       public class MathOperation {

           public int add(int a, int b) {

               return a + b;

           }

           public double add(double a, double b) {

               return a + b;

           }

       }

```

     - **Runtime polymorphism (Method Overriding)**: A subclass provides a specific implementation of a method that is already defined in its superclass.

```java

       public class Animal {

           public void sound() {

               System.out.println("Animal makes a sound");

           }

       }

       public class Dog extends Animal {

           @Override

           public void sound() {

               System.out.println("Dog barks");

           }

       }

```

5\. **Abstraction**

   - The concept of hiding the complex implementation details and showing only the necessary features of an object. It helps to reduce programming complexity and effort.

   - Achieved using abstract classes and interfaces.

   - Example:

```java

     abstract class Animal {

         abstract void sound();

     }

     class Dog extends Animal {

         void sound() {

             System.out.println("Dog barks");

         }

     }

     interface AnimalInterface {

         void sound();

     }

     class Cat implements AnimalInterface {

         public void sound() {

             System.out.println("Cat meows");

         }

     }

```

### Benefits of OOP

1\. **Modularity**: The source code for an object can be written and maintained independently of the source code for other objects.

2\. **Reusability**: Objects can be reused across programs.

3\. **Pluggability and Debugging Ease**: If a particular object turns out to be problematic, it can simply be removed and replaced with a better object.

4\. **Flexibility through Polymorphism**: Different objects can be accessed through the same interface, making it easier to write flexible and extensible code.

### Example: Simple OOP Implementation in Java

Here's a simple example that brings together all the core concepts:

```java

// Abstract class

abstract class Vehicle {

    private String brand;

    private int year;

    public Vehicle(String brand, int year) {

        this.brand = brand;

        this.year = year;

    }

    public String getBrand() {

        return brand;

    }

    public int getYear() {

        return year;

    }

    // Abstract method

    abstract void startEngine();

}

// Interface

interface Drivable {

    void drive();

}

// Inheritance

class Car extends Vehicle implements Drivable {

    private int numDoors;

    public Car(String brand, int year, int numDoors) {

        super(brand, year);

        this.numDoors = numDoors;

    }

    public int getNumDoors() {

        return numDoors;

    }

    // Overriding method

    @Override

    void startEngine() {

        System.out.println("Car engine started");

    }

    // Implementing interface method

    @Override

    public void drive() {

        System.out.println("Car is driving");

    }

}

// Main class to demonstrate OOP concepts

public class OOPExample {

    public static void main(String[] args) {

        Car myCar = new Car("Toyota", 2020, 4);

        System.out.println("Brand: " + myCar.getBrand());

        System.out.println("Year: " + myCar.getYear());

        System.out.println("Number of Doors: " + myCar.getNumDoors());

        myCar.startEngine();

        myCar.drive();

    }

}

```

In this example:

- **Encapsulation** is used in the `Vehicle` class by making its fields private and providing public getter methods.

- **Inheritance** is demonstrated by the `Car` class inheriting from the `Vehicle` class.

- **Polymorphism** is shown with method overriding (`startEngine` in `Car`).

- **Abstraction** is achieved using the abstract class `Vehicle` and the interface `Drivable`.

This is a basic introduction to OOP, illustrating how these fundamental concepts are used to build flexible, modular, and reusable code.erException` if the wrapper class object is null during unboxing.

Therefore, it's important to be mindful of these aspects, especially in performance-critical sections of code.

### Summary

- **Wrapper Classes:** Provide object representation for primitive data types.

- **Autoboxing:** Automatic conversion from a primitive type to its corresponding wrapper class.

- **Unboxing:** Automatic conversion from a wrapper class to its corresponding primitive type.

These features make it easier to work with collections and APIs that require objects, while still allowing you to use primitive types for their performance benefits.
