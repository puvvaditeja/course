# Object-Oriented Programming

## Introduction To OOP

Object-Oriented Programming (OOP) is a programming paradigm based on the concept of "objects" that contain data and code. Java is a pure OOP language where everything (except primitives) is an object.

### Four Pillars of OOP

```
┌─────────────────────────────────────┐
│         OOP Principles              │
├─────────────────────────────────────┤
│  1. Encapsulation                   │
│     Hide internal details           │
│                                     │
│  2. Inheritance                     │
│     Reuse and extend functionality  │
│                                     │
│  3. Polymorphism                    │
│     One interface, many forms       │
│                                     │
│  4. Abstraction                     │
│     Hide complexity, show essentials│
└─────────────────────────────────────┘
```

#### 1. Encapsulation

Bundling data and methods that operate on that data within a single unit (class), hiding internal details.

```java
public class BankAccount {
    // Private data - hidden from outside
    private double balance;

    // Public methods - controlled access
    public void deposit(double amount) {
        if (amount > 0) {
            balance += amount;
        }
    }

    public boolean withdraw(double amount) {
        if (amount > 0 && balance >= amount) {
            balance -= amount;
            return true;
        }
        return false;
    }

    public double getBalance() {
        return balance;
    }
}
```

**Benefits:**
- Data protection
- Flexibility (change implementation without affecting users)
- Maintainability

#### 2. Inheritance

Mechanism where a new class (child) inherits properties and behaviors from an existing class (parent).

```java
// Parent class
public class Animal {
    protected String name;

    public void eat() {
        System.out.println(name + " is eating");
    }
}

// Child class
public class Dog extends Animal {
    public void bark() {
        System.out.println(name + " is barking");
    }
}

// Usage
Dog dog = new Dog();
dog.name = "Buddy";
dog.eat();   // Inherited from Animal
dog.bark();  // Defined in Dog
```

**Benefits:**
- Code reuse
- Hierarchical classification
- Method overriding

#### 3. Polymorphism

Ability of objects to take many forms. Same interface, different implementations.

```java
// Method Overloading (Compile-time polymorphism)
public class Calculator {
    public int add(int a, int b) {
        return a + b;
    }

    public double add(double a, double b) {
        return a + b;
    }

    public int add(int a, int b, int c) {
        return a + b + c;
    }
}

// Method Overriding (Runtime polymorphism)
public class Animal {
    public void makeSound() {
        System.out.println("Some sound");
    }
}

public class Dog extends Animal {
    @Override
    public void makeSound() {
        System.out.println("Bark!");
    }
}

public class Cat extends Animal {
    @Override
    public void makeSound() {
        System.out.println("Meow!");
    }
}

// Usage
Animal animal1 = new Dog();
Animal animal2 = new Cat();
animal1.makeSound();  // Bark!
animal2.makeSound();  // Meow!
```

**Benefits:**
- Flexibility
- Extensibility
- Code reusability

#### 4. Abstraction

Hiding complex implementation details, showing only essential features.

```java
// Abstract class
public abstract class Shape {
    // Abstract method - no implementation
    public abstract double calculateArea();

    // Concrete method
    public void display() {
        System.out.println("Area: " + calculateArea());
    }
}

public class Circle extends Shape {
    private double radius;

    public Circle(double radius) {
        this.radius = radius;
    }

    @Override
    public double calculateArea() {
        return Math.PI * radius * radius;
    }
}

// Interface
public interface Drawable {
    void draw();  // Implicitly abstract
}

public class Rectangle implements Drawable {
    @Override
    public void draw() {
        System.out.println("Drawing rectangle");
    }
}
```

**Benefits:**
- Reduces complexity
- Focuses on what object does, not how
- Increases security

---

## Classes Vs Objects

Understanding the distinction between classes and objects is fundamental to OOP.

### Class

A class is a blueprint or template for creating objects. It defines:
- **State** (fields/attributes)
- **Behavior** (methods)
- **Identity** (name)

```java
public class Car {
    // State (fields)
    String brand;
    String model;
    int year;
    String color;

    // Behavior (methods)
    void start() {
        System.out.println("Car started");
    }

    void stop() {
        System.out.println("Car stopped");
    }

    void accelerate() {
        System.out.println("Car accelerating");
    }
}
```

### Object

An object is an instance of a class. It has:
- **Actual values** for the attributes
- **Can invoke** methods
- **Exists in memory**

```java
// Creating objects (instances)
Car car1 = new Car();
car1.brand = "Toyota";
car1.model = "Camry";
car1.year = 2024;
car1.color = "Blue";

Car car2 = new Car();
car2.brand = "Honda";
car2.model = "Accord";
car2.year = 2023;
car2.color = "Red";

// Different objects, same class
car1.start();  // car1 starts
car2.start();  // car2 starts
```

### Visualization

```
Class (Blueprint)
┌─────────────────┐
│      Car        │
├─────────────────┤
│ - brand         │
│ - model         │
│ - year          │
│ - color         │
├─────────────────┤
│ + start()       │
│ + stop()        │
│ + accelerate()  │
└─────────────────┘
        │
        │ creates
        ↓
Objects (Instances)
┌─────────────────┐    ┌─────────────────┐
│     car1        │    │     car2        │
├─────────────────┤    ├─────────────────┤
│ Toyota          │    │ Honda           │
│ Camry           │    │ Accord          │
│ 2024            │    │ 2023            │
│ Blue            │    │ Red             │
└─────────────────┘    └─────────────────┘
```

### Key Differences

| Class | Object |
|-------|--------|
| Blueprint/Template | Instance of class |
| Defined once | Can create many |
| No memory at definition | Allocated memory when created |
| Logical entity | Physical entity |
| Contains no data | Contains actual data |

---

## Class Members

Class members define the structure and behavior of objects.

### Fields (Attributes)

Variables that hold object state.

```java
public class Person {
    // Instance fields (each object has its own copy)
    String name;
    int age;
    String email;

    // Different access levels
    public String publicField;
    private String privateField;
    protected String protectedField;
    String packageField;  // default (package-private)
}
```

### Methods

Functions that define object behavior.

```java
public class Calculator {
    // Method with no parameters, returns int
    public int getConstant() {
        return 42;
    }

    // Method with parameters, returns int
    public int add(int a, int b) {
        return a + b;
    }

    // Method with parameters, returns void
    public void printSum(int a, int b) {
        System.out.println("Sum: " + (a + b));
    }

    // Method with multiple parameters
    public double average(int a, int b, int c) {
        return (a + b + c) / 3.0;
    }

    // Varargs method (variable arguments)
    public int sum(int... numbers) {
        int total = 0;
        for (int num : numbers) {
            total += num;
        }
        return total;
    }
}

// Usage
Calculator calc = new Calculator();
int result1 = calc.add(5, 3);           // 8
calc.printSum(10, 20);                  // Sum: 30
double avg = calc.average(10, 20, 30);  // 20.0
int sum = calc.sum(1, 2, 3, 4, 5);      // 15
```

### Constructors

Special methods for initializing objects.

```java
public class Person {
    private String name;
    private int age;

    // No-argument constructor
    public Person() {
        this.name = "Unknown";
        this.age = 0;
    }

    // Parameterized constructor
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    // Constructor overloading
    public Person(String name) {
        this.name = name;
        this.age = 0;
    }

    // Constructor chaining
    public Person(String name, int age, String email) {
        this(name, age);  // Call other constructor
        // Additional initialization
    }
}

// Usage
Person p1 = new Person();              // No-arg constructor
Person p2 = new Person("Alice", 25);   // Parameterized constructor
Person p3 = new Person("Bob");         // Single parameter
```

**Constructor Rules:**
- Same name as class
- No return type (not even void)
- Called automatically when object is created
- Can be overloaded
- If no constructor defined, Java provides default no-arg constructor

### this Keyword

Reference to current object.

```java
public class Person {
    private String name;
    private int age;

    // 'this' to distinguish fields from parameters
    public Person(String name, int age) {
        this.name = name;  // this.name refers to field
        this.age = age;    // age refers to parameter
    }

    // 'this' to call another constructor
    public Person(String name) {
        this(name, 0);  // Call other constructor
    }

    // 'this' to return current object
    public Person setName(String name) {
        this.name = name;
        return this;  // Enable method chaining
    }

    public Person setAge(int age) {
        this.age = age;
        return this;
    }
}

// Method chaining
Person person = new Person()
    .setName("Alice")
    .setAge(25);
```

### Initializer Blocks

Code blocks that initialize objects.

```java
public class Example {
    private int value;
    private static int staticValue;

    // Instance initializer block (runs before constructor)
    {
        System.out.println("Instance initializer");
        value = 10;
    }

    // Static initializer block (runs once when class is loaded)
    static {
        System.out.println("Static initializer");
        staticValue = 100;
    }

    // Constructor
    public Example() {
        System.out.println("Constructor");
    }
}

// Execution order when creating object:
// 1. Static initializer (only once)
// 2. Instance initializer
// 3. Constructor
```

### Getters and Setters

Methods to access and modify private fields.

```java
public class Person {
    private String name;
    private int age;

    // Getter
    public String getName() {
        return name;
    }

    // Setter with validation
    public void setName(String name) {
        if (name != null && !name.isEmpty()) {
            this.name = name;
        }
    }

    // Getter
    public int getAge() {
        return age;
    }

    // Setter with validation
    public void setAge(int age) {
        if (age >= 0 && age <= 150) {
            this.age = age;
        }
    }

    // Derived property (no setter)
    public boolean isAdult() {
        return age >= 18;
    }
}
```

---

## Static Members

Static members belong to the class itself, not to any specific object.

### Static Fields

Shared among all instances of the class.

```java
public class Counter {
    // Instance field - each object has its own
    private int instanceCount;

    // Static field - shared by all objects
    private static int totalCount = 0;

    public Counter() {
        instanceCount++;
        totalCount++;
    }

    public void display() {
        System.out.println("Instance: " + instanceCount);
        System.out.println("Total: " + totalCount);
    }
}

// Usage
Counter c1 = new Counter();
c1.display();  // Instance: 1, Total: 1

Counter c2 = new Counter();
c2.display();  // Instance: 1, Total: 2

Counter c3 = new Counter();
c3.display();  // Instance: 1, Total: 3
```

### Static Methods

Belong to class, can be called without creating object.

```java
public class MathUtils {
    // Static constant
    public static final double PI = 3.14159;

    // Static method
    public static int add(int a, int b) {
        return a + b;
    }

    public static int square(int n) {
        return n * n;
    }

    // Static method cannot access instance members
    private int instanceVar = 10;

    public static void staticMethod() {
        // System.out.println(instanceVar);  // ERROR!
        // Must use static members only
        System.out.println(PI);  // OK
    }
}

// Usage - no object needed
int sum = MathUtils.add(5, 3);
int sq = MathUtils.square(4);
double pi = MathUtils.PI;
```

### Static vs Instance

```java
public class Example {
    // Static members
    private static int staticVar = 100;

    public static void staticMethod() {
        System.out.println("Static: " + staticVar);
        // Can only access static members
    }

    // Instance members
    private int instanceVar = 200;

    public void instanceMethod() {
        System.out.println("Instance: " + instanceVar);
        System.out.println("Static: " + staticVar);
        // Can access both static and instance members
    }
}

// Usage
Example.staticMethod();  // Call on class

Example obj = new Example();
obj.instanceMethod();    // Call on object
```

| Feature | Static | Instance |
|---------|--------|----------|
| Belongs to | Class | Object |
| Memory | Single copy | Copy per object |
| Access | Class name or object | Object only |
| Can access | Static members only | Static and instance |
| Use case | Utilities, constants, counters | Object-specific data |

### Static Import

```java
// Without static import
import java.lang.Math;

public class Example {
    double x = Math.sqrt(16);
    double y = Math.PI;
}

// With static import
import static java.lang.Math.sqrt;
import static java.lang.Math.PI;

public class Example {
    double x = sqrt(16);  // Cleaner!
    double y = PI;
}
```

### Common Use Cases

```java
// Constants
public class Constants {
    public static final String APP_NAME = "MyApp";
    public static final int MAX_USERS = 1000;
}

// Utility methods
public class StringUtils {
    public static boolean isEmpty(String str) {
        return str == null || str.isEmpty();
    }

    public static String capitalize(String str) {
        if (isEmpty(str)) return str;
        return str.substring(0, 1).toUpperCase() + str.substring(1);
    }
}

// Factory methods
public class User {
    private String name;

    private User(String name) {
        this.name = name;
    }

    public static User createAdmin(String name) {
        return new User(name);
    }

    public static User createGuest() {
        return new User("Guest");
    }
}

// Singleton pattern
public class Database {
    private static Database instance;

    private Database() { }

    public static Database getInstance() {
        if (instance == null) {
            instance = new Database();
        }
        return instance;
    }
}
```

---

## Inheritance

Inheritance allows a class to inherit properties and methods from another class.

### Basic Inheritance

```java
// Parent class (superclass, base class)
public class Animal {
    protected String name;
    protected int age;

    public Animal(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public void eat() {
        System.out.println(name + " is eating");
    }

    public void sleep() {
        System.out.println(name + " is sleeping");
    }
}

// Child class (subclass, derived class)
public class Dog extends Animal {
    private String breed;

    public Dog(String name, int age, String breed) {
        super(name, age);  // Call parent constructor
        this.breed = breed;
    }

    // Inherited: eat(), sleep()

    // New method
    public void bark() {
        System.out.println(name + " is barking");
    }
}

// Usage
Dog dog = new Dog("Buddy", 3, "Golden Retriever");
dog.eat();    // Inherited from Animal
dog.sleep();  // Inherited from Animal
dog.bark();   // Defined in Dog
```

### super Keyword

Reference to parent class.

```java
public class Vehicle {
    protected String brand;

    public Vehicle(String brand) {
        this.brand = brand;
    }

    public void start() {
        System.out.println("Vehicle starting");
    }
}

public class Car extends Vehicle {
    private String model;

    public Car(String brand, String model) {
        super(brand);  // Call parent constructor
        this.model = model;
    }

    @Override
    public void start() {
        super.start();  // Call parent method
        System.out.println("Car starting: " + brand + " " + model);
    }

    public void display() {
        System.out.println("Brand: " + super.brand);  // Access parent field
    }
}
```

### Method Overriding

Child class provides specific implementation of parent method.

```java
public class Animal {
    public void makeSound() {
        System.out.println("Some sound");
    }
}

public class Dog extends Animal {
    @Override  // Annotation (recommended)
    public void makeSound() {
        System.out.println("Bark!");
    }
}

public class Cat extends Animal {
    @Override
    public void makeSound() {
        System.out.println("Meow!");
    }
}

// Usage
Animal animal = new Animal();
animal.makeSound();  // Some sound

Dog dog = new Dog();
dog.makeSound();     // Bark!

Cat cat = new Cat();
cat.makeSound();     // Meow!

// Polymorphism
Animal pet = new Dog();
pet.makeSound();     // Bark! (runtime polymorphism)
```

**Override Rules:**
- Same method signature (name, parameters)
- Return type same or subtype (covariant return)
- Cannot reduce visibility (can increase)
- Cannot override final, static, or private methods
- Can add @Override annotation for compile-time checking

### Types of Inheritance

#### Single Inheritance

```java
class A { }
class B extends A { }
```

#### Multilevel Inheritance

```java
class A { }
class B extends A { }
class C extends B { }
```

#### Hierarchical Inheritance

```java
class A { }
class B extends A { }
class C extends A { }
class D extends A { }
```

**Note:** Java does NOT support multiple inheritance (class extending multiple classes) to avoid the Diamond Problem. Use interfaces instead.

### IS-A vs HAS-A

```java
// IS-A relationship (Inheritance)
public class Dog extends Animal {
    // Dog IS-A Animal
}

// HAS-A relationship (Composition)
public class Car {
    private Engine engine;  // Car HAS-A Engine

    public Car() {
        this.engine = new Engine();
    }
}
```

### Constructor Chaining in Inheritance

```java
public class Animal {
    public Animal() {
        System.out.println("Animal constructor");
    }
}

public class Dog extends Animal {
    public Dog() {
        super();  // Implicit if not specified
        System.out.println("Dog constructor");
    }
}

public class Puppy extends Dog {
    public Puppy() {
        super();  // Implicit if not specified
        System.out.println("Puppy constructor");
    }
}

// Creating Puppy object prints:
// Animal constructor
// Dog constructor
// Puppy constructor
```

---

## Interfaces And Abstract Classes

Both provide abstraction but with different purposes and capabilities.

### Abstract Classes

Cannot be instantiated, may contain abstract and concrete methods.

```java
public abstract class Shape {
    // Abstract method (no implementation)
    public abstract double calculateArea();
    public abstract double calculatePerimeter();

    // Concrete method
    public void display() {
        System.out.println("Area: " + calculateArea());
        System.out.println("Perimeter: " + calculatePerimeter());
    }

    // Fields allowed
    protected String color;

    // Constructor allowed
    public Shape(String color) {
        this.color = color;
    }
}

public class Circle extends Shape {
    private double radius;

    public Circle(String color, double radius) {
        super(color);
        this.radius = radius;
    }

    @Override
    public double calculateArea() {
        return Math.PI * radius * radius;
    }

    @Override
    public double calculatePerimeter() {
        return 2 * Math.PI * radius;
    }
}

// Usage
// Shape shape = new Shape("red");  // ERROR: Cannot instantiate
Shape circle = new Circle("red", 5.0);
circle.display();
```

### Interfaces

Contract that implementing classes must fulfill. All methods implicitly public and abstract (before Java 8).

```java
public interface Drawable {
    // Abstract method (implicitly public abstract)
    void draw();

    void resize(int width, int height);
}

public class Rectangle implements Drawable {
    @Override
    public void draw() {
        System.out.println("Drawing rectangle");
    }

    @Override
    public void resize(int width, int height) {
        System.out.println("Resizing to " + width + "x" + height);
    }
}

// Multiple interfaces
public class Square implements Drawable, Comparable<Square> {
    private int side;

    @Override
    public void draw() {
        System.out.println("Drawing square");
    }

    @Override
    public void resize(int width, int height) {
        this.side = width;  // Square maintains aspect ratio
    }

    @Override
    public int compareTo(Square other) {
        return Integer.compare(this.side, other.side);
    }
}
```

### Interface Features (Java 8+)

```java
public interface ModernInterface {
    // Constants (implicitly public static final)
    int MAX_SIZE = 100;

    // Abstract method
    void abstractMethod();

    // Default method (Java 8+)
    default void defaultMethod() {
        System.out.println("Default implementation");
        privateHelper();  // Can call private methods
    }

    // Static method (Java 8+)
    static void staticMethod() {
        System.out.println("Static method in interface");
    }

    // Private method (Java 9+)
    private void privateHelper() {
        System.out.println("Private helper method");
    }

    // Private static method (Java 9+)
    private static void privateStaticHelper() {
        System.out.println("Private static helper");
    }
}

public class Implementation implements ModernInterface {
    @Override
    public void abstractMethod() {
        System.out.println("Implementation");
    }

    // Can optionally override default method
    @Override
    public void defaultMethod() {
        System.out.println("Custom implementation");
    }
}

// Usage
Implementation obj = new Implementation();
obj.abstractMethod();
obj.defaultMethod();
ModernInterface.staticMethod();  // Called on interface
```

### Abstract Class vs Interface

| Feature | Abstract Class | Interface |
|---------|---------------|-----------|
| Methods | Abstract and concrete | Abstract, default, static (Java 8+) |
| Fields | Any | public static final only |
| Constructor | Yes | No |
| Multiple Inheritance | No | Yes |
| Access Modifiers | Any | public only (methods) |
| When to use | IS-A relationship | CAN-DO capability |

```java
// Abstract class for IS-A relationship
public abstract class Animal {
    protected String name;

    public Animal(String name) {
        this.name = name;
    }

    public abstract void makeSound();

    public void sleep() {
        System.out.println(name + " is sleeping");
    }
}

// Interfaces for capabilities
public interface Swimmable {
    void swim();
}

public interface Flyable {
    void fly();
}

// Using both
public class Duck extends Animal implements Swimmable, Flyable {
    public Duck(String name) {
        super(name);
    }

    @Override
    public void makeSound() {
        System.out.println("Quack!");
    }

    @Override
    public void swim() {
        System.out.println(name + " is swimming");
    }

    @Override
    public void fly() {
        System.out.println(name + " is flying");
    }
}
```

### Functional Interfaces

Interface with exactly one abstract method (SAM - Single Abstract Method).

```java
@FunctionalInterface
public interface Converter<F, T> {
    T convert(F from);

    // Can have default methods
    default void log() {
        System.out.println("Converting...");
    }
}

// Usage with lambda
Converter<String, Integer> converter = (s) -> Integer.parseInt(s);
int number = converter.convert("123");
```

### Marker Interfaces

Interface with no methods, used for type checking.

```java
public interface Serializable { }

public class User implements Serializable {
    private String name;
    private int age;
}

// Java checks if object is instance of Serializable
```

---

## Non-Access Modifiers

Modifiers that provide additional properties to classes, methods, and fields.

### final Modifier

#### final Variables

Cannot be reassigned after initialization.

```java
public class Example {
    // final instance variable - must be initialized
    private final int ID;

    // final static variable (constant)
    public static final double PI = 3.14159;
    public static final String APP_NAME = "MyApp";

    public Example(int id) {
        this.ID = id;  // Can initialize in constructor
        // this.ID = 100;  // ERROR: Cannot reassign
    }

    public void method() {
        final int localVar = 10;
        // localVar = 20;  // ERROR: Cannot reassign
    }
}
```

#### final Methods

Cannot be overridden by subclasses.

```java
public class Parent {
    public final void finalMethod() {
        System.out.println("Cannot be overridden");
    }

    public void normalMethod() {
        System.out.println("Can be overridden");
    }
}

public class Child extends Parent {
    // @Override
    // public void finalMethod() { }  // ERROR: Cannot override

    @Override
    public void normalMethod() {
        System.out.println("Overridden");
    }
}
```

#### final Classes

Cannot be extended.

```java
public final class ImmutableClass {
    private final int value;

    public ImmutableClass(int value) {
        this.value = value;
    }

    public int getValue() {
        return value;
    }
}

// public class SubClass extends ImmutableClass { }  // ERROR: Cannot extend
```

### abstract Modifier

#### abstract Methods

No implementation, must be overridden.

```java
public abstract class Shape {
    public abstract void draw();  // No body

    public void display() {        // Concrete method allowed
        System.out.println("Displaying shape");
    }
}
```

#### abstract Classes

Cannot be instantiated, may contain abstract methods.

```java
public abstract class AbstractClass {
    public abstract void abstractMethod();

    public void concreteMethod() {
        System.out.println("Concrete implementation");
    }
}

// AbstractClass obj = new AbstractClass();  // ERROR
```

### static Modifier

Covered in detail in the Static Members section.

```java
public class Example {
    private static int count = 0;

    public static void staticMethod() {
        System.out.println("Static method");
    }
}
```

### synchronized Modifier

Thread-safe execution (covered in Multithreading section).

```java
public class Counter {
    private int count = 0;

    // Synchronized method
    public synchronized void increment() {
        count++;
    }

    // Synchronized block
    public void decrement() {
        synchronized(this) {
            count--;
        }
    }
}
```

### volatile Modifier

Ensures visibility across threads (covered in Multithreading section).

```java
public class SharedData {
    private volatile boolean flag = false;

    public void setFlag() {
        flag = true;  // Immediately visible to all threads
    }

    public boolean getFlag() {
        return flag;
    }
}
```

### transient Modifier

Field should not be serialized.

```java
import java.io.Serializable;

public class User implements Serializable {
    private String username;
    private transient String password;  // Won't be serialized

    public User(String username, String password) {
        this.username = username;
        this.password = password;
    }
}
```

### native Modifier

Method implemented in native code (C/C++).

```java
public class NativeExample {
    public native void nativeMethod();

    static {
        System.loadLibrary("nativelib");
    }
}
```

### strictfp Modifier

Ensures consistent floating-point calculations across platforms.

```java
public strictfp class MathOperations {
    public double calculate() {
        return 1.0 / 3.0;  // Consistent result on all platforms
    }
}
```

---

## Equality, hashCode, and equals

Understanding object equality is crucial for collections and comparisons.

### == vs equals()

```java
String s1 = new String("Hello");
String s2 = new String("Hello");
String s3 = s1;

// == compares references
System.out.println(s1 == s2);     // false (different objects)
System.out.println(s1 == s3);     // true (same reference)

// equals() compares content
System.out.println(s1.equals(s2)); // true (same content)
System.out.println(s1.equals(s3)); // true

// String pool special case
String s4 = "Hello";
String s5 = "Hello";
System.out.println(s4 == s5);     // true (same string pool object)
```

### equals() Contract

When overriding equals(), must satisfy:

1. **Reflexive**: `x.equals(x)` must be true
2. **Symmetric**: If `x.equals(y)` then `y.equals(x)`
3. **Transitive**: If `x.equals(y)` and `y.equals(z)` then `x.equals(z)`
4. **Consistent**: Multiple calls return same result
5. **Null**: `x.equals(null)` must be false

### Implementing equals()

```java
public class Person {
    private String name;
    private int age;
    private String email;

    // Constructor, getters, setters...

    @Override
    public boolean equals(Object obj) {
        // 1. Check reference equality
        if (this == obj) {
            return true;
        }

        // 2. Check null and type
        if (obj == null || getClass() != obj.getClass()) {
            return false;
        }

        // 3. Cast and compare fields
        Person other = (Person) obj;
        return age == other.age &&
               Objects.equals(name, other.name) &&
               Objects.equals(email, other.email);
    }
}
```

### hashCode() Contract

When overriding equals(), must also override hashCode():

1. **Consistent**: Multiple calls on same object return same value
2. **Equal objects**: If `x.equals(y)` then `x.hashCode() == y.hashCode()`
3. **Unequal objects**: Not required, but different hash codes improve performance

### Implementing hashCode()

```java
public class Person {
    private String name;
    private int age;
    private String email;

    @Override
    public int hashCode() {
        return Objects.hash(name, age, email);
    }

    // Or manually
    @Override
    public int hashCode() {
        int result = 17;
        result = 31 * result + (name != null ? name.hashCode() : 0);
        result = 31 * result + age;
        result = 31 * result + (email != null ? email.hashCode() : 0);
        return result;
    }
}
```

### Complete Example

```java
import java.util.Objects;

public class Person {
    private final String name;
    private final int age;
    private final String email;

    public Person(String name, int age, String email) {
        this.name = name;
        this.age = age;
        this.email = email;
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (obj == null || getClass() != obj.getClass()) return false;

        Person person = (Person) obj;
        return age == person.age &&
               Objects.equals(name, person.name) &&
               Objects.equals(email, person.email);
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, age, email);
    }

    @Override
    public String toString() {
        return "Person{name='" + name + "', age=" + age + ", email='" + email + "'}";
    }
}

// Usage in collections
Set<Person> people = new HashSet<>();
people.add(new Person("Alice", 25, "alice@example.com"));
people.add(new Person("Alice", 25, "alice@example.com"));  // Duplicate, won't be added

System.out.println(people.size());  // 1
```

### Why hashCode() Matters

```java
Person p1 = new Person("Alice", 25, "alice@example.com");
Person p2 = new Person("Alice", 25, "alice@example.com");

// Without proper hashCode(), HashMap won't work correctly
Map<Person, String> map = new HashMap<>();
map.put(p1, "Value1");

// If hashCode() not overridden, this might return null!
System.out.println(map.get(p2));  // Should return "Value1"
```

---

## Garbage Collection

Automatic memory management in Java.

### How Garbage Collection Works

```java
public class GCExample {
    public static void main(String[] args) {
        // Object created
        Person p1 = new Person("Alice");

        // Another reference to same object
        Person p2 = p1;

        // p1 no longer references object, but p2 still does
        p1 = null;  // Object NOT eligible for GC

        // Now no references exist
        p2 = null;  // Object eligible for GC
    }
}
```

### Making Objects Eligible for GC

```java
// 1. Nulling references
Person p = new Person("Alice");
p = null;  // Eligible

// 2. Reassigning reference
Person p1 = new Person("Alice");
p1 = new Person("Bob");  // "Alice" object eligible

// 3. Object out of scope
public void method() {
    Person local = new Person("Alice");
}  // "Alice" object eligible when method ends

// 4. Anonymous objects
new Person("Alice").display();  // Eligible immediately after use
```

### Requesting Garbage Collection

```java
// Suggest GC (not guaranteed to run immediately)
System.gc();  // or
Runtime.getRuntime().gc();

// Note: You cannot force garbage collection!
// JVM decides when to run GC
```

### finalize() Method (Deprecated)

Called before object is garbage collected (avoid using).

```java
public class Resource {
    @Override
    protected void finalize() throws Throwable {
        try {
            // Cleanup code (not recommended)
            System.out.println("Finalize called");
        } finally {
            super.finalize();
        }
    }
}

// Better: Use try-with-resources or explicit cleanup
public class Resource implements AutoCloseable {
    @Override
    public void close() {
        // Cleanup code
        System.out.println("Cleanup");
    }
}

// Usage
try (Resource r = new Resource()) {
    // Use resource
}  // close() automatically called
```

### Memory Leaks in Java

Despite GC, memory leaks can occur:

```java
// 1. Static collections
public class Cache {
    private static Map<String, Object> cache = new HashMap<>();

    public void add(String key, Object value) {
        cache.put(key, value);  // Never removed, grows forever
    }
}

// 2. Unclosed resources
public void readFile() {
    FileReader reader = new FileReader("file.txt");
    // Forgot to close - resource leak
}

// Fix: Use try-with-resources
public void readFile() throws IOException {
    try (FileReader reader = new FileReader("file.txt")) {
        // Use reader
    }  // Automatically closed
}

// 3. Event listeners not removed
button.addActionListener(listener);
// Later: button.removeActionListener(listener);  // Don't forget!
```

### Best Practices

1. **Don't rely on finalize()**: Use try-with-resources or explicit cleanup
2. **Avoid memory leaks**: Close resources, remove listeners, clear caches
3. **Don't call System.gc()**: Let JVM manage GC
4. **Use weak references**: For caches (WeakHashMap)
5. **Profile your application**: Find memory leaks with tools

---

## Summary

| Concept | Key Points |
|---------|------------|
| OOP Principles | Encapsulation, Inheritance, Polymorphism, Abstraction |
| Classes vs Objects | Class is blueprint, Object is instance |
| Class Members | Fields, methods, constructors, initializers |
| Static Members | Belong to class, shared among instances |
| Inheritance | Code reuse, IS-A relationship, method overriding |
| Abstract Classes | Cannot instantiate, may have abstract methods |
| Interfaces | Contract, multiple inheritance, default methods (Java 8+) |
| equals/hashCode | Override together, important for collections |
| Garbage Collection | Automatic memory management, finalize deprecated |

## Next Topic

Continue to [Exception Handling](./03-exception-handling.md) to learn about handling errors and exceptions in Java.
