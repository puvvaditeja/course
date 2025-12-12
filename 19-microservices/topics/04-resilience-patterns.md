# Resilience Patterns

## Introduction to Resilience

In a distributed microservices architecture, failures are inevitable. Services may be temporarily unavailable due to network issues, high load, deployment, or crashes. Resilience patterns help systems gracefully handle these failures and maintain acceptable service levels.

### Why Resilience Matters

**Cascading Failures:**
```
Service A (healthy) → Service B (slow) → Service C (down)
         ↓                  ↓
    Timeout            Backup requests         X
         ↓                  ↓
    Thread pool exhausted   System overload
         ↓
    Service A fails
```

**Resilience prevents:**
- Cascading failures
- Resource exhaustion
- Complete system outages
- Poor user experience

---

## Circuit Breaker Pattern with Resilience4j

### What is Circuit Breaker?

A circuit breaker prevents an application from repeatedly trying to execute an operation that's likely to fail, allowing it to continue without waiting for the fault to be fixed.

### Circuit Breaker States

```
┌─────────────┐
│   CLOSED    │ ←─────────────────────┐
│  (Normal)   │                       │
└──────┬──────┘                       │
       │                              │
       │ Failure threshold reached    │ Success threshold reached
       │                              │
       ▼                              │
┌─────────────┐                       │
│    OPEN     │                       │
│  (Failing)  │                       │
└──────┬──────┘                       │
       │                              │
       │ Wait duration elapsed        │
       │                              │
       ▼                              │
┌─────────────┐                       │
│ HALF_OPEN   │ ──────────────────────┘
│  (Testing)  │
└─────────────┘
```

**States:**
1. **CLOSED**: Normal operation, requests pass through
2. **OPEN**: Failures detected, requests fail immediately (no call to service)
3. **HALF_OPEN**: Test if service recovered, limited requests allowed

### Resilience4j Setup

#### Dependencies

```xml
<dependencies>
    <!-- Resilience4j with Spring Boot -->
    <dependency>
        <groupId>io.github.resilience4j</groupId>
        <artifactId>resilience4j-spring-boot3</artifactId>
        <version>2.1.0</version>
    </dependency>

    <!-- AOP for annotations -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-aop</artifactId>
    </dependency>

    <!-- Actuator for monitoring -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
</dependencies>
```

#### Configuration

```yaml
resilience4j:
  circuitbreaker:
    configs:
      default:
        registerHealthIndicator: true
        slidingWindowSize: 10  # Number of calls to record
        slidingWindowType: COUNT_BASED
        minimumNumberOfCalls: 5  # Min calls before calculating error rate
        permittedNumberOfCallsInHalfOpenState: 3
        automaticTransitionFromOpenToHalfOpenEnabled: true
        waitDurationInOpenState: 5s  # Time in OPEN state
        failureRateThreshold: 50  # Percentage of failures to open circuit
        eventConsumerBufferSize: 10
        recordExceptions:
          - java.io.IOException
          - java.util.concurrent.TimeoutException
        ignoreExceptions:
          - com.example.exceptions.BusinessException

    instances:
      paymentService:
        baseConfig: default
        waitDurationInOpenState: 10s
        failureRateThreshold: 60

      orderService:
        baseConfig: default
        slidingWindowSize: 20
        minimumNumberOfCalls: 10

# Actuator endpoints
management:
  endpoints:
    web:
      exposure:
        include: health,circuitbreakers,circuitbreakerevents
  health:
    circuitbreakers:
      enabled: true
  endpoint:
    health:
      show-details: always
```

### Using Circuit Breaker

#### Annotation-based Approach

```java
import io.github.resilience4j.circuitbreaker.annotation.CircuitBreaker;
import org.springframework.stereotype.Service;

@Service
public class OrderService {

    @Autowired
    private PaymentClient paymentClient;

    @CircuitBreaker(name = "paymentService", fallbackMethod = "paymentFallback")
    public PaymentResponse processPayment(PaymentRequest request) {
        // Call to potentially failing service
        return paymentClient.processPayment(request);
    }

    // Fallback method - same signature + Exception parameter
    public PaymentResponse paymentFallback(PaymentRequest request, Exception ex) {
        log.error("Payment service unavailable, using fallback", ex);

        return PaymentResponse.builder()
            .status("PENDING")
            .message("Payment will be processed later")
            .build();
    }

    // Can have multiple fallback levels
    public PaymentResponse paymentFallback(PaymentRequest request, TimeoutException ex) {
        log.error("Payment service timeout", ex);
        return PaymentResponse.builder()
            .status("TIMEOUT")
            .message("Payment service timeout, please retry")
            .build();
    }
}
```

#### Programmatic Approach

```java
import io.github.resilience4j.circuitbreaker.CircuitBreaker;
import io.github.resilience4j.circuitbreaker.CircuitBreakerRegistry;
import org.springframework.stereotype.Service;
import java.util.function.Supplier;

@Service
public class ProductService {

    private final CircuitBreaker circuitBreaker;
    private final InventoryClient inventoryClient;

    public ProductService(CircuitBreakerRegistry registry, InventoryClient inventoryClient) {
        this.circuitBreaker = registry.circuitBreaker("inventoryService");
        this.inventoryClient = inventoryClient;
    }

    public InventoryResponse checkInventory(Long productId) {
        Supplier<InventoryResponse> supplier = CircuitBreaker
            .decorateSupplier(circuitBreaker, () -> inventoryClient.checkStock(productId));

        try {
            return supplier.get();
        } catch (Exception ex) {
            return handleInventoryFailure(productId, ex);
        }
    }

    private InventoryResponse handleInventoryFailure(Long productId, Exception ex) {
        log.error("Inventory check failed for product: {}", productId, ex);
        return InventoryResponse.builder()
            .available(false)
            .message("Unable to check inventory")
            .build();
    }
}
```

#### Reactive Approach (WebFlux)

```java
import io.github.resilience4j.reactor.circuitbreaker.operator.CircuitBreakerOperator;
import reactor.core.publisher.Mono;

@Service
public class ReactiveOrderService {

    private final CircuitBreaker circuitBreaker;
    private final WebClient webClient;

    public ReactiveOrderService(CircuitBreakerRegistry registry, WebClient.Builder builder) {
        this.circuitBreaker = registry.circuitBreaker("paymentService");
        this.webClient = builder.build();
    }

    public Mono<PaymentResponse> processPayment(PaymentRequest request) {
        return webClient.post()
            .uri("http://payment-service/api/payments")
            .bodyValue(request)
            .retrieve()
            .bodyToMono(PaymentResponse.class)
            .transformDeferred(CircuitBreakerOperator.of(circuitBreaker))
            .onErrorResume(this::handlePaymentError);
    }

    private Mono<PaymentResponse> handlePaymentError(Throwable throwable) {
        return Mono.just(PaymentResponse.builder()
            .status("FAILED")
            .message("Payment processing unavailable")
            .build());
    }
}
```

### Circuit Breaker Events

```java
import io.github.resilience4j.circuitbreaker.CircuitBreaker;
import io.github.resilience4j.circuitbreaker.event.CircuitBreakerEvent;
import io.github.resilience4j.circuitbreaker.event.CircuitBreakerOnStateTransitionEvent;

@Component
public class CircuitBreakerEventListener {

    @PostConstruct
    public void init() {
        CircuitBreakerRegistry registry = CircuitBreakerRegistry.ofDefaults();
        CircuitBreaker circuitBreaker = registry.circuitBreaker("paymentService");

        circuitBreaker.getEventPublisher()
            .onEvent(event -> log.info("CircuitBreaker Event: {}", event));

        circuitBreaker.getEventPublisher()
            .onStateTransition(event ->
                log.warn("CircuitBreaker State Transition: {} -> {}",
                    event.getStateTransition().getFromState(),
                    event.getStateTransition().getToState())
            );

        circuitBreaker.getEventPublisher()
            .onError(event ->
                log.error("CircuitBreaker Error: {}", event.getThrowable().getMessage())
            );
    }
}
```

---

## Retry Mechanisms

### What is Retry Pattern?

Retry pattern automatically re-attempts failed operations, useful for transient failures (temporary network issues, service restarts, etc.).

### Retry Configuration

```yaml
resilience4j:
  retry:
    configs:
      default:
        maxAttempts: 3  # Maximum retry attempts
        waitDuration: 1s  # Wait time between retries
        enableExponentialBackoff: true
        exponentialBackoffMultiplier: 2  # 1s, 2s, 4s
        retryExceptions:
          - java.io.IOException
          - org.springframework.web.client.ResourceAccessException
        ignoreExceptions:
          - com.example.exceptions.BusinessException

    instances:
      paymentService:
        baseConfig: default
        maxAttempts: 5
        waitDuration: 500ms

      inventoryService:
        maxAttempts: 3
        waitDuration: 2s
        enableExponentialBackoff: false
```

### Using Retry

#### Annotation-based

```java
import io.github.resilience4j.retry.annotation.Retry;

@Service
public class PaymentService {

    @Retry(name = "paymentService", fallbackMethod = "paymentFallback")
    public PaymentResponse processPayment(PaymentRequest request) {
        log.info("Attempting payment processing...");
        return paymentClient.process(request);
    }

    public PaymentResponse paymentFallback(PaymentRequest request, Exception ex) {
        log.error("All retry attempts failed", ex);
        return PaymentResponse.failed("Service unavailable after retries");
    }
}
```

#### Programmatic

```java
import io.github.resilience4j.retry.Retry;
import io.github.resilience4j.retry.RetryRegistry;

@Service
public class InventoryService {

    private final Retry retry;

    public InventoryService(RetryRegistry registry) {
        this.retry = registry.retry("inventoryService");
    }

    public StockResponse checkStock(Long productId) {
        Supplier<StockResponse> supplier = Retry.decorateSupplier(
            retry,
            () -> inventoryClient.getStock(productId)
        );

        return supplier.get();
    }
}
```

#### With Event Listeners

```java
@Component
public class RetryEventListener {

    @Autowired
    private RetryRegistry retryRegistry;

    @PostConstruct
    public void registerEventListener() {
        Retry retry = retryRegistry.retry("paymentService");

        retry.getEventPublisher()
            .onRetry(event ->
                log.warn("Retry attempt {} for: {}",
                    event.getNumberOfRetryAttempts(),
                    event.getName())
            );

        retry.getEventPublisher()
            .onError(event ->
                log.error("All retries failed for: {}", event.getName())
            );

        retry.getEventPublisher()
            .onSuccess(event ->
                log.info("Retry succeeded after {} attempts",
                    event.getNumberOfRetryAttempts())
            );
    }
}
```

### Combining Circuit Breaker and Retry

```java
@Service
public class OrderService {

    // Retry first, then Circuit Breaker
    @Retry(name = "orderService")
    @CircuitBreaker(name = "orderService", fallbackMethod = "orderFallback")
    public OrderResponse createOrder(OrderRequest request) {
        return orderClient.create(request);
    }

    public OrderResponse orderFallback(OrderRequest request, Exception ex) {
        return OrderResponse.builder()
            .status("FAILED")
            .message("Unable to create order")
            .build();
    }
}
```

**Order matters:**
```
Request → Retry (tries 3 times) → Circuit Breaker → Service
```

---

## Fallback Methods

### What are Fallback Methods?

Fallback methods provide alternative responses when primary operations fail, ensuring graceful degradation.

### Fallback Strategies

#### 1. Default Response

```java
@CircuitBreaker(name = "productService", fallbackMethod = "getProductFallback")
public Product getProduct(Long id) {
    return productClient.getById(id);
}

public Product getProductFallback(Long id, Exception ex) {
    return Product.builder()
        .id(id)
        .name("Product Unavailable")
        .available(false)
        .build();
}
```

#### 2. Cached Response

```java
@Service
public class ProductService {

    private final Cache<Long, Product> productCache;

    @CircuitBreaker(name = "productService", fallbackMethod = "getCachedProduct")
    public Product getProduct(Long id) {
        Product product = productClient.getById(id);
        productCache.put(id, product);  // Update cache
        return product;
    }

    public Product getCachedProduct(Long id, Exception ex) {
        Product cached = productCache.getIfPresent(id);
        if (cached != null) {
            log.info("Returning cached product for id: {}", id);
            return cached;
        }
        return getDefaultProduct(id);
    }
}
```

#### 3. Alternative Service

```java
@CircuitBreaker(name = "primaryPayment", fallbackMethod = "useBackupPaymentService")
public PaymentResponse processPayment(PaymentRequest request) {
    return primaryPaymentClient.process(request);
}

public PaymentResponse useBackupPaymentService(PaymentRequest request, Exception ex) {
    log.warn("Using backup payment service");
    return backupPaymentClient.process(request);
}
```

#### 4. Degraded Response

```java
@CircuitBreaker(name = "recommendationService", fallbackMethod = "getDegradedRecommendations")
public List<Product> getRecommendations(Long userId) {
    return recommendationClient.getPersonalized(userId);
}

public List<Product> getDegradedRecommendations(Long userId, Exception ex) {
    // Return generic popular products instead of personalized
    return productService.getPopularProducts(10);
}
```

#### 5. Queue for Later Processing

```java
@CircuitBreaker(name = "emailService", fallbackMethod = "queueEmail")
public void sendEmail(EmailRequest request) {
    emailClient.send(request);
}

public void queueEmail(EmailRequest request, Exception ex) {
    log.warn("Email service unavailable, queuing for later");
    emailQueue.add(request);
    // Background job will process queue later
}
```

### Fallback Chaining

```java
@Service
public class OrderService {

    @CircuitBreaker(name = "orderService", fallbackMethod = "fallbackLevel1")
    public OrderResponse createOrder(OrderRequest request) {
        return orderClient.create(request);
    }

    // First fallback: Try alternative approach
    public OrderResponse fallbackLevel1(OrderRequest request, Exception ex) {
        log.warn("Primary order creation failed, trying alternative");
        return alternativeOrderClient.create(request);
    }

    // Second fallback: Cache or default
    public OrderResponse fallbackLevel1(OrderRequest request, RuntimeException ex) {
        log.error("Alternative also failed, using default");
        return OrderResponse.builder()
            .status("PENDING")
            .message("Order will be processed later")
            .build();
    }
}
```

---

## Bulkhead Pattern

### What is Bulkhead Pattern?

Bulkhead pattern isolates resources for different services to prevent resource exhaustion in one service from affecting others.

**Analogy:** Ship bulkheads prevent water from flooding entire ship if one section is breached.

### Bulkhead Types

#### 1. Semaphore-based Bulkhead
Limits concurrent calls using semaphores.

#### 2. Thread Pool Bulkhead
Dedicates a fixed thread pool for each service.

### Configuration

```yaml
resilience4j:
  bulkhead:
    configs:
      default:
        maxConcurrentCalls: 10  # Max concurrent calls
        maxWaitDuration: 1s     # Max time to wait for permission

    instances:
      paymentService:
        baseConfig: default
        maxConcurrentCalls: 5

      inventoryService:
        maxConcurrentCalls: 20
        maxWaitDuration: 2s

  thread-pool-bulkhead:
    configs:
      default:
        maxThreadPoolSize: 10
        coreThreadPoolSize: 5
        queueCapacity: 20
        keepAliveDuration: 20ms

    instances:
      orderService:
        baseConfig: default
        maxThreadPoolSize: 15
        queueCapacity: 50
```

### Using Bulkhead

#### Semaphore Bulkhead

```java
import io.github.resilience4j.bulkhead.annotation.Bulkhead;

@Service
public class PaymentService {

    @Bulkhead(name = "paymentService", fallbackMethod = "paymentBulkheadFallback")
    public PaymentResponse processPayment(PaymentRequest request) {
        return paymentClient.process(request);
    }

    public PaymentResponse paymentBulkheadFallback(PaymentRequest request,
                                                    BulkheadFullException ex) {
        log.warn("Payment service bulkhead full");
        return PaymentResponse.builder()
            .status("THROTTLED")
            .message("Too many concurrent requests, please retry later")
            .build();
    }
}
```

#### Thread Pool Bulkhead

```java
import io.github.resilience4j.bulkhead.annotation.Bulkhead;
import java.util.concurrent.CompletableFuture;

@Service
public class OrderService {

    @Bulkhead(name = "orderService", type = Bulkhead.Type.THREADPOOL,
              fallbackMethod = "orderBulkheadFallback")
    public CompletableFuture<OrderResponse> createOrder(OrderRequest request) {
        return CompletableFuture.supplyAsync(() -> orderClient.create(request));
    }

    public CompletableFuture<OrderResponse> orderBulkheadFallback(
            OrderRequest request, BulkheadFullException ex) {
        return CompletableFuture.completedFuture(
            OrderResponse.builder()
                .status("REJECTED")
                .message("Service capacity exceeded")
                .build()
        );
    }
}
```

#### Programmatic Approach

```java
import io.github.resilience4j.bulkhead.Bulkhead;
import io.github.resilience4j.bulkhead.BulkheadRegistry;

@Service
public class InventoryService {

    private final Bulkhead bulkhead;

    public InventoryService(BulkheadRegistry registry) {
        this.bulkhead = registry.bulkhead("inventoryService");
    }

    public StockResponse checkStock(Long productId) {
        Supplier<StockResponse> supplier = Bulkhead.decorateSupplier(
            bulkhead,
            () -> inventoryClient.getStock(productId)
        );

        try {
            return supplier.get();
        } catch (BulkheadFullException ex) {
            log.warn("Bulkhead full for inventory check");
            return StockResponse.unavailable();
        }
    }
}
```

### Combining All Resilience Patterns

```java
@Service
public class ComprehensiveService {

    // Order of execution: Retry → CircuitBreaker → Bulkhead → RateLimiter
    @Retry(name = "comprehensiveService")
    @CircuitBreaker(name = "comprehensiveService", fallbackMethod = "fallback")
    @Bulkhead(name = "comprehensiveService")
    @RateLimiter(name = "comprehensiveService")
    public ServiceResponse callService(ServiceRequest request) {
        return externalClient.call(request);
    }

    public ServiceResponse fallback(ServiceRequest request, Exception ex) {
        log.error("All resilience mechanisms triggered", ex);
        return ServiceResponse.builder()
            .status("DEGRADED")
            .message("Service temporarily unavailable")
            .build();
    }
}
```

### Rate Limiter (Bonus Pattern)

```yaml
resilience4j:
  ratelimiter:
    configs:
      default:
        limitForPeriod: 10      # Number of calls
        limitRefreshPeriod: 1s  # Time period
        timeoutDuration: 0s     # Wait time for permission

    instances:
      apiService:
        limitForPeriod: 100
        limitRefreshPeriod: 1s
```

```java
import io.github.resilience4j.ratelimiter.annotation.RateLimiter;

@Service
public class ApiService {

    @RateLimiter(name = "apiService", fallbackMethod = "rateLimitFallback")
    public ApiResponse callApi(ApiRequest request) {
        return apiClient.call(request);
    }

    public ApiResponse rateLimitFallback(ApiRequest request, RequestNotPermitted ex) {
        return ApiResponse.builder()
            .status("RATE_LIMITED")
            .message("Too many requests")
            .build();
    }
}
```

---

## Summary

| Pattern | Purpose | When to Use |
|---------|---------|-------------|
| **Circuit Breaker** | Prevent cascading failures | Service frequently fails |
| **Retry** | Handle transient failures | Network glitches, temporary unavailability |
| **Fallback** | Graceful degradation | Always (with other patterns) |
| **Bulkhead** | Isolate resources | Prevent resource exhaustion |
| **Rate Limiter** | Control request rate | Protect against overload |

## Best Practices

1. **Combine Patterns**: Use multiple patterns together for robust resilience
2. **Meaningful Fallbacks**: Provide useful degraded responses
3. **Monitor Everything**: Track circuit breaker states, retry attempts, etc.
4. **Configure Appropriately**: Tune based on actual failure patterns
5. **Test Failures**: Use chaos engineering to test resilience
6. **Log Events**: Log all resilience events for debugging
7. **Alert on Patterns**: Alert when circuits open or retry rates increase
8. **Cache When Possible**: Use caching for fallback responses
9. **Avoid Retry Storms**: Use exponential backoff
10. **Document Behavior**: Document how services degrade

## Monitoring Resilience

```yaml
# Actuator configuration
management:
  endpoints:
    web:
      exposure:
        include: health,metrics,circuitbreakers,circuitbreakerevents,retries,retryevents,bulkheads
  health:
    circuitbreakers:
      enabled: true
  metrics:
    export:
      prometheus:
        enabled: true
```

**Access Metrics:**
```
http://localhost:8080/actuator/circuitbreakers
http://localhost:8080/actuator/circuitbreakerevents
http://localhost:8080/actuator/retries
http://localhost:8080/actuator/bulkheads
http://localhost:8080/actuator/metrics
```

## Next Topic

Continue to [Inter-Service Communication](./05-inter-service-communication.md) to learn about different methods for microservices to communicate with each other.
