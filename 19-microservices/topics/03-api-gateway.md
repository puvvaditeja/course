# API Gateway

## Spring Cloud Gateway

### What is an API Gateway?

An API Gateway is a server that acts as a single entry point for all clients. It sits between clients and microservices, routing requests to appropriate services and handling cross-cutting concerns.

### Why Use an API Gateway?

**Without API Gateway:**
```
Client → Service A (auth, logging, rate limiting)
      → Service B (auth, logging, rate limiting)
      → Service C (auth, logging, rate limiting)
      → Service D (auth, logging, rate limiting)
```

**With API Gateway:**
```
Client → API Gateway (auth, logging, rate limiting, routing)
              ├→ Service A
              ├→ Service B
              ├→ Service C
              └→ Service D
```

### Benefits

1. **Single Entry Point**: Clients interact with one endpoint
2. **Cross-Cutting Concerns**: Authentication, logging, rate limiting in one place
3. **Protocol Translation**: REST to gRPC, HTTP to WebSocket, etc.
4. **Service Aggregation**: Combine multiple service calls
5. **Load Balancing**: Distribute traffic across instances
6. **Security**: Centralized authentication and authorization
7. **Monitoring**: Centralized logging and metrics

### Spring Cloud Gateway Architecture

```
┌──────────────────────────────────────────────┐
│           Spring Cloud Gateway               │
│                                              │
│  ┌────────────────────────────────────────┐ │
│  │  Route Definition                      │ │
│  │  - Predicates (when to route)         │ │
│  │  - Filters (modify request/response)  │ │
│  │  - URI (where to route)               │ │
│  └────────────────────────────────────────┘ │
│                                              │
│  Request → Predicates → Filters → Service   │
└──────────────────────────────────────────────┘
```

### Core Concepts

#### 1. Route
Basic building block of the gateway, consisting of:
- **ID**: Unique identifier
- **Destination URI**: Where to route the request
- **Predicates**: Conditions to match
- **Filters**: Modifications to request/response

#### 2. Predicate
Java 8 Predicate that matches HTTP request attributes (headers, parameters, paths, etc.)

#### 3. Filter
Modify requests and responses before or after sending to downstream service

### Setting Up Spring Cloud Gateway

#### Maven Dependencies

```xml
<dependencies>
    <!-- Spring Cloud Gateway -->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-gateway</artifactId>
    </dependency>

    <!-- Eureka Client for Service Discovery -->
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>

    <!-- Do NOT include spring-boot-starter-web -->
    <!-- Gateway uses WebFlux (reactive) -->
</dependencies>
```

#### Application Class

```java
package com.example.gateway;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;

@SpringBootApplication
@EnableDiscoveryClient
public class ApiGatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(ApiGatewayApplication.class, args);
    }
}
```

#### Basic Configuration

```yaml
spring:
  application:
    name: api-gateway
  cloud:
    gateway:
      discovery:
        locator:
          enabled: true  # Enable service discovery routing
          lower-case-service-id: true

server:
  port: 8080

eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/
```

---

## Routing

### Route Configuration Methods

#### 1. YAML Configuration

```yaml
spring:
  cloud:
    gateway:
      routes:
        # Order Service Route
        - id: order-service
          uri: lb://order-service  # lb:// for load-balanced
          predicates:
            - Path=/api/orders/**
          filters:
            - RewritePath=/api/orders/(?<segment>.*), /$\{segment}

        # Payment Service Route
        - id: payment-service
          uri: lb://payment-service
          predicates:
            - Path=/api/payments/**
          filters:
            - RewritePath=/api/payments/(?<segment>.*), /$\{segment}

        # Product Service Route
        - id: product-service
          uri: lb://product-service
          predicates:
            - Path=/api/products/**
            - Method=GET,POST
          filters:
            - AddRequestHeader=X-Request-Source, API-Gateway
```

#### 2. Java Configuration

```java
import org.springframework.cloud.gateway.route.RouteLocator;
import org.springframework.cloud.gateway.route.builder.RouteLocatorBuilder;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class GatewayConfig {

    @Bean
    public RouteLocator customRouteLocator(RouteLocatorBuilder builder) {
        return builder.routes()
            // Order Service
            .route("order-service", r -> r
                .path("/api/orders/**")
                .filters(f -> f
                    .rewritePath("/api/orders/(?<segment>.*)", "/${segment}")
                    .addRequestHeader("X-Request-Source", "API-Gateway")
                )
                .uri("lb://order-service")
            )
            // Payment Service
            .route("payment-service", r -> r
                .path("/api/payments/**")
                .and()
                .method("POST", "GET")
                .filters(f -> f
                    .rewritePath("/api/payments/(?<segment>.*)", "/${segment}")
                )
                .uri("lb://payment-service")
            )
            // Product Service with Circuit Breaker
            .route("product-service", r -> r
                .path("/api/products/**")
                .filters(f -> f
                    .circuitBreaker(config -> config
                        .setName("productServiceCircuitBreaker")
                        .setFallbackUri("forward:/fallback/products")
                    )
                )
                .uri("lb://product-service")
            )
            .build();
    }
}
```

### Route Predicates

#### Built-in Predicates

**1. Path Predicate:**
```yaml
predicates:
  - Path=/api/orders/**  # Match path pattern
```

**2. Method Predicate:**
```yaml
predicates:
  - Method=GET,POST  # Match HTTP methods
```

**3. Header Predicate:**
```yaml
predicates:
  - Header=X-Request-Id, \d+  # Header exists with regex match
```

**4. Query Parameter Predicate:**
```yaml
predicates:
  - Query=premium, true  # Query param exists
```

**5. Host Predicate:**
```yaml
predicates:
  - Host=**.example.com  # Match hostname
```

**6. Cookie Predicate:**
```yaml
predicates:
  - Cookie=session, abc.*  # Cookie exists with value pattern
```

**7. Time-based Predicates:**
```yaml
predicates:
  - After=2024-01-01T00:00:00-05:00[America/New_York]
  - Before=2024-12-31T23:59:59-05:00[America/New_York]
  - Between=2024-01-01T00:00:00Z, 2024-12-31T23:59:59Z
```

**8. RemoteAddr Predicate:**
```yaml
predicates:
  - RemoteAddr=192.168.1.1/24  # IP address range
```

**9. Weight Predicate (A/B Testing):**
```yaml
routes:
  - id: product-service-v1
    uri: lb://product-service-v1
    predicates:
      - Path=/api/products/**
      - Weight=group1, 8  # 80% traffic

  - id: product-service-v2
    uri: lb://product-service-v2
    predicates:
      - Path=/api/products/**
      - Weight=group1, 2  # 20% traffic
```

#### Custom Predicates

```java
import org.springframework.cloud.gateway.handler.predicate.AbstractRoutePredicateFactory;
import org.springframework.stereotype.Component;
import org.springframework.web.server.ServerWebExchange;
import java.util.function.Predicate;

@Component
public class CustomHeaderRoutePredicateFactory
        extends AbstractRoutePredicateFactory<CustomHeaderRoutePredicateFactory.Config> {

    public CustomHeaderRoutePredicateFactory() {
        super(Config.class);
    }

    @Override
    public Predicate<ServerWebExchange> apply(Config config) {
        return exchange -> {
            String headerValue = exchange.getRequest()
                .getHeaders()
                .getFirst(config.getHeaderName());
            return config.getHeaderValue().equals(headerValue);
        };
    }

    public static class Config {
        private String headerName;
        private String headerValue;

        // Getters and setters
        public String getHeaderName() { return headerName; }
        public void setHeaderName(String headerName) { this.headerName = headerName; }
        public String getHeaderValue() { return headerValue; }
        public void setHeaderValue(String headerValue) { this.headerValue = headerValue; }
    }
}
```

**Usage:**
```yaml
predicates:
  - CustomHeader=X-Custom-Header, CustomValue
```

---

## Filters

### Gateway Filter Types

#### 1. Pre-Filters
Execute before routing to downstream service.

#### 2. Post-Filters
Execute after receiving response from downstream service.

### Built-in Gateway Filters

#### Request Modification Filters

**1. AddRequestHeader:**
```yaml
filters:
  - AddRequestHeader=X-Request-Source, API-Gateway
```

**2. AddRequestParameter:**
```yaml
filters:
  - AddRequestParameter=source, gateway
```

**3. RewritePath:**
```yaml
filters:
  - RewritePath=/api/orders/(?<segment>.*), /${segment}
  # /api/orders/123 → /123
```

**4. SetPath:**
```yaml
filters:
  - SetPath=/orders/{segment}
```

**5. PrefixPath:**
```yaml
filters:
  - PrefixPath=/v1
  # /orders → /v1/orders
```

**6. StripPrefix:**
```yaml
filters:
  - StripPrefix=1
  # /api/orders/123 → /orders/123
```

**7. SetRequestHeader:**
```yaml
filters:
  - SetRequestHeader=X-Request-Type, API
```

**8. RemoveRequestHeader:**
```yaml
filters:
  - RemoveRequestHeader=X-Internal-Header
```

#### Response Modification Filters

**1. AddResponseHeader:**
```yaml
filters:
  - AddResponseHeader=X-Response-Source, API-Gateway
```

**2. SetResponseHeader:**
```yaml
filters:
  - SetResponseHeader=X-Response-Time, ${timestamp}
```

**3. RemoveResponseHeader:**
```yaml
filters:
  - RemoveResponseHeader=X-Internal-Info
```

**4. RewriteResponseHeader:**
```yaml
filters:
  - RewriteResponseHeader=X-Response-Location, , /api
```

**5. DedupeResponseHeader:**
```yaml
filters:
  - DedupeResponseHeader=Access-Control-Allow-Origin
```

#### Redirect and Retry Filters

**1. RedirectTo:**
```yaml
filters:
  - RedirectTo=302, https://example.com
```

**2. Retry:**
```yaml
filters:
  - name: Retry
    args:
      retries: 3
      statuses: BAD_GATEWAY,GATEWAY_TIMEOUT
      methods: GET,POST
      backoff:
        firstBackoff: 10ms
        maxBackoff: 50ms
        factor: 2
        basedOnPreviousValue: false
```

#### Circuit Breaker Filter

```yaml
filters:
  - name: CircuitBreaker
    args:
      name: productServiceCircuitBreaker
      fallbackUri: forward:/fallback/products
```

#### Request/Response Size Filters

**1. RequestSize:**
```yaml
filters:
  - name: RequestSize
    args:
      maxSize: 5MB
```

#### Hystrix/Resilience4j Filter

```yaml
filters:
  - name: CircuitBreaker
    args:
      name: myCircuitBreaker
      fallbackUri: forward:/fallback
```

### Custom Gateway Filters

#### Global Filter (applies to all routes)

```java
import org.springframework.cloud.gateway.filter.GatewayFilterChain;
import org.springframework.cloud.gateway.filter.GlobalFilter;
import org.springframework.core.Ordered;
import org.springframework.stereotype.Component;
import org.springframework.web.server.ServerWebExchange;
import reactor.core.publisher.Mono;

@Component
public class LoggingFilter implements GlobalFilter, Ordered {

    private static final Logger logger = LoggerFactory.getLogger(LoggingFilter.class);

    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        logger.info("Request Path: {}", exchange.getRequest().getPath());
        logger.info("Request Method: {}", exchange.getRequest().getMethod());

        return chain.filter(exchange).then(Mono.fromRunnable(() -> {
            logger.info("Response Status: {}", exchange.getResponse().getStatusCode());
        }));
    }

    @Override
    public int getOrder() {
        return -1;  // Higher priority
    }
}
```

#### Gateway Filter Factory (configurable per route)

```java
import org.springframework.cloud.gateway.filter.GatewayFilter;
import org.springframework.cloud.gateway.filter.factory.AbstractGatewayFilterFactory;
import org.springframework.stereotype.Component;

@Component
public class CustomGatewayFilterFactory
        extends AbstractGatewayFilterFactory<CustomGatewayFilterFactory.Config> {

    public CustomGatewayFilterFactory() {
        super(Config.class);
    }

    @Override
    public GatewayFilter apply(Config config) {
        return (exchange, chain) -> {
            // Pre-processing
            if (config.isPreLogger()) {
                System.out.println("Pre GatewayFilter: " + config.getMessage());
            }

            return chain.filter(exchange).then(Mono.fromRunnable(() -> {
                // Post-processing
                if (config.isPostLogger()) {
                    System.out.println("Post GatewayFilter: " + config.getMessage());
                }
            }));
        };
    }

    public static class Config {
        private String message;
        private boolean preLogger;
        private boolean postLogger;

        // Getters and setters
        public String getMessage() { return message; }
        public void setMessage(String message) { this.message = message; }
        public boolean isPreLogger() { return preLogger; }
        public void setPreLogger(boolean preLogger) { this.preLogger = preLogger; }
        public boolean isPostLogger() { return postLogger; }
        public void setPostLogger(boolean postLogger) { this.postLogger = postLogger; }
    }
}
```

**Usage:**
```yaml
filters:
  - name: Custom
    args:
      message: "Custom Filter Message"
      preLogger: true
      postLogger: true
```

---

## Rate Limiting

### Why Rate Limiting?

1. **Prevent Abuse**: Protect against DDoS attacks
2. **Ensure Fair Usage**: Prevent single user from consuming all resources
3. **Cost Control**: Limit API calls to external services
4. **Quality of Service**: Maintain performance for all users

### Rate Limiting Strategies

#### 1. Fixed Window
```
Time Window: 1 minute
Limit: 100 requests

00:00-00:59 → 100 requests allowed
01:00-01:59 → 100 requests allowed (counter resets)
```

#### 2. Sliding Window
```
Time Window: 1 minute (rolling)
Limit: 100 requests

At 00:30 → Count requests from 23:30 to 00:30
At 00:31 → Count requests from 23:31 to 00:31
```

#### 3. Token Bucket
```
Bucket Capacity: 100 tokens
Refill Rate: 10 tokens/second

Request → Consume 1 token
If tokens available → Allow
If no tokens → Reject (429 Too Many Requests)
```

#### 4. Leaky Bucket
```
Bucket processes requests at constant rate
Incoming requests queue up
If queue full → Reject
```

### Spring Cloud Gateway Rate Limiting

#### Redis-based Rate Limiter

**1. Add Dependencies:**
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis-reactive</artifactId>
</dependency>
```

**2. Configure Redis:**
```yaml
spring:
  redis:
    host: localhost
    port: 6379
```

**3. Apply Rate Limiter Filter:**

**YAML Configuration:**
```yaml
spring:
  cloud:
    gateway:
      routes:
        - id: order-service
          uri: lb://order-service
          predicates:
            - Path=/api/orders/**
          filters:
            - name: RequestRateLimiter
              args:
                redis-rate-limiter:
                  replenishRate: 10  # Tokens per second
                  burstCapacity: 20  # Maximum tokens
                  requestedTokens: 1 # Tokens per request
                key-resolver: "#{@userKeyResolver}"
```

**4. Key Resolver Implementation:**

**By User:**
```java
import org.springframework.cloud.gateway.filter.ratelimit.KeyResolver;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import reactor.core.publisher.Mono;

@Configuration
public class RateLimitConfig {

    @Bean
    public KeyResolver userKeyResolver() {
        return exchange -> Mono.just(
            exchange.getRequest()
                .getHeaders()
                .getFirst("X-User-Id") // Rate limit per user
        );
    }
}
```

**By IP Address:**
```java
@Bean
public KeyResolver ipKeyResolver() {
    return exchange -> Mono.just(
        exchange.getRequest()
            .getRemoteAddress()
            .getAddress()
            .getHostAddress()
    );
}
```

**By API Key:**
```java
@Bean
public KeyResolver apiKeyResolver() {
    return exchange -> Mono.just(
        exchange.getRequest()
            .getHeaders()
            .getFirst("X-API-Key")
    );
}
```

**Combined Key Resolver:**
```java
@Bean
public KeyResolver combinedKeyResolver() {
    return exchange -> {
        String userId = exchange.getRequest()
            .getHeaders()
            .getFirst("X-User-Id");
        String path = exchange.getRequest()
            .getPath()
            .value();
        return Mono.just(userId + ":" + path);
    };
}
```

#### Custom Rate Limiter

```java
import org.springframework.cloud.gateway.filter.ratelimit.RateLimiter;
import reactor.core.publisher.Mono;

public class CustomRateLimiter implements RateLimiter<CustomRateLimiter.Config> {

    @Override
    public Mono<Response> isAllowed(String routeId, String id) {
        // Custom rate limiting logic
        boolean allowed = checkRateLimit(id);

        return Mono.just(new Response(allowed, getHeaders()));
    }

    private boolean checkRateLimit(String id) {
        // Implement custom logic
        // Check database, cache, etc.
        return true;
    }

    private Map<String, String> getHeaders() {
        Map<String, String> headers = new HashMap<>();
        headers.put("X-RateLimit-Remaining", "50");
        headers.put("X-RateLimit-Limit", "100");
        headers.put("X-RateLimit-Reset", "60");
        return headers;
    }

    @Override
    public Class<Config> getConfigClass() {
        return Config.class;
    }

    public static class Config {
        private int limit;
        private int duration;

        // Getters and setters
        public int getLimit() { return limit; }
        public void setLimit(int limit) { this.limit = limit; }
        public int getDuration() { return duration; }
        public void setDuration(int duration) { this.duration = duration; }
    }
}
```

### Rate Limit Response Handling

**Custom Response:**
```java
import org.springframework.cloud.gateway.filter.GatewayFilterChain;
import org.springframework.cloud.gateway.filter.GlobalFilter;
import org.springframework.core.Ordered;
import org.springframework.http.HttpStatus;
import org.springframework.stereotype.Component;
import org.springframework.web.server.ServerWebExchange;
import reactor.core.publisher.Mono;

@Component
public class RateLimitResponseFilter implements GlobalFilter, Ordered {

    @Override
    public Mono<Void> filter(ServerWebExchange exchange, GatewayFilterChain chain) {
        return chain.filter(exchange).onErrorResume(throwable -> {
            if (throwable instanceof RateLimitExceededException) {
                exchange.getResponse().setStatusCode(HttpStatus.TOO_MANY_REQUESTS);
                exchange.getResponse().getHeaders().add(
                    "X-RateLimit-Retry-After", "60"
                );
                return exchange.getResponse().setComplete();
            }
            return Mono.error(throwable);
        });
    }

    @Override
    public int getOrder() {
        return -1;
    }
}
```

### Rate Limiting Best Practices

1. **Choose Appropriate Limits**: Based on capacity and expected usage
2. **Use Multiple Tiers**: Different limits for different user types
3. **Provide Clear Responses**: Return 429 with retry information
4. **Monitor and Adjust**: Track rate limit hits and adjust as needed
5. **Cache Key Resolution**: Efficient key resolution for performance
6. **Graceful Degradation**: Don't fail completely on rate limit errors

**Example with Tiers:**
```yaml
routes:
  # Free tier users
  - id: free-tier
    uri: lb://api-service
    predicates:
      - Path=/api/**
      - Header=X-User-Tier, free
    filters:
      - name: RequestRateLimiter
        args:
          redis-rate-limiter.replenishRate: 10
          redis-rate-limiter.burstCapacity: 20

  # Premium tier users
  - id: premium-tier
    uri: lb://api-service
    predicates:
      - Path=/api/**
      - Header=X-User-Tier, premium
    filters:
      - name: RequestRateLimiter
        args:
          redis-rate-limiter.replenishRate: 100
          redis-rate-limiter.burstCapacity: 200
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| **API Gateway** | Single entry point for all client requests |
| **Routing** | Direct requests to appropriate microservices |
| **Predicates** | Conditions to match requests (path, method, headers, etc.) |
| **Filters** | Modify requests and responses (pre and post processing) |
| **Rate Limiting** | Control request frequency to prevent abuse |
| **Load Balancing** | Distribute traffic across service instances |

## Best Practices

1. **Keep Gateway Lightweight**: Don't add business logic
2. **Use Service Discovery**: Dynamic routing with Eureka
3. **Implement Rate Limiting**: Protect services from overload
4. **Add Circuit Breakers**: Handle service failures gracefully
5. **Centralize Cross-Cutting Concerns**: Auth, logging, rate limiting
6. **Monitor Gateway**: Track latency, errors, traffic patterns
7. **Version APIs**: Support multiple API versions
8. **Use Filters Wisely**: Balance between gateway and service responsibilities
9. **Configure Timeouts**: Prevent hanging requests
10. **Enable CORS**: Configure properly for web clients

## Complete Example

```yaml
spring:
  application:
    name: api-gateway
  cloud:
    gateway:
      default-filters:
        - DedupeResponseHeader=Access-Control-Allow-Origin
      routes:
        - id: order-service
          uri: lb://order-service
          predicates:
            - Path=/api/orders/**
            - Method=GET,POST,PUT,DELETE
          filters:
            - StripPrefix=1
            - name: CircuitBreaker
              args:
                name: orderServiceCircuitBreaker
                fallbackUri: forward:/fallback/orders
            - name: RequestRateLimiter
              args:
                redis-rate-limiter.replenishRate: 50
                redis-rate-limiter.burstCapacity: 100
                key-resolver: "#{@userKeyResolver}"
            - name: Retry
              args:
                retries: 3
                statuses: BAD_GATEWAY
  redis:
    host: localhost
    port: 6379

server:
  port: 8080

eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/
```

## Next Topic

Continue to [Resilience Patterns](./04-resilience-patterns.md) to learn about building fault-tolerant microservices with circuit breakers, retries, and fallback mechanisms.
