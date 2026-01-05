# OOP Advanced + Exceptions

## Learning Objectives
- Design with interfaces and default methods
- Implement generic classes and methods
- Create and process custom annotations
- Build robust exception handling systems

---

## Exercise 2.1: Interfaces (45 min)

Create a payment processing system using interfaces.

### Requirements

1. **Interface `Payable`**:
   - `double getPaymentAmount()`
   - Default method `void printPaymentInfo()`

2. **Interface `Taxable`**:
   - `double calculateTax()`
   - Static method `double getTaxRate()` returns 0.18 (18%)

3. **Classes implementing interfaces**:
   - `Invoice` implements `Payable` (partNumber, description, quantity, pricePerItem)
   - `Employee` implements `Payable`, `Taxable` (name, salary)

### Expected Usage
```java
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

### Submission
Files in `oop-advanced/payment/`:
- `Payable.java`
- `Taxable.java`
- `Invoice.java`
- `Employee.java`
- `PaymentTest.java`

---

## Exercise 2.2: Generics (45 min)

Create a generic `Pair<K, V>` class and a generic utility class.

### Requirements

1. **`Pair<K, V>` class**:
   - Fields: key (K), value (V)
   - Methods: getKey(), getValue(), setKey(), setValue()
   - Override equals(), hashCode(), toString()

2. **`Box<T>` class**:
   - Store single item of type T
   - Methods: set(), get(), isEmpty(), clear()

3. **`CollectionUtils` class** with static generic methods:
   - `<T> T findFirst(List<T> list, Predicate<T> predicate)`
   - `<T extends Comparable<T>> T findMax(List<T> list)`
   - `<T> List<T> filter(List<T> list, Predicate<T> predicate)`

### Expected Usage
```java
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

### Submission
Files in `oop-advanced/generics/`:
- `Pair.java`
- `Box.java`
- `CollectionUtils.java`
- `GenericsTest.java`

---

## Exercise 2.3: Custom Annotations (30 min)

Create and process custom annotations using reflection.

### Requirements

1. **Create `@Validate` annotation**:
   - Target: FIELD
   - Retention: RUNTIME
   - Elements: `min`, `max`, `message`

2. **Create `@NotNull` annotation**:
   - Target: FIELD
   - Retention: RUNTIME
   - Element: `message`

3. **Create `Validator` class**:
   - Method `validate(Object obj)` that uses reflection
   - Returns `List<String>` of validation errors

### Expected Usage
```java
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

### Submission
Files in `oop-advanced/annotations/`:
- `Validate.java`
- `NotNull.java`
- `Validator.java`
- `User.java`
- `AnnotationTest.java`

---

## Exercise 2.4: Exception Handling (1.5 hours)

Build a user registration system with comprehensive exception handling.

### Requirements

1. **Custom exceptions**:
   - `ValidationException` (checked) - for validation errors
   - `DuplicateUserException` (unchecked) - for duplicate emails
   - `DatabaseException` (checked) - for DB errors

2. **`User` class with validation**:
   - name: 2-50 chars
   - email: valid format, unique
   - password: min 8 chars, 1 uppercase, 1 number
   - age: 13+

3. **`UserService` class**:
   - `register(User user)` throws ValidationException, DuplicateUserException
   - `findByEmail(String email)` throws DatabaseException
   - Uses try-with-resources for any resources

4. **`UserController` class**:
   - Handles all exceptions appropriately
   - Returns meaningful error messages

### Expected Usage
```java
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

### Submission
Files in `oop-advanced/registration/`:
- `ValidationException.java`
- `DuplicateUserException.java`
- `DatabaseException.java`
- `User.java`
- `Result.java`
- `UserService.java`
- `UserController.java`
- `RegistrationTest.java`

---

## Submission Checklist

| Exercise | File(s) | Status |
|----------|---------|--------|
| 2.1 Interfaces | `payment/*.java` | ⬜ |
| 2.2 Generics | `generics/*.java` | ⬜ |
| 2.3 Custom Annotations | `annotations/*.java` | ⬜ |
| 2.4 Exception Handling | `registration/*.java` | ⬜ |

## Evaluation Criteria

- Interfaces correctly defined with default/static methods
- Generic classes properly parameterized
- Annotations have correct retention and target
- Reflection used properly in Validator
- Exception hierarchy properly designed
- All exceptions handled appropriately
- Code compiles and runs without errors
