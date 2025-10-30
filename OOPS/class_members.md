

### 1. Class

A class is a blueprint for creating objects. It defines a datatype by bundling data and methods that work on the data into one single unit. A class in Java is defined using the `class` keyword.

Example:

```java

public class Person {

    // Class body

}

```

### 2. Fields

Fields (or instance variables) are variables declared inside a class but outside any method, constructor, or block. They represent the state or attributes of an object.

Example:

```java

public class Person {

    // Fields

    String name;

    int age;

}

```

### 3. Constructors

A constructor is a special method that is called when an object is instantiated. It initializes the newly created object. A constructor has the same name as the class and does not have a return type.

Example:

```java

public class Person {

    String name;

    int age;

    // Constructor

    public Person(String name, int age) {

        this.name = name;

        this.age = age;

    }

}

```

### 4. Methods

Methods define the behavior of objects created from a class. They can operate on the fields of the class and are used to perform operations or return information about the object.

Example:

```java

public class Person {

    String name;

    int age;

    public Person(String name, int age) {

        this.name = name;

        this.age = age;

    }

    // Method

    public void displayInfo() {

        System.out.println("Name: " + name);

        System.out.println("Age: " + age);

    }

}

```

### 5. Static Members

Static members belong to the class rather than any specific instance. This means they are shared among all instances of the class. Static fields are also known as class variables, and static methods can be called without creating an instance of the class.

Example:

```java

public class Person {

    String name;

    int age;

    // Static field

    static int numberOfPeople = 0;

    public Person(String name, int age) {

        this.name = name;

        this.age = age;

        numberOfPeople++; // Increment the static field

    }

    // Static method

    public static int getNumberOfPeople() {

        return numberOfPeople;

    }

    public void displayInfo() {

        System.out.println("Name: " + name);

        System.out.println("Age: " + age);

    }

}

```

### Complete Example

Here is a complete example that ties all these concepts together:

```java

public class Person {

    // Fields

    String name;

    int age;

    // Static field

    static int numberOfPeople = 0;

    // Constructor

    public Person(String name, int age) {

        this.name = name;

        this.age = age;

        numberOfPeople++; // Increment the static field

    }

    // Method

    public void displayInfo() {

        System.out.println("Name: " + name);

        System.out.println("Age: " + age);

    }

    // Static method

    public static int getNumberOfPeople() {

        return numberOfPeople;

    }

    // Main method to test the class

    public static void main(String[] args) {

        Person person1 = new Person("Alice", 30);

        Person person2 = new Person("Bob", 25);

        person1.displayInfo();

        person2.displayInfo();

        System.out.println("Number of people: " + Person.getNumberOfPeople());

    }

}

```

### Explanation of the Example:

1\. **Class**: The `Person` class is defined with fields, a constructor, methods, and static members.

2\. **Fields**: `name` and `age` are instance variables representing the state of each `Person` object.

3\. **Constructor**: `Person(String name, int age)` initializes the fields `name` and `age` when a new `Person` object is created and increments the static field `numberOfPeople`.

4\. **Methods**: `displayInfo()` is a non-static method that prints the details of a `Person` object.

5\. **Static Members**: `numberOfPeople` is a static field shared by all `Person` objects. `getNumberOfPeople()` is a static method that returns the number of `Person` objects created.

6\. **Main Method**: The `main` method creates two `Person` objects, calls their `displayInfo()` method, and prints the total number of `Person` objects using the static method `getNumberOfPeople()`.

This example demonstrates how classes encapsulate data and behavior, how constructors initialize objects, how methods define object behavior, and how static members can be shared across all instances of a class.