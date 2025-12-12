# Introduction to JUnit

## What is JUnit?

JUnit is a widely-used testing framework for Java applications. It provides a comprehensive platform for writing and running repeatable automated tests. JUnit is essential for Test-Driven Development (TDD) and is the de facto standard for unit testing in Java.

### Key Characteristics

- **Open Source**: Free to use and modify, with extensive community support
- **Annotation-Based**: Uses Java annotations to identify test methods
- **Assertions**: Provides rich assertion methods to validate expected outcomes
- **Test Runners**: Executes tests and reports results
- **Integration**: Works seamlessly with build tools (Maven, Gradle) and IDEs (Eclipse, IntelliJ)

### JUnit Evolution

| Version | Release Year | Key Features |
|---------|--------------|--------------|
| JUnit 3 | 2000 | Test classes extend TestCase, naming conventions |
| JUnit 4 | 2006 | Annotation-based, no inheritance required |
| JUnit 5 | 2017 | Modular architecture, Java 8+, lambda support |

---

## Why Use JUnit?

### Benefits of Unit Testing

1. **Early Bug Detection**
   - Find issues before they reach production
   - Reduce debugging time and costs
   - Increase code reliability

2. **Documentation**
   - Tests serve as living documentation
   - Show how code is intended to be used
   - Provide examples for other developers

3. **Refactoring Confidence**
   - Modify code with safety net
   - Ensure changes don't break functionality
   - Support continuous improvement

4. **Design Improvement**
   - Encourages modular, testable code
   - Identifies tightly coupled components
   - Promotes better architecture

### JUnit in the Development Lifecycle

```
┌─────────────────────────────────────────┐
│          Development Workflow           │
├─────────────────────────────────────────┤
│  1. Write failing test (Red)            │
│  2. Write minimal code to pass (Green)  │
│  3. Refactor code (Refactor)            │
│  4. Run tests to verify                 │
│  5. Repeat cycle                        │
└─────────────────────────────────────────┘
```

---

## JUnit 5 Architecture

JUnit 5 is composed of three sub-projects:

### JUnit Platform
- Foundation for launching testing frameworks on the JVM
- Provides TestEngine API for developing frameworks
- Console launcher for running tests from command line

### JUnit Jupiter
- New programming model for writing tests
- Extension model for writing test extensions
- Contains all new annotations and assertions

### JUnit Vintage
- Provides backward compatibility
- Runs JUnit 3 and JUnit 4 tests
- Migration support for legacy projects

```
┌───────────────────────────────────────┐
│         JUnit 5 Platform              │
│  (Test Discovery & Execution)         │
├───────────────────────────────────────┤
│  JUnit Jupiter  │  JUnit Vintage      │
│  (JUnit 5 Tests)│  (JUnit 3/4 Tests)  │
└───────────────────────────────────────┘
```

---

## Setting Up JUnit 5

### Maven Dependencies

Add the following to your `pom.xml`:

```xml
<dependencies>
    <!-- JUnit 5 Jupiter API for writing tests -->
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter-api</artifactId>
        <version>5.10.0</version>
        <scope>test</scope>
    </dependency>

    <!-- JUnit 5 Jupiter Engine for running tests -->
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter-engine</artifactId>
        <version>5.10.0</version>
        <scope>test</scope>
    </dependency>
</dependencies>

<build>
    <plugins>
        <!-- Maven Surefire Plugin for running tests -->
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-surefire-plugin</artifactId>
            <version>2.22.2</version>
        </plugin>
    </plugins>
</build>
```

### Gradle Dependencies

Add to your `build.gradle`:

```gradle
dependencies {
    testImplementation 'org.junit.jupiter:junit-jupiter-api:5.10.0'
    testRuntimeOnly 'org.junit.jupiter:junit-jupiter-engine:5.10.0'
}

test {
    useJUnitPlatform()
}
```

---

## Basic JUnit 5 Annotations

### Core Annotations

| Annotation | Purpose | Usage |
|------------|---------|-------|
| `@Test` | Marks a method as a test method | Method level |
| `@BeforeEach` | Executed before each test method | Method level |
| `@AfterEach` | Executed after each test method | Method level |
| `@BeforeAll` | Executed once before all tests (must be static) | Method level |
| `@AfterAll` | Executed once after all tests (must be static) | Method level |
| `@DisplayName` | Declares custom display name for test | Class/Method |
| `@Disabled` | Disables a test class or method | Class/Method |

### Example: Test Lifecycle

```java
import org.junit.jupiter.api.*;

public class CalculatorTest {

    private Calculator calculator;

    @BeforeAll
    static void setupAll() {
        System.out.println("Setting up test suite...");
        // Runs once before all tests
        // Must be static
    }

    @BeforeEach
    void setUp() {
        calculator = new Calculator();
        System.out.println("Creating new Calculator instance");
        // Runs before each test method
    }

    @Test
    @DisplayName("Should add two positive numbers correctly")
    void testAddition() {
        int result = calculator.add(5, 3);
        Assertions.assertEquals(8, result);
    }

    @Test
    void testSubtraction() {
        int result = calculator.subtract(10, 4);
        Assertions.assertEquals(6, result);
    }

    @AfterEach
    void tearDown() {
        calculator = null;
        System.out.println("Cleaning up after test");
        // Runs after each test method
    }

    @AfterAll
    static void tearDownAll() {
        System.out.println("Tearing down test suite...");
        // Runs once after all tests
        // Must be static
    }

    @Test
    @Disabled("Not implemented yet")
    void testMultiplication() {
        // This test will be skipped
    }
}
```

---

## Writing Your First Test

### Class Under Test

```java
public class Calculator {

    public int add(int a, int b) {
        return a + b;
    }

    public int subtract(int a, int b) {
        return a - b;
    }

    public int multiply(int a, int b) {
        return a * b;
    }

    public double divide(int a, int b) {
        if (b == 0) {
            throw new IllegalArgumentException("Cannot divide by zero");
        }
        return (double) a / b;
    }
}
```

### Test Class

```java
import org.junit.jupiter.api.*;
import static org.junit.jupiter.api.Assertions.*;

class CalculatorTest {

    private Calculator calculator;

    @BeforeEach
    void setUp() {
        calculator = new Calculator();
    }

    @Test
    @DisplayName("Addition of two positive numbers")
    void testAddPositiveNumbers() {
        // Arrange
        int a = 5;
        int b = 3;

        // Act
        int result = calculator.add(a, b);

        // Assert
        assertEquals(8, result, "5 + 3 should equal 8");
    }

    @Test
    @DisplayName("Division by zero should throw exception")
    void testDivisionByZero() {
        // Assert that exception is thrown
        assertThrows(IllegalArgumentException.class, () -> {
            calculator.divide(10, 0);
        });
    }

    @Test
    @DisplayName("Division of positive numbers")
    void testDivision() {
        double result = calculator.divide(10, 2);
        assertEquals(5.0, result, 0.001);
    }
}
```

---

## Test Naming Conventions

### Method Names

Good test names are descriptive and follow patterns:

1. **Given-When-Then Pattern**
```java
@Test
void givenTwoPositiveNumbers_whenAdding_thenReturnSum() {
    // Test implementation
}
```

2. **Should Pattern**
```java
@Test
void shouldReturnSumWhenAddingTwoPositiveNumbers() {
    // Test implementation
}
```

3. **Test with DisplayName**
```java
@Test
@DisplayName("Should throw exception when dividing by zero")
void testDivisionByZero() {
    // Test implementation
}
```

### Class Names

- Test class name = Class name + "Test"
- Example: `Calculator` → `CalculatorTest`
- Place in same package structure under `src/test/java`

---

## Project Structure

```
my-project/
├── src/
│   ├── main/
│   │   └── java/
│   │       └── com/
│   │           └── example/
│   │               └── Calculator.java
│   └── test/
│       └── java/
│           └── com/
│               └── example/
│                   └── CalculatorTest.java
├── pom.xml (or build.gradle)
└── target/ (or build/)
```

---

## Running Tests

### From IDE
- Right-click on test class or method
- Select "Run" or "Run as JUnit Test"
- View results in test runner panel

### From Command Line

**Maven:**
```bash
mvn test
mvn test -Dtest=CalculatorTest
mvn test -Dtest=CalculatorTest#testAddition
```

**Gradle:**
```bash
gradle test
gradle test --tests CalculatorTest
gradle test --tests CalculatorTest.testAddition
```

### Test Results

JUnit provides detailed output:
- Green: All tests passed
- Red: One or more tests failed
- Yellow: Tests skipped or disabled
- Stack traces for failures
- Execution time for each test

---

## Best Practices

### 1. One Assertion Per Test (When Possible)
```java
// Good - focuses on one behavior
@Test
void shouldAddPositiveNumbers() {
    assertEquals(8, calculator.add(5, 3));
}

// Acceptable - related assertions
@Test
void shouldHandleNegativeNumbers() {
    assertEquals(-2, calculator.add(-5, 3));
    assertEquals(-8, calculator.add(-5, -3));
}
```

### 2. Keep Tests Independent
```java
// Bad - tests depend on execution order
@Test
void testA() {
    calculator.value = 10; // State modification
}

@Test
void testB() {
    assertEquals(10, calculator.value); // Depends on testA
}

// Good - each test is independent
@Test
void testA() {
    Calculator calc = new Calculator();
    calc.value = 10;
    assertEquals(10, calc.value);
}

@Test
void testB() {
    Calculator calc = new Calculator();
    calc.value = 20;
    assertEquals(20, calc.value);
}
```

### 3. Test Edge Cases
- Null values
- Empty collections
- Boundary values (min, max)
- Invalid inputs
- Exception scenarios

### 4. Fast Tests
- Unit tests should execute quickly
- Avoid external dependencies (databases, networks)
- Use test doubles for dependencies

### 5. Readable Tests
- Use descriptive names
- Follow Arrange-Act-Assert pattern
- Keep tests simple and focused

---

## Summary

| Concept | Key Points |
|---------|------------|
| What is JUnit | Java testing framework for automated unit tests |
| Architecture | Platform + Jupiter + Vintage components |
| Annotations | @Test, @BeforeEach, @AfterEach, @DisplayName |
| Setup | Maven/Gradle dependencies, test directory structure |
| Best Practices | Independent tests, edge cases, fast execution |

## Next Topic

Continue to [Unit Testing Introduction To Junit](./02-unit-testing-introduction-to-junit.md) to dive deeper into writing effective unit tests.
