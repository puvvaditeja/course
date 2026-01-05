# I/O + Concurrency

## Learning Objectives
- Read and write files using NIO
- Implement serialization and deserialization
- Create multi-threaded applications
- Use CompletableFuture for async programming
- Leverage Virtual Threads (Java 21+)

---

## Exercise 4.1: File I/O with NIO (1 hour)

Build a log file analyzer.

### Requirements

1. **Read a log file** with entries like:
   ```
   2024-01-15 10:30:45 INFO User login: alice@email.com
   2024-01-15 10:31:02 ERROR Database connection failed
   2024-01-15 10:32:15 WARN High memory usage: 85%
   ```

2. **`LogEntry` record**:
   - timestamp, level (INFO/WARN/ERROR), message

3. **`LogAnalyzer` class**:
   - `List<LogEntry> readLogs(Path file)` - parse log file
   - `void writeSummary(Path output, List<LogEntry> logs)` - write summary
   - `Map<String, Long> countByLevel(List<LogEntry> logs)`
   - `List<LogEntry> getErrors(List<LogEntry> logs)`

4. **Use NIO** (Files, Paths) for all operations

### Expected Usage
```java
LogAnalyzer analyzer = new LogAnalyzer();

List<LogEntry> logs = analyzer.readLogs(Path.of("app.log"));
Map<String, Long> counts = analyzer.countByLevel(logs);
// {INFO=150, WARN=23, ERROR=5}

List<LogEntry> errors = analyzer.getErrors(logs);
analyzer.writeSummary(Path.of("summary.txt"), logs);
```

### Submission
Files in `io-concurrency/logs/`:
- `LogEntry.java`
- `LogAnalyzer.java`
- `app.log` (sample log file with at least 20 entries)
- `LogAnalyzerTest.java`

---

## Exercise 4.2: Serialization (45 min)

Create a configuration system with serialization.

### Requirements

1. **`AppConfig` class (Serializable)**:
   - Fields: appName, version, maxConnections, timeout
   - transient field: `lastLoaded` (LocalDateTime)
   - Custom `readObject()` to set lastLoaded on deserialization

2. **`ConfigManager`**:
   - `void saveConfig(AppConfig config, Path file)`
   - `AppConfig loadConfig(Path file)`
   - Handle all exceptions appropriately

3. **`DatabaseConfig` (Externalizable)**:
   - Fields: host, port, username, password
   - Encrypt password in `writeExternal()`
   - Decrypt in `readExternal()`

### Expected Usage
```java
AppConfig config = new AppConfig("MyApp", "1.0", 100, 30000);
ConfigManager.saveConfig(config, Path.of("config.ser"));

AppConfig loaded = ConfigManager.loadConfig(Path.of("config.ser"));
System.out.println(loaded.getLastLoaded());  // Current time (set during deserialization)
```

### Submission
Files in `io-concurrency/config/`:
- `AppConfig.java`
- `DatabaseConfig.java`
- `ConfigManager.java`
- `ConfigTest.java`

---

## Exercise 4.3: Multithreading Basics (1 hour)

Build a concurrent download manager.

### Requirements

1. **`DownloadTask` implements Runnable**:
   - Simulates file download (Thread.sleep)
   - Reports progress (0%, 25%, 50%, 75%, 100%)
   - Thread-safe progress tracking

2. **`DownloadManager`**:
   - Uses ExecutorService with fixed thread pool (3 threads)
   - `Future<String> download(String url)` - returns file path
   - `void downloadAll(List<String> urls)` - download multiple
   - Track total downloaded count (thread-safe)

3. **Handle**:
   - Thread interruption
   - Timeout (30 seconds max per download)
   - Proper shutdown

### Expected Usage
```java
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

### Submission
Files in `io-concurrency/download/`:
- `DownloadTask.java`
- `DownloadManager.java`
- `DownloadTest.java`

---

## Exercise 4.4: CompletableFuture (1 hour)

Build an async e-commerce order processor.

### Requirements

1. **Async services** (simulate with delays):
   - `UserService.getUser(Long id)` - 500ms
   - `ProductService.getProduct(Long id)` - 300ms
   - `InventoryService.checkStock(Long productId)` - 200ms
   - `PaymentService.processPayment(Order order)` - 1000ms
   - `NotificationService.sendConfirmation(Order order)` - 400ms

2. **`OrderProcessor`**:
   - Fetch user and product in parallel
   - Then check stock
   - If in stock, process payment
   - After payment, send notification
   - Handle any failures with fallback

3. **Use**:
   - `supplyAsync` for async operations
   - `thenCombine` to combine user and product
   - `thenCompose` for dependent operations
   - `exceptionally` for error handling
   - `allOf` for parallel operations

### Expected Usage
```java
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

### Submission
Files in `io-concurrency/order/`:
- `User.java`, `Product.java`, `Order.java`, `OrderResult.java`
- `UserService.java`, `ProductService.java`, `InventoryService.java`
- `PaymentService.java`, `NotificationService.java`
- `OrderProcessor.java`
- `OrderTest.java`

---

## Exercise 4.5: Producer-Consumer with Virtual Threads (45 min)

Build a message processing system using virtual threads.

### Requirements

1. **`Message` record**: id, content, priority

2. **`MessageQueue`**:
   - Thread-safe queue (BlockingQueue)
   - `void publish(Message msg)`
   - `Message consume()`

3. **`MessageProcessor`**:
   - Use virtual threads for consumers
   - Spawn 1000 virtual thread consumers
   - Each consumer processes messages and simulates work

4. **`MessageProducer`**:
   - Produces 10,000 messages
   - Use virtual threads for production

5. **Compare performance**:
   - Platform threads (100 consumers)
   - Virtual threads (1000 consumers)

### Expected Usage
```java
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

### Submission
Files in `io-concurrency/messaging/`:
- `Message.java`
- `MessageQueue.java`
- `MessageConsumer.java`
- `MessageProducer.java`
- `MessageProcessor.java`
- `MessagingTest.java`

---

## Submission Checklist

| Exercise | File(s) | Status |
|----------|---------|--------|
| 4.1 File I/O with NIO | `logs/*.java` | ⬜ |
| 4.2 Serialization | `config/*.java` | ⬜ |
| 4.3 Multithreading Basics | `download/*.java` | ⬜ |
| 4.4 CompletableFuture | `order/*.java` | ⬜ |
| 4.5 Virtual Threads | `messaging/*.java` | ⬜ |

## Evaluation Criteria

- NIO used correctly (Files, Paths, BufferedReader/Writer)
- Serialization handles transient fields properly
- Externalizable encrypts sensitive data
- Thread safety ensured (synchronized, AtomicInteger, etc.)
- ExecutorService properly shutdown
- CompletableFuture chain is correct and efficient
- Virtual threads used appropriately
- Code compiles and runs without errors
- Performance comparison documented (for Exercise 4.5)
