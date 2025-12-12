# Queries

## Overview of Query Methods

Spring Data JPA provides multiple approaches for querying data, each with its own advantages and use cases:

1. **Query Derivation** - Method name parsing
2. **@Query Annotation** - JPQL or native SQL
3. **Named Queries** - Predefined, reusable queries
4. **Native Queries** - Direct SQL execution
5. **Criteria API** - Type-safe, dynamic queries
6. **Query by Example** - Query based on entity instances

---

## Query Derivation

Spring Data JPA can automatically generate queries by parsing method names. This is the most common and convenient approach for simple queries.

### Basic Query Keywords

```java
public interface UserRepository extends JpaRepository<User, Long> {

    // Find by exact match
    User findByUsername(String username);
    List<User> findByLastName(String lastName);

    // Find with AND
    User findByUsernameAndEmail(String username, String email);
    List<User> findByFirstNameAndLastName(String firstName, String lastName);

    // Find with OR
    List<User> findByUsernameOrEmail(String username, String email);
    List<User> findByFirstNameOrLastNameOrEmail(
        String firstName, String lastName, String email);

    // Find with NOT
    List<User> findByActiveNot(boolean active);
    List<User> findByStatusNotIn(List<String> statuses);
}
```

### Comparison Operators

```java
public interface ProductRepository extends JpaRepository<Product, Long> {

    // Greater than / Less than
    List<Product> findByPriceGreaterThan(double price);
    List<Product> findByPriceGreaterThanEqual(double price);
    List<Product> findByPriceLessThan(double price);
    List<Product> findByPriceLessThanEqual(double price);

    // Between
    List<Product> findByPriceBetween(double minPrice, double maxPrice);
    List<Product> findByCreatedAtBetween(
        LocalDateTime start, LocalDateTime end);

    // After / Before (for dates)
    List<Product> findByCreatedAtAfter(LocalDateTime date);
    List<Product> findByCreatedAtBefore(LocalDateTime date);

    // In / Not In
    List<Product> findByCategoryIn(List<String> categories);
    List<Product> findByIdNotIn(List<Long> ids);
}
```

### String Matching

```java
public interface UserRepository extends JpaRepository<User, Long> {

    // Like (use % wildcards in the parameter)
    List<User> findByEmailLike(String pattern);  // findByEmailLike("%@gmail.com")

    // Containing (automatic % wrapping)
    List<User> findByEmailContaining(String emailPart);
    List<User> findByFirstNameContainingIgnoreCase(String namePart);

    // Starting with
    List<User> findByUsernameStartingWith(String prefix);
    List<User> findByUsernameStartingWithIgnoreCase(String prefix);

    // Ending with
    List<User> findByEmailEndingWith(String suffix);
    List<User> findByEmailEndingWithIgnoreCase(String suffix);

    // Ignore case
    User findByUsernameIgnoreCase(String username);
    List<User> findByLastNameIgnoreCase(String lastName);
}
```

### Null Handling

```java
public interface EmployeeRepository extends JpaRepository<Employee, Long> {

    // Is Null
    List<Employee> findByManagerIsNull();
    List<Employee> findByTerminationDateIsNull();

    // Is Not Null
    List<Employee> findByMiddleNameIsNotNull();
    List<Employee> findByBonusIsNotNull();
}
```

### Boolean Queries

```java
public interface UserRepository extends JpaRepository<User, Long> {

    // True / False
    List<User> findByActiveTrue();
    List<User> findByActiveFalse();
    List<User> findByEmailVerifiedTrue();

    // With parameter
    List<User> findByActive(boolean active);
}
```

### Ordering

```java
public interface ProductRepository extends JpaRepository<Product, Long> {

    // Order by single field
    List<Product> findByOrderByPriceAsc();
    List<Product> findByOrderByPriceDesc();
    List<Product> findByCategoryOrderByNameAsc(String category);

    // Order by multiple fields
    List<Product> findByOrderByPriceAscNameDesc();
    List<Product> findByCategoryOrderByPriceAscNameAsc(String category);

    // Combined with other keywords
    List<Product> findByActiveTrueOrderByCreatedAtDesc(boolean active);
}
```

### Limiting Results

```java
public interface UserRepository extends JpaRepository<User, Long> {

    // First / Top
    User findFirstByOrderByCreatedAtDesc();
    User findTopByOrderBySalaryDesc();

    // First N / Top N
    List<User> findFirst5ByOrderByScoreDesc();
    List<User> findTop10ByActiveTrueOrderByCreatedAtDesc();

    // With filters
    User findFirstByActiveTrueOrderByLastLoginDesc(boolean active);
    List<User> findTop3ByDepartmentOrderBySalaryDesc(String department);
}
```

### Distinct Results

```java
public interface OrderRepository extends JpaRepository<Order, Long> {

    // Distinct
    List<Order> findDistinctByCustomerId(Long customerId);
    List<String> findDistinctStatusByCustomerId(Long customerId);
}
```

### Nested Properties

```java
public interface UserRepository extends JpaRepository<User, Long> {

    // Query nested object properties
    List<User> findByAddress_City(String city);
    List<User> findByAddress_State(String state);
    List<User> findByAddress_ZipCode(String zipCode);

    // Multiple levels
    List<User> findByProfile_Settings_Theme(String theme);
}
```

### Count and Exists

```java
public interface UserRepository extends JpaRepository<User, Long> {

    // Count
    long countByActive(boolean active);
    long countByDepartment(String department);
    long countByCreatedAtAfter(LocalDateTime date);

    // Exists
    boolean existsByEmail(String email);
    boolean existsByUsernameAndActive(String username, boolean active);
}
```

### Delete Queries

```java
public interface UserRepository extends JpaRepository<User, Long> {

    // Delete methods
    void deleteByEmail(String email);
    long deleteByActiveFalse();
    void deleteByCreatedAtBefore(LocalDateTime date);

    // Return deleted entities
    List<User> removeByActiveFalse();
}
```

---

## @Query Annotation

The `@Query` annotation allows you to define custom JPQL or native SQL queries directly in repository methods.

### Basic JPQL Queries

```java
public interface UserRepository extends JpaRepository<User, Long> {

    // Positional parameters
    @Query("SELECT u FROM User u WHERE u.email = ?1")
    User findByEmailQuery(String email);

    @Query("SELECT u FROM User u WHERE u.firstName = ?1 AND u.lastName = ?2")
    User findByFullNamePositional(String firstName, String lastName);

    // Named parameters (recommended)
    @Query("SELECT u FROM User u WHERE u.firstName = :firstName " +
           "AND u.lastName = :lastName")
    User findByFullName(
        @Param("firstName") String firstName,
        @Param("lastName") String lastName);

    @Query("SELECT u FROM User u WHERE u.email = :email")
    Optional<User> findByEmailOptional(@Param("email") String email);
}
```

### JPQL with Joins

```java
public interface OrderRepository extends JpaRepository<Order, Long> {

    // Inner join
    @Query("SELECT o FROM Order o JOIN o.customer c WHERE c.email = :email")
    List<Order> findByCustomerEmail(@Param("email") String email);

    // Left join
    @Query("SELECT o FROM Order o LEFT JOIN o.items i WHERE o.id = :id")
    Order findOrderWithItems(@Param("id") Long id);

    // Join fetch (avoid N+1 queries)
    @Query("SELECT DISTINCT o FROM Order o JOIN FETCH o.items " +
           "WHERE o.status = :status")
    List<Order> findByStatusWithItems(@Param("status") String status);

    // Multiple joins
    @Query("SELECT o FROM Order o " +
           "JOIN o.customer c " +
           "JOIN o.items i " +
           "WHERE c.email = :email AND i.product.category = :category")
    List<Order> findByCustomerEmailAndProductCategory(
        @Param("email") String email,
        @Param("category") String category);
}
```

### JPQL with Aggregations

```java
public interface OrderRepository extends JpaRepository<Order, Long> {

    // Count
    @Query("SELECT COUNT(o) FROM Order o WHERE o.customer.id = :customerId")
    long countByCustomerId(@Param("customerId") Long customerId);

    // Sum
    @Query("SELECT SUM(o.totalAmount) FROM Order o " +
           "WHERE o.customer.id = :customerId")
    Double getTotalAmountByCustomer(@Param("customerId") Long customerId);

    // Average
    @Query("SELECT AVG(o.totalAmount) FROM Order o WHERE o.status = :status")
    Double getAverageOrderAmount(@Param("status") String status);

    // Max / Min
    @Query("SELECT MAX(o.totalAmount) FROM Order o")
    Double getMaxOrderAmount();

    @Query("SELECT MIN(o.createdAt) FROM Order o WHERE o.customer.id = :customerId")
    LocalDateTime getFirstOrderDate(@Param("customerId") Long customerId);

    // Group by
    @Query("SELECT o.status, COUNT(o) FROM Order o GROUP BY o.status")
    List<Object[]> getOrderCountByStatus();

    @Query("SELECT o.customer.id, SUM(o.totalAmount) FROM Order o " +
           "GROUP BY o.customer.id HAVING SUM(o.totalAmount) > :amount")
    List<Object[]> getHighValueCustomers(@Param("amount") Double amount);
}
```

### DTO Projections

```java
// DTO class
public class UserDTO {
    private Long id;
    private String username;
    private String email;

    public UserDTO(Long id, String username, String email) {
        this.id = id;
        this.username = username;
        this.email = email;
    }

    // Getters and setters
}

public interface UserRepository extends JpaRepository<User, Long> {

    // Constructor expression
    @Query("SELECT new com.example.dto.UserDTO(u.id, u.username, u.email) " +
           "FROM User u WHERE u.active = :active")
    List<UserDTO> findActiveUserDTOs(@Param("active") boolean active);

    @Query("SELECT new com.example.dto.OrderSummaryDTO(" +
           "o.id, o.orderNumber, o.totalAmount, c.name) " +
           "FROM Order o JOIN o.customer c WHERE o.status = :status")
    List<OrderSummaryDTO> findOrderSummaries(@Param("status") String status);
}
```

### Update Queries

```java
public interface UserRepository extends JpaRepository<User, Long> {

    @Modifying
    @Transactional
    @Query("UPDATE User u SET u.status = :status WHERE u.id = :id")
    int updateUserStatus(@Param("id") Long id, @Param("status") String status);

    @Modifying
    @Transactional
    @Query("UPDATE User u SET u.active = :active " +
           "WHERE u.lastLoginDate < :date")
    int deactivateInactiveUsers(
        @Param("active") boolean active,
        @Param("date") LocalDateTime date);

    @Modifying
    @Transactional
    @Query("UPDATE User u SET u.failedLoginAttempts = u.failedLoginAttempts + 1 " +
           "WHERE u.email = :email")
    int incrementFailedLoginAttempts(@Param("email") String email);
}
```

### Delete Queries

```java
public interface UserRepository extends JpaRepository<User, Long> {

    @Modifying
    @Transactional
    @Query("DELETE FROM User u WHERE u.active = false")
    int deleteInactiveUsers();

    @Modifying
    @Transactional
    @Query("DELETE FROM User u WHERE u.lastLoginDate < :date")
    int deleteUsersInactiveSince(@Param("date") LocalDateTime date);

    @Modifying
    @Transactional
    @Query("DELETE FROM OrderItem oi WHERE oi.order.id = :orderId")
    int deleteOrderItems(@Param("orderId") Long orderId);
}
```

### Pagination with @Query

```java
public interface UserRepository extends JpaRepository<User, Long> {

    @Query("SELECT u FROM User u WHERE u.department = :department")
    Page<User> findByDepartmentPaged(
        @Param("department") String department,
        Pageable pageable);

    @Query("SELECT u FROM User u WHERE u.active = :active")
    Slice<User> findByActiveSliced(
        @Param("active") boolean active,
        Pageable pageable);

    // With count query optimization
    @Query(value = "SELECT u FROM User u WHERE u.firstName LIKE %:name%",
           countQuery = "SELECT COUNT(u) FROM User u WHERE u.firstName LIKE %:name%")
    Page<User> findByFirstNameContaining(
        @Param("name") String name,
        Pageable pageable);
}
```

---

## Named Queries

Named queries are predefined queries associated with entities, defined using `@NamedQuery` or `@NamedQueries`.

### Defining Named Queries

```java
@Entity
@NamedQuery(
    name = "User.findByEmailDomain",
    query = "SELECT u FROM User u WHERE u.email LIKE CONCAT('%@', :domain)"
)
@NamedQueries({
    @NamedQuery(
        name = "User.findActiveUsers",
        query = "SELECT u FROM User u WHERE u.active = true ORDER BY u.createdAt DESC"
    ),
    @NamedQuery(
        name = "User.findByDepartmentAndActive",
        query = "SELECT u FROM User u WHERE u.department = :department " +
                "AND u.active = :active"
    )
})
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String email;
    private String department;
    private boolean active;

    // Other fields, getters, setters
}
```

### Using Named Queries

```java
public interface UserRepository extends JpaRepository<User, Long> {

    // Spring Data automatically recognizes named queries
    // Method name must match the query name (without entity prefix)
    List<User> findByEmailDomain(@Param("domain") String domain);

    List<User> findActiveUsers();

    List<User> findByDepartmentAndActive(
        @Param("department") String department,
        @Param("active") boolean active);
}
```

### Named Queries in XML

You can also define named queries in `META-INF/orm.xml`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<entity-mappings xmlns="https://jakarta.ee/xml/ns/persistence/orm"
                 version="3.0">

    <named-query name="User.findByAgeRange">
        <query>
            SELECT u FROM User u
            WHERE u.age BETWEEN :minAge AND :maxAge
            ORDER BY u.age
        </query>
    </named-query>

    <named-query name="User.findHighSalaryUsers">
        <query>
            SELECT u FROM User u
            WHERE u.salary > :minSalary
            ORDER BY u.salary DESC
        </query>
    </named-query>

</entity-mappings>
```

---

## Native Queries

Native queries allow you to execute raw SQL directly against the database.

### Basic Native Queries

```java
public interface UserRepository extends JpaRepository<User, Long> {

    // Simple native query
    @Query(value = "SELECT * FROM users WHERE email = ?1",
           nativeQuery = true)
    User findByEmailNative(String email);

    // With named parameters
    @Query(value = "SELECT * FROM users WHERE first_name = :firstName " +
                   "AND last_name = :lastName",
           nativeQuery = true)
    User findByFullNameNative(
        @Param("firstName") String firstName,
        @Param("lastName") String lastName);

    // Return list
    @Query(value = "SELECT * FROM users WHERE department = :department " +
                   "ORDER BY created_at DESC",
           nativeQuery = true)
    List<User> findByDepartmentNative(@Param("department") String department);
}
```

### Native Queries with Joins

```java
public interface OrderRepository extends JpaRepository<Order, Long> {

    @Query(value = "SELECT o.* FROM orders o " +
                   "INNER JOIN customers c ON o.customer_id = c.id " +
                   "WHERE c.email = :email",
           nativeQuery = true)
    List<Order> findByCustomerEmailNative(@Param("email") String email);

    @Query(value = "SELECT o.* FROM orders o " +
                   "LEFT JOIN order_items oi ON o.id = oi.order_id " +
                   "WHERE o.status = :status " +
                   "GROUP BY o.id " +
                   "HAVING COUNT(oi.id) > :minItems",
           nativeQuery = true)
    List<Order> findOrdersWithMinItemsNative(
        @Param("status") String status,
        @Param("minItems") int minItems);
}
```

### Native Queries with Projections

```java
public interface UserRepository extends JpaRepository<User, Long> {

    // Return specific columns as Object[]
    @Query(value = "SELECT id, username, email FROM users WHERE active = true",
           nativeQuery = true)
    List<Object[]> findActiveUserProjectionsNative();

    // Using interface projection
    @Query(value = "SELECT id, username, email FROM users WHERE active = :active",
           nativeQuery = true)
    List<UserProjection> findUserProjectionsNative(@Param("active") boolean active);
}

// Projection interface
public interface UserProjection {
    Long getId();
    String getUsername();
    String getEmail();
}
```

### Native Update/Delete Queries

```java
public interface UserRepository extends JpaRepository<User, Long> {

    @Modifying
    @Transactional
    @Query(value = "UPDATE users SET status = :status WHERE id = :id",
           nativeQuery = true)
    int updateUserStatusNative(
        @Param("id") Long id,
        @Param("status") String status);

    @Modifying
    @Transactional
    @Query(value = "DELETE FROM users WHERE active = false " +
                   "AND last_login_date < :date",
           nativeQuery = true)
    int deleteInactiveUsersNative(@Param("date") LocalDateTime date);
}
```

### Native Queries with Pagination

```java
public interface ProductRepository extends JpaRepository<Product, Long> {

    @Query(value = "SELECT * FROM products WHERE category = :category",
           countQuery = "SELECT COUNT(*) FROM products WHERE category = :category",
           nativeQuery = true)
    Page<Product> findByCategoryNative(
        @Param("category") String category,
        Pageable pageable);
}
```

### Database-Specific Features

```java
public interface UserRepository extends JpaRepository<User, Long> {

    // PostgreSQL-specific: Full-text search
    @Query(value = "SELECT * FROM users " +
                   "WHERE to_tsvector('english', bio) @@ to_tsquery(:query)",
           nativeQuery = true)
    List<User> fullTextSearchBio(@Param("query") String query);

    // MySQL-specific: JSON operations
    @Query(value = "SELECT * FROM users " +
                   "WHERE JSON_EXTRACT(preferences, '$.theme') = :theme",
           nativeQuery = true)
    List<User> findByJsonPreference(@Param("theme") String theme);

    // PostgreSQL: Window functions
    @Query(value = "SELECT *, " +
                   "RANK() OVER (PARTITION BY department ORDER BY salary DESC) as salary_rank " +
                   "FROM users WHERE active = true",
           nativeQuery = true)
    List<Object[]> findUsersWithSalaryRank();
}
```

---

## Query Best Practices

### 1. Choose the Right Query Method

```java
// Good: Simple query - use derived method
List<User> findByEmail(String email);

// Good: Complex query - use @Query
@Query("SELECT u FROM User u JOIN FETCH u.roles WHERE u.active = true")
List<User> findActiveUsersWithRoles();

// Avoid: Overly complex derived method names
// Bad: findByFirstNameAndLastNameAndEmailAndAgeGreaterThanAndDepartment(...)
// Better: Use @Query instead
```

### 2. Avoid N+1 Queries

```java
// Bad: N+1 query problem
@Query("SELECT u FROM User u WHERE u.active = true")
List<User> findActiveUsers();
// Later accessing u.getRoles() triggers additional queries

// Good: Use JOIN FETCH
@Query("SELECT DISTINCT u FROM User u LEFT JOIN FETCH u.roles " +
       "WHERE u.active = true")
List<User> findActiveUsersWithRoles();
```

### 3. Use Pagination for Large Datasets

```java
// Bad: Loading all records
List<Product> findAll();

// Good: Use pagination
Page<Product> findAll(Pageable pageable);
```

### 4. Optimize with Projections

```java
// Bad: Loading entire entity when only few fields needed
@Query("SELECT u FROM User u WHERE u.active = true")
List<User> findActiveUsers();

// Good: Use DTO projection
@Query("SELECT new com.example.dto.UserDTO(u.id, u.username) " +
       "FROM User u WHERE u.active = true")
List<UserDTO> findActiveUserDTOs();
```

---

## Summary

| Query Type | Use Case | Pros | Cons |
|-----------|----------|------|------|
| Derived Methods | Simple queries | Easy, no boilerplate | Limited complexity |
| @Query (JPQL) | Complex queries | Type-safe, database-agnostic | Requires JPQL knowledge |
| Named Queries | Reusable queries | Centralized, validated at startup | Less flexible |
| Native Queries | Database-specific features | Full SQL power | Database-dependent |

## Next Topic

Continue to [Pagination and Auditing](./07-pagination-auditing.md) to learn advanced pagination techniques and automatic auditing features.
