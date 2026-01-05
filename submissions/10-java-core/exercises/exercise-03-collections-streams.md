# Collections + Functional + Streams

## Learning Objectives
- Master Java Collections Framework
- Convert imperative code to functional style
- Handle null safely with Optional
- Process data efficiently with Stream API

---

## Exercise 3.1: Collections CRUD (1 hour)

Build a product inventory system using different collections.

### Requirements

1. **`Product` class**:
   - Fields: id, name, category, price, quantity
   - Implement `Comparable<Product>` (by name)
   - Override `equals()` and `hashCode()` (by id)

2. **`Inventory` class** using:
   - `List<Product>` for maintaining order
   - `Set<String>` for unique categories
   - `Map<String, Product>` for id-based lookup
   - `Queue<Product>` for low-stock alerts

3. **Operations**:
   - Add, update, delete products
   - Find by id, by category
   - Get all products sorted by price
   - Get low stock products (quantity < 10)

### Expected Usage
```java
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

### Submission
Files in `collections-streams/inventory/`:
- `Product.java`
- `Inventory.java`
- `InventoryTest.java`

---

## Exercise 3.2: Lambdas & Method References (45 min)

Refactor code to use lambdas and method references.

### Task
Convert the following code to use lambdas/method references:

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

### Expected Solutions Use
- Lambda expressions
- Method references (static, instance, constructor)
- Functional interfaces (Predicate, Function, Consumer)

### Submission
- File: `collections-streams/LambdaRefactor.java`
- Include both original and refactored code with comments

---

## Exercise 3.3: Optional (30 min)

Refactor null-handling code to use Optional.

### Requirements

1. **`UserRepository`**:
   - `Optional<User> findById(Long id)`
   - `Optional<User> findByEmail(String email)`

2. **`User` class**:
   - `Optional<String> getMiddleName()`
   - `Optional<Address> getAddress()`

3. **`Address` class**:
   - `Optional<String> getApartment()`

4. **Chain operations** to get user's apartment number or default:

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

### Submission
Files in `collections-streams/optional/`:
- `User.java`
- `Address.java`
- `UserRepository.java`
- `OptionalTest.java`

---

## Exercise 3.4: Stream API (1.5 hours)

Process employee data using Stream API.

### Setup
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

### Tasks

1. **Filter & Sort**: Get engineering employees sorted by salary (descending)

2. **Map & Collect**: Get list of all employee names in uppercase

3. **Grouping**: Group employees by department

4. **Statistics**:
   - Total salary expense
   - Average salary by department
   - Highest paid employee

5. **FlatMap**: Get all unique skills across all employees

6. **Partitioning**: Partition employees by salary > 70000

7. **Reduce**: Calculate total years of service

8. **Complex**: Find departments where average salary > 70000

### Expected Outputs
```java
// 1. [Diana, Alice, Bob]
// 2. [ALICE, BOB, CHARLIE, DIANA, EVE, FRANK]
// 3. {Engineering=[Alice, Bob, Diana], Sales=[Charlie, Frank], HR=[Eve]}
// 4. Total: $445000, Avg by dept: {Engineering=85000, Sales=67500, HR=55000}
// 5. [Java, Python, JavaScript, Kotlin, Go, Communication, CRM, Recruiting, Negotiation]
// 6. {true=[Alice, Bob, Diana], false=[Charlie, Eve, Frank]}
// 7. 29 years
// 8. [Engineering]
```

### Submission
- File: `collections-streams/StreamExercise.java`
- Each task should be a separate method with clear output

---

## Submission Checklist

| Exercise | File(s) | Status |
|----------|---------|--------|
| 3.1 Collections CRUD | `inventory/*.java` | ⬜ |
| 3.2 Lambdas & Method References | `LambdaRefactor.java` | ⬜ |
| 3.3 Optional | `optional/*.java` | ⬜ |
| 3.4 Stream API | `StreamExercise.java` | ⬜ |

## Evaluation Criteria

- Collections used appropriately for each use case
- Lambdas are concise and readable
- Method references used where appropriate
- Optional chaining avoids null checks
- Stream operations are efficient (no unnecessary intermediate operations)
- Collectors used correctly for grouping/partitioning
- Code compiles and produces correct output
