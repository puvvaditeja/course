# Unit Testing Introduction To JUnit

## What is Unit Testing?

Unit testing is a software testing methodology where individual units or components of a software application are tested in isolation from the rest of the application. A "unit" is the smallest testable part of an application, typically a method or function.

### Characteristics of Unit Tests

- **Isolated**: Tests run independently without external dependencies
- **Fast**: Execute quickly (milliseconds per test)
- **Repeatable**: Produce same results every time
- **Self-Validating**: Clearly pass or fail without manual inspection
- **Timely**: Written close to when the code is written

### Testing Pyramid

```
        /\
       /  \
      /    \
     / UI   \      ← Few, slow, expensive
    /________\
   /          \
  / Integration\   ← Moderate number
 /______________\
/                \
/   Unit Tests    \ ← Many, fast, cheap
/__________________\
```

Unit tests form the foundation of a healthy test suite.

---

## Unit vs Integration vs System Testing

| Aspect | Unit Testing | Integration Testing | System Testing |
|--------|--------------|---------------------|----------------|
| Scope | Single method/class | Multiple components | Entire application |
| Dependencies | Mocked/Stubbed | Real or partial | Real |
| Speed | Very fast (ms) | Moderate (seconds) | Slow (minutes) |
| Isolation | Complete | Partial | None |
| Who writes | Developers | Developers/QA | QA Team |
| When | During development | After unit tests | Before release |

---

## Writing Effective Unit Tests

### The Anatomy of a Good Unit Test

```java
@Test
@DisplayName("Should calculate discount correctly for premium customers")
void testPremiumCustomerDiscount() {
    // 1. ARRANGE - Set up test data and dependencies
    Customer customer = new Customer("John", CustomerType.PREMIUM);
    Order order = new Order(100.0);
    DiscountCalculator calculator = new DiscountCalculator();

    // 2. ACT - Execute the method being tested
    double discount = calculator.calculateDiscount(customer, order);

    // 3. ASSERT - Verify the result
    assertEquals(20.0, discount, "Premium customers should get 20% discount");
}
```

### Test Structure: AAA Pattern

1. **Arrange**: Set up test data, create objects, configure mocks
2. **Act**: Execute the method or behavior being tested
3. **Assert**: Verify the outcome matches expectations

---

## JUnit Test Examples

### Example 1: Testing a Service Class

```java
public class UserService {

    private UserRepository userRepository;

    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    public User createUser(String username, String email) {
        if (username == null || username.trim().isEmpty()) {
            throw new IllegalArgumentException("Username cannot be empty");
        }

        if (email == null || !email.contains("@")) {
            throw new IllegalArgumentException("Invalid email address");
        }

        User user = new User(username, email);
        return userRepository.save(user);
    }

    public boolean isValidUsername(String username) {
        return username != null
            && username.length() >= 3
            && username.length() <= 20
            && username.matches("[a-zA-Z0-9_]+");
    }
}
```

### Test Class for UserService

```java
import org.junit.jupiter.api.*;
import static org.junit.jupiter.api.Assertions.*;

class UserServiceTest {

    private UserService userService;
    private UserRepository mockRepository;

    @BeforeEach
    void setUp() {
        // For now, we'll create a simple stub (Mockito covered later)
        mockRepository = new UserRepositoryStub();
        userService = new UserService(mockRepository);
    }

    @Nested
    @DisplayName("User Creation Tests")
    class UserCreationTests {

        @Test
        @DisplayName("Should create user with valid username and email")
        void shouldCreateValidUser() {
            // Arrange
            String username = "john_doe";
            String email = "john@example.com";

            // Act
            User result = userService.createUser(username, email);

            // Assert
            assertNotNull(result);
            assertEquals(username, result.getUsername());
            assertEquals(email, result.getEmail());
        }

        @Test
        @DisplayName("Should throw exception for null username")
        void shouldRejectNullUsername() {
            // Arrange
            String username = null;
            String email = "john@example.com";

            // Act & Assert
            IllegalArgumentException exception = assertThrows(
                IllegalArgumentException.class,
                () -> userService.createUser(username, email)
            );

            assertEquals("Username cannot be empty", exception.getMessage());
        }

        @Test
        @DisplayName("Should throw exception for empty username")
        void shouldRejectEmptyUsername() {
            // Arrange
            String username = "   ";
            String email = "john@example.com";

            // Act & Assert
            assertThrows(
                IllegalArgumentException.class,
                () -> userService.createUser(username, email),
                "Should reject whitespace-only username"
            );
        }

        @Test
        @DisplayName("Should throw exception for invalid email")
        void shouldRejectInvalidEmail() {
            // Arrange
            String username = "john_doe";
            String email = "invalid-email";

            // Act & Assert
            IllegalArgumentException exception = assertThrows(
                IllegalArgumentException.class,
                () -> userService.createUser(username, email)
            );

            assertTrue(exception.getMessage().contains("Invalid email"));
        }
    }

    @Nested
    @DisplayName("Username Validation Tests")
    class UsernameValidationTests {

        @Test
        @DisplayName("Should accept valid username")
        void shouldAcceptValidUsername() {
            assertTrue(userService.isValidUsername("john_doe"));
            assertTrue(userService.isValidUsername("user123"));
            assertTrue(userService.isValidUsername("test_user_99"));
        }

        @Test
        @DisplayName("Should reject username that is too short")
        void shouldRejectShortUsername() {
            assertFalse(userService.isValidUsername("ab"));
        }

        @Test
        @DisplayName("Should reject username that is too long")
        void shouldRejectLongUsername() {
            assertFalse(userService.isValidUsername("a".repeat(21)));
        }

        @Test
        @DisplayName("Should reject username with special characters")
        void shouldRejectSpecialCharacters() {
            assertFalse(userService.isValidUsername("user@name"));
            assertFalse(userService.isValidUsername("user-name"));
            assertFalse(userService.isValidUsername("user.name"));
        }

        @Test
        @DisplayName("Should reject null username")
        void shouldRejectNullUsername() {
            assertFalse(userService.isValidUsername(null));
        }
    }

    // Simple stub for demonstration
    private static class UserRepositoryStub implements UserRepository {
        @Override
        public User save(User user) {
            user.setId(1L);
            return user;
        }
    }
}
```

---

## Testing Different Scenarios

### Example 2: Testing Collections

```java
public class ShoppingCart {

    private List<CartItem> items = new ArrayList<>();

    public void addItem(CartItem item) {
        if (item == null) {
            throw new IllegalArgumentException("Item cannot be null");
        }
        items.add(item);
    }

    public void removeItem(CartItem item) {
        items.remove(item);
    }

    public double getTotalPrice() {
        return items.stream()
                   .mapToDouble(CartItem::getPrice)
                   .sum();
    }

    public int getItemCount() {
        return items.size();
    }

    public boolean isEmpty() {
        return items.isEmpty();
    }

    public void clear() {
        items.clear();
    }
}
```

### Test Class for ShoppingCart

```java
import org.junit.jupiter.api.*;
import static org.junit.jupiter.api.Assertions.*;

class ShoppingCartTest {

    private ShoppingCart cart;
    private CartItem item1;
    private CartItem item2;

    @BeforeEach
    void setUp() {
        cart = new ShoppingCart();
        item1 = new CartItem("Book", 29.99);
        item2 = new CartItem("Pen", 4.99);
    }

    @Test
    @DisplayName("New cart should be empty")
    void newCartShouldBeEmpty() {
        assertTrue(cart.isEmpty());
        assertEquals(0, cart.getItemCount());
    }

    @Test
    @DisplayName("Should add item to cart")
    void shouldAddItemToCart() {
        // Act
        cart.addItem(item1);

        // Assert
        assertFalse(cart.isEmpty());
        assertEquals(1, cart.getItemCount());
    }

    @Test
    @DisplayName("Should add multiple items to cart")
    void shouldAddMultipleItems() {
        // Act
        cart.addItem(item1);
        cart.addItem(item2);

        // Assert
        assertEquals(2, cart.getItemCount());
        assertEquals(34.98, cart.getTotalPrice(), 0.01);
    }

    @Test
    @DisplayName("Should remove item from cart")
    void shouldRemoveItemFromCart() {
        // Arrange
        cart.addItem(item1);
        cart.addItem(item2);

        // Act
        cart.removeItem(item1);

        // Assert
        assertEquals(1, cart.getItemCount());
        assertEquals(4.99, cart.getTotalPrice(), 0.01);
    }

    @Test
    @DisplayName("Should clear all items from cart")
    void shouldClearCart() {
        // Arrange
        cart.addItem(item1);
        cart.addItem(item2);

        // Act
        cart.clear();

        // Assert
        assertTrue(cart.isEmpty());
        assertEquals(0, cart.getTotalPrice());
    }

    @Test
    @DisplayName("Should throw exception when adding null item")
    void shouldRejectNullItem() {
        assertThrows(IllegalArgumentException.class, () -> {
            cart.addItem(null);
        });
    }

    @Test
    @DisplayName("Should calculate correct total for empty cart")
    void emptyCartShouldHaveZeroTotal() {
        assertEquals(0.0, cart.getTotalPrice());
    }
}
```

---

## Nested Tests for Better Organization

Use `@Nested` to group related tests:

```java
class CalculatorTest {

    private Calculator calculator;

    @BeforeEach
    void setUp() {
        calculator = new Calculator();
    }

    @Nested
    @DisplayName("Addition Tests")
    class AdditionTests {

        @Test
        @DisplayName("Adding positive numbers")
        void testPositiveAddition() {
            assertEquals(8, calculator.add(5, 3));
        }

        @Test
        @DisplayName("Adding negative numbers")
        void testNegativeAddition() {
            assertEquals(-8, calculator.add(-5, -3));
        }

        @Test
        @DisplayName("Adding positive and negative")
        void testMixedAddition() {
            assertEquals(2, calculator.add(5, -3));
        }
    }

    @Nested
    @DisplayName("Division Tests")
    class DivisionTests {

        @Test
        @DisplayName("Dividing positive numbers")
        void testPositiveDivision() {
            assertEquals(2.5, calculator.divide(5, 2), 0.01);
        }

        @Test
        @DisplayName("Division by zero throws exception")
        void testDivisionByZero() {
            assertThrows(ArithmeticException.class, () -> {
                calculator.divide(5, 0);
            });
        }
    }
}
```

---

## Parameterized Tests

Test the same logic with different inputs:

```java
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.*;

class StringUtilsTest {

    @ParameterizedTest
    @ValueSource(strings = {"racecar", "radar", "level", "noon"})
    @DisplayName("Should identify palindromes")
    void testPalindromes(String word) {
        assertTrue(StringUtils.isPalindrome(word));
    }

    @ParameterizedTest
    @ValueSource(strings = {"hello", "world", "java", "test"})
    @DisplayName("Should identify non-palindromes")
    void testNonPalindromes(String word) {
        assertFalse(StringUtils.isPalindrome(word));
    }

    @ParameterizedTest
    @CsvSource({
        "5, 3, 8",
        "10, -2, 8",
        "0, 0, 0",
        "-5, -3, -8"
    })
    @DisplayName("Should add numbers correctly")
    void testAddition(int a, int b, int expected) {
        assertEquals(expected, calculator.add(a, b));
    }

    @ParameterizedTest
    @MethodSource("provideTestData")
    @DisplayName("Should validate email addresses")
    void testEmailValidation(String email, boolean expected) {
        assertEquals(expected, EmailValidator.isValid(email));
    }

    static Stream<Arguments> provideTestData() {
        return Stream.of(
            Arguments.of("user@example.com", true),
            Arguments.of("invalid.email", false),
            Arguments.of("user@domain.co.uk", true),
            Arguments.of("@example.com", false)
        );
    }
}
```

---

## Test Lifecycle Hooks

Understanding when lifecycle methods execute:

```java
class TestLifecycleDemo {

    @BeforeAll
    static void initAll() {
        System.out.println("Before all tests - runs once");
    }

    @BeforeEach
    void init() {
        System.out.println("  Before each test");
    }

    @Test
    void test1() {
        System.out.println("    Test 1");
    }

    @Test
    void test2() {
        System.out.println("    Test 2");
    }

    @AfterEach
    void tearDown() {
        System.out.println("  After each test");
    }

    @AfterAll
    static void tearDownAll() {
        System.out.println("After all tests - runs once");
    }
}
```

**Output:**
```
Before all tests - runs once
  Before each test
    Test 1
  After each test
  Before each test
    Test 2
  After each test
After all tests - runs once
```

---

## Best Practices for Unit Testing

### 1. Test One Thing at a Time

```java
// Bad - testing multiple behaviors
@Test
void testUserOperations() {
    User user = new User("john", "john@example.com");
    assertTrue(user.isValid());
    user.setEmail("invalid");
    assertFalse(user.isValid());
    user.activate();
    assertTrue(user.isActive());
}

// Good - separate tests
@Test
void shouldValidateCorrectUser() {
    User user = new User("john", "john@example.com");
    assertTrue(user.isValid());
}

@Test
void shouldInvalidateUserWithBadEmail() {
    User user = new User("john", "invalid");
    assertFalse(user.isValid());
}

@Test
void shouldActivateUser() {
    User user = new User("john", "john@example.com");
    user.activate();
    assertTrue(user.isActive());
}
```

### 2. Use Descriptive Names

```java
// Bad
@Test
void test1() { }

// Good
@Test
@DisplayName("Should calculate shipping cost for international orders")
void shouldCalculateInternationalShippingCost() { }
```

### 3. Don't Test Framework Code

```java
// Bad - testing Java's ArrayList
@Test
void testArrayListAdd() {
    List<String> list = new ArrayList<>();
    list.add("item");
    assertEquals(1, list.size());
}

// Good - test your business logic
@Test
void shouldAddItemToInventory() {
    Inventory inventory = new Inventory();
    inventory.addProduct(new Product("Laptop"));
    assertEquals(1, inventory.getProductCount());
}
```

### 4. Keep Tests Fast

- Avoid Thread.sleep()
- Don't access databases or networks
- Use test doubles for external dependencies
- Run tests in parallel when possible

### 5. Make Tests Independent

```java
// Bad - tests share state
private static int counter = 0;

@Test
void testA() {
    counter++;
    assertEquals(1, counter);
}

@Test
void testB() {
    counter++;
    assertEquals(2, counter); // Fails if testA doesn't run first
}

// Good - each test has own state
@Test
void testA() {
    int counter = 0;
    counter++;
    assertEquals(1, counter);
}

@Test
void testB() {
    int counter = 0;
    counter++;
    assertEquals(1, counter);
}
```

---

## Common Pitfalls to Avoid

### 1. Testing Implementation Details

```java
// Bad - testing private methods or internal state
@Test
void testPrivateMethod() {
    // Using reflection to test private method
    Method method = MyClass.class.getDeclaredMethod("privateHelper");
    method.setAccessible(true);
    // ...
}

// Good - test public API
@Test
void testPublicBehavior() {
    MyClass instance = new MyClass();
    String result = instance.publicMethod();
    assertEquals("expected", result);
}
```

### 2. Over-Relying on setUp

```java
// Bad - too much in setUp
@BeforeEach
void setUp() {
    // Creating lots of objects that not all tests need
}

// Good - create only what each test needs
@Test
void testSpecificScenario() {
    // Create only what this test needs
    User user = new User("test");
    // ...
}
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| Unit Testing | Testing individual components in isolation |
| Test Structure | Arrange-Act-Assert (AAA) pattern |
| JUnit Features | @Test, @BeforeEach, @AfterEach, nested tests |
| Parameterized | Test same logic with different inputs |
| Best Practices | Fast, independent, focused, descriptive tests |

## Next Topic

Continue to [Unit Testing Line Vs Branch Coverage](./03-unit-testing-line-vs-branch-coverage.md) to learn about measuring test effectiveness.
