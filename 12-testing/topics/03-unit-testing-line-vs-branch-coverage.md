# Unit Testing Line Vs Branch Coverage

## What is Code Coverage?

Code coverage is a metric that measures the percentage of your source code that is executed when your test suite runs. It helps identify untested parts of a codebase and is a useful indicator of test completeness.

### Why Code Coverage Matters

- **Identifies untested code**: Shows which parts of your code lack tests
- **Improves code quality**: Encourages comprehensive testing
- **Prevents regressions**: More coverage means fewer bugs slip through
- **Builds confidence**: Higher coverage provides assurance during refactoring

**Important Note**: High code coverage does not guarantee bug-free code. It only measures what code is executed, not whether it's tested correctly.

---

## Types of Code Coverage

### 1. Line Coverage (Statement Coverage)

Line coverage measures the percentage of executable lines of code that are executed during testing.

```java
public int calculateDiscount(int price, boolean isPremium) {
    int discount = 0;                    // Line 1
    if (isPremium) {                     // Line 2
        discount = price * 20 / 100;     // Line 3
    }
    return discount;                     // Line 4
}
```

**Test Example:**
```java
@Test
void testPremiumDiscount() {
    int result = calculateDiscount(100, true);
    assertEquals(20, result);
}
```

**Coverage Result:**
- Lines executed: 1, 2, 3, 4
- Line coverage: 100% (4/4 lines)

### 2. Branch Coverage (Decision Coverage)

Branch coverage measures the percentage of decision branches (if/else, switch, loops) that are executed during testing.

```java
public int calculateDiscount(int price, boolean isPremium) {
    int discount = 0;
    if (isPremium) {              // Branch point
        discount = price * 20 / 100;  // True branch
    }                                // False branch (implicit else)
    return discount;
}
```

**With only the premium test:**
- True branch: Tested
- False branch: NOT tested
- Branch coverage: 50% (1/2 branches)

**To achieve 100% branch coverage:**
```java
@Test
void testPremiumDiscount() {
    int result = calculateDiscount(100, true);
    assertEquals(20, result);
}

@Test
void testNonPremiumDiscount() {
    int result = calculateDiscount(100, false);
    assertEquals(0, result);
}
```

Now branch coverage: 100% (2/2 branches)

### 3. Other Coverage Types

| Coverage Type | What It Measures |
|---------------|------------------|
| **Function Coverage** | Percentage of functions/methods called |
| **Condition Coverage** | Individual boolean sub-expressions in decisions |
| **Path Coverage** | All possible paths through the code |
| **Statement Coverage** | Same as line coverage |

---

## Line Coverage vs Branch Coverage

### Detailed Comparison

| Aspect | Line Coverage | Branch Coverage |
|--------|---------------|-----------------|
| **What it measures** | Executable lines executed | Decision paths taken |
| **Granularity** | Individual statements | Control flow decisions |
| **Thoroughness** | Less comprehensive | More comprehensive |
| **Difficulty** | Easier to achieve | Harder to achieve |
| **Value** | Basic safety net | Better quality indicator |

### Example Demonstrating the Difference

```java
public class DiscountCalculator {

    public double calculateDiscount(double price, String customerType, boolean hasVoucher) {
        double discount = 0.0;                           // Line 1

        if (customerType.equals("PREMIUM")) {            // Line 2 - Branch A
            discount = price * 0.20;                     // Line 3
        } else if (customerType.equals("REGULAR")) {     // Line 4 - Branch B
            discount = price * 0.10;                     // Line 5
        }

        if (hasVoucher) {                                // Line 6 - Branch C
            discount += 5.0;                             // Line 7
        }

        return discount;                                 // Line 8
    }
}
```

### Test Case 1: Basic Line Coverage

```java
@Test
void testPremiumCustomerWithVoucher() {
    DiscountCalculator calc = new DiscountCalculator();
    double result = calc.calculateDiscount(100.0, "PREMIUM", true);
    assertEquals(25.0, result, 0.01);
}
```

**Coverage Analysis:**
- **Lines executed**: 1, 2, 3, 6, 7, 8 → **75% line coverage** (6/8 lines)
- **Branches covered**:
  - Branch A (PREMIUM): True ✓
  - Branch A (PREMIUM): False ✗
  - Branch B (REGULAR): True ✗
  - Branch B (REGULAR): False ✗
  - Branch C (hasVoucher): True ✓
  - Branch C (hasVoucher): False ✗
  - **Branch coverage**: **33%** (2/6 branches)

### Complete Test Suite for 100% Coverage

```java
class DiscountCalculatorTest {

    private DiscountCalculator calculator;

    @BeforeEach
    void setUp() {
        calculator = new DiscountCalculator();
    }

    @Test
    @DisplayName("Premium customer with voucher")
    void testPremiumWithVoucher() {
        // Tests: PREMIUM=true, hasVoucher=true
        double result = calculator.calculateDiscount(100.0, "PREMIUM", true);
        assertEquals(25.0, result, 0.01);
    }

    @Test
    @DisplayName("Premium customer without voucher")
    void testPremiumWithoutVoucher() {
        // Tests: PREMIUM=true, hasVoucher=false
        double result = calculator.calculateDiscount(100.0, "PREMIUM", false);
        assertEquals(20.0, result, 0.01);
    }

    @Test
    @DisplayName("Regular customer with voucher")
    void testRegularWithVoucher() {
        // Tests: REGULAR=true, hasVoucher=true
        double result = calculator.calculateDiscount(100.0, "REGULAR", true);
        assertEquals(15.0, result, 0.01);
    }

    @Test
    @DisplayName("Regular customer without voucher")
    void testRegularWithoutVoucher() {
        // Tests: REGULAR=false, hasVoucher=false
        double result = calculator.calculateDiscount(100.0, "REGULAR", false);
        assertEquals(10.0, result, 0.01);
    }

    @Test
    @DisplayName("Guest customer without voucher")
    void testGuestWithoutVoucher() {
        // Tests: neither PREMIUM nor REGULAR, hasVoucher=false
        double result = calculator.calculateDiscount(100.0, "GUEST", false);
        assertEquals(0.0, result, 0.01);
    }

    @Test
    @DisplayName("Guest customer with voucher")
    void testGuestWithVoucher() {
        // Tests: neither PREMIUM nor REGULAR, hasVoucher=true
        double result = calculator.calculateDiscount(100.0, "GUEST", true);
        assertEquals(5.0, result, 0.01);
    }
}
```

**Final Coverage:**
- **Line Coverage**: 100% (8/8 lines)
- **Branch Coverage**: 100% (all decision paths tested)

---

## Complex Branch Coverage Example

### Multiple Conditions

```java
public class OrderValidator {

    public boolean isValidOrder(Order order) {
        // Multiple conditions in one if statement
        if (order != null && order.getItems().size() > 0 && order.getTotalPrice() > 0) {
            return true;
        }
        return false;
    }

    public String getShippingMethod(double weight, boolean isExpress, String destination) {
        if (weight > 50 && isExpress) {              // Branch 1
            return "FREIGHT_EXPRESS";
        } else if (weight > 50) {                    // Branch 2
            return "FREIGHT_STANDARD";
        } else if (isExpress && destination.equals("LOCAL")) {  // Branch 3
            return "COURIER_EXPRESS";
        } else if (isExpress) {                      // Branch 4
            return "AIR_EXPRESS";
        } else {                                     // Branch 5
            return "STANDARD";
        }
    }
}
```

### Comprehensive Tests for Branch Coverage

```java
class OrderValidatorTest {

    private OrderValidator validator;

    @BeforeEach
    void setUp() {
        validator = new OrderValidator();
    }

    // Testing isValidOrder - 4 branches to cover
    @Test
    void testValidOrder() {
        Order order = new Order();
        order.addItem(new Item("Product", 10.0));
        assertTrue(validator.isValidOrder(order));
    }

    @Test
    void testNullOrder() {
        assertFalse(validator.isValidOrder(null));
    }

    @Test
    void testOrderWithNoItems() {
        Order order = new Order();
        assertFalse(validator.isValidOrder(order));
    }

    @Test
    void testOrderWithZeroPrice() {
        Order order = new Order();
        order.addItem(new Item("Free Item", 0.0));
        assertFalse(validator.isValidOrder(order));
    }

    // Testing getShippingMethod - 5 branches to cover
    @Test
    void testHeavyExpressShipping() {
        String result = validator.getShippingMethod(60, true, "REMOTE");
        assertEquals("FREIGHT_EXPRESS", result);
    }

    @Test
    void testHeavyStandardShipping() {
        String result = validator.getShippingMethod(60, false, "REMOTE");
        assertEquals("FREIGHT_STANDARD", result);
    }

    @Test
    void testLightExpressLocalShipping() {
        String result = validator.getShippingMethod(30, true, "LOCAL");
        assertEquals("COURIER_EXPRESS", result);
    }

    @Test
    void testLightExpressRemoteShipping() {
        String result = validator.getShippingMethod(30, true, "REMOTE");
        assertEquals("AIR_EXPRESS", result);
    }

    @Test
    void testStandardShipping() {
        String result = validator.getShippingMethod(30, false, "REMOTE");
        assertEquals("STANDARD", result);
    }
}
```

---

## Measuring Code Coverage

### Using JaCoCo (Java Code Coverage)

JaCoCo is the most popular code coverage tool for Java.

#### Maven Configuration

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.jacoco</groupId>
            <artifactId>jacoco-maven-plugin</artifactId>
            <version>0.8.10</version>
            <executions>
                <execution>
                    <goals>
                        <goal>prepare-agent</goal>
                    </goals>
                </execution>
                <execution>
                    <id>report</id>
                    <phase>test</phase>
                    <goals>
                        <goal>report</goal>
                    </goals>
                </execution>
                <execution>
                    <id>jacoco-check</id>
                    <goals>
                        <goal>check</goal>
                    </goals>
                    <configuration>
                        <rules>
                            <rule>
                                <element>PACKAGE</element>
                                <limits>
                                    <limit>
                                        <counter>LINE</counter>
                                        <value>COVEREDRATIO</value>
                                        <minimum>0.80</minimum>
                                    </limit>
                                    <limit>
                                        <counter>BRANCH</counter>
                                        <value>COVEREDRATIO</value>
                                        <minimum>0.80</minimum>
                                    </limit>
                                </limits>
                            </rule>
                        </rules>
                    </configuration>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

#### Gradle Configuration

```gradle
plugins {
    id 'java'
    id 'jacoco'
}

jacoco {
    toolVersion = "0.8.10"
}

jacocoTestReport {
    reports {
        xml.required = true
        html.required = true
    }
}

jacocoTestCoverageVerification {
    violationRules {
        rule {
            limit {
                counter = 'LINE'
                value = 'COVEREDRATIO'
                minimum = 0.80
            }
        }
        rule {
            limit {
                counter = 'BRANCH'
                value = 'COVEREDRATIO'
                minimum = 0.80
            }
        }
    }
}

test {
    finalizedBy jacocoTestReport
}
```

### Running Coverage Reports

**Maven:**
```bash
mvn clean test
mvn jacoco:report

# View report at: target/site/jacoco/index.html
```

**Gradle:**
```bash
gradle test jacocoTestReport

# View report at: build/reports/jacoco/test/html/index.html
```

### Reading Coverage Reports

JaCoCo HTML report shows:

- **Green**: Fully covered
- **Yellow**: Partially covered (some branches)
- **Red**: Not covered

**Coverage Metrics:**
- **Instruction Coverage**: Bytecode instructions executed
- **Branch Coverage**: Decision branches taken
- **Line Coverage**: Source lines executed
- **Complexity Coverage**: Cyclomatic complexity paths
- **Method Coverage**: Methods invoked
- **Class Coverage**: Classes loaded

---

## Understanding Coverage Gaps

### Example: Missing Edge Cases

```java
public class DateValidator {

    public boolean isValidDate(int day, int month, int year) {
        if (month < 1 || month > 12) {
            return false;
        }

        if (day < 1 || day > 31) {
            return false;
        }

        // February special case
        if (month == 2) {
            if (isLeapYear(year)) {
                return day <= 29;
            } else {
                return day <= 28;
            }
        }

        // Months with 30 days
        if (month == 4 || month == 6 || month == 9 || month == 11) {
            return day <= 30;
        }

        return true;
    }

    private boolean isLeapYear(int year) {
        return (year % 4 == 0 && year % 100 != 0) || (year % 400 == 0);
    }
}
```

### Insufficient Tests (Low Branch Coverage)

```java
@Test
void testValidDate() {
    DateValidator validator = new DateValidator();
    assertTrue(validator.isValidDate(15, 6, 2023));
}
```

**Coverage:** High line coverage, but LOW branch coverage!

### Complete Tests (100% Branch Coverage)

```java
class DateValidatorTest {

    private DateValidator validator;

    @BeforeEach
    void setUp() {
        validator = new DateValidator();
    }

    @Test
    void testValidDate() {
        assertTrue(validator.isValidDate(15, 6, 2023));
    }

    @Test
    void testInvalidMonth() {
        assertFalse(validator.isValidDate(15, 13, 2023));
        assertFalse(validator.isValidDate(15, 0, 2023));
    }

    @Test
    void testInvalidDay() {
        assertFalse(validator.isValidDate(0, 6, 2023));
        assertFalse(validator.isValidDate(32, 6, 2023));
    }

    @Test
    void testFebruaryLeapYear() {
        assertTrue(validator.isValidDate(29, 2, 2020));
        assertFalse(validator.isValidDate(30, 2, 2020));
    }

    @Test
    void testFebruaryNonLeapYear() {
        assertFalse(validator.isValidDate(29, 2, 2021));
        assertTrue(validator.isValidDate(28, 2, 2021));
    }

    @Test
    void testThirtyDayMonths() {
        assertTrue(validator.isValidDate(30, 4, 2023));
        assertFalse(validator.isValidDate(31, 4, 2023));
        assertTrue(validator.isValidDate(30, 11, 2023));
    }

    @Test
    void testThirtyOneDayMonths() {
        assertTrue(validator.isValidDate(31, 1, 2023));
        assertTrue(validator.isValidDate(31, 12, 2023));
    }
}
```

---

## Coverage Goals and Best Practices

### Recommended Coverage Levels

| Coverage Type | Minimum Target | Good Target | Excellent |
|---------------|----------------|-------------|-----------|
| Line Coverage | 70% | 80% | 90%+ |
| Branch Coverage | 60% | 75% | 85%+ |

### What to Aim For

1. **Critical Code**: 90%+ branch coverage
   - Business logic
   - Security features
   - Data validation

2. **Standard Code**: 75-85% branch coverage
   - Service layers
   - Controllers
   - Utilities

3. **Lower Priority**: 60-70% coverage
   - Simple getters/setters
   - Configuration classes
   - DTOs

### Best Practices

1. **Focus on Branch Coverage**
   - More meaningful than line coverage
   - Catches edge cases
   - Better quality indicator

2. **Don't Chase 100%**
   - Diminishing returns after 85-90%
   - Some code is hard to test (UI, external APIs)
   - Focus on meaningful tests

3. **Use Coverage as a Guide**
   - Identify untested code
   - Don't write tests just for coverage
   - Quality > Quantity

4. **Exclude Appropriate Code**
   ```xml
   <configuration>
       <excludes>
           <exclude>**/dto/**</exclude>
           <exclude>**/config/**</exclude>
           <exclude>**/Application.class</exclude>
       </excludes>
   </configuration>
   ```

5. **Monitor Trends**
   - Track coverage over time
   - Set minimum thresholds in CI/CD
   - Prevent coverage regression

---

## Coverage in CI/CD

### Fail Build on Low Coverage

**Maven:**
```xml
<execution>
    <id>check</id>
    <goals>
        <goal>check</goal>
    </goals>
    <configuration>
        <rules>
            <rule>
                <limits>
                    <limit>
                        <counter>BRANCH</counter>
                        <value>COVEREDRATIO</value>
                        <minimum>0.80</minimum>
                    </limit>
                </limits>
            </rule>
        </rules>
    </configuration>
</execution>
```

---

## Summary

| Concept | Key Points |
|---------|------------|
| Line Coverage | Measures executed lines, easier to achieve |
| Branch Coverage | Measures decision paths, more comprehensive |
| Tools | JaCoCo for Java, integrated with Maven/Gradle |
| Goals | 80% line, 75% branch for most projects |
| Best Practice | Focus on meaningful tests, not just coverage numbers |

## Next Topic

Continue to [Unit Testing Arrange Act Assert](./04-unit-testing-arrange-act-assert.md) to learn about structuring your tests effectively.
