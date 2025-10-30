In Java, the `Object` class is the root of the class hierarchy. Every class in Java directly or indirectly inherits from the `Object` class. This means that the `Object` class provides fundamental methods that are available to every Java object. Understanding the `Object` class and its methods is crucial for effective Java programming.

### Key Methods of the `Object` Class

1\. **`public final Class<?> getClass()`**

   - Returns the runtime class of the object.

   - Example:

     
```java

     Person person = new Person("Alice", 30);

     System.out.println(person.getClass()); // Output: class Person

```

2\. **`public int hashCode()`**

   - Returns a hash code value for the object. This method is used in hashing-based collections like `HashMap`.

   - Example:

    
```java

     Person person = new Person("Alice", 30);

     System.out.println(person.hashCode());

```

3\. **`public boolean equals(Object obj)`**

   - Indicates whether some other object is "equal to" this one. It is often overridden to provide meaningful equality logic.

   - Example:

     
```java

     @Override

     public boolean equals(Object obj) {

         if (this == obj) return true;

         if (obj == null || getClass() != obj.getClass()) return false;

         Person person = (Person) obj;

         return age == person.age && Objects.equals(name, person.name);

     }

```

4\. **`protected Object clone() throws CloneNotSupportedException`**

   - Creates and returns a copy (clone) of this object. This method should be overridden if a class needs to support cloning.

   - Example:

 
```java

     @Override

     protected Object clone() throws CloneNotSupportedException {

         return super.clone();

     }

```

5\. **`public String toString()`**

   - Returns a string representation of the object. It is often overridden to provide a meaningful string representation.

   - Example:

    
```java

     @Override

     public String toString() {

         return "Person{name='" + name + "', age=" + age + "}";

     }

```

6\. **`public final void notify()`**

   - Wakes up a single thread that is waiting on this object's monitor.

7\. **`public final void notifyAll()`**

   - Wakes up all threads that are waiting on this object's monitor.

8\. **`public final void wait(long timeout) throws InterruptedException`**

   - Causes the current thread to wait until it is awakened, typically by being notified or interrupted.

9\. **`public final void wait(long timeout, int nanos) throws InterruptedException`**

   - Causes the current thread to wait until it is awakened, typically by being notified or interrupted.

10\. **`public final void wait() throws InterruptedException`**

    - Causes the current thread to wait until it is awakened, typically by being notified or interrupted.

### Example: Utilizing the `Object` Class Methods

Let's create a `Person` class and demonstrate how some of these methods can be overridden and used.

```java

import java.util.Objects;

public class Person {

    private String name;

    private int age;

    public Person(String name, int age) {

        this.name = name;

        this.age = age;

    }

    // Override toString method

    @Override

    public String toString() {

        return "Person{name='" + name + "', age=" + age + "}";

    }

    // Override equals method

    @Override

    public boolean equals(Object obj) {

        if (this == obj) return true;

        if (obj == null || getClass() != obj.getClass()) return false;

        Person person = (Person) obj;

        return age == person.age && Objects.equals(name, person.name);

    }

    // Override hashCode method

    @Override

    public int hashCode() {

        return Objects.hash(name, age);

    }

    // Main method to demonstrate the usage

    public static void main(String[] args) {

        Person person1 = new Person("Alice", 30);

        Person person2 = new Person("Bob", 25);

        Person person3 = new Person("Alice", 30);

        // toString

        System.out.println(person1); // Output: Person{name='Alice', age=30}

        // equals

        System.out.println(person1.equals(person2)); // Output: false

        System.out.println(person1.equals(person3)); // Output: true

        // hashCode

        System.out.println(person1.hashCode()); // Output: hash code value

        System.out.println(person3.hashCode()); // Output: same hash code value as person1

    }

}

```

### Explanation:

1\. **`toString`**: Provides a string representation of the `Person` object. This is useful for debugging and logging.

2\. **`equals`**: Compares two `Person` objects for equality based on their `name` and `age`.

3\. **`hashCode`**: Returns a hash code value for the object, which is consistent with the `equals` method.

### Summary

The `Object` class is fundamental to Java's class hierarchy. Understanding and effectively utilizing its methods allows developers to implement meaningful equality checks, generate appropriate hash codes, and provide informative string representations for their classes. Additionally, methods like `wait` and `notify` are crucial for thread synchronization in concurrent programming.
