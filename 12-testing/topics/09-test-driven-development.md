# Test Driven Development (TDD)

## What is Test Driven Development?

Test Driven Development (TDD) is a software development approach where tests are written before the actual code. Instead of writing code first and then testing it, TDD reverses the process: write a failing test, write minimal code to pass it, then refactor.

### The TDD Mantra

```
Red → Green → Refactor
```

1. **Red**: Write a failing test
2. **Green**: Write minimal code to make it pass
3. **Refactor**: Improve the code while keeping tests green

---

## The TDD Cycle

### Detailed Workflow

```
┌─────────────────────────────────────────┐
│  1. Write a Failing Test (Red)          │
│     - Test doesn't compile or fails     │
└─────────────┬───────────────────────────┘
              ↓
┌─────────────────────────────────────────┐
│  2. Write Minimal Code (Green)          │
│     - Just enough to pass the test     │
└─────────────┬───────────────────────────┘
              ↓
┌─────────────────────────────────────────┐
│  3. Refactor (Refactor)                 │
│     - Improve code quality              │
│     - Keep tests passing                │
└─────────────┬───────────────────────────┘
              ↓
              Back to step 1 (next feature)
```

### Time Investment

- **Red**: 20-30% of time
- **Green**: 30-40% of time
- **Refactor**: 30-40% of time

---

## Why Practice TDD?

### Benefits

1. **Better Design**
   - Forces you to think about API design first
   - Promotes loose coupling
   - Encourages single responsibility

2. **Higher Test Coverage**
   - Tests written for all code
   - No untested code paths
   - Better coverage naturally

3. **Confidence in Refactoring**
   - Safe to improve code
   - Tests catch regressions
   - Continuous improvement

4. **Living Documentation**
   - Tests show how to use the code
   - Always up-to-date
   - Executable specifications

5. **Fewer Bugs**
   - Catch issues early
   - Prevent regressions
   - Better quality code

6. **Faster Development**
   - Seems slower initially
   - Faster in the long run
   - Less debugging time

---

## TDD in Action: Complete Example

### Requirement: String Calculator

Create a `StringCalculator` with an `add` method that:
1. Takes a string of comma-separated numbers
2. Returns their sum
3. Empty string returns 0
4. Single number returns that number
5. Two numbers return their sum

### Step 1: Write First Failing Test (Red)

```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class StringCalculatorTest {

    @Test
    void shouldReturnZeroForEmptyString() {
        // ARRANGE
        StringCalculator calculator = new StringCalculator();

        // ACT
        int result = calculator.add("");

        // ASSERT
        assertEquals(0, result);
    }
}
```

**Result**: Test fails - `StringCalculator` class doesn't exist!

### Step 2: Write Minimal Code (Green)

```java
public class StringCalculator {

    public int add(String numbers) {
        return 0;  // Simplest code to pass the test
    }
}
```

**Result**: Test passes! ✓

### Step 3: Refactor (if needed)

Code is simple enough, no refactoring needed yet.

---

### Iteration 2: Single Number

#### Red - Write Test

```java
@Test
void shouldReturnNumberForSingleNumber() {
    StringCalculator calculator = new StringCalculator();
    assertEquals(5, calculator.add("5"));
}
```

**Result**: Test fails - returns 0 instead of 5

#### Green - Make It Pass

```java
public class StringCalculator {

    public int add(String numbers) {
        if (numbers.isEmpty()) {
            return 0;
        }
        return Integer.parseInt(numbers);
    }
}
```

**Result**: Both tests pass! ✓

#### Refactor

Code is still simple, no refactoring needed.

---

### Iteration 3: Two Numbers

#### Red - Write Test

```java
@Test
void shouldReturnSumForTwoNumbers() {
    StringCalculator calculator = new StringCalculator();
    assertEquals(8, calculator.add("3,5"));
}
```

**Result**: Test fails - throws NumberFormatException

#### Green - Make It Pass

```java
public class StringCalculator {

    public int add(String numbers) {
        if (numbers.isEmpty()) {
            return 0;
        }

        if (numbers.contains(",")) {
            String[] parts = numbers.split(",");
            int sum = 0;
            for (String part : parts) {
                sum += Integer.parseInt(part);
            }
            return sum;
        }

        return Integer.parseInt(numbers);
    }
}
```

**Result**: All tests pass! ✓

#### Refactor - Improve Code

```java
public class StringCalculator {

    public int add(String numbers) {
        if (numbers.isEmpty()) {
            return 0;
        }

        return Arrays.stream(numbers.split(","))
                    .mapToInt(Integer::parseInt)
                    .sum();
    }
}
```

**Result**: All tests still pass! ✓

---

### Iteration 4: Multiple Numbers

#### Red - Write Test

```java
@Test
void shouldReturnSumForMultipleNumbers() {
    StringCalculator calculator = new StringCalculator();
    assertEquals(15, calculator.add("1,2,3,4,5"));
}
```

**Result**: Test passes already! The refactored code handles this case.

---

### Iteration 5: Handle Newlines

New requirement: Allow newlines as delimiters (e.g., "1\n2,3" = 6)

#### Red - Write Test

```java
@Test
void shouldHandleNewlineDelimiter() {
    StringCalculator calculator = new StringCalculator();
    assertEquals(6, calculator.add("1\n2,3"));
}
```

**Result**: Test fails

#### Green - Make It Pass

```java
public class StringCalculator {

    public int add(String numbers) {
        if (numbers.isEmpty()) {
            return 0;
        }

        return Arrays.stream(numbers.split("[,\n]"))
                    .mapToInt(Integer::parseInt)
                    .sum();
    }
}
```

**Result**: All tests pass! ✓

---

### Iteration 6: Negative Numbers

New requirement: Throw exception for negative numbers

#### Red - Write Test

```java
@Test
void shouldThrowExceptionForNegativeNumbers() {
    StringCalculator calculator = new StringCalculator();

    Exception exception = assertThrows(IllegalArgumentException.class, () -> {
        calculator.add("1,-2,3");
    });

    assertEquals("Negatives not allowed: -2", exception.getMessage());
}
```

**Result**: Test fails - no exception thrown

#### Green - Make It Pass

```java
public class StringCalculator {

    public int add(String numbers) {
        if (numbers.isEmpty()) {
            return 0;
        }

        String[] parts = numbers.split("[,\n]");
        int sum = 0;

        for (String part : parts) {
            int number = Integer.parseInt(part);
            if (number < 0) {
                throw new IllegalArgumentException("Negatives not allowed: " + number);
            }
            sum += number;
        }

        return sum;
    }
}
```

**Result**: All tests pass! ✓

---

### Final Complete Test Suite

```java
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class StringCalculatorTest {

    private StringCalculator calculator;

    @BeforeEach
    void setUp() {
        calculator = new StringCalculator();
    }

    @Test
    void shouldReturnZeroForEmptyString() {
        assertEquals(0, calculator.add(""));
    }

    @Test
    void shouldReturnNumberForSingleNumber() {
        assertEquals(5, calculator.add("5"));
    }

    @Test
    void shouldReturnSumForTwoNumbers() {
        assertEquals(8, calculator.add("3,5"));
    }

    @Test
    void shouldReturnSumForMultipleNumbers() {
        assertEquals(15, calculator.add("1,2,3,4,5"));
    }

    @Test
    void shouldHandleNewlineDelimiter() {
        assertEquals(6, calculator.add("1\n2,3"));
    }

    @Test
    void shouldThrowExceptionForNegativeNumbers() {
        Exception exception = assertThrows(IllegalArgumentException.class, () -> {
            calculator.add("1,-2,3");
        });

        assertEquals("Negatives not allowed: -2", exception.getMessage());
    }

    @Test
    void shouldThrowExceptionWithAllNegativeNumbers() {
        Exception exception = assertThrows(IllegalArgumentException.class, () -> {
            calculator.add("1,-2,-3,4");
        });

        assertTrue(exception.getMessage().contains("-2"));
        assertTrue(exception.getMessage().contains("-3"));
    }
}
```

---

## TDD Best Practices

### 1. Write the Simplest Test First

```java
// Good - start simple
@Test
void shouldReturnZeroForEmptyString() {
    assertEquals(0, calculator.add(""));
}

// Bad - too complex for first test
@Test
void shouldHandleComplexScenarioWithMultipleDelimitersAndNegatives() {
    // Too much at once!
}
```

### 2. One Test at a Time

```java
// Good - one concept per test
@Test
void shouldReturnSumForTwoNumbers() {
    assertEquals(8, calculator.add("3,5"));
}

@Test
void shouldHandleNewlines() {
    assertEquals(6, calculator.add("1\n2,3"));
}

// Bad - multiple concepts
@Test
void shouldHandleEverything() {
    assertEquals(8, calculator.add("3,5"));
    assertEquals(6, calculator.add("1\n2,3"));
    assertEquals(0, calculator.add(""));
}
```

### 3. Write Just Enough Code

```java
// Good - minimal code to pass
public int add(String numbers) {
    if (numbers.isEmpty()) {
        return 0;
    }
    return Integer.parseInt(numbers);
}

// Bad - over-engineering before needed
public int add(String numbers) {
    if (numbers.isEmpty()) return 0;

    // Complex parsing logic that's not needed yet
    String[] delimiters = extractCustomDelimiters(numbers);
    numbers = normalizeInput(numbers);
    List<Integer> nums = parseWithDelimiters(numbers, delimiters);
    return calculateSum(nums);
}
```

### 4. Refactor with Confidence

```java
// Before refactoring - all tests pass
public int add(String numbers) {
    if (numbers.isEmpty()) return 0;

    String[] parts = numbers.split(",");
    int sum = 0;
    for (String part : parts) {
        sum += Integer.parseInt(part);
    }
    return sum;
}

// After refactoring - all tests still pass
public int add(String numbers) {
    if (numbers.isEmpty()) return 0;

    return Arrays.stream(numbers.split(","))
                .mapToInt(Integer::parseInt)
                .sum();
}
```

### 5. Keep Tests Fast

```java
// Good - fast unit test
@Test
void testCalculation() {
    int result = calculator.add("1,2,3");
    assertEquals(6, result);
}

// Bad - slow test
@Test
void testCalculation() {
    // Don't do this in unit tests
    Thread.sleep(1000);
    database.connect();
    // ...
}
```

---

## TDD Example: Shopping Cart

### Requirement 1: Empty cart has zero total

#### Red

```java
@Test
void emptyCartShouldHaveZeroTotal() {
    ShoppingCart cart = new ShoppingCart();
    assertEquals(0.0, cart.getTotal(), 0.01);
}
```

#### Green

```java
public class ShoppingCart {
    public double getTotal() {
        return 0.0;
    }
}
```

### Requirement 2: Add item to cart

#### Red

```java
@Test
void shouldAddItemToCart() {
    ShoppingCart cart = new ShoppingCart();
    cart.addItem(new Item("Book", 29.99));
    assertEquals(29.99, cart.getTotal(), 0.01);
}
```

#### Green

```java
public class ShoppingCart {
    private List<Item> items = new ArrayList<>();

    public void addItem(Item item) {
        items.add(item);
    }

    public double getTotal() {
        return items.stream()
                   .mapToDouble(Item::getPrice)
                   .sum();
    }
}
```

### Requirement 3: Multiple items

#### Red

```java
@Test
void shouldCalculateTotalForMultipleItems() {
    ShoppingCart cart = new ShoppingCart();
    cart.addItem(new Item("Book", 29.99));
    cart.addItem(new Item("Pen", 4.99));
    assertEquals(34.98, cart.getTotal(), 0.01);
}
```

**Result**: Test passes already! ✓

### Requirement 4: Apply discount

#### Red

```java
@Test
void shouldApplyPercentageDiscount() {
    ShoppingCart cart = new ShoppingCart();
    cart.addItem(new Item("Book", 100.0));
    cart.applyDiscount(10); // 10% discount
    assertEquals(90.0, cart.getTotal(), 0.01);
}
```

#### Green

```java
public class ShoppingCart {
    private List<Item> items = new ArrayList<>();
    private double discountPercent = 0;

    public void addItem(Item item) {
        items.add(item);
    }

    public void applyDiscount(double percent) {
        this.discountPercent = percent;
    }

    public double getTotal() {
        double subtotal = items.stream()
                              .mapToDouble(Item::getPrice)
                              .sum();
        return subtotal * (1 - discountPercent / 100);
    }
}
```

---

## Common TDD Pitfalls

### 1. Writing Too Much Code at Once

```java
// Bad - implementing everything before tests
public class UserService {
    // Tons of methods without tests
    public User create(User user) { /* ... */ }
    public User update(User user) { /* ... */ }
    public void delete(Long id) { /* ... */ }
    public List<User> findAll() { /* ... */ }
    public User findById(Long id) { /* ... */ }
}

// Good - one test, one method at a time
public class UserService {
    public User create(User user) {
        // Implemented after writing test
    }
    // Next method after next test
}
```

### 2. Not Refactoring

```java
// Bad - messy code that passes tests but isn't refactored
public int calculate(String input) {
    if (input.isEmpty()) return 0;
    String[] p = input.split(",");
    int s = 0;
    for (int i = 0; i < p.length; i++) {
        s = s + Integer.parseInt(p[i]);
    }
    return s;
}

// Good - refactored for clarity
public int calculate(String input) {
    if (input.isEmpty()) {
        return 0;
    }

    return Arrays.stream(input.split(","))
                .mapToInt(Integer::parseInt)
                .sum();
}
```

### 3. Testing Implementation Details

```java
// Bad - testing private methods
@Test
void testPrivateMethod() {
    // Using reflection to test private method
}

// Good - test public API
@Test
void testPublicBehavior() {
    String result = service.process(input);
    assertEquals(expected, result);
}
```

### 4. Skipping the Red Phase

```java
// Bad - writing code before test
public int add(int a, int b) {
    return a + b;
}

@Test
void testAdd() {
    assertEquals(8, calculator.add(5, 3));
}

// Good - test first, see it fail, then implement
@Test
void testAdd() {
    assertEquals(8, calculator.add(5, 3)); // Fails first!
}

public int add(int a, int b) {
    return a + b; // Then implement
}
```

---

## TDD vs Traditional Testing

| Aspect | Traditional | TDD |
|--------|-------------|-----|
| **When tests written** | After code | Before code |
| **Focus** | Verification | Design |
| **Coverage** | Variable | High |
| **Refactoring** | Risky | Safe |
| **Design quality** | Variable | Usually better |
| **Learning curve** | Easier | Steeper |

---

## When to Use TDD

### Great For:

- New features and projects
- Complex business logic
- APIs and libraries
- Refactoring existing code
- Bug fixing (write test that reproduces bug)

### Consider Alternatives For:

- UI/Visual components (use other testing strategies)
- Proof of concepts/spikes
- Very simple CRUD operations
- Legacy code without tests (use other strategies first)

---

## The Three Laws of TDD

According to Robert C. Martin (Uncle Bob):

1. **You may not write production code until you have written a failing unit test**

2. **You may not write more of a unit test than is sufficient to fail**
   - Compilation failures are failures

3. **You may not write more production code than is sufficient to pass the currently failing test**

---

## TDD Kata Practice

### Kata: FizzBuzz

Write a program that:
- Prints numbers from 1 to 100
- For multiples of 3, print "Fizz"
- For multiples of 5, print "Buzz"
- For multiples of both, print "FizzBuzz"

#### Test-First Approach

```java
class FizzBuzzTest {

    @Test
    void shouldReturnNumberForRegularNumber() {
        assertEquals("1", fizzBuzz(1));
        assertEquals("2", fizzBuzz(2));
    }

    @Test
    void shouldReturnFizzForMultiplesOf3() {
        assertEquals("Fizz", fizzBuzz(3));
        assertEquals("Fizz", fizzBuzz(6));
    }

    @Test
    void shouldReturnBuzzForMultiplesOf5() {
        assertEquals("Buzz", fizzBuzz(5));
        assertEquals("Buzz", fizzBuzz(10));
    }

    @Test
    void shouldReturnFizzBuzzForMultiplesOf15() {
        assertEquals("FizzBuzz", fizzBuzz(15));
        assertEquals("FizzBuzz", fizzBuzz(30));
    }

    private String fizzBuzz(int number) {
        if (number % 15 == 0) return "FizzBuzz";
        if (number % 3 == 0) return "Fizz";
        if (number % 5 == 0) return "Buzz";
        return String.valueOf(number);
    }
}
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| **TDD Cycle** | Red → Green → Refactor |
| **Benefits** | Better design, higher coverage, confidence |
| **Best Practices** | Simple tests first, minimal code, refactor |
| **Three Laws** | Test first, minimal test, minimal code |
| **When to Use** | New features, complex logic, bug fixes |

### Key Takeaways

1. Write the test before the code
2. See the test fail (Red)
3. Write minimal code to pass (Green)
4. Refactor while keeping tests green
5. Repeat for each new feature

### The TDD Mindset

```
Traditional: Code → Test → Hope it works
TDD: Test → Code → Know it works
```

---

## Additional Resources

### Books
- "Test Driven Development: By Example" by Kent Beck
- "Growing Object-Oriented Software, Guided by Tests" by Steve Freeman & Nat Pryce

### Practice
- [Coding Dojo Kata](http://codingdojo.org/kata/)
- [Cyber-Dojo](https://cyber-dojo.org/)
- [Code Katas](http://codekata.com/)

### Online Resources
- [TDD by Example - Martin Fowler](https://martinfowler.com/bliki/TestDrivenDevelopment.html)
- [The Three Laws of TDD - Uncle Bob](http://butunclebob.com/ArticleS.UncleBob.TheThreeRulesOfTdd)

---

## Next Steps

Now that you've completed the Testing module, you should:

1. Practice TDD with simple katas
2. Apply TDD to your projects
3. Review test coverage in existing code
4. Learn advanced testing topics (integration testing, E2E testing)
5. Continue to the next module in the curriculum

---

**Time Estimate:** 3 days | **Difficulty:** Intermediate | **Prerequisites:** JUnit, Mockito
