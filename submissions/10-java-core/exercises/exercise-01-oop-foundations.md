# OOP Foundations

## Learning Objectives
- Understand class design and encapsulation
- Work with static and instance members
- Implement inheritance and polymorphism
- Apply OOP principles in a real-world scenario

---

## Exercise 1.1: Class Basics (30 min)

Create a `Person` class with proper encapsulation.

### Requirements
- Fields: `name` (String), `age` (int), `email` (String)
- Private fields with getters/setters
- Constructors: no-arg, all-args
- `toString()` method
- Validation: age must be 0-150, email must contain '@'

### Expected Usage
```java
Person p1 = new Person();
Person p2 = new Person("John", 25, "john@email.com");
p2.setAge(30);
System.out.println(p2);  // Person{name='John', age=30, email='john@email.com'}
```

### Submission
- File: `oop-foundations/Person.java`
- Include a `main` method demonstrating all functionality

---

## Exercise 1.2: Static Members (30 min)

Create a `BankAccount` class with instance and static members.

### Requirements
- Static field: `bankName`, `totalAccounts` (tracks count)
- Instance fields: `accountNumber`, `holderName`, `balance`
- Static method: `getBankInfo()` returns bank name and total accounts
- Instance methods: `deposit()`, `withdraw()`, `getBalance()`
- Auto-generate account numbers using static counter

### Expected Usage
```java
BankAccount acc1 = new BankAccount("Alice", 1000);
BankAccount acc2 = new BankAccount("Bob", 500);
System.out.println(BankAccount.getBankInfo());
// "MyBank - Total Accounts: 2"
```

### Submission
- File: `oop-foundations/BankAccount.java`

---

## Exercise 1.3: Inheritance & Polymorphism (1 hour)

Build a shape hierarchy with area calculations.

### Requirements

1. Abstract class `Shape` with:
   - Abstract method `double area()`
   - Abstract method `double perimeter()`
   - Concrete method `void displayInfo()` - prints area and perimeter

2. Subclasses:
   - `Circle` (radius)
   - `Rectangle` (width, height)
   - `Triangle` (base, height, side1, side2, side3)

3. Test polymorphism:
```java
Shape[] shapes = {
    new Circle(5),
    new Rectangle(4, 6),
    new Triangle(3, 4, 3, 4, 5)
};

for (Shape s : shapes) {
    s.displayInfo();
}
```

### Submission
- Files: `oop-foundations/Shape.java`, `oop-foundations/Circle.java`, `oop-foundations/Rectangle.java`, `oop-foundations/Triangle.java`
- Include `oop-foundations/ShapeTest.java` with the polymorphism test

---

## Exercise 1.4: Banking System (2 hours)

Build a complete banking system demonstrating OOP principles.

### Class Hierarchy
```
Account (abstract)
├── SavingsAccount
├── CheckingAccount
└── FixedDepositAccount
```

### Requirements

1. **`Account` (abstract)**:
   - Fields: accountNumber, holderName, balance
   - Abstract: `calculateInterest()`
   - Concrete: `deposit()`, `withdraw()`, `getBalance()`
   - Static: account number generator

2. **`SavingsAccount`**:
   - Interest rate: 4% annually
   - Minimum balance: $100
   - Withdraw fails if balance goes below minimum

3. **`CheckingAccount`**:
   - No interest
   - Overdraft limit: $500
   - Can withdraw up to (balance + overdraft)

4. **`FixedDepositAccount`**:
   - Interest rate: 7% annually
   - Lock period: 12 months
   - No withdrawals allowed (throw exception)

5. **`Bank` class**:
   - Stores list of accounts
   - Methods: `addAccount()`, `findAccount()`, `getTotalDeposits()`

### Expected Usage
```java
Bank bank = new Bank("MyBank");

Account savings = new SavingsAccount("Alice", 1000);
Account checking = new CheckingAccount("Bob", 500);
Account fd = new FixedDepositAccount("Charlie", 10000);

bank.addAccount(savings);
bank.addAccount(checking);
bank.addAccount(fd);

savings.deposit(500);
checking.withdraw(800);  // Uses overdraft

System.out.println("Total deposits: $" + bank.getTotalDeposits());

// Print interest for all accounts
for (Account acc : bank.getAllAccounts()) {
    System.out.println(acc.getHolderName() + " interest: $" + acc.calculateInterest());
}
```

### Submission
Files in `oop-foundations/banking/`:
- `Account.java`
- `SavingsAccount.java`
- `CheckingAccount.java`
- `FixedDepositAccount.java`
- `Bank.java`
- `BankingTest.java`

---

## Submission Checklist

| Exercise | File(s) | Status |
|----------|---------|--------|
| 1.1 Class Basics | `Person.java` | ⬜ |
| 1.2 Static Members | `BankAccount.java` | ⬜ |
| 1.3 Inheritance & Polymorphism | `Shape.java`, `Circle.java`, `Rectangle.java`, `Triangle.java`, `ShapeTest.java` | ⬜ |
| 1.4 Banking System | `banking/*.java` | ⬜ |

## Evaluation Criteria

- Proper encapsulation (private fields, public methods)
- Correct use of constructors
- Valid implementation of inheritance
- Polymorphism demonstrated correctly
- Code compiles and runs without errors
- Meaningful variable/method names
