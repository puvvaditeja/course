# Security in Microservices

## Introduction to Microservices Security

Security in microservices is more complex than in monolithic applications due to distributed nature, multiple entry points, and inter-service communication.

### Security Challenges

1. **Multiple Entry Points**: Each service is a potential attack vector
2. **Service-to-Service Communication**: Internal APIs need protection
3. **Distributed Authentication**: Managing identity across services
4. **Data Protection**: Data in transit and at rest
5. **Network Security**: Multiple network boundaries

### Security Layers

```
┌─────────────────────────────────────────┐
│  Edge Security (API Gateway)            │ ← Rate limiting, SSL/TLS
├─────────────────────────────────────────┤
│  Authentication & Authorization         │ ← OAuth2, JWT
├─────────────────────────────────────────┤
│  Service-to-Service Security            │ ← mTLS, Service mesh
├─────────────────────────────────────────┤
│  Application Security                   │ ← Input validation, RBAC
├─────────────────────────────────────────┤
│  Data Security                          │ ← Encryption, key management
└─────────────────────────────────────────┘
```

---

## OAuth2

### What is OAuth2?

OAuth2 is an authorization framework that enables applications to obtain limited access to user accounts on an HTTP service. It works by delegating user authentication to the service that hosts the user account.

### OAuth2 Roles

1. **Resource Owner**: User who owns the data
2. **Client**: Application requesting access to resources
3. **Resource Server**: API server hosting protected resources
4. **Authorization Server**: Issues access tokens after authentication

### OAuth2 Flow

```
┌──────────────┐                                  ┌──────────────────┐
│   Client     │                                  │ Authorization    │
│ Application  │                                  │     Server       │
└──────┬───────┘                                  └────────┬─────────┘
       │                                                   │
       │ 1. Authorization Request                         │
       │──────────────────────────────────────────────────>│
       │                                                   │
       │ 2. Authorization Grant                           │
       │<──────────────────────────────────────────────────│
       │                                                   │
       │ 3. Access Token Request                          │
       │──────────────────────────────────────────────────>│
       │                                                   │
       │ 4. Access Token                                  │
       │<──────────────────────────────────────────────────│
       │                                                   │
       │                    ┌──────────────────┐          │
       │ 5. Access Resource │  Resource Server │          │
       │───────────────────>│                  │          │
       │                    └──────────────────┘          │
```

### OAuth2 Grant Types

#### 1. Authorization Code Grant (Most Common)

```
User → Login Page → Authorization Server → Authorization Code → Client
Client → Exchange Code for Token → Authorization Server → Access Token
Client → Use Token → Resource Server
```

#### 2. Client Credentials Grant (Service-to-Service)

```
Client → Client ID + Secret → Authorization Server → Access Token
```

#### 3. Password Grant (Legacy, Not Recommended)

```
User → Username + Password → Client → Authorization Server → Access Token
```

#### 4. Refresh Token Grant

```
Client → Refresh Token → Authorization Server → New Access Token
```

### Spring Security OAuth2 Setup

#### Authorization Server

**Dependencies:**
```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-security</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-oauth2-authorization-server</artifactId>
    </dependency>
</dependencies>
```

**Configuration:**
```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.annotation.Order;
import org.springframework.security.config.Customizer;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.oauth2.server.authorization.config.annotation.web.configuration.OAuth2AuthorizationServerConfiguration;
import org.springframework.security.web.SecurityFilterChain;

@Configuration
public class AuthorizationServerConfig {

    @Bean
    @Order(1)
    public SecurityFilterChain authorizationServerSecurityFilterChain(HttpSecurity http)
            throws Exception {
        OAuth2AuthorizationServerConfiguration.applyDefaultSecurity(http);
        return http.formLogin(Customizer.withDefaults()).build();
    }

    @Bean
    @Order(2)
    public SecurityFilterChain defaultSecurityFilterChain(HttpSecurity http)
            throws Exception {
        http
            .authorizeHttpRequests(authorize -> authorize
                .anyRequest().authenticated()
            )
            .formLogin(Customizer.withDefaults());
        return http.build();
    }

    @Bean
    public RegisteredClientRepository registeredClientRepository() {
        RegisteredClient registeredClient = RegisteredClient.withId(UUID.randomUUID().toString())
            .clientId("order-service")
            .clientSecret("{noop}secret")
            .clientAuthenticationMethod(ClientAuthenticationMethod.CLIENT_SECRET_BASIC)
            .authorizationGrantType(AuthorizationGrantType.AUTHORIZATION_CODE)
            .authorizationGrantType(AuthorizationGrantType.REFRESH_TOKEN)
            .authorizationGrantType(AuthorizationGrantType.CLIENT_CREDENTIALS)
            .redirectUri("http://localhost:8080/login/oauth2/code/order-service")
            .scope("read")
            .scope("write")
            .build();

        return new InMemoryRegisteredClientRepository(registeredClient);
    }

    @Bean
    public JWKSource<SecurityContext> jwkSource() {
        KeyPair keyPair = generateRsaKey();
        RSAPublicKey publicKey = (RSAPublicKey) keyPair.getPublic();
        RSAPrivateKey privateKey = (RSAPrivateKey) keyPair.getPrivate();
        RSAKey rsaKey = new RSAKey.Builder(publicKey)
            .privateKey(privateKey)
            .keyID(UUID.randomUUID().toString())
            .build();
        JWKSet jwkSet = new JWKSet(rsaKey);
        return new ImmutableJWKSet<>(jwkSet);
    }

    private static KeyPair generateRsaKey() {
        try {
            KeyPairGenerator keyPairGenerator = KeyPairGenerator.getInstance("RSA");
            keyPairGenerator.initialize(2048);
            return keyPairGenerator.generateKeyPair();
        } catch (Exception ex) {
            throw new IllegalStateException(ex);
        }
    }
}
```

**application.yml:**
```yaml
spring:
  application:
    name: authorization-server
server:
  port: 9000
```

---

## Resource Server

### What is Resource Server?

A resource server hosts protected resources and validates access tokens to authorize requests.

### Setup

**Dependencies:**
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-oauth2-resource-server</artifactId>
</dependency>
```

**Configuration:**
```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.web.SecurityFilterChain;

@Configuration
@EnableWebSecurity
public class ResourceServerConfig {

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests(authorize -> authorize
                .requestMatchers("/api/public/**").permitAll()
                .requestMatchers("/api/admin/**").hasRole("ADMIN")
                .requestMatchers("/api/orders/**").hasAnyRole("USER", "ADMIN")
                .anyRequest().authenticated()
            )
            .oauth2ResourceServer(oauth2 -> oauth2
                .jwt(Customizer.withDefaults())
            );
        return http.build();
    }
}
```

**application.yml:**
```yaml
spring:
  application:
    name: order-service
  security:
    oauth2:
      resourceserver:
        jwt:
          issuer-uri: http://localhost:9000
          # Or use jwk-set-uri directly
          # jwk-set-uri: http://localhost:9000/oauth2/jwks

server:
  port: 8081
```

### Accessing Token Information

```java
import org.springframework.security.core.annotation.AuthenticationPrincipal;
import org.springframework.security.oauth2.jwt.Jwt;

@RestController
@RequestMapping("/api/orders")
public class OrderController {

    @GetMapping
    public List<Order> getOrders(@AuthenticationPrincipal Jwt jwt) {
        String username = jwt.getSubject();
        String userId = jwt.getClaim("user_id");
        List<String> roles = jwt.getClaim("roles");

        log.info("User: {}, Roles: {}", username, roles);

        return orderService.getOrdersByUser(userId);
    }

    @PostMapping
    @PreAuthorize("hasRole('USER')")
    public Order createOrder(@RequestBody OrderRequest request,
                            @AuthenticationPrincipal Jwt jwt) {
        String userId = jwt.getSubject();
        return orderService.createOrder(userId, request);
    }
}
```

### Method-Level Security

```java
import org.springframework.security.access.prepost.PreAuthorize;
import org.springframework.security.access.prepost.PostAuthorize;

@Service
public class OrderService {

    @PreAuthorize("hasRole('USER')")
    public Order createOrder(OrderRequest request) {
        return orderRepository.save(new Order(request));
    }

    @PreAuthorize("hasRole('ADMIN') or #userId == authentication.principal.subject")
    public List<Order> getOrdersByUser(String userId) {
        return orderRepository.findByUserId(userId);
    }

    @PostAuthorize("returnObject.userId == authentication.principal.subject or hasRole('ADMIN')")
    public Order getOrder(Long orderId) {
        return orderRepository.findById(orderId)
            .orElseThrow(() -> new OrderNotFoundException(orderId));
    }

    @PreAuthorize("hasRole('ADMIN')")
    public void deleteOrder(Long orderId) {
        orderRepository.deleteById(orderId);
    }
}
```

**Enable Method Security:**
```java
@Configuration
@EnableMethodSecurity(prePostEnabled = true)
public class MethodSecurityConfig {
}
```

### Service-to-Service Communication with OAuth2

**Client Configuration:**
```yaml
spring:
  security:
    oauth2:
      client:
        registration:
          payment-service:
            client-id: order-service
            client-secret: secret
            authorization-grant-type: client_credentials
            scope: read,write
        provider:
          payment-service:
            token-uri: http://localhost:9000/oauth2/token
```

**Using WebClient with OAuth2:**
```java
import org.springframework.security.oauth2.client.registration.ClientRegistrationRepository;
import org.springframework.security.oauth2.client.web.reactive.function.client.ServletOAuth2AuthorizedClientExchangeFilterFunction;
import org.springframework.web.reactive.function.client.WebClient;

@Configuration
public class WebClientConfig {

    @Bean
    public WebClient webClient(ClientRegistrationRepository clientRegistrationRepository) {
        ServletOAuth2AuthorizedClientExchangeFilterFunction oauth2 =
            new ServletOAuth2AuthorizedClientExchangeFilterFunction(
                clientRegistrationRepository,
                new InMemoryOAuth2AuthorizedClientService(clientRegistrationRepository)
            );

        oauth2.setDefaultClientRegistrationId("payment-service");

        return WebClient.builder()
            .apply(oauth2.oauth2Configuration())
            .build();
    }
}

@Service
public class PaymentService {

    private final WebClient webClient;

    public PaymentService(WebClient webClient) {
        this.webClient = webClient;
    }

    public PaymentResponse processPayment(PaymentRequest request) {
        return webClient.post()
            .uri("http://payment-service/api/payments")
            .bodyValue(request)
            .retrieve()
            .bodyToMono(PaymentResponse.class)
            .block();
    }
}
```

---

## Single Sign-On (SSO)

### What is SSO?

Single Sign-On allows users to log in once and access multiple applications without re-authenticating.

### SSO Flow

```
User → Login → Authorization Server → Token
User → App 1 (uses token) ✓
User → App 2 (uses same token) ✓
User → App 3 (uses same token) ✓
```

### Implementing SSO

**OAuth2 Client Configuration:**
```yaml
spring:
  security:
    oauth2:
      client:
        registration:
          keycloak:
            client-id: order-service
            client-secret: ${CLIENT_SECRET}
            scope: openid,profile,email
            authorization-grant-type: authorization_code
            redirect-uri: "{baseUrl}/login/oauth2/code/{registrationId}"
        provider:
          keycloak:
            issuer-uri: http://localhost:8080/realms/microservices
            user-name-attribute: preferred_username
```

**Security Configuration:**
```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests(authorize -> authorize
                .requestMatchers("/", "/public/**").permitAll()
                .anyRequest().authenticated()
            )
            .oauth2Login(oauth2 -> oauth2
                .defaultSuccessUrl("/home", true)
            )
            .logout(logout -> logout
                .logoutSuccessUrl("/")
                .invalidateHttpSession(true)
                .deleteCookies("JSESSIONID")
            );
        return http.build();
    }
}
```

**Controller:**
```java
@Controller
public class HomeController {

    @GetMapping("/home")
    public String home(@AuthenticationPrincipal OidcUser principal, Model model) {
        model.addAttribute("username", principal.getPreferredUsername());
        model.addAttribute("email", principal.getEmail());
        return "home";
    }
}
```

---

## Session Hijacking Prevention

### What is Session Hijacking?

Session hijacking is when an attacker steals a user's session ID to gain unauthorized access.

### Prevention Techniques

#### 1. HTTPS Only

**Force HTTPS:**
```java
@Configuration
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .requiresChannel(channel -> channel
                .anyRequest().requiresSecure()  // Force HTTPS
            );
        return http.build();
    }
}
```

```yaml
server:
  ssl:
    enabled: true
    key-store: classpath:keystore.p12
    key-store-password: ${KEYSTORE_PASSWORD}
    key-store-type: PKCS12
```

#### 2. Secure Cookies

```java
@Bean
public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
    http
        .sessionManagement(session -> session
            .sessionCreationPolicy(SessionCreationPolicy.IF_REQUIRED)
            .sessionFixation().newSession()  // Generate new session on login
        )
        .csrf(csrf -> csrf
            .csrfTokenRepository(CookieCsrfTokenRepository.withHttpOnlyFalse())
        );
    return http.build();
}
```

```yaml
server:
  servlet:
    session:
      cookie:
        secure: true      # Only send over HTTPS
        http-only: true   # Not accessible via JavaScript
        same-site: strict # Prevent CSRF
```

#### 3. Short Session Timeout

```yaml
server:
  servlet:
    session:
      timeout: 30m  # 30 minutes
```

```java
@Component
public class SessionListener implements HttpSessionListener {

    @Override
    public void sessionCreated(HttpSessionEvent event) {
        event.getSession().setMaxInactiveInterval(30 * 60); // 30 minutes
    }
}
```

#### 4. Session Validation

```java
@Component
public class SessionValidationFilter extends OncePerRequestFilter {

    @Override
    protected void doFilterInternal(HttpServletRequest request,
                                   HttpServletResponse response,
                                   FilterChain filterChain) throws ServletException, IOException {

        HttpSession session = request.getSession(false);

        if (session != null) {
            // Validate session IP
            String currentIp = request.getRemoteAddr();
            String sessionIp = (String) session.getAttribute("ip");

            if (sessionIp == null) {
                session.setAttribute("ip", currentIp);
            } else if (!sessionIp.equals(currentIp)) {
                session.invalidate();
                response.sendError(HttpServletResponse.SC_UNAUTHORIZED);
                return;
            }

            // Validate user agent
            String currentUserAgent = request.getHeader("User-Agent");
            String sessionUserAgent = (String) session.getAttribute("userAgent");

            if (sessionUserAgent == null) {
                session.setAttribute("userAgent", currentUserAgent);
            } else if (!sessionUserAgent.equals(currentUserAgent)) {
                session.invalidate();
                response.sendError(HttpServletResponse.SC_UNAUTHORIZED);
                return;
            }
        }

        filterChain.doFilter(request, response);
    }
}
```

#### 5. Token-Based Authentication (Stateless)

**Use JWT instead of sessions:**
```java
@Component
public class JwtAuthenticationFilter extends OncePerRequestFilter {

    @Autowired
    private JwtTokenProvider tokenProvider;

    @Override
    protected void doFilterInternal(HttpServletRequest request,
                                   HttpServletResponse response,
                                   FilterChain filterChain) throws ServletException, IOException {

        String token = getJwtFromRequest(request);

        if (token != null && tokenProvider.validateToken(token)) {
            String userId = tokenProvider.getUserIdFromToken(token);
            UsernamePasswordAuthenticationToken authentication =
                new UsernamePasswordAuthenticationToken(userId, null, authorities);

            SecurityContextHolder.getContext().setAuthentication(authentication);
        }

        filterChain.doFilter(request, response);
    }

    private String getJwtFromRequest(HttpServletRequest request) {
        String bearerToken = request.getHeader("Authorization");
        if (bearerToken != null && bearerToken.startsWith("Bearer ")) {
            return bearerToken.substring(7);
        }
        return null;
    }
}
```

#### 6. CSRF Protection

```java
@Configuration
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .csrf(csrf -> csrf
                .csrfTokenRepository(CookieCsrfTokenRepository.withHttpOnlyFalse())
                .requireCsrfProtectionMatcher(new AntPathRequestMatcher("/api/**"))
            );
        return http.build();
    }
}
```

#### 7. Logout Properly

```java
@RestController
public class AuthController {

    @PostMapping("/logout")
    public ResponseEntity<Void> logout(HttpServletRequest request) {
        HttpSession session = request.getSession(false);
        if (session != null) {
            session.invalidate();
        }
        SecurityContextHolder.clearContext();
        return ResponseEntity.ok().build();
    }
}
```

#### 8. Monitor Sessions

```java
@Component
public class SessionEventListener implements ApplicationListener<SessionCreatedEvent> {

    @Override
    public void onApplicationEvent(SessionCreatedEvent event) {
        HttpSession session = event.getSession();
        log.info("Session created: {}", session.getId());

        // Store in Redis for monitoring
        sessionMonitoringService.trackSession(session.getId());
    }
}

@Component
public class SessionDestroyedListener implements ApplicationListener<SessionDestroyedEvent> {

    @Override
    public void onApplicationEvent(SessionDestroyedEvent event) {
        String sessionId = event.getId();
        log.info("Session destroyed: {}", sessionId);

        sessionMonitoringService.removeSession(sessionId);
    }
}
```

---

## Additional Security Best Practices

### 1. API Security

```java
@Configuration
public class RateLimitingConfig {

    @Bean
    public KeyResolver userKeyResolver() {
        return exchange -> Mono.just(
            exchange.getRequest().getHeaders().getFirst("X-User-Id")
        );
    }
}
```

### 2. Input Validation

```java
@RestController
@Validated
public class OrderController {

    @PostMapping("/api/orders")
    public Order createOrder(@Valid @RequestBody OrderRequest request) {
        return orderService.createOrder(request);
    }
}

public class OrderRequest {

    @NotNull(message = "Product ID is required")
    private Long productId;

    @Min(value = 1, message = "Quantity must be at least 1")
    @Max(value = 100, message = "Quantity cannot exceed 100")
    private Integer quantity;

    @Pattern(regexp = "^[A-Z0-9._%+-]+@[A-Z0-9.-]+\\.[A-Z]{2,6}$",
             flags = Pattern.Flag.CASE_INSENSITIVE,
             message = "Invalid email format")
    private String email;
}
```

### 3. SQL Injection Prevention

```java
// Use JPA/Hibernate parameterized queries
@Repository
public interface OrderRepository extends JpaRepository<Order, Long> {

    // Safe - parameterized
    @Query("SELECT o FROM Order o WHERE o.userId = :userId")
    List<Order> findByUserId(@Param("userId") String userId);

    // Avoid - string concatenation (vulnerable)
    // @Query("SELECT o FROM Order o WHERE o.userId = '" + userId + "'")
}
```

### 4. Secrets Management

```yaml
# Don't hardcode secrets
spring:
  datasource:
    url: ${DATABASE_URL}
    username: ${DATABASE_USER}
    password: ${DATABASE_PASSWORD}
```

**Use environment variables or secret managers:**
```bash
export DATABASE_PASSWORD=$(aws secretsmanager get-secret-value \
  --secret-id db-password --query SecretString --output text)
```

### 5. Enable Actuator Security

```yaml
management:
  endpoints:
    web:
      exposure:
        include: health,info,metrics
  endpoint:
    health:
      show-details: when-authorized
```

```java
@Configuration
public class ActuatorSecurityConfig {

    @Bean
    public SecurityFilterChain actuatorSecurity(HttpSecurity http) throws Exception {
        http
            .requestMatcher(EndpointRequest.toAnyEndpoint())
            .authorizeHttpRequests(authorize -> authorize
                .requestMatchers(EndpointRequest.to("health", "info")).permitAll()
                .anyRequest().hasRole("ADMIN")
            );
        return http.build();
    }
}
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| **OAuth2** | Authorization framework for delegated access |
| **Resource Server** | Validates access tokens, protects resources |
| **SSO** | Single login for multiple applications |
| **Session Hijacking** | Prevent with HTTPS, secure cookies, validation |
| **JWT** | Stateless, token-based authentication |
| **Best Practices** | HTTPS, validation, secrets management, monitoring |

## Security Checklist

- [ ] Use HTTPS for all communications
- [ ] Implement OAuth2/JWT for authentication
- [ ] Enable CSRF protection
- [ ] Secure cookies (HttpOnly, Secure, SameSite)
- [ ] Validate all inputs
- [ ] Use parameterized queries
- [ ] Implement rate limiting
- [ ] Short session timeouts
- [ ] Monitor and log security events
- [ ] Keep dependencies updated
- [ ] Use secrets management
- [ ] Enable CORS appropriately
- [ ] Implement method-level security
- [ ] Secure actuator endpoints
- [ ] Regular security audits

## Next Topic

Continue to [Configuration Management](./07-configuration-management.md) to learn about centralized configuration with Spring Cloud Config.
