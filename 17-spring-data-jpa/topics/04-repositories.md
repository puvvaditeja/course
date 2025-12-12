# Repositories

## Repository Interface Hierarchy

Spring Data JPA provides a hierarchy of repository interfaces, each building upon the previous one with additional functionality. Understanding this hierarchy helps you choose the right interface for your needs.

```
Repository<T, ID>
    ↓
CrudRepository<T, ID>
    ↓
PagingAndSortingRepository<T, ID>
    ↓
JpaRepository<T, ID>
```

---

## CrudRepository

`CrudRepository` provides basic CRUD (Create, Read, Update, Delete) operations for an entity.

### Interface Definition

```java
public interface CrudRepository<T, ID> extends Repository<T, ID> {
    <S extends T> S save(S entity);
    <S extends T> Iterable<S> saveAll(Iterable<S> entities);
    Optional<T> findById(ID id);
    boolean existsById(ID id);
    Iterable<T> findAll();
    Iterable<T> findAllById(Iterable<ID> ids);
    long count();
    void deleteById(ID id);
    void delete(T entity);
    void deleteAllById(Iterable<? extends ID> ids);
    void deleteAll(Iterable<? extends T> entities);
    void deleteAll();
}
```

### Basic Usage

```java
import org.springframework.data.repository.CrudRepository;

public interface UserRepository extends CrudRepository<User, Long> {
    // Interface is ready to use with built-in methods
}
```

### Example Operations

```java
@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    // Save a single user
    public User createUser(User user) {
        return userRepository.save(user);
    }

    // Save multiple users
    public List<User> createUsers(List<User> users) {
        return (List<User>) userRepository.saveAll(users);
    }

    // Find by ID
    public User getUserById(Long id) {
        return userRepository.findById(id)
            .orElseThrow(() -> new UserNotFoundException(id));
    }

    // Check if exists
    public boolean userExists(Long id) {
        return userRepository.existsById(id);
    }

    // Get all users
    public List<User> getAllUsers() {
        return (List<User>) userRepository.findAll();
    }

    // Count users
    public long countUsers() {
        return userRepository.count();
    }

    // Delete by ID
    public void deleteUser(Long id) {
        userRepository.deleteById(id);
    }

    // Delete entity
    public void deleteUser(User user) {
        userRepository.delete(user);
    }

    // Delete all
    public void deleteAllUsers() {
        userRepository.deleteAll();
    }
}
```

### When to Use CrudRepository

- Simple applications with basic CRUD needs
- When you don't need pagination or sorting
- Microservices with limited data access requirements
- When extending with custom methods is sufficient

---

## PagingAndSortingRepository

`PagingAndSortingRepository` extends `CrudRepository` and adds pagination and sorting capabilities.

### Interface Definition

```java
public interface PagingAndSortingRepository<T, ID> extends CrudRepository<T, ID> {
    Iterable<T> findAll(Sort sort);
    Page<T> findAll(Pageable pageable);
}
```

### Basic Usage

```java
public interface ProductRepository extends PagingAndSortingRepository<Product, Long> {
    // Inherits all CrudRepository methods plus pagination and sorting
}
```

### Sorting Examples

```java
@Service
public class ProductService {

    @Autowired
    private ProductRepository productRepository;

    // Sort by single field ascending
    public List<Product> getProductsSortedByName() {
        Sort sort = Sort.by("name").ascending();
        return (List<Product>) productRepository.findAll(sort);
    }

    // Sort by single field descending
    public List<Product> getProductsSortedByPriceDesc() {
        Sort sort = Sort.by("price").descending();
        return (List<Product>) productRepository.findAll(sort);
    }

    // Sort by multiple fields
    public List<Product> getProductsSortedByCategoryAndPrice() {
        Sort sort = Sort.by("category").ascending()
                        .and(Sort.by("price").descending());
        return (List<Product>) productRepository.findAll(sort);
    }

    // Sort with case-insensitive ordering
    public List<Product> getProductsSortedCaseInsensitive() {
        Sort sort = Sort.by(Sort.Order.asc("name").ignoreCase());
        return (List<Product>) productRepository.findAll(sort);
    }
}
```

### Pagination Examples

```java
@Service
public class ProductService {

    @Autowired
    private ProductRepository productRepository;

    // Get first page with 10 items
    public Page<Product> getFirstPage() {
        Pageable pageable = PageRequest.of(0, 10);
        return productRepository.findAll(pageable);
    }

    // Get page with sorting
    public Page<Product> getPageSorted(int page, int size) {
        Pageable pageable = PageRequest.of(page, size,
            Sort.by("name").ascending());
        return productRepository.findAll(pageable);
    }

    // Get page information
    public void demonstratePageInfo() {
        Page<Product> page = productRepository.findAll(
            PageRequest.of(0, 10));

        int totalPages = page.getTotalPages();
        long totalElements = page.getTotalElements();
        int numberOfElements = page.getNumberOfElements();
        List<Product> content = page.getContent();
        boolean hasNext = page.hasNext();
        boolean hasPrevious = page.hasPrevious();
    }
}
```

### When to Use PagingAndSortingRepository

- Applications displaying paginated lists
- When you need to sort results
- REST APIs with pagination support
- Large datasets that shouldn't be loaded all at once

---

## JpaRepository

`JpaRepository` is the most feature-rich repository interface, extending `PagingAndSortingRepository` with JPA-specific functionality.

### Interface Definition

```java
public interface JpaRepository<T, ID> extends PagingAndSortingRepository<T, ID> {
    List<T> findAll();
    List<T> findAll(Sort sort);
    List<T> findAllById(Iterable<ID> ids);
    <S extends T> List<S> saveAll(Iterable<S> entities);
    void flush();
    <S extends T> S saveAndFlush(S entity);
    <S extends T> List<S> saveAllAndFlush(Iterable<S> entities);
    void deleteAllInBatch(Iterable<T> entities);
    void deleteAllByIdInBatch(Iterable<ID> ids);
    void deleteAllInBatch();
    T getOne(ID id);  // Deprecated, use getById or getReferenceById
    T getById(ID id);
    T getReferenceById(ID id);
    <S extends T> List<S> findAll(Example<S> example);
    <S extends T> List<S> findAll(Example<S> example, Sort sort);
}
```

### Basic Usage

```java
public interface EmployeeRepository extends JpaRepository<Employee, Long> {
    // Recommended interface for most use cases
}
```

### JPA-Specific Features

#### Flushing

```java
@Service
public class EmployeeService {

    @Autowired
    private EmployeeRepository employeeRepository;

    // Save and immediately flush to database
    @Transactional
    public Employee createEmployeeAndFlush(Employee employee) {
        return employeeRepository.saveAndFlush(employee);
    }

    // Manually flush pending changes
    @Transactional
    public void updateAndFlush(Employee employee) {
        employeeRepository.save(employee);
        employeeRepository.flush(); // Force synchronization with DB
    }
}
```

#### Batch Operations

```java
@Service
public class EmployeeService {

    @Autowired
    private EmployeeRepository employeeRepository;

    // Delete in batch (more efficient than individual deletes)
    @Transactional
    public void deleteEmployeesBatch(List<Employee> employees) {
        employeeRepository.deleteAllInBatch(employees);
    }

    // Delete by IDs in batch
    @Transactional
    public void deleteEmployeesByIdBatch(List<Long> ids) {
        employeeRepository.deleteAllByIdInBatch(ids);
    }

    // Delete all in batch
    @Transactional
    public void deleteAllEmployeesBatch() {
        employeeRepository.deleteAllInBatch();
    }
}
```

#### Query by Example

```java
@Service
public class EmployeeService {

    @Autowired
    private EmployeeRepository employeeRepository;

    // Find by example
    public List<Employee> findByExample() {
        Employee probe = new Employee();
        probe.setDepartment("Engineering");
        probe.setActive(true);

        Example<Employee> example = Example.of(probe);
        return employeeRepository.findAll(example);
    }

    // Find by example with matcher
    public List<Employee> findByExampleWithMatcher() {
        Employee probe = new Employee();
        probe.setFirstName("John");
        probe.setEmail("john@example.com");

        ExampleMatcher matcher = ExampleMatcher.matching()
            .withIgnoreCase()
            .withStringMatcher(ExampleMatcher.StringMatcher.CONTAINING)
            .withIgnorePaths("id", "createdAt");

        Example<Employee> example = Example.of(probe, matcher);
        return employeeRepository.findAll(example);
    }
}
```

### When to Use JpaRepository

- **Most common choice** for Spring Data JPA applications
- When you need batch operations
- When you need to flush changes explicitly
- When you want to use Query by Example
- General-purpose applications with full JPA features

---

## Custom Repositories

Custom repositories allow you to add methods beyond what Spring Data provides automatically.

### Method 1: Custom Query Methods

Spring Data automatically implements methods based on naming conventions:

```java
public interface UserRepository extends JpaRepository<User, Long> {

    // Find by single field
    User findByEmail(String email);

    // Find by multiple fields
    List<User> findByFirstNameAndLastName(String firstName, String lastName);

    // Find with comparison operators
    List<User> findByAgeGreaterThan(int age);
    List<User> findByAgeLessThanEqual(int age);
    List<User> findBySalaryBetween(double min, double max);

    // Find with pattern matching
    List<User> findByEmailContaining(String emailPart);
    List<User> findByUsernameStartingWith(String prefix);
    List<User> findByFirstNameEndingWith(String suffix);

    // Find with null checks
    List<User> findByMiddleNameIsNull();
    List<User> findByMiddleNameIsNotNull();

    // Find with boolean
    List<User> findByActiveTrue();
    List<User> findByActiveFalse();

    // Find with collections
    List<User> findByRolesIn(List<String> roles);
    List<User> findByRolesNotIn(List<String> roles);

    // Count methods
    long countByActive(boolean active);
    long countByAgeGreaterThan(int age);

    // Existence checks
    boolean existsByEmail(String email);
    boolean existsByUsername(String username);

    // Delete methods
    void deleteByEmail(String email);
    long deleteByActiveFalse();

    // Limiting results
    User findFirstByOrderByCreatedAtDesc();
    List<User> findTop10ByOrderBySalaryDesc();

    // Distinct results
    List<User> findDistinctByLastName(String lastName);

    // Ordering
    List<User> findByActiveOrderByLastNameAsc(boolean active);
    List<User> findByAgeGreaterThanOrderByLastNameAscFirstNameDesc(int age);
}
```

### Method 2: Custom Repository Interface

For complex logic that can't be expressed through method names:

**Step 1: Create custom interface**

```java
public interface UserRepositoryCustom {
    List<User> findUsersWithComplexCriteria(String name, Integer minAge, String department);
    void updateUserStatusBatch(List<Long> ids, String status);
}
```

**Step 2: Implement the interface**

```java
@Repository
public class UserRepositoryImpl implements UserRepositoryCustom {

    @PersistenceContext
    private EntityManager entityManager;

    @Override
    public List<User> findUsersWithComplexCriteria(
            String name, Integer minAge, String department) {

        CriteriaBuilder cb = entityManager.getCriteriaBuilder();
        CriteriaQuery<User> query = cb.createQuery(User.class);
        Root<User> user = query.from(User.class);

        List<Predicate> predicates = new ArrayList<>();

        if (name != null && !name.isEmpty()) {
            predicates.add(cb.like(cb.lower(user.get("firstName")),
                "%" + name.toLowerCase() + "%"));
        }

        if (minAge != null) {
            predicates.add(cb.greaterThanOrEqualTo(user.get("age"), minAge));
        }

        if (department != null) {
            predicates.add(cb.equal(user.get("department"), department));
        }

        query.where(predicates.toArray(new Predicate[0]));
        query.orderBy(cb.asc(user.get("lastName")));

        return entityManager.createQuery(query).getResultList();
    }

    @Override
    @Transactional
    public void updateUserStatusBatch(List<Long> ids, String status) {
        String jpql = "UPDATE User u SET u.status = :status WHERE u.id IN :ids";
        entityManager.createQuery(jpql)
            .setParameter("status", status)
            .setParameter("ids", ids)
            .executeUpdate();
    }
}
```

**Step 3: Extend the custom interface**

```java
public interface UserRepository extends JpaRepository<User, Long>,
                                        UserRepositoryCustom {
    // Standard repository methods + custom methods
    User findByEmail(String email);
}
```

### Method 3: @Query Annotation

For custom JPQL or native SQL queries:

```java
public interface OrderRepository extends JpaRepository<Order, Long> {

    // JPQL Query
    @Query("SELECT o FROM Order o WHERE o.customer.id = :customerId " +
           "AND o.status = :status")
    List<Order> findOrdersByCustomerAndStatus(
        @Param("customerId") Long customerId,
        @Param("status") String status);

    // JPQL with JOIN
    @Query("SELECT o FROM Order o JOIN FETCH o.items " +
           "WHERE o.id = :orderId")
    Order findOrderWithItems(@Param("orderId") Long orderId);

    // Native SQL Query
    @Query(value = "SELECT * FROM orders WHERE total_amount > ?1 " +
                   "AND order_date >= ?2",
           nativeQuery = true)
    List<Order> findHighValueRecentOrders(double minAmount, LocalDate fromDate);

    // Update query
    @Modifying
    @Query("UPDATE Order o SET o.status = :status WHERE o.id = :id")
    int updateOrderStatus(@Param("id") Long id, @Param("status") String status);

    // Delete query
    @Modifying
    @Query("DELETE FROM Order o WHERE o.status = :status " +
           "AND o.createdAt < :date")
    int deleteOldOrdersByStatus(
        @Param("status") String status,
        @Param("date") LocalDateTime date);

    // Count query
    @Query("SELECT COUNT(o) FROM Order o WHERE o.customer.id = :customerId")
    long countCustomerOrders(@Param("customerId") Long customerId);

    // Projection query
    @Query("SELECT o.id, o.orderNumber, o.totalAmount FROM Order o " +
           "WHERE o.status = :status")
    List<Object[]> findOrderSummaryByStatus(@Param("status") String status);
}
```

### Method 4: Specifications (Criteria API)

For dynamic queries with complex criteria:

```java
public interface UserRepository extends JpaRepository<User, Long>,
                                        JpaSpecificationExecutor<User> {
}

// Specification class
public class UserSpecifications {

    public static Specification<User> hasFirstName(String firstName) {
        return (root, query, cb) ->
            firstName == null ? null : cb.equal(root.get("firstName"), firstName);
    }

    public static Specification<User> hasAgeGreaterThan(Integer age) {
        return (root, query, cb) ->
            age == null ? null : cb.greaterThan(root.get("age"), age);
    }

    public static Specification<User> hasDepartment(String department) {
        return (root, query, cb) ->
            department == null ? null : cb.equal(root.get("department"), department);
    }

    public static Specification<User> isActive() {
        return (root, query, cb) -> cb.isTrue(root.get("active"));
    }
}

// Usage in service
@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    public List<User> findUsers(String firstName, Integer minAge, String department) {
        Specification<User> spec = Specification.where(null);

        if (firstName != null) {
            spec = spec.and(UserSpecifications.hasFirstName(firstName));
        }

        if (minAge != null) {
            spec = spec.and(UserSpecifications.hasAgeGreaterThan(minAge));
        }

        if (department != null) {
            spec = spec.and(UserSpecifications.hasDepartment(department));
        }

        spec = spec.and(UserSpecifications.isActive());

        return userRepository.findAll(spec);
    }
}
```

---

## Summary

| Repository Type | Use Case | Key Features |
|----------------|----------|--------------|
| CrudRepository | Basic CRUD operations | save, findById, findAll, delete |
| PagingAndSortingRepository | Paginated/sorted data | Pagination, sorting support |
| JpaRepository | Full-featured (recommended) | Batch operations, flush, Query by Example |
| Custom Methods | Derived query methods | Automatic implementation from method names |
| Custom Repository | Complex custom logic | Full EntityManager access |

## Next Topic

Continue to [CRUD Operations](./05-crud-operations.md) to learn how to perform create, read, update, and delete operations with Spring Data JPA.
