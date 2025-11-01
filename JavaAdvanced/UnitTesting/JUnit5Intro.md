JUnit 5 is a powerful and flexible Java testing framework that provides several new features and improvements over JUnit 4. It is designed to support modern Java features, such as lambdas and streams, and offers enhanced capabilities for writing and executing tests. In this introduction, we'll cover the fundamental concepts of JUnit 5 along with code examples to illustrate each concept.

### 1. Setting Up JUnit 5

To start using JUnit 5 in your Java project, you need to add the necessary dependencies. Here's how you can do it using Maven:

#### Maven Dependencies

Add the following dependencies to your `pom.xml` file:

```xml

<dependencies>

    <!-- JUnit 5 API -->

    <dependency>

        <groupId>org.junit.jupiter</groupId>

        <artifactId>junit-jupiter-api</artifactId>

        <version>6.0.0</version>

        <scope>test</scope>

    </dependency>

</dependencies>

```

### 2. Writing and Running Tests

Let's explore how to write and run tests using JUnit 5 with some examples.

#### Example 1: Simple JUnit 5 Test

Create a test class and write a simple test using JUnit 5's `@Test` annotation and assertions from the `Assertions` class:

```java

import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.*;

public class MyFirstJUnit5Test {

    @Test

    void additionTest() {

        int result = 2 + 3;

        assertEquals(5, result, "2 + 3 should equal 5");

    }

    @Test

    void multiplicationTest() {

        int result = 2 * 3;

        assertEquals(6, result, () -> "2 * 3 should equal 6");

    }

}

```

In this example:

- `@Test` annotation marks a method as a test method.

- `assertEquals` method is used to assert that the expected and actual values are equal.

#### Example 2: Assertions and Test Lifecycle Methods

JUnit 5 provides various assertion methods in the `Assertions` class for verifying expected outcomes. Additionally, you can use lifecycle methods like `@BeforeEach` and `@AfterEach` for setup and teardown:

```java

import org.junit.jupiter.api.BeforeEach;

import org.junit.jupiter.api.AfterEach;

import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.*;

public class LifecycleAndAssertionsTest {

    @BeforeEach

    void setUp() {

        System.out.println("Before each test");

    }

    @AfterEach

    void tearDown() {

        System.out.println("After each test");

    }

    @Test

    void additionTest() {

        int result = 2 + 3;

        assertEquals(5, result, "2 + 3 should equal 5");

    }

    @Test

    void multiplicationTest() {

        int result = 2 * 3;

        assertEquals(6, result, () -> "2 * 3 should equal 6");

    }

}

```

- `@BeforeEach` and `@AfterEach` are used to annotate methods that should be executed before and after each test method, respectively.

- `System.out.println` statements in these methods demonstrate the test lifecycle.

### 3. Parameterized Tests

JUnit 5 supports parameterized tests, allowing you to run the same test with different sets of parameters. Use `@ParameterizedTest` and provide parameters using various sources like `@ValueSource`, `@EnumSource`, `@CsvSource`, and custom methods.

#### Example: Parameterized Test with `@ValueSource`

```java

import org.junit.jupiter.params.ParameterizedTest;

import org.junit.jupiter.params.provider.ValueSource;

import static org.junit.jupiter.api.Assertions.*;

public class ParameterizedTestExample {

    @ParameterizedTest

    @ValueSource(ints = {1, 2, 3})

    void testIsPositive(int number) {

        assertTrue(number > 0);

    }

}

```

- `@ParameterizedTest` marks a method as a parameterized test.

- `@ValueSource` provides a single parameter to the annotated method.

### 4. Nested Tests

JUnit 5 supports nested tests to create hierarchical test structures using `@Nested` annotation. This helps in organizing tests and improving readability.

#### Example: Nested Tests

```java

import org.junit.jupiter.api.Nested;

import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.*;

public class NestedTestExample {

    @Nested

    class MathOperations {

        @Test

        void testAddition() {

            assertEquals(4, 2 + 2);

        }

        @Test

        void testSubtraction() {

            assertEquals(2, 4 - 2);

        }

    }

}

```

- `@Nested` annotation allows nesting of test classes within another class.

- Inner classes define logical groupings of tests (`MathOperations` in this case).

### 5. Running Tests

You can run JUnit 5 tests using your IDE (IntelliJ IDEA, Eclipse) with built-in support or using Maven/Gradle commands:

- **Maven**: Execute `mvn test` command to run tests.

- **Gradle**: Execute `gradle test` command to run tests.

### Conclusion

JUnit 5 provides a modern and flexible framework for writing and executing tests in Java applications. With its annotations, assertion methods, parameterized tests, nested tests, and improved architecture, JUnit 5 supports effective testing practices that contribute to the overall quality and reliability of your software. By following these examples and principles, you can leverage JUnit 5 to develop robust and maintainable test suites for your Java projects.