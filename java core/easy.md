# Core Java Interview Questions

I'll provide comprehensive interview questions covering all core Java concepts. I'll organize them by topic and keep answers simple with industry examples.

## **TOPIC 1: Java Basics**

### Q1. What is Java?
**Answer:** Java is a programming language that works on any computer. You write code once, it runs anywhere.

**Example:** A banking app written in Java runs on Windows PCs, Mac laptops, and Linux servers without changing code.

---

### Q2. What is JVM (Java Virtual Machine)?
**Answer:** JVM is like a translator that converts Java code into instructions your computer understands.

**Example:** When you run a hotel booking application, JVM translates Java code into instructions for your specific computer.

---

### Q3. What is JRE (Java Runtime Environment)?
**Answer:** JRE is a package that contains JVM and libraries needed to run Java programs. You need it to run Java apps.

**Example:** To run your company's payroll software on your computer, you install JRE.

---

### Q4. What is JDK (Java Development Kit)?
**Answer:** JDK is a complete toolkit for developers. It has JRE plus tools to write and compile code.

**Example:** Developers writing an e-commerce website use JDK to write, compile, and test code.

---

### Q5. What is bytecode?
**Answer:** Bytecode is intermediate code created after compiling Java. JVM reads this bytecode.

**Example:** You write Employee.java → Compiler creates Employee.class (bytecode) → JVM runs it.

---

### Q6. Why is Java platform independent?
**Answer:** Java code converts to bytecode, which runs on any computer having JVM.

**Example:** An inventory management system works on shop owner's Windows PC and warehouse Linux server using same code.

---

### Q7. What is the difference between JDK, JRE, and JVM?
**Answer:** 
- JVM = Runs programs
- JRE = JVM + Libraries (to run programs)
- JDK = JRE + Development tools (to create programs)

**Example:** Customer needs JRE to use your billing software. You need JDK to build it.

---

### Q8. What are the main features of Java?
**Answer:** Simple, Object-oriented, Platform independent, Secure, Multi-threaded, Robust.

**Example:** A banking app is secure (encryption), multi-threaded (multiple users), platform-independent (any OS).

---

## **TOPIC 2: Data Types and Variables**

### Q9. What are primitive data types in Java?
**Answer:** Basic data types: byte, short, int, long, float, double, char, boolean.

**Example:** 
```java
int employeeCount = 150;        // whole number
double salary = 45000.50;       // decimal number
boolean isActive = true;        // yes/no
char grade = 'A';               // single character
```

---

### Q10. What is the difference between int and Integer?
**Answer:** 
- `int` is primitive (simple value)
- `Integer` is wrapper class (object with methods)

**Example:**
```java
int age = 25;                   // primitive, just stores value
Integer empId = 101;            // object, can use methods
String idString = empId.toString();
```

---

### Q11. What are wrapper classes?
**Answer:** Classes that wrap primitive types to treat them as objects.

**Example:** In a database, you might get null salary. `int` cannot be null, but `Integer` can.
```java
Integer bonus = null;  // possible
int bonus = null;      // ERROR
```

---

### Q12. What is autoboxing and unboxing?
**Answer:** 
- Autoboxing: automatic conversion of primitive to wrapper
- Unboxing: automatic conversion of wrapper to primitive

**Example:**
```java
int score = 95;
Integer scoreObj = score;        // autoboxing (int → Integer)
int newScore = scoreObj;         // unboxing (Integer → int)
```

---

### Q13. What is the difference between float and double?
**Answer:** 
- `float` = 32-bit, 6-7 decimal digits
- `double` = 64-bit, 15-16 decimal digits

**Example:**
```java
float productPrice = 99.99f;     // for simple prices
double scientificData = 3.14159265359;  // for precise calculations
```

---

### Q14. What is a variable?
**Answer:** A container that stores data that can change.

**Example:**
```java
int customerCount = 10;          // starts with 10
customerCount = 15;              // changes to 15
```

---

### Q15. What are different types of variables?
**Answer:** 
- Local variable (inside method)
- Instance variable (inside class)
- Static variable (shared by all objects)

**Example:**
```java
class Employee {
    static String companyName = "TechCorp";  // static (same for all)
    String empName;                          // instance (different per employee)
    
    void calculateSalary() {
        int bonus = 5000;                    // local (only in this method)
    }
}
```

---

### Q16. What is the default value of variables?
**Answer:** 
- Numbers: 0
- boolean: false
- Objects: null

**Example:**
```java
class Report {
    int pageCount;        // default = 0
    boolean isPublished;  // default = false
    String author;        // default = null
}
```

---

## **TOPIC 3: Operators**

### Q17. What are different types of operators?
**Answer:** Arithmetic (+, -, *, /), Relational (==, !=, >, <), Logical (&&, ||, !), Assignment (=, +=, -=).

**Example:**
```java
int salary = 50000;
int bonus = salary + 5000;         // Arithmetic
if (salary > 40000) { }            // Relational
if (isActive && salary > 30000) {} // Logical
salary += 1000;                    // Assignment
```

---

### Q18. What is the difference between == and equals()?
**Answer:** 
- `==` compares memory address
- `equals()` compares actual content

**Example:**
```java
String name1 = new String("John");
String name2 = new String("John");

name1 == name2;         // false (different objects in memory)
name1.equals(name2);    // true (same content "John")
```

---

### Q19. What is the difference between ++ i and i++?
**Answer:** 
- `++i` increments first, then uses value
- `i++` uses value first, then increments

**Example:**
```java
int tickets = 5;
int sold = ++tickets;    // tickets=6, sold=6 (increment first)

int items = 5;
int count = items++;     // count=5, items=6 (use first, then increment)
```

---

### Q20. What is the ternary operator?
**Answer:** Short form of if-else: `condition ? valueIfTrue : valueIfFalse`

**Example:**
```java
int orderAmount = 1500;
String shipping = (orderAmount > 1000) ? "Free" : "Paid";
// shipping = "Free"
```

---

## **TOPIC 4: Control Statements**

### Q21. What is the difference between if and switch?
**Answer:** 
- `if` checks any condition
- `switch` matches exact values

**Example:**
```java
// if - for ranges
if (age >= 18) { }

// switch - for exact values
switch (paymentMethod) {
    case "Cash": break;
    case "Card": break;
}
```

---

### Q22. What is the difference between while and do-while?
**Answer:** 
- `while` checks condition first
- `do-while` runs once, then checks

**Example:**
```java
// while - may not run at all
while (stock > 0) { 
    sellItem(); 
}

// do-while - runs at least once
do { 
    showMenu(); 
} while (userWantsToContinue);
```

---

### Q23. What is the difference between break and continue?
**Answer:** 
- `break` exits loop completely
- `continue` skips current iteration, continues loop

**Example:**
```java
// Processing orders
for (Order order : orders) {
    if (order.isCancelled()) {
        continue;  // skip this order, check next
    }
    if (order.isPriority()) {
        break;     // stop checking, process this immediately
    }
}
```

---

### Q24. What is an enhanced for loop?
**Answer:** Simple way to loop through arrays/collections without index.

**Example:**
```java
String[] employees = {"John", "Mary", "Bob"};

// Enhanced for loop
for (String emp : employees) {
    System.out.println(emp);
}
```

---

## **TOPIC 5: Arrays**

### Q25. What is an array?
**Answer:** A container that holds multiple values of same type.

**Example:**
```java
int[] dailySales = {1200, 1500, 1800, 2000, 1600};
// Store 5 days of sales in one variable
```

---

### Q26. How to declare and initialize an array?
**Answer:**
```java
// Declaration
int[] scores;

// Initialization
scores = new int[5];              // creates array of size 5
scores[0] = 95;

// Both together
String[] departments = {"HR", "IT", "Sales"};
```

**Example:** In employee management, store 12 months' sales data.

---

### Q27. What is the default value in an array?
**Answer:** Same as variables - numbers: 0, boolean: false, objects: null.

**Example:**
```java
int[] ratings = new int[5];    // all values = 0
String[] names = new String[3]; // all values = null
```

---

### Q28. What is a multidimensional array?
**Answer:** Array of arrays. Like a table with rows and columns.

**Example:**
```java
// Sales data: 4 quarters, 3 regions
int[][] quarterlySales = {
    {1000, 1200, 1100},  // Q1: Region1, Region2, Region3
    {1500, 1300, 1400},  // Q2
    {1800, 1600, 1700},  // Q3
    {2000, 1900, 2100}   // Q4
};
```

---

### Q29. How to find array length?
**Answer:** Use `.length` property.

**Example:**
```java
String[] products = {"Laptop", "Mouse", "Keyboard"};
int totalProducts = products.length;  // 3

for (int i = 0; i < products.length; i++) {
    System.out.println(products[i]);
}
```

---

### Q30. What is ArrayIndexOutOfBoundsException?
**Answer:** Error when you try to access array position that doesn't exist.

**Example:**
```java
int[] prices = {100, 200, 300};  // index 0,1,2
System.out.println(prices[5]);   // ERROR! Index 5 doesn't exist
```

---

## **TOPIC 6: Strings**

### Q31. What is String in Java?
**Answer:** A sequence of characters. Strings are objects, not primitive types.

**Example:**
```java
String customerName = "John Smith";
String productCode = "LAPTOP-2024-001";
```

---

### Q32. Why are Strings immutable?
**Answer:** Once created, String value cannot be changed. Any modification creates a new String.

**Example:**
```java
String orderId = "ORD123";
orderId.concat("-PAID");      // creates new String, doesn't change original
System.out.println(orderId);  // still "ORD123"

orderId = orderId + "-PAID";  // now orderId points to new String
```

---

### Q33. What is String pool?
**Answer:** Special memory area where Java stores String literals to save memory.

**Example:**
```java
String emp1 = "Manager";
String emp2 = "Manager";
// Both point to same "Manager" in String pool

String emp3 = new String("Manager");
// Creates new object in heap, not pool
```

---

### Q34. What is the difference between String, StringBuffer, and StringBuilder?
**Answer:** 
- String: Immutable, thread-safe
- StringBuffer: Mutable, thread-safe, slow
- StringBuilder: Mutable, not thread-safe, fast

**Example:**
```java
// Building a report with many lines
StringBuilder report = new StringBuilder();
for (int i = 0; i < 1000; i++) {
    report.append("Line " + i + "\n");  // Efficient, no new objects
}
```

---

### Q35. What is the difference between == and equals() for Strings?
**Answer:** 
- `==` checks if both refer to same object
- `equals()` checks if content is same

**Example:**
```java
String user1 = "admin";
String user2 = "admin";
String user3 = new String("admin");

user1 == user2;         // true (same object in pool)
user1 == user3;         // false (different objects)
user1.equals(user3);    // true (same content)
```

---

### Q36. What are important String methods?
**Answer:** length(), charAt(), substring(), indexOf(), equals(), toLowerCase(), toUpperCase(), trim(), split().

**Example:**
```java
String email = " john.doe@company.com ";

email.length();              // 24
email.trim();               // "john.doe@company.com"
email.toUpperCase();        // " JOHN.DOE@COMPANY.COM "
email.indexOf("@");         // 10
email.substring(0, 8);      // " john.do"
email.split("@");           // [" john.doe", "company.com "]
```

---

### Q37. How to compare two Strings?
**Answer:** Use `equals()` for case-sensitive, `equalsIgnoreCase()` for case-insensitive.

**Example:**
```java
String password = "Admin123";

password.equals("admin123");           // false
password.equalsIgnoreCase("admin123"); // true
```

---

### Q38. How to convert String to int and int to String?
**Answer:** 
- String to int: `Integer.parseInt()`
- int to String: `String.valueOf()` or `"" + number`

**Example:**
```java
String quantity = "150";
int qty = Integer.parseInt(quantity);  // 150

int price = 2500;
String priceText = String.valueOf(price);  // "2500"
String priceText2 = "" + price;            // "2500"
```

---

## **TOPIC 7: Object-Oriented Programming**

### Q39. What is a class?
**Answer:** A blueprint/template for creating objects. Defines properties and behaviors.

**Example:**
```java
class Employee {
    // Properties
    String name;
    int id;
    double salary;
    
    // Behavior
    void work() {
        System.out.println(name + " is working");
    }
}
```

---

### Q40. What is an object?
**Answer:** An instance of a class. Real entity created from the blueprint.

**Example:**
```java
Employee emp1 = new Employee();  // Object 1
emp1.name = "John";
emp1.id = 101;

Employee emp2 = new Employee();  // Object 2
emp2.name = "Mary";
emp2.id = 102;
```

---

### Q41. What is a constructor?
**Answer:** Special method called when creating an object. Initializes object properties.

**Example:**
```java
class Product {
    String name;
    double price;
    
    // Constructor
    Product(String n, double p) {
        name = n;
        price = p;
    }
}

Product laptop = new Product("Dell Laptop", 45000);
```

---

### Q42. What is the difference between default and parameterized constructor?
**Answer:** 
- Default: No parameters, uses default values
- Parameterized: Takes parameters to initialize with specific values

**Example:**
```java
class Account {
    int balance;
    
    // Default constructor
    Account() {
        balance = 0;
    }
    
    // Parameterized constructor
    Account(int initialBalance) {
        balance = initialBalance;
    }
}

Account acc1 = new Account();        // balance = 0
Account acc2 = new Account(5000);    // balance = 5000
```

---

### Q43. What is constructor overloading?
**Answer:** Having multiple constructors with different parameters.

**Example:**
```java
class Customer {
    String name;
    String phone;
    String email;
    
    Customer(String n) {
        name = n;
    }
    
    Customer(String n, String p) {
        name = n;
        phone = p;
    }
    
    Customer(String n, String p, String e) {
        name = n;
        phone = p;
        email = e;
    }
}
```

---

### Q44. What is the 'this' keyword?
**Answer:** Refers to current object. Used to differentiate between class variables and parameters.

**Example:**
```java
class Employee {
    String name;
    
    Employee(String name) {
        this.name = name;  // this.name is class variable, name is parameter
    }
    
    void printInfo() {
        System.out.println(this.name);  // refers to current object's name
    }
}
```

---

### Q45. What is method overloading?
**Answer:** Same method name with different parameters (number, type, or order).

**Example:**
```java
class Calculator {
    int add(int a, int b) {
        return a + b;
    }
    
    double add(double a, double b) {
        return a + b;
    }
    
    int add(int a, int b, int c) {
        return a + b + c;
    }
}

Calculator calc = new Calculator();
calc.add(5, 3);           // uses first method
calc.add(5.5, 3.2);       // uses second method
calc.add(1, 2, 3);        // uses third method
```

---

I have many more questions to cover! This is approximately 20% of all core Java interview questions.

**Remaining topics to cover:**
- Inheritance
- Polymorphism
- Abstraction
- Encapsulation
- Interfaces
- Access Modifiers
- Static keyword
- Final keyword
- Exception Handling
- Collections Framework (ArrayList, HashMap, etc.)
- Multithreading basics
- File I/O
- And more...

**Should I continue with the next set?** Reply with "continue" and I'll provide more questions.

# Core Java Interview Questions (Continued)

## **TOPIC 8: Inheritance**

### Q46. What is inheritance?
**Answer:** A class acquiring properties and methods of another class. Promotes code reuse.

**Example:**
```java
class Employee {
    String name;
    int id;
    
    void work() {
        System.out.println("Working...");
    }
}

class Manager extends Employee {
    int teamSize;
    
    void conductMeeting() {
        System.out.println("Meeting in progress");
    }
}

Manager mgr = new Manager();
mgr.name = "John";      // inherited from Employee
mgr.teamSize = 5;       // own property
mgr.work();             // inherited method
mgr.conductMeeting();   // own method
```

---

### Q47. What are types of inheritance?
**Answer:** 
- Single: A → B
- Multilevel: A → B → C
- Hierarchical: A → B, A → C
- (Multiple and Hybrid not supported through classes)

**Example:**
```java
// Single Inheritance
class Vehicle { }
class Car extends Vehicle { }

// Multilevel Inheritance
class Vehicle { }
class Car extends Vehicle { }
class SportsCar extends Car { }

// Hierarchical Inheritance
class Employee { }
class Manager extends Employee { }
class Developer extends Employee { }
```

---

### Q48. Why doesn't Java support multiple inheritance?
**Answer:** To avoid confusion when two parent classes have same method (Diamond Problem).

**Example:**
```java
// This is NOT allowed in Java
class Printer { void print() { } }
class Scanner { void print() { } }
class AllInOne extends Printer, Scanner { }  // ERROR!
// Which print() method to use?

// Solution: Use interfaces instead
```

---

### Q49. What is the super keyword?
**Answer:** Refers to parent class. Used to call parent's constructor or methods.

**Example:**
```java
class Employee {
    String name;
    
    Employee(String name) {
        this.name = name;
    }
    
    void work() {
        System.out.println("Employee working");
    }
}

class Manager extends Employee {
    int teamSize;
    
    Manager(String name, int teamSize) {
        super(name);           // calls parent constructor
        this.teamSize = teamSize;
    }
    
    void work() {
        super.work();          // calls parent's work method
        System.out.println("Also managing team");
    }
}
```

---

### Q50. What is method overriding?
**Answer:** Child class provides its own implementation of parent's method.

**Example:**
```java
class BankAccount {
    double calculateInterest() {
        return 1000 * 0.04;  // 4% interest
    }
}

class SavingsAccount extends BankAccount {
    double calculateInterest() {
        return 1000 * 0.06;  // 6% interest (overridden)
    }
}

class SeniorCitizenAccount extends BankAccount {
    double calculateInterest() {
        return 1000 * 0.08;  // 8% interest (overridden)
    }
}
```

---

### Q51. What is the difference between overloading and overriding?
**Answer:**
- Overloading: Same method name, different parameters, same class
- Overriding: Same method signature, different classes (parent-child)

**Example:**
```java
// Overloading (same class)
class Calculator {
    int add(int a, int b) { return a + b; }
    double add(double a, double b) { return a + b; }
}

// Overriding (parent-child)
class Vehicle {
    void start() { System.out.println("Vehicle starting"); }
}
class Car extends Vehicle {
    void start() { System.out.println("Car starting"); }  // overrides
}
```

---

### Q52. Can we override static methods?
**Answer:** No. Static methods belong to class, not objects. They are hidden, not overridden.

**Example:**
```java
class Bank {
    static void displayRate() {
        System.out.println("Rate: 5%");
    }
}

class HDFC extends Bank {
    static void displayRate() {
        System.out.println("HDFC Rate: 6%");  // This is method hiding, not overriding
    }
}

Bank.displayRate();      // Rate: 5%
HDFC.displayRate();      // HDFC Rate: 6%
```

---

### Q53. Can we override private methods?
**Answer:** No. Private methods are not visible to child classes.

**Example:**
```java
class Parent {
    private void show() {
        System.out.println("Parent");
    }
}

class Child extends Parent {
    private void show() {  // This is a NEW method, not overriding
        System.out.println("Child");
    }
}
```

---

## **TOPIC 9: Polymorphism**

### Q54. What is polymorphism?
**Answer:** Same thing behaving differently in different situations. "Many forms".

**Example:**
```java
class Payment {
    void pay() { }
}

class CreditCard extends Payment {
    void pay() { System.out.println("Paid by Credit Card"); }
}

class DebitCard extends Payment {
    void pay() { System.out.println("Paid by Debit Card"); }
}

class Cash extends Payment {
    void pay() { System.out.println("Paid by Cash"); }
}

// Same method, different behavior
Payment p1 = new CreditCard();
p1.pay();  // Paid by Credit Card

Payment p2 = new Cash();
p2.pay();  // Paid by Cash
```

---

### Q55. What are types of polymorphism?
**Answer:**
- Compile-time (Method Overloading)
- Runtime (Method Overriding)

**Example:**
```java
// Compile-time polymorphism
class Printer {
    void print(String doc) { }          // decided at compile time
    void print(String doc, int copies) { }
}

// Runtime polymorphism
class Shape {
    void draw() { }
}
class Circle extends Shape {
    void draw() { }  // decided at runtime based on object type
}
```

---

### Q56. What is dynamic method dispatch?
**Answer:** JVM decides which method to call at runtime based on actual object type.

**Example:**
```java
class Employee {
    void calculateSalary() {
        System.out.println("Basic salary");
    }
}

class Manager extends Employee {
    void calculateSalary() {
        System.out.println("Salary + bonus");
    }
}

// Runtime decision
Employee emp = new Manager();  // Reference: Employee, Object: Manager
emp.calculateSalary();         // Calls Manager's method (runtime decision)
```

---

## **TOPIC 10: Abstraction**

### Q57. What is abstraction?
**Answer:** Hiding implementation details, showing only essential features.

**Example:**
```java
// User knows ATM has withdrawCash() method
// User doesn't know internal cash counting, balance checking logic

abstract class ATM {
    abstract void withdrawCash(int amount);  // what to do
    
    void displayBalance() {                  // how to do (hidden)
        // complex balance checking logic
    }
}
```

---

### Q58. What is an abstract class?
**Answer:** A class that cannot be instantiated. May have abstract methods (without body).

**Example:**
```java
abstract class Vehicle {
    String model;
    
    abstract void start();        // no body (abstract method)
    
    void stop() {                 // with body (concrete method)
        System.out.println("Vehicle stopped");
    }
}

class Car extends Vehicle {
    void start() {                // must implement abstract method
        System.out.println("Car started with key");
    }
}

// Vehicle v = new Vehicle();    // ERROR! Can't create object
Car c = new Car();               // OK
```

---

### Q59. What are abstract methods?
**Answer:** Methods without body, declared using `abstract` keyword. Must be implemented by child class.

**Example:**
```java
abstract class PaymentGateway {
    abstract void processPayment(double amount);  // no implementation
    abstract boolean verifyAccount();
}

class PayPal extends PaymentGateway {
    void processPayment(double amount) {
        System.out.println("Processing $" + amount + " via PayPal");
    }
    
    boolean verifyAccount() {
        // PayPal-specific verification
        return true;
    }
}
```

---

### Q60. Can abstract class have constructor?
**Answer:** Yes. Used to initialize common properties when child object is created.

**Example:**
```java
abstract class Employee {
    String name;
    int id;
    
    Employee(String name, int id) {  // constructor in abstract class
        this.name = name;
        this.id = id;
    }
    
    abstract void work();
}

class Developer extends Employee {
    Developer(String name, int id) {
        super(name, id);  // calls parent constructor
    }
    
    void work() {
        System.out.println("Writing code");
    }
}

Developer dev = new Developer("John", 101);
```

---

### Q61. Can abstract class have final methods?
**Answer:** Yes. Final methods cannot be overridden.

**Example:**
```java
abstract class PaymentSystem {
    abstract void pay(double amount);
    
    final void generateReceipt() {  // final method
        System.out.println("Receipt generated");
        // No child class can change receipt format
    }
}
```

---

## **TOPIC 11: Interfaces**

### Q62. What is an interface?
**Answer:** A contract that defines what a class must do, but not how. All methods are abstract by default.

**Example:**
```java
interface Printable {
    void print();           // public abstract by default
}

class Invoice implements Printable {
    public void print() {
        System.out.println("Printing invoice");
    }
}

class Report implements Printable {
    public void print() {
        System.out.println("Printing report");
    }
}
```

---

### Q63. What is the difference between abstract class and interface?
**Answer:**
- Abstract class: Can have both abstract and concrete methods, constructors, variables
- Interface: Only abstract methods (before Java 8), no constructors, only constants

**Example:**
```java
// Abstract class - partial implementation
abstract class Employee {
    String name;                    // variable
    
    Employee(String name) {         // constructor
        this.name = name;
    }
    
    void clockIn() {                // concrete method
        System.out.println("Clocked in");
    }
    
    abstract void work();           // abstract method
}

// Interface - only contract
interface Workable {
    void work();                    // only abstract methods
}
```

---

### Q64. Can a class implement multiple interfaces?
**Answer:** Yes. This is how Java achieves multiple inheritance.

**Example:**
```java
interface Printable {
    void print();
}

interface Scannable {
    void scan();
}

class AllInOnePrinter implements Printable, Scannable {
    public void print() {
        System.out.println("Printing document");
    }
    
    public void scan() {
        System.out.println("Scanning document");
    }
}
```

---

### Q65. Can an interface extend another interface?
**Answer:** Yes. An interface can extend one or more interfaces.

**Example:**
```java
interface Readable {
    void read();
}

interface Writable {
    void write();
}

interface Storage extends Readable, Writable {
    void delete();
}

class FileSystem implements Storage {
    public void read() { }
    public void write() { }
    public void delete() { }
}
```

---

### Q66. What are marker interfaces?
**Answer:** Empty interfaces with no methods. Used to mark classes for special treatment.

**Example:**
```java
interface Serializable {  // marker interface (no methods)
}

class Employee implements Serializable {
    String name;
    int id;
    // Now Employee objects can be saved to file
}
```

---

### Q67. Why use interfaces?
**Answer:** Achieve abstraction, multiple inheritance, loose coupling.

**Example:**
```java
// Loose coupling - easily switch payment methods
interface PaymentMethod {
    void pay(double amount);
}

class CreditCard implements PaymentMethod {
    public void pay(double amount) { }
}

class UPI implements PaymentMethod {
    public void pay(double amount) { }
}

class ShoppingCart {
    void checkout(PaymentMethod payment) {
        payment.pay(1000);  // works with any payment method
    }
}
```

---

## **TOPIC 12: Encapsulation**

### Q68. What is encapsulation?
**Answer:** Wrapping data and methods together, hiding internal details. Use private variables with public getters/setters.

**Example:**
```java
class BankAccount {
    private double balance;  // hidden from outside
    
    public double getBalance() {  // controlled access
        return balance;
    }
    
    public void deposit(double amount) {
        if (amount > 0) {
            balance += amount;
        }
    }
    
    public void withdraw(double amount) {
        if (amount > 0 && amount <= balance) {
            balance -= amount;
        }
    }
}

// Can't directly change balance
BankAccount acc = new BankAccount();
// acc.balance = -1000;  // ERROR! Can't access private variable
acc.deposit(5000);       // OK, controlled way
```

---

### Q69. What are getters and setters?
**Answer:** Methods to read (get) and modify (set) private variables.

**Example:**
```java
class Employee {
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
    
    public void setAge(int age) {
        if (age >= 18 && age <= 60) {  // validation
            this.age = age;
        }
    }
}
```

---

### Q70. Why use private variables?
**Answer:** To protect data from invalid modifications and control access.

**Example:**
```java
class Product {
    private double price;
    
    public void setPrice(double price) {
        if (price >= 0) {           // can't set negative price
            this.price = price;
        } else {
            System.out.println("Invalid price");
        }
    }
}

Product p = new Product();
// p.price = -100;  // ERROR! Can't access private
p.setPrice(-100);   // Validation prevents invalid value
```

---

## **TOPIC 13: Access Modifiers**

### Q71. What are access modifiers?
**Answer:** Keywords that set access levels for classes, variables, methods.
- private: Same class only
- default: Same package only
- protected: Same package + child classes
- public: Everywhere

**Example:**
```java
class Employee {
    private int ssn;           // only inside Employee class
    String department;         // default - same package only
    protected double salary;   // same package + child classes
    public String name;        // accessible everywhere
}
```

---

### Q72. What is the difference between private and protected?
**Answer:**
- private: Only same class
- protected: Same package + child classes (even in different package)

**Example:**
```java
// Package: com.company
class Employee {
    private int privateData;
    protected int protectedData;
}

// Package: com.hr
class Manager extends Employee {
    void test() {
        // privateData = 10;     // ERROR! Can't access
        protectedData = 20;      // OK! Child class can access
    }
}
```

---

### Q73. What is default access modifier?
**Answer:** When no modifier is specified. Accessible within same package only.

**Example:**
```java
// Package: com.sales
class SalesReport {
    int revenue;  // default access
}

class SalesManager {
    void calculate() {
        SalesReport report = new SalesReport();
        report.revenue = 50000;  // OK, same package
    }
}

// Package: com.hr
class HRManager {
    void check() {
        SalesReport report = new SalesReport();
        // report.revenue = 1000;  // ERROR! Different package
    }
}
```

---

## **TOPIC 14: Static Keyword**

### Q74. What is the static keyword?
**Answer:** Belongs to class, not objects. Shared by all instances.

**Example:**
```java
class Employee {
    static String companyName = "TechCorp";  // shared by all
    String employeeName;                      // individual
    
    static int employeeCount = 0;
    
    Employee(String name) {
        employeeName = name;
        employeeCount++;  // increases for each new employee
    }
}

Employee e1 = new Employee("John");
Employee e2 = new Employee("Mary");
System.out.println(Employee.employeeCount);  // 2 (shared)
System.out.println(Employee.companyName);    // TechCorp (shared)
```

---

### Q75. What is a static method?
**Answer:** Method that belongs to class. Called using class name. Cannot access non-static members.

**Example:**
```java
class Calculator {
    static int add(int a, int b) {  // static method
        return a + b;
    }
}

// Call without creating object
int result = Calculator.add(5, 3);

// Real example
class MathUtils {
    static double calculateTax(double amount) {
        return amount * 0.18;
    }
}

double tax = MathUtils.calculateTax(10000);  // 1800
```

---

### Q76. What is a static block?
**Answer:** Executed when class is loaded, before main method. Used for static initialization.

**Example:**
```java
class DatabaseConfig {
    static String dbUrl;
    static String username;
    
    static {  // static block
        System.out.println("Loading database config...");
        dbUrl = "jdbc:mysql://localhost:3306/company";
        username = "admin";
    }
}

// Static block runs when class is first used
```

---

### Q77. Can we override static methods?
**Answer:** No. Static methods are hidden, not overridden.

**Example:**
```java
class Bank {
    static void getInterestRate() {
        System.out.println("5%");
    }
}

class HDFC extends Bank {
    static void getInterestRate() {  // method hiding, not overriding
        System.out.println("6%");
    }
}

Bank.getInterestRate();   // 5%
HDFC.getInterestRate();   // 6%

Bank b = new HDFC();
b.getInterestRate();      // 5% (uses reference type, not object type)
```

---

### Q78. Can we access non-static variables in static methods?
**Answer:** No. Static methods don't have access to instance variables.

**Example:**
```java
class Employee {
    String name;           // non-static
    static String company; // static
    
    static void displayCompany() {
        System.out.println(company);  // OK
        // System.out.println(name);  // ERROR! Can't access non-static
    }
    
    void displayName() {
        System.out.println(name);     // OK
        System.out.println(company);  // OK
    }
}
```

---

## **TOPIC 15: Final Keyword**

### Q79. What is the final keyword?
**Answer:** Makes things unchangeable.
- final variable: constant (cannot change value)
- final method: cannot be overridden
- final class: cannot be inherited

**Example:**
```java
final class Constants {
    final double PI = 3.14159;
    final int MAX_USERS = 100;
}

final class SecurityUtil {  // cannot be extended
    final void encrypt() {  // cannot be overridden
        // encryption logic
    }
}
```

---

### Q80. What is a final variable?
**Answer:** A constant whose value cannot be changed after initialization.

**Example:**
```java
class Configuration {
    final String DATABASE_URL = "jdbc:mysql://localhost:3306/db";
    final int MAX_CONNECTIONS = 50;
    
    void test() {
        // DATABASE_URL = "new_url";  // ERROR! Cannot change
        // MAX_CONNECTIONS = 100;      // ERROR! Cannot change
    }
}
```

---

### Q81. What is a final method?
**Answer:** A method that child classes cannot override.

**Example:**
```java
class PaymentSystem {
    final void generateTransactionId() {
        // Critical logic that must not be changed
        System.out.println("TXN-" + System.currentTimeMillis());
    }
}

class OnlinePayment extends PaymentSystem {
    // void generateTransactionId() { }  // ERROR! Cannot override final method
}
```

---

### Q82. What is a final class?
**Answer:** A class that cannot be inherited.

**Example:**
```java
final class SecurityManager {
    void authenticateUser() {
        // Security logic
    }
}

// class CustomSecurity extends SecurityManager { }  // ERROR! Cannot inherit

// Real Java example: String class is final
final class String {
    // Cannot extend String class
}
```

---

### Q83. What is a blank final variable?
**Answer:** A final variable declared but not initialized. Must be initialized in constructor.

**Example:**
```java
class Order {
    final String orderId;  // blank final
    
    Order(String id) {
        orderId = id;  // must initialize in constructor
    }
}

Order o1 = new Order("ORD001");
Order o2 = new Order("ORD002");
// Each object has different but unchangeable orderId
```

---

I'm at about 40% completion. 

**Remaining major topics:**
- Exception Handling (try-catch, throw, throws, custom exceptions)
- Collections Framework (ArrayList, LinkedList, HashMap, HashSet, etc.)
- Object Class Methods (equals, hashCode, toString, etc.)
- Packages and Import
- Multithreading Basics (Thread, Runnable, synchronization)
- File I/O
- Wrapper Classes and Autoboxing
- Type Casting
- And more...

**Should I continue?** Type "continue" for the next set!

# Core Java Interview Questions (Continued)

## **TOPIC 16: Exception Handling**

### Q84. What is an exception?
**Answer:** An unexpected event that disrupts normal program flow. Like an error during execution.

**Example:**
```java
int[] prices = {100, 200, 300};
System.out.println(prices[5]);  // ArrayIndexOutOfBoundsException

int result = 10 / 0;  // ArithmeticException

String name = null;
name.length();  // NullPointerException
```

---

### Q85. What is exception handling?
**Answer:** Managing errors gracefully so program doesn't crash.

**Example:**
```java
class PaymentProcessor {
    void processPayment(int amount) {
        try {
            // Risky code
            int tax = amount / 0;
        } catch (ArithmeticException e) {
            System.out.println("Error: Cannot divide by zero");
            // Log error, notify admin
        }
    }
}
// Program continues running instead of crashing
```

---

### Q86. What is the difference between checked and unchecked exceptions?
**Answer:**
- Checked: Compiler forces you to handle (IOException, SQLException)
- Unchecked: Compiler doesn't force (NullPointerException, ArithmeticException)

**Example:**
```java
// Checked exception - must handle
void readFile() throws IOException {  // must declare
    FileReader file = new FileReader("data.txt");
}

// OR handle with try-catch
void readFile() {
    try {
        FileReader file = new FileReader("data.txt");
    } catch (IOException e) {
        e.printStackTrace();
    }
}

// Unchecked exception - optional handling
void divide(int a, int b) {
    int result = a / b;  // may throw ArithmeticException, no need to declare
}
```

---

### Q87. What is try-catch block?
**Answer:** try contains risky code, catch handles the error.

**Example:**
```java
class ATM {
    void withdrawCash(int amount) {
        try {
            // Check balance
            // Dispense cash
            int notes = 10000 / amount;  // risky
            System.out.println("Dispensing " + notes + " notes");
        } catch (ArithmeticException e) {
            System.out.println("Invalid amount entered");
        }
    }
}
```

---

### Q88. Can we have multiple catch blocks?
**Answer:** Yes. To handle different types of exceptions differently.

**Example:**
```java
class FileProcessor {
    void processFile(String filename, int index) {
        try {
            FileReader file = new FileReader(filename);
            int[] data = {1, 2, 3};
            System.out.println(data[index]);
        } catch (FileNotFoundException e) {
            System.out.println("File not found: " + filename);
        } catch (ArrayIndexOutOfBoundsException e) {
            System.out.println("Invalid index: " + index);
        } catch (Exception e) {
            System.out.println("Some other error occurred");
        }
    }
}
```

---

### Q89. What is the finally block?
**Answer:** Code that always executes, whether exception occurs or not. Used for cleanup.

**Example:**
```java
class DatabaseHandler {
    void fetchData() {
        Connection conn = null;
        try {
            conn = getConnection();
            // Fetch data
        } catch (SQLException e) {
            System.out.println("Database error");
        } finally {
            // Always close connection
            if (conn != null) {
                conn.close();  // cleanup
            }
        }
    }
}
```

---

### Q90. What is the difference between final, finally, and finalize?
**Answer:**
- final: keyword (constant, no override, no inherit)
- finally: block (always executes after try-catch)
- finalize: method (called before object garbage collection)

**Example:**
```java
final int MAX = 100;  // final variable

try {
    // code
} finally {
    // cleanup
}

class Employee {
    protected void finalize() {  // called before object is destroyed
        System.out.println("Cleaning up employee resources");
    }
}
```

---

### Q91. What is throw keyword?
**Answer:** Used to manually throw an exception.

**Example:**
```java
class AgeValidator {
    void validateAge(int age) {
        if (age < 18) {
            throw new ArithmeticException("Age must be 18 or above");
        }
        System.out.println("Age valid: " + age);
    }
}

AgeValidator validator = new AgeValidator();
validator.validateAge(15);  // throws exception
```

---

### Q92. What is throws keyword?
**Answer:** Declares that a method might throw an exception. Caller must handle it.

**Example:**
```java
class FileManager {
    void readFile(String filename) throws IOException {
        FileReader file = new FileReader(filename);
        // May throw IOException, caller must handle
    }
}

class FileProcessor {
    void process() {
        FileManager fm = new FileManager();
        try {
            fm.readFile("data.txt");  // must handle
        } catch (IOException e) {
            System.out.println("File error");
        }
    }
}
```

---

### Q93. What is the difference between throw and throws?
**Answer:**
- throw: Used to throw an exception (inside method)
- throws: Declares exception (in method signature)

**Example:**
```java
// throws - declaration
void withdraw(int amount) throws InsufficientFundsException {
    if (balance < amount) {
        throw new InsufficientFundsException();  // throw - actual throwing
    }
}
```

---

### Q94. What are custom exceptions?
**Answer:** User-defined exceptions for specific business logic.

**Example:**
```java
// Custom exception
class InsufficientBalanceException extends Exception {
    InsufficientBalanceException(String message) {
        super(message);
    }
}

class BankAccount {
    double balance = 5000;
    
    void withdraw(double amount) throws InsufficientBalanceException {
        if (amount > balance) {
            throw new InsufficientBalanceException("Balance too low. Available: " + balance);
        }
        balance -= amount;
    }
}

// Usage
BankAccount acc = new BankAccount();
try {
    acc.withdraw(10000);
} catch (InsufficientBalanceException e) {
    System.out.println(e.getMessage());
}
```

---

### Q95. What is NullPointerException?
**Answer:** Occurs when trying to use an object reference that points to null.

**Example:**
```java
String customerName = null;
System.out.println(customerName.length());  // NullPointerException

// Prevention
if (customerName != null) {
    System.out.println(customerName.length());
}
```

---

## **TOPIC 17: Object Class Methods**

### Q96. What is the Object class?
**Answer:** Parent of all classes in Java. Every class inherits from Object.

**Example:**
```java
class Employee {  // automatically extends Object
    String name;
}

// Employee inherits: toString(), equals(), hashCode(), etc.
```

---

### Q97. What is the toString() method?
**Answer:** Returns string representation of object. Default returns classname@hashcode.

**Example:**
```java
class Employee {
    String name;
    int id;
    
    // Without overriding
    // Employee@15db9742 (not helpful)
    
    // Override toString
    public String toString() {
        return "Employee[name=" + name + ", id=" + id + "]";
    }
}

Employee emp = new Employee();
emp.name = "John";
emp.id = 101;
System.out.println(emp);  // Employee[name=John, id=101]
```

---

### Q98. What is the equals() method?
**Answer:** Compares two objects for equality. Default compares memory addresses.

**Example:**
```java
class Product {
    String code;
    
    // Override equals to compare by code
    public boolean equals(Object obj) {
        if (obj instanceof Product) {
            Product p = (Product) obj;
            return this.code.equals(p.code);
        }
        return false;
    }
}

Product p1 = new Product();
p1.code = "LAP001";

Product p2 = new Product();
p2.code = "LAP001";

System.out.println(p1.equals(p2));  // true (same code)
System.out.println(p1 == p2);       // false (different objects)
```

---

### Q99. What is the hashCode() method?
**Answer:** Returns integer hash value for object. Used in HashMap, HashSet.

**Example:**
```java
class Employee {
    int id;
    
    public int hashCode() {
        return id;  // simple hash based on id
    }
    
    public boolean equals(Object obj) {
        if (obj instanceof Employee) {
            return this.id == ((Employee) obj).id;
        }
        return false;
    }
}

// HashMap uses hashCode to store/retrieve objects quickly
HashMap<Employee, String> map = new HashMap<>();
Employee emp = new Employee();
emp.id = 101;
map.put(emp, "John");
```

---

### Q100. Why override equals() and hashCode() together?
**Answer:** If two objects are equal, they must have same hashCode. Required for HashMap, HashSet.

**Example:**
```java
class Student {
    int rollNo;
    
    public boolean equals(Object obj) {
        if (obj instanceof Student) {
            return this.rollNo == ((Student) obj).rollNo;
        }
        return false;
    }
    
    public int hashCode() {
        return rollNo;  // same rollNo = same hashCode
    }
}

HashSet<Student> students = new HashSet<>();
Student s1 = new Student();
s1.rollNo = 101;
students.add(s1);

Student s2 = new Student();
s2.rollNo = 101;
students.add(s2);  // Won't add duplicate (same equals and hashCode)

System.out.println(students.size());  // 1
```

---

## **TOPIC 18: Collections Framework - List**

### Q101. What is the Collections Framework?
**Answer:** Ready-made data structures (List, Set, Map) to store and manage groups of objects.

**Example:**
```java
// Instead of arrays with fixed size
String[] names = new String[5];  // fixed size

// Use ArrayList - dynamic size
ArrayList<String> nameList = new ArrayList<>();
nameList.add("John");
nameList.add("Mary");
nameList.add("Bob");
// Can grow automatically
```

---

### Q102. What is ArrayList?
**Answer:** Dynamic array that can grow/shrink. Fast access by index.

**Example:**
```java
class ProductManager {
    ArrayList<String> products = new ArrayList<>();
    
    void addProduct(String name) {
        products.add(name);
    }
    
    void displayProducts() {
        for (String product : products) {
            System.out.println(product);
        }
    }
}

ProductManager pm = new ProductManager();
pm.addProduct("Laptop");
pm.addProduct("Mouse");
pm.addProduct("Keyboard");
pm.displayProducts();
```

---

### Q103. What is the difference between ArrayList and Array?
**Answer:**
- Array: Fixed size, can store primitives
- ArrayList: Dynamic size, stores only objects

**Example:**
```java
// Array - fixed size
int[] scores = new int[5];  // exactly 5 elements
scores[0] = 90;

// ArrayList - dynamic
ArrayList<Integer> scoreList = new ArrayList<>();
scoreList.add(90);
scoreList.add(85);
scoreList.add(95);
scoreList.add(88);  // can keep adding
```

---

### Q104. What are common ArrayList methods?
**Answer:** add(), get(), remove(), size(), contains(), clear(), isEmpty()

**Example:**
```java
ArrayList<String> tasks = new ArrayList<>();

tasks.add("Meeting");           // add element
tasks.add("Report");
tasks.add("Email");

String first = tasks.get(0);    // get element at index 0
tasks.remove("Report");         // remove by value
tasks.remove(0);                // remove by index

int count = tasks.size();       // size
boolean hasEmail = tasks.contains("Email");  // check existence
tasks.clear();                  // remove all
boolean empty = tasks.isEmpty(); // check if empty
```

---

### Q105. What is LinkedList?
**Answer:** Doubly-linked list. Fast insertion/deletion, slow access by index.

**Example:**
```java
LinkedList<String> queue = new LinkedList<>();

queue.add("Customer 1");
queue.add("Customer 2");
queue.addFirst("VIP Customer");  // add at beginning
queue.addLast("Regular Customer"); // add at end

String first = queue.removeFirst();  // remove from beginning
String last = queue.removeLast();    // remove from end

// Good for queue/stack operations
```

---

### Q106. What is the difference between ArrayList and LinkedList?
**Answer:**
- ArrayList: Fast access by index, slow insertion/deletion
- LinkedList: Slow access, fast insertion/deletion

**Example:**
```java
// ArrayList - good for frequent access
ArrayList<String> products = new ArrayList<>();
products.add("Item1");
String item = products.get(1000);  // Fast

// LinkedList - good for frequent add/remove
LinkedList<String> taskQueue = new LinkedList<>();
taskQueue.addFirst("Urgent task");  // Fast
taskQueue.removeFirst();            // Fast
```

---

### Q107. What is Vector?
**Answer:** Like ArrayList but synchronized (thread-safe). Slower than ArrayList.

**Example:**
```java
Vector<Integer> numbers = new Vector<>();
numbers.add(10);
numbers.add(20);

// Thread-safe - multiple threads can use safely
// But slower than ArrayList
```

---

## **TOPIC 19: Collections Framework - Set**

### Q108. What is HashSet?
**Answer:** Stores unique elements, no duplicates. No specific order.

**Example:**
```java
HashSet<String> uniqueEmails = new HashSet<>();

uniqueEmails.add("john@company.com");
uniqueEmails.add("mary@company.com");
uniqueEmails.add("john@company.com");  // duplicate - won't be added

System.out.println(uniqueEmails.size());  // 2 (no duplicates)

// Use case: remove duplicate entries
ArrayList<Integer> numbers = new ArrayList<>();
numbers.add(10);
numbers.add(20);
numbers.add(10);

HashSet<Integer> unique = new HashSet<>(numbers);
System.out.println(unique);  // [10, 20]
```

---

### Q109. What is LinkedHashSet?
**Answer:** Like HashSet but maintains insertion order.

**Example:**
```java
LinkedHashSet<String> tasks = new LinkedHashSet<>();

tasks.add("Task C");
tasks.add("Task A");
tasks.add("Task B");

// Maintains order: Task C, Task A, Task B
for (String task : tasks) {
    System.out.println(task);
}
```

---

### Q110. What is TreeSet?
**Answer:** Stores elements in sorted order. Uses natural ordering or comparator.

**Example:**
```java
TreeSet<Integer> scores = new TreeSet<>();

scores.add(85);
scores.add(92);
scores.add(78);
scores.add(95);

System.out.println(scores);  // [78, 85, 92, 95] - sorted

// Use case: leaderboard, sorted employee list
TreeSet<String> names = new TreeSet<>();
names.add("Zara");
names.add("Alice");
names.add("Mike");
System.out.println(names);  // [Alice, Mike, Zara]
```

---

### Q111. What is the difference between HashSet and TreeSet?
**Answer:**
- HashSet: Fast, no order, uses hashCode
- TreeSet: Slower, sorted order, uses comparison

**Example:**
```java
// HashSet - fast lookup, no order
HashSet<String> userIds = new HashSet<>();
userIds.add("U103");
userIds.add("U101");
userIds.add("U102");
// Order: random

// TreeSet - sorted, slower
TreeSet<String> sortedIds = new TreeSet<>();
sortedIds.add("U103");
sortedIds.add("U101");
sortedIds.add("U102");
// Order: U101, U102, U103
```

---

## **TOPIC 20: Collections Framework - Map**

### Q112. What is HashMap?
**Answer:** Stores key-value pairs. Fast lookup by key. No duplicate keys.

**Example:**
```java
HashMap<String, Double> productPrices = new HashMap<>();

productPrices.put("Laptop", 45000.0);
productPrices.put("Mouse", 500.0);
productPrices.put("Keyboard", 1200.0);

double mousePrice = productPrices.get("Mouse");  // 500.0

// Check if key exists
if (productPrices.containsKey("Laptop")) {
    System.out.println("Laptop available");
}

// Loop through map
for (String product : productPrices.keySet()) {
    System.out.println(product + ": " + productPrices.get(product));
}
```

---

### Q113. What is LinkedHashMap?
**Answer:** Like HashMap but maintains insertion order.

**Example:**
```java
LinkedHashMap<String, Integer> orderItems = new LinkedHashMap<>();

orderItems.put("Pizza", 2);
orderItems.put("Burger", 1);
orderItems.put("Coke", 3);

// Maintains order: Pizza, Burger, Coke
for (String item : orderItems.keySet()) {
    System.out.println(item + ": " + orderItems.get(item));
}
```

---

### Q114. What is TreeMap?
**Answer:** Stores entries in sorted order by keys.

**Example:**
```java
TreeMap<String, Integer> employeeIds = new TreeMap<>();

employeeIds.put("John", 103);
employeeIds.put("Alice", 101);
employeeIds.put("Mike", 102);

// Sorted by key: Alice, John, Mike
for (String name : employeeIds.keySet()) {
    System.out.println(name + ": " + employeeIds.get(name));
}
```

---

### Q115. What is Hashtable?
**Answer:** Like HashMap but synchronized (thread-safe). Doesn't allow null keys/values.

**Example:**
```java
Hashtable<String, String> config = new Hashtable<>();

config.put("url", "localhost");
config.put("port", "8080");
// config.put(null, "value");  // ERROR! No null keys
// config.put("key", null);    // ERROR! No null values

// Thread-safe but slower than HashMap
```

---

### Q116. What is the difference between HashMap and Hashtable?
**Answer:**
- HashMap: Not synchronized, allows null, faster
- Hashtable: Synchronized, no null, slower

**Example:**
```java
// HashMap - single thread, allows null
HashMap<String, String> map1 = new HashMap<>();
map1.put(null, "value");  // OK
map1.put("key", null);    // OK

// Hashtable - multi-thread, no null
Hashtable<String, String> map2 = new Hashtable<>();
// map2.put(null, "value");  // ERROR!
```

---

### Q117. What are important Map methods?
**Answer:** put(), get(), remove(), containsKey(), containsValue(), keySet(), values(), entrySet()

**Example:**
```java
HashMap<Integer, String> employees = new HashMap<>();

employees.put(101, "John");
employees.put(102, "Mary");
employees.put(103, "Bob");

String name = employees.get(101);           // get value
employees.remove(102);                      // remove entry
boolean hasKey = employees.containsKey(103); // check key
boolean hasValue = employees.containsValue("Bob"); // check value

Set<Integer> ids = employees.keySet();      // all keys
Collection<String> names = employees.values(); // all values

// Loop using entrySet
for (Map.Entry<Integer, String> entry : employees.entrySet()) {
    System.out.println(entry.getKey() + ": " + entry.getValue());
}
```

---

I'm at about 60% completion.

**Remaining major topics:**
- Iterator and ListIterator
- Comparable and Comparator
- Generics
- Packages and Import Statements
- Type Casting (Upcasting/Downcasting)
- instanceof Operator
- Multithreading (Thread, Runnable, synchronization)
- File I/O basics
- Miscellaneous important questions

**Should I continue?** Type "continue" for the next set!

# Core Java Interview Questions (Continued)

## **TOPIC 21: Iterator and Iteration**

### Q118. What is an Iterator?
**Answer:** An object used to loop through collections one element at a time.

**Example:**
```java
ArrayList<String> products = new ArrayList<>();
products.add("Laptop");
products.add("Mouse");
products.add("Keyboard");

Iterator<String> it = products.iterator();
while (it.hasNext()) {
    String product = it.next();
    System.out.println(product);
}
```

---

### Q119. What is the difference between Iterator and ListIterator?
**Answer:**
- Iterator: Forward only, works with all collections
- ListIterator: Both directions, only for List

**Example:**
```java
ArrayList<String> items = new ArrayList<>();
items.add("Item1");
items.add("Item2");
items.add("Item3");

// ListIterator - can go backward
ListIterator<String> listIt = items.listIterator();

// Forward
while (listIt.hasNext()) {
    System.out.println(listIt.next());
}

// Backward
while (listIt.hasPrevious()) {
    System.out.println(listIt.previous());
}
```

---

### Q120. Can we remove elements while iterating?
**Answer:** Yes, but use Iterator's remove() method, not collection's remove().

**Example:**
```java
ArrayList<Integer> numbers = new ArrayList<>();
numbers.add(10);
numbers.add(20);
numbers.add(30);

Iterator<Integer> it = numbers.iterator();
while (it.hasNext()) {
    Integer num = it.next();
    if (num == 20) {
        it.remove();  // Safe removal
    }
}

// Wrong way - throws ConcurrentModificationException
for (Integer num : numbers) {
    if (num == 20) {
        // numbers.remove(num);  // ERROR!
    }
}
```

---

### Q121. What is the difference between Enumeration and Iterator?
**Answer:**
- Enumeration: Old, read-only, legacy classes
- Iterator: New, can remove elements

**Example:**
```java
Vector<String> vector = new Vector<>();
vector.add("A");
vector.add("B");

// Enumeration - old way
Enumeration<String> e = vector.elements();
while (e.hasMoreElements()) {
    System.out.println(e.nextElement());
}

// Iterator - modern way
Iterator<String> it = vector.iterator();
while (it.hasNext()) {
    System.out.println(it.next());
    it.remove();  // can remove
}
```

---

## **TOPIC 22: Comparable and Comparator**

### Q122. What is Comparable interface?
**Answer:** Used to define natural ordering of objects. Class implements compareTo() method.

**Example:**
```java
class Employee implements Comparable<Employee> {
    String name;
    int id;
    
    Employee(String name, int id) {
        this.name = name;
        this.id = id;
    }
    
    public int compareTo(Employee other) {
        return this.id - other.id;  // sort by id
    }
}

ArrayList<Employee> employees = new ArrayList<>();
employees.add(new Employee("John", 103));
employees.add(new Employee("Alice", 101));
employees.add(new Employee("Bob", 102));

Collections.sort(employees);  // sorts by id: 101, 102, 103
```

---

### Q123. What is Comparator interface?
**Answer:** Used to define custom ordering. Separate class implements compare() method.

**Example:**
```java
class Employee {
    String name;
    double salary;
    
    Employee(String name, double salary) {
        this.name = name;
        this.salary = salary;
    }
}

// Comparator to sort by salary
class SalaryComparator implements Comparator<Employee> {
    public int compare(Employee e1, Employee e2) {
        return (int)(e1.salary - e2.salary);
    }
}

ArrayList<Employee> employees = new ArrayList<>();
employees.add(new Employee("John", 50000));
employees.add(new Employee("Alice", 60000));
employees.add(new Employee("Bob", 45000));

Collections.sort(employees, new SalaryComparator());
// Sorted by salary: Bob, John, Alice
```

---

### Q124. What is the difference between Comparable and Comparator?
**Answer:**
- Comparable: Single natural ordering, modify class
- Comparator: Multiple custom orderings, separate class

**Example:**
```java
// Comparable - one way to sort (by id)
class Product implements Comparable<Product> {
    int id;
    String name;
    double price;
    
    public int compareTo(Product other) {
        return this.id - other.id;
    }
}

// Comparator - multiple ways to sort
class PriceComparator implements Comparator<Product> {
    public int compare(Product p1, Product p2) {
        return (int)(p1.price - p2.price);
    }
}

class NameComparator implements Comparator<Product> {
    public int compare(Product p1, Product p2) {
        return p1.name.compareTo(p2.name);
    }
}

// Can sort by price or name
Collections.sort(products, new PriceComparator());
Collections.sort(products, new NameComparator());
```

---

## **TOPIC 23: Generics**

### Q125. What are Generics?
**Answer:** Allow you to specify type of objects a collection can hold. Type safety at compile time.

**Example:**
```java
// Without generics - can add anything
ArrayList list = new ArrayList();
list.add("String");
list.add(100);  // mixing types - bad
String s = (String) list.get(0);  // need casting

// With generics - type safe
ArrayList<String> nameList = new ArrayList<String>();
nameList.add("John");
// nameList.add(100);  // ERROR! Can only add String
String name = nameList.get(0);  // no casting needed
```

---

### Q126. Why use Generics?
**Answer:** Type safety, no casting, compile-time error checking.

**Example:**
```java
// Without generics
ArrayList products = new ArrayList();
products.add("Laptop");
products.add(12345);  // wrong type, but compiles

String product = (String) products.get(1);  // Runtime error!

// With generics
ArrayList<String> productList = new ArrayList<>();
productList.add("Laptop");
// productList.add(12345);  // Compile error! Caught early
String item = productList.get(0);  // safe
```

---

### Q127. What is a generic class?
**Answer:** A class that can work with any data type.

**Example:**
```java
class Box<T> {
    T item;
    
    void set(T item) {
        this.item = item;
    }
    
    T get() {
        return item;
    }
}

// Use with String
Box<String> stringBox = new Box<>();
stringBox.set("Hello");
String text = stringBox.get();

// Use with Integer
Box<Integer> intBox = new Box<>();
intBox.set(100);
Integer num = intBox.get();
```

---

### Q128. What is a generic method?
**Answer:** A method that can work with any data type.

**Example:**
```java
class Utility {
    // Generic method
    public static <T> void printArray(T[] array) {
        for (T element : array) {
            System.out.println(element);
        }
    }
}

// Use with different types
String[] names = {"John", "Mary", "Bob"};
Integer[] numbers = {1, 2, 3, 4, 5};

Utility.printArray(names);    // works with String
Utility.printArray(numbers);  // works with Integer
```

---

### Q129. What are bounded type parameters?
**Answer:** Restrict generic type to specific types or subtypes.

**Example:**
```java
// Only accept Number or its subclasses
class Calculator<T extends Number> {
    T num1, num2;
    
    Calculator(T num1, T num2) {
        this.num1 = num1;
        this.num2 = num2;
    }
    
    double add() {
        return num1.doubleValue() + num2.doubleValue();
    }
}

Calculator<Integer> intCalc = new Calculator<>(10, 20);
Calculator<Double> doubleCalc = new Calculator<>(10.5, 20.5);
// Calculator<String> calc = new Calculator<>("a", "b");  // ERROR!
```

---

## **TOPIC 24: Packages**

### Q130. What is a package?
**Answer:** A folder/namespace that groups related classes together.

**Example:**
```java
// Package declaration
package com.company.employees;

public class Employee {
    String name;
    int id;
}

// Another file
package com.company.payroll;

import com.company.employees.Employee;

public class Salary {
    void calculate(Employee emp) {
        // calculate salary
    }
}
```

---

### Q131. What are types of packages?
**Answer:**
- Built-in: java.lang, java.util, java.io
- User-defined: created by developers

**Example:**
```java
import java.util.ArrayList;  // built-in package
import java.io.File;         // built-in package

import com.mycompany.Employee;  // user-defined package
```

---

### Q132. What is import statement?
**Answer:** Makes classes from other packages available in current file.

**Example:**
```java
// Import single class
import java.util.ArrayList;

// Import all classes from package
import java.util.*;

// Use without import (fully qualified name)
java.util.ArrayList<String> list = new java.util.ArrayList<>();
```

---

### Q133. What is a static import?
**Answer:** Import static members (variables/methods) directly.

**Example:**
```java
// Regular import
import java.lang.Math;
double result = Math.sqrt(16);
double pi = Math.PI;

// Static import
import static java.lang.Math.sqrt;
import static java.lang.Math.PI;

double result = sqrt(16);  // no Math. prefix
double piValue = PI;
```

---

### Q134. What is the default package?
**Answer:** When no package is specified, class belongs to default unnamed package.

**Example:**
```java
// No package statement - default package
public class Employee {
    String name;
}

// Not recommended for real projects
// Always use named packages
```

---

## **TOPIC 25: Type Casting**

### Q135. What is type casting?
**Answer:** Converting one data type to another.
- Widening: Small to large (automatic)
- Narrowing: Large to small (manual)

**Example:**
```java
// Widening (implicit) - no data loss
int num = 100;
double d = num;  // int to double - automatic

// Narrowing (explicit) - possible data loss
double price = 99.99;
int roundedPrice = (int) price;  // 99 (loses decimal)
```

---

### Q136. What is upcasting?
**Answer:** Child object referenced by parent type. Automatic.

**Example:**
```java
class Employee {
    void work() {
        System.out.println("Working");
    }
}

class Manager extends Employee {
    void conductMeeting() {
        System.out.println("Meeting");
    }
}

// Upcasting - automatic
Employee emp = new Manager();  // Manager object, Employee reference
emp.work();  // OK
// emp.conductMeeting();  // ERROR! Parent reference can't access child methods
```

---

### Q137. What is downcasting?
**Answer:** Parent reference converted to child type. Manual, risky.

**Example:**
```java
class Employee {
    void work() { }
}

class Manager extends Employee {
    void conductMeeting() { }
}

Employee emp = new Manager();  // upcasting

// Downcasting - manual
Manager mgr = (Manager) emp;
mgr.conductMeeting();  // Now can access Manager methods

// Risky - ClassCastException if wrong type
Employee emp2 = new Employee();
// Manager mgr2 = (Manager) emp2;  // Runtime error!
```

---

### Q138. What is the instanceof operator?
**Answer:** Checks if an object is an instance of a specific class.

**Example:**
```java
class Employee { }
class Manager extends Employee { }

Employee emp = new Manager();

if (emp instanceof Manager) {
    Manager mgr = (Manager) emp;  // safe downcasting
    System.out.println("Is a Manager");
}

if (emp instanceof Employee) {
    System.out.println("Is an Employee");  // true
}

// Use before downcasting to avoid errors
Object obj = "Hello";
if (obj instanceof String) {
    String str = (String) obj;  // safe
}
```

---

## **TOPIC 26: Multithreading Basics**

### Q139. What is a thread?
**Answer:** Smallest unit of execution. Allows multiple tasks to run simultaneously.

**Example:**
```java
// Real-world example: Banking application
// Thread 1: Processing withdrawals
// Thread 2: Processing deposits
// Thread 3: Generating statements
// All running at the same time
```

---

### Q140. What is multithreading?
**Answer:** Running multiple threads simultaneously to improve performance.

**Example:**
```java
// Single thread - sequential
downloadFile();  // takes 5 seconds
processData();   // takes 3 seconds
// Total: 8 seconds

// Multithreading - parallel
Thread t1 = new Thread(() -> downloadFile());
Thread t2 = new Thread(() -> processData());
t1.start();
t2.start();
// Total: 5 seconds (both run together)
```

---

### Q141. How to create a thread?
**Answer:** Two ways: Extend Thread class or implement Runnable interface.

**Example:**
```java
// Method 1: Extend Thread class
class MyThread extends Thread {
    public void run() {
        System.out.println("Thread running: " + getName());
    }
}

MyThread t1 = new MyThread();
t1.start();

// Method 2: Implement Runnable (preferred)
class MyTask implements Runnable {
    public void run() {
        System.out.println("Task running");
    }
}

Thread t2 = new Thread(new MyTask());
t2.start();
```

---

### Q142. What is the difference between Thread class and Runnable interface?
**Answer:**
- Thread: Direct extension, can't extend other class
- Runnable: Implementation, can extend other class, more flexible

**Example:**
```java
// Thread - can't extend another class
class MyThread extends Thread {
    // can't extend Employee class now
}

// Runnable - flexible
class MyTask extends Employee implements Runnable {
    public void run() {
        // can extend Employee and implement Runnable
    }
}
```

---

### Q143. What is the difference between start() and run()?
**Answer:**
- start(): Creates new thread and calls run()
- run(): Executes in same thread (no multithreading)

**Example:**
```java
class MyThread extends Thread {
    public void run() {
        System.out.println("Running in: " + Thread.currentThread().getName());
    }
}

MyThread t = new MyThread();
t.start();  // Creates new thread: Thread-0
t.run();    // Runs in main thread: main
```

---

### Q144. What are thread states?
**Answer:** NEW, RUNNABLE, RUNNING, BLOCKED, WAITING, TERMINATED

**Example:**
```java
Thread t = new Thread(() -> {
    System.out.println("Running");
});

// NEW - created but not started
System.out.println(t.getState());  // NEW

t.start();
// RUNNABLE - ready to run

// RUNNING - executing

// BLOCKED - waiting for resource

// TERMINATED - finished execution
```

---

### Q145. What are important thread methods?
**Answer:** start(), run(), sleep(), join(), yield(), interrupt()

**Example:**
```java
Thread t = new Thread(() -> {
    try {
        Thread.sleep(2000);  // pause for 2 seconds
        System.out.println("Task complete");
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
});

t.start();
t.join();  // wait for thread to finish
System.out.println("Main continues");
```

---

### Q146. What is synchronization?
**Answer:** Controlling access to shared resource by multiple threads. Prevents data corruption.

**Example:**
```java
class BankAccount {
    int balance = 1000;
    
    // Without synchronization - problem
    void withdraw(int amount) {
        if (balance >= amount) {
            // Thread 1 and Thread 2 both pass this check
            balance -= amount;
        }
    }
    
    // With synchronization - safe
    synchronized void safeWithdraw(int amount) {
        if (balance >= amount) {
            balance -= amount;  // only one thread at a time
        }
    }
}
```

---

### Q147. What is a synchronized block?
**Answer:** Synchronizes only specific code section, not entire method.

**Example:**
```java
class Counter {
    int count = 0;
    
    void increment() {
        // Some code - not synchronized
        System.out.println("Processing");
        
        // Only critical section synchronized
        synchronized(this) {
            count++;
        }
        
        // Some code - not synchronized
        System.out.println("Done");
    }
}
```

---

### Q148. What is a deadlock?
**Answer:** Two threads waiting for each other to release resources, both stuck forever.

**Example:**
```java
class Resource1 { }
class Resource2 { }

// Thread 1
synchronized(resource1) {
    synchronized(resource2) {
        // use both
    }
}

// Thread 2
synchronized(resource2) {
    synchronized(resource1) {
        // use both
    }
}

// Thread 1 locks resource1, waits for resource2
// Thread 2 locks resource2, waits for resource1
// DEADLOCK! Both waiting forever
```

---

## **TOPIC 27: File I/O Basics**

### Q149. What is File class?
**Answer:** Represents file or directory. Used for file operations.

**Example:**
```java
import java.io.File;

File file = new File("employee.txt");

if (file.exists()) {
    System.out.println("File exists");
    System.out.println("Size: " + file.length() + " bytes");
    System.out.println("Name: " + file.getName());
    System.out.println("Path: " + file.getAbsolutePath());
}

// Create new file
File newFile = new File("data.txt");
newFile.createNewFile();

// Delete file
file.delete();
```

---

### Q150. How to read from a file?
**Answer:** Use FileReader, BufferedReader, or Scanner.

**Example:**
```java
import java.io.*;

// Method 1: BufferedReader
try {
    BufferedReader reader = new BufferedReader(new FileReader("data.txt"));
    String line;
    while ((line = reader.readLine()) != null) {
        System.out.println(line);
    }
    reader.close();
} catch (IOException e) {
    e.printStackTrace();
}

// Method 2: Scanner
Scanner sc = new Scanner(new File("data.txt"));
while (sc.hasNextLine()) {
    System.out.println(sc.nextLine());
}
sc.close();
```

---

### Q151. How to write to a file?
**Answer:** Use FileWriter or BufferedWriter.

**Example:**
```java
import java.io.*;

// FileWriter
try {
    FileWriter writer = new FileWriter("output.txt");
    writer.write("Employee Name: John\n");
    writer.write("Employee ID: 101\n");
    writer.close();
} catch (IOException e) {
    e.printStackTrace();
}

// BufferedWriter - more efficient
try {
    BufferedWriter writer = new BufferedWriter(new FileWriter("log.txt"));
    writer.write("Transaction completed");
    writer.newLine();
    writer.write("Amount: 5000");
    writer.close();
} catch (IOException e) {
    e.printStackTrace();
}
```

---

### Q152. What is the difference between FileWriter and BufferedWriter?
**Answer:**
- FileWriter: Writes directly to file (slower)
- BufferedWriter: Uses buffer (faster, efficient)

**Example:**
```java
// FileWriter - writes character by character
FileWriter fw = new FileWriter("data.txt");
fw.write("Hello");  // writes immediately

// BufferedWriter - uses memory buffer
BufferedWriter bw = new BufferedWriter(new FileWriter("data.txt"));
bw.write("Hello");  // stores in buffer
bw.flush();         // writes to file when buffer is full or flush() called
```

---

## **TOPIC 28: Miscellaneous Important Questions**

### Q153. What is the main method?
**Answer:** Entry point of Java program. JVM starts execution from here.

**Example:**
```java
public class Application {
    public static void main(String[] args) {
        System.out.println("Program starts here");
    }
}

// public: accessible from anywhere
// static: can be called without creating object
// void: doesn't return anything
// String[] args: command line arguments
```

---

### Q154. What are command line arguments?
**Answer:** Arguments passed to program when running from command line.

**Example:**
```java
public class Calculator {
    public static void main(String[] args) {
        if (args.length == 2) {
            int num1 = Integer.parseInt(args[0]);
            int num2 = Integer.parseInt(args[1]);
            System.out.println("Sum: " + (num1 + num2));
        }
    }
}

// Run: java Calculator 10 20
// Output: Sum: 30
```

---

### Q155. What is garbage collection?
**Answer:** Automatic memory management. JVM removes unused objects.

**Example:**
```java
class Employee {
    String name;
}

public class Test {
    public static void main(String[] args) {
        Employee emp1 = new Employee();
        emp1.name = "John";
        
        emp1 = null;  // Object becomes eligible for garbage collection
        
        // JVM automatically removes the object from memory
        System.gc();  // Request garbage collection (not guaranteed immediately)
    }
}
```

---

### Q156. Can we override main method?
**Answer:** No. Main method is static. Static methods are hidden, not overridden.

**Example:**
```java
class Parent {
    public static void main(String[] args) {
        System.out.println("Parent main");
    }
}

class Child extends Parent {
    public static void main(String[] args) {
        System.out.println("Child main");
    }
}

// Both main methods exist separately
// java Parent  -> "Parent main"
// java Child   -> "Child main"
```

---

### Q157. What is method signature?
**Answer:** Method name + parameter list (not return type).

**Example:**
```java
class Calculator {
    // Method signature: add(int, int)
    int add(int a, int b) {
        return a + b;
    }
    
    // Different signature: add(double, double)
    double add(double a, double b) {
        return a + b;
    }
    
    // Same signature as first - ERROR!
    // double add(int a, int b) {  // return type doesn't matter
    //     return a + b;
    // }
}
```

---

### Q158. What is pass by value in Java?
**Answer:** Java always passes copy of value. Changes don't affect original for primitives.

**Example:**
```java
class Test {
    static void changeValue(int num) {
        num = 100;  // changes copy, not original
    }
    
    static void changeObject(Employee emp) {
        emp.name = "Changed";  // changes object content (reference is copied)
    }
    
    public static void main(String[] args) {
        int x = 10;
        changeValue(x);
        System.out.println(x);  // 10 (unchanged)
        
        Employee e = new Employee();
        e.name = "John";
        changeObject(e);
        System.out.println(e.name);  // Changed
    }
}
```

---

### Q159. What is the difference between break and continue?
**Answer:**
- break: Exit loop completely
- continue: Skip current iteration, continue loop

**Example:**
```java
// break - exit loop
for (int i = 1; i <= 5; i++) {
    if (i == 3) {
        break;  // exits loop
    }
    System.out.println(i);
}
// Output: 1, 2

// continue - skip iteration
for (int i = 1; i <= 5; i++) {
    if (i == 3) {
        continue;  // skips 3, continues with 4
    }
    System.out.println(i);
}
// Output: 1, 2, 4, 5
```

---

### Q160. What is the this() and super() constructor call?
**Answer:**
- this(): Calls another constructor in same class
- super(): Calls parent class constructor

**Example:**
```java
class Employee {
    String name;
    int id;
    
    Employee() {
        this("Unknown", 0);  // calls parameterized constructor
    }
    
    Employee(String name, int id) {
        this.name = name;
        this.id = id;
    }
}

class Manager extends Employee {
    String department;
    
    Manager(String name, int id, String dept) {
        super(name, id);  // calls parent constructor
        this.department = dept;
    }
}
```

---

### Q161. What is a constructor chaining?
**Answer:** Calling one constructor from another using this() or super().

**Example:**
```java
class Product {
    String name;
    double price;
    String category;
    
    Product(String name) {
        this(name, 0.0);  // calls 2-param constructor
    }
    
    Product(String name, double price) {
        this(name, price, "General");  // calls 3-param constructor
    }
    
    Product(String name, double price, String category) {
        this.name = name;
        this.price = price;
        this.category = category;
    }
}

Product p1 = new Product("Laptop");
// Chains: 1-param -> 2-param -> 3-param constructor
```

---

### Q162. What is covariant return type?
**Answer:** Overridden method can return subtype of original return type.

**Example:**
```java
class Animal {
    Animal getAnimal() {
        return new Animal();
    }
}

class Dog extends Animal {
    Dog getAnimal() {  // returns Dog (subtype of Animal)
        return new Dog();
    }
}
```

---

### Q163. What is the difference between StringBuffer and StringBuilder?
**Answer:**
- StringBuffer: Thread-safe, synchronized, slower
- StringBuilder: Not thread-safe, not synchronized, faster

**Example:**
```java
// StringBuffer - safe for multi-threading
StringBuffer sb1 = new StringBuffer("Hello");
sb1.append(" World");  // thread-safe

// StringBuilder - faster for single thread
StringBuilder sb2 = new StringBuilder("Hello");
sb2.append(" World");  // faster but not thread-safe

// Use StringBuilder in most cases (single-threaded)
```

---

### Q164. What is shallow copy vs deep copy?
**Answer:**
- Shallow: Copies object reference (both point to same object)
- Deep: Creates new object with copied values

**Example:**
```java
class Employee {
    String name;
    int id;
}

// Shallow copy
Employee emp1 = new Employee();
emp1.name = "John";

Employee emp2 = emp1;  // shallow copy (same object)
emp2.name = "Mary";
System.out.println(emp1.name);  // Mary (both changed)

// Deep copy
Employee emp3 = new Employee();
emp3.name = emp1.name;  // new object with copied values
emp3.id = emp1.id;
```

---

### Q165. What is the classpath?
**Answer:** Path where JVM looks for class files and libraries.

**Example:**
```bash
# Set classpath
java -classpath /path/to/classes MyProgram

# Or
set CLASSPATH=/path/to/classes;/path/to/lib.jar

# JVM searches these locations for .class files
```

---

I'm at approximately 85% completion!

**Remaining topics:**
- Few more String methods
- Math class
- System class
- Runtime class
- Some edge case questions
- Best practices questions

**Should I continue with the final set?** Type "continue" for the remaining questions!

# Core Java Interview Questions (Final Set)

## **TOPIC 29: String Methods (Additional)**

### Q166. What is the concat() method?
**Answer:** Joins two strings together.

**Example:**
```java
String firstName = "John";
String lastName = "Smith";

String fullName = firstName.concat(" ").concat(lastName);
System.out.println(fullName);  // John Smith

// Same as using +
String fullName2 = firstName + " " + lastName;
```

---

### Q167. What is the replace() method?
**Answer:** Replaces characters or substrings in a string.

**Example:**
```java
String email = "admin@oldcompany.com";

// Replace character
String newEmail = email.replace('o', '0');
System.out.println(newEmail);  // admin@0ldcompany.c0m

// Replace substring
String updated = email.replace("oldcompany", "newcompany");
System.out.println(updated);  // admin@newcompany.com

// Real use: masking phone numbers
String phone = "9876543210";
String masked = phone.replace(phone.substring(0, 6), "******");
System.out.println(masked);  // ******3210
```

---

### Q168. What is the trim() method?
**Answer:** Removes leading and trailing spaces.

**Example:**
```java
String username = "  john123  ";
String cleaned = username.trim();
System.out.println(cleaned);  // "john123"

// Use case: user input validation
String input = "  admin  ";
if (input.trim().equals("admin")) {
    System.out.println("Valid username");
}
```

---

### Q169. What is the split() method?
**Answer:** Splits string into array based on delimiter.

**Example:**
```java
String data = "John,25,Developer,50000";
String[] parts = data.split(",");

System.out.println(parts[0]);  // John
System.out.println(parts[1]);  // 25
System.out.println(parts[2]);  // Developer

// Real use: parsing CSV data
String csvLine = "Laptop,45000,Electronics";
String[] productInfo = csvLine.split(",");
String productName = productInfo[0];
double price = Double.parseDouble(productInfo[1]);
```

---

### Q170. What is the startsWith() and endsWith() method?
**Answer:** Checks if string starts or ends with specific substring.

**Example:**
```java
String filename = "report_2024.pdf";

if (filename.startsWith("report")) {
    System.out.println("This is a report file");
}

if (filename.endsWith(".pdf")) {
    System.out.println("PDF file");
}

// Use case: file type validation
String[] files = {"doc.pdf", "image.jpg", "data.xlsx"};
for (String file : files) {
    if (file.endsWith(".pdf")) {
        System.out.println("Process PDF: " + file);
    }
}
```

---

### Q171. What is the contains() method?
**Answer:** Checks if string contains a specific sequence.

**Example:**
```java
String email = "john.doe@company.com";

if (email.contains("@")) {
    System.out.println("Valid email format");
}

// Use case: search functionality
String productDesc = "Wireless Bluetooth Headphones";
if (productDesc.contains("Bluetooth")) {
    System.out.println("Bluetooth product found");
}
```

---

### Q172. What is isEmpty() vs isBlank()?
**Answer:**
- isEmpty(): Checks if length is 0
- isBlank(): Checks if empty or only whitespace (Java 11+)

**Example:**
```java
String str1 = "";
String str2 = "   ";
String str3 = "Hello";

System.out.println(str1.isEmpty());   // true
System.out.println(str2.isEmpty());   // false (has spaces)

// isBlank (Java 11+)
// System.out.println(str1.isBlank());   // true
// System.out.println(str2.isBlank());   // true (only spaces)
// System.out.println(str3.isBlank());   // false
```

---

## **TOPIC 30: Math Class**

### Q173. What is the Math class?
**Answer:** Provides mathematical operations like power, square root, rounding, etc.

**Example:**
```java
// Common Math methods
double result1 = Math.sqrt(16);        // 4.0
double result2 = Math.pow(2, 3);       // 8.0 (2^3)
int result3 = Math.abs(-10);           // 10
double result4 = Math.max(10, 20);     // 20.0
double result5 = Math.min(10, 20);     // 10.0

// Constants
double pi = Math.PI;                   // 3.141592653589793
double e = Math.E;                     // 2.718281828459045
```

---

### Q174. What are rounding methods in Math class?
**Answer:** ceil(), floor(), round()

**Example:**
```java
double price = 99.45;

// ceil - rounds up
double ceilValue = Math.ceil(price);    // 100.0

// floor - rounds down
double floorValue = Math.floor(price);  // 99.0

// round - nearest integer
long roundValue = Math.round(price);    // 99

// Use case: calculating total pages
double totalItems = 47;
double itemsPerPage = 10;
int totalPages = (int) Math.ceil(totalItems / itemsPerPage);
System.out.println(totalPages);  // 5 pages
```

---

### Q175. How to generate random numbers?
**Answer:** Use Math.random() or Random class.

**Example:**
```java
// Math.random() - returns 0.0 to 1.0
double random = Math.random();

// Random number between 1 and 100
int randomNum = (int)(Math.random() * 100) + 1;

// Random class (better control)
Random rand = new Random();
int num1 = rand.nextInt(100);        // 0 to 99
int num2 = rand.nextInt(50) + 1;     // 1 to 50

// Use case: OTP generation
int otp = (int)(Math.random() * 9000) + 1000;  // 4-digit OTP
System.out.println("Your OTP: " + otp);
```

---

## **TOPIC 31: System Class**

### Q176. What is the System class?
**Answer:** Provides access to system resources like input/output, time, properties.

**Example:**
```java
// Standard output
System.out.println("Hello");

// Standard error
System.err.println("Error occurred");

// Current time in milliseconds
long currentTime = System.currentTimeMillis();

// Exit program
System.exit(0);  // 0 = normal termination

// Get system properties
String osName = System.getProperty("os.name");
String userName = System.getProperty("user.name");
```

---

### Q177. How to measure code execution time?
**Answer:** Use System.currentTimeMillis() or System.nanoTime().

**Example:**
```java
class PerformanceTest {
    public static void main(String[] args) {
        long startTime = System.currentTimeMillis();
        
        // Code to measure
        for (int i = 0; i < 1000000; i++) {
            // some operation
        }
        
        long endTime = System.currentTimeMillis();
        long duration = endTime - startTime;
        
        System.out.println("Execution time: " + duration + " ms");
    }
}

// More precise with nanoTime
long start = System.nanoTime();
// code
long end = System.nanoTime();
System.out.println("Time: " + (end - start) + " nanoseconds");
```

---

### Q178. What is System.arraycopy()?
**Answer:** Copies elements from one array to another.

**Example:**
```java
int[] source = {1, 2, 3, 4, 5};
int[] destination = new int[5];

// Copy from source to destination
System.arraycopy(source, 0, destination, 0, 5);
// source: source array
// 0: start position in source
// destination: destination array
// 0: start position in destination
// 5: number of elements to copy

System.out.println(Arrays.toString(destination));  // [1, 2, 3, 4, 5]

// Partial copy
int[] partial = new int[3];
System.arraycopy(source, 1, partial, 0, 3);
System.out.println(Arrays.toString(partial));  // [2, 3, 4]
```

---

## **TOPIC 32: Wrapper Classes (Additional)**

### Q179. What is parseInt() and valueOf()?
**Answer:**
- parseInt(): Converts String to primitive int
- valueOf(): Converts String to Integer object

**Example:**
```java
String numberStr = "123";

// parseInt - returns primitive
int num1 = Integer.parseInt(numberStr);
System.out.println(num1 + 10);  // 133

// valueOf - returns object
Integer num2 = Integer.valueOf(numberStr);
System.out.println(num2 + 10);  // 133

// Use case: form data processing
String ageInput = "25";
int age = Integer.parseInt(ageInput);
if (age >= 18) {
    System.out.println("Eligible");
}
```

---

### Q180. What are MIN_VALUE and MAX_VALUE?
**Answer:** Constants representing minimum and maximum values for numeric types.

**Example:**
```java
System.out.println(Integer.MIN_VALUE);  // -2147483648
System.out.println(Integer.MAX_VALUE);  // 2147483647

System.out.println(Long.MIN_VALUE);
System.out.println(Long.MAX_VALUE);

// Use case: validation
int salary = 2147483647;
if (salary == Integer.MAX_VALUE) {
    System.out.println("Salary at maximum limit");
}
```

---

## **TOPIC 33: Important Edge Cases and Tricky Questions**

### Q181. What happens if we don't have a main method?
**Answer:** Compilation succeeds, but runtime error occurs when running.

**Example:**
```java
class Test {
    // No main method
    void display() {
        System.out.println("Hello");
    }
}

// javac Test.java  - compiles successfully
// java Test        - Error: Main method not found
```

---

### Q182. Can we have multiple classes in a single file?
**Answer:** Yes, but only one can be public, and file name must match public class.

**Example:**
```java
// File: Employee.java
public class Employee {
    String name;
}

class Manager {
    String department;
}

class Developer {
    String language;
}

// Only Employee can be public
// File name must be Employee.java
```

---

### Q183. What is the difference between length and length()?
**Answer:**
- length: Property for arrays
- length(): Method for Strings

**Example:**
```java
int[] numbers = {1, 2, 3, 4, 5};
System.out.println(numbers.length);  // 5 (property, no parentheses)

String name = "John";
System.out.println(name.length());   // 4 (method, with parentheses)
```

---

### Q184. Can we declare main method as private?
**Answer:** Yes, but JVM cannot access it, runtime error occurs.

**Example:**
```java
class Test {
    private static void main(String[] args) {
        System.out.println("Hello");
    }
}

// Compiles successfully
// Runtime error: Main method not public
```

---

### Q185. What happens if static block throws exception?
**Answer:** Class initialization fails, ExceptionInInitializerError occurs.

**Example:**
```java
class Database {
    static {
        int result = 10 / 0;  // ArithmeticException
    }
    
    public static void main(String[] args) {
        System.out.println("Hello");
    }
}

// ExceptionInInitializerError - class initialization failed
```

---

### Q186. Can constructor be private?
**Answer:** Yes. Used in Singleton pattern to restrict object creation.

**Example:**
```java
class DatabaseConnection {
    private static DatabaseConnection instance;
    
    // Private constructor
    private DatabaseConnection() {
        // initialization
    }
    
    // Public method to get single instance
    public static DatabaseConnection getInstance() {
        if (instance == null) {
            instance = new DatabaseConnection();
        }
        return instance;
    }
}

// DatabaseConnection db = new DatabaseConnection();  // ERROR!
DatabaseConnection db = DatabaseConnection.getInstance();  // OK
```

---

### Q187. What is the difference between == and equals() for Integer?
**Answer:** == compares references, equals() compares values. Integer caching affects ==.

**Example:**
```java
Integer a = 127;
Integer b = 127;
System.out.println(a == b);        // true (cached, same object)

Integer c = 128;
Integer d = 128;
System.out.println(c == d);        // false (not cached, different objects)
System.out.println(c.equals(d));   // true (same value)

// Java caches Integer objects from -128 to 127
```

---

### Q188. Can we override equals() without overriding hashCode()?
**Answer:** Yes, but it breaks HashMap/HashSet behavior. Both should be overridden together.

**Example:**
```java
class Employee {
    int id;
    
    public boolean equals(Object obj) {
        if (obj instanceof Employee) {
            return this.id == ((Employee) obj).id;
        }
        return false;
    }
    
    // Missing hashCode() - PROBLEM!
}

Employee e1 = new Employee();
e1.id = 101;

Employee e2 = new Employee();
e2.id = 101;

System.out.println(e1.equals(e2));  // true

HashMap<Employee, String> map = new HashMap<>();
map.put(e1, "John");
System.out.println(map.get(e2));  // null (should be "John")
// Problem: Different hashCodes even though equals returns true
```

---

### Q189. What happens when we divide by zero?
**Answer:**
- Integer division by zero: ArithmeticException
- Floating point division by zero: Infinity or NaN

**Example:**
```java
// Integer division
int a = 10;
int b = 0;
// int result = a / b;  // ArithmeticException

// Floating point division
double x = 10.0;
double y = 0.0;
double result = x / y;
System.out.println(result);  // Infinity

double nan = 0.0 / 0.0;
System.out.println(nan);     // NaN (Not a Number)
```

---

### Q190. Can we call non-static method from static context?
**Answer:** No. Need to create object first.

**Example:**
```java
class Calculator {
    void add(int a, int b) {  // non-static
        System.out.println(a + b);
    }
    
    static void multiply(int a, int b) {  // static
        // add(5, 3);  // ERROR! Can't call non-static from static
        
        Calculator calc = new Calculator();
        calc.add(5, 3);  // OK - using object
    }
}
```

---

## **TOPIC 34: Best Practices & Common Mistakes**

### Q191. Why should we use StringBuilder over String concatenation in loops?
**Answer:** String is immutable. Each concatenation creates new object, wastes memory.

**Example:**
```java
// Bad practice - creates 1000 String objects
String result = "";
for (int i = 0; i < 1000; i++) {
    result += i;  // creates new String each time
}

// Good practice - single StringBuilder object
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 1000; i++) {
    sb.append(i);  // modifies same object
}
String result2 = sb.toString();
```

---

### Q192. Why should we close resources in finally block?
**Answer:** Ensures resources are closed even if exception occurs.

**Example:**
```java
// Bad practice
BufferedReader reader = new BufferedReader(new FileReader("data.txt"));
String line = reader.readLine();
reader.close();  // Won't execute if readLine() throws exception

// Good practice
BufferedReader reader = null;
try {
    reader = new BufferedReader(new FileReader("data.txt"));
    String line = reader.readLine();
} catch (IOException e) {
    e.printStackTrace();
} finally {
    if (reader != null) {
        try {
            reader.close();  // Always executes
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

---

### Q193. Why should we avoid using == for String comparison?
**Answer:** == compares references, not content. Use equals() for content comparison.

**Example:**
```java
String password1 = new String("admin123");
String password2 = new String("admin123");

if (password1 == password2) {
    // Never executes - different objects
}

if (password1.equals(password2)) {
    System.out.println("Password match");  // Correct way
}
```

---

### Q194. Why should we use interface over abstract class for multiple inheritance?
**Answer:** Java doesn't support multiple class inheritance, but supports multiple interface implementation.

**Example:**
```java
// Can't do this
class Employee extends Person, Worker {  // ERROR!
}

// Can do this
class Employee implements Workable, Payable {  // OK
}
```

---

### Q195. Why should we override toString() method?
**Answer:** Provides meaningful string representation instead of classname@hashcode.

**Example:**
```java
class Employee {
    String name;
    int id;
    
    // Without toString
    // Output: Employee@15db9742 (not useful)
    
    // With toString
    public String toString() {
        return "Employee{name='" + name + "', id=" + id + "}";
    }
}

Employee emp = new Employee();
emp.name = "John";
emp.id = 101;
System.out.println(emp);  // Employee{name='John', id=101}
```

---

### Q196. Why should we use private variables with public getters/setters?
**Answer:** Encapsulation - control access, validate data, protect from invalid values.

**Example:**
```java
// Bad practice - direct access
class BankAccount {
    public double balance;
}
BankAccount acc = new BankAccount();
acc.balance = -1000;  // Invalid! No validation

// Good practice - encapsulation
class BankAccount {
    private double balance;
    
    public void setBalance(double balance) {
        if (balance >= 0) {
            this.balance = balance;
        } else {
            System.out.println("Invalid balance");
        }
    }
    
    public double getBalance() {
        return balance;
    }
}
```

---

### Q197. Why should we use appropriate collection?
**Answer:** Different collections have different performance characteristics.

**Example:**
```java
// Need fast access by index? Use ArrayList
ArrayList<String> names = new ArrayList<>();
String name = names.get(100);  // Fast O(1)

// Need unique elements? Use HashSet
HashSet<String> uniqueEmails = new HashSet<>();
uniqueEmails.add("john@mail.com");  // No duplicates

// Need sorted elements? Use TreeSet
TreeSet<Integer> scores = new TreeSet<>();
// Automatically sorted

// Need key-value pairs? Use HashMap
HashMap<String, Double> prices = new HashMap<>();
double price = prices.get("Laptop");  // Fast lookup
```

---

### Q198. Why should we handle exceptions?
**Answer:** Prevents program crash, provides graceful error handling, better user experience.

**Example:**
```java
// Bad practice - program crashes
public void processPayment(double amount) {
    int tax = (int) (amount / 0);  // Crashes application
}

// Good practice - graceful handling
public void processPayment(double amount) {
    try {
        if (amount <= 0) {
            throw new IllegalArgumentException("Invalid amount");
        }
        // process payment
    } catch (Exception e) {
        System.out.println("Payment failed: " + e.getMessage());
        // log error, notify admin
    }
}
```

---

### Q199. When to use ArrayList vs LinkedList?
**Answer:**
- ArrayList: Frequent access by index, less insertion/deletion
- LinkedList: Frequent insertion/deletion, less random access

**Example:**
```java
// ArrayList - reading customer data frequently
ArrayList<Customer> customers = new ArrayList<>();
Customer c = customers.get(100);  // Fast random access

// LinkedList - task queue with frequent add/remove
LinkedList<Task> taskQueue = new LinkedList<>();
taskQueue.addFirst(urgentTask);   // Fast insertion
taskQueue.removeFirst();          // Fast removal
```

---

### Q200. When to use HashMap vs TreeMap?
**Answer:**
- HashMap: Fast lookup, no ordering needed
- TreeMap: Need sorted order, slower

**Example:**
```java
// HashMap - fast product lookup
HashMap<String, Double> productPrices = new HashMap<>();
double price = productPrices.get("Laptop");  // Very fast O(1)

// TreeMap - need sorted employee list
TreeMap<Integer, Employee> employees = new TreeMap<>();
// Automatically sorted by employee ID
for (Integer id : employees.keySet()) {
    System.out.println("ID: " + id);  // In order: 101, 102, 103...
}
```

---

## **BONUS: Quick Fire Questions**

### Q201. Can we have constructor in interface?
**Answer:** No. Interfaces cannot have constructors.

---

### Q202. Can abstract class have final methods?
**Answer:** Yes. Final methods cannot be overridden.

---

### Q203. Can we declare interface as final?
**Answer:** No. Interfaces are meant to be implemented.

---

### Q204. Can we have static methods in interface?
**Answer:** Yes (from Java 8+).

---

### Q205. What is default method size of ArrayList?
**Answer:** 10 elements initially, grows by 50% when full.

---

### Q206. Is String thread-safe?
**Answer:** Yes. String is immutable, so thread-safe.

---

### Q207. Is HashMap thread-safe?
**Answer:** No. Use ConcurrentHashMap for thread safety.

---

### Q208. Can we serialize static variables?
**Answer:** No. Static variables belong to class, not object.

---

### Q209. What is the parent class of all exceptions?
**Answer:** Throwable class.

---

### Q210. Can we have try without catch?
**Answer:** Yes, if followed by finally block.

---

## **COMPLETION**

🎉 **ALL 210+ CORE JAVA INTERVIEW QUESTIONS COMPLETED!**

**Topics Covered:**
1. ✅ Java Basics (JVM, JDK, JRE)
2. ✅ Data Types & Variables
3. ✅ Operators
4. ✅ Control Statements
5. ✅ Arrays
6. ✅ Strings
7. ✅ OOP Concepts (Classes, Objects, Constructors)
8. ✅ Inheritance
9. ✅ Polymorphism
10. ✅ Abstraction
11. ✅ Interfaces
12. ✅ Encapsulation
13. ✅ Access Modifiers
14. ✅ Static Keyword
15. ✅ Final Keyword
16. ✅ Exception Handling
17. ✅ Object Class Methods
18. ✅ Collections - List (ArrayList, LinkedList, Vector)
19. ✅ Collections - Set (HashSet, TreeSet, LinkedHashSet)
20. ✅ Collections - Map (HashMap, TreeMap, Hashtable)
21. ✅ Iterator & ListIterator
22. ✅ Comparable & Comparator
23. ✅ Generics
24. ✅ Packages & Imports
25. ✅ Type Casting
26. ✅ Multithreading Basics
27. ✅ File I/O Basics
28. ✅ Miscellaneous Topics
29. ✅ String Methods
30. ✅ Math Class
31. ✅ System Class
32. ✅ Wrapper Classes
33. ✅ Edge Cases
34. ✅ Best Practices

**This covers ALL core Java concepts (pre-Java 8) as requested!**

Good luck with your interview preparation! 🚀