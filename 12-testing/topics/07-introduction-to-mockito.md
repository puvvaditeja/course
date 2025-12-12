# Introduction to Mockito

## What is Mockito?

Mockito is a popular mocking framework for Java that allows you to create and configure mock objects. It simplifies the creation of test doubles and provides a clean API for defining mock behavior and verifying interactions.

### Why Use Mockito?

- **No Manual Stubs**: Automatically create mock objects without writing stub classes
- **Flexible**: Configure behavior on the fly
- **Verification**: Verify that methods were called with expected parameters
- **Clean Syntax**: Readable and maintainable test code
- **Industry Standard**: Most widely used mocking framework for Java

### Mockito vs Manual Stubs

| Aspect | Manual Stub | Mockito Mock |
|--------|-------------|--------------|
| Creation | Write full class | One line of code |
| Configuration | Edit class code | Configure in test |
| Verification | Manual tracking | Built-in verification |
| Flexibility | Limited | Highly flexible |
| Maintenance | More code to maintain | Less code |

---

## Setting Up Mockito

### Maven Dependencies

```xml
<dependencies>
    <!-- JUnit 5 -->
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter-api</artifactId>
        <version>5.10.0</version>
        <scope>test</scope>
    </dependency>

    <!-- Mockito Core -->
    <dependency>
        <groupId>org.mockito</groupId>
        <artifactId>mockito-core</artifactId>
        <version>5.5.0</version>
        <scope>test</scope>
    </dependency>

    <!-- Mockito JUnit Jupiter Integration -->
    <dependency>
        <groupId>org.mockito</groupId>
        <artifactId>mockito-junit-jupiter</artifactId>
        <version>5.5.0</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

### Gradle Dependencies

```gradle
dependencies {
    testImplementation 'org.junit.jupiter:junit-jupiter-api:5.10.0'
    testImplementation 'org.mockito:mockito-core:5.5.0'
    testImplementation 'org.mockito:mockito-junit-jupiter:5.5.0'
    testRuntimeOnly 'org.junit.jupiter:junit-jupiter-engine:5.10.0'
}
```

---

## Creating Mocks

### Method 1: Using Mockito.mock()

```java
import org.junit.jupiter.api.Test;
import static org.mockito.Mockito.*;

class BasicMockTest {

    @Test
    void testWithManualMock() {
        // Create mock
        UserRepository mockRepository = mock(UserRepository.class);

        // Use mock
        UserService service = new UserService(mockRepository);

        // Test...
    }
}
```

### Method 2: Using @Mock Annotation

```java
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

class AnnotationMockTest {

    @Mock
    private UserRepository mockRepository;

    private UserService service;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
        service = new UserService(mockRepository);
    }

    @Test
    void testSomething() {
        // Use mockRepository
    }
}
```

### Method 3: Using @ExtendWith (Recommended)

```java
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.Mock;
import org.mockito.InjectMocks;
import org.mockito.junit.jupiter.MockitoExtension;

@ExtendWith(MockitoExtension.class)
class ModernMockTest {

    @Mock
    private UserRepository userRepository;

    @Mock
    private EmailService emailService;

    @InjectMocks
    private UserService userService;  // Automatically injects mocks

    @Test
    void testSomething() {
        // Mocks are automatically initialized
        // userService already has mocks injected
    }
}
```

---

## Stubbing Methods

### Basic Stubbing with when().thenReturn()

```java
@ExtendWith(MockitoExtension.class)
class StubbingExampleTest {

    @Mock
    private UserRepository mockRepository;

    @Test
    void testBasicStubbing() {
        // ARRANGE - Define mock behavior
        User expectedUser = new User(1L, "john", "john@example.com");
        when(mockRepository.findById(1L)).thenReturn(expectedUser);

        // ACT
        User result = mockRepository.findById(1L);

        // ASSERT
        assertEquals("john", result.getUsername());
        assertEquals("john@example.com", result.getEmail());
    }

    @Test
    void testMultipleStubbing() {
        User user1 = new User(1L, "john");
        User user2 = new User(2L, "jane");

        // Different return values for different arguments
        when(mockRepository.findById(1L)).thenReturn(user1);
        when(mockRepository.findById(2L)).thenReturn(user2);

        assertEquals("john", mockRepository.findById(1L).getUsername());
        assertEquals("jane", mockRepository.findById(2L).getUsername());
    }
}
```

### Stubbing with Argument Matchers

```java
import static org.mockito.ArgumentMatchers.*;

class ArgumentMatcherTest {

    @Mock
    private UserRepository mockRepository;

    @Test
    void testWithArgumentMatchers() {
        User defaultUser = new User("default");

        // Match any Long argument
        when(mockRepository.findById(anyLong())).thenReturn(defaultUser);

        assertEquals("default", mockRepository.findById(1L).getUsername());
        assertEquals("default", mockRepository.findById(999L).getUsername());
    }

    @Test
    void testStringMatchers() {
        User user = new User("john");

        // Match any String
        when(mockRepository.findByUsername(anyString())).thenReturn(user);

        // Match specific value
        when(mockRepository.findByUsername(eq("admin"))).thenReturn(null);

        assertNotNull(mockRepository.findByUsername("anything"));
        assertNull(mockRepository.findByUsername("admin"));
    }

    @Test
    void testComplexMatchers() {
        User user = new User("test@example.com");

        // Custom matcher - email containing @
        when(mockRepository.findByEmail(contains("@"))).thenReturn(user);

        // String starting with specific prefix
        when(mockRepository.findByUsername(startsWith("admin_")))
            .thenReturn(new User("admin"));

        assertNotNull(mockRepository.findByEmail("test@example.com"));
        assertNotNull(mockRepository.findByUsername("admin_user"));
    }
}
```

### Common Argument Matchers

| Matcher | Description | Example |
|---------|-------------|---------|
| `any()` | Any object | `any()` |
| `anyInt()` | Any int | `anyInt()` |
| `anyLong()` | Any long | `anyLong()` |
| `anyString()` | Any String | `anyString()` |
| `eq(value)` | Specific value | `eq(5)` |
| `isNull()` | Null value | `isNull()` |
| `isNotNull()` | Non-null value | `isNotNull()` |
| `contains(str)` | String containing substring | `contains("test")` |
| `startsWith(str)` | String starting with | `startsWith("pre")` |
| `endsWith(str)` | String ending with | `endsWith("fix")` |

---

## Stubbing Exceptions

### Throwing Exceptions

```java
class ExceptionStubbingTest {

    @Mock
    private UserRepository mockRepository;

    @Test
    void testThrowException() {
        // Stub to throw exception
        when(mockRepository.findById(999L))
            .thenThrow(new UserNotFoundException("User not found"));

        // Verify exception is thrown
        assertThrows(UserNotFoundException.class, () -> {
            mockRepository.findById(999L);
        });
    }

    @Test
    void testThrowRuntimeException() {
        when(mockRepository.save(null))
            .thenThrow(IllegalArgumentException.class);

        assertThrows(IllegalArgumentException.class, () -> {
            mockRepository.save(null);
        });
    }

    @Test
    void testServiceHandlesException() {
        // ARRANGE
        UserService service = new UserService(mockRepository);
        when(mockRepository.findById(anyLong()))
            .thenThrow(new DatabaseException("Connection failed"));

        // ACT & ASSERT
        assertThrows(ServiceException.class, () -> {
            service.getUser(1L);
        });
    }
}
```

---

## Stubbing Void Methods

### Using doThrow() for Void Methods

```java
class VoidMethodStubbingTest {

    @Mock
    private EmailService mockEmailService;

    @Test
    void testVoidMethodThrowsException() {
        // Use doThrow() for void methods
        doThrow(new EmailException("SMTP error"))
            .when(mockEmailService)
            .sendEmail(anyString(), anyString(), anyString());

        // Verify exception
        assertThrows(EmailException.class, () -> {
            mockEmailService.sendEmail("to@example.com", "subject", "body");
        });
    }

    @Test
    void testDoNothing() {
        // Explicitly do nothing (default behavior anyway)
        doNothing().when(mockEmailService)
            .sendEmail(anyString(), anyString(), anyString());

        // Should not throw exception
        assertDoesNotThrow(() -> {
            mockEmailService.sendEmail("to@example.com", "subject", "body");
        });
    }
}
```

---

## Verification

### Verifying Method Calls

```java
class VerificationTest {

    @Mock
    private UserRepository mockRepository;

    @Mock
    private EmailService mockEmailService;

    @InjectMocks
    private UserService userService;

    @Test
    void testMethodWasCalled() {
        // ARRANGE
        User user = new User("john", "john@example.com");
        when(mockRepository.save(any(User.class))).thenReturn(user);

        // ACT
        userService.createUser("john", "john@example.com");

        // ASSERT - Verify repository.save() was called
        verify(mockRepository).save(any(User.class));
    }

    @Test
    void testMethodCalledWithSpecificArguments() {
        // ARRANGE
        User user = new User("john", "john@example.com");
        when(mockRepository.save(any(User.class))).thenReturn(user);

        // ACT
        userService.createUser("john", "john@example.com");

        // ASSERT - Verify with specific argument
        ArgumentCaptor<User> userCaptor = ArgumentCaptor.forClass(User.class);
        verify(mockRepository).save(userCaptor.capture());

        User capturedUser = userCaptor.getValue();
        assertEquals("john", capturedUser.getUsername());
        assertEquals("john@example.com", capturedUser.getEmail());
    }

    @Test
    void testMethodCalledExactlyOnce() {
        User user = new User(1L, "john");
        when(mockRepository.findById(1L)).thenReturn(user);

        userService.getUser(1L);

        // Verify called exactly once (default)
        verify(mockRepository, times(1)).findById(1L);
    }

    @Test
    void testMethodCalledMultipleTimes() {
        User user = new User(1L, "john");
        when(mockRepository.findById(1L)).thenReturn(user);

        userService.getUser(1L);
        userService.getUser(1L);
        userService.getUser(1L);

        // Verify called exactly 3 times
        verify(mockRepository, times(3)).findById(1L);
    }

    @Test
    void testMethodNeverCalled() {
        userService.getUser(1L);

        // Verify email service was never called
        verify(mockEmailService, never()).sendEmail(anyString(), anyString(), anyString());
    }

    @Test
    void testMethodCalledAtLeast() {
        User user = new User(1L, "john");
        when(mockRepository.findById(1L)).thenReturn(user);

        userService.getUser(1L);
        userService.getUser(1L);

        // Verify called at least once
        verify(mockRepository, atLeastOnce()).findById(1L);

        // Verify called at least twice
        verify(mockRepository, atLeast(2)).findById(1L);
    }

    @Test
    void testMethodCalledAtMost() {
        User user = new User(1L, "john");
        when(mockRepository.findById(1L)).thenReturn(user);

        userService.getUser(1L);

        // Verify called at most 2 times
        verify(mockRepository, atMost(2)).findById(1L);
    }
}
```

### Verification Modes

| Mode | Description | Example |
|------|-------------|---------|
| `times(n)` | Exactly n times (default is 1) | `verify(mock, times(2)).method()` |
| `never()` | Never called | `verify(mock, never()).method()` |
| `atLeastOnce()` | At least once | `verify(mock, atLeastOnce()).method()` |
| `atLeast(n)` | At least n times | `verify(mock, atLeast(3)).method()` |
| `atMost(n)` | At most n times | `verify(mock, atMost(5)).method()` |

---

## Argument Captors

Capture arguments passed to mocked methods for detailed assertions.

```java
import org.mockito.ArgumentCaptor;

class ArgumentCaptorTest {

    @Mock
    private EmailService mockEmailService;

    @InjectMocks
    private UserService userService;

    @Test
    void testCaptureEmailArguments() {
        // ARRANGE
        doNothing().when(mockEmailService)
            .sendEmail(anyString(), anyString(), anyString());

        // ACT
        userService.sendWelcomeEmail("john@example.com", "John");

        // ASSERT - Capture arguments
        ArgumentCaptor<String> toCaptor = ArgumentCaptor.forClass(String.class);
        ArgumentCaptor<String> subjectCaptor = ArgumentCaptor.forClass(String.class);
        ArgumentCaptor<String> bodyCaptor = ArgumentCaptor.forClass(String.class);

        verify(mockEmailService).sendEmail(
            toCaptor.capture(),
            subjectCaptor.capture(),
            bodyCaptor.capture()
        );

        assertEquals("john@example.com", toCaptor.getValue());
        assertEquals("Welcome!", subjectCaptor.getValue());
        assertTrue(bodyCaptor.getValue().contains("John"));
    }

    @Test
    void testCaptureMultipleCalls() {
        // ACT - Multiple calls
        userService.sendWelcomeEmail("user1@example.com", "User1");
        userService.sendWelcomeEmail("user2@example.com", "User2");

        // ASSERT - Capture all calls
        ArgumentCaptor<String> emailCaptor = ArgumentCaptor.forClass(String.class);
        verify(mockEmailService, times(2))
            .sendEmail(emailCaptor.capture(), anyString(), anyString());

        List<String> capturedEmails = emailCaptor.getAllValues();
        assertEquals(2, capturedEmails.size());
        assertTrue(capturedEmails.contains("user1@example.com"));
        assertTrue(capturedEmails.contains("user2@example.com"));
    }
}
```

---

## Real-World Example

### Service Layer Testing

```java
public class OrderService {

    private OrderRepository orderRepository;
    private InventoryService inventoryService;
    private PaymentService paymentService;
    private NotificationService notificationService;

    public OrderService(OrderRepository orderRepository,
                       InventoryService inventoryService,
                       PaymentService paymentService,
                       NotificationService notificationService) {
        this.orderRepository = orderRepository;
        this.inventoryService = inventoryService;
        this.paymentService = paymentService;
        this.notificationService = notificationService;
    }

    public Order placeOrder(String customerId, List<OrderItem> items) {
        // Check inventory
        for (OrderItem item : items) {
            if (!inventoryService.checkStock(item.getProductId(), item.getQuantity())) {
                throw new InsufficientStockException(
                    "Not enough stock for product: " + item.getProductId()
                );
            }
        }

        // Calculate total
        double total = items.stream()
            .mapToDouble(item -> item.getPrice() * item.getQuantity())
            .sum();

        // Process payment
        PaymentResult paymentResult = paymentService.charge(customerId, total);
        if (!paymentResult.isSuccess()) {
            throw new PaymentFailedException("Payment failed");
        }

        // Create order
        Order order = new Order(customerId, items, total);
        order.setPaymentTransactionId(paymentResult.getTransactionId());
        order.setStatus(OrderStatus.CONFIRMED);

        // Save order
        Order savedOrder = orderRepository.save(order);

        // Update inventory
        for (OrderItem item : items) {
            inventoryService.decrementStock(item.getProductId(), item.getQuantity());
        }

        // Send confirmation email
        notificationService.sendOrderConfirmation(customerId, savedOrder);

        return savedOrder;
    }
}
```

### Complete Test Suite

```java
@ExtendWith(MockitoExtension.class)
class OrderServiceTest {

    @Mock
    private OrderRepository orderRepository;

    @Mock
    private InventoryService inventoryService;

    @Mock
    private PaymentService paymentService;

    @Mock
    private NotificationService notificationService;

    @InjectMocks
    private OrderService orderService;

    private String customerId;
    private List<OrderItem> orderItems;

    @BeforeEach
    void setUp() {
        customerId = "CUST123";
        orderItems = Arrays.asList(
            new OrderItem("PROD1", 2, 50.0),
            new OrderItem("PROD2", 1, 30.0)
        );
    }

    @Test
    @DisplayName("Should successfully place order when all conditions are met")
    void testSuccessfulOrderPlacement() {
        // ARRANGE
        // Mock inventory check
        when(inventoryService.checkStock("PROD1", 2)).thenReturn(true);
        when(inventoryService.checkStock("PROD2", 1)).thenReturn(true);

        // Mock payment
        PaymentResult paymentResult = new PaymentResult(true, "TXN123");
        when(paymentService.charge(customerId, 130.0)).thenReturn(paymentResult);

        // Mock repository save
        Order savedOrder = new Order(customerId, orderItems, 130.0);
        savedOrder.setId("ORD123");
        when(orderRepository.save(any(Order.class))).thenReturn(savedOrder);

        // ACT
        Order result = orderService.placeOrder(customerId, orderItems);

        // ASSERT
        assertNotNull(result);
        assertEquals("ORD123", result.getId());
        assertEquals(OrderStatus.CONFIRMED, result.getStatus());
        assertEquals("TXN123", result.getPaymentTransactionId());

        // Verify all interactions
        verify(inventoryService).checkStock("PROD1", 2);
        verify(inventoryService).checkStock("PROD2", 1);
        verify(paymentService).charge(customerId, 130.0);
        verify(orderRepository).save(any(Order.class));
        verify(inventoryService).decrementStock("PROD1", 2);
        verify(inventoryService).decrementStock("PROD2", 1);
        verify(notificationService).sendOrderConfirmation(customerId, savedOrder);
    }

    @Test
    @DisplayName("Should throw exception when product is out of stock")
    void testInsufficientStock() {
        // ARRANGE
        when(inventoryService.checkStock("PROD1", 2)).thenReturn(false);

        // ACT & ASSERT
        InsufficientStockException exception = assertThrows(
            InsufficientStockException.class,
            () -> orderService.placeOrder(customerId, orderItems)
        );

        assertTrue(exception.getMessage().contains("PROD1"));

        // Verify no payment or order creation occurred
        verify(paymentService, never()).charge(anyString(), anyDouble());
        verify(orderRepository, never()).save(any(Order.class));
        verify(notificationService, never())
            .sendOrderConfirmation(anyString(), any(Order.class));
    }

    @Test
    @DisplayName("Should throw exception when payment fails")
    void testPaymentFailure() {
        // ARRANGE
        when(inventoryService.checkStock(anyString(), anyInt())).thenReturn(true);

        PaymentResult failedPayment = new PaymentResult(false, null);
        when(paymentService.charge(customerId, 130.0)).thenReturn(failedPayment);

        // ACT & ASSERT
        assertThrows(PaymentFailedException.class, () -> {
            orderService.placeOrder(customerId, orderItems);
        });

        // Verify order was not saved
        verify(orderRepository, never()).save(any(Order.class));
        verify(inventoryService, never()).decrementStock(anyString(), anyInt());
    }

    @Test
    @DisplayName("Should use argument captor to verify order details")
    void testOrderDetailsWithCaptor() {
        // ARRANGE
        when(inventoryService.checkStock(anyString(), anyInt())).thenReturn(true);
        when(paymentService.charge(anyString(), anyDouble()))
            .thenReturn(new PaymentResult(true, "TXN123"));
        when(orderRepository.save(any(Order.class)))
            .thenAnswer(invocation -> invocation.getArgument(0));

        // ACT
        orderService.placeOrder(customerId, orderItems);

        // ASSERT - Capture the order
        ArgumentCaptor<Order> orderCaptor = ArgumentCaptor.forClass(Order.class);
        verify(orderRepository).save(orderCaptor.capture());

        Order capturedOrder = orderCaptor.getValue();
        assertEquals(customerId, capturedOrder.getCustomerId());
        assertEquals(2, capturedOrder.getItems().size());
        assertEquals(130.0, capturedOrder.getTotal(), 0.01);
        assertEquals(OrderStatus.CONFIRMED, capturedOrder.getStatus());
        assertEquals("TXN123", capturedOrder.getPaymentTransactionId());
    }
}
```

---

## Best Practices

### 1. Don't Mock Everything

```java
// Bad - mocking value objects
@Mock
private User user;  // Don't mock simple POJOs

// Good - create real objects
private User user = new User("john", "john@example.com");
```

### 2. Use @InjectMocks

```java
// Bad - manual injection
@Mock private Repository repo;
private Service service;

@BeforeEach
void setUp() {
    service = new Service(repo);  // Manual
}

// Good - automatic injection
@Mock private Repository repo;
@InjectMocks private Service service;
```

### 3. Verify Important Interactions

```java
@Test
void testImportantInteractions() {
    orderService.placeOrder(customerId, items);

    // Verify critical operations
    verify(paymentService).charge(anyString(), anyDouble());
    verify(notificationService).sendConfirmation(anyString());

    // Don't verify every single interaction
    // Focus on what's important for this test
}
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| **What is Mockito** | Mocking framework for creating test doubles |
| **Creating Mocks** | `@Mock`, `@InjectMocks`, `@ExtendWith` |
| **Stubbing** | `when().thenReturn()`, argument matchers |
| **Verification** | `verify()`, times(), never(), ArgumentCaptor |
| **Best Practices** | Don't mock everything, verify important interactions |

## Next Topic

Continue to [Mock vs Stub vs Spy](./08-mock-vs-stub-vs-spy.md) to understand the differences between test double types.
