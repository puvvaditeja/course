# Unit Testing Stubs

## What is a Stub?

A stub is a simplified implementation of a dependency that returns predefined responses. Stubs are used in unit testing to isolate the code under test from its dependencies, ensuring tests are fast, reliable, and focused.

### Key Characteristics of Stubs

- **Predefined Responses**: Return hard-coded values
- **No Logic**: Simple implementations without complex behavior
- **State-Based Testing**: Verify return values and state changes
- **Isolation**: Remove dependencies on external systems
- **Deterministic**: Always return the same result for the same input

### Why Use Stubs?

```
Without Stubs:                    With Stubs:
┌──────────────┐                 ┌──────────────┐
│   Your Code  │                 │   Your Code  │
└──────┬───────┘                 └──────┬───────┘
       │                                │
       ↓                                ↓
┌──────────────┐                 ┌──────────────┐
│   Database   │                 │  Stub (Fast) │
└──────────────┘                 └──────────────┘
  • Slow                           • Fast
  • Requires setup                 • No setup
  • Can fail                       • Reliable
  • External dependency            • Self-contained
```

---

## When to Use Stubs

### Good Use Cases

1. **Database Access**
   - Avoid slow database queries
   - No database setup required
   - Test without data

2. **External APIs**
   - No network calls
   - No rate limiting
   - Consistent responses

3. **File System**
   - No actual file I/O
   - Test error scenarios
   - Platform-independent

4. **Complex Dependencies**
   - Simplify testing
   - Focus on unit under test
   - Avoid cascading failures

---

## Creating Stubs Manually

### Example 1: Repository Stub

#### Interface

```java
public interface UserRepository {
    User findById(Long id);
    User save(User user);
    void delete(Long id);
    List<User> findAll();
}
```

#### Stub Implementation

```java
public class UserRepositoryStub implements UserRepository {

    private Map<Long, User> storage = new HashMap<>();
    private Long nextId = 1L;

    @Override
    public User findById(Long id) {
        return storage.get(id);
    }

    @Override
    public User save(User user) {
        if (user.getId() == null) {
            user.setId(nextId++);
        }
        storage.put(user.getId(), user);
        return user;
    }

    @Override
    public void delete(Long id) {
        storage.remove(id);
    }

    @Override
    public List<User> findAll() {
        return new ArrayList<>(storage.values());
    }

    // Helper method for testing
    public void clear() {
        storage.clear();
    }
}
```

#### Using the Stub in Tests

```java
class UserServiceTest {

    private UserRepository userRepository;
    private UserService userService;

    @BeforeEach
    void setUp() {
        userRepository = new UserRepositoryStub();
        userService = new UserService(userRepository);
    }

    @Test
    @DisplayName("Should create user successfully")
    void testCreateUser() {
        // ARRANGE
        String username = "john_doe";
        String email = "john@example.com";

        // ACT
        User result = userService.createUser(username, email);

        // ASSERT
        assertNotNull(result.getId());
        assertEquals(username, result.getUsername());
        assertEquals(email, result.getEmail());
    }

    @Test
    @DisplayName("Should find user by id")
    void testFindUserById() {
        // ARRANGE
        User savedUser = userRepository.save(new User("john", "john@example.com"));

        // ACT
        User result = userService.findUserById(savedUser.getId());

        // ASSERT
        assertNotNull(result);
        assertEquals("john", result.getUsername());
    }

    @Test
    @DisplayName("Should return null for non-existent user")
    void testFindNonExistentUser() {
        // ACT
        User result = userService.findUserById(999L);

        // ASSERT
        assertNull(result);
    }

    @Test
    @DisplayName("Should update user email")
    void testUpdateUserEmail() {
        // ARRANGE
        User user = userRepository.save(new User("john", "john@example.com"));
        String newEmail = "newemail@example.com";

        // ACT
        userService.updateUserEmail(user.getId(), newEmail);

        // ASSERT
        User updated = userRepository.findById(user.getId());
        assertEquals(newEmail, updated.getEmail());
    }
}
```

---

## Different Types of Stubs

### 1. Hard-Coded Stub

Returns the same value every time.

```java
public class PaymentGatewayStub implements PaymentGateway {

    @Override
    public PaymentResult processPayment(double amount) {
        // Always returns success
        return new PaymentResult(true, "TXN12345", "Payment successful");
    }
}
```

### 2. Configurable Stub

Can be configured to return different values.

```java
public class ConfigurablePaymentGatewayStub implements PaymentGateway {

    private boolean shouldSucceed = true;
    private String transactionId = "TXN12345";

    public void setShouldSucceed(boolean shouldSucceed) {
        this.shouldSucceed = shouldSucceed;
    }

    public void setTransactionId(String transactionId) {
        this.transactionId = transactionId;
    }

    @Override
    public PaymentResult processPayment(double amount) {
        if (shouldSucceed) {
            return new PaymentResult(true, transactionId, "Payment successful");
        } else {
            return new PaymentResult(false, null, "Payment failed");
        }
    }
}
```

#### Using Configurable Stub

```java
class OrderServiceTest {

    private ConfigurablePaymentGatewayStub paymentGateway;
    private OrderService orderService;

    @BeforeEach
    void setUp() {
        paymentGateway = new ConfigurablePaymentGatewayStub();
        orderService = new OrderService(paymentGateway);
    }

    @Test
    void testSuccessfulPayment() {
        // ARRANGE
        paymentGateway.setShouldSucceed(true);
        Order order = new Order(100.0);

        // ACT
        OrderResult result = orderService.placeOrder(order);

        // ASSERT
        assertTrue(result.isSuccess());
        assertEquals("TXN12345", result.getTransactionId());
    }

    @Test
    void testFailedPayment() {
        // ARRANGE
        paymentGateway.setShouldSucceed(false);
        Order order = new Order(100.0);

        // ACT
        OrderResult result = orderService.placeOrder(order);

        // ASSERT
        assertFalse(result.isSuccess());
        assertNull(result.getTransactionId());
    }
}
```

### 3. State-Based Stub

Maintains state and returns different values based on previous calls.

```java
public class SequenceGeneratorStub implements SequenceGenerator {

    private int currentValue = 0;

    @Override
    public int getNext() {
        return ++currentValue;
    }

    @Override
    public void reset() {
        currentValue = 0;
    }

    public int getCurrentValue() {
        return currentValue;
    }
}
```

---

## Stub vs Fake vs Dummy

### Comparison Table

| Type | Purpose | Has Working Implementation | Example |
|------|---------|---------------------------|---------|
| **Dummy** | Fill parameter lists | No | `new Object()` |
| **Stub** | Provide predefined responses | Minimal | Returns hard-coded values |
| **Fake** | Working implementation | Yes (simplified) | In-memory database |

### Examples

#### Dummy Object

```java
// Just to satisfy method signature
@Test
void testSomething() {
    Logger dummyLogger = null;  // Never used
    Service service = new Service(dummyLogger);
    // Test doesn't need logger
}
```

#### Stub

```java
public class EmailServiceStub implements EmailService {
    @Override
    public boolean sendEmail(String to, String subject, String body) {
        return true;  // Always succeeds
    }
}
```

#### Fake

```java
public class InMemoryUserRepository implements UserRepository {
    private Map<Long, User> users = new HashMap<>();

    @Override
    public User findById(Long id) {
        return users.get(id);
    }

    @Override
    public User save(User user) {
        if (user.getId() == null) {
            user.setId(generateId());
        }
        users.put(user.getId(), user);
        return user;
    }

    // Full working implementation, just in-memory instead of database
}
```

---

## Real-World Stub Examples

### Example 1: Weather Service Stub

```java
public interface WeatherService {
    WeatherData getWeather(String city);
    List<WeatherForecast> getForecast(String city, int days);
}

public class WeatherServiceStub implements WeatherService {

    @Override
    public WeatherData getWeather(String city) {
        // Return predefined weather data
        return new WeatherData(city, 72.0, "Sunny", 65);
    }

    @Override
    public List<WeatherForecast> getForecast(String city, int days) {
        List<WeatherForecast> forecast = new ArrayList<>();
        for (int i = 0; i < days; i++) {
            forecast.add(new WeatherForecast(
                LocalDate.now().plusDays(i),
                70 + i,  // Temperature
                "Partly Cloudy"
            ));
        }
        return forecast;
    }
}
```

#### Testing with Weather Stub

```java
class WeatherDashboardTest {

    private WeatherService weatherService;
    private WeatherDashboard dashboard;

    @BeforeEach
    void setUp() {
        weatherService = new WeatherServiceStub();
        dashboard = new WeatherDashboard(weatherService);
    }

    @Test
    void testDisplayCurrentWeather() {
        // ACT
        String display = dashboard.getCurrentWeatherDisplay("New York");

        // ASSERT
        assertTrue(display.contains("New York"));
        assertTrue(display.contains("72.0"));
        assertTrue(display.contains("Sunny"));
    }

    @Test
    void testGetWeekForecast() {
        // ACT
        List<String> forecast = dashboard.getWeekForecast("Chicago", 7);

        // ASSERT
        assertEquals(7, forecast.size());
        assertTrue(forecast.get(0).contains("70"));
    }
}
```

### Example 2: Notification Service Stub

```java
public interface NotificationService {
    void sendNotification(String userId, String message);
    void sendBulkNotifications(List<String> userIds, String message);
}

public class NotificationServiceStub implements NotificationService {

    private List<SentNotification> sentNotifications = new ArrayList<>();

    @Override
    public void sendNotification(String userId, String message) {
        sentNotifications.add(new SentNotification(userId, message));
    }

    @Override
    public void sendBulkNotifications(List<String> userIds, String message) {
        for (String userId : userIds) {
            sendNotification(userId, message);
        }
    }

    // Test helper methods
    public int getNotificationCount() {
        return sentNotifications.size();
    }

    public boolean wasNotificationSent(String userId, String message) {
        return sentNotifications.stream()
            .anyMatch(n -> n.getUserId().equals(userId) &&
                          n.getMessage().equals(message));
    }

    public void clear() {
        sentNotifications.clear();
    }

    private static class SentNotification {
        private String userId;
        private String message;

        public SentNotification(String userId, String message) {
            this.userId = userId;
            this.message = message;
        }

        public String getUserId() { return userId; }
        public String getMessage() { return message; }
    }
}
```

#### Testing with Notification Stub

```java
class UserRegistrationServiceTest {

    private NotificationServiceStub notificationService;
    private UserRegistrationService registrationService;

    @BeforeEach
    void setUp() {
        notificationService = new NotificationServiceStub();
        registrationService = new UserRegistrationService(notificationService);
    }

    @Test
    void testUserRegistrationSendsWelcomeEmail() {
        // ARRANGE
        User user = new User("john", "john@example.com");

        // ACT
        registrationService.registerUser(user);

        // ASSERT
        assertEquals(1, notificationService.getNotificationCount());
        assertTrue(notificationService.wasNotificationSent(
            user.getId(),
            "Welcome to our platform!"
        ));
    }

    @Test
    void testBulkRegistrationSendsMultipleNotifications() {
        // ARRANGE
        List<User> users = Arrays.asList(
            new User("user1", "user1@example.com"),
            new User("user2", "user2@example.com"),
            new User("user3", "user3@example.com")
        );

        // ACT
        registrationService.registerBulk(users);

        // ASSERT
        assertEquals(3, notificationService.getNotificationCount());
    }
}
```

---

## Advanced Stub Techniques

### 1. Exception-Throwing Stub

```java
public class FailingDatabaseStub implements Database {

    private boolean shouldFail = false;

    public void setShouldFail(boolean shouldFail) {
        this.shouldFail = shouldFail;
    }

    @Override
    public User findById(Long id) {
        if (shouldFail) {
            throw new DatabaseException("Connection failed");
        }
        return new User(id, "Test User");
    }
}
```

#### Testing Error Handling

```java
@Test
void testHandlesDatabaseFailure() {
    // ARRANGE
    FailingDatabaseStub database = new FailingDatabaseStub();
    database.setShouldFail(true);
    UserService service = new UserService(database);

    // ACT & ASSERT
    assertThrows(ServiceException.class, () -> {
        service.getUser(1L);
    });
}
```

### 2. Delayed Response Stub

```java
public class DelayedApiStub implements ExternalApi {

    private long delayMillis = 0;

    public void setDelay(long millis) {
        this.delayMillis = millis;
    }

    @Override
    public Response getData() {
        if (delayMillis > 0) {
            try {
                Thread.sleep(delayMillis);
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }
        return new Response("data");
    }
}
```

#### Testing Timeout Behavior

```java
@Test
void testTimeoutHandling() {
    // ARRANGE
    DelayedApiStub api = new DelayedApiStub();
    api.setDelay(5000);  // 5 second delay
    TimeoutService service = new TimeoutService(api, 1000);  // 1 second timeout

    // ACT & ASSERT
    assertThrows(TimeoutException.class, () -> {
        service.fetchData();
    });
}
```

### 3. Counting Stub

Tracks how many times methods are called.

```java
public class CountingCacheStub implements Cache {

    private int getCallCount = 0;
    private int putCallCount = 0;
    private Map<String, Object> storage = new HashMap<>();

    @Override
    public Object get(String key) {
        getCallCount++;
        return storage.get(key);
    }

    @Override
    public void put(String key, Object value) {
        putCallCount++;
        storage.put(key, value);
    }

    public int getGetCallCount() {
        return getCallCount;
    }

    public int getPutCallCount() {
        return putCallCount;
    }

    public void resetCounts() {
        getCallCount = 0;
        putCallCount = 0;
    }
}
```

#### Testing Cache Usage

```java
@Test
void testServiceUsesCache() {
    // ARRANGE
    CountingCacheStub cache = new CountingCacheStub();
    DataService service = new DataService(cache);

    // ACT
    service.getData("key1");
    service.getData("key1");  // Should use cache
    service.getData("key2");

    // ASSERT
    assertEquals(3, cache.getGetCallCount(), "Should call cache.get() 3 times");
}
```

---

## Stub Best Practices

### 1. Keep Stubs Simple

```java
// Good - simple and focused
public class UserRepositoryStub implements UserRepository {
    private Map<Long, User> users = new HashMap<>();

    @Override
    public User findById(Long id) {
        return users.get(id);
    }

    @Override
    public User save(User user) {
        if (user.getId() == null) {
            user.setId((long) (users.size() + 1));
        }
        users.put(user.getId(), user);
        return user;
    }
}

// Bad - too complex, becoming a fake
public class ComplexUserRepositoryStub implements UserRepository {
    private Map<Long, User> users = new HashMap<>();
    private List<String> queries = new ArrayList<>();
    private boolean simulateNetworkDelay = false;
    private Random random = new Random();

    @Override
    public User findById(Long id) {
        queries.add("SELECT * FROM users WHERE id = " + id);
        if (simulateNetworkDelay) {
            sleep(random.nextInt(100));
        }
        // ... too much logic
    }
}
```

### 2. Make Stubs Reusable

```java
// Create a base stub with common functionality
public class BaseRepositoryStub<T, ID> {
    protected Map<ID, T> storage = new HashMap<>();
    protected Function<T, ID> idGenerator;

    public BaseRepositoryStub(Function<T, ID> idGenerator) {
        this.idGenerator = idGenerator;
    }

    public T findById(ID id) {
        return storage.get(id);
    }

    public T save(T entity) {
        ID id = idGenerator.apply(entity);
        storage.put(id, entity);
        return entity;
    }

    public void clear() {
        storage.clear();
    }
}

// Extend for specific entities
public class UserRepositoryStub extends BaseRepositoryStub<User, Long>
        implements UserRepository {

    public UserRepositoryStub() {
        super(User::getId);
    }

    @Override
    public List<User> findByUsername(String username) {
        return storage.values().stream()
            .filter(u -> u.getUsername().equals(username))
            .collect(Collectors.toList());
    }
}
```

### 3. Provide Test Helpers

```java
public class EmailServiceStub implements EmailService {

    private List<Email> sentEmails = new ArrayList<>();

    @Override
    public void send(Email email) {
        sentEmails.add(email);
    }

    // Test helper methods
    public int getSentEmailCount() {
        return sentEmails.size();
    }

    public boolean wasEmailSent(String to) {
        return sentEmails.stream()
            .anyMatch(e -> e.getTo().equals(to));
    }

    public Email getLastSentEmail() {
        return sentEmails.isEmpty() ? null : sentEmails.get(sentEmails.size() - 1);
    }

    public List<Email> getEmailsSentTo(String recipient) {
        return sentEmails.stream()
            .filter(e -> e.getTo().equals(recipient))
            .collect(Collectors.toList());
    }

    public void clear() {
        sentEmails.clear();
    }
}
```

---

## When NOT to Use Stubs

### 1. Testing the Stub Itself

```java
// Bad - testing the stub
@Test
void testRepositoryStub() {
    UserRepositoryStub repo = new UserRepositoryStub();
    User user = new User("john");
    repo.save(user);
    assertEquals(user, repo.findById(user.getId()));
    // This tests the stub, not your actual code!
}

// Good - test your service using the stub
@Test
void testUserService() {
    UserRepositoryStub repo = new UserRepositoryStub();
    UserService service = new UserService(repo);
    User user = service.createUser("john", "john@example.com");
    assertNotNull(user);
    // This tests your service
}
```

### 2. Complex Business Logic

If the dependency has complex logic, consider using a mock framework (Mockito) instead of creating manual stubs.

---

## Summary

| Concept | Key Points |
|---------|------------|
| **What is a Stub** | Simplified implementation returning predefined responses |
| **Purpose** | Isolate code under test, fast and reliable tests |
| **Types** | Hard-coded, configurable, state-based |
| **vs Fake** | Stub: minimal logic, Fake: working implementation |
| **Best Practices** | Keep simple, make reusable, provide test helpers |

### Stub Characteristics Summary

```
Stub Characteristics:
├── Returns predefined data
├── No real logic
├── Fast and deterministic
├── Easy to understand
└── Manual implementation
```

## Next Topic

Continue to [Introduction to Mockito](./07-introduction-to-mockito.md) to learn about using a mocking framework for more powerful test doubles.
