# Microservices Architecture

## Overview
Learn to design and implement microservices-based applications with Spring Cloud and modern patterns. This module covers the complete microservices ecosystem including service discovery, API gateways, resilience patterns, security, configuration management, and observability.

## Learning Objectives
By the end of this module, you will be able to:
- Understand microservices architecture principles and patterns
- Design microservices using Domain-Driven Design
- Implement service discovery and load balancing with Eureka
- Build API gateways with Spring Cloud Gateway
- Apply resilience patterns (Circuit Breaker, Retry, Bulkhead)
- Implement inter-service communication (REST, Feign, Messaging)
- Secure microservices with OAuth2 and JWT
- Manage centralized configuration with Spring Cloud Config
- Implement distributed tracing and monitoring
- Apply security best practices in microservices

## Topics Covered

### 1. [Microservices Introduction](./topics/01-microservices-introduction.md)
- Introduction to Microservices Architecture (MSA)
- Monolith vs Microservices
- Microservices Characteristics
- Benefits and Challenges
- Domain-Driven Design (DDD)
- 12 Factor App Methodology

### 2. [Service Discovery](./topics/02-service-discovery.md)
- Spring Cloud Overview
- Service Discovery with Eureka Server
- Eureka Client Configuration
- Load Balancing with Spring Cloud LoadBalancer
- Client-Side vs Server-Side Load Balancing
- Load Balancing Algorithms

### 3. [API Gateway](./topics/03-api-gateway.md)
- Spring Cloud Gateway
- Routing and Predicates
- Gateway Filters (Pre and Post)
- Rate Limiting
- Circuit Breaker Integration
- Custom Filters

### 4. [Resilience Patterns](./topics/04-resilience-patterns.md)
- Circuit Breaker with Resilience4j
- Retry Mechanisms
- Fallback Methods
- Bulkhead Pattern
- Rate Limiter
- Combining Resilience Patterns

### 5. [Inter-Service Communication](./topics/05-inter-service-communication.md)
- REST Template
- WebClient (Reactive)
- Feign Client (Declarative)
- Asynchronous Communication
- Spring Cloud Stream
- Message Brokers (RabbitMQ, Kafka)

### 6. [Security](./topics/06-security.md)
- OAuth2 Authorization Framework
- Resource Server Configuration
- Single Sign-On (SSO)
- Session Hijacking Prevention
- JWT Authentication
- API Security Best Practices

### 7. [Configuration Management](./topics/07-configuration-management.md)
- Spring Cloud Config Server
- Config Client Setup
- Centralized Configuration
- Git-based Configuration Repository
- Refresh Scope
- Spring Cloud Bus
- Configuration Encryption

### 8. [Monitoring and Observability](./topics/08-monitoring-observability.md)
- Distributed Tracing with Sleuth and Zipkin
- Logging Aggregation (ELK Stack)
- Metrics Collection with Actuator
- Prometheus Integration
- Grafana Dashboards
- Health Checks
- Security Best Practices

## Key Concepts

### Microservices Characteristics
- **Componentization via Services**: Independently deployable units
- **Organized Around Business Capabilities**: Service boundaries align with business domains
- **Decentralized Governance**: Teams choose appropriate technologies
- **Decentralized Data Management**: Database per service pattern
- **Infrastructure Automation**: CI/CD, automated testing, deployment
- **Design for Failure**: Circuit breakers, retries, fallbacks

### Spring Cloud Components
```
┌─────────────────────────────────────────┐
│        Spring Cloud Ecosystem           │
├─────────────────────────────────────────┤
│ • Eureka (Service Discovery)            │
│ • Gateway (API Gateway)                 │
│ • Config (Configuration Management)     │
│ • LoadBalancer (Load Balancing)         │
│ • Resilience4j (Resilience Patterns)    │
│ • OpenFeign (Declarative HTTP Client)   │
│ • Sleuth (Distributed Tracing)          │
│ • Bus (Event Broadcasting)              │
└─────────────────────────────────────────┘
```

### Architecture Patterns

**Service Discovery Pattern:**
```
Services → Register → Eureka Server ← Discover ← Clients
```

**API Gateway Pattern:**
```
Clients → API Gateway → Route/Filter → Microservices
```

**Circuit Breaker Pattern:**
```
Service A → Circuit Breaker → Service B
         ↓ (if failing)
      Fallback
```

**Saga Pattern:**
```
Service A → Event → Service B → Event → Service C
         ↓ (if fails)
  Compensating Transaction
```

## Exercises

See the [exercises](./exercises/) directory for hands-on practice problems including:
- Building a complete microservices application
- Implementing service discovery and registration
- Creating API gateway with routing and filtering
- Applying resilience patterns
- Implementing distributed tracing
- Securing microservices with OAuth2

## Best Practices

### Design Principles
1. **Start with a Monolith**: Don't begin with microservices
2. **Domain-Driven Design**: Use bounded contexts for service boundaries
3. **Single Responsibility**: Each service has one business capability
4. **Loose Coupling**: Minimize dependencies between services
5. **High Cohesion**: Related functionality within same service

### Development Practices
1. **API First**: Design APIs before implementation
2. **Version APIs**: Maintain backward compatibility
3. **Automate Everything**: CI/CD, testing, deployment
4. **Containerization**: Use Docker for consistency
5. **Infrastructure as Code**: Terraform, CloudFormation

### Operational Practices
1. **Monitoring**: Implement comprehensive observability
2. **Distributed Tracing**: Track requests across services
3. **Centralized Logging**: Aggregate logs from all services
4. **Health Checks**: Monitor service availability
5. **Graceful Degradation**: Handle failures gracefully

### Security Practices
1. **Defense in Depth**: Multiple security layers
2. **OAuth2/JWT**: Token-based authentication
3. **mTLS**: Secure service-to-service communication
4. **API Gateway Security**: Centralized authentication
5. **Secrets Management**: Never hardcode credentials

## Common Patterns

### Communication Patterns
- **Synchronous**: REST, gRPC (request-response)
- **Asynchronous**: Message queues, event streaming
- **Service Mesh**: Istio, Linkerd (infrastructure layer)

### Data Patterns
- **Database per Service**: Each service owns its data
- **Saga Pattern**: Distributed transactions
- **CQRS**: Command Query Responsibility Segregation
- **Event Sourcing**: Store events, not current state

### Deployment Patterns
- **Blue-Green Deployment**: Zero downtime
- **Canary Deployment**: Gradual rollout
- **Rolling Deployment**: Update instances incrementally

## Additional Resources

### Documentation
- [Spring Cloud Documentation](https://spring.io/projects/spring-cloud)
- [Microservices.io](https://microservices.io/)
- [12 Factor App](https://12factor.net/)
- [Domain-Driven Design](https://www.domainlanguage.com/ddd/)

### Books
- "Building Microservices" by Sam Newman
- "Microservices Patterns" by Chris Richardson
- "Domain-Driven Design" by Eric Evans
- "Release It!" by Michael Nygard

### Tools
- **Service Mesh**: Istio, Linkerd, Consul
- **Container Orchestration**: Kubernetes, Docker Swarm
- **Monitoring**: Prometheus, Grafana, ELK Stack
- **API Gateway**: Kong, Apigee, AWS API Gateway

### Online Resources
- [Spring Cloud Examples](https://github.com/spring-cloud-samples)
- [Microservices Patterns GitHub](https://github.com/microservices-patterns)
- [Awesome Microservices](https://github.com/mfornos/awesome-microservices)

### Cheat Sheets
- See [resources/cheat-sheets/](../resources/cheat-sheets/) for quick references

## Hands-On Lab

Build a complete e-commerce microservices application:

### Services
1. **Product Service**: Manage product catalog
2. **Order Service**: Process orders
3. **Payment Service**: Handle payments
4. **Inventory Service**: Track stock
5. **Notification Service**: Send emails/SMS
6. **User Service**: Manage users

### Infrastructure
- **Eureka Server**: Service discovery
- **Config Server**: Centralized configuration
- **API Gateway**: Single entry point
- **Zipkin**: Distributed tracing
- **PostgreSQL**: Databases
- **RabbitMQ**: Message broker

### Implementation Steps
1. Set up infrastructure services (Eureka, Config, Gateway)
2. Implement business services with Spring Boot
3. Configure service discovery and registration
4. Implement inter-service communication
5. Add resilience patterns (Circuit Breaker, Retry)
6. Secure with OAuth2
7. Implement distributed tracing
8. Add monitoring and logging

## Assessment

You should be comfortable with:
- [ ] Understanding microservices principles and patterns
- [ ] Designing services using Domain-Driven Design
- [ ] Implementing service discovery with Eureka
- [ ] Building API gateways with Spring Cloud Gateway
- [ ] Applying resilience patterns (Circuit Breaker, Retry, Bulkhead)
- [ ] Implementing REST and asynchronous communication
- [ ] Securing microservices with OAuth2
- [ ] Managing centralized configuration
- [ ] Implementing distributed tracing with Sleuth and Zipkin
- [ ] Monitoring microservices with Actuator and Prometheus
- [ ] Deploying microservices to cloud platforms

## Common Challenges and Solutions

### Challenge 1: Data Consistency
**Problem**: Maintaining consistency across distributed services
**Solutions**:
- Saga pattern for distributed transactions
- Event sourcing and CQRS
- Eventual consistency model

### Challenge 2: Service Discovery
**Problem**: How services find each other dynamically
**Solutions**:
- Eureka for service registry
- DNS-based discovery
- Service mesh (Istio, Linkerd)

### Challenge 3: Distributed Tracing
**Problem**: Tracking requests across multiple services
**Solutions**:
- Spring Cloud Sleuth + Zipkin
- Jaeger
- AWS X-Ray

### Challenge 4: Configuration Management
**Problem**: Managing configuration across many services
**Solutions**:
- Spring Cloud Config
- Consul
- Kubernetes ConfigMaps

### Challenge 5: Testing
**Problem**: Testing distributed systems
**Solutions**:
- Contract testing (Pact, Spring Cloud Contract)
- Consumer-driven contracts
- Chaos engineering (Chaos Monkey)

## Migration Strategy

### Monolith to Microservices
1. **Strangler Pattern**: Gradually extract services
2. **Identify Bounded Contexts**: Use DDD to find boundaries
3. **Extract Service**: Start with least dependent module
4. **Build Anti-Corruption Layer**: Isolate old and new
5. **Repeat**: Incrementally extract services

### Steps
1. Analyze monolith and identify bounded contexts
2. Set up microservices infrastructure
3. Extract peripheral services first
4. Implement event-driven communication
5. Migrate data gradually
6. Decommission monolith when ready

## Next Steps

Once you've completed this module and feel confident with microservices:
- Explore service mesh technologies (Istio, Linkerd)
- Learn Kubernetes for container orchestration
- Study advanced patterns (Event Sourcing, CQRS)
- Practice chaos engineering
- Explore serverless architectures

---

**Time Estimate:** 1 week
**Difficulty:** Advanced
**Prerequisites:** Spring Boot, REST APIs, Database concepts, Docker basics
