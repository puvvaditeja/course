# Java Core - 4-Day Practice Exercises

## Table of Contents
- [Day 1: OOP Foundations](#day-1-oop-foundations)
- [Day 2: OOP Advanced + Exceptions](#day-2-oop-advanced--exceptions)
- [Day 3: Collections + Functional + Streams](#day-3-collections--functional--streams)
- [Day 4: I/O + Concurrency](#day-4-io--concurrency)

---

## Day 1: OOP Foundations

### Exercise 1.1: Class Basics (30 min)

Create a `Person` class with proper encapsulation.

**Requirements:**
- Fields: `name` (String), `age` (int), `email` (String)
- Private fields with getters/setters
- Constructors: no-arg, all-args
- `toString()` method
- Validation: age must be 0-150, email must contain '@'

```java
// Expected usage:
Person p1 = new Person();
Person p2 = new Person("John", 25, "john@email.com");
p2.setAge(30);
System.out.println(p2);  // Person{name='John', age=30, email='john@email.com'}
```

---

### Exercise 1.2: Static Members (30 min)

Create a `BankAccount` class with instance and static members.

**Requirements:**
- Static field: `bankName`, `totalAccounts` (tracks count)
- Instance fields: `accountNumber`, `holderName`, `balance`
- Static method: `getBankInfo()` returns bank name and total accounts
- Instance methods: `deposit()`, `withdraw()`, `getBalance()`
- Auto-generate account numbers using static counter

```java
// Expected usage:
BankAccount acc1 = new BankAccount("Alice", 1000);
BankAccount acc2 = new BankAccount("Bob", 500);
System.out.println(BankAccount.getBankInfo());
// "MyBank - Total Accounts: 2"
```

---

### Exercise 1.3: Inheritance & Polymorphism (1 hour)

Build a shape hierarchy with area calculations.

**Requirements:**
1. Abstract class `Shape` with:
   - Abstract method `double area()`
   - Abstract method `double perimeter()`
   - Concrete method `void displayInfo()` - prints area and perimeter

2. Subclasses:
   - `Circle` (radius)
   - `Rectangle` (width, height)
   - `Triangle` (base, height, side1, side2, side3)

3. Test polymorphism:
```java
Shape[] shapes = {
    new Circle(5),
    new Rectangle(4, 6),
    new Triangle(3, 4, 3, 4, 5)
};

for (Shape s : shapes) {
    s.displayInfo();
}
```

---

### Exercise 1.4: Banking System (2 hours)

Build a complete banking system demonstrating OOP principles.

**Class Hierarchy:**
```
Account (abstract)
├── SavingsAccount
├── CheckingAccount
└── FixedDepositAccount
```

**Requirements:**

1. `Account` (abstract):
   - Fields: accountNumber, holderName, balance
   - Abstract: `calculateInterest()`
   - Concrete: `deposit()`, `withdraw()`, `getBalance()`
   - Static: account number generator

2. `SavingsAccount`:
   - Interest rate: 4% annually
   - Minimum balance: $100
   - Withdraw fails if balance goes below minimum

3. `CheckingAccount`:
   - No interest
   - Overdraft limit: $500
   - Can withdraw up to (balance + overdraft)

4. `FixedDepositAccount`:
   - Interest rate: 7% annually
   - Lock period: 12 months
   - No withdrawals allowed (throw exception)

5. Create a `Bank` class:
   - Stores list of accounts
   - Methods: `addAccount()`, `findAccount()`, `getTotalDeposits()`

```java
// Expected usage:
Bank bank = new Bank("MyBank");

Account savings = new SavingsAccount("Alice", 1000);
Account checking = new CheckingAccount("Bob", 500);
Account fd = new FixedDepositAccount("Charlie", 10000);

bank.addAccount(savings);
bank.addAccount(checking);
bank.addAccount(fd);

savings.deposit(500);
checking.withdraw(800);  // Uses overdraft

System.out.println("Total deposits: $" + bank.getTotalDeposits());

// Print interest for all accounts
for (Account acc : bank.getAllAccounts()) {
    System.out.println(acc.getHolderName() + " interest: $" + acc.calculateInterest());
}
```

---

## Day 2: OOP Advanced + Exceptions

### Exercise 2.1: Interfaces (45 min)

Create a payment processing system using interfaces.

**Requirements:**

1. Interface `Payable`:
   - `double getPaymentAmount()`
   - Default method `void printPaymentInfo()`

2. Interface `Taxable`:
   - `double calculateTax()`
   - Static method `double getTaxRate()` returns 0.18 (18%)

3. Classes implementing interfaces:
   - `Invoice` implements `Payable` (partNumber, description, quantity, pricePerItem)
   - `Employee` implements `Payable`, `Taxable` (name, salary)

```java
// Expected usage:
Payable[] payables = {
    new Invoice("P001", "Laptop", 2, 1000),
    new Employee("John", 5000)
};

double total = 0;
for (Payable p : payables) {
    p.printPaymentInfo();
    total += p.getPaymentAmount();
}
System.out.println("Total: $" + total);
```

---

### Exercise 2.2: Generics (45 min)

Create a generic `Pair<K, V>` class and a generic utility class.

**Requirements:**

1. `Pair<K, V>` class:
   - Fields: key (K), value (V)
   - Methods: getKey(), getValue(), setKey(), setValue()
   - Override equals(), hashCode(), toString()

2. `Box<T>` class:
   - Store single item of type T
   - Methods: set(), get(), isEmpty(), clear()

3. `CollectionUtils` class with static generic methods:
   - `<T> T findFirst(List<T> list, Predicate<T> predicate)`
   - `<T extends Comparable<T>> T findMax(List<T> list)`
   - `<T> List<T> filter(List<T> list, Predicate<T> predicate)`

```java
// Expected usage:
Pair<String, Integer> pair = new Pair<>("Age", 25);
System.out.println(pair);  // Pair{key='Age', value=25}

Box<String> box = new Box<>();
box.set("Hello");
System.out.println(box.get());  // Hello

List<Integer> numbers = List.of(1, 5, 3, 9, 2);
Integer max = CollectionUtils.findMax(numbers);  // 9

List<String> names = List.of("Alice", "Bob", "Anna");
List<String> aNames = CollectionUtils.filter(names, s -> s.startsWith("A"));
// [Alice, Anna]
```

---

### Exercise 2.3: Custom Annotations (30 min)

Create and process custom annotations.

**Requirements:**

1. Create `@Validate` annotation:
   - Target: FIELD
   - Retention: RUNTIME
   - Elements: `min`, `max`, `message`

2. Create `@NotNull` annotation:
   - Target: FIELD
   - Retention: RUNTIME
   - Element: `message`

3. Create `Validator` class:
   - Method `validate(Object obj)` that uses reflection
   - Returns `List<String>` of validation errors

```java
// Usage:
public class User {
    @NotNull(message = "Name is required")
    private String name;

    @Validate(min = 18, max = 100, message = "Age must be 18-100")
    private int age;
}

User user = new User();
user.setAge(15);

List<String> errors = Validator.validate(user);
// ["Name is required", "Age must be 18-100"]
```

---

### Exercise 2.4: Exception Handling (1.5 hours)

Build a user registration system with comprehensive exception handling.

**Requirements:**

1. Custom exceptions:
   - `ValidationException` (checked) - for validation errors
   - `DuplicateUserException` (unchecked) - for duplicate emails
   - `DatabaseException` (checked) - for DB errors

2. `User` class with validation:
   - name: 2-50 chars
   - email: valid format, unique
   - password: min 8 chars, 1 uppercase, 1 number
   - age: 13+

3. `UserService` class:
   - `register(User user)` throws ValidationException, DuplicateUserException
   - `findByEmail(String email)` throws DatabaseException
   - Uses try-with-resources for any resources

4. `UserController` class:
   - Handles all exceptions appropriately
   - Returns meaningful error messages

```java
// Expected usage:
UserController controller = new UserController();

// Should handle validation error
Result result1 = controller.register("J", "invalid-email", "weak", 10);
// Result{success=false, errors=[Name too short, Invalid email, Password too weak, Must be 13+]}

// Should handle duplicate
Result result2 = controller.register("John", "existing@email.com", "Strong1Pass", 25);
// Result{success=false, errors=[Email already registered]}

// Should succeed
Result result3 = controller.register("Alice", "alice@email.com", "Secure123", 30);
// Result{success=true, message=User registered successfully}
```

---

## Day 3: Collections + Functional + Streams

### Exercise 3.1: Collections CRUD (1 hour)

Build a product inventory system using different collections.

**Requirements:**

1. `Product` class:
   - Fields: id, name, category, price, quantity
   - Implement `Comparable<Product>` (by name)
   - Override `equals()` and `hashCode()` (by id)

2. `Inventory` class using:
   - `List<Product>` for maintaining order
   - `Set<String>` for unique categories
   - `Map<String, Product>` for id-based lookup
   - `Queue<Product>` for low-stock alerts

3. Operations:
   - Add, update, delete products
   - Find by id, by category
   - Get all products sorted by price
   - Get low stock products (quantity < 10)

```java
// Expected usage:
Inventory inventory = new Inventory();

inventory.addProduct(new Product("P001", "Laptop", "Electronics", 999.99, 50));
inventory.addProduct(new Product("P002", "Mouse", "Electronics", 29.99, 5));
inventory.addProduct(new Product("P003", "Desk", "Furniture", 299.99, 15));

// Get by category
List<Product> electronics = inventory.getByCategory("Electronics");

// Get sorted by price
List<Product> sorted = inventory.getAllSortedByPrice();

// Get low stock alerts
Queue<Product> lowStock = inventory.getLowStockAlerts();
while (!lowStock.isEmpty()) {
    Product p = lowStock.poll();
    System.out.println("Low stock: " + p.getName());
}
```

---

### Exercise 3.2: Lambdas & Method References (45 min)

Refactor code to use lambdas and method references.

**Task:** Convert the following to use lambdas/method references:

```java
// 1. Sort list of strings by length
Collections.sort(names, new Comparator<String>() {
    @Override
    public int compare(String s1, String s2) {
        return Integer.compare(s1.length(), s2.length());
    }
});

// 2. Filter even numbers
List<Integer> evens = new ArrayList<>();
for (Integer n : numbers) {
    if (n % 2 == 0) {
        evens.add(n);
    }
}

// 3. Print each element
for (String s : names) {
    System.out.println(s);
}

// 4. Create thread
Thread t = new Thread(new Runnable() {
    @Override
    public void run() {
        System.out.println("Running");
    }
});

// 5. Transform strings to uppercase
List<String> upper = new ArrayList<>();
for (String s : names) {
    upper.add(s.toUpperCase());
}
```

**Expected solutions use:**
- Lambda expressions
- Method references (static, instance, constructor)
- Functional interfaces (Predicate, Function, Consumer)

---

### Exercise 3.3: Optional (30 min)

Refactor null-handling code to use Optional.

**Requirements:**

1. `UserRepository`:
   - `Optional<User> findById(Long id)`
   - `Optional<User> findByEmail(String email)`

2. `User` class:
   - `Optional<String> getMiddleName()`
   - `Optional<Address> getAddress()`

3. `Address` class:
   - `Optional<String> getApartment()`

4. Chain operations to get user's apartment number or default:

```java
// Refactor this null-checking code:
String apartment = "N/A";
User user = repository.findById(1L);
if (user != null) {
    Address address = user.getAddress();
    if (address != null) {
        String apt = address.getApartment();
        if (apt != null) {
            apartment = apt;
        }
    }
}

// To use Optional fluently:
String apartment = repository.findById(1L)
    // ... chain Optional operations
    .orElse("N/A");
```

---

### Exercise 3.4: Stream API (1.5 hours)

Process employee data using Stream API.

**Setup:**
```java
record Employee(
    Long id,
    String name,
    String department,
    double salary,
    int yearsOfService,
    List<String> skills
) {}

List<Employee> employees = List.of(
    new Employee(1L, "Alice", "Engineering", 85000, 5, List.of("Java", "Python")),
    new Employee(2L, "Bob", "Engineering", 75000, 3, List.of("Java", "JavaScript")),
    new Employee(3L, "Charlie", "Sales", 65000, 7, List.of("Communication", "CRM")),
    new Employee(4L, "Diana", "Engineering", 95000, 8, List.of("Java", "Kotlin", "Go")),
    new Employee(5L, "Eve", "HR", 55000, 2, List.of("Recruiting", "Communication")),
    new Employee(6L, "Frank", "Sales", 70000, 4, List.of("Negotiation", "CRM"))
);
```

**Tasks:**

1. **Filter & Sort:** Get engineering employees sorted by salary (descending)

2. **Map & Collect:** Get list of all employee names in uppercase

3. **Grouping:** Group employees by department

4. **Statistics:**
   - Total salary expense
   - Average salary by department
   - Highest paid employee

5. **FlatMap:** Get all unique skills across all employees

6. **Partitioning:** Partition employees by salary > 70000

7. **Reduce:** Calculate total years of service

8. **Complex:** Find departments where average salary > 70000

```java
// Expected outputs:
// 1. [Diana, Alice, Bob]
// 2. [ALICE, BOB, CHARLIE, DIANA, EVE, FRANK]
// 3. {Engineering=[Alice, Bob, Diana], Sales=[Charlie, Frank], HR=[Eve]}
// 4. Total: $445000, Avg by dept: {Engineering=85000, Sales=67500, HR=55000}
// 5. [Java, Python, JavaScript, Kotlin, Go, Communication, CRM, Recruiting, Negotiation]
// 6. {true=[Alice, Bob, Diana], false=[Charlie, Eve, Frank]}
// 7. 29 years
// 8. [Engineering]
```

---

## Day 4: I/O + Concurrency

### Exercise 4.1: File I/O with NIO (1 hour)

Build a log file analyzer.

**Requirements:**

1. Read a log file with entries like:
   ```
   2024-01-15 10:30:45 INFO User login: alice@email.com
   2024-01-15 10:31:02 ERROR Database connection failed
   2024-01-15 10:32:15 WARN High memory usage: 85%
   ```

2. `LogEntry` record:
   - timestamp, level (INFO/WARN/ERROR), message

3. `LogAnalyzer` class:
   - `List<LogEntry> readLogs(Path file)` - parse log file
   - `void writeSummary(Path output, List<LogEntry> logs)` - write summary
   - `Map<String, Long> countByLevel(List<LogEntry> logs)`
   - `List<LogEntry> getErrors(List<LogEntry> logs)`

4. Use NIO (Files, Paths) for all operations

```java
// Expected usage:
LogAnalyzer analyzer = new LogAnalyzer();

List<LogEntry> logs = analyzer.readLogs(Path.of("app.log"));
Map<String, Long> counts = analyzer.countByLevel(logs);
// {INFO=150, WARN=23, ERROR=5}

List<LogEntry> errors = analyzer.getErrors(logs);
analyzer.writeSummary(Path.of("summary.txt"), logs);
```

---

### Exercise 4.2: Serialization (45 min)

Create a configuration system with serialization.

**Requirements:**

1. `AppConfig` class (Serializable):
   - Fields: appName, version, maxConnections, timeout
   - transient field: `lastLoaded` (LocalDateTime)
   - Custom `readObject()` to set lastLoaded

2. `ConfigManager`:
   - `void saveConfig(AppConfig config, Path file)`
   - `AppConfig loadConfig(Path file)`
   - Handle all exceptions appropriately

3. `DatabaseConfig` (Externalizable):
   - Fields: host, port, username, password
   - Encrypt password in `writeExternal()`
   - Decrypt in `readExternal()`

```java
// Expected usage:
AppConfig config = new AppConfig("MyApp", "1.0", 100, 30000);
ConfigManager.saveConfig(config, Path.of("config.ser"));

AppConfig loaded = ConfigManager.loadConfig(Path.of("config.ser"));
System.out.println(loaded.getLastLoaded());  // Current time (set during deserialization)
```

---

### Exercise 4.3: Multithreading Basics (1 hour)

Build a concurrent download manager.

**Requirements:**

1. `DownloadTask` implements Runnable:
   - Simulates file download (Thread.sleep)
   - Reports progress (0%, 25%, 50%, 75%, 100%)
   - Thread-safe progress tracking

2. `DownloadManager`:
   - Uses ExecutorService with fixed thread pool (3 threads)
   - `Future<String> download(String url)` - returns file path
   - `void downloadAll(List<String> urls)` - download multiple
   - Track total downloaded count (thread-safe)

3. Handle:
   - Thread interruption
   - Timeout (30 seconds max per download)
   - Proper shutdown

```java
// Expected usage:
DownloadManager manager = new DownloadManager(3);

List<String> urls = List.of(
    "http://example.com/file1.zip",
    "http://example.com/file2.zip",
    "http://example.com/file3.zip",
    "http://example.com/file4.zip",
    "http://example.com/file5.zip"
);

manager.downloadAll(urls);
// Output:
// [Thread-1] Downloading file1.zip... 0%
// [Thread-2] Downloading file2.zip... 0%
// [Thread-3] Downloading file3.zip... 0%
// [Thread-1] Downloading file1.zip... 25%
// ... etc
// Downloaded 5 files total
```

---

### Exercise 4.4: CompletableFuture (1 hour)

Build an async e-commerce order processor.

**Requirements:**

1. Async services (simulate with delays):
   - `UserService.getUser(Long id)` - 500ms
   - `ProductService.getProduct(Long id)` - 300ms
   - `InventoryService.checkStock(Long productId)` - 200ms
   - `PaymentService.processPayment(Order order)` - 1000ms
   - `NotificationService.sendConfirmation(Order order)` - 400ms

2. `OrderProcessor`:
   - Fetch user and product in parallel
   - Then check stock
   - If in stock, process payment
   - After payment, send notification
   - Handle any failures with fallback

3. Use:
   - `supplyAsync` for async operations
   - `thenCombine` to combine user and product
   - `thenCompose` for dependent operations
   - `exceptionally` for error handling
   - `allOf` for parallel operations

```java
// Expected usage:
OrderProcessor processor = new OrderProcessor();

CompletableFuture<OrderResult> future = processor.processOrder(1L, 100L, 2);

future.thenAccept(result -> {
    if (result.isSuccess()) {
        System.out.println("Order completed: " + result.getOrderId());
    } else {
        System.out.println("Order failed: " + result.getError());
    }
});

// Should complete in ~2 seconds (not 2.4 seconds if sequential)
// Due to parallel fetching of user and product
```

---

### Exercise 4.5: Producer-Consumer with Virtual Threads (45 min)

Build a message processing system using virtual threads.

**Requirements:**

1. `Message` record: id, content, priority

2. `MessageQueue`:
   - Thread-safe queue (BlockingQueue)
   - `void publish(Message msg)`
   - `Message consume()`

3. `MessageProcessor`:
   - Use virtual threads for consumers
   - Spawn 1000 virtual thread consumers
   - Each consumer processes messages and simulates work

4. `MessageProducer`:
   - Produces 10,000 messages
   - Use virtual threads for production

5. Compare performance:
   - Platform threads (100 consumers)
   - Virtual threads (1000 consumers)

```java
// Expected usage:
MessageQueue queue = new MessageQueue();

// Start consumers (virtual threads)
try (var executor = Executors.newVirtualThreadPerTaskExecutor()) {
    for (int i = 0; i < 1000; i++) {
        executor.submit(new MessageConsumer(queue));
    }

    // Produce messages
    MessageProducer producer = new MessageProducer(queue);
    producer.produceMessages(10000);
}

// Output:
// Produced 10000 messages
// Processed 10000 messages in 2.5 seconds
// Average processing time: 0.25ms per message
```

---

## Bonus Challenges

### Challenge 1: Mini ORM

Build a simple ORM using annotations and reflection:
- `@Entity`, `@Table`, `@Column`, `@Id` annotations
- `EntityManager` that can save/find objects
- Generate SQL from annotated classes

### Challenge 2: Event System

Build a pub-sub event system:
- Generic `Event<T>` class
- `EventBus` for publishing/subscribing
- Use functional interfaces for handlers
- Support async event handling

### Challenge 3: Caching System

Build a thread-safe cache:
- Generic `Cache<K, V>` interface
- `LRUCache` implementation with size limit
- TTL (time-to-live) support
- Use ConcurrentHashMap and locks

---

## Solutions

Solutions for each exercise are available in the `solutions/` directory.

---

## Tips for Practice

1. **Type code, don't copy-paste** - muscle memory matters
2. **Run and debug** - understand errors
3. **Experiment** - modify examples, see what happens
4. **Time yourself** - track improvement
5. **Review** - revisit topics you struggled with
