# Unit Testing Assertion Types

## What are Assertions?

Assertions are statements that verify expected outcomes in your tests. They compare actual results with expected values and fail the test if they don't match. JUnit 5 provides a rich set of assertion methods through the `org.junit.jupiter.api.Assertions` class.

### Why Assertions Matter

- **Verification**: Confirm that code behaves as expected
- **Documentation**: Show what the code should do
- **Failure Detection**: Identify bugs immediately
- **Regression Prevention**: Ensure fixes stay fixed

---

## Basic Assertions

### assertEquals and assertNotEquals

Compare expected and actual values for equality.

```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class BasicAssertionsTest {

    @Test
    void testAssertEquals() {
        // Integer equality
        int expected = 10;
        int actual = 5 + 5;
        assertEquals(expected, actual);

        // String equality
        String expectedStr = "Hello";
        String actualStr = "Hello";
        assertEquals(expectedStr, actualStr);

        // With custom message
        assertEquals(100, calculateTotal(), "Total should be 100");
    }

    @Test
    void testAssertNotEquals() {
        assertNotEquals(5, 10);
        assertNotEquals("Hello", "World");
        assertNotEquals(5, 10, "Values should be different");
    }

    @Test
    void testFloatingPointEquality() {
        // For floating point, use delta (tolerance)
        double expected = 10.0;
        double actual = 10.0001;

        // This would fail without delta
        // assertEquals(expected, actual);

        // With delta - passes if difference is within tolerance
        assertEquals(expected, actual, 0.001, "Values should be within 0.001");
    }

    private int calculateTotal() {
        return 100;
    }
}
```

### Assertion Method Signatures

| Method | Parameters | Description |
|--------|------------|-------------|
| `assertEquals(expected, actual)` | Two values | Basic equality check |
| `assertEquals(expected, actual, message)` | Values + message | With custom failure message |
| `assertEquals(expected, actual, delta)` | Floating point + tolerance | For doubles/floats |
| `assertEquals(expected, actual, delta, message)` | All combined | Floating point with message |

---

## Boolean Assertions

### assertTrue and assertFalse

Verify boolean conditions.

```java
class BooleanAssertionsTest {

    @Test
    void testAssertTrue() {
        assertTrue(isEven(4));
        assertTrue(5 > 3);
        assertTrue("hello".startsWith("h"));
        assertTrue(isEven(10), "10 should be even");
    }

    @Test
    void testAssertFalse() {
        assertFalse(isEven(5));
        assertFalse(10 < 5);
        assertFalse("hello".startsWith("x"));
        assertFalse(isEven(7), "7 should not be even");
    }

    @Test
    void testComplexConditions() {
        List<String> list = Arrays.asList("apple", "banana", "cherry");

        assertTrue(list.size() > 0, "List should not be empty");
        assertTrue(list.contains("banana"), "List should contain banana");
        assertFalse(list.isEmpty(), "List should not be empty");
    }

    private boolean isEven(int number) {
        return number % 2 == 0;
    }
}
```

---

## Null Assertions

### assertNull and assertNotNull

Verify null or non-null values.

```java
class NullAssertionsTest {

    @Test
    void testAssertNull() {
        String nullString = null;
        assertNull(nullString);
        assertNull(getNullValue(), "Should return null");
    }

    @Test
    void testAssertNotNull() {
        String nonNullString = "Hello";
        assertNotNull(nonNullString);

        User user = createUser();
        assertNotNull(user, "User should not be null");
        assertNotNull(user.getId(), "User ID should be assigned");
    }

    @Test
    void testOptionalHandling() {
        Optional<String> optional = findUser("john");

        // Not recommended - checking Optional directly
        // assertNotNull(optional);

        // Better - check if value is present
        assertTrue(optional.isPresent(), "User should be found");
        assertEquals("john", optional.get());
    }

    private String getNullValue() {
        return null;
    }

    private User createUser() {
        return new User(1L, "John");
    }

    private Optional<String> findUser(String name) {
        return Optional.of(name);
    }
}
```

---

## Same and NotSame Assertions

### assertSame and assertNotSame

Check if two references point to the same object (reference equality, not value equality).

```java
class SameAssertionsTest {

    @Test
    void testAssertSame() {
        String str1 = "Hello";
        String str2 = str1;  // Same reference

        assertSame(str1, str2, "Both should reference the same object");

        // String interning example
        String a = "test";
        String b = "test";
        assertSame(a, b, "String literals are interned");
    }

    @Test
    void testAssertNotSame() {
        String str1 = new String("Hello");
        String str2 = new String("Hello");

        // Different objects, same value
        assertNotSame(str1, str2, "Should be different objects");
        assertEquals(str1, str2, "But values should be equal");
    }

    @Test
    void testObjectReferences() {
        User user1 = new User("john");
        User user2 = new User("john");
        User user3 = user1;

        assertNotSame(user1, user2, "Different User objects");
        assertSame(user1, user3, "Same User reference");
    }
}
```

### assertEquals vs assertSame

| Assertion | Checks | Example |
|-----------|--------|---------|
| `assertEquals` | Value equality (using `.equals()`) | "Hello".equals("Hello") |
| `assertSame` | Reference equality (using `==`) | obj1 == obj2 |

---

## Array Assertions

### assertArrayEquals

Compare arrays element by element.

```java
class ArrayAssertionsTest {

    @Test
    void testArrayEquals() {
        int[] expected = {1, 2, 3, 4, 5};
        int[] actual = {1, 2, 3, 4, 5};

        assertArrayEquals(expected, actual);
    }

    @Test
    void testArrayEqualsWithMessage() {
        String[] expected = {"apple", "banana", "cherry"};
        String[] actual = getFruits();

        assertArrayEquals(expected, actual, "Fruit arrays should match");
    }

    @Test
    void testFloatingPointArrays() {
        double[] expected = {1.0, 2.0, 3.0};
        double[] actual = {1.0001, 2.0001, 3.0001};

        // With delta for floating point comparison
        assertArrayEquals(expected, actual, 0.001,
            "Arrays should match within tolerance");
    }

    @Test
    void testMultidimensionalArrays() {
        int[][] expected = {
            {1, 2, 3},
            {4, 5, 6}
        };
        int[][] actual = getMatrix();

        assertArrayEquals(expected, actual, "Matrices should match");
    }

    private String[] getFruits() {
        return new String[]{"apple", "banana", "cherry"};
    }

    private int[][] getMatrix() {
        return new int[][]{{1, 2, 3}, {4, 5, 6}};
    }
}
```

---

## Iterable Assertions

### assertIterableEquals

Compare iterables (Lists, Sets, etc.) in order.

```java
class IterableAssertionsTest {

    @Test
    void testIterableEquals() {
        List<String> expected = Arrays.asList("apple", "banana", "cherry");
        List<String> actual = getFruitList();

        assertIterableEquals(expected, actual,
            "Lists should contain same elements in same order");
    }

    @Test
    void testSetComparison() {
        // Note: Sets don't guarantee order, but assertIterableEquals
        // compares in iteration order
        Set<String> expected = new LinkedHashSet<>(
            Arrays.asList("apple", "banana", "cherry")
        );
        Set<String> actual = new LinkedHashSet<>(
            Arrays.asList("apple", "banana", "cherry")
        );

        assertIterableEquals(expected, actual);
    }

    @Test
    void testCustomObjects() {
        List<User> expected = Arrays.asList(
            new User("john", "john@example.com"),
            new User("jane", "jane@example.com")
        );
        List<User> actual = getUserList();

        assertIterableEquals(expected, actual,
            "User lists should match");
    }

    private List<String> getFruitList() {
        return Arrays.asList("apple", "banana", "cherry");
    }

    private List<User> getUserList() {
        return Arrays.asList(
            new User("john", "john@example.com"),
            new User("jane", "jane@example.com")
        );
    }
}
```

---

## Exception Assertions

### assertThrows and assertDoesNotThrow

Verify that code throws (or doesn't throw) exceptions.

```java
class ExceptionAssertionsTest {

    @Test
    void testAssertThrows() {
        // Simple exception check
        assertThrows(IllegalArgumentException.class, () -> {
            divide(10, 0);
        });

        // Verify exception message
        Exception exception = assertThrows(IllegalArgumentException.class, () -> {
            validateAge(-5);
        });
        assertEquals("Age must be positive", exception.getMessage());
    }

    @Test
    void testAssertThrowsWithMessage() {
        IllegalArgumentException exception = assertThrows(
            IllegalArgumentException.class,
            () -> createUser(null, "email@example.com"),
            "Should throw exception for null username"
        );

        assertTrue(exception.getMessage().contains("Username"));
    }

    @Test
    void testAssertDoesNotThrow() {
        // Verify no exception is thrown
        assertDoesNotThrow(() -> {
            divide(10, 2);
        });

        // Can also capture and assert on the return value
        String result = assertDoesNotThrow(() -> {
            return processData("valid data");
        });
        assertEquals("processed", result);
    }

    @Test
    void testMultipleExceptionScenarios() {
        Calculator calc = new Calculator();

        // Division by zero
        assertThrows(ArithmeticException.class, () -> {
            calc.divide(10, 0);
        });

        // Null input
        assertThrows(NullPointerException.class, () -> {
            calc.process(null);
        });

        // Invalid state
        assertThrows(IllegalStateException.class, () -> {
            calc.getResult(); // Before any calculation
        });
    }

    private int divide(int a, int b) {
        if (b == 0) {
            throw new IllegalArgumentException("Cannot divide by zero");
        }
        return a / b;
    }

    private void validateAge(int age) {
        if (age < 0) {
            throw new IllegalArgumentException("Age must be positive");
        }
    }

    private void createUser(String username, String email) {
        if (username == null) {
            throw new IllegalArgumentException("Username cannot be null");
        }
    }

    private String processData(String data) {
        return "processed";
    }
}
```

---

## Timeout Assertions

### assertTimeout and assertTimeoutPreemptively

Verify that code completes within a specified time.

```java
import java.time.Duration;

class TimeoutAssertionsTest {

    @Test
    void testAssertTimeout() {
        // Code must complete within 2 seconds
        assertTimeout(Duration.ofSeconds(2), () -> {
            // Fast operation
            Thread.sleep(100);
        });

        // Can capture return value
        String result = assertTimeout(Duration.ofMillis(500), () -> {
            return "completed";
        });
        assertEquals("completed", result);
    }

    @Test
    void testAssertTimeoutWithMessage() {
        assertTimeout(
            Duration.ofSeconds(1),
            () -> performQuickOperation(),
            "Operation should complete within 1 second"
        );
    }

    @Test
    void testAssertTimeoutPreemptively() {
        // Aborts execution if timeout is exceeded
        // (assertTimeout lets it complete)
        assertTimeoutPreemptively(Duration.ofSeconds(2), () -> {
            Thread.sleep(100);
        });
    }

    @Test
    void demonstrateTimeoutDifference() {
        // assertTimeout - waits for completion even if timeout exceeded
        // Useful to see how long it actually took
        assertTimeout(Duration.ofMillis(100), () -> {
            Thread.sleep(200); // Will fail but wait for full 200ms
        });

        // assertTimeoutPreemptively - aborts at timeout
        // Useful to prevent long-running tests
        assertTimeoutPreemptively(Duration.ofMillis(100), () -> {
            Thread.sleep(200); // Will abort at 100ms
        });
    }

    private void performQuickOperation() {
        // Simulate quick operation
    }
}
```

---

## Grouped Assertions

### assertAll

Execute multiple assertions and report all failures (not just the first one).

```java
class GroupedAssertionsTest {

    @Test
    void testAssertAll() {
        User user = new User("john", "john@example.com", 25);

        // All assertions execute even if some fail
        assertAll("User properties",
            () -> assertEquals("john", user.getUsername()),
            () -> assertEquals("john@example.com", user.getEmail()),
            () -> assertEquals(25, user.getAge())
        );
    }

    @Test
    void testNestedAssertAll() {
        Address address = new Address("123 Main St", "Springfield", "IL", "62701");
        User user = new User("john", "john@example.com", 25, address);

        assertAll("User with address",
            () -> assertEquals("john", user.getUsername()),
            () -> assertEquals("john@example.com", user.getEmail()),
            () -> assertAll("Address",
                () -> assertEquals("123 Main St", address.getStreet()),
                () -> assertEquals("Springfield", address.getCity()),
                () -> assertEquals("IL", address.getState()),
                () -> assertEquals("62701", address.getZipCode())
            )
        );
    }

    @Test
    void testCollectionWithAssertAll() {
        List<Product> products = getProducts();

        assertAll("Product list validations",
            () -> assertNotNull(products, "Products should not be null"),
            () -> assertEquals(3, products.size(), "Should have 3 products"),
            () -> assertTrue(
                products.stream().anyMatch(p -> p.getName().equals("Laptop")),
                "Should contain Laptop"
            ),
            () -> assertTrue(
                products.stream().allMatch(p -> p.getPrice() > 0),
                "All products should have positive price"
            )
        );
    }

    @Test
    void demonstrateAssertAllBenefit() {
        Calculator calc = new Calculator();

        // Without assertAll - stops at first failure
        assertEquals(8, calc.add(5, 3));
        assertEquals(2, calc.subtract(5, 3));
        assertEquals(15, calc.multiply(5, 3));  // If this fails, tests below won't run
        assertEquals(2, calc.divide(10, 5));

        // With assertAll - all assertions run
        assertAll("Calculator operations",
            () -> assertEquals(8, calc.add(5, 3), "Addition"),
            () -> assertEquals(2, calc.subtract(5, 3), "Subtraction"),
            () -> assertEquals(15, calc.multiply(5, 3), "Multiplication"),
            () -> assertEquals(2, calc.divide(10, 5), "Division")
        );
    }

    private List<Product> getProducts() {
        return Arrays.asList(
            new Product("Laptop", 999.99),
            new Product("Mouse", 29.99),
            new Product("Keyboard", 79.99)
        );
    }
}
```

---

## Assertion Method Summary Table

### Basic Assertions

| Method | Purpose | Example |
|--------|---------|---------|
| `assertEquals(expected, actual)` | Value equality | `assertEquals(5, result)` |
| `assertNotEquals(unexpected, actual)` | Value inequality | `assertNotEquals(0, result)` |
| `assertTrue(condition)` | Boolean true | `assertTrue(isValid)` |
| `assertFalse(condition)` | Boolean false | `assertFalse(isEmpty)` |
| `assertNull(object)` | Null check | `assertNull(user)` |
| `assertNotNull(object)` | Not null check | `assertNotNull(user)` |

### Reference and Collection Assertions

| Method | Purpose | Example |
|--------|---------|---------|
| `assertSame(expected, actual)` | Reference equality | `assertSame(obj1, obj2)` |
| `assertNotSame(unexpected, actual)` | Reference inequality | `assertNotSame(obj1, obj2)` |
| `assertArrayEquals(expected, actual)` | Array equality | `assertArrayEquals(arr1, arr2)` |
| `assertIterableEquals(expected, actual)` | Iterable equality | `assertIterableEquals(list1, list2)` |

### Exception and Timeout Assertions

| Method | Purpose | Example |
|--------|---------|---------|
| `assertThrows(type, executable)` | Exception thrown | `assertThrows(NPE.class, ...)` |
| `assertDoesNotThrow(executable)` | No exception | `assertDoesNotThrow(...)` |
| `assertTimeout(duration, executable)` | Completes in time | `assertTimeout(Duration.ofSeconds(1), ...)` |
| `assertTimeoutPreemptively(duration, executable)` | Aborts if timeout | `assertTimeoutPreemptively(...)` |

### Grouped Assertions

| Method | Purpose | Example |
|--------|---------|---------|
| `assertAll(executables...)` | Multiple assertions | `assertAll(() -> ..., () -> ...)` |
| `assertAll(message, executables...)` | With heading | `assertAll("User", ...)` |

---

## Best Practices for Assertions

### 1. Use Descriptive Messages

```java
// Poor
assertEquals(100, total);

// Good
assertEquals(100, total, "Total price should be 100 for 2 items at 50 each");
```

### 2. Choose the Right Assertion

```java
// Less specific
assertTrue(list.size() == 0);

// More specific (better error messages)
assertTrue(list.isEmpty());

// Even better
assertEquals(0, list.size());
```

### 3. Use assertAll for Related Checks

```java
// Bad - stops at first failure
assertEquals("John", user.getName());
assertEquals("john@example.com", user.getEmail());
assertEquals(25, user.getAge());

// Good - sees all failures
assertAll("User validation",
    () -> assertEquals("John", user.getName()),
    () -> assertEquals("john@example.com", user.getEmail()),
    () -> assertEquals(25, user.getAge())
);
```

### 4. Be Precise with Floating Point Comparisons

```java
// Bad - may fail due to precision
assertEquals(0.3, 0.1 + 0.2);

// Good - use delta
assertEquals(0.3, 0.1 + 0.2, 0.0001);
```

### 5. Test Exception Messages

```java
// Good - verify exception is thrown
assertThrows(IllegalArgumentException.class, () -> {
    validator.validate(null);
});

// Better - also verify message
Exception ex = assertThrows(IllegalArgumentException.class, () -> {
    validator.validate(null);
});
assertEquals("Input cannot be null", ex.getMessage());
```

---

## Common Pitfalls

### 1. Wrong Order of Parameters

```java
// Wrong - actual, expected (backward!)
assertEquals(result, 100);

// Correct - expected, actual
assertEquals(100, result);
```

### 2. Comparing Collections Without Order

```java
// May fail even if elements are same but in different order
List<String> list1 = Arrays.asList("a", "b", "c");
List<String> list2 = Arrays.asList("c", "b", "a");

assertIterableEquals(list1, list2); // Fails!

// Better for unordered comparison
assertEquals(new HashSet<>(list1), new HashSet<>(list2));
```

### 3. Not Using Delta for Floating Point

```java
// Bad - unreliable
assertEquals(10.0, calculateValue());

// Good - reliable
assertEquals(10.0, calculateValue(), 0.001);
```

---

## Summary

| Assertion Type | Use Case | Key Points |
|----------------|----------|------------|
| **Basic** | Value comparisons | assertEquals, assertTrue, assertNull |
| **Reference** | Object identity | assertSame vs assertEquals |
| **Collections** | Arrays, Lists, Sets | assertArrayEquals, assertIterableEquals |
| **Exceptions** | Error handling | assertThrows, verify messages |
| **Timeout** | Performance | assertTimeout for time-sensitive code |
| **Grouped** | Multiple checks | assertAll to see all failures |

## Next Topic

Continue to [Unit Testing Stubs](./06-unit-testing-stubs.md) to learn about creating test doubles for dependencies.
