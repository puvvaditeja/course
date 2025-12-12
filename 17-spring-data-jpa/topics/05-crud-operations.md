# CRUD Operations

## Overview of CRUD Operations

CRUD stands for Create, Read, Update, and Delete - the four basic operations for persistent storage. Spring Data JPA provides multiple ways to perform these operations efficiently.

---

## Save and Update

In Spring Data JPA, both save and update operations use the `save()` method. The framework determines whether to INSERT or UPDATE based on the entity's state.

### Basic Save Operation

```java
@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    // Create a new user
    public User createUser(String username, String email) {
        User user = new User();
        user.setUsername(username);
        user.setEmail(email);
        user.setCreatedAt(LocalDateTime.now());

        return userRepository.save(user);  // INSERT operation
    }

    // Using Lombok @Builder
    public User createUserWithBuilder(String username, String email) {
        User user = User.builder()
            .username(username)
            .email(email)
            .createdAt(LocalDateTime.now())
            .build();

        return userRepository.save(user);
    }
}
```

### How save() Works

```java
public interface CrudRepository<T, ID> extends Repository<T, ID> {
    <S extends T> S save(S entity);
}
```

**Decision Logic:**
- If entity ID is **null** → **INSERT** (new entity)
- If entity ID **exists** and entity is **managed** → **UPDATE** (merge)
- If entity ID **exists** but entity is **detached** → **SELECT then UPDATE** (merge)

### Update Operation

```java
@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    // Update existing user
    public User updateUser(Long id, String newEmail) {
        User user = userRepository.findById(id)
            .orElseThrow(() -> new UserNotFoundException(id));

        user.setEmail(newEmail);
        user.setUpdatedAt(LocalDateTime.now());

        return userRepository.save(user);  // UPDATE operation
    }

    // Update multiple fields
    public User updateUserProfile(Long id, UserUpdateDTO dto) {
        User user = userRepository.findById(id)
            .orElseThrow(() -> new UserNotFoundException(id));

        user.setFirstName(dto.getFirstName());
        user.setLastName(dto.getLastName());
        user.setPhoneNumber(dto.getPhoneNumber());
        user.setUpdatedAt(LocalDateTime.now());

        return userRepository.save(user);
    }

    // Partial update using Optional
    public User partialUpdateUser(Long id, UserUpdateDTO dto) {
        User user = userRepository.findById(id)
            .orElseThrow(() -> new UserNotFoundException(id));

        Optional.ofNullable(dto.getFirstName())
            .ifPresent(user::setFirstName);
        Optional.ofNullable(dto.getLastName())
            .ifPresent(user::setLastName);
        Optional.ofNullable(dto.getEmail())
            .ifPresent(user::setEmail);

        user.setUpdatedAt(LocalDateTime.now());

        return userRepository.save(user);
    }
}
```

### Batch Save Operations

```java
@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    // Save multiple entities
    public List<User> createUsers(List<UserDTO> userDTOs) {
        List<User> users = userDTOs.stream()
            .map(dto -> User.builder()
                .username(dto.getUsername())
                .email(dto.getEmail())
                .build())
            .collect(Collectors.toList());

        return userRepository.saveAll(users);
    }

    // Save and flush (immediate synchronization)
    public User createUserAndFlush(User user) {
        return userRepository.saveAndFlush(user);
    }

    // Batch save and flush
    public List<User> createUsersAndFlush(List<User> users) {
        return userRepository.saveAllAndFlush(users);
    }
}
```

### Update with @Query

For bulk updates without loading entities:

```java
public interface UserRepository extends JpaRepository<User, Long> {

    @Modifying
    @Transactional
    @Query("UPDATE User u SET u.status = :status WHERE u.id = :id")
    int updateUserStatus(@Param("id") Long id, @Param("status") String status);

    @Modifying
    @Transactional
    @Query("UPDATE User u SET u.active = :active WHERE u.lastLoginDate < :date")
    int deactivateInactiveUsers(
        @Param("active") boolean active,
        @Param("date") LocalDateTime date);

    @Modifying
    @Transactional
    @Query("UPDATE User u SET u.failedLoginAttempts = 0 WHERE u.id IN :ids")
    int resetFailedLoginAttempts(@Param("ids") List<Long> ids);
}
```

### saveAndFlush vs save

```java
@Service
public class ProductService {

    @Autowired
    private ProductRepository productRepository;

    // Regular save (buffered)
    @Transactional
    public Product createProduct(Product product) {
        // Changes might not be immediately visible in database
        // Flushed at transaction commit
        return productRepository.save(product);
    }

    // Save and flush (immediate)
    @Transactional
    public Product createProductAndFlush(Product product) {
        // Changes immediately written to database
        // Useful when you need the generated ID immediately
        // or for subsequent native queries
        Product saved = productRepository.saveAndFlush(product);

        // Now safe to use in native queries
        nativeQueryUsingProductId(saved.getId());

        return saved;
    }
}
```

---

## Find Methods

Spring Data JPA provides multiple ways to retrieve data from the database.

### Basic Find Operations

```java
@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    // Find by ID
    public User findById(Long id) {
        return userRepository.findById(id)
            .orElseThrow(() -> new UserNotFoundException(id));
    }

    // Find by ID with Optional
    public Optional<User> findByIdOptional(Long id) {
        return userRepository.findById(id);
    }

    // Find by ID or return default
    public User findByIdOrDefault(Long id) {
        return userRepository.findById(id)
            .orElse(getDefaultUser());
    }

    // Find by ID or create new
    public User findByIdOrCreate(Long id) {
        return userRepository.findById(id)
            .orElseGet(() -> createDefaultUser());
    }

    // Find all
    public List<User> findAll() {
        return userRepository.findAll();
    }

    // Find all by IDs
    public List<User> findAllByIds(List<Long> ids) {
        return userRepository.findAllById(ids);
    }

    // Check existence
    public boolean exists(Long id) {
        return userRepository.existsById(id);
    }

    // Count all
    public long count() {
        return userRepository.count();
    }
}
```

### Derived Query Methods

Spring Data JPA automatically implements methods based on naming conventions:

```java
public interface UserRepository extends JpaRepository<User, Long> {

    // Find by single field
    User findByEmail(String email);
    Optional<User> findByUsername(String username);
    List<User> findByLastName(String lastName);

    // Find by multiple fields (AND)
    User findByUsernameAndEmail(String username, String email);
    List<User> findByFirstNameAndLastName(String firstName, String lastName);

    // Find with OR
    List<User> findByUsernameOrEmail(String username, String email);

    // Comparison operators
    List<User> findByAgeGreaterThan(int age);
    List<User> findByAgeGreaterThanEqual(int age);
    List<User> findByAgeLessThan(int age);
    List<User> findByAgeLessThanEqual(int age);
    List<User> findBySalaryBetween(double min, double max);
    List<User> findByCreatedAtAfter(LocalDateTime date);
    List<User> findByCreatedAtBefore(LocalDateTime date);

    // String matching
    List<User> findByEmailContaining(String emailPart);
    List<User> findByEmailContainingIgnoreCase(String emailPart);
    List<User> findByUsernameStartingWith(String prefix);
    List<User> findByFirstNameEndingWith(String suffix);
    List<User> findByLastNameLike(String pattern);  // Use % wildcards

    // Null checks
    List<User> findByMiddleNameIsNull();
    List<User> findByMiddleNameIsNotNull();

    // Boolean
    List<User> findByActiveTrue();
    List<User> findByActiveFalse();
    List<User> findByActive(boolean active);

    // Collections
    List<User> findByRolesIn(List<String> roles);
    List<User> findByRolesNotIn(List<String> roles);

    // Ordering
    List<User> findByActiveOrderByCreatedAtDesc(boolean active);
    List<User> findByLastNameOrderByFirstNameAsc(String lastName);

    // Distinct
    List<User> findDistinctByLastName(String lastName);

    // Limiting results
    User findFirstByOrderByCreatedAtDesc();
    User findTopByOrderBySalaryDesc();
    List<User> findTop3ByOrderByScoreDesc();
    List<User> findFirst10ByActiveOrderByCreatedAtDesc(boolean active);

    // Nested properties
    List<User> findByAddress_City(String city);
    List<User> findByAddress_ZipCode(String zipCode);

    // Count
    long countByActive(boolean active);
    long countByDepartment(String department);

    // Exists
    boolean existsByEmail(String email);
    boolean existsByUsernameAndActive(String username, boolean active);
}
```

### Using @Query for Complex Queries

```java
public interface UserRepository extends JpaRepository<User, Long> {

    // JPQL Query
    @Query("SELECT u FROM User u WHERE u.email = ?1")
    User findByEmailQuery(String email);

    // JPQL with named parameters
    @Query("SELECT u FROM User u WHERE u.firstName = :firstName " +
           "AND u.lastName = :lastName")
    User findByFullName(
        @Param("firstName") String firstName,
        @Param("lastName") String lastName);

    // JPQL with JOIN
    @Query("SELECT u FROM User u JOIN u.roles r WHERE r.name = :roleName")
    List<User> findByRoleName(@Param("roleName") String roleName);

    // JPQL with LEFT JOIN FETCH (avoid N+1 problem)
    @Query("SELECT DISTINCT u FROM User u LEFT JOIN FETCH u.roles " +
           "WHERE u.active = true")
    List<User> findActiveUsersWithRoles();

    // Native SQL Query
    @Query(value = "SELECT * FROM users WHERE email LIKE %?1%",
           nativeQuery = true)
    List<User> searchByEmailNative(String emailPart);

    // Projection (selecting specific fields)
    @Query("SELECT u.id, u.username, u.email FROM User u WHERE u.active = true")
    List<Object[]> findActiveUserSummaries();

    // DTO Projection
    @Query("SELECT new com.example.dto.UserDTO(u.id, u.username, u.email) " +
           "FROM User u WHERE u.active = :active")
    List<UserDTO> findUserDTOs(@Param("active") boolean active);

    // Pagination with @Query
    @Query("SELECT u FROM User u WHERE u.department = :department")
    Page<User> findByDepartmentPaged(
        @Param("department") String department,
        Pageable pageable);

    // Count query
    @Query("SELECT COUNT(u) FROM User u WHERE u.createdAt >= :date")
    long countUsersCreatedAfter(@Param("date") LocalDateTime date);
}
```

### Pagination and Sorting

```java
@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    // Simple pagination
    public Page<User> getUsers(int page, int size) {
        Pageable pageable = PageRequest.of(page, size);
        return userRepository.findAll(pageable);
    }

    // Pagination with sorting
    public Page<User> getUsersSorted(int page, int size, String sortBy) {
        Pageable pageable = PageRequest.of(page, size,
            Sort.by(sortBy).ascending());
        return userRepository.findAll(pageable);
    }

    // Complex sorting
    public Page<User> getUsersComplexSort(int page, int size) {
        Sort sort = Sort.by("lastName").ascending()
            .and(Sort.by("firstName").ascending())
            .and(Sort.by("createdAt").descending());

        Pageable pageable = PageRequest.of(page, size, sort);
        return userRepository.findAll(pageable);
    }

    // Extract page information
    public Map<String, Object> getUsersWithMetadata(int page, int size) {
        Page<User> userPage = userRepository.findAll(
            PageRequest.of(page, size));

        Map<String, Object> response = new HashMap<>();
        response.put("users", userPage.getContent());
        response.put("currentPage", userPage.getNumber());
        response.put("totalItems", userPage.getTotalElements());
        response.put("totalPages", userPage.getTotalPages());
        response.put("hasNext", userPage.hasNext());
        response.put("hasPrevious", userPage.hasPrevious());

        return response;
    }
}
```

---

## Delete Operations

Spring Data JPA provides several methods for deleting entities.

### Basic Delete Operations

```java
@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    // Delete by ID
    public void deleteById(Long id) {
        userRepository.deleteById(id);
    }

    // Delete by ID with existence check
    public void deleteByIdSafe(Long id) {
        if (userRepository.existsById(id)) {
            userRepository.deleteById(id);
        } else {
            throw new UserNotFoundException(id);
        }
    }

    // Delete entity
    public void deleteUser(User user) {
        userRepository.delete(user);
    }

    // Delete multiple entities
    public void deleteUsers(List<User> users) {
        userRepository.deleteAll(users);
    }

    // Delete by IDs
    public void deleteByIds(List<Long> ids) {
        userRepository.deleteAllById(ids);
    }

    // Delete all
    public void deleteAll() {
        userRepository.deleteAll();
    }
}
```

### Batch Delete Operations

```java
@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    // Delete in batch (single SQL DELETE)
    @Transactional
    public void deleteBatch(List<User> users) {
        userRepository.deleteAllInBatch(users);
    }

    // Delete by IDs in batch
    @Transactional
    public void deleteByIdsBatch(List<Long> ids) {
        userRepository.deleteAllByIdInBatch(ids);
    }

    // Delete all in batch
    @Transactional
    public void deleteAllBatch() {
        userRepository.deleteAllInBatch();
    }
}
```

### Delete Performance Comparison

```java
// Method 1: deleteAll() - Slower
// Fetches all entities first, then deletes one by one
// Triggers lifecycle callbacks (@PreRemove)
userRepository.deleteAll();

// Method 2: deleteAllInBatch() - Faster
// Single DELETE query, no entity loading
// Does NOT trigger lifecycle callbacks
userRepository.deleteAllInBatch();
```

### Custom Delete Methods

```java
public interface UserRepository extends JpaRepository<User, Long> {

    // Derived delete method
    void deleteByEmail(String email);

    long deleteByActivefalse();

    void deleteByCreatedAtBefore(LocalDateTime date);

    // Delete with @Query
    @Modifying
    @Transactional
    @Query("DELETE FROM User u WHERE u.active = false")
    int deleteInactiveUsers();

    @Modifying
    @Transactional
    @Query("DELETE FROM User u WHERE u.lastLoginDate < :date")
    int deleteUsersInactiveSince(@Param("date") LocalDateTime date);

    // Native delete query
    @Modifying
    @Transactional
    @Query(value = "DELETE FROM users WHERE created_at < ?1",
           nativeQuery = true)
    int deleteOldUsersNative(LocalDateTime date);
}
```

### Soft Delete Pattern

Instead of physically deleting records, mark them as deleted:

```java
@Entity
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String username;

    private boolean deleted = false;

    private LocalDateTime deletedAt;

    // Other fields, getters, setters
}

public interface UserRepository extends JpaRepository<User, Long> {

    // Find only non-deleted users
    @Query("SELECT u FROM User u WHERE u.deleted = false")
    List<User> findAllActive();

    @Query("SELECT u FROM User u WHERE u.id = :id AND u.deleted = false")
    Optional<User> findActiveById(@Param("id") Long id);
}

@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    // Soft delete
    @Transactional
    public void softDelete(Long id) {
        User user = userRepository.findById(id)
            .orElseThrow(() -> new UserNotFoundException(id));

        user.setDeleted(true);
        user.setDeletedAt(LocalDateTime.now());
        userRepository.save(user);
    }

    // Restore
    @Transactional
    public void restore(Long id) {
        User user = userRepository.findById(id)
            .orElseThrow(() -> new UserNotFoundException(id));

        user.setDeleted(false);
        user.setDeletedAt(null);
        userRepository.save(user);
    }
}
```

### Delete with Relationships

```java
@Entity
public class Department {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;

    // orphanRemoval ensures employees are deleted when removed from collection
    @OneToMany(mappedBy = "department", cascade = CascadeType.ALL,
               orphanRemoval = true)
    private List<Employee> employees = new ArrayList<>();

    public void removeEmployee(Employee employee) {
        employees.remove(employee);
        employee.setDepartment(null);
    }
}

@Service
public class DepartmentService {

    @Autowired
    private DepartmentRepository departmentRepository;

    @Transactional
    public void deleteDepartment(Long id) {
        // All employees cascade deleted due to CascadeType.ALL
        departmentRepository.deleteById(id);
    }

    @Transactional
    public void removeEmployeeFromDepartment(Long deptId, Long empId) {
        Department dept = departmentRepository.findById(deptId)
            .orElseThrow(() -> new DepartmentNotFoundException(deptId));

        Employee emp = dept.getEmployees().stream()
            .filter(e -> e.getId().equals(empId))
            .findFirst()
            .orElseThrow(() -> new EmployeeNotFoundException(empId));

        // orphanRemoval = true ensures employee is deleted
        dept.removeEmployee(emp);
    }
}
```

---

## Summary

| Operation | Methods | Use Case |
|-----------|---------|----------|
| Create | save(), saveAll() | Insert new entities |
| Update | save(), saveAndFlush() | Modify existing entities |
| Read | findById(), findAll(), derived methods | Retrieve entities |
| Delete | deleteById(), deleteAll(), deleteAllInBatch() | Remove entities |
| Bulk Operations | saveAll(), deleteAllInBatch() | Process multiple entities efficiently |

## Next Topic

Continue to [Queries](./06-queries.md) to learn advanced querying techniques with Spring Data JPA.
