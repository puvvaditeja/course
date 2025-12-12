# Mock vs Stub vs Spy

## Understanding Test Doubles

Test doubles are objects that stand in for real dependencies during testing. There are several types of test doubles, each serving a different purpose. Understanding the differences helps you choose the right tool for each testing scenario.

### Test Double Hierarchy

```
Test Doubles
├── Dummy
├── Stub
├── Spy
├── Mock
└── Fake
```

---

## The Five Types of Test Doubles

### 1. Dummy Objects

Objects passed around but never actually used. They fill parameter lists but have no behavior.

```java
// Example: Logger that's required but not used in test
@Test
void testWithDummy() {
    Logger dummyLogger = null;  // Never called in this test
    Calculator calculator = new Calculator(dummyLogger);

    int result = calculator.add(5, 3);

    assertEquals(8, result);
}
```

**Characteristics:**
- No behavior
- Never actually invoked
- Just fills method parameters

---

## 2. Stubs

### What is a Stub?

A stub provides predefined answers to method calls. It has minimal implementation and returns hard-coded values.

### Stub Characteristics

| Aspect | Description |
|--------|-------------|
| **Purpose** | Provide canned responses |
| **Behavior** | Returns predefined values |
| **Verification** | Not used for verification |
| **State** | Can maintain simple state |
| **Logic** | Minimal or no logic |

### Manual Stub Example

```java
public class UserRepositoryStub implements UserRepository {

    private Map<Long, User> users = new HashMap<>();

    @Override
    public User findById(Long id) {
        return users.get(id);
    }

    @Override
    public User save(User user) {
        if (user.getId() == null) {
            user.setId((long) users.size() + 1);
        }
        users.put(user.getId(), user);
        return user;
    }

    @Override
    public List<User> findAll() {
        return new ArrayList<>(users.values());
    }
}
```

### Mockito Stub Example

```java
@Test
void testWithMockitoStub() {
    // Create stub using Mockito
    UserRepository stub = mock(UserRepository.class);

    // Define stub behavior
    User user = new User(1L, "john");
    when(stub.findById(1L)).thenReturn(user);

    // Use stub
    UserService service = new UserService(stub);
    User result = service.getUser(1L);

    assertEquals("john", result.getUsername());

    // No verification needed - stub is for state-based testing
}
```

### When to Use Stubs

- Testing query operations (reads)
- Need predefined responses
- State-based verification
- Don't care about how many times methods are called

---

## 3. Mocks

### What is a Mock?

A mock is pre-programmed with expectations about calls it should receive. It's used for behavior verification (interaction testing).

### Mock Characteristics

| Aspect | Description |
|--------|-------------|
| **Purpose** | Verify interactions |
| **Behavior** | Can be programmed |
| **Verification** | Primary purpose is verification |
| **Expectations** | Pre-programmed expectations |
| **Failures** | Can fail tests on unexpected calls |

### Mock Example

```java
@ExtendWith(MockitoExtension.class)
class MockExampleTest {

    @Mock
    private EmailService emailService;

    @InjectMocks
    private UserRegistrationService registrationService;

    @Test
    void testMockVerification() {
        // ARRANGE
        User user = new User("john", "john@example.com");

        // ACT
        registrationService.registerUser(user);

        // ASSERT - Verify behavior (interaction)
        verify(emailService).sendWelcomeEmail("john@example.com", "john");
        verify(emailService, times(1)).sendWelcomeEmail(anyString(), anyString());
        verify(emailService, never()).sendPasswordResetEmail(anyString());
    }

    @Test
    void testMockWithArgumentCaptor() {
        // ARRANGE
        User user = new User("john", "john@example.com");

        // ACT
        registrationService.registerUser(user);

        // ASSERT - Capture and verify arguments
        ArgumentCaptor<String> emailCaptor = ArgumentCaptor.forClass(String.class);
        ArgumentCaptor<String> nameCaptor = ArgumentCaptor.forClass(String.class);

        verify(emailService).sendWelcomeEmail(
            emailCaptor.capture(),
            nameCaptor.capture()
        );

        assertEquals("john@example.com", emailCaptor.getValue());
        assertEquals("john", nameCaptor.getValue());
    }
}
```

### When to Use Mocks

- Testing command operations (writes)
- Verify method calls and interactions
- Behavior-based verification
- Care about how many times methods are called
- Need to verify method parameters

---

## 4. Spies

### What is a Spy?

A spy wraps a real object and allows you to verify calls while still calling the real methods. It's a partial mock.

### Spy Characteristics

| Aspect | Description |
|--------|-------------|
| **Purpose** | Wrap real objects |
| **Behavior** | Calls real methods by default |
| **Selective Stubbing** | Can stub specific methods |
| **Verification** | Can verify interactions |
| **Real Logic** | Executes actual code |

### Spy Example

```java
@ExtendWith(MockitoExtension.class)
class SpyExampleTest {

    @Test
    void testSpyBasics() {
        // Create a spy of a real object
        List<String> spyList = spy(new ArrayList<>());

        // Real methods are called
        spyList.add("one");
        spyList.add("two");

        // Real method called - returns actual size
        assertEquals(2, spyList.size());

        // Can verify interactions
        verify(spyList).add("one");
        verify(spyList).add("two");
        verify(spyList, times(2)).add(anyString());
    }

    @Test
    void testSpyWithPartialStubbing() {
        List<String> spyList = spy(new ArrayList<>());

        // Add real items
        spyList.add("one");
        spyList.add("two");

        // Stub one method
        when(spyList.size()).thenReturn(100);

        // Stubbed method returns stubbed value
        assertEquals(100, spyList.size());

        // Other methods still work normally
        assertEquals("one", spyList.get(0));
    }

    @Test
    void testSpyWithRealService() {
        // Create real service
        UserService realService = new UserService();

        // Spy on real service
        UserService spyService = spy(realService);

        // Stub only one method
        doReturn(new User("stubbed")).when(spyService).findById(999L);

        // Stubbed method returns stub
        assertEquals("stubbed", spyService.findById(999L).getUsername());

        // Real methods still execute
        User newUser = spyService.createUser("john", "john@example.com");
        assertNotNull(newUser);  // Real creation logic executed

        // Can verify calls
        verify(spyService).findById(999L);
        verify(spyService).createUser("john", "john@example.com");
    }
}
```

### When to Use Spies

- Need mostly real behavior
- Want to verify some interactions
- Need to stub only specific methods
- Testing legacy code where full mocking is difficult
- Partial mocking scenarios

### Spy Caution

```java
@Test
void spyCaution() {
    List<String> spyList = spy(new ArrayList<>());

    // DANGEROUS - calls real method first
    // when(spyList.get(0)).thenReturn("foo");  // IndexOutOfBoundsException!

    // SAFE - doesn't call real method
    doReturn("foo").when(spyList).get(0);

    assertEquals("foo", spyList.get(0));
}
```

**Rule**: Use `doReturn()` instead of `when()` for spies to avoid calling real methods.

---

## 5. Fakes

### What is a Fake?

A fake has a working implementation but takes shortcuts (e.g., in-memory database instead of real database).

### Fake Example

```java
public class InMemoryUserRepository implements UserRepository {

    private Map<Long, User> database = new HashMap<>();
    private AtomicLong idGenerator = new AtomicLong(1);

    @Override
    public User save(User user) {
        if (user.getId() == null) {
            user.setId(idGenerator.getAndIncrement());
        }
        database.put(user.getId(), user);
        return user;
    }

    @Override
    public User findById(Long id) {
        return database.get(id);
    }

    @Override
    public List<User> findAll() {
        return new ArrayList<>(database.values());
    }

    @Override
    public void delete(Long id) {
        database.remove(id);
    }

    @Override
    public List<User> findByUsername(String username) {
        return database.values().stream()
            .filter(u -> u.getUsername().equals(username))
            .collect(Collectors.toList());
    }

    // Complete working implementation!
}
```

### Fake vs Stub

| Aspect | Stub | Fake |
|--------|------|------|
| Implementation | Minimal, hard-coded | Working implementation |
| Logic | Little to none | Business logic |
| Complexity | Simple | More complex |
| Example | Returns fixed value | In-memory database |

---

## Comparison Table

| Type | Purpose | Has Working Logic | Verifiable | Example |
|------|---------|-------------------|------------|---------|
| **Dummy** | Fill parameters | No | No | `null`, unused object |
| **Stub** | Provide answers | Minimal | No (not intended) | Returns hard-coded data |
| **Mock** | Verify behavior | No | Yes | Verify method calls |
| **Spy** | Partial mock | Yes (real object) | Yes | Wrap real object |
| **Fake** | Simplified impl | Yes | No | In-memory database |

---

## Detailed Comparison: Mock vs Stub

### State vs Behavior Verification

```java
// STUB - State Verification
@Test
void testWithStub() {
    UserRepository stub = mock(UserRepository.class);
    when(stub.findById(1L)).thenReturn(new User("john"));

    UserService service = new UserService(stub);
    User result = service.getUser(1L);

    // State verification - check the result
    assertEquals("john", result.getUsername());

    // No verification of interactions
}

// MOCK - Behavior Verification
@Test
void testWithMock() {
    EmailService mock = mock(EmailService.class);
    UserService service = new UserService(mock);

    service.registerUser(new User("john", "john@example.com"));

    // Behavior verification - check interactions
    verify(mock).sendWelcomeEmail("john@example.com", "john");
    verify(mock, times(1)).sendWelcomeEmail(anyString(), anyString());
}
```

### When to Use Each

#### Use Stubs When:
```java
@Test
void useStubForQueries() {
    // Testing queries - care about returned data
    ProductRepository stub = mock(ProductRepository.class);
    when(stub.findById(1L)).thenReturn(new Product("Laptop", 999.99));

    ProductService service = new ProductService(stub);
    Product product = service.getProductDetails(1L);

    // Assert on state/data
    assertEquals("Laptop", product.getName());
    assertEquals(999.99, product.getPrice(), 0.01);
}
```

#### Use Mocks When:
```java
@Test
void useMockForCommands() {
    // Testing commands - care about behavior
    AuditLogger mock = mock(AuditLogger.class);
    OrderService service = new OrderService(mock);

    service.deleteOrder(123L);

    // Verify interaction occurred
    verify(mock).logDeletion("Order", 123L);
}
```

---

## Practical Examples

### Example 1: E-commerce Order Processing

```java
@ExtendWith(MockitoExtension.class)
class OrderProcessingTest {

    // STUB - for reading data
    @Mock
    private ProductRepository productRepository;

    @Mock
    private CustomerRepository customerRepository;

    // MOCK - for verifying actions
    @Mock
    private PaymentGateway paymentGateway;

    @Mock
    private EmailService emailService;

    @Mock
    private InventoryService inventoryService;

    @InjectMocks
    private OrderService orderService;

    @Test
    void testCompleteOrderFlow() {
        // ARRANGE - Stub repositories (state)
        Product product = new Product(1L, "Laptop", 999.99);
        when(productRepository.findById(1L)).thenReturn(product);

        Customer customer = new Customer(1L, "john@example.com");
        when(customerRepository.findById(1L)).thenReturn(customer);

        // Stub payment gateway to return success
        when(paymentGateway.charge(anyDouble(), anyString()))
            .thenReturn(new PaymentResult(true, "TXN123"));

        // ACT
        Order order = orderService.placeOrder(1L, 1L, 1);

        // ASSERT - State verification
        assertEquals(999.99, order.getTotalAmount(), 0.01);
        assertEquals("TXN123", order.getPaymentTransactionId());

        // ASSERT - Behavior verification (mocks)
        verify(paymentGateway).charge(999.99, "john@example.com");
        verify(inventoryService).decrementStock(1L, 1);
        verify(emailService).sendOrderConfirmation(eq("john@example.com"), any(Order.class));
    }
}
```

### Example 2: Using Spy for Legacy Code

```java
class LegacyServiceTest {

    @Test
    void testLegacyServiceWithSpy() {
        // Real legacy service with complex constructor
        LegacyService realService = new LegacyService(
            new Config(),
            new Database(),
            new Logger()
        );

        // Spy on it to stub problematic method
        LegacyService spyService = spy(realService);

        // Stub only the problematic external call
        doReturn("stubbed-response")
            .when(spyService)
            .callExternalApi(anyString());

        // Rest of the real logic executes
        String result = spyService.processData("input");

        // Verify real logic worked
        assertNotNull(result);

        // Verify stubbed method was called
        verify(spyService).callExternalApi("input");
    }
}
```

### Example 3: Fake for Integration-Like Tests

```java
class UserServiceIntegrationStyleTest {

    @Test
    void testWithFakeRepository() {
        // Use fake instead of real database
        UserRepository fakeRepo = new InMemoryUserRepository();
        UserService service = new UserService(fakeRepo);

        // Create user
        User created = service.createUser("john", "john@example.com");
        assertNotNull(created.getId());

        // Retrieve user
        User retrieved = service.getUser(created.getId());
        assertEquals("john", retrieved.getUsername());

        // Update user
        service.updateEmail(created.getId(), "newemail@example.com");
        User updated = service.getUser(created.getId());
        assertEquals("newemail@example.com", updated.getEmail());

        // List all users
        List<User> allUsers = service.getAllUsers();
        assertEquals(1, allUsers.size());

        // Fake has full working implementation!
    }
}
```

---

## Best Practices

### 1. Choose the Right Test Double

```java
// Good - Use stub for data
@Mock
private UserRepository userRepository;  // Stub usage

// Good - Use mock for verification
@Mock
private EmailService emailService;  // Mock usage

@Test
void testExample() {
    // Stub - just return data
    when(userRepository.findById(1L)).thenReturn(user);

    service.notifyUser(1L);

    // Mock - verify interaction
    verify(emailService).send(anyString());
}
```

### 2. Don't Overuse Spies

```java
// Bad - using spy when mock would suffice
@Spy
private SimpleService service;

// Good - use mock for new code
@Mock
private SimpleService service;

// Good - use spy for legacy/complex scenarios
@Spy
private ComplexLegacyService legacyService;
```

### 3. Prefer Mocks Over Stubs for Mockito

```java
// In Mockito, both are created the same way
@Mock
private EmailService emailService;

// Use as stub (state-based)
when(emailService.getTemplate()).thenReturn("template");

// OR use as mock (behavior-based)
verify(emailService).send(anyString());

// Same object, different usage patterns
```

---

## Decision Tree

```
Need a test double?
│
├─ Just filling parameters?
│  └─> DUMMY
│
├─ Need to return data?
│  ├─ Simple hard-coded response?
│  │  └─> STUB
│  └─ Complex working implementation?
│     └─> FAKE
│
├─ Need to verify interactions?
│  ├─ All methods are mock?
│  │  └─> MOCK
│  └─ Some real, some mock?
│     └─> SPY
```

---

## Common Pitfalls

### 1. Overusing Mocks

```java
// Bad - mocking everything
@Mock private User user;
@Mock private String username;

// Good - only mock dependencies
@Mock private UserRepository userRepository;
private User user = new User("john");  // Real object
```

### 2. Confusing Stub and Mock Usage

```java
// Bad - verifying stub (misuse)
when(repository.findById(1L)).thenReturn(user);
service.getUser(1L);
verify(repository).findById(1L);  // Unnecessary!

// Good - verify mocks, assert stub results
when(repository.findById(1L)).thenReturn(user);
User result = service.getUser(1L);
assertEquals("john", result.getUsername());  // Assert state
```

### 3. Spy Pitfalls

```java
// Bad - will call real method
List<String> spy = spy(new ArrayList<>());
when(spy.get(0)).thenReturn("foo");  // Exception!

// Good - use doReturn
doReturn("foo").when(spy).get(0);
```

---

## Summary Table

| Test Double | Creation | Usage | Verification |
|-------------|----------|-------|--------------|
| **Dummy** | `new Object()` or `null` | Parameter filling | No |
| **Stub** | `mock()` + `when()` | Return data | Optional |
| **Mock** | `mock()` + `verify()` | Verify interactions | Yes |
| **Spy** | `spy(realObject)` | Partial mocking | Yes |
| **Fake** | Manual class | Working implementation | No |

### Key Takeaways

1. **Stubs** = State verification (return data)
2. **Mocks** = Behavior verification (verify calls)
3. **Spies** = Partial mocks (real + stubbed)
4. **Fakes** = Working implementation
5. **Dummies** = Unused parameters

## Next Topic

Continue to [Test Driven Development](./09-test-driven-development.md) to learn about the TDD methodology and workflow.
