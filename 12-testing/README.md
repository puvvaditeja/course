# Testing with JUnit and Mockito

## Overview
Learn unit testing and test-driven development using JUnit 5 and Mockito framework.

## Learning Objectives
By the end of this module, you will understand and be able to apply the key concepts and practices of Testing with JUnit and Mockito.

## Topics Covered

### 1. [Introduction to JUnit](./topics/01-introduction-to-junit.md)
- What is JUnit?
- JUnit 5 Architecture (Platform, Jupiter, Vintage)
- Setting up JUnit with Maven and Gradle
- Basic annotations (@Test, @BeforeEach, @AfterEach, @DisplayName)
- Writing your first test
- Test naming conventions and project structure

### 2. [Unit Testing Introduction To JUnit](./topics/02-unit-testing-introduction-to-junit.md)
- What is unit testing?
- Unit vs Integration vs System testing
- Writing effective unit tests
- Test structure with Arrange-Act-Assert
- Parameterized tests and nested tests
- Test lifecycle hooks and best practices

### 3. [Unit Testing Line Vs Branch Coverage](./topics/03-unit-testing-line-vs-branch-coverage.md)
- Understanding code coverage metrics
- Line coverage vs Branch coverage
- Using JaCoCo for coverage analysis
- Reading and interpreting coverage reports
- Setting coverage goals and thresholds
- Coverage in CI/CD pipelines

### 4. [Unit Testing Arrange Act Assert](./topics/04-unit-testing-arrange-act-assert.md)
- The AAA pattern explained
- Arrange phase: Setting up test data
- Act phase: Executing the behavior
- Assert phase: Verifying outcomes
- Complete examples with service layers
- Common pitfalls and solutions

### 5. [Unit Testing Assertion Types](./topics/05-unit-testing-assertion-types.md)
- Basic assertions (assertEquals, assertTrue, assertNull)
- Reference assertions (assertSame, assertNotSame)
- Collection assertions (assertArrayEquals, assertIterableEquals)
- Exception assertions (assertThrows, assertDoesNotThrow)
- Timeout assertions and grouped assertions (assertAll)
- Best practices for writing assertions

### 6. [Unit Testing Stubs](./topics/06-unit-testing-stubs.md)
- What is a stub?
- Creating manual stubs
- Types of stubs (hard-coded, configurable, state-based)
- Stub vs Fake vs Dummy objects
- Real-world stub examples
- When to use stubs and best practices

### 7. [Introduction to Mockito](./topics/07-introduction-to-mockito.md)
- What is Mockito and why use it?
- Setting up Mockito
- Creating mocks (@Mock, @InjectMocks, @ExtendWith)
- Stubbing methods (when().thenReturn())
- Argument matchers and exception stubbing
- Verification and argument captors

### 8. [Mock vs Stub vs Spy](./topics/08-mock-vs-stub-vs-spy.md)
- Understanding test doubles
- The five types: Dummy, Stub, Mock, Spy, Fake
- Mock vs Stub: Behavior vs State verification
- When to use Spies (partial mocking)
- Detailed comparison and decision tree
- Common pitfalls and best practices

### 9. [Test Driven Development](./topics/09-test-driven-development.md)
- What is TDD?
- The Red-Green-Refactor cycle
- TDD in action: Complete examples
- The three laws of TDD
- TDD best practices and common pitfalls
- Practice katas (String Calculator, FizzBuzz)


## Key Concepts
Refer to the curriculum and lecture notes for detailed explanations of each topic.

## Exercises
See the [exercises](./exercises/) directory for hands-on practice problems and solutions.

## Code Examples
Check the module materials and exercises for practical code examples.

## Additional Resources
- Official documentation
- Online tutorials and courses
- Community forums and discussions

## Assessment
Make sure you are comfortable with all topics listed above before proceeding to the next module.

## Next Steps
Continue to the next module in the curriculum sequence.

---
**Time Estimate:** 3 days | **Difficulty:** Intermediate | **Prerequisites:** Previous modules
