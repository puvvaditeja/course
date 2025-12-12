# Unit Testing Arrange Act Assert

## What is the AAA Pattern?

The Arrange-Act-Assert (AAA) pattern is a common structure for organizing unit tests. It divides tests into three distinct sections, making them easier to read, understand, and maintain.

### The Three Phases

```
┌──────────────────────────────────┐
│  ARRANGE                         │
│  Set up test data and            │
│  configure dependencies          │
├──────────────────────────────────┤
│  ACT                             │
│  Execute the method or behavior  │
│  being tested                    │
├──────────────────────────────────┤
│  ASSERT                          │
│  Verify the outcome matches      │
│  expectations                    │
└──────────────────────────────────┘
```

### Benefits of AAA Pattern

- **Readability**: Clear structure makes tests easy to understand
- **Maintainability**: Changes are easier to locate and implement
- **Consistency**: Standardized approach across test suite
- **Documentation**: Self-documenting test structure
- **Debugging**: Easy to identify which phase is failing

---

## The Arrange Phase

The Arrange phase prepares everything needed for the test. This includes:
- Creating objects and test data
- Setting up method parameters
- Configuring mocks and stubs
- Establishing preconditions

### Example: Arrange Phase

```java
@Test
void testCalculateTotalPrice() {
    // ARRANGE
    ShoppingCart cart = new ShoppingCart();
    Product product1 = new Product("Laptop", 999.99);
    Product product2 = new Product("Mouse", 29.99);
    cart.addProduct(product1);
    cart.addProduct(product2);

    // Act and Assert follow...
}
```

### Arrange Best Practices

1. **Create Fresh Objects**
```java
// Good - fresh instance for each test
@Test
void testAddProduct() {
    ShoppingCart cart = new ShoppingCart();  // Fresh instance
    Product product = new Product("Item", 10.0);
    // ...
}
```

2. **Use Object Mothers or Builders**
```java
// Object Mother pattern
public class ProductMother {
    public static Product createDefaultProduct() {
        return new Product("Default Product", 10.0);
    }

    public static Product createExpensiveProduct() {
        return new Product("Expensive Product", 1000.0);
    }
}

@Test
void testExpensiveProductDiscount() {
    // ARRANGE
    Product product = ProductMother.createExpensiveProduct();
    DiscountCalculator calculator = new DiscountCalculator();
    // ...
}
```

3. **Builder Pattern for Complex Objects**
```java
public class OrderBuilder {
    private String customerId = "default";
    private List<Item> items = new ArrayList<>();
    private double totalPrice = 0.0;

    public OrderBuilder withCustomerId(String customerId) {
        this.customerId = customerId;
        return this;
    }

    public OrderBuilder withItem(Item item) {
        this.items.add(item);
        return this;
    }

    public Order build() {
        return new Order(customerId, items, totalPrice);
    }
}

@Test
void testOrderWithMultipleItems() {
    // ARRANGE
    Order order = new OrderBuilder()
        .withCustomerId("CUST123")
        .withItem(new Item("Product A", 50.0))
        .withItem(new Item("Product B", 30.0))
        .build();
    // ...
}
```

---

## The Act Phase

The Act phase executes the specific behavior being tested. This should typically be a single method call.

### Example: Act Phase

```java
@Test
void testCalculateTotalPrice() {
    // ARRANGE
    ShoppingCart cart = new ShoppingCart();
    cart.addProduct(new Product("Laptop", 999.99));
    cart.addProduct(new Product("Mouse", 29.99));

    // ACT
    double totalPrice = cart.calculateTotalPrice();

    // Assert follows...
}
```

### Act Best Practices

1. **Single Method Call**
```java
// Good - single action
@Test
void testAddProduct() {
    // ARRANGE
    ShoppingCart cart = new ShoppingCart();
    Product product = new Product("Item", 10.0);

    // ACT
    cart.addProduct(product);

    // ASSERT
    assertEquals(1, cart.getProductCount());
}

// Bad - multiple actions
@Test
void testMultipleOperations() {
    ShoppingCart cart = new ShoppingCart();
    cart.addProduct(new Product("Item1", 10.0));
    cart.addProduct(new Product("Item2", 20.0));  // Too many actions
    cart.removeProduct("Item1");                   // Should be separate tests
    // ...
}
```

2. **Store Result for Assertion**
```java
// Good - store result
@Test
void testCalculateDiscount() {
    // ARRANGE
    DiscountCalculator calculator = new DiscountCalculator();

    // ACT
    double discount = calculator.calculate(100.0, "PREMIUM");

    // ASSERT
    assertEquals(20.0, discount, 0.01);
}

// Acceptable for void methods
@Test
void testAddProduct() {
    // ARRANGE
    ShoppingCart cart = new ShoppingCart();
    Product product = new Product("Item", 10.0);

    // ACT
    cart.addProduct(product);

    // ASSERT - check side effects
    assertEquals(1, cart.getProductCount());
}
```

---

## The Assert Phase

The Assert phase verifies that the outcome of the Act phase matches expectations.

### Example: Assert Phase

```java
@Test
void testCalculateTotalPrice() {
    // ARRANGE
    ShoppingCart cart = new ShoppingCart();
    cart.addProduct(new Product("Laptop", 999.99));
    cart.addProduct(new Product("Mouse", 29.99));

    // ACT
    double totalPrice = cart.calculateTotalPrice();

    // ASSERT
    assertEquals(1029.98, totalPrice, 0.01);
}
```

### Assert Best Practices

1. **Use Descriptive Messages**
```java
// Good - descriptive failure message
@Test
void testPremiumDiscount() {
    // ARRANGE
    DiscountCalculator calculator = new DiscountCalculator();

    // ACT
    double discount = calculator.calculate(100.0, "PREMIUM");

    // ASSERT
    assertEquals(20.0, discount, 0.01,
        "Premium customers should receive 20% discount");
}
```

2. **Single Logical Assertion**
```java
// Good - single concept
@Test
void testUserCreation() {
    // ARRANGE & ACT
    User user = new User("john", "john@example.com");

    // ASSERT - verifying one concept (user validity)
    assertAll("User should be valid",
        () -> assertNotNull(user.getId()),
        () -> assertEquals("john", user.getUsername()),
        () -> assertEquals("john@example.com", user.getEmail())
    );
}

// Bad - unrelated assertions
@Test
void testEverything() {
    User user = new User("john", "john@example.com");
    assertEquals("john", user.getUsername());

    ShoppingCart cart = new ShoppingCart();  // Different concept!
    assertEquals(0, cart.getProductCount());  // Should be separate test
}
```

3. **Use Appropriate Assertions**
```java
// Good - specific assertions
@Test
void testExceptionHandling() {
    // ARRANGE
    Calculator calculator = new Calculator();

    // ACT & ASSERT
    Exception exception = assertThrows(IllegalArgumentException.class, () -> {
        calculator.divide(10, 0);
    });

    assertEquals("Cannot divide by zero", exception.getMessage());
}

// Good - collection assertions
@Test
void testProductList() {
    // ARRANGE & ACT
    List<Product> products = productService.findByCategory("Electronics");

    // ASSERT
    assertAll("Product list assertions",
        () -> assertNotNull(products),
        () -> assertFalse(products.isEmpty()),
        () -> assertTrue(products.size() >= 5),
        () -> assertTrue(products.stream()
            .allMatch(p -> p.getCategory().equals("Electronics")))
    );
}
```

---

## Complete AAA Examples

### Example 1: Testing a Service Layer

```java
public class OrderService {
    private OrderRepository orderRepository;
    private InventoryService inventoryService;
    private PaymentService paymentService;

    public OrderService(OrderRepository orderRepository,
                       InventoryService inventoryService,
                       PaymentService paymentService) {
        this.orderRepository = orderRepository;
        this.inventoryService = inventoryService;
        this.paymentService = paymentService;
    }

    public Order placeOrder(String customerId, List<OrderItem> items) {
        // Validate inventory
        for (OrderItem item : items) {
            if (!inventoryService.isAvailable(item.getProductId(), item.getQuantity())) {
                throw new OutOfStockException("Product not available: " + item.getProductId());
            }
        }

        // Calculate total
        double total = items.stream()
            .mapToDouble(item -> item.getPrice() * item.getQuantity())
            .sum();

        // Create order
        Order order = new Order(customerId, items, total);

        // Process payment
        paymentService.processPayment(customerId, total);

        // Reserve inventory
        for (OrderItem item : items) {
            inventoryService.reserve(item.getProductId(), item.getQuantity());
        }

        // Save order
        return orderRepository.save(order);
    }
}
```

### Test Class with AAA Pattern

```java
import org.junit.jupiter.api.*;
import org.mockito.*;
import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

class OrderServiceTest {

    @Mock
    private OrderRepository orderRepository;

    @Mock
    private InventoryService inventoryService;

    @Mock
    private PaymentService paymentService;

    @InjectMocks
    private OrderService orderService;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    @DisplayName("Should successfully place order when items are in stock")
    void testPlaceOrder_Success() {
        // ==================== ARRANGE ====================
        // Test data
        String customerId = "CUST123";
        List<OrderItem> items = Arrays.asList(
            new OrderItem("PROD1", 2, 50.0),
            new OrderItem("PROD2", 1, 30.0)
        );

        // Mock behavior
        when(inventoryService.isAvailable("PROD1", 2)).thenReturn(true);
        when(inventoryService.isAvailable("PROD2", 1)).thenReturn(true);

        Order savedOrder = new Order(customerId, items, 130.0);
        savedOrder.setId("ORD123");
        when(orderRepository.save(any(Order.class))).thenReturn(savedOrder);

        // ==================== ACT ====================
        Order result = orderService.placeOrder(customerId, items);

        // ==================== ASSERT ====================
        assertAll("Order placement assertions",
            () -> assertNotNull(result, "Order should not be null"),
            () -> assertEquals("ORD123", result.getId(), "Order ID should match"),
            () -> assertEquals(customerId, result.getCustomerId(), "Customer ID should match"),
            () -> assertEquals(130.0, result.getTotal(), 0.01, "Total should be correct")
        );

        // Verify interactions
        verify(inventoryService).isAvailable("PROD1", 2);
        verify(inventoryService).isAvailable("PROD2", 1);
        verify(paymentService).processPayment(customerId, 130.0);
        verify(inventoryService).reserve("PROD1", 2);
        verify(inventoryService).reserve("PROD2", 1);
        verify(orderRepository).save(any(Order.class));
    }

    @Test
    @DisplayName("Should throw exception when product is out of stock")
    void testPlaceOrder_OutOfStock() {
        // ==================== ARRANGE ====================
        String customerId = "CUST123";
        List<OrderItem> items = Arrays.asList(
            new OrderItem("PROD1", 2, 50.0)
        );

        // Mock inventory as unavailable
        when(inventoryService.isAvailable("PROD1", 2)).thenReturn(false);

        // ==================== ACT & ASSERT ====================
        OutOfStockException exception = assertThrows(
            OutOfStockException.class,
            () -> orderService.placeOrder(customerId, items),
            "Should throw OutOfStockException"
        );

        assertTrue(exception.getMessage().contains("PROD1"));

        // Verify payment was not processed
        verify(paymentService, never()).processPayment(anyString(), anyDouble());
        verify(orderRepository, never()).save(any(Order.class));
    }
}
```

### Example 2: Testing Data Validation

```java
public class UserValidator {

    public ValidationResult validate(User user) {
        ValidationResult result = new ValidationResult();

        if (user.getUsername() == null || user.getUsername().trim().isEmpty()) {
            result.addError("username", "Username is required");
        } else if (user.getUsername().length() < 3) {
            result.addError("username", "Username must be at least 3 characters");
        }

        if (user.getEmail() == null || !user.getEmail().contains("@")) {
            result.addError("email", "Invalid email address");
        }

        if (user.getAge() < 18) {
            result.addError("age", "User must be at least 18 years old");
        }

        return result;
    }
}
```

### Test Class

```java
class UserValidatorTest {

    private UserValidator validator;

    @BeforeEach
    void setUp() {
        validator = new UserValidator();
    }

    @Test
    @DisplayName("Should pass validation for valid user")
    void testValidUser() {
        // ==================== ARRANGE ====================
        User user = new User();
        user.setUsername("john_doe");
        user.setEmail("john@example.com");
        user.setAge(25);

        // ==================== ACT ====================
        ValidationResult result = validator.validate(user);

        // ==================== ASSERT ====================
        assertTrue(result.isValid(), "Valid user should pass validation");
        assertTrue(result.getErrors().isEmpty(), "Should have no errors");
    }

    @Test
    @DisplayName("Should fail validation for user with short username")
    void testShortUsername() {
        // ==================== ARRANGE ====================
        User user = new User();
        user.setUsername("ab");  // Too short
        user.setEmail("john@example.com");
        user.setAge(25);

        // ==================== ACT ====================
        ValidationResult result = validator.validate(user);

        // ==================== ASSERT ====================
        assertAll("Validation failure assertions",
            () -> assertFalse(result.isValid(), "Should fail validation"),
            () -> assertTrue(result.hasError("username"), "Should have username error"),
            () -> assertEquals("Username must be at least 3 characters",
                result.getError("username"))
        );
    }

    @Test
    @DisplayName("Should fail validation for multiple errors")
    void testMultipleErrors() {
        // ==================== ARRANGE ====================
        User user = new User();
        user.setUsername("ab");           // Too short
        user.setEmail("invalid-email");   // No @
        user.setAge(16);                  // Too young

        // ==================== ACT ====================
        ValidationResult result = validator.validate(user);

        // ==================== ASSERT ====================
        assertAll("Multiple validation errors",
            () -> assertFalse(result.isValid()),
            () -> assertEquals(3, result.getErrors().size(), "Should have 3 errors"),
            () -> assertTrue(result.hasError("username")),
            () -> assertTrue(result.hasError("email")),
            () -> assertTrue(result.hasError("age"))
        );
    }
}
```

---

## AAA with Different Testing Scenarios

### Testing Exception Throwing

```java
@Test
@DisplayName("Should throw exception for negative quantity")
void testNegativeQuantity() {
    // ==================== ARRANGE ====================
    InventoryService service = new InventoryService();
    String productId = "PROD123";
    int negativeQuantity = -5;

    // ==================== ACT & ASSERT ====================
    IllegalArgumentException exception = assertThrows(
        IllegalArgumentException.class,
        () -> service.addStock(productId, negativeQuantity)
    );

    // Additional assertions on exception
    assertEquals("Quantity must be positive", exception.getMessage());
}
```

### Testing Void Methods

```java
@Test
@DisplayName("Should update user profile successfully")
void testUpdateProfile() {
    // ==================== ARRANGE ====================
    UserService service = new UserService();
    User user = new User("john", "john@example.com");
    String newEmail = "newemail@example.com";

    // ==================== ACT ====================
    service.updateEmail(user, newEmail);

    // ==================== ASSERT ====================
    // Verify state changes
    assertEquals(newEmail, user.getEmail());
}
```

### Testing Collections

```java
@Test
@DisplayName("Should filter products by price range")
void testFilterByPriceRange() {
    // ==================== ARRANGE ====================
    ProductService service = new ProductService();
    List<Product> products = Arrays.asList(
        new Product("Product A", 50.0),
        new Product("Product B", 150.0),
        new Product("Product C", 250.0),
        new Product("Product D", 75.0)
    );
    service.addProducts(products);

    // ==================== ACT ====================
    List<Product> result = service.findByPriceRange(50.0, 100.0);

    // ==================== ASSERT ====================
    assertAll("Filtered products",
        () -> assertEquals(2, result.size(), "Should return 2 products"),
        () -> assertTrue(result.stream()
            .allMatch(p -> p.getPrice() >= 50.0 && p.getPrice() <= 100.0),
            "All products should be in price range"),
        () -> assertTrue(result.stream()
            .anyMatch(p -> p.getName().equals("Product A")),
            "Should include Product A"),
        () -> assertTrue(result.stream()
            .anyMatch(p -> p.getName().equals("Product D")),
            "Should include Product D")
    );
}
```

---

## Visual Separation of AAA Sections

### Using Comments

```java
@Test
void testExample() {
    // ARRANGE
    Object obj = new Object();

    // ACT
    obj.doSomething();

    // ASSERT
    assertEquals(expected, actual);
}
```

### Using Blank Lines

```java
@Test
void testExample() {
    Object obj = new Object();

    obj.doSomething();

    assertEquals(expected, actual);
}
```

### Using Helper Methods

```java
@Test
void testComplexScenario() {
    // ARRANGE
    ComplexObject obj = arrangeComplexObject();

    // ACT
    Result result = obj.performOperation();

    // ASSERT
    assertComplexResult(result);
}

private ComplexObject arrangeComplexObject() {
    // Complex setup logic
    return new ComplexObject(...);
}

private void assertComplexResult(Result result) {
    // Complex assertions
    assertAll(...);
}
```

---

## Common Pitfalls and Solutions

### Pitfall 1: Act Phase Too Large

```java
// Bad - too much in Act phase
@Test
void testBad() {
    User user = new User();

    // ACT - too many operations
    user.setName("John");
    user.setEmail("john@example.com");
    user.activate();
    user.addRole("ADMIN");

    assertTrue(user.isActive());
}

// Good - separate tests
@Test
void testActivation() {
    // ARRANGE
    User user = new User("John", "john@example.com");

    // ACT
    user.activate();

    // ASSERT
    assertTrue(user.isActive());
}
```

### Pitfall 2: Arrange Phase in Wrong Place

```java
// Bad - setup in wrong phase
@Test
void testBad() {
    Calculator calc = new Calculator();

    int result = calc.add(5, 3);

    int expected = 8;  // Should be in Arrange!
    assertEquals(expected, result);
}

// Good - all setup in Arrange
@Test
void testGood() {
    // ARRANGE
    Calculator calc = new Calculator();
    int expected = 8;

    // ACT
    int result = calc.add(5, 3);

    // ASSERT
    assertEquals(expected, result);
}
```

---

## Summary

| Phase | Purpose | Best Practices |
|-------|---------|----------------|
| **Arrange** | Set up test data and dependencies | Use builders, object mothers, fresh instances |
| **Act** | Execute the behavior being tested | Single method call, store result |
| **Assert** | Verify the outcome | Descriptive messages, appropriate assertions |

### Key Takeaways

1. AAA makes tests readable and maintainable
2. Each phase has a specific, distinct purpose
3. Visual separation improves clarity
4. Keep Act phase focused on single operation
5. Use assertAll for related assertions

## Next Topic

Continue to [Unit Testing Assertion Types](./05-unit-testing-assertion-types.md) to learn about different types of assertions available in JUnit.
