# Java 8-25 Interview Questions & Answers

## LAMBDA EXPRESSIONS

### Q1: What are lambda expressions and why do we use them?
Lambda expressions are basically a short way to write anonymous functions. Before Java 8, if you wanted to pass behavior as a parameter, you had to create anonymous inner classes which was very verbose.

**Example:** Imagine you're building an e-commerce system and need to sort products by price.

```java
// Old way (before Java 8)
Collections.sort(products, new Comparator<Product>() {
    public int compare(Product p1, Product p2) {
        return p1.getPrice().compareTo(p2.getPrice());
    }
});

// Lambda way (Java 8+)
Collections.sort(products, (p1, p2) -> p1.getPrice().compareTo(p2.getPrice()));
```

The lambda makes it much cleaner and readable. You're just saying "given two products, compare their prices" without all the ceremony.

---

### Q2: What is the syntax of lambda expressions?
Lambda syntax has three parts: parameters, arrow token, and body.

Basic forms:
- `() -> System.out.println("No parameters")`
- `(x) -> x * x` (single parameter)
- `(x, y) -> x + y` (multiple parameters)
- `(x, y) -> { int sum = x + y; return sum; }` (multiple statements)

**Industry example:** In a notification service, you might filter customers who want email notifications:

```java
List<Customer> emailCustomers = customers.stream()
    .filter(customer -> customer.isEmailEnabled())
    .collect(Collectors.toList());
```

Here `customer -> customer.isEmailEnabled()` is the lambda taking one parameter and returning a boolean.

---

### Q3: What are functional interfaces?
A functional interface is an interface with exactly one abstract method. Java 8 added the `@FunctionalInterface` annotation to mark them explicitly. Lambda expressions work because they implement these functional interfaces.

**Example:** In your PerformanceTrack system, you might create a custom validator:

```java
@FunctionalInterface
interface PerformanceValidator {
    boolean validate(Review review);
}

// Usage
PerformanceValidator scoreValidator = review -> review.getScore() >= 1 && review.getScore() <= 5;

if (scoreValidator.validate(employeeReview)) {
    // Process review
}
```

Common built-in functional interfaces: `Predicate<T>`, `Function<T,R>`, `Consumer<T>`, `Supplier<T>`.

---

### Q4: What is the difference between Predicate, Function, Consumer, and Supplier?
These are the four main functional interfaces in Java:

**Predicate**: Takes input, returns boolean. Used for filtering/testing.
```java
Predicate<Employee> isManager = emp -> emp.getRole().equals("MANAGER");
```

**Function**: Takes input, returns output. Used for transformations.
```java
Function<Employee, String> getFullName = emp -> emp.getFirstName() + " " + emp.getLastName();
```

**Consumer**: Takes input, returns nothing. Used for operations with side effects.
```java
Consumer<Review> printReview = review -> System.out.println(review.getComments());
```

**Supplier**: Takes no input, returns output. Used for lazy generation.
```java
Supplier<String> generateReportId = () -> "RPT-" + UUID.randomUUID();
```

**Industry scenario:** In a reporting system, you'd use Predicate to filter reviews, Function to transform data, Consumer to send notifications, and Supplier to generate unique IDs.

---

### Q5: Can you write a lambda expression with multiple statements?
Yes, use curly braces and explicit return statement for multiple lines.

**Single statement (implicit return):**
```java
Function<Integer, Integer> square = x -> x * x;
```

**Multiple statements (explicit return):**
```java
Function<Review, String> processReview = review -> {
    int score = review.getScore();
    String status = review.getStatus();
    String result = "Score: " + score + ", Status: " + status;
    return result;
};
```

**Real PerformanceTrack example:**
```java
Consumer<Employee> processEmployee = emp -> {
    System.out.println("Processing: " + emp.getName());
    List<Review> reviews = getReviews(emp.getId());
    double avg = calculateAverage(reviews);
    emp.setAverageScore(avg);
    saveEmployee(emp);
};
```

---

### Q6: What is variable capture in lambdas? What are effectively final variables?
Lambdas can access variables from the enclosing scope, but those variables must be effectively final (not modified after initialization).

**This works:**
```java
String department = "Engineering";  // Effectively final

List<Employee> engineers = employees.stream()
    .filter(emp -> emp.getDepartment().equals(department))  // Can use department
    .toList();
```

**This fails:**
```java
String department = "Engineering";
department = "Sales";  // Modified!

List<Employee> filtered = employees.stream()
    .filter(emp -> emp.getDepartment().equals(department))  // ERROR!
    .toList();
```

**Why this restriction?** Lambdas might execute later or on different threads. If variables could change, you'd get unpredictable results.

**Real scenario in PerformanceTrack:**
```java
public List<Review> getReviewsByScore(int minScore) {
    // minScore is effectively final (parameter)
    return reviews.stream()
        .filter(review -> review.getScore() >= minScore)  // OK
        .toList();
}
```

---

### Q7: What's the difference between lambda expressions and anonymous inner classes?
**Key differences:**

1. **`this` keyword:**
```java
// Anonymous class - 'this' refers to anonymous class instance
Runnable r1 = new Runnable() {
    public void run() {
        System.out.println(this.getClass());  // Anonymous$1
    }
};

// Lambda - 'this' refers to enclosing class instance
Runnable r2 = () -> {
    System.out.println(this.getClass());  // EnclosingClass
};
```

2. **Compilation:**
- Anonymous class creates a separate .class file
- Lambda uses invokedynamic (more efficient)

3. **Scope:**
- Anonymous class introduces new scope
- Lambda shares scope with enclosing method

**Example in PerformanceTrack:**
```java
public class ReviewService {
    private String serviceName = "ReviewService";
    
    public void processReviews() {
        // Anonymous class
        reviews.forEach(new Consumer<Review>() {
            public void accept(Review review) {
                // Can't access serviceName directly without ReviewService.this
            }
        });
        
        // Lambda - can access serviceName directly
        reviews.forEach(review -> {
            System.out.println(serviceName);  // Works!
        });
    }
}
```

---

### Q8: Can lambdas throw checked exceptions?
Lambda expressions can throw exceptions, but the functional interface must declare them.

**Problem:**
```java
// This won't compile - IOException not declared
List<String> lines = files.stream()
    .map(file -> Files.readString(file))  // ERROR: readString throws IOException
    .toList();
```

**Solution 1: Wrap in unchecked exception**
```java
List<String> lines = files.stream()
    .map(file -> {
        try {
            return Files.readString(file);
        } catch (IOException e) {
            throw new UncheckedIOException(e);
        }
    })
    .toList();
```

**Solution 2: Create custom functional interface**
```java
@FunctionalInterface
interface ThrowingFunction<T, R> {
    R apply(T t) throws Exception;
}

public static <T, R> Function<T, R> wrap(ThrowingFunction<T, R> func) {
    return t -> {
        try {
            return func.apply(t);
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    };
}

// Usage
List<String> lines = files.stream()
    .map(wrap(file -> Files.readString(file)))
    .toList();
```

**Real PerformanceTrack example:**
```java
public List<Report> generateReports(List<Review> reviews) {
    return reviews.stream()
        .map(review -> {
            try {
                return pdfGenerator.generate(review);
            } catch (PDFException e) {
                logger.error("Failed to generate report", e);
                return null;
            }
        })
        .filter(Objects::nonNull)
        .toList();
}
```

---

### Q9: How do you use lambda expressions with custom functional interfaces?
You can create your own functional interfaces for specific business logic.

**Custom interfaces in PerformanceTrack:**
```java
@FunctionalInterface
interface ScoreCalculator {
    double calculate(List<Review> reviews);
}

@FunctionalInterface
interface ReviewMatcher {
    boolean matches(Review review, String criteria);
}

@FunctionalInterface
interface ReportGenerator {
    String generate(Employee emp, List<Review> reviews);
}

// Usage
public class AnalyticsService {
    
    ScoreCalculator weightedAverage = reviews -> {
        double managerWeight = 0.5;
        double peerWeight = 0.3;
        double selfWeight = 0.2;
        
        double managerScore = reviews.stream()
            .filter(r -> r.getType().equals("MANAGER"))
            .mapToDouble(Review::getScore)
            .average()
            .orElse(0);
            
        // Similar for peer and self
        return managerScore * managerWeight;  // + others
    };
    
    ReviewMatcher highPerformer = (review, criteria) -> {
        return review.getScore() >= 4 && 
               review.getStatus().equals("COMPLETED");
    };
    
    ReportGenerator summaryGenerator = (emp, reviews) -> {
        return String.format(
            "Employee: %s, Reviews: %d, Avg: %.2f",
            emp.getName(),
            reviews.size(),
            reviews.stream().mapToDouble(Review::getScore).average().orElse(0)
        );
    };
}
```

---

### Q10: What is target typing in lambda expressions?
Target typing means the lambda's type is inferred from the context where it's used.

**Same lambda, different types:**
```java
// Type inferred as Comparator<Employee>
Comparator<Employee> comp1 = (e1, e2) -> e1.getName().compareTo(e2.getName());

// Type inferred as BiFunction<Employee, Employee, Integer>
BiFunction<Employee, Employee, Integer> comp2 = (e1, e2) -> e1.getName().compareTo(e2.getName());
```

**Context determines type:**
```java
// Runnable (no parameters, void return)
Runnable r = () -> System.out.println("Running");

// Callable (no parameters, returns value)
Callable<String> c = () -> "Result";

// Supplier (no parameters, returns value)
Supplier<String> s = () -> "Supplied";
```

**Real example in PerformanceTrack:**
```java
public class ReviewService {
    
    // Same lambda works for different functional interfaces
    public void processReviews() {
        // As Consumer
        Consumer<Review> printer = r -> System.out.println(r.getComments());
        reviews.forEach(printer);
        
        // As Predicate
        Predicate<Review> isComplete = r -> r.getStatus().equals("COMPLETED");
        reviews.stream().filter(isComplete).forEach(printer);
        
        // Context tells Java what type the lambda should be
    }
}
```

---

### Q11: Can you pass lambdas as method parameters?
Yes! This is one of the main benefits - passing behavior as parameters.

**Example in PerformanceTrack:**
```java
public class ReportService {
    
    // Method accepting lambda as parameter
    public List<Review> filterReviews(List<Review> reviews, 
                                      Predicate<Review> condition) {
        return reviews.stream()
            .filter(condition)
            .toList();
    }
    
    // Method accepting multiple lambdas
    public <T> List<T> transformReviews(List<Review> reviews,
                                        Predicate<Review> filter,
                                        Function<Review, T> transformer) {
        return reviews.stream()
            .filter(filter)
            .map(transformer)
            .toList();
    }
    
    // Usage
    public void generateReport() {
        // Pass lambdas directly
        List<Review> completed = filterReviews(
            allReviews,
            review -> review.getStatus().equals("COMPLETED")
        );
        
        List<String> comments = transformReviews(
            allReviews,
            review -> review.getScore() >= 4,
            review -> review.getComments()
        );
    }
}
```

**Strategy pattern with lambdas:**
```java
public class BonusCalculator {
    
    public double calculateBonus(Employee emp, 
                                Function<Employee, Double> strategy) {
        return strategy.apply(emp);
    }
    
    public void process() {
        // Different strategies as lambdas
        Function<Employee, Double> basicStrategy = 
            emp -> emp.getSalary() * 0.05;
            
        Function<Employee, Double> performanceStrategy = 
            emp -> emp.getSalary() * 0.10 * emp.getAverageScore();
            
        double bonus1 = calculateBonus(employee, basicStrategy);
        double bonus2 = calculateBonus(employee, performanceStrategy);
    }
}
```

---

### Q12: What happens if you use a local variable in a lambda and then modify it?
You'll get a compilation error. Variables used in lambdas must be effectively final.

**This fails:**
```java
int counter = 0;
employees.forEach(emp -> {
    counter++;  // ERROR: counter must be effectively final
});
```

**Workarounds:**

**Option 1: Use array (not recommended)**
```java
int[] counter = {0};
employees.forEach(emp -> {
    counter[0]++;  // Works but ugly
});
```

**Option 2: Use AtomicInteger (thread-safe)**
```java
AtomicInteger counter = new AtomicInteger(0);
employees.forEach(emp -> {
    counter.incrementAndGet();  // Works!
});
```

**Option 3: Use proper stream operations (best)**
```java
long count = employees.stream()
    .filter(emp -> emp.getStatus().equals("ACTIVE"))
    .count();
```

**Real example:**
```java
public class ReviewAnalytics {
    
    // WRONG way
    public Map<String, Integer> countByDepartmentWrong() {
        Map<String, Integer> counts = new HashMap<>();
        employees.forEach(emp -> {
            String dept = emp.getDepartment();
            counts.put(dept, counts.getOrDefault(dept, 0) + 1);  // Modifying map - risky!
        });
        return counts;
    }
    
    // RIGHT way - use collectors
    public Map<String, Long> countByDepartment() {
        return employees.stream()
            .collect(Collectors.groupingBy(
                Employee::getDepartment,
                Collectors.counting()
            ));
    }
}
```

---

## STREAM API

### Q13: What is the Stream API and why was it introduced?
Stream API is a way to process collections of data in a functional style. It was introduced to make data processing more declarative - you describe *what* you want, not *how* to do it.

**Example:** In your Analytics module, calculating average performance score:

```java
// Old way
double totalScore = 0;
int count = 0;
for (Review review : reviews) {
    if (review.getStatus().equals("COMPLETED")) {
        totalScore += review.getScore();
        count++;
    }
}
double average = count > 0 ? totalScore / count : 0;

// Stream way
double average = reviews.stream()
    .filter(review -> review.getStatus().equals("COMPLETED"))
    .mapToDouble(Review::getScore)
    .average()
    .orElse(0.0);
```

The stream version is easier to read and understand. Plus, streams can be parallelized easily for better performance.

---

### Q14: What's the difference between intermediate and terminal operations in streams?
Intermediate operations are lazy - they don't execute until a terminal operation is called. They return a new stream.

Terminal operations trigger the actual processing and return a result or produce a side effect.

**Intermediate operations:** `filter()`, `map()`, `sorted()`, `distinct()`, `limit()`, `skip()`

**Terminal operations:** `collect()`, `forEach()`, `count()`, `reduce()`, `findFirst()`, `anyMatch()`

**Example:** In feedback analysis:

```java
List<String> topComplaints = feedbacks.stream()
    .filter(f -> f.getRating() <= 2)        // Intermediate - nothing happens yet
    .map(Feedback::getComment)              // Intermediate - still lazy
    .distinct()                              // Intermediate - still waiting
    .limit(10)                               // Intermediate - pipeline building
    .collect(Collectors.toList());           // Terminal - NOW everything executes!
```

Until `collect()` is called, none of the filtering or mapping actually happens. This allows Java to optimize the operations.

---

### Q15: Explain map() vs flatMap() in streams
`map()` transforms each element into exactly one new element. One-to-one relationship.

`flatMap()` transforms each element into a stream of elements, then flattens all those streams into one. One-to-many relationship.

**Industry example:** In your PerformanceTrack system:

```java
// map() - Getting employee names (one employee -> one name)
List<String> names = employees.stream()
    .map(Employee::getName)
    .collect(Collectors.toList());

// flatMap() - Getting all goals from all employees (one employee -> many goals)
List<Goal> allGoals = employees.stream()
    .flatMap(employee -> employee.getGoals().stream())
    .collect(Collectors.toList());
```

Think of it this way: If you have a list of departments, and each department has multiple employees, `flatMap()` lets you get all employees from all departments as a single flat list.

---

### Q16: What is the difference between Collection and Stream?
Collection stores data, Stream processes data. Collections are eager, Streams are lazy.

**Key differences:**

Collections are data structures. Streams are not - they're like a pipeline for processing data.

You can iterate Collections multiple times. Streams can only be consumed once.

Collections actively store all elements in memory. Streams compute elements on demand.

**Example:** In your reporting module:

```java
List<Review> reviews = reviewRepository.findAll(); // Collection - all data loaded
Stream<Review> reviewStream = reviews.stream();     // Stream - ready to process

// This works
reviews.size();
reviews.get(0);
reviews.forEach(System.out::println);
reviews.forEach(System.out::println); // Can do again!

// This works
reviewStream.filter(r -> r.getScore() > 3).count();

// This FAILS - stream already consumed!
reviewStream.filter(r -> r.getScore() < 3).count(); // IllegalStateException
```

---

### Q17: How do you convert a Stream to a Collection?
Use the `collect()` terminal operation with `Collectors` utility class.

**Common conversions in a performance system:**

```java
// To List
List<Employee> highPerformers = employees.stream()
    .filter(e -> e.getAvgScore() > 4)
    .collect(Collectors.toList());

// To Set (removes duplicates)
Set<String> departments = employees.stream()
    .map(Employee::getDepartment)
    .collect(Collectors.toSet());

// To Map
Map<Long, Employee> employeeMap = employees.stream()
    .collect(Collectors.toMap(Employee::getId, e -> e));

// To specific collection type
LinkedList<Review> recentReviews = reviews.stream()
    .filter(r -> r.getCreatedDate().isAfter(LocalDate.now().minusMonths(3)))
    .collect(Collectors.toCollection(LinkedList::new));
```

**Industry use case:** In analytics, you might collect department-wise performance into a Map for quick lookups.

---

### Q18: What is the reduce() operation in streams?
`reduce()` combines all elements of a stream into a single result by repeatedly applying a combining operation.

Think of it as "reducing" many values down to one value.

**Syntax:** `reduce(identity, accumulator)` or `reduce(accumulator)`

**Example:** In performance analytics:

```java
// Calculate total score across all reviews
int totalScore = reviews.stream()
    .map(Review::getScore)
    .reduce(0, (sum, score) -> sum + score);
// Same as: reduce(0, Integer::sum);

// Find highest score
Optional<Integer> maxScore = reviews.stream()
    .map(Review::getScore)
    .reduce((a, b) -> a > b ? a : b);
// Same as: reduce(Integer::max);

// Concatenate all feedback comments
String allComments = feedbacks.stream()
    .map(Feedback::getComment)
    .reduce("", (combined, comment) -> combined + comment + "; ");
```

**Real scenario:** When generating a summary report, you might reduce multiple review scores into a single performance rating.

---

### Q19: Explain parallel streams and when to use them
Parallel streams split the data into chunks and process them on multiple CPU cores simultaneously. Just call `.parallelStream()` or `.parallel()` on a stream.

**When to use:**
- Large datasets (thousands of elements)
- CPU-intensive operations
- Independent operations (no shared state)

**When NOT to use:**
- Small datasets (overhead isn't worth it)
- Operations accessing shared mutable state
- I/O operations (database calls, file reading)

**Example:** In your Analytics module processing thousands of reviews:

```java
// Sequential - processes one by one
double avgSequential = reviews.stream()
    .mapToDouble(Review::getScore)
    .average()
    .orElse(0.0);

// Parallel - splits work across CPU cores
double avgParallel = reviews.parallelStream()
    .mapToDouble(Review::getScore)
    .average()
    .orElse(0.0);
```

**Warning scenario:** Don't use parallel streams for database operations in PerformanceTrack because multiple threads hitting the database can cause connection pool exhaustion and actually slow things down!

---

### Q20: What are method references and their types?
Method references are shorthand for lambda expressions that just call a method. They use the `::` operator.

**Four types:**

1. **Static method reference:** `ClassName::staticMethod`
2. **Instance method on particular object:** `object::instanceMethod`
3. **Instance method on arbitrary object:** `ClassName::instanceMethod`
4. **Constructor reference:** `ClassName::new`

**Examples in PerformanceTrack:**

```java
// 1. Static method reference
List<String> ids = reviews.stream()
    .map(Review::generateId)  // if generateId is static
    .collect(Collectors.toList());

// 2. Instance method on particular object
EmailService emailService = new EmailService();
employees.forEach(emailService::sendWelcomeEmail);

// 3. Instance method on arbitrary object of a type
List<String> names = employees.stream()
    .map(Employee::getName)  // calling getName() on each employee
    .collect(Collectors.toList());

// 4. Constructor reference
List<EmployeeDTO> dtos = employees.stream()
    .map(EmployeeDTO::new)  // calling new EmployeeDTO(employee)
    .collect(Collectors.toList());
```

**Industry benefit:** Makes code cleaner when you're just calling existing methods without any additional logic.

---

### Q21: What is the difference between findFirst() and findAny()?
Both return an Optional with an element from the stream, but:

**findFirst()** - Always returns the first element in encounter order
**findAny()** - Returns any element (useful in parallel streams for performance)

**Sequential stream:**
```java
Optional<Review> first = reviews.stream()
    .filter(r -> r.getScore() >= 4)
    .findFirst();  // Always the first match

Optional<Review> any = reviews.stream()
    .filter(r -> r.getScore() >= 4)
    .findAny();    // Also the first in sequential, but not guaranteed
```

**Parallel stream:**
```java
Optional<Review> first = reviews.parallelStream()
    .filter(r -> r.getScore() >= 4)
    .findFirst();  // Still returns first, but slower (must maintain order)

Optional<Review> any = reviews.parallelStream()
    .filter(r -> r.getScore() >= 4)
    .findAny();    // Faster! Returns whichever thread finds one first
```

**Real usage:**
```java
// When order matters - use findFirst()
Optional<Review> mostRecent = reviews.stream()
    .sorted(Comparator.comparing(Review::getSubmittedDate).reversed())
    .findFirst();

// When you just need any match - use findAny()
boolean hasHighScore = reviews.parallelStream()
    .anyMatch(r -> r.getScore() == 5);  // anyMatch is even better for this
```

---

### Q22: What's the difference between anyMatch(), allMatch(), and noneMatch()?
These are terminal operations that test elements against a predicate:

**anyMatch()** - Returns true if ANY element matches
**allMatch()** - Returns true if ALL elements match
**noneMatch()** - Returns true if NO elements match

**Examples in PerformanceTrack:**
```java
List<Review> reviews = getReviews();

// Check if there's at least one excellent review
boolean hasExcellent = reviews.stream()
    .anyMatch(r -> r.getScore() == 5);

// Check if all reviews are completed
boolean allCompleted = reviews.stream()
    .allMatch(r -> r.getStatus().equals("COMPLETED"));

// Check if there are no failing reviews
boolean noFailing = reviews.stream()
    .noneMatch(r -> r.getScore() < 3);
```

**Short-circuit behavior:**
```java
// anyMatch stops as soon as it finds a match
boolean found = Stream.of(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
    .peek(n -> System.out.println("Processing: " + n))
    .anyMatch(n -> n > 5);
// Output: Processing 1, 2, 3, 4, 5, 6 (stops at 6!)

// allMatch stops as soon as it finds a non-match
boolean allEven = Stream.of(2, 4, 6, 7, 8, 10)
    .peek(n -> System.out.println("Checking: " + n))
    .allMatch(n -> n % 2 == 0);
// Output: Checking 2, 4, 6, 7 (stops at 7!)
```

---

### Q23: How do you create streams from different sources?
Streams can be created from many sources:

**From Collections:**
```java
List<Review> reviews = getReviews();
Stream<Review> stream1 = reviews.stream();
Stream<Review> stream2 = reviews.parallelStream();
```

**From arrays:**
```java
String[] departments = {"Engineering", "Sales", "HR"};
Stream<String> stream3 = Arrays.stream(departments);
Stream<String> stream4 = Stream.of("Engineering", "Sales", "HR");
```

**From values:**
```java
Stream<Integer> scores = Stream.of(1, 2, 3, 4, 5);
Stream<String> empty = Stream.empty();
```

**From builder:**
```java
Stream<String> built = Stream.<String>builder()
    .add("Review 1")
    .add("Review 2")
    .add("Review 3")
    .build();
```

**Infinite streams:**
```java
// Generate infinite sequence
Stream<Integer> infinite = Stream.iterate(0, n -> n + 1);
Stream<Integer> first10 = infinite.limit(10);  // Take only 10

// Generate random values
Stream<Double> randoms = Stream.generate(Math::random).limit(5);
```

**From files (Java NIO):**
```java
try (Stream<String> lines = Files.lines(Paths.get("reviews.txt"))) {
    lines.forEach(System.out::println);
}
```

**Real PerformanceTrack usage:**
```java
public class ReportGenerator {
    
    // Generate review IDs
    public List<String> generateReviewIds(int count) {
        return Stream.iterate(1, n -> n + 1)
            .limit(count)
            .map(n -> "REV-2025-" + String.format("%05d", n))
            .toList();
    }
    
    // Generate sample scores for testing
    public List<Integer> generateTestScores(int count) {
        return Stream.generate(() -> new Random().nextInt(5) + 1)
            .limit(count)
            .toList();
    }
}
```

---

### Q24: What is the peek() operation and when should you use it?
`peek()` is an intermediate operation that lets you perform an action on each element without modifying the stream. It's mainly used for debugging.

**Debugging usage:**
```java
List<String> result = reviews.stream()
    .peek(r -> System.out.println("Original: " + r))
    .filter(r -> r.getScore() >= 4)
    .peek(r -> System.out.println("After filter: " + r))
    .map(Review::getComments)
    .peek(c -> System.out.println("Comments: " + c))
    .collect(Collectors.toList());
```

**Real PerformanceTrack debugging:**
```java
public List<EmployeeReport> generateReports() {
    return employees.stream()
        .peek(e -> logger.debug("Processing employee: " + e.getId()))
        .filter(e -> e.getStatus().equals("ACTIVE"))
        .peek(e -> logger.debug("Employee is active: " + e.getName()))
        .map(this::createReport)
        .peek(r -> logger.debug("Report created: " + r.getReportId()))
        .collect(Collectors.toList());
}
```

**Side effects (use carefully):**
```java
// Can use peek for side effects, but forEach is clearer
List<Integer> numbers = new ArrayList<>();
Stream.of(1, 2, 3, 4, 5)
    .peek(numbers::add)  // Adding to list - side effect
    .count();
```

**Warning:** Don't rely on peek() for important side effects because optimization might skip it. Use `forEach()` for guaranteed side effects.

---

### Q25: How does skip() and limit() work in streams?
**skip(n)** - Skips the first n elements
**limit(n)** - Keeps only the first n elements

**Examples:**
```java
// Get reviews 11-20 (skip first 10, take next 10)
List<Review> page2 = reviews.stream()
    .skip(10)
    .limit(10)
    .toList();

// Get top 5 performers
List<Employee> top5 = employees.stream()
    .sorted(Comparator.comparing(Employee::getAverageScore).reversed())
    .limit(5)
    .toList();

// Get all except first 3
List<Review> exceptFirst3 = reviews.stream()
    .skip(3)
    .toList();
```

**Pagination in PerformanceTrack:**
```java
public class ReviewService {
    
    public List<Review> getPage(int pageNumber, int pageSize) {
        return reviews.stream()
            .skip((long) pageNumber * pageSize)
            .limit(pageSize)
            .toList();
    }
    
    // Get reviews excluding top and bottom performers
    public List<Review> getMiddlePerformers() {
        List<Review> sorted = reviews.stream()
            .sorted(Comparator.comparing(Review::getScore))
            .toList();
            
        return sorted.stream()
            .skip(sorted.size() / 10)      // Skip bottom 10%
            .limit(sorted.size() * 8 / 10)  // Take middle 80%
            .toList();
    }
}
```

**Performance note:** With large streams, `skip()` still has to process skipped elements (can't jump directly to position).

---

### Q26: What is the difference between sorted() with and without Comparator?
**sorted()** (no parameter) - Uses natural ordering (elements must implement Comparable)
**sorted(Comparator)** - Uses custom ordering

**Natural ordering:**
```java
// Integers - natural order is ascending
List<Integer> scores = Stream.of(3, 1, 4, 1, 5, 9, 2, 6)
    .sorted()
    .toList();  // [1, 1, 2, 3, 4, 5, 6, 9]

// Strings - natural order is alphabetical
List<String> names = Stream.of("Charlie", "Alice", "Bob")
    .sorted()
    .toList();  // [Alice, Bob, Charlie]
```

**Custom ordering:**
```java
// Sort reviews by score (descending)
List<Review> byScore = reviews.stream()
    .sorted(Comparator.comparing(Review::getScore).reversed())
    .toList();

// Sort employees by name
List<Employee> byName = employees.stream()
    .sorted(Comparator.comparing(Employee::getName))
    .toList();

// Multiple criteria
List<Review> sorted = reviews.stream()
    .sorted(Comparator
        .comparing(Review::getScore).reversed()
        .thenComparing(Review::getSubmittedDate))
    .toList();
```

**Real PerformanceTrack scenarios:**
```java
public class ReviewSorter {
    
    // Sort by multiple criteria
    public List<Review> getSortedReviews() {
        return reviews.stream()
            .sorted(Comparator
                .comparing(Review::getStatus)           // Status first
                .thenComparing(Review::getScore).reversed()  // Then score desc
                .thenComparing(Review::getSubmittedDate))    // Then date
            .toList();
    }
    
    // Custom comparator
    public List<Employee> sortByPerformance() {
        return employees.stream()
            .sorted((e1, e2) -> {
                // Custom logic
                double diff = e2.getAverageScore() - e1.getAverageScore();
                if (diff > 0) return 1;
                if (diff < 0) return -1;
                return e1.getName().compareTo(e2.getName());
            })
            .toList();
    }
    
    // Null-safe sorting
    public List<Review> sortWithNulls() {
        return reviews.stream()
            .sorted(Comparator.comparing(
                Review::getApprovedDate,
                Comparator.nullsLast(Comparator.naturalOrder())
            ))
            .toList();
    }
}
```

---

### Q27: How do you use distinct() in streams?
`distinct()` removes duplicate elements based on `equals()` and `hashCode()`.

**Basic usage:**
```java
// Remove duplicate scores
List<Integer> uniqueScores = reviews.stream()
    .map(Review::getScore)
    .distinct()
    .toList();

// Get unique departments
Set<String> departments = employees.stream()
    .map(Employee::getDepartment)
    .distinct()
    .collect(Collectors.toSet());  // Or just use toSet() collector
```

**With custom objects:**
```java
// Removes duplicates based on Employee's equals() method
List<Employee> uniqueEmployees = employees.stream()
    .distinct()
    .toList();

// Get employees with unique names (need custom logic)
List<Employee> uniqueByName = employees.stream()
    .collect(Collectors.toMap(
        Employee::getName,
        e -> e,
        (e1, e2) -> e1  // Keep first if duplicate
    ))
    .values()
    .stream()
    .toList();
```

**Real PerformanceTrack usage:**
```java
public class AnalyticsService {
    
    // Get unique review statuses across all reviews
    public List<String> getUniqueStatuses() {
        return reviews.stream()
            .map(Review::getStatus)
            .distinct()
            .sorted()
            .toList();
    }
    
    // Get employees who have submitted at least one review
    public List<Employee> getReviewingEmployees() {
        return reviews.stream()
            .map(Review::getEmployee)
            .distinct()  // Remove duplicate employees
            .toList();
    }
    
    // Get unique year-month combinations from review dates
    public List<String> getReviewPeriods() {
        return reviews.stream()
            .map(r -> r.getSubmittedDate().getYear() + "-" + 
                     r.getSubmittedDate().getMonthValue())
            .distinct()
            .sorted()
            .toList();
    }
}
```

**Performance note:** `distinct()` maintains internal state to track seen elements, so it has memory overhead proportional to the number of unique elements.

---

## DEFAULT AND STATIC METHODS IN INTERFACES

### Q28: What are default methods in interfaces and why were they added?
Default methods allow you to add new methods to interfaces without breaking existing implementations. They have a body (implementation) in the interface itself.

Before Java 8, if you added a method to an interface, ALL classes implementing it would break. Default methods solve this.

**Syntax:**

```java
public interface PerformanceEvaluator {
    // Abstract method - must be implemented
    double calculateScore(Employee employee);
    
    // Default method - can be overridden but not mandatory
    default boolean isHighPerformer(Employee employee) {
        return calculateScore(employee) >= 4.0;
    }
}
```

**Real example in PerformanceTrack:**

```java
public interface Notifiable {
    void sendNotification(String message);
    
    // New method added later - doesn't break existing implementations
    default void sendBulkNotifications(List<String> messages) {
        messages.forEach(this::sendNotification);
    }
}

// Old implementation still works
public class EmailNotifier implements Notifiable {
    public void sendNotification(String message) {
        // send email
    }
    // No need to implement sendBulkNotifications - default is used
}

// New implementation can override
public class SMSNotifier implements Notifiable {
    public void sendNotification(String message) {
        // send SMS
    }
    
    @Override
    public void sendBulkNotifications(List<String> messages) {
        // Batch SMS sending for better performance
    }
}
```

**Why it matters:** When Java 8 added Stream API, they needed to add `stream()` method to Collection interface. Using default methods, they did this without breaking millions of existing classes!

---

### Q29: What are static methods in interfaces?
Static methods in interfaces are utility methods that belong to the interface itself, not to implementing classes.

**Key points:**
- Called using interface name, not implementing class
- Cannot be overridden
- Used for helper/utility methods

**Example in your Analytics module:**

```java
public interface ReportGenerator {
    Report generate(List<Review> reviews);
    
    // Static utility method
    static List<Review> filterByDateRange(List<Review> reviews, 
                                         LocalDate start, 
                                         LocalDate end) {
        return reviews.stream()
            .filter(r -> !r.getCreatedDate().isBefore(start))
            .filter(r -> !r.getCreatedDate().isAfter(end))
            .collect(Collectors.toList());
    }
    
    static double calculateAverageScore(List<Review> reviews) {
        return reviews.stream()
            .mapToDouble(Review::getScore)
            .average()
            .orElse(0.0);
    }
}

// Usage
List<Review> filtered = ReportGenerator.filterByDateRange(
    allReviews, 
    LocalDate.of(2025, 1, 1), 
    LocalDate.of(2025, 1, 31)
);
```

**Industry benefit:** Groups related utility methods with the interface instead of creating separate utility classes.

---

### Q30: What happens if a class implements two interfaces with the same default method?
You'll get a compilation error due to ambiguity. You must override the method and explicitly choose which implementation to use.

**Example scenario in PerformanceTrack:**

```java
interface ManagerEvaluator {
    default String getEvaluationType() {
        return "Manager Evaluation";
    }
}

interface PeerEvaluator {
    default String getEvaluationType() {
        return "Peer Evaluation";
    }
}

// This WON'T compile - diamond problem
class ComprehensiveEvaluator implements ManagerEvaluator, PeerEvaluator {
    // MUST override to resolve conflict
    @Override
    public String getEvaluationType() {
        // Option 1: Choose one
        return ManagerEvaluator.super.getEvaluationType();
        
        // Option 2: Provide custom implementation
        // return "Comprehensive Evaluation";
    }
}
```

**Real scenario:** If you have multiple notification interfaces with default methods, you need to explicitly handle conflicts.

**Rule:** Class implementations always win over interface defaults. If a class method and interface default have same signature, class method is used.

---

## OPTIONAL CLASS

### Q31: What is Optional and why should we use it?
`Optional` is a container that may or may not hold a value. It's a better way to handle potentially null values instead of using null checks everywhere.

Before Optional, you'd write code like:

```java
Employee employee = findEmployeeById(id);
if (employee != null) {
    String dept = employee.getDepartment();
    if (dept != null) {
        // use dept
    }
}
```

**With Optional in PerformanceTrack:**

```java
Optional<Employee> employeeOpt = employeeRepository.findById(id);
String dept = employeeOpt
    .map(Employee::getDepartment)
    .orElse("No Department");
```

**Key benefits:**
- Makes it explicit when a value might be absent
- Prevents NullPointerException
- Provides functional methods for handling absence

**Real scenario:** When fetching a review that might not exist:

```java
Optional<Review> review = reviewRepository.findById(reviewId);
return review.orElseThrow(() -> 
    new ResourceNotFoundException("Review not found with id: " + reviewId));
```

---

### Q32: What are common Optional methods?
Here are the most used Optional methods with real examples:

**Creation methods:**
```java
Optional<Employee> emp1 = Optional.of(employee);          // throws if null
Optional<Employee> emp2 = Optional.ofNullable(employee);  // safe, creates empty if null
Optional<Employee> emp3 = Optional.empty();               // explicitly empty
```

**Checking methods:**
```java
if (reviewOpt.isPresent()) {
    Review r = reviewOpt.get();
}

// Better way (Java 11+)
reviewOpt.ifPresent(review -> System.out.println(review));
```

**Retrieval methods:**
```java
Review review = reviewOpt.orElse(defaultReview);
Review review = reviewOpt.orElseGet(() -> createDefaultReview());
Review review = reviewOpt.orElseThrow(() -> new ReviewNotFoundException());
```

**Transformation methods:**
```java
// map - transform the value if present
Optional<String> comment = reviewOpt.map(Review::getComments);

// filter - keep only if condition is true
Optional<Review> highScore = reviewOpt.filter(r -> r.getScore() >= 4);

// flatMap - when the mapper returns Optional
Optional<String> managerName = employeeOpt
    .flatMap(Employee::getManager)
    .map(Employee::getName);
```

**Industry example in your Analytics module:**

```java
public Double calculateDepartmentAverage(String department) {
    return reviews.stream()
        .filter(r -> r.getEmployee().getDepartment().equals(department))
        .map(Review::getScore)
        .mapToDouble(Integer::doubleValue)
        .average()  // returns OptionalDouble
        .orElse(0.0);
}
```

---

### Q33: What's the difference between orElse() and orElseGet()?
Both provide default values when Optional is empty, but they work differently.

`orElse()` - Always evaluates the parameter, even if Optional has a value.

`orElseGet()` - Only evaluates when Optional is empty (lazy evaluation).

**Example showing the difference:**

```java
public Employee createDefaultEmployee() {
    System.out.println("Creating default employee...");
    return new Employee("Unknown", "No Department");
}

Optional<Employee> employeeOpt = Optional.of(existingEmployee);

// This ALWAYS calls createDefaultEmployee() even though we have a value!
Employee emp1 = employeeOpt.orElse(createDefaultEmployee());

// This NEVER calls createDefaultEmployee() because we have a value
Employee emp2 = employeeOpt.orElseGet(() -> createDefaultEmployee());
```

**Industry best practice:** In PerformanceTrack, if creating a default value is expensive (like querying database or complex calculation), use `orElseGet()`:

```java
Review review = reviewRepository.findById(id)
    .orElseGet(() -> {
        // This expensive operation only runs if review not found
        Review defaultReview = new Review();
        defaultReview.setScore(0);
        defaultReview.setStatus("PENDING");
        return reviewRepository.save(defaultReview);
    });
```

---

### Q34: What's the difference between map() and flatMap() in Optional?
`map()` - Transform value, wraps result in Optional
`flatMap()` - Transform value that already returns Optional, doesn't double-wrap

**map() example:**
```java
Optional<Employee> empOpt = Optional.of(employee);

// map wraps the result in Optional
Optional<String> nameOpt = empOpt.map(Employee::getName);
// Result: Optional<String>
```

**flatMap() example:**
```java
// Employee.getManager() returns Optional<Manager>
Optional<Manager> managerOpt = empOpt.flatMap(Employee::getManager);
// Result: Optional<Manager> (not Optional<Optional<Manager>>)
```

**Real PerformanceTrack chain:**
```java
public Optional<String> getManagerEmail(Long employeeId) {
    return employeeRepository.findById(employeeId)      // Optional<Employee>
        .flatMap(Employee::getManager)                   // Optional<Manager>
        .map(Manager::getEmail);                         // Optional<String>
}

// Without flatMap, you'd get nested Optionals:
Optional<Optional<Manager>> nested = empOpt.map(Employee::getManager); // Wrong!
```

---

### Q35: How do you chain multiple Optional operations?
You can chain map, flatMap, filter operations to handle complex null scenarios.

**Complex chaining:**
```java
public Optional<Double> getEmployeeBonusAmount(Long employeeId) {
    return employeeRepository.findById(employeeId)           // Optional<Employee>
        .filter(emp -> emp.getStatus().equals("ACTIVE"))     // Filter inactive
        .flatMap(emp -> reviewRepository.findLatestReview(emp.getId())) // Optional<Review>
        .filter(review -> review.getScore() >= 4)            // Filter low scores
        .map(Review::getScore)                               // Optional<Integer>
        .map(score -> score * 1000.0);                       // Optional<Double>
}
```

**Real scenario - getting nested data:**
```java
public class EmployeeService {
    
    // Get employee's manager's department
    public Optional<String> getManagerDepartment(Long employeeId) {
        return findById(employeeId)
            .flatMap(Employee::getManager)
            .map(Employee::getDepartment);
    }
    
    // Get review comment if score is high
    public Optional<String> getHighScoreComment(Long reviewId) {
        return reviewRepository.findById(reviewId)
            .filter(review -> review.getScore() >= 4)
            .map(Review::getComments)
            .filter(comment -> !comment.isEmpty());
    }
    
    // Complex: Get peer review average if employee has manager
    public Optional<Double> getPeerReviewAverage(Long employeeId) {
        return findById(employeeId)
            .filter(emp -> emp.getManager().isPresent())
            .map(emp -> reviewRepository.findPeerReviews(emp.getId()))
            .filter(reviews -> !reviews.isEmpty())
            .map(reviews -> reviews.stream()
                .mapToDouble(Review::getScore)
                .average()
                .orElse(0.0));
    }
}
```

---

### Q36: When should you use Optional.of() vs Optional.ofNullable()?
**Optional.of()** - Use when you're SURE the value is not null. Throws NPE if null.
**Optional.ofNullable()** - Use when value might be null. Safe option.

**Examples:**
```java
// of() - value guaranteed non-null
Employee emp = new Employee("John", "Engineering");
Optional<Employee> opt1 = Optional.of(emp);  // Safe

// of() with null - CRASHES!
Employee nullEmp = null;
Optional<Employee> opt2 = Optional.of(nullEmp);  // NullPointerException!

// ofNullable() - handles null safely
Optional<Employee> opt3 = Optional.ofNullable(nullEmp);  // Creates empty Optional
```

**Real usage in PerformanceTrack:**
```java
public class ReviewService {
    
    // Use of() when you create the object yourself
    public Optional<Review> createNewReview(Employee emp) {
        Review review = new Review();  // Never null
        review.setEmployee(emp);
        review.setStatus("DRAFT");
        return Optional.of(review);  // Safe to use of()
    }
    
    // Use ofNullable() when getting from external source
    public Optional<Review> findReview(Long id) {
        Review review = database.query(id);  // Might be null
        return Optional.ofNullable(review);  // Safe
    }
    
    // Real-world: Converting from possibly-null DTO
    public Optional<Employee> fromDTO(EmployeeDTO dto) {
        return Optional.ofNullable(dto)  // DTO might be null
            .map(d -> {
                Employee emp = new Employee();
                emp.setName(d.getName());
                return emp;
            });
    }
}
```

---

### Q37: How do you handle Optional with exceptions?
Use `orElseThrow()` to throw custom exceptions when Optional is empty.

**Different ways:**
```java
// No argument - throws NoSuchElementException
Review review1 = reviewOpt.orElseThrow();

// With supplier - custom exception
Review review2 = reviewOpt.orElseThrow(() -> 
    new ReviewNotFoundException("Review not found"));

// With message
Review review3 = reviewOpt.orElseThrow(() -> 
    new IllegalArgumentException("Invalid review ID: " + id));
```

**Real PerformanceTrack REST API:**
```java
@RestController
public class ReviewController {
    
    @GetMapping("/reviews/{id}")
    public ReviewDTO getReview(@PathVariable Long id) {
        return reviewRepository.findById(id)
            .map(this::convertToDTO)
            .orElseThrow(() -> new ResponseStatusException(
                HttpStatus.NOT_FOUND, 
                "Review not found with id: " + id
            ));
    }
    
    @GetMapping("/employees/{id}/manager")
    public EmployeeDTO getManager(@PathVariable Long id) {
        return employeeRepository.findById(id)
            .flatMap(Employee::getManager)
            .map(this::convertToDTO)
            .orElseThrow(() -> new BusinessException(
                "Employee " + id + " has no assigned manager"
            ));
    }
    
    // Custom exception with details
    @GetMapping("/reviews/{id}/approval")
    public ApprovalDTO getApproval(@PathVariable Long id) {
        return reviewRepository.findById(id)
            .filter(r -> r.getStatus().equals("COMPLETED"))
            .flatMap(Review::getApproval)
            .orElseThrow(() -> {
                Review r = reviewRepository.findById(id).orElse(null);
                if (r == null) {
                    return new NotFoundException("Review not found");
                } else {
                    return new ValidationException(
                        "Review is in " + r.getStatus() + " status, not approved yet"
                    );
                }
            });
    }
}
```

---

### Q38: Can you convert Optional to Stream?
Yes! Java 9+ allows converting Optional to Stream.

**stream() method:**
```java
Optional<Review> reviewOpt = reviewRepository.findById(id);

// Convert to Stream
Stream<Review> stream = reviewOpt.stream();
```

**Why is this useful?**
```java
// Combine multiple Optionals into a single list
List<Long> reviewIds = List.of(1L, 2L, 3L, 4L, 5L);

List<Review> reviews = reviewIds.stream()
    .map(id -> reviewRepository.findById(id))  // Stream<Optional<Review>>
    .flatMap(Optional::stream)                 // Filters out empty, unwraps present
    .collect(Collectors.toList());
```

**Real PerformanceTrack scenarios:**
```java
public class AnalyticsService {
    
    // Get all reviews for multiple employees (some might not exist)
    public List<Review> getReviewsForEmployees(List<Long> employeeIds) {
        return employeeIds.stream()
            .map(id -> employeeRepository.findById(id))
            .flatMap(Optional::stream)  // Skip missing employees
            .flatMap(emp -> emp.getReviews().stream())
            .collect(Collectors.toList());
    }
    
    // Get manager emails (only for employees who have managers)
    public List<String> getManagerEmails(List<Employee> employees) {
        return employees.stream()
            .map(Employee::getManager)      // Stream<Optional<Manager>>
            .flatMap(Optional::stream)       // Stream<Manager>
            .map(Manager::getEmail)
            .distinct()
            .collect(Collectors.toList());
    }
    
    // Find first completed review from list of IDs
    public Optional<Review> findFirstCompleted(List<Long> reviewIds) {
        return reviewIds.stream()
            .map(id -> reviewRepository.findById(id))
            .flatMap(Optional::stream)
            .filter(r -> r.getStatus().equals("COMPLETED"))
            .findFirst();
    }
}
```

---

### Q39: What's wrong with using Optional.get() directly?
`get()` throws `NoSuchElementException` if Optional is empty, defeating the purpose of Optional!

**Bad practice:**
```java
Optional<Review> reviewOpt = reviewRepository.findById(id);
Review review = reviewOpt.get();  // Might throw exception!
```

**Better alternatives:**
```java
// Option 1: orElse
Review review = reviewOpt.orElse(defaultReview);

// Option 2: orElseGet
Review review = reviewOpt.orElseGet(this::createDefaultReview);

// Option 3: orElseThrow with meaningful exception
Review review = reviewOpt.orElseThrow(() -> 
    new ReviewNotFoundException("Review " + id + " not found"));

// Option 4: ifPresent
reviewOpt.ifPresent(review -> {
    processReview(review);
});

// Option 5: ifPresentOrElse (Java 11+)
reviewOpt.ifPresentOrElse(
    review -> processReview(review),
    () -> handleMissingReview()
);
```

**When get() is acceptable:**
```java
// Only after checking isPresent()
if (reviewOpt.isPresent()) {
    Review review = reviewOpt.get();  // Safe here
    process(review);
}

// But even this is better written as:
reviewOpt.ifPresent(review -> process(review));
```

---

### Q40: How do you use Optional in method return types?
Optional is designed for return types, not parameters or fields.

**Good use - method returns:**
```java
public class EmployeeRepository {
    
    // Good - might not find employee
    public Optional<Employee> findById(Long id) {
        Employee emp = queryDatabase(id);
        return Optional.ofNullable(emp);
    }
    
    // Good - manager is optional
    public Optional<Employee> findManager(Long employeeId) {
        return findById(employeeId)
            .flatMap(Employee::getManager);
    }
}
```

**Bad uses - don't do these:**
```java
// BAD - Optional as parameter
public void updateEmployee(Optional<Employee> emp) {  // Wrong!
    emp.ifPresent(e -> save(e));
}
// Better: public void updateEmployee(Employee emp)

// BAD - Optional as field
public class Review {
    private Optional<String> comments;  // Wrong!
}
// Better: private String comments; (can be null)

// BAD - Optional in collections
List<Optional<Employee>> employees;  // Wrong!
// Better: List<Employee> (nulls are OK in lists if needed)
```

**Real PerformanceTrack patterns:**
```java
public class ReviewService {
    
    // Good - returning optional for queries
    public Optional<Review> findLatestReview(Long employeeId) {
        List<Review> reviews = reviewRepository.findByEmployeeId(employeeId);
        return reviews.stream()
            .max(Comparator.comparing(Review::getSubmittedDate));
    }
    
    // Good - optional result of calculation
    public Optional<Double> calculateAverage(Long employeeId) {
        return findReviews(employeeId)
            .stream()
            .mapToDouble(Review::getScore)
            .average()
            .stream()
            .boxed()
            .findFirst();
    }
    
    // Good - conditional business logic
    public Optional<Bonus> calculateBonus(Employee emp) {
        if (emp.getYearsOfService() < 1) {
            return Optional.empty();
        }
        
        return Optional.of(new Bonus(
            emp.getSalary() * 0.1
        ));
    }
}
```

---

### Q41: How do you combine multiple Optionals?
You can combine Optionals using flatMap, or helper methods in Java 11+.

**Combining two Optionals:**
```java
public Optional<Report> generateReport(Long employeeId, Long reviewId) {
    Optional<Employee> empOpt = employeeRepository.findById(employeeId);
    Optional<Review> revOpt = reviewRepository.findById(reviewId);
    
    // Both must be present
    return empOpt.flatMap(emp -> 
        revOpt.map(rev -> 
            createReport(emp, rev)
        )
    );
}
```

**Better with helper method:**
```java
public static <T, U, R> Optional<R> combine(
        Optional<T> opt1, 
        Optional<U> opt2,
        BiFunction<T, U, R> combiner) {
    
    return opt1.flatMap(t -> 
        opt2.map(u -> 
            combiner.apply(t, u)
        )
    );
}

// Usage
Optional<Report> report = combine(
    employeeOpt, 
    reviewOpt,
    (emp, rev) -> createReport(emp, rev)
);
```

**Real scenarios:**
```java
public class BonusCalculator {
    
    // Both employee and review must exist
    public Optional<Double> calculateBonus(Long empId, Long reviewId) {
        return employeeRepository.findById(empId)
            .flatMap(emp -> reviewRepository.findById(reviewId)
                .map(review -> {
                    double base = emp.getSalary() * 0.05;
                    double performance = review.getScore() * 0.02;
                    return base * performance;
                })
            );
    }
    
    // Either manager or peer review can determine bonus
    public Optional<Double> calculateFromEitherReview(Long empId) {
        Optional<Review> managerReview = getManagerReview(empId);
        Optional<Review> peerReview = getPeerReview(empId);
        
        // Use manager review if present, otherwise peer review
        return managerReview
            .or(() -> peerReview)  // Java 9+
            .map(review -> review.getScore() * 1000.0);
    }
    
    // All three must be present
    public Optional<String> createComprehensiveReport(Long empId) {
        Optional<Employee> emp = employeeRepository.findById(empId);
        Optional<Review> review = reviewRepository.findLatest(empId);
        Optional<List<Goal>> goals = goalRepository.findByEmployee(empId);
        
        if (emp.isPresent() && review.isPresent() && goals.isPresent()) {
            return Optional.of(buildReport(
                emp.get(), 
                review.get(), 
                goals.get()
            ));
        }
        return Optional.empty();
    }
}
```

---

### Q42: What is Optional.or() method introduced in Java 9?
`or()` provides an alternative Optional if the current one is empty.

**Syntax:**
```java
Optional<T> or(Supplier<Optional<T>> supplier)
```

**Example:**
```java
Optional<Review> review = reviewRepository.findById(id)
    .or(() -> reviewRepository.findDraft(id))
    .or(() -> Optional.of(createDefaultReview()));
```

**Real PerformanceTrack usage:**
```java
public class ReviewService {
    
    // Try multiple sources
    public Optional<Review> findReviewAnywhere(Long empId) {
        return reviewRepository.findCompleted(empId)
            .or(() -> reviewRepository.findDraft(empId))
            .or(() -> reviewRepository.findArchived(empId));
    }
    
    // Fallback chain for employee lookup
    public Optional<Employee> findEmployeeWithFallback(String email) {
        return employeeRepository.findByEmail(email)
            .or(() -> employeeRepository.findByAlternateEmail(email))
            .or(() -> employeeRepository.searchByName(extractName(email)));
    }
    
    // Configuration with defaults
    public String getConfigValue(String key) {
        return configRepository.findByKey(key)
            .map(Config::getValue)
            .or(() -> defaultConfigs.get(key))
            .orElse("DEFAULT_VALUE");
    }
}
```

---

### Q43: How do you use Optional with Java 8 Streams?
Optional works great with Streams for filtering and transforming collections.

**Filter with Optional:**
```java
// Get employees with managers
List<Employee> withManagers = employees.stream()
    .filter(emp -> emp.getManager().isPresent())
    .collect(Collectors.toList());

// Get manager names
List<String> managerNames = employees.stream()
    .map(Employee::getManager)
    .filter(Optional::isPresent)
    .map(Optional::get)
    .map(Employee::getName)
    .collect(Collectors.toList());

// Java 9+ - cleaner with flatMap
List<String> managerNames = employees.stream()
    .map(Employee::getManager)
    .flatMap(Optional::stream)
    .map(Employee::getName)
    .collect(Collectors.toList());
```

**Real PerformanceTrack examples:**
```java
public class AnalyticsService {
    
    // Get average score only from completed reviews
    public OptionalDouble getAverageScore(Long employeeId) {
        return employeeRepository.findById(employeeId)
            .stream()
            .flatMap(emp -> emp.getReviews().stream())
            .filter(r -> r.getStatus().equals("COMPLETED"))
            .mapToDouble(Review::getScore)
            .average();
    }
    
    // Get all valid email addresses
    public List<String> getAllEmailAddresses() {
        return employees.stream()
            .map(Employee::getEmail)  // might return Optional<String>
            .flatMap(Optional::stream)
            .filter(email -> email.contains("@"))
            .collect(Collectors.toList());
    }
    
    // Find first high-scoring review across all employees
    public Optional<Review> findFirstHighScore(List<Long> employeeIds) {
        return employeeIds.stream()
            .map(id -> reviewRepository.findLatest(id))
            .flatMap(Optional::stream)
            .filter(r -> r.getScore() >= 4)
            .findFirst();
    }
    
    // Collect all approved reviews
    public List<Review> getAllApprovedReviews() {
        return reviews.stream()
            .map(Review::getApproval)  // Optional<Approval>
            .filter(Optional::isPresent)
            .map(Optional::get)
            .map(Approval::getReview)
            .collect(Collectors.toList());
    }
}
```

---

## COLLECTORS

### Q44: What is the Collectors class and its common methods?
`Collectors` is a utility class that provides implementations for common reduction operations on streams.

**Most common collectors in PerformanceTrack:**

**1. toList() - Collect to List**
```java
List<Employee> managers = employees.stream()
    .filter(e -> e.getRole().equals("MANAGER"))
    .collect(Collectors.toList());
```

**2. toSet() - Collect to Set (removes duplicates)**
```java
Set<String> uniqueDepartments = employees.stream()
    .map(Employee::getDepartment)
    .collect(Collectors.toSet());
```

**3. toMap() - Collect to Map**
```java
Map<Long, Employee> employeeMap = employees.stream()
    .collect(Collectors.toMap(
        Employee::getId,        // key
        employee -> employee    // value
    ));
```

**4. joining() - Join strings**
```java
String departmentList = employees.stream()
    .map(Employee::getDepartment)
    .distinct()
    .collect(Collectors.joining(", "));
// Output: "Engineering, HR, Sales, Marketing"
```

**5. counting() - Count elements**
```java
long managerCount = employees.stream()
    .filter(e -> e.getRole().equals("MANAGER"))
    .collect(Collectors.counting());
```

**6. summingInt/summingDouble() - Sum values**
```java
int totalScore = reviews.stream()
    .collect(Collectors.summingInt(Review::getScore));
```

**7. averagingDouble() - Calculate average**
```java
double avgScore = reviews.stream()
    .collect(Collectors.averagingDouble(Review::getScore));
```

---

### Q45: Explain groupingBy() collector with examples
`groupingBy()` groups elements by a classifier function. It's like SQL's GROUP BY.

**Basic grouping:**

```java
// Group employees by department
Map<String, List<Employee>> byDepartment = employees.stream()
    .collect(Collectors.groupingBy(Employee::getDepartment));

// Result:
// {
//   "Engineering": [emp1, emp2, emp3],
//   "HR": [emp4, emp5],
//   "Sales": [emp6]
// }
```

**Grouping with counting:**

```java
// Count employees per department
Map<String, Long> countByDepartment = employees.stream()
    .collect(Collectors.groupingBy(
        Employee::getDepartment,
        Collectors.counting()
    ));

// Result: {"Engineering": 3, "HR": 2, "Sales": 1}
```

**Real PerformanceTrack analytics scenarios:**

```java
// 1. Average score by department
Map<String, Double> avgScoreByDept = reviews.stream()
    .collect(Collectors.groupingBy(
        review -> review.getEmployee().getDepartment(),
        Collectors.averagingDouble(Review::getScore)
    ));

// 2. Group reviews by performance rating
Map<String, List<Review>> byRating = reviews.stream()
    .collect(Collectors.groupingBy(review -> {
        double score = review.getScore();
        if (score >= 4.5) return "Excellent";
        if (score >= 3.5) return "Good";
        if (score >= 2.5) return "Average";
        return "Needs Improvement";
    }));

// 3. Count reviews by status for each employee
Map<Long, Map<String, Long>> reviewsByEmployeeAndStatus = reviews.stream()
    .collect(Collectors.groupingBy(
        review -> review.getEmployee().getId(),
        Collectors.groupingBy(
            Review::getStatus,
            Collectors.counting()
        )
    ));
```

---

### Q46: What is partitioningBy() and how is it different from groupingBy()?
`partitioningBy()` is a special case of grouping that splits elements into exactly two groups based on a boolean condition. The result is always a Map with true/false keys.

**Difference:**
- `groupingBy()` - Can create any number of groups
- `partitioningBy()` - Always creates exactly 2 groups (true/false)

**Examples in PerformanceTrack:**

```java
// Partition employees into managers and non-managers
Map<Boolean, List<Employee>> partitioned = employees.stream()
    .collect(Collectors.partitioningBy(
        e -> e.getRole().equals("MANAGER")
    ));

// Result:
// {
//   true: [manager1, manager2],
//   false: [emp1, emp2, emp3, emp4]
// }

// Access specific partitions
List<Employee> managers = partitioned.get(true);
List<Employee> nonManagers = partitioned.get(false);
```

**Real analytics use cases:**

```java
// 1. Partition reviews into completed and pending
Map<Boolean, List<Review>> reviewStatus = reviews.stream()
    .collect(Collectors.partitioningBy(
        r -> r.getStatus().equals("COMPLETED")
    ));

// 2. Partition high performers and others
Map<Boolean, List<Employee>> performanceSplit = employees.stream()
    .collect(Collectors.partitioningBy(
        e -> e.getAverageScore() >= 4.0
    ));

// 3. Partition with counting
Map<Boolean, Long> overdueCounts = reviews.stream()
    .collect(Collectors.partitioningBy(
        review -> LocalDate.now().isAfter(review.getDeadline()),
        Collectors.counting()
    ));
// Result: {true: 5, false: 20} (5 overdue, 20 on-time)
```

**Performance tip:** `partitioningBy()` is more efficient than `groupingBy()` when you only need a boolean split because it optimizes for the two-group case.

---

### Q47: How do you handle duplicate keys in toMap() collector?
By default, `toMap()` throws `IllegalStateException` if duplicate keys are found. You need to provide a merge function.

**Problem - duplicate keys:**
```java
// This fails if two employees have same name!
Map<String, Employee> byName = employees.stream()
    .collect(Collectors.toMap(
        Employee::getName,  // key - might have duplicates
        e -> e             // value
    ));
// IllegalStateException: Duplicate key!
```

**Solution 1: Keep first occurrence**
```java
Map<String, Employee> byName = employees.stream()
    .collect(Collectors.toMap(
        Employee::getName,
        e -> e,
        (existing, replacement) -> existing  // Keep first
    ));
```

**Solution 2: Keep last occurrence**
```java
Map<String, Employee> byName = employees.stream()
    .collect(Collectors.toMap(
        Employee::getName,
        e -> e,
        (existing, replacement) -> replacement  // Keep last
    ));
```

**Solution 3: Combine values**
```java
// Combine review scores for employees with same department
Map<String, Integer> totalByDept = reviews.stream()
    .collect(Collectors.toMap(
        review -> review.getEmployee().getDepartment(),
        Review::getScore,
        (score1, score2) -> score1 + score2  // Add scores
    ));
```

**Real PerformanceTrack scenarios:**
```java
public class ReportService {
    
    // Keep highest score per employee
    public Map<Long, Integer> getHighestScores() {
        return reviews.stream()
            .collect(Collectors.toMap(
                review -> review.getEmployee().getId(),
                Review::getScore,
                Integer::max  // Keep higher score
            ));
    }
    
    // Concatenate all comments per employee
    public Map<Long, String> getAllComments() {
        return reviews.stream()
            .collect(Collectors.toMap(
                review -> review.getEmployee().getId(),
                Review::getComments,
                (c1, c2) -> c1 + "; " + c2  // Combine comments
            ));
    }
    
    // Keep most recent review per employee
    public Map<Long, Review> getLatestReviews() {
        return reviews.stream()
            .collect(Collectors.toMap(
                review -> review.getEmployee().getId(),
                review -> review,
                (r1, r2) -> r1.getSubmittedDate().isAfter(r2.getSubmittedDate()) ? r1 : r2
            ));
    }
}
```

---

### Q48: How do you specify map implementation with toMap()?
Use the 4-parameter version of `toMap()` to specify the Map implementation.

**Default (HashMap):**
```java
Map<Long, Employee> map1 = employees.stream()
    .collect(Collectors.toMap(Employee::getId, e -> e));
// Returns HashMap
```

**Specify TreeMap (sorted):**
```java
Map<Long, Employee> sortedMap = employees.stream()
    .collect(Collectors.toMap(
        Employee::getId,
        e -> e,
        (e1, e2) -> e1,
        TreeMap::new  // Specify implementation
    ));
```

**Specify LinkedHashMap (insertion order):**
```java
Map<Long, Employee> orderedMap = employees.stream()
    .collect(Collectors.toMap(
        Employee::getId,
        e -> e,
        (e1, e2) -> e1,
        LinkedHashMap::new
    ));
```

**Real usage:**
```java
public class AnalyticsService {
    
    // Department scores in alphabetical order
    public Map<String, Double> getDeptScoresSorted() {
        return reviews.stream()
            .collect(Collectors.groupingBy(
                review -> review.getEmployee().getDepartment(),
                TreeMap::new,  // Sorted by department name
                Collectors.averagingDouble(Review::getScore)
            ));
    }
    
    // Maintain insertion order for report
    public Map<String, Long> getStatusCountsInOrder() {
        return reviews.stream()
            .collect(Collectors.groupingBy(
                Review::getStatus,
                LinkedHashMap::new,  // Preserve order
                Collectors.counting()
            ));
    }
}
```

---

### Q49: What is Collectors.mapping() and when do you use it?
`mapping()` applies a transformation before collecting. It's often used as a downstream collector.

**Basic usage:**
```java
// Collect employee names grouped by department
Map<String, List<String>> namesByDept = employees.stream()
    .collect(Collectors.groupingBy(
        Employee::getDepartment,
        Collectors.mapping(
            Employee::getName,
            Collectors.toList()
        )
    ));
```

**Without mapping (more verbose):**
```java
Map<String, List<String>> namesByDept = employees.stream()
    .collect(Collectors.groupingBy(Employee::getDepartment))
    .entrySet().stream()
    .collect(Collectors.toMap(
        Map.Entry::getKey,
        entry -> entry.getValue().stream()
            .map(Employee::getName)
            .collect(Collectors.toList())
    ));
```

**Real PerformanceTrack examples:**
```java
public class AnalyticsService {
    
    // Get review scores grouped by department
    public Map<String, List<Integer>> getScoresByDept() {
        return reviews.stream()
            .collect(Collectors.groupingBy(
                review -> review.getEmployee().getDepartment(),
                Collectors.mapping(
                    Review::getScore,
                    Collectors.toList()
                )
            ));
    }
    
    // Get unique employee names per status
    public Map<String, Set<String>> getEmployeeNamesByStatus() {
        return reviews.stream()
            .collect(Collectors.groupingBy(
                Review::getStatus,
                Collectors.mapping(
                    review -> review.getEmployee().getName(),
                    Collectors.toSet()  // Remove duplicates
                )
            ));
    }
    
    // Get comma-separated employee names per department
    public Map<String, String> getEmployeeListByDept() {
        return employees.stream()
            .collect(Collectors.groupingBy(
                Employee::getDepartment,
                Collectors.mapping(
                    Employee::getName,
                    Collectors.joining(", ")
                )
            ));
    }
}
```

---

### Q50: What is Collectors.filtering() introduced in Java 9?
`filtering()` filters elements before collecting. It's a downstream collector that applies a predicate.

**Before Java 9:**
```java
// Filter after grouping
Map<String, List<Employee>> highPerformers = employees.stream()
    .collect(Collectors.groupingBy(Employee::getDepartment));

highPerformers.replaceAll((dept, empList) -> 
    empList.stream()
        .filter(e -> e.getAverageScore() >= 4.0)
        .collect(Collectors.toList())
);
```

**Java 9+ with filtering():**
```java
Map<String, List<Employee>> highPerformers = employees.stream()
    .collect(Collectors.groupingBy(
        Employee::getDepartment,
        Collectors.filtering(
            emp -> emp.getAverageScore() >= 4.0,
            Collectors.toList()
        )
    ));
```

**Real PerformanceTrack usage:**
```java
public class ReportService {
    
    // Count only completed reviews per department
    public Map<String, Long> getCompletedCountByDept() {
        return reviews.stream()
            .collect(Collectors.groupingBy(
                review -> review.getEmployee().getDepartment(),
                Collectors.filtering(
                    review -> review.getStatus().equals("COMPLETED"),
                    Collectors.counting()
                )
            ));
    }
    
    // Get high scores (4+) grouped by department
    public Map<String, List<Integer>> getHighScoresByDept() {
        return reviews.stream()
            .collect(Collectors.groupingBy(
                review -> review.getEmployee().getDepartment(),
                Collectors.filtering(
                    review -> review.getScore() >= 4,
                    Collectors.mapping(
                        Review::getScore,
                        Collectors.toList()
                    )
                )
            ));
    }
    
    // Average score of only approved reviews per employee
    public Map<Long, Double> getApprovedAvgByEmployee() {
        return reviews.stream()
            .collect(Collectors.groupingBy(
                review -> review.getEmployee().getId(),
                Collectors.filtering(
                    review -> review.getStatus().equals("APPROVED"),
                    Collectors.averagingDouble(Review::getScore)
                )
            ));
    }
}
```

---

### Q51: What is Collectors.flatMapping() introduced in Java 9?
`flatMapping()` is like `flatMap()` for collectors. It flattens nested collections during collection.

**Example - collecting all goals from employees grouped by department:**
```java
Map<String, List<Goal>> goalsByDept = employees.stream()
    .collect(Collectors.groupingBy(
        Employee::getDepartment,
        Collectors.flatMapping(
            emp -> emp.getGoals().stream(),
            Collectors.toList()
        )
    ));
```

**Without flatMapping:**
```java
Map<String, List<Goal>> goalsByDept = employees.stream()
    .collect(Collectors.groupingBy(Employee::getDepartment))
    .entrySet().stream()
    .collect(Collectors.toMap(
        Map.Entry::getKey,
        entry -> entry.getValue().stream()
            .flatMap(emp -> emp.getGoals().stream())
            .collect(Collectors.toList())
    ));
```

**Real PerformanceTrack scenarios:**
```java
public class AnalyticsService {
    
    // Get all review comments grouped by department
    public Map<String, List<String>> getCommentsByDept() {
        return employees.stream()
            .collect(Collectors.groupingBy(
                Employee::getDepartment,
                Collectors.flatMapping(
                    emp -> reviewRepository.findByEmployee(emp.getId())
                        .stream()
                        .map(Review::getComments),
                    Collectors.toList()
                )
            ));
    }
    
    // Get all feedback items grouped by review score
    public Map<Integer, List<Feedback>> getFeedbackByScore() {
        return reviews.stream()
            .collect(Collectors.groupingBy(
                Review::getScore,
                Collectors.flatMapping(
                    review -> review.getFeedback().stream(),
                    Collectors.toList()
                )
            ));
    }
    
    // Get unique skills from all employees per department
    public Map<String, Set<String>> getSkillsByDept() {
        return employees.stream()
            .collect(Collectors.groupingBy(
                Employee::getDepartment,
                Collectors.flatMapping(
                    emp -> emp.getSkills().stream(),
                    Collectors.toSet()
                )
            ));
    }
}
```

---

### Q52: What is Collectors.teeing() introduced in Java 12?
`teeing()` applies two collectors and combines their results. Very useful for calculating multiple statistics in one pass.

**Syntax:**
```java
Collectors.teeing(
    collector1,      // First collector
    collector2,      // Second collector
    merger           // BiFunction to combine results
)
```

**Basic example:**
```java
record Stats(double average, long count) {}

Stats stats = reviews.stream()
    .collect(Collectors.teeing(
        Collectors.averagingDouble(Review::getScore),  // First
        Collectors.counting(),                          // Second
        (avg, count) -> new Stats(avg, count)          // Combine
    ));
```

**Real PerformanceTrack analytics:**
```java
public class AnalyticsService {
    
    record DepartmentStats(double avgScore, long employeeCount) {}
    
    // Get average score and employee count in one pass
    public DepartmentStats getDeptStats(String department) {
        return employees.stream()
            .filter(e -> e.getDepartment().equals(department))
            .collect(Collectors.teeing(
                Collectors.averagingDouble(Employee::getAverageScore),
                Collectors.counting(),
                DepartmentStats::new
            ));
    }
    
    record ScoreRange(int min, int max) {}
    
    // Get min and max score together
    public ScoreRange getScoreRange() {
        return reviews.stream()
            .map(Review::getScore)
            .collect(Collectors.teeing(
                Collectors.minBy(Integer::compareTo),
                Collectors.maxBy(Integer::compareTo),
                (min, max) -> new ScoreRange(
                    min.orElse(0), 
                    max.orElse(0)
                )
            ));
    }
    
    record PerformanceReport(
        List<Employee> highPerformers,
        List<Employee> needsImprovement
    ) {}
    
    // Split employees into high/low performers in one pass
    public PerformanceReport getPerformanceReport() {
        return employees.stream()
            .collect(Collectors.teeing(
                Collectors.filtering(
                    e -> e.getAverageScore() >= 4.0,
                    Collectors.toList()
                ),
                Collectors.filtering(
                    e -> e.getAverageScore() < 3.0,
                    Collectors.toList()
                ),
                PerformanceReport::new
            ));
    }
}
```

---

### Q53: How do you use Collectors.joining() with prefix and suffix?
`joining()` can take delimiter, prefix, and suffix parameters.

**Different forms:**
```java
// Just delimiter
String names1 = employees.stream()
    .map(Employee::getName)
    .collect(Collectors.joining(", "));
// Output: "Alice, Bob, Charlie"

// With prefix and suffix
String names2 = employees.stream()
    .map(Employee::getName)
    .collect(Collectors.joining(", ", "[", "]"));
// Output: "[Alice, Bob, Charlie]"
```

**Real PerformanceTrack formatting:**
```java
public class ReportFormatter {
    
    // Format employee list for email
    public String formatEmployeeList(List<Employee> employees) {
        return employees.stream()
            .map(Employee::getName)
            .collect(Collectors.joining(", ", "Employees: ", "."));
        // Output: "Employees: Alice, Bob, Charlie."
    }
    
    // Format review IDs for API response
    public String formatReviewIds(List<Review> reviews) {
        return reviews.stream()
            .map(r -> r.getId().toString())
            .collect(Collectors.joining(",", "review_ids=[", "]"));
        // Output: "review_ids=[123,456,789]"
    }
    
    // Format SQL IN clause
    public String buildInClause(List<Long> ids) {
        return ids.stream()
            .map(String::valueOf)
            .collect(Collectors.joining(", ", "WHERE id IN (", ")"));
        // Output: "WHERE id IN (1, 2, 3)"
    }
    
    // Format JSON array
    public String formatAsJsonArray(List<String> departments) {
        return departments.stream()
            .map(dept -> "\"" + dept + "\"")
            .collect(Collectors.joining(", ", "[", "]"));
        // Output: ["Engineering", "Sales", "HR"]
    }
}
```

---

### Q54: What is Collectors.collectingAndThen()?
`collectingAndThen()` applies a finisher function after collecting.

**Syntax:**
```java
Collectors.collectingAndThen(
    downstream,     // Collector
    finisher        // Function to transform result
)
```

**Examples:**
```java
// Get immutable list
List<Review> immutableReviews = reviews.stream()
    .filter(r -> r.getScore() >= 4)
    .collect(Collectors.collectingAndThen(
        Collectors.toList(),
        Collections::unmodifiableList
    ));

// Count as string
String count = reviews.stream()
    .collect(Collectors.collectingAndThen(
        Collectors.counting(),
        c -> "Total: " + c
    ));
```

**Real PerformanceTrack usage:**
```java
public class ReportService {
    
    // Get top performer with details
    public Optional<EmployeeReport> getTopPerformer() {
        return employees.stream()
            .collect(Collectors.collectingAndThen(
                Collectors.maxBy(
                    Comparator.comparing(Employee::getAverageScore)
                ),
                opt -> opt.map(this::createDetailedReport)
            ));
    }
    
    // Get average as formatted string
    public String getFormattedAverage() {
        return reviews.stream()
            .collect(Collectors.collectingAndThen(
                Collectors.averagingDouble(Review::getScore),
                avg -> String.format("Average: %.2f/5.0", avg)
            ));
    }
    
    // Get sorted unique scores
    public List<Integer> getUniqueScoresSorted() {
        return reviews.stream()
            .map(Review::getScore)
            .collect(Collectors.collectingAndThen(
                Collectors.toSet(),
                set -> {
                    List<Integer> list = new ArrayList<>(set);
                    Collections.sort(list);
                    return list;
                }
            ));
    }
    
    // Get department stats and cache result
    public DepartmentStats getCachedStats(String department) {
        return reviews.stream()
            .filter(r -> r.getEmployee().getDepartment().equals(department))
            .collect(Collectors.collectingAndThen(
                Collectors.toList(),
                list -> {
                    DepartmentStats stats = calculateStats(list);
                    cache.put(department, stats);  // Side effect
                    return stats;
                }
            ));
    }
}
```

---

### Q55: How do you create custom collectors?
You can create custom collectors using `Collector.of()`.

**Collector has 4 components:**
1. **Supplier** - Creates accumulator
2. **Accumulator** - Adds element to accumulator
3. **Combiner** - Merges two accumulators (for parallel)
4. **Finisher** - Final transformation

**Simple custom collector:**
```java
// Collect to ImmutableList
public static <T> Collector<T, ?, ImmutableList<T>> toImmutableList() {
    return Collector.of(
        ImmutableList.Builder<T>::new,           // Supplier
        ImmutableList.Builder::add,               // Accumulator
        (b1, b2) -> b1.addAll(b2.build()),       // Combiner
        ImmutableList.Builder::build              // Finisher
    );
}

// Usage
ImmutableList<Employee> employees = stream
    .collect(toImmutableList());
```

**Real PerformanceTrack custom collectors:**
```java
public class CustomCollectors {
    
    // Collect to comma-separated string with custom format
    public static Collector<Review, ?, String> toReviewSummary() {
        return Collector.of(
            StringBuilder::new,
            (sb, review) -> {
                if (sb.length() > 0) sb.append(", ");
                sb.append(review.getId())
                  .append(":")
                  .append(review.getScore());
            },
            (sb1, sb2) -> {
                if (sb1.length() > 0 && sb2.length() > 0) {
                    sb1.append(", ");
                }
                return sb1.append(sb2);
            },
            StringBuilder::toString
        );
    }
    
    // Calculate statistics
    public static Collector<Review, ?, ReviewStats> toReviewStats() {
        class Accumulator {
            int count = 0;
            int sum = 0;
            int min = Integer.MAX_VALUE;
            int max = Integer.MIN_VALUE;
            
            void add(Review review) {
                int score = review.getScore();
                count++;
                sum += score;
                min = Math.min(min, score);
                max = Math.max(max, score);
            }
            
            Accumulator combine(Accumulator other) {
                count += other.count;
                sum += other.sum;
                min = Math.min(min, other.min);
                max = Math.max(max, other.max);
                return this;
            }
            
            ReviewStats finish() {
                double avg = count > 0 ? (double) sum / count : 0;
                return new ReviewStats(count, avg, min, max);
            }
        }
        
        return Collector.of(
            Accumulator::new,
            Accumulator::add,
            Accumulator::combine,
            Accumulator::finish
        );
    }
    
    // Usage
    public void example() {
        String summary = reviews.stream()
            .collect(toReviewSummary());
        // Output: "1:4, 2:5, 3:3"
        
        ReviewStats stats = reviews.stream()
            .collect(toReviewStats());
        // Stats: count=3, avg=4.0, min=3, max=5
    }
}

record ReviewStats(int count, double average, int min, int max) {}
```

---

## COMPLETABLEFUTURE (ASYNC PROGRAMMING)

### Q56: What is CompletableFuture and why do we need it?
`CompletableFuture` is Java's way of handling asynchronous programming. It lets you run tasks in the background without blocking the main thread.

Before CompletableFuture, async code with `Future` was difficult - you couldn't chain operations, combine multiple futures, or handle exceptions easily.

**Example problem:** In PerformanceTrack, generating a report might need to:
1. Fetch employee data from database (slow)
2. Fetch all their reviews (slow)
3. Calculate analytics (CPU intensive)
4. Generate PDF (slow)

Without async, user waits for everything sequentially. With CompletableFuture:

```java
// Old blocking way
Employee employee = employeeService.getEmployee(id);        // 200ms
List<Review> reviews = reviewService.getReviews(id);        // 300ms
Analytics analytics = analyticsService.calculate(reviews);  // 500ms
PDF pdf = pdfService.generate(analytics);                   // 400ms
// Total: 1400ms

// Async way
CompletableFuture<Employee> employeeFuture = 
    CompletableFuture.supplyAsync(() -> employeeService.getEmployee(id));

CompletableFuture<List<Review>> reviewsFuture = 
    CompletableFuture.supplyAsync(() -> reviewService.getReviews(id));

// Combine results when both are ready
CompletableFuture<Report> reportFuture = employeeFuture
    .thenCombine(reviewsFuture, (emp, reviews) -> {
        Analytics analytics = analyticsService.calculate(reviews);
        return pdfService.generate(emp, analytics);
    });

Report report = reportFuture.get();  // Total: ~500ms (parallel execution!)
```

**Key benefits:**
- Non-blocking operations
- Easy composition and chaining
- Better exception handling
- Improved performance through parallelism

---

### Q57: What are the main methods of CompletableFuture?
**Creation methods:**

```java
// Run task asynchronously (no return value)
CompletableFuture<Void> future1 = CompletableFuture.runAsync(() -> {
    System.out.println("Background task");
});

// Run task asynchronously (returns value)
CompletableFuture<String> future2 = CompletableFuture.supplyAsync(() -> {
    return "Result from background";
});

// Already completed future
CompletableFuture<String> future3 = CompletableFuture.completedFuture("Instant result");
```

**Transformation methods:**

```java
CompletableFuture<Employee> employeeFuture = CompletableFuture.supplyAsync(
    () -> employeeRepository.findById(id)
);

// thenApply - transform result
CompletableFuture<String> nameFuture = employeeFuture
    .thenApply(Employee::getName);

// thenAccept - consume result (no return)
employeeFuture.thenAccept(emp -> {
    System.out.println("Employee: " + emp.getName());
});

// thenRun - run action after completion (no access to result)
employeeFuture.thenRun(() -> {
    System.out.println("Employee fetched");
});
```

**Combining futures:**

```java
// thenCombine - combine two futures
CompletableFuture<Report> combined = employeeFuture.thenCombine(
    reviewsFuture,
    (emp, reviews) -> generateReport(emp, reviews)
);

// allOf - wait for all futures
CompletableFuture.allOf(future1, future2, future3)
    .thenRun(() -> System.out.println("All done"));

// anyOf - wait for first completed
CompletableFuture.anyOf(future1, future2, future3)
    .thenAccept(result -> System.out.println("First result: " + result));
```

**Exception handling:**

```java
CompletableFuture<Report> reportFuture = CompletableFuture
    .supplyAsync(() -> generateReport())
    .exceptionally(ex -> {
        System.err.println("Error: " + ex.getMessage());
        return getDefaultReport();
    })
    .handle((result, ex) -> {
        if (ex != null) {
            return handleError(ex);
        }
        return result;
    });
```

---

### Q58: What's the difference between thenApply, thenCompose, and thenCombine?
These three methods are often confused. Let me explain with PerformanceTrack examples:

**thenApply()** - Transform the result synchronously. Input is value, output is value.

```java
CompletableFuture<Employee> employeeFuture = getEmployeeAsync(id);

CompletableFuture<String> nameFuture = employeeFuture
    .thenApply(employee -> employee.getName());
// Simple transformation: Employee -> String
```

**thenCompose()** - Chain dependent async operations. Input is value, output is CompletableFuture.

```java
CompletableFuture<Employee> employeeFuture = getEmployeeAsync(id);

CompletableFuture<List<Review>> reviewsFuture = employeeFuture
    .thenCompose(employee -> getReviewsAsync(employee.getId()));
// One async operation depends on result of another

// WITHOUT thenCompose (WRONG - creates nested futures):
CompletableFuture<CompletableFuture<List<Review>>> nested = employeeFuture
    .thenApply(employee -> getReviewsAsync(employee.getId()));
```

**thenCombine()** - Combine two independent async operations.

```java
CompletableFuture<Employee> employeeFuture = getEmployeeAsync(id);
CompletableFuture<List<Goal>> goalsFuture = getGoalsAsync(id);

CompletableFuture<EmployeeReport> reportFuture = employeeFuture
    .thenCombine(goalsFuture, (employee, goals) -> {
        return new EmployeeReport(employee, goals);
    });
// Combines results from TWO independent futures
```

**Real scenario in your Analytics module:**

```java
public CompletableFuture<DepartmentReport> generateDepartmentReport(String dept) {
    // Get employees (independent)
    CompletableFuture<List<Employee>> employeesFuture = 
        CompletableFuture.supplyAsync(() -> employeeRepo.findByDepartment(dept));
    
    // Get reviews (independent)
    CompletableFuture<List<Review>> reviewsFuture = 
        CompletableFuture.supplyAsync(() -> reviewRepo.findByDepartment(dept));
    
    // Combine both results
    return employeesFuture.thenCombine(reviewsFuture, (employees, reviews) -> {
        // Then chain another async operation
        return calculateAnalytics(employees, reviews);
    }).thenCompose(analytics -> {
        // Generate PDF asynchronously based on analytics
        return generatePDFAsync(analytics);
    });
}
```

**Remember:**
- `thenApply` = simple transformation
- `thenCompose` = chaining (like flatMap)
- `thenCombine` = merge two independent results

---

### Q59: How do you handle exceptions in CompletableFuture?
CompletableFuture provides several methods for exception handling.

**exceptionally() - Recover from exception:**
```java
CompletableFuture<Employee> future = CompletableFuture
    .supplyAsync(() -> {
        if (Math.random() > 0.5) {
            throw new RuntimeException("Database error");
        }
        return findEmployee();
    })
    .exceptionally(ex -> {
        logger.error("Failed to fetch employee", ex);
        return getDefaultEmployee();  // Fallback
    });
```

**handle() - Handle both success and failure:**
```java
CompletableFuture<String> future = CompletableFuture
    .supplyAsync(() -> generateReport())
    .handle((result, ex) -> {
        if (ex != null) {
            return "Error: " + ex.getMessage();
        }
        return "Success: " + result;
    });
```

**whenComplete() - Perform action without changing result:**
```java
CompletableFuture<Review> future = CompletableFuture
    .supplyAsync(() -> processReview())
    .whenComplete((result, ex) -> {
        if (ex != null) {
            logger.error("Review processing failed", ex);
        } else {
            logger.info("Review processed: " + result.getId());
        }
        // Doesn't change the result
    });
```

**Real PerformanceTrack error handling:**
```java
public class ReviewService {
    
    public CompletableFuture<Review> submitReview(Review review) {
        return CompletableFuture
            .supplyAsync(() -> {
                // Validate
                if (review.getScore() < 1 || review.getScore() > 5) {
                    throw new ValidationException("Invalid score");
                }
                
                // Save to database
                return reviewRepository.save(review);
            })
            .exceptionally(ex -> {
                // Log and handle specific exceptions
                if (ex.getCause() instanceof ValidationException) {
                    logger.warn("Validation failed: " + ex.getMessage());
                } else if (ex.getCause() instanceof DataAccessException) {
                    logger.error("Database error", ex);
                }
                throw new CompletionException(ex);
            })
            .whenComplete((result, ex) -> {
                // Audit logging regardless of success/failure
                if (ex == null) {
                    auditLog.log("Review submitted: " + result.getId());
                } else {
                    auditLog.log("Review submission failed");
                }
            });
    }
    
    // Multiple fallback strategies
    public CompletableFuture<Employee> getEmployeeWithFallback(Long id) {
        return CompletableFuture
            .supplyAsync(() -> primaryDB.findEmployee(id))
            .exceptionally(ex -> {
                logger.warn("Primary DB failed, trying cache");
                return cache.get(id);
            })
            .thenCompose(emp -> {
                if (emp == null) {
                    return CompletableFuture.supplyAsync(() -> 
                        backupDB.findEmployee(id)
                    );
                }
                return CompletableFuture.completedFuture(emp);
            })
            .exceptionally(ex -> {
                logger.error("All sources failed", ex);
                return Employee.createDefault();
            });
    }
}
```

---

### Q60: What's the difference between thenApply and thenApplyAsync?
**thenApply()** - Runs in the same thread that completed the previous stage
**thenApplyAsync()** - Runs in a separate thread from thread pool

**Example:**
```java
CompletableFuture<String> future = CompletableFuture
    .supplyAsync(() -> {
        System.out.println("Supply: " + Thread.currentThread().getName());
        return "Hello";
    })
    .thenApply(s -> {
        System.out.println("Apply: " + Thread.currentThread().getName());
        return s + " World";
    })
    .thenApplyAsync(s -> {
        System.out.println("ApplyAsync: " + Thread.currentThread().getName());
        return s + "!";
    });

// Output might be:
// Supply: ForkJoinPool.commonPool-worker-1
// Apply: ForkJoinPool.commonPool-worker-1  (same thread!)
// ApplyAsync: ForkJoinPool.commonPool-worker-2  (different thread!)
```

**When to use which:**
```java
public class ReportService {
    
    // Use thenApply for quick transformations
    public CompletableFuture<String> getEmployeeName(Long id) {
        return CompletableFuture
            .supplyAsync(() -> employeeRepo.findById(id))
            .thenApply(Employee::getName);  // Quick, no need for new thread
    }
    
    // Use thenApplyAsync for heavy processing
    public CompletableFuture<Report> generateReport(Long id) {
        return CompletableFuture
            .supplyAsync(() -> reviewRepo.findAll(id))
            .thenApplyAsync(reviews -> {
                // Heavy calculation - deserves separate thread
                return complexAnalytics(reviews);
            });
    }
}
```

**Rule of thumb:** Use async variant if the operation is:
- CPU-intensive
- Might block (I/O operations)
- Takes significant time

---

### Q61: How do you use CompletableFuture.allOf() and anyOf()?
**allOf()** - Waits for ALL futures to complete
**anyOf()** - Returns when ANY future completes

**allOf() example:**
```java
CompletableFuture<Employee> emp1 = getEmployeeAsync(1L);
CompletableFuture<Employee> emp2 = getEmployeeAsync(2L);
CompletableFuture<Employee> emp3 = getEmployeeAsync(3L);

// Wait for all to complete
CompletableFuture<Void> allFuture = CompletableFuture.allOf(emp1, emp2, emp3);

allFuture.thenRun(() -> {
    System.out.println("All employees fetched");
    // Now get results
    Employee e1 = emp1.join();
    Employee e2 = emp2.join();
    Employee e3 = emp3.join();
});
```

**Collecting results from allOf:**
```java
public CompletableFuture<List<Employee>> getAllEmployees(List<Long> ids) {
    List<CompletableFuture<Employee>> futures = ids.stream()
        .map(id -> CompletableFuture.supplyAsync(() -> findEmployee(id)))
        .toList();
    
    return CompletableFuture.allOf(futures.toArray(new CompletableFuture[0]))
        .thenApply(v -> futures.stream()
            .map(CompletableFuture::join)
            .collect(Collectors.toList())
        );
}
```

**anyOf() example:**
```java
// Query multiple databases, return first response
CompletableFuture<Employee> fromPrimaryDB = 
    CompletableFuture.supplyAsync(() -> primaryDB.find(id));
    
CompletableFuture<Employee> fromBackupDB = 
    CompletableFuture.supplyAsync(() -> backupDB.find(id));
    
CompletableFuture<Employee> fromCache = 
    CompletableFuture.supplyAsync(() -> cache.get(id));

CompletableFuture<Object> fastest = CompletableFuture.anyOf(
    fromPrimaryDB, fromBackupDB, fromCache
);

fastest.thenAccept(result -> {
    Employee emp = (Employee) result;
    System.out.println("Got employee from fastest source: " + emp);
});
```

**Real PerformanceTrack scenarios:**
```java
public class AnalyticsService {
    
    // Generate multiple reports in parallel
    public CompletableFuture<DashboardData> generateDashboard() {
        CompletableFuture<DeptStats> deptStats = 
            CompletableFuture.supplyAsync(this::calculateDeptStats);
            
        CompletableFuture<ReviewStats> reviewStats = 
            CompletableFuture.supplyAsync(this::calculateReviewStats);
            
        CompletableFuture<EmployeeStats> empStats = 
            CompletableFuture.supplyAsync(this::calculateEmployeeStats);
        
        return CompletableFuture.allOf(deptStats, reviewStats, empStats)
            .thenApply(v -> new DashboardData(
                deptStats.join(),
                reviewStats.join(),
                empStats.join()
            ));
    }
    
    // Try multiple analytics engines, use fastest
    public CompletableFuture<AnalyticsResult> getAnalytics(Long id) {
        CompletableFuture<AnalyticsResult> engine1 = 
            CompletableFuture.supplyAsync(() -> analyticsEngine1.process(id));
            
        CompletableFuture<AnalyticsResult> engine2 = 
            CompletableFuture.supplyAsync(() -> analyticsEngine2.process(id));
        
        return CompletableFuture.anyOf(engine1, engine2)
            .thenApply(result -> (AnalyticsResult) result);
    }
}
```

---

### Q62: How do you add timeouts to CompletableFuture?
Java 9+ added timeout methods to CompletableFuture.

**orTimeout() - Fail with TimeoutException:**
```java
CompletableFuture<Employee> future = CompletableFuture
    .supplyAsync(() -> slowDatabaseCall())
    .orTimeout(5, TimeUnit.SECONDS);  // Fails if takes > 5 seconds

future.exceptionally(ex -> {
    if (ex instanceof TimeoutException) {
        System.out.println("Operation timed out!");
    }
    return getDefaultEmployee();
});
```

**completeOnTimeout() - Provide default value on timeout:**
```java
CompletableFuture<Employee> future = CompletableFuture
    .supplyAsync(() -> slowDatabaseCall())
    .completeOnTimeout(
        getDefaultEmployee(), 
        5, 
        TimeUnit.SECONDS
    );  // Returns default after 5 seconds
```

**Real PerformanceTrack usage:**
```java
public class ReviewService {
    
    // API call with timeout
    public CompletableFuture<ExternalData> fetchExternalData(Long id) {
        return CompletableFuture
            .supplyAsync(() -> externalAPI.fetch(id))
            .orTimeout(3, TimeUnit.SECONDS)
            .exceptionally(ex -> {
                if (ex instanceof TimeoutException) {
                    logger.warn("External API timeout for id: " + id);
                }
                return ExternalData.empty();
            });
    }
    
    // Report generation with fallback
    public CompletableFuture<Report> generateWithTimeout(Long empId) {
        return CompletableFuture
            .supplyAsync(() -> generateFullReport(empId))
            .completeOnTimeout(
                generateSummaryReport(empId),  // Fallback to summary
                10,
                TimeUnit.SECONDS
            );
    }
    
    // Multiple operations with individual timeouts
    public CompletableFuture<DashboardData> getDashboard() {
        CompletableFuture<Stats> stats = CompletableFuture
            .supplyAsync(this::calculateStats)
            .orTimeout(5, TimeUnit.SECONDS);
            
        CompletableFuture<Chart> chart = CompletableFuture
            .supplyAsync(this::generateChart)
            .completeOnTimeout(Chart.empty(), 3, TimeUnit.SECONDS);
        
        return stats.thenCombine(chart, DashboardData::new)
            .orTimeout(10, TimeUnit.SECONDS);  // Overall timeout too
    }
}
```

---

### Q63: What is the difference between supplyAsync and runAsync?
**runAsync()** - Executes Runnable (no return value)
**supplyAsync()** - Executes Supplier (returns value)

**runAsync example:**
```java
CompletableFuture<Void> future = CompletableFuture.runAsync(() -> {
    // No return value
    System.out.println("Processing...");
    updateDatabase();
});
```

**supplyAsync example:**
```java
CompletableFuture<Employee> future = CompletableFuture.supplyAsync(() -> {
    // Returns value
    return employeeRepository.findById(id);
});
```

**Real usage in PerformanceTrack:**
```java
public class NotificationService {
    
    // runAsync for fire-and-forget operations
    public void sendNotificationAsync(Employee emp, String message) {
        CompletableFuture.runAsync(() -> {
            emailService.send(emp.getEmail(), message);
            // Don't care about result
        });
    }
    
    // supplyAsync when you need the result
    public CompletableFuture<Boolean> sendAndVerify(Employee emp, String msg) {
        return CompletableFuture.supplyAsync(() -> {
            emailService.send(emp.getEmail(), msg);
            return emailService.verify(emp.getEmail());
        });
    }
    
    // Combining both
    public CompletableFuture<Report> generateAndNotify(Long empId) {
        return CompletableFuture
            .supplyAsync(() -> generateReport(empId))  // Need the report
            .thenApply(report -> {
                // Send notification (fire-and-forget)
                CompletableFuture.runAsync(() -> 
                    notifyEmployee(empId, report)
                );
                return report;  // Return report to caller
            });
    }
}
```

---

### Q64: How do you use custom Executor with CompletableFuture?
By default, CompletableFuture uses ForkJoinPool.commonPool(). You can provide custom executor.

**Custom thread pool:**
```java
ExecutorService executor = Executors.newFixedThreadPool(10);

CompletableFuture<Employee> future = CompletableFuture
    .supplyAsync(() -> findEmployee(id), executor);  // Use custom executor
```

**Why use custom executor:**
1. Control thread pool size
2. Isolate different types of work
3. Custom thread naming for debugging
4. Different priorities for tasks

**Real PerformanceTrack example:**
```java
public class ReportService {
    
    // Separate executors for different operations
    private final ExecutorService dbExecutor = 
        Executors.newFixedThreadPool(5);  // Database operations
        
    private final ExecutorService cpuExecutor = 
        Executors.newFixedThreadPool(
            Runtime.getRuntime().availableProcessors()
        );  // CPU-intensive tasks
        
    private final ExecutorService ioExecutor = 
        Executors.newFixedThreadPool(20);  // I/O operations
    
    public CompletableFuture<Report> generateReport(Long empId) {
        // Fetch from DB
        CompletableFuture<Employee> empFuture = CompletableFuture
            .supplyAsync(() -> employeeRepo.findById(empId), dbExecutor);
        
        CompletableFuture<List<Review>> reviewsFuture = CompletableFuture
            .supplyAsync(() -> reviewRepo.findByEmployee(empId), dbExecutor);
        
        // Process data (CPU-intensive)
        return empFuture.thenCombineAsync(
            reviewsFuture,
            (emp, reviews) -> calculateAnalytics(emp, reviews),
            cpuExecutor
        )
        // Generate PDF (I/O)
        .thenApplyAsync(
            analytics -> pdfGenerator.generate(analytics),
            ioExecutor
        );
    }
    
    // Named threads for debugging
    public void initExecutors() {
        ThreadFactory namedFactory = new ThreadFactory() {
            private AtomicInteger counter = new AtomicInteger(0);
            
            public Thread newThread(Runnable r) {
                Thread t = new Thread(r);
                t.setName("Report-Worker-" + counter.getAndIncrement());
                return t;
            }
        };
        
        ExecutorService executor = Executors.newFixedThreadPool(
            10, 
            namedFactory
        );
    }
    
    // Cleanup
    @PreDestroy
    public void cleanup() {
        dbExecutor.shutdown();
        cpuExecutor.shutdown();
        ioExecutor.shutdown();
    }
}
```

---

### Q65: How do you chain multiple CompletableFutures sequentially?
Use `thenCompose()` for sequential chaining where each step depends on the previous.

**Sequential operations:**
```java
CompletableFuture<Report> reportFuture = CompletableFuture
    .supplyAsync(() -> findEmployee(empId))
    .thenCompose(emp -> 
        CompletableFuture.supplyAsync(() -> findReviews(emp.getId()))
    )
    .thenCompose(reviews -> 
        CompletableFuture.supplyAsync(() -> calculateAnalytics(reviews))
    )
    .thenCompose(analytics -> 
        CompletableFuture.supplyAsync(() -> generateReport(analytics))
    );
```

**Real PerformanceTrack workflow:**
```java
public class ReviewWorkflow {
    
    // Complete review submission workflow
    public CompletableFuture<String> submitReviewWorkflow(Review review) {
        return CompletableFuture
            // Step 1: Validate review
            .supplyAsync(() -> {
                validateReview(review);
                return review;
            })
            // Step 2: Save to database
            .thenCompose(validReview -> 
                CompletableFuture.supplyAsync(() -> 
                    reviewRepository.save(validReview)
                )
            )
            // Step 3: Notify manager
            .thenCompose(savedReview -> 
                CompletableFuture.supplyAsync(() -> {
                    notifyManager(savedReview);
                    return savedReview;
                })
            )
            // Step 4: Update analytics
            .thenCompose(review -> 
                CompletableFuture.supplyAsync(() -> {
                    updateAnalytics(review);
                    return review;
                })
            )
            // Step 5: Send confirmation email
            .thenCompose(review -> 
                CompletableFuture.supplyAsync(() -> 
                    sendConfirmation(review.getEmployee())
                )
            )
            // Final result
            .thenApply(result -> "Review submitted successfully");
    }
    
    // Approval workflow with multiple steps
    public CompletableFuture<Review> approvalWorkflow(Long reviewId) {
        return CompletableFuture
            .supplyAsync(() -> reviewRepository.findById(reviewId))
            .thenCompose(review -> 
                // Check if manager approved
                CompletableFuture.supplyAsync(() -> 
                    checkManagerApproval(review)
                )
            )
            .thenCompose(approved -> {
                if (!approved) {
                    throw new ApprovalException("Manager approval required");
                }
                // Check HR approval
                return CompletableFuture.supplyAsync(() -> 
                    checkHRApproval(reviewId)
                );
            })
            .thenCompose(hrApproved -> {
                if (!hrApproved) {
                    throw new ApprovalException("HR approval required");
                }
                // Final approval
                return CompletableFuture.supplyAsync(() -> 
                    finalizeApproval(reviewId)
                );
            });
    }
}
```

---

### Q66: How do you handle CompletableFuture in Spring Boot REST APIs?
Spring supports returning CompletableFuture from controller methods for async request processing.

**Basic async controller:**
```java
@RestController
@RequestMapping("/api/reviews")
public class ReviewController {
    
    @Autowired
    private ReviewService reviewService;
    
    // Async endpoint
    @GetMapping("/{id}")
    public CompletableFuture<ReviewDTO> getReview(@PathVariable Long id) {
        return reviewService.getReviewAsync(id)
            .thenApply(this::convertToDTO);
    }
    
    // Multiple async operations
    @GetMapping("/employee/{empId}")
    public CompletableFuture<EmployeeReviewReport> getEmployeeReport(
            @PathVariable Long empId) {
        
        CompletableFuture<Employee> empFuture = 
            employeeService.getEmployeeAsync(empId);
            
        CompletableFuture<List<Review>> reviewsFuture = 
            reviewService.getReviewsAsync(empId);
        
        return empFuture.thenCombine(reviewsFuture, (emp, reviews) -> 
            new EmployeeReviewReport(emp, reviews)
        );
    }
}
```

**Service layer:**
```java
@Service
public class ReviewService {
    
    @Async  // Spring's @Async annotation
    public CompletableFuture<Review> getReviewAsync(Long id) {
        return CompletableFuture.supplyAsync(() -> 
            reviewRepository.findById(id)
                .orElseThrow(() -> new NotFoundException("Review not found"))
        );
    }
    
    @Async
    public CompletableFuture<List<Review>> getReviewsAsync(Long empId) {
        return CompletableFuture.supplyAsync(() -> 
            reviewRepository.findByEmployeeId(empId)
        );
    }
}
```

**Configuration for async:**
```java
@Configuration
@EnableAsync
public class AsyncConfig {
    
    @Bean(name = "taskExecutor")
    public Executor taskExecutor() {
        ThreadPoolTaskExecutor executor = new ThreadPoolTaskExecutor();
        executor.setCorePoolSize(10);
        executor.setMaxPoolSize(20);
        executor.setQueueCapacity(100);
        executor.setThreadNamePrefix("async-");
        executor.initialize();
        return executor;
    }
}
```

**Error handling in controllers:**
```java
@RestController
public class ReviewController {
    
    @GetMapping("/reviews/{id}")
    public CompletableFuture<ResponseEntity<ReviewDTO>> getReview(
            @PathVariable Long id) {
        
        return reviewService.getReviewAsync(id)
            .thenApply(review -> ResponseEntity.ok(convertToDTO(review)))
            .exceptionally(ex -> {
                if (ex.getCause() instanceof NotFoundException) {
                    return ResponseEntity.notFound().build();
                }
                return ResponseEntity.status(500).build();
            });
    }
}
```

---

## DATE-TIME API (JAVA 8)

### Q67: Why was the new Date-Time API introduced in Java 8?
The old `java.util.Date` and `Calendar` classes had serious problems:

1. **Not thread-safe** - caused bugs in multi-threaded applications
2. **Mutable** - dates could be changed accidentally
3. **Poor API design** - confusing methods like months starting from 0
4. **No timezone support** - very difficult to handle timezones

**Old way problems:**

```java
Date date = new Date();
date.setMonth(5);  // Wait, is this May or June? (It's June!)
// Also, date is mutable - can be changed accidentally
```

**New way in PerformanceTrack:**

```java
LocalDate reviewDate = LocalDate.now();
LocalDate nextMonth = reviewDate.plusMonths(1);  // Original unchanged!
// reviewDate is still the same, nextMonth is new object
```

**Industry benefit:** In your system, when storing review creation dates, using `LocalDateTime` ensures the date can't be accidentally modified and timezone handling is clear.

---

### Q68: Explain LocalDate, LocalTime, and LocalDateTime
These are the three main classes for dates and times without timezone:

**LocalDate** - Just the date (year, month, day). No time, no timezone.

```java
LocalDate today = LocalDate.now();
LocalDate reviewDeadline = LocalDate.of(2025, 3, 31);
LocalDate nextWeek = today.plusDays(7);
```

**LocalTime** - Just the time (hours, minutes, seconds). No date, no timezone.

```java
LocalTime now = LocalTime.now();
LocalTime meetingTime = LocalTime.of(14, 30);  // 2:30 PM
LocalTime oneHourLater = meetingTime.plusHours(1);
```

**LocalDateTime** - Both date and time. No timezone.

```java
LocalDateTime submissionTime = LocalDateTime.now();
LocalDateTime deadline = LocalDateTime.of(2025, 3, 31, 23, 59);
```

**Real PerformanceTrack usage:**

```java
@Entity
public class Review {
    private LocalDate reviewPeriodStart;
    private LocalDate reviewPeriodEnd;
    private LocalDateTime submittedAt;
    private LocalDateTime approvedAt;
}

// Check if review is overdue
public boolean isOverdue(Review review) {
    return LocalDateTime.now().isAfter(review.getDeadline());
}
```

---

### Q69: What is ZonedDateTime and when do you use it?
`ZonedDateTime` includes date, time, AND timezone information. Use it when you need to handle events across different timezones.

**When to use:**
- International applications
- Scheduling across timezones
- Storing exact moments in time

**Example:** Your company has offices in India, US, and UK:

```java
// Review submission in India
ZonedDateTime indiaSubmission = ZonedDateTime.now(ZoneId.of("Asia/Kolkata"));

// Convert to US Eastern time to show manager
ZonedDateTime usTime = indiaSubmission.withZoneSameInstant(ZoneId.of("America/New_York"));

System.out.println("India: " + indiaSubmission);  // 2025-01-31T15:30:00+05:30[Asia/Kolkata]
System.out.println("US: " + usTime);              // 2025-01-31T05:00:00-05:00[America/New_York]
```

**Industry scenario:** If employees can submit reviews from anywhere in the world, store submission time as `ZonedDateTime` or convert to UTC using `Instant`:

```java
Instant submissionInstant = indiaSubmission.toInstant(); // UTC representation
// Store this in database - always unambiguous
```

---

### Q70: What is the difference between Instant and LocalDateTime?
`Instant` represents a point on the timeline in UTC (like a timestamp).

`LocalDateTime` represents a date-time without timezone context.

**Think of it like this:**

Instant = "exactly 1.7 billion seconds since January 1, 1970 UTC"
LocalDateTime = "3:30 PM on March 15, 2025" (but in which timezone?)

**Example in PerformanceTrack:**

```java
// When review was submitted (exact moment, timezone-independent)
Instant submittedInstant = Instant.now();

// When to show it to user (depends on their timezone)
LocalDateTime userLocalTime = LocalDateTime.ofInstant(
    submittedInstant, 
    ZoneId.of("Asia/Kolkata")
);
```

**Best practice for your database:**

```java
@Entity
public class Review {
    // Store as Instant for precise moment
    private Instant createdAt;
    
    // Convert to LocalDateTime when displaying to users
    public LocalDateTime getCreatedAtInUserTimezone(String timezone) {
        return LocalDateTime.ofInstant(createdAt, ZoneId.of(timezone));
    }
}
```

**Rule of thumb:** Use `Instant` for storing events in database, use `LocalDateTime` for displaying to users.

---

### Q71: How do you format and parse dates in Java 8+?
Use `DateTimeFormatter` class. It's immutable and thread-safe (unlike old `SimpleDateFormat`).

**Formatting (date to string):**

```java
LocalDateTime now = LocalDateTime.now();

// Predefined formats
String iso = now.format(DateTimeFormatter.ISO_DATE_TIME);
// Output: 2025-01-31T15:30:45.123

// Custom format for reports
DateTimeFormatter reportFormat = DateTimeFormatter.ofPattern("dd MMM yyyy, hh:mm a");
String formatted = now.format(reportFormat);
// Output: 31 Jan 2025, 03:30 PM
```

**Parsing (string to date):**

```java
String dateStr = "31-01-2025";
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("dd-MM-yyyy");
LocalDate date = LocalDate.parse(dateStr, formatter);
```

**Real PerformanceTrack usage:**

```java
public class ReportService {
    private static final DateTimeFormatter REPORT_DATE_FORMAT = 
        DateTimeFormatter.ofPattern("dd-MMM-yyyy");
    
    public String generateReportFilename(LocalDate reportDate) {
        return "Performance_Report_" + 
               reportDate.format(REPORT_DATE_FORMAT) + ".pdf";
        // Output: Performance_Report_31-Jan-2025.pdf
    }
    
    public LocalDate parseReportPeriod(String dateStr) {
        return LocalDate.parse(dateStr, REPORT_DATE_FORMAT);
    }
}
```

**Common patterns:**
- `dd/MM/yyyy` - 31/01/2025
- `yyyy-MM-dd` - 2025-01-31 (ISO format)
- `dd MMM yyyy` - 31 Jan 2025
- `dd-MM-yyyy HH:mm:ss` - 31-01-2025 15:30:45

---

### Q72: How do you calculate the difference between two dates?
Use `Period` for date-based differences and `Duration` for time-based differences.

**Period - for dates (years, months, days):**

```java
LocalDate start = LocalDate.of(2024, 1, 1);
LocalDate end = LocalDate.of(2025, 3, 15);

Period period = Period.between(start, end);

System.out.println("Years: " + period.getYears());    // 1
System.out.println("Months: " + period.getMonths());  // 2
System.out.println("Days: " + period.getDays());      // 14

// Total months
long totalMonths = start.until(end, ChronoUnit.MONTHS);  // 14 months
```

**Duration - for time (hours, minutes, seconds):**

```java
LocalDateTime start = LocalDateTime.of(2025, 1, 31, 10, 0);
LocalDateTime end = LocalDateTime.of(2025, 1, 31, 15, 30);

Duration duration = Duration.between(start, end);

System.out.println("Hours: " + duration.toHours());      // 5
System.out.println("Minutes: " + duration.toMinutes());  // 330
System.out.println("Seconds: " + duration.getSeconds()); // 19800
```

**Real PerformanceTrack scenarios:**

```java
public class ReviewAnalytics {
    
    // Calculate employee tenure
    public Period getEmployeeTenure(Employee emp) {
        LocalDate hireDate = emp.getHireDate();
        LocalDate today = LocalDate.now();
        return Period.between(hireDate, today);
    }
    
    // Check if review is overdue
    public String getOverdueStatus(Review review) {
        LocalDate deadline = review.getDeadline();
        LocalDate today = LocalDate.now();
        
        if (today.isAfter(deadline)) {
            Period overdue = Period.between(deadline, today);
            return "Overdue by " + overdue.getDays() + " days";
        }
        return "On time";
    }
    
    // Calculate review completion time
    public String getCompletionTime(Review review) {
        LocalDateTime submitted = review.getSubmittedAt();
        LocalDateTime approved = review.getApprovedAt();
        
        Duration timeTaken = Duration.between(submitted, approved);
        long hours = timeTaken.toHours();
        long minutes = timeTaken.toMinutesPart();
        
        return String.format("Completed in %d hours and %d minutes", hours, minutes);
    }
    
    // Check if employee is eligible (worked 90+ days)
    public boolean isEligibleForReview(Employee emp) {
        long daysWorked = ChronoUnit.DAYS.between(
            emp.getHireDate(), 
            LocalDate.now()
        );
        return daysWorked >= 90;
    }
}
```

---

### Q73: How do you work with different timezones?
Use `ZoneId` and `ZonedDateTime` for timezone conversions.

**Available timezones:**
```java
// Get all available zones
Set<String> zones = ZoneId.getAvailableZoneIds();

// Common zones
ZoneId india = ZoneId.of("Asia/Kolkata");
ZoneId usEast = ZoneId.of("America/New_York");
ZoneId london = ZoneId.of("Europe/London");
ZoneId utc = ZoneId.of("UTC");
```

**Converting between timezones:**
```java
// Current time in India
ZonedDateTime indiaTime = ZonedDateTime.now(ZoneId.of("Asia/Kolkata"));
System.out.println("India: " + indiaTime);

// Convert to US Eastern
ZonedDateTime usTime = indiaTime.withZoneSameInstant(
    ZoneId.of("America/New_York")
);
System.out.println("US: " + usTime);

// Convert to UTC
ZonedDateTime utcTime = indiaTime.withZoneSameInstant(ZoneId.of("UTC"));
System.out.println("UTC: " + utcTime);
```

**Real PerformanceTrack global operations:**

```java
public class GlobalReviewService {
    
    // Schedule review deadline in employee's timezone
    public ZonedDateTime scheduleReviewDeadline(Employee emp, int daysFromNow) {
        ZoneId employeeZone = ZoneId.of(emp.getTimezone());
        
        return ZonedDateTime.now(employeeZone)
            .plusDays(daysFromNow)
            .withHour(23)
            .withMinute(59);
    }
    
    // Convert submission time to manager's timezone
    public String getSubmissionTimeForManager(Review review, Manager manager) {
        Instant submissionTime = review.getSubmittedAt();
        ZoneId managerZone = ZoneId.of(manager.getTimezone());
        
        ZonedDateTime managerTime = ZonedDateTime.ofInstant(
            submissionTime, 
            managerZone
        );
        
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern(
            "dd MMM yyyy, hh:mm a z"
        );
        
        return managerTime.format(formatter);
        // Output: "31 Jan 2025, 02:30 PM EST"
    }
    
    // Check if review submitted during business hours
    public boolean isSubmittedDuringBusinessHours(Review review, String timezone) {
        ZonedDateTime submissionTime = ZonedDateTime.ofInstant(
            review.getSubmittedAt(),
            ZoneId.of(timezone)
        );
        
        LocalTime time = submissionTime.toLocalTime();
        LocalTime businessStart = LocalTime.of(9, 0);
        LocalTime businessEnd = LocalTime.of(18, 0);
        
        return time.isAfter(businessStart) && time.isBefore(businessEnd);
    }
    
    // Schedule global team meeting
    public Map<String, ZonedDateTime> scheduleMeeting(
            LocalDateTime utcMeetingTime,
            List<String> timezones) {
        
        ZonedDateTime utcTime = ZonedDateTime.of(
            utcMeetingTime, 
            ZoneId.of("UTC")
        );
        
        Map<String, ZonedDateTime> meetingTimes = new HashMap<>();
        
        for (String tz : timezones) {
            ZonedDateTime localTime = utcTime.withZoneSameInstant(
                ZoneId.of(tz)
            );
            meetingTimes.put(tz, localTime);
        }
        
        return meetingTimes;
    }
}
```

---

### Q74: How do you add or subtract time from dates?
Use `plus` and `minus` methods with specific units.

**LocalDate operations:**
```java
LocalDate today = LocalDate.now();

LocalDate nextWeek = today.plusWeeks(1);
LocalDate nextMonth = today.plusMonths(1);
LocalDate nextYear = today.plusYears(1);
LocalDate tomorrow = today.plusDays(1);

LocalDate lastWeek = today.minusWeeks(1);
LocalDate lastMonth = today.minusMonths(1);
LocalDate yesterday = today.minusDays(1);
```

**LocalDateTime operations:**
```java
LocalDateTime now = LocalDateTime.now();

LocalDateTime inTwoHours = now.plusHours(2);
LocalDateTime in30Minutes = now.plusMinutes(30);
LocalDateTime in45Seconds = now.plusSeconds(45);

LocalDateTime anHourAgo = now.minusHours(1);
```

**Real PerformanceTrack usage:**

```java
public class ReviewScheduler {
    
    // Set review period
    public Review createReviewPeriod(Employee emp) {
        Review review = new Review();
        
        LocalDate today = LocalDate.now();
        review.setPeriodStart(today.minusMonths(6));  // Last 6 months
        review.setPeriodEnd(today);
        review.setDeadline(today.plusDays(14));       // 2 weeks to complete
        
        return review;
    }
    
    // Calculate quarterly review dates
    public List<LocalDate> getQuarterlyReviewDates(int year) {
        LocalDate startOfYear = LocalDate.of(year, 1, 1);
        
        return List.of(
            startOfYear.plusMonths(3),   // Q1: March 31
            startOfYear.plusMonths(6),   // Q2: June 30
            startOfYear.plusMonths(9),   // Q3: September 30
            startOfYear.plusMonths(12)   // Q4: December 31
        );
    }
    
    // Get review reminders schedule
    public List<LocalDateTime> getReminderSchedule(LocalDateTime deadline) {
        return List.of(
            deadline.minusDays(7),   // 1 week before
            deadline.minusDays(3),   // 3 days before
            deadline.minusDays(1),   // 1 day before
            deadline.minusHours(6)   // 6 hours before
        );
    }
    
    // Extend deadline
    public LocalDate extendDeadline(Review review, int additionalDays) {
        LocalDate currentDeadline = review.getDeadline();
        LocalDate newDeadline = currentDeadline.plusDays(additionalDays);
        
        review.setDeadline(newDeadline);
        review.setExtensionGranted(true);
        
        return newDeadline;
    }
}
```

---

### Q75: How do you compare dates and times?
Use comparison methods: `isBefore()`, `isAfter()`, `isEqual()`, or `compareTo()`.

**Comparing LocalDate:**
```java
LocalDate date1 = LocalDate.of(2025, 1, 31);
LocalDate date2 = LocalDate.of(2025, 2, 15);

boolean before = date1.isBefore(date2);      // true
boolean after = date1.isAfter(date2);        // false
boolean equal = date1.isEqual(date2);        // false

int comparison = date1.compareTo(date2);     // negative (date1 < date2)
```

**Comparing LocalDateTime:**
```java
LocalDateTime dt1 = LocalDateTime.now();
LocalDateTime dt2 = dt1.plusHours(1);

if (dt1.isBefore(dt2)) {
    System.out.println("dt1 is earlier");
}
```

**Real PerformanceTrack validations:**

```java
public class ReviewValidator {
    
    // Check if review period is valid
    public boolean isValidReviewPeriod(Review review) {
        LocalDate start = review.getPeriodStart();
        LocalDate end = review.getPeriodEnd();
        
        return end.isAfter(start);
    }
    
    // Check if submission is on time
    public boolean isSubmittedOnTime(Review review) {
        LocalDateTime submitted = review.getSubmittedAt();
        LocalDateTime deadline = review.getDeadline();
        
        return submitted.isBefore(deadline) || submitted.isEqual(deadline);
    }
    
    // Get review status based on dates
    public String getReviewStatus(Review review) {
        LocalDate today = LocalDate.now();
        LocalDate deadline = review.getDeadline();
        
        if (review.getStatus().equals("COMPLETED")) {
            return "Completed";
        } else if (today.isAfter(deadline)) {
            return "Overdue";
        } else if (today.isEqual(deadline)) {
            return "Due Today";
        } else {
            return "In Progress";
        }
    }
    
    // Sort reviews by submission date
    public List<Review> sortBySubmissionDate(List<Review> reviews) {
        return reviews.stream()
            .sorted(Comparator.comparing(Review::getSubmittedAt))
            .toList();
    }
    
    // Find most recent review
    public Optional<Review> getMostRecentReview(List<Review> reviews) {
        return reviews.stream()
            .max(Comparator.comparing(Review::getSubmittedAt));
    }
}
```

---

### Q76: How do you get specific components from dates?
Use getter methods to extract individual components.

**Extracting from LocalDate:**
```java
LocalDate date = LocalDate.of(2025, 3, 15);

int year = date.getYear();           // 2025
int month = date.getMonthValue();    // 3
Month monthEnum = date.getMonth();   // MARCH
int day = date.getDayOfMonth();      // 15
DayOfWeek dayOfWeek = date.getDayOfWeek();  // SATURDAY
```

**Extracting from LocalTime:**
```java
LocalTime time = LocalTime.of(15, 30, 45);

int hour = time.getHour();           // 15
int minute = time.getMinute();       // 30
int second = time.getSecond();       // 45
```

**Extracting from LocalDateTime:**
```java
LocalDateTime dt = LocalDateTime.now();

int year = dt.getYear();
int month = dt.getMonthValue();
int day = dt.getDayOfMonth();
int hour = dt.getHour();
int minute = dt.getMinute();
```

**Real PerformanceTrack analytics:**

```java
public class ReviewAnalytics {
    
    // Group reviews by month
    public Map<String, List<Review>> groupByMonth(List<Review> reviews) {
        return reviews.stream()
            .collect(Collectors.groupingBy(review -> {
                LocalDate date = review.getSubmittedDate();
                return date.getMonth().toString() + " " + date.getYear();
            }));
    }
    
    // Count reviews by day of week
    public Map<DayOfWeek, Long> countByDayOfWeek(List<Review> reviews) {
        return reviews.stream()
            .collect(Collectors.groupingBy(
                review -> review.getSubmittedDate().getDayOfWeek(),
                Collectors.counting()
            ));
    }
    
    // Get reviews from specific quarter
    public List<Review> getQuarterlyReviews(List<Review> reviews, int year, int quarter) {
        int startMonth = (quarter - 1) * 3 + 1;
        int endMonth = quarter * 3;
        
        return reviews.stream()
            .filter(review -> {
                LocalDate date = review.getSubmittedDate();
                return date.getYear() == year &&
                       date.getMonthValue() >= startMonth &&
                       date.getMonthValue() <= endMonth;
            })
            .toList();
    }
    
    // Check if submitted during weekend
    public boolean isWeekendSubmission(Review review) {
        DayOfWeek day = review.getSubmittedDate().getDayOfWeek();
        return day == DayOfWeek.SATURDAY || day == DayOfWeek.SUNDAY;
    }
    
    // Generate monthly report filename
    public String generateMonthlyReportName(LocalDate date) {
        return String.format(
            "Report_%s_%d.pdf",
            date.getMonth().toString(),
            date.getYear()
        );
    }
}
```

---

### Q77: How do you work with specific dates like first/last day of month?
Use `TemporalAdjusters` for common date adjustments.

**Common adjusters:**
```java
LocalDate today = LocalDate.now();

// First/last day of month
LocalDate firstDay = today.with(TemporalAdjusters.firstDayOfMonth());
LocalDate lastDay = today.with(TemporalAdjusters.lastDayOfMonth());

// First/last day of year
LocalDate firstDayOfYear = today.with(TemporalAdjusters.firstDayOfYear());
LocalDate lastDayOfYear = today.with(TemporalAdjusters.lastDayOfYear());

// Next Monday
LocalDate nextMonday = today.with(TemporalAdjusters.next(DayOfWeek.MONDAY));

// Previous Friday
LocalDate prevFriday = today.with(TemporalAdjusters.previous(DayOfWeek.FRIDAY));

// First Monday of month
LocalDate firstMonday = today.with(
    TemporalAdjusters.firstInMonth(DayOfWeek.MONDAY)
);
```

**Real PerformanceTrack usage:**

```java
public class ReviewScheduler {
    
    // Set review period to last month
    public Review createMonthlyReview(Employee emp) {
        Review review = new Review();
        
        LocalDate today = LocalDate.now();
        LocalDate lastMonth = today.minusMonths(1);
        
        review.setPeriodStart(lastMonth.with(TemporalAdjusters.firstDayOfMonth()));
        review.setPeriodEnd(lastMonth.with(TemporalAdjusters.lastDayOfMonth()));
        
        return review;
    }
    
    // Schedule quarterly reviews on first Monday
    public LocalDate scheduleQuarterlyReview(int year, int quarter) {
        int startMonth = (quarter - 1) * 3 + 1;
        LocalDate firstOfQuarter = LocalDate.of(year, startMonth, 1);
        
        return firstOfQuarter.with(
            TemporalAdjusters.firstInMonth(DayOfWeek.MONDAY)
        );
    }
    
    // Get all Fridays in a month (pay days)
    public List<LocalDate> getPayDaysInMonth(int year, int month) {
        LocalDate date = LocalDate.of(year, month, 1);
        LocalDate endOfMonth = date.with(TemporalAdjusters.lastDayOfMonth());
        
        List<LocalDate> fridays = new ArrayList<>();
        LocalDate friday = date.with(TemporalAdjusters.nextOrSame(DayOfWeek.FRIDAY));
        
        while (!friday.isAfter(endOfMonth)) {
            fridays.add(friday);
            friday = friday.with(TemporalAdjusters.next(DayOfWeek.FRIDAY));
        }
        
        return fridays;
    }
    
    // Set deadline to end of next business day
    public LocalDateTime setBusinessDeadline() {
        LocalDate today = LocalDate.now();
        DayOfWeek dayOfWeek = today.getDayOfWeek();
        
        LocalDate deadline;
        if (dayOfWeek == DayOfWeek.FRIDAY) {
            deadline = today.with(TemporalAdjusters.next(DayOfWeek.MONDAY));
        } else if (dayOfWeek == DayOfWeek.SATURDAY) {
            deadline = today.with(TemporalAdjusters.next(DayOfWeek.MONDAY));
        } else {
            deadline = today.plusDays(1);
        }
        
        return deadline.atTime(17, 0);  // 5 PM
    }
}
```

---

### Q78: How do you handle date parsing errors?
Use try-catch blocks to handle `DateTimeParseException`.

**Basic error handling:**
```java
String dateStr = "invalid-date";

try {
    LocalDate date = LocalDate.parse(dateStr);
} catch (DateTimeParseException e) {
    System.out.println("Invalid date format: " + e.getMessage());
}
```

**With custom formatter:**
```java
String dateStr = "31/01/2025";
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("dd/MM/yyyy");

try {
    LocalDate date = LocalDate.parse(dateStr, formatter);
    System.out.println("Parsed date: " + date);
} catch (DateTimeParseException e) {
    System.out.println("Error parsing date: " + e.getParsedString());
}
```

**Real PerformanceTrack validation:**

```java
public class ReviewInputValidator {
    
    private static final DateTimeFormatter DATE_FORMATTER = 
        DateTimeFormatter.ofPattern("dd-MM-yyyy");
    
    // Validate and parse review date
    public Optional<LocalDate> parseReviewDate(String dateStr) {
        try {
            LocalDate date = LocalDate.parse(dateStr, DATE_FORMATTER);
            
            // Additional validation
            if (date.isAfter(LocalDate.now())) {
                throw new ValidationException("Date cannot be in future");
            }
            
            if (date.isBefore(LocalDate.now().minusYears(5))) {
                throw new ValidationException("Date too old");
            }
            
            return Optional.of(date);
            
        } catch (DateTimeParseException e) {
            logger.warn("Invalid date format: " + dateStr, e);
            return Optional.empty();
        }
    }
    
    // Parse with multiple format attempts
    public LocalDate parseFlexibleDate(String dateStr) {
        List<DateTimeFormatter> formatters = List.of(
            DateTimeFormatter.ofPattern("dd-MM-yyyy"),
            DateTimeFormatter.ofPattern("dd/MM/yyyy"),
            DateTimeFormatter.ofPattern("yyyy-MM-dd"),
            DateTimeFormatter.ISO_DATE
        );
        
        for (DateTimeFormatter formatter : formatters) {
            try {
                return LocalDate.parse(dateStr, formatter);
            } catch (DateTimeParseException e) {
                // Try next format
            }
        }
        
        throw new ValidationException(
            "Could not parse date: " + dateStr + 
            ". Supported formats: dd-MM-yyyy, dd/MM/yyyy, yyyy-MM-dd"
        );
    }
    
    // Validate date range
    public ValidationResult validateDateRange(String startStr, String endStr) {
        try {
            LocalDate start = LocalDate.parse(startStr, DATE_FORMATTER);
            LocalDate end = LocalDate.parse(endStr, DATE_FORMATTER);
            
            if (!end.isAfter(start)) {
                return ValidationResult.error("End date must be after start date");
            }
            
            Period period = Period.between(start, end);
            if (period.toTotalMonths() > 12) {
                return ValidationResult.error("Period cannot exceed 12 months");
            }
            
            return ValidationResult.success();
            
        } catch (DateTimeParseException e) {
            return ValidationResult.error("Invalid date format");
        }
    }
}
```

---

## FUNCTIONAL INTERFACES (DETAILED)

### Q79: What are BiPredicate, BiFunction, and BiConsumer?
These are the "Bi" versions of functional interfaces that accept TWO parameters instead of one.

**BiPredicate<T, U>** - Takes two inputs, returns boolean

```java
BiPredicate<Employee, Review> isEligibleForBonus = 
    (emp, review) -> emp.getYearsOfService() > 2 && review.getScore() >= 4;

// Usage
if (isEligibleForBonus.test(employee, latestReview)) {
    calculateBonus(employee);
}
```

**BiFunction<T, U, R>** - Takes two inputs, returns output

```java
BiFunction<Employee, List<Review>, Double> calculateAverage = 
    (emp, reviews) -> reviews.stream()
        .mapToDouble(Review::getScore)
        .average()
        .orElse(0.0);

// Usage
Double avgScore = calculateAverage.apply(employee, employeeReviews);
```

**BiConsumer<T, U>** - Takes two inputs, returns nothing

```java
BiConsumer<Employee, String> sendNotification = 
    (emp, message) -> emailService.send(emp.getEmail(), "Notification", message);

// Usage
employees.forEach(emp -> 
    sendNotification.accept(emp, "Your review is ready")
);
```

**Real PerformanceTrack usage:**

```java
public class AnalyticsService {
    
    // Compare two departments
    private BiPredicate<String, String> isDeptBetter = (dept1, dept2) -> {
        double avg1 = getDeptAverage(dept1);
        double avg2 = getDeptAverage(dept2);
        return avg1 > avg2;
    };
    
    // Calculate performance gap between employee and department average
    private BiFunction<Employee, String, Double> performanceGap = (emp, dept) -> {
        double empAvg = getEmployeeAverage(emp);
        double deptAvg = getDepartmentAverage(dept);
        return empAvg - deptAvg;
    };
    
    // Log review activity
    private BiConsumer<Employee, Review> logActivity = (emp, review) -> {
        auditLog.log(String.format(
            "Review %d for %s - Score: %d",
            review.getId(), emp.getName(), review.getScore()
        ));
    };
}
```

---

### Q80: What is UnaryOperator and BinaryOperator?
These are special cases of Function where input and output types are the same.

**UnaryOperator<T>** - Takes one input, returns same type
Equivalent to `Function<T, T>`

```java
UnaryOperator<String> toUpperCase = String::toUpperCase;
String result = toUpperCase.apply("hello");  // "HELLO"

// In PerformanceTrack
UnaryOperator<Review> approveReview = review -> {
    review.setStatus("APPROVED");
    review.setApprovedDate(LocalDateTime.now());
    return review;
};

Review approved = approveReview.apply(pendingReview);
```

**BinaryOperator<T>** - Takes two inputs of same type, returns same type
Equivalent to `BiFunction<T, T, T>`

```java
BinaryOperator<Integer> sum = (a, b) -> a + b;
Integer total = sum.apply(10, 20);  // 30

// In PerformanceTrack - merge two review periods
BinaryOperator<Review> mergeReviews = (r1, r2) -> {
    Review merged = new Review();
    merged.setScore((r1.getScore() + r2.getScore()) / 2);
    merged.setComments(r1.getComments() + "; " + r2.getComments());
    return merged;
};
```

**Real analytics example:**

```java
public class ScoreCalculator {
    
    // Apply performance multiplier
    private UnaryOperator<Double> applyMultiplier = score -> score * 1.1;
    
    // Combine scores with weights
    private BinaryOperator<Double> weightedAverage = (score1, score2) -> 
        (score1 * 0.7) + (score2 * 0.3);  // 70% manager, 30% peer
    
    public Double calculateFinalScore(Review managerReview, Review peerReview) {
        return weightedAverage.apply(
            applyMultiplier.apply((double) managerReview.getScore()),
            (double) peerReview.getScore()
        );
    }
}
```

---

### Q81: Explain IntPredicate, IntFunction, IntConsumer and other primitive specializations
Java provides primitive specializations to avoid boxing/unboxing overhead when working with int, long, double.

**Why they exist:**
- `Predicate<Integer>` boxes int to Integer (slow)
- `IntPredicate` works with primitive int (fast)

**Common primitive functional interfaces:**

```java
// INT specializations
IntPredicate scoreAbove3 = score -> score > 3;
IntFunction<String> scoreToGrade = score -> score >= 4 ? "Good" : "Average";
IntConsumer printScore = score -> System.out.println("Score: " + score);
IntSupplier randomScore = () -> (int) (Math.random() * 5) + 1;
IntUnaryOperator doubleIt = x -> x * 2;
IntBinaryOperator sum = (a, b) -> a + b;

// LONG specializations
LongPredicate isLargeId = id -> id > 1000000;
LongFunction<Employee> findById = id -> employeeRepository.findById(id);
LongConsumer logId = id -> logger.info("Processing ID: " + id);

// DOUBLE specializations
DoublePredicate isHighScore = score -> score >= 4.5;
DoubleFunction<String> formatScore = score -> String.format("%.2f", score);
DoubleConsumer addToTotal = score -> totalScore += score;
```

**Real PerformanceTrack performance optimization:**

```java
// BAD - Boxing overhead
public double calculateAverage(List<Review> reviews) {
    return reviews.stream()
        .map(Review::getScore)           // Stream<Integer> - boxed
        .mapToDouble(Integer::doubleValue)  // Now unboxing
        .average()
        .orElse(0.0);
}

// GOOD - No boxing
public double calculateAverage(List<Review> reviews) {
    return reviews.stream()
        .mapToInt(Review::getScore)      // IntStream - primitive
        .average()
        .orElse(0.0);
}

// Using primitive predicates
IntPredicate isPassingScore = score -> score >= 3;

long passingCount = reviews.stream()
    .mapToInt(Review::getScore)
    .filter(isPassingScore)  // Works directly with int
    .count();
```

**ToIntFunction, ToLongFunction, ToDoubleFunction:**

```java
ToIntFunction<Review> extractScore = Review::getScore;
ToDoubleFunction<Employee> calculateBonus = emp -> emp.getSalary() * 0.1;

// Usage in streams
int totalScore = reviews.stream()
    .mapToInt(extractScore)
    .sum();
```

**Performance matters:** In analytics processing thousands of reviews, using `mapToInt()` instead of `map()` can give significant performance improvement by avoiding boxing.

---

### Q82: How do you chain functional interfaces together?
Functional interfaces provide default methods for chaining: `and()`, `or()`, `negate()` for Predicate, `andThen()` and `compose()` for Function.

**Predicate chaining:**

```java
Predicate<Employee> isManager = emp -> emp.getRole().equals("MANAGER");
Predicate<Employee> isActive = emp -> emp.getStatus().equals("ACTIVE");
Predicate<Employee> hasHighScore = emp -> emp.getAverageScore() >= 4.0;

// Combine with AND
Predicate<Employee> activeManager = isManager.and(isActive);

// Combine with OR
Predicate<Employee> managerOrHighPerformer = isManager.or(hasHighScore);

// Negate
Predicate<Employee> notManager = isManager.negate();
```

**Function chaining:**

```java
Function<Employee, String> getName = Employee::getName;
Function<String, String> toUpper = String::toUpperCase;

// andThen - apply first, then second
Function<Employee, String> getUpperName = getName.andThen(toUpper);
String name1 = getUpperName.apply(employee);  // "JOHN DOE"

// compose - apply second, then first
Function<String, Integer> getLength = String::length;
Function<Employee, Integer> getNameLength = getLength.compose(getName);
Integer length = getNameLength.apply(employee);  // 8
```

**Real PerformanceTrack scenarios:**

```java
public class ReviewFilters {
    
    // Basic predicates
    private static final Predicate<Review> isCompleted = 
        r -> r.getStatus().equals("COMPLETED");
    
    private static final Predicate<Review> isHighScore = 
        r -> r.getScore() >= 4;
    
    private static final Predicate<Review> isRecent = 
        r -> r.getSubmittedDate().isAfter(LocalDate.now().minusMonths(3));
    
    // Combined predicates
    public Predicate<Review> getEligibleForBonus() {
        return isCompleted.and(isHighScore).and(isRecent);
    }
    
    public Predicate<Review> getNeedsAttention() {
        return isCompleted.negate()
            .or(r -> r.getScore() < 3);
    }
    
    // Usage
    public List<Review> getReviews(Predicate<Review> filter) {
        return reviews.stream()
            .filter(filter)
            .toList();
    }
}

public class DataTransformers {
    
    // Individual transformations
    private static final Function<Review, Integer> getScore = Review::getScore;
    private static final Function<Integer, String> scoreToGrade = score -> {
        if (score >= 4) return "Excellent";
        if (score >= 3) return "Good";
        return "Needs Improvement";
    };
    private static final Function<String, String> addPrefix = 
        grade -> "Performance: " + grade;
    
    // Chained transformation
    public Function<Review, String> getPerformanceLabel() {
        return getScore
            .andThen(scoreToGrade)
            .andThen(addPrefix);
    }
    
    // Usage
    public List<String> getPerformanceLabels(List<Review> reviews) {
        return reviews.stream()
            .map(getPerformanceLabel())
            .toList();
    }
}
```

**Consumer chaining:**

```java
Consumer<Review> logReview = r -> logger.info("Processing review: " + r.getId());
Consumer<Review> saveReview = r -> repository.save(r);
Consumer<Review> notifyEmployee = r -> emailService.notify(r.getEmployee());

// Chain consumers
Consumer<Review> processReview = logReview
    .andThen(saveReview)
    .andThen(notifyEmployee);

// Execute all in sequence
processReview.accept(review);
```

---

### Q83: What are the differences between Function, Operator, and Converter interfaces?
**Function<T, R>** - General transformation from one type to another

```java
Function<Employee, EmployeeDTO> converter = emp -> new EmployeeDTO(emp);
```

**UnaryOperator<T>** - Special Function where input and output are same type

```java
UnaryOperator<String> trimmer = String::trim;
```

**BinaryOperator<T>** - Combines two values of same type

```java
BinaryOperator<Integer> maxScore = Integer::max;
```

**There's no standard "Converter" interface**, but you can create custom ones:

```java
@FunctionalInterface
interface Converter<FROM, TO> {
    TO convert(FROM source);
    
    // Default method for reverse conversion
    default Converter<TO, FROM> reverse(Converter<TO, FROM> reverseConverter) {
        return reverseConverter;
    }
}
```

**Real PerformanceTrack usage:**

```java
public class DataConverters {
    
    // Function - different types
    private static final Function<Review, ReviewDTO> reviewToDTO = review -> {
        ReviewDTO dto = new ReviewDTO();
        dto.setId(review.getId());
        dto.setScore(review.getScore());
        dto.setComments(review.getComments());
        return dto;
    };
    
    // UnaryOperator - same type transformation
    private static final UnaryOperator<Review> normalizeReview = review -> {
        review.setComments(review.getComments().trim());
        review.setStatus(review.getStatus().toUpperCase());
        return review;
    };
    
    // BinaryOperator - combine two reviews
    private static final BinaryOperator<Review> mergeReviews = (r1, r2) -> {
        Review merged = new Review();
        merged.setScore((r1.getScore() + r2.getScore()) / 2);
        merged.setComments(r1.getComments() + "; " + r2.getComments());
        return merged;
    };
    
    // Custom converter interface
    @FunctionalInterface
    interface EntityConverter<E, D> {
        D toDTO(E entity);
        
        default List<D> toDTOList(List<E> entities) {
            return entities.stream()
                .map(this::toDTO)
                .toList();
        }
    }
    
    // Implementation
    public class ReviewConverter implements EntityConverter<Review, ReviewDTO> {
        public ReviewDTO toDTO(Review review) {
            return reviewToDTO.apply(review);
        }
    }
}
```

---

### Q84: How do you create custom functional interfaces?
Create an interface with exactly one abstract method and annotate with `@FunctionalInterface`.

**Basic custom functional interface:**

```java
@FunctionalInterface
public interface Validator<T> {
    boolean validate(T item);
    
    // Can have default methods
    default Validator<T> and(Validator<T> other) {
        return item -> this.validate(item) && other.validate(item);
    }
    
    // Can have static methods
    static <T> Validator<T> alwaysValid() {
        return item -> true;
    }
}
```

**Real PerformanceTrack custom interfaces:**

```java
@FunctionalInterface
public interface ScoreCalculator {
    double calculate(List<Review> reviews);
    
    default ScoreCalculator withBonus(double bonus) {
        return reviews -> this.calculate(reviews) + bonus;
    }
}

@FunctionalInterface
public interface ReviewProcessor {
    Review process(Review review);
    
    default ReviewProcessor andThen(ReviewProcessor after) {
        return review -> after.process(this.process(review));
    }
}

@FunctionalInterface
public interface NotificationStrategy {
    void notify(Employee employee, String message);
    
    static NotificationStrategy email() {
        return (emp, msg) -> emailService.send(emp.getEmail(), msg);
    }
    
    static NotificationStrategy sms() {
        return (emp, msg) -> smsService.send(emp.getPhone(), msg);
    }
}

// Usage
public class ReviewService {
    
    // Custom score calculator
    ScoreCalculator weightedCalculator = reviews -> {
        double managerWeight = 0.5;
        double peerWeight = 0.3;
        double selfWeight = 0.2;
        
        double managerScore = reviews.stream()
            .filter(r -> r.getType().equals("MANAGER"))
            .mapToDouble(Review::getScore)
            .average()
            .orElse(0);
        
        double peerScore = reviews.stream()
            .filter(r -> r.getType().equals("PEER"))
            .mapToDouble(Review::getScore)
            .average()
            .orElse(0);
        
        double selfScore = reviews.stream()
            .filter(r -> r.getType().equals("SELF"))
            .mapToDouble(Review::getScore)
            .average()
            .orElse(0);
        
        return (managerScore * managerWeight) + 
               (peerScore * peerWeight) + 
               (selfScore * selfWeight);
    };
    
    // Chain review processors
    ReviewProcessor processReview = ReviewProcessor
        .normalize()
        .andThen(ReviewProcessor.validate())
        .andThen(ReviewProcessor.calculateScore());
    
    // Use notification strategy
    public void notifyEmployee(Employee emp, String msg) {
        NotificationStrategy strategy = emp.isEmailEnabled() 
            ? NotificationStrategy.email()
            : NotificationStrategy.sms();
        
        strategy.notify(emp, msg);
    }
}
```

---

### Q85: What is the difference between Runnable and Callable?
**Runnable** - No return value, cannot throw checked exceptions
**Callable** - Returns a value, can throw exceptions

**Runnable:**
```java
Runnable task = () -> {
    System.out.println("Running task");
    // No return value
};

// Execute
new Thread(task).start();
```

**Callable:**
```java
Callable<String> task = () -> {
    System.out.println("Running task");
    return "Task result";  // Returns value
};

// Execute with ExecutorService
ExecutorService executor = Executors.newSingleThreadExecutor();
Future<String> future = executor.submit(task);
String result = future.get();
```

**Real PerformanceTrack usage:**

```java
public class AsyncReportService {
    
    private ExecutorService executor = Executors.newFixedThreadPool(10);
    
    // Runnable - fire and forget operations
    public void sendNotificationsAsync(List<Employee> employees) {
        Runnable task = () -> {
            employees.forEach(emp -> 
                emailService.send(emp.getEmail(), "Review reminder")
            );
        };
        
        executor.submit(task);
    }
    
    // Callable - need the result
    public Future<Report> generateReportAsync(Long employeeId) {
        Callable<Report> task = () -> {
            Employee emp = employeeRepository.findById(employeeId);
            List<Review> reviews = reviewRepository.findByEmployee(employeeId);
            return reportGenerator.generate(emp, reviews);
        };
        
        return executor.submit(task);
    }
    
    // Multiple callable tasks
    public List<Report> generateAllReports(List<Long> employeeIds) 
            throws InterruptedException, ExecutionException {
        
        List<Callable<Report>> tasks = employeeIds.stream()
            .map(id -> (Callable<Report>) () -> generateReport(id))
            .toList();
        
        List<Future<Report>> futures = executor.invokeAll(tasks);
        
        List<Report> reports = new ArrayList<>();
        for (Future<Report> future : futures) {
            reports.add(future.get());
        }
        
        return reports;
    }
}
```

---

### Q86: How do you handle exceptions in functional interfaces?
Standard functional interfaces don't allow checked exceptions. You have several options:

**Option 1: Wrap in unchecked exception**
```java
Function<String, LocalDate> parseDate = dateStr -> {
    try {
        return LocalDate.parse(dateStr);
    } catch (DateTimeParseException e) {
        throw new RuntimeException("Invalid date: " + dateStr, e);
    }
};
```

**Option 2: Create custom functional interface**
```java
@FunctionalInterface
public interface ThrowingFunction<T, R, E extends Exception> {
    R apply(T t) throws E;
}

// Usage
ThrowingFunction<String, LocalDate, DateTimeParseException> parseDate = 
    LocalDate::parse;
```

**Option 3: Wrapper utility**
```java
public class ExceptionWrapper {
    
    @FunctionalInterface
    public interface ThrowingFunction<T, R> {
        R apply(T t) throws Exception;
    }
    
    public static <T, R> Function<T, R> wrap(ThrowingFunction<T, R> func) {
        return t -> {
            try {
                return func.apply(t);
            } catch (Exception e) {
                throw new RuntimeException(e);
            }
        };
    }
}

// Usage
List<LocalDate> dates = dateStrings.stream()
    .map(ExceptionWrapper.wrap(LocalDate::parse))
    .toList();
```

**Real PerformanceTrack implementation:**

```java
public class FunctionalUtils {
    
    // Throwing consumer
    @FunctionalInterface
    public interface ThrowingConsumer<T> {
        void accept(T t) throws Exception;
    }
    
    public static <T> Consumer<T> wrap(ThrowingConsumer<T> consumer) {
        return t -> {
            try {
                consumer.accept(t);
            } catch (Exception e) {
                throw new RuntimeException(e);
            }
        };
    }
    
    // Throwing function
    @FunctionalInterface
    public interface ThrowingFunction<T, R> {
        R apply(T t) throws Exception;
    }
    
    public static <T, R> Function<T, R> wrapFunction(
            ThrowingFunction<T, R> function) {
        return t -> {
            try {
                return function.apply(t);
            } catch (Exception e) {
                throw new RuntimeException(e);
            }
        };
    }
}

// Usage in service
public class ReviewService {
    
    public void processReviews(List<Review> reviews) {
        // Wrapping method that throws IOException
        reviews.forEach(FunctionalUtils.wrap(review -> {
            pdfGenerator.generate(review);  // throws IOException
        }));
    }
    
    public List<EmployeeData> fetchEmployeeData(List<Long> ids) {
        return ids.stream()
            .map(FunctionalUtils.wrapFunction(id -> {
                return externalAPI.fetch(id);  // throws APIException
            }))
            .toList();
    }
}
```

---

## JAVA 9+ FEATURES

### Q87: What are Java 9 modules and the module system?
Java 9 introduced the module system (Project Jigsaw) to better organize large applications and improve encapsulation.

A module is a group of related packages with explicit dependencies. It's defined in a `module-info.java` file.

**Why modules?**
- Better encapsulation (hide internal implementation)
- Explicit dependencies (no more classpath hell)
- Improved security
- Smaller runtime images

**Example for PerformanceTrack:**

```java
// File: src/analytics/module-info.java
module com.performancetrack.analytics {
    // What this module needs
    requires com.performancetrack.core;
    requires spring.boot;
    requires spring.data.jpa;
    
    // What this module exposes to others
    exports com.performancetrack.analytics.service;
    exports com.performancetrack.analytics.dto;
    
    // Keep internal implementation hidden
    // (not exported, so other modules can't access)
    // com.performancetrack.analytics.internal
}

// File: src/reviews/module-info.java
module com.performancetrack.reviews {
    requires com.performancetrack.core;
    requires com.performancetrack.analytics;  // Can use analytics now
    
    exports com.performancetrack.reviews.service;
}
```

**Industry benefit:** In large enterprise apps, modules prevent developers from accidentally using internal classes they shouldn't depend on.

**Note:** Most Spring Boot applications don't use modules yet because it's complex and the ecosystem is still catching up. You'll see it more in large enterprise systems.

---

### Q88: What are factory methods for collections in Java 9?
Java 9 added convenient factory methods to create immutable collections in one line.

**Before Java 9:**
```java
List<String> list = new ArrayList<>();
list.add("PENDING");
list.add("IN_PROGRESS");
list.add("COMPLETED");
List<String> immutableList = Collections.unmodifiableList(list);
```

**Java 9+:**
```java
List<String> statuses = List.of("PENDING", "IN_PROGRESS", "COMPLETED");
Set<String> roles = Set.of("ADMIN", "MANAGER", "EMPLOYEE");
Map<String, Integer> scores = Map.of(
    "Excellent", 5,
    "Good", 4,
    "Average", 3
);
```

**Real PerformanceTrack usage:**

```java
public class ReviewService {
    // Immutable list of valid statuses
    private static final List<String> VALID_STATUSES = 
        List.of("DRAFT", "SUBMITTED", "IN_REVIEW", "APPROVED", "REJECTED");
    
    // Immutable set of allowed file types
    private static final Set<String> ALLOWED_FILE_TYPES = 
        Set.of("pdf", "docx", "jpg", "png");
    
    // Immutable map of score to rating
    private static final Map<Integer, String> SCORE_RATINGS = Map.of(
        5, "Exceptional",
        4, "Exceeds Expectations",
        3, "Meets Expectations",
        2, "Needs Improvement",
        1, "Unsatisfactory"
    );
    
    public boolean isValidStatus(String status) {
        return VALID_STATUSES.contains(status);
    }
}
```

**Important:** These collections are immutable. Trying to modify them throws `UnsupportedOperationException`:

```java
List<String> statuses = List.of("PENDING", "COMPLETED");
statuses.add("IN_PROGRESS");  // THROWS EXCEPTION!
```

**Benefit:** Safer code - you can share these collections without worrying about accidental modifications.

---

### Q89: What is the difference between var and traditional variable declaration?
`var` (introduced in Java 10) lets the compiler infer the type from the right-hand side. The variable is still strongly typed - just less verbose.

**Traditional way:**
```java
Map<String, List<Employee>> employeesByDepartment = new HashMap<>();
```

**With var:**
```java
var employeesByDepartment = new HashMap<String, List<Employee>>();
```

**Real PerformanceTrack examples:**

```java
// Instead of this:
List<Review> reviews = reviewRepository.findAll();
Map<Long, Employee> employeeMap = employees.stream()
    .collect(Collectors.toMap(Employee::getId, e -> e));

// You can write:
var reviews = reviewRepository.findAll();
var employeeMap = employees.stream()
    .collect(Collectors.toMap(Employee::getId, e -> e));

// Especially helpful with complex streams:
var avgScoreByDept = reviews.stream()
    .collect(Collectors.groupingBy(
        review -> review.getEmployee().getDepartment(),
        Collectors.averagingDouble(Review::getScore)
    ));
// Type is Map<String, Double> but you don't need to write it
```

**When NOT to use var:**

```java
// BAD - unclear what type this is
var data = processData();

// GOOD - type is clear from right side
var employeeList = new ArrayList<Employee>();
var name = "John Doe";
var count = 10;
```

**Important rules:**
- Only for local variables
- Cannot use with null: `var x = null;` // ERROR
- Cannot use without initialization: `var x;` // ERROR
- Still strongly typed - not like JavaScript's var!

---

### Q90: What are the enhancements to Optional in Java 9-11?
Java 9, 10, and 11 added several useful methods to Optional.

**Java 9 additions:**

**1. ifPresentOrElse()** - Do something if present, else do something else

```java
Optional<Review> reviewOpt = reviewRepository.findById(id);

reviewOpt.ifPresentOrElse(
    review -> System.out.println("Found: " + review.getId()),
    () -> System.out.println("Review not found")
);
```

**2. or()** - Provide alternative Optional if empty

```java
Optional<Employee> manager = employeeOpt
    .flatMap(Employee::getManager)
    .or(() -> employeeRepository.findDefaultManager());
```

**3. stream()** - Convert Optional to Stream

```java
List<Review> allReviews = employees.stream()
    .map(emp -> reviewRepository.findLatestReview(emp.getId()))
    .flatMap(Optional::stream)  // Filters out empty optionals
    .collect(Collectors.toList());
```

**Java 10 addition:**

**4. orElseThrow()** - Throw NoSuchElementException without parameter

```java
// Old way
Review review = reviewOpt.orElseThrow(() -> new NoSuchElementException());

// New way
Review review = reviewOpt.orElseThrow();
```

**Java 11 addition:**

**5. isEmpty()** - Check if Optional is empty

```java
// Old way
if (!reviewOpt.isPresent()) {
    // handle empty case
}

// New way
if (reviewOpt.isEmpty()) {
    // handle empty case
}
```

**Real scenario in your Analytics module:**

```java
public DepartmentReport generateReport(String department) {
    return employees.stream()
        .filter(e -> e.getDepartment().equals(department))
        .findFirst()
        .flatMap(emp -> reviewRepository.findLatestReview(emp.getId()))
        .map(this::calculateMetrics)
        .or(() -> Optional.of(getDefaultReport()))  // Java 9
        .orElseThrow();  // Java 10
}
```

---

### Q91: What are Text Blocks (Java 13-15)?
Text blocks make it easy to write multi-line strings without concatenation or escape characters. They use triple quotes `"""`.

**Old way:**
```java
String json = "{\n" +
              "  \"employeeId\": 123,\n" +
              "  \"name\": \"John Doe\",\n" +
              "  \"department\": \"Engineering\"\n" +
              "}";

String sql = "SELECT e.id, e.name, r.score " +
             "FROM employees e " +
             "JOIN reviews r ON e.id = r.employee_id " +
             "WHERE e.department = ?";
```

**Text block way:**
```java
String json = """
    {
      "employeeId": 123,
      "name": "John Doe",
      "department": "Engineering"
    }
    """;

String sql = """
    SELECT e.id, e.name, r.score
    FROM employees e
    JOIN reviews r ON e.id = r.employee_id
    WHERE e.department = ?
    """;
```

**Real PerformanceTrack usage:**

```java
public class ReportService {
    
    private static final String DEPARTMENT_REPORT_QUERY = """
        SELECT 
            e.department,
            AVG(r.score) as avg_score,
            COUNT(r.id) as review_count
        FROM employees e
        JOIN reviews r ON e.id = r.employee_id
        WHERE r.status = 'COMPLETED'
        AND r.created_date BETWEEN ? AND ?
        GROUP BY e.department
        ORDER BY avg_score DESC
        """;
    
    public String generateEmailTemplate(Employee emp, Review review) {
        return """
            Dear %s,
            
            Your performance review for %s has been completed.
            
            Score: %d/5
            Manager Comments: %s
            
            Please log in to view the complete review.
            
            Best regards,
            HR Team
            """.formatted(
                emp.getName(),
                review.getPeriod(),
                review.getScore(),
                review.getComments()
            );
    }
}
```

**Benefits:**
- No escape sequences needed
- Better readability
- Preserves formatting
- Great for SQL, JSON, HTML, email templates

---

### Q92: What are Records in Java (Java 14-16)?
Records are a compact way to create immutable data carrier classes. They automatically generate constructor, getters, `equals()`, `hashCode()`, and `toString()`.

**Old way:**
```java
public class EmployeeDTO {
    private final Long id;
    private final String name;
    private final String department;
    
    public EmployeeDTO(Long id, String name, String department) {
        this.id = id;
        this.name = name;
        this.department = department;
    }
    
    public Long getId() { return id; }
    public String getName() { return name; }
    public String getDepartment() { return department; }
    
    @Override
    public boolean equals(Object o) {
        // ... 10 lines of boilerplate
    }
    
    @Override
    public int hashCode() {
        // ... more boilerplate
    }
    
    @Override
    public String toString() {
        // ... more boilerplate
    }
}
```

**Record way:**
```java
public record EmployeeDTO(Long id, String name, String department) {}
```

That's it! You get everything automatically.

**Real PerformanceTrack usage:**

```java
// DTOs for API responses
public record ReviewSummaryDTO(
    Long reviewId,
    String employeeName,
    String department,
    Integer score,
    String status,
    LocalDate submittedDate
) {}

public record DepartmentStatsDTO(
    String department,
    Integer employeeCount,
    Double averageScore,
    Integer completedReviews
) {}

// Usage in service
public List<ReviewSummaryDTO> getReviewSummaries() {
    return reviews.stream()
        .map(review -> new ReviewSummaryDTO(
            review.getId(),
            review.getEmployee().getName(),
            review.getEmployee().getDepartment(),
            review.getScore(),
            review.getStatus(),
            review.getSubmittedDate()
        ))
        .collect(Collectors.toList());
}
```

**You can add custom methods:**
```java
public record PerformanceRating(Integer score, String comments) {
    // Compact constructor for validation
    public PerformanceRating {
        if (score < 1 || score > 5) {
            throw new IllegalArgumentException("Score must be 1-5");
        }
    }
    
    // Custom method
    public String getRatingLabel() {
        return switch(score) {
            case 5 -> "Exceptional";
            case 4 -> "Exceeds Expectations";
            case 3 -> "Meets Expectations";
            case 2 -> "Needs Improvement";
            case 1 -> "Unsatisfactory";
            default -> "Invalid";
        };
    }
}
```

**Benefits:**
- Less boilerplate
- Immutable by default
- Perfect for DTOs, API responses, configuration objects
- Clearer intent - signals this is just data

---

### Q93: What are Sealed Classes (Java 17)?
Sealed classes restrict which classes can extend or implement them. You explicitly declare who's allowed to be a subclass.

**Use `sealed` keyword with `permits` clause:**

```java
public sealed class Review 
    permits SelfReview, ManagerReview, PeerReview {
    // common review fields and methods
}

public final class SelfReview extends Review {
    // self review specific
}

public final class ManagerReview extends Review {
    // manager review specific
}

public final class PeerReview extends Review {
    // peer review specific
}

// This would NOT compile - not permitted
// public class CustomReview extends Review {}
```

**Real PerformanceTrack example:**

```java
public sealed interface Notification 
    permits EmailNotification, SMSNotification, InAppNotification {
    void send(String message);
}

public final class EmailNotification implements Notification {
    public void send(String message) {
        // send email
    }
}

public final class SMSNotification implements Notification {
    public void send(String message) {
        // send SMS
    }
}

public final class InAppNotification implements Notification {
    public void send(String message) {
        // show in app
    }
}
```

**Works great with pattern matching (Java 17+):**

```java
public String formatNotification(Notification notification) {
    return switch (notification) {
        case EmailNotification email -> "Email: " + email.getRecipient();
        case SMSNotification sms -> "SMS: " + sms.getPhoneNumber();
        case InAppNotification app -> "App: " + app.getUserId();
    };
    // No default needed - compiler knows all possible types!
}
```

**Permitted subclasses must be:**
- `final` (can't be extended further)
- `sealed` (restrict further)
- `non-sealed` (open for extension)

**Why use sealed classes?**
- Better domain modeling
- Exhaustive pattern matching
- Clear inheritance hierarchy
- Prevent unwanted extensions

**Example domain model for review types:**

```java
public sealed interface Goal
    permits IndividualGoal, TeamGoal, OrganizationalGoal {
    String getDescription();
    LocalDate getDeadline();
}

public final class IndividualGoal implements Goal {
    private String assignedTo;
    // ...
}

public final class TeamGoal implements Goal {
    private List<String> teamMembers;
    // ...
}

public final class OrganizationalGoal implements Goal {
    private List<String> departments;
    // ...
}
```

---

## PATTERN MATCHING

### Q94: What is Pattern Matching for instanceof (Java 16)?
Pattern matching eliminates the need to cast after an `instanceof` check. The cast happens automatically.

**Old way:**
```java
public String getEmployeeInfo(Object obj) {
    if (obj instanceof Employee) {
        Employee emp = (Employee) obj;  // Manual cast
        return emp.getName() + " - " + emp.getDepartment();
    }
    return "Unknown";
}
```

**New way (Java 16+):**
```java
public String getEmployeeInfo(Object obj) {
    if (obj instanceof Employee emp) {  // Pattern variable
        return emp.getName() + " - " + emp.getDepartment();
    }
    return "Unknown";
}
```

**Real PerformanceTrack usage:**

```java
public void processNotification(Object notification) {
    if (notification instanceof EmailNotification email) {
        emailService.send(email.getRecipient(), email.getSubject(), email.getBody());
    } else if (notification instanceof SMSNotification sms) {
        smsService.send(sms.getPhoneNumber(), sms.getMessage());
    } else if (notification instanceof InAppNotification app) {
        inAppService.show(app.getUserId(), app.getAlert());
    }
}

public double calculateBonus(Object evaluation) {
    if (evaluation instanceof ManagerReview review && review.getScore() >= 4) {
        return review.getScore() * 1000;  // Can use review immediately
    } else if (evaluation instanceof PeerReview review && review.getAverageScore() >= 4.5) {
        return review.getAverageScore() * 800;
    }
    return 0;
}
```

**Pattern variable scope:**
```java
if (obj instanceof Employee emp) {
    System.out.println(emp.getName());  // emp is in scope
}
// emp is NOT in scope here

// Works with && (pattern variable in scope for right side)
if (obj instanceof Employee emp && emp.getDepartment().equals("Engineering")) {
    // emp is accessible here
}

// Does NOT work with ||
if (obj instanceof Employee emp || someOtherCondition) {
    // emp is NOT accessible here
}
```

---

### Q95: What is Pattern Matching for switch (Java 17-21)?
Switch expressions now support patterns, not just constants. You can match on types, conditions, and even deconstruct records.

**Basic type patterns:**

```java
public String formatValue(Object obj) {
    return switch (obj) {
        case Integer i -> "Integer: " + i;
        case String s -> "String: " + s;
        case Employee e -> "Employee: " + e.getName();
        case null -> "Null value";
        default -> "Unknown type";
    };
}
```

**With guards (conditions):**

```java
public String evaluatePerformance(Review review) {
    return switch (review) {
        case Review r when r.getScore() >= 4.5 -> "Exceptional";
        case Review r when r.getScore() >= 3.5 -> "Good";
        case Review r when r.getScore() >= 2.5 -> "Average";
        case Review r -> "Needs Improvement";
    };
}
```

**Real PerformanceTrack scenarios:**

```java
public double calculateBonus(Object evaluation) {
    return switch (evaluation) {
        case ManagerReview mr when mr.getScore() >= 4 -> 
            mr.getScore() * 1000.0;
        case PeerReview pr when pr.getAverageScore() >= 4.5 -> 
            pr.getAverageScore() * 800.0;
        case SelfReview sr when sr.getScore() >= 4 -> 
            sr.getScore() * 500.0;
        case null -> 0.0;
        default -> 0.0;
    };
}

public String getReviewStatus(Review review) {
    return switch (review) {
        case Review r when r.getStatus().equals("DRAFT") -> 
            "In Progress";
        case Review r when r.getStatus().equals("SUBMITTED") && 
                           LocalDate.now().isBefore(r.getDeadline()) -> 
            "Pending Review";
        case Review r when r.getStatus().equals("SUBMITTED") && 
                           LocalDate.now().isAfter(r.getDeadline()) -> 
            "Overdue";
        case Review r when r.getStatus().equals("APPROVED") -> 
            "Completed";
        default -> "Unknown";
    };
}
```

**Pattern matching with records (Java 21):**

```java
record Point(int x, int y) {}

public String describePoint(Point point) {
    return switch (point) {
        case Point(0, 0) -> "Origin";
        case Point(int x, 0) -> "On X-axis at " + x;
        case Point(0, int y) -> "On Y-axis at " + y;
        case Point(int x, int y) -> "Point at (" + x + ", " + y + ")";
    };
}

// In your Analytics module
record PerformanceMetric(String department, double avgScore, int count) {}

public String analyzeMetric(PerformanceMetric metric) {
    return switch (metric) {
        case PerformanceMetric(var dept, double avg, int cnt) 
            when avg >= 4.5 && cnt >= 10 -> 
            dept + " is performing exceptionally well";
        case PerformanceMetric(var dept, double avg, int cnt) 
            when avg < 3.0 && cnt >= 5 -> 
            dept + " needs immediate attention";
        case PerformanceMetric(var dept, _, int cnt) 
            when cnt < 5 -> 
            dept + " has insufficient data";
        default -> 
            "Normal performance";
    };
}
```

**Benefits:**
- More expressive than if-else chains
- Exhaustiveness checking by compiler
- Can match on types, values, and patterns
- Guards add conditional logic

---

## STREAM API ENHANCEMENTS

### Q96: What new Stream methods were added in Java 9-16?
**Java 9 additions:**

**1. takeWhile()** - Takes elements while condition is true, then stops

```java
List<Review> reviews = getAllReviews();  // sorted by date, newest first

// Get reviews until we hit one older than 30 days
List<Review> recentReviews = reviews.stream()
    .takeWhile(r -> r.getCreatedDate().isAfter(LocalDate.now().minusDays(30)))
    .collect(Collectors.toList());
```

**2. dropWhile()** - Opposite of takeWhile, drops elements while condition is true

```java
// Skip all pending reviews, then process rest
List<Review> processableReviews = reviews.stream()
    .dropWhile(r -> r.getStatus().equals("PENDING"))
    .collect(Collectors.toList());
```

**3. ofNullable()** - Creates stream from single element (or empty if null)

```java
// Before
Stream<Employee> empStream = manager != null ? 
    Stream.of(manager) : Stream.empty();

// After
Stream<Employee> empStream = Stream.ofNullable(manager);

// Useful in flatMap
List<Employee> allManagers = employees.stream()
    .flatMap(emp -> Stream.ofNullable(emp.getManager()))
    .distinct()
    .collect(Collectors.toList());
```

**Java 12 addition:**

**4. teeing()** - Applies two collectors and combines results

```java
record ScoreStats(Double average, Long count) {}

ScoreStats stats = reviews.stream()
    .collect(Collectors.teeing(
        Collectors.averagingDouble(Review::getScore),  // First collector
        Collectors.counting(),                          // Second collector
        (avg, count) -> new ScoreStats(avg, count)     // Combiner
    ));

System.out.println("Average: " + stats.average + ", Count: " + stats.count);
```

**Java 16 addition:**

**5. toList()** - Simpler way to collect to list

```java
// Old way
List<Employee> emps = employees.stream()
    .filter(e -> e.getRole().equals("MANAGER"))
    .collect(Collectors.toList());

// New way
List<Employee> emps = employees.stream()
    .filter(e -> e.getRole().equals("MANAGER"))
    .toList();  // Returns immutable list
```

**Real PerformanceTrack example combining multiple:**

```java
public DepartmentAnalytics analyzeDepartment(String dept, LocalDate cutoffDate) {
    List<Review> deptReviews = reviews.stream()
        .filter(r -> r.getEmployee().getDepartment().equals(dept))
        .sorted(Comparator.comparing(Review::getCreatedDate).reversed())
        .takeWhile(r -> r.getCreatedDate().isAfter(cutoffDate))  // Java 9
        .toList();  // Java 16
    
    var stats = deptReviews.stream()
        .collect(Collectors.teeing(  // Java 12
            Collectors.averagingDouble(Review::getScore),
            Collectors.counting(),
            (avg, count) -> new DepartmentStats(dept, avg, count)
        ));
    
    return new DepartmentAnalytics(dept, stats, deptReviews);
}
```

---

### Q97: What is the difference between Collection.removeIf() and Stream.filter()?
Both can remove elements based on condition, but they work differently:

**removeIf()** - Modifies the original collection. Mutation.

**filter()** - Creates new stream without modifying original. Immutable operation.

**Examples:**

```java
List<Review> reviews = new ArrayList<>(getAllReviews());

// removeIf - MODIFIES original list
reviews.removeIf(r -> r.getStatus().equals("DRAFT"));
// reviews list now has fewer elements

// filter - Original unchanged, returns new list
List<Review> completedReviews = reviews.stream()
    .filter(r -> r.getStatus().equals("COMPLETED"))
    .collect(Collectors.toList());
// reviews list still has all elements
```

**Real PerformanceTrack scenario:**

```java
// Scenario 1: Clean up old draft reviews (mutation is fine)
public void cleanupDrafts() {
    List<Review> reviews = reviewRepository.findByStatus("DRAFT");
    reviews.removeIf(r -> 
        r.getCreatedDate().isBefore(LocalDate.now().minusDays(30))
    );
    reviewRepository.deleteAll(reviews);
}

// Scenario 2: Get active employees for report (need original list intact)
public List<Employee> getActiveEmployees(List<Employee> allEmployees) {
    return allEmployees.stream()
        .filter(emp -> emp.getStatus().equals("ACTIVE"))
        .filter(emp -> emp.getHireDate().isBefore(LocalDate.now().minusMonths(3)))
        .collect(Collectors.toList());
    // allEmployees unchanged - can use it again
}
```

**Key differences:**

| removeIf() | filter() |
|---|---|
| Mutates original | Keeps original unchanged |
| Returns boolean (success/fail) | Returns new stream |
| Works only on mutable collections | Works on any stream |
| More efficient for deletion | More flexible for transformations |

**When to use which:**

Use `removeIf()` when:
- You want to modify the collection in place
- You're done with the original data
- Performance matters (no new collection created)

Use `filter()` when:
- You need the original collection later
- You're chaining multiple operations
- You want immutable/functional style
- Working with immutable collections

---

## STRING METHODS (JAVA 11-15)

### Q98: What new String methods were added in Java 11?
Java 11 added several useful String methods:

**1. isBlank()** - Checks if string is empty or contains only whitespace

```java
String comment = "   ";
comment.isEmpty();   // false (has spaces)
comment.isBlank();   // true (only whitespace)

// In PerformanceTrack validation
public boolean isValidComment(String comment) {
    return comment != null && !comment.isBlank();
}
```

**2. lines()** - Returns stream of lines

```java
String multiLineComment = """
    Performance was good.
    Needs improvement in communication.
    Excellent technical skills.
    """;

long lineCount = multiLineComment.lines().count();  // 3

List<String> points = multiLineComment.lines()
    .map(String::trim)
    .filter(line -> !line.isEmpty())
    .collect(Collectors.toList());
```

**3. strip(), stripLeading(), stripTrailing()** - Better than trim()

```java
String feedback = "  Great work!  ";

feedback.trim();           // "Great work!" (removes ASCII whitespace)
feedback.strip();          // "Great work!" (removes Unicode whitespace)
feedback.stripLeading();   // "Great work!  "
feedback.stripTrailing();  // "  Great work!"

// Difference: strip() handles Unicode spaces that trim() misses
String unicode = "\u2000Hello\u2000";  // Unicode space
unicode.trim();   // Still has spaces
unicode.strip();  // Properly cleaned
```

**4. repeat()** - Repeat string n times

```java
String separator = "-".repeat(50);
System.out.println(separator);  // --------------------------------------------------

// In report generation
String stars = "*".repeat(review.getScore());
System.out.println("Rating: " + stars);  // Rating: ****
```

**Real usage in PerformanceTrack:**

```java
public class ReviewValidator {
    
    public List<String> validateReview(Review review) {
        List<String> errors = new ArrayList<>();
        
        if (review.getComments().isBlank()) {
            errors.add("Comments cannot be blank");
        }
        
        if (review.getGoals().isBlank()) {
            errors.add("Goals cannot be blank");
        }
        
        // Check if comments have multiple meaningful points
        long meaningfulLines = review.getComments()
            .lines()
            .map(String::strip)
            .filter(line -> line.length() > 10)
            .count();
            
        if (meaningfulLines < 2) {
            errors.add("Please provide at least 2 detailed points");
        }
        
        return errors;
    }
}
```

---

### Q99: What is String.formatted() and String.transform()?
These were added in Java 13-15 to make string manipulation more functional.

**formatted() (Java 13+)** - Instance method version of String.format()

```java
// Old way
String message = String.format("Employee %s scored %d", name, score);

// New way
String message = "Employee %s scored %d".formatted(name, score);

// In PerformanceTrack
String notification = """
    Dear %s,
    Your review score is %d/5.
    Manager: %s
    """.formatted(employee.getName(), review.getScore(), manager.getName());
```

**transform() (Java 12+)** - Apply a function to the string

```java
String result = "hello".transform(String::toUpperCase);  // "HELLO"

// Chaining transformations
String processed = rawInput
    .transform(String::strip)
    .transform(String::toUpperCase)
    .transform(s -> s.replace("_", " "));

// In PerformanceTrack - sanitize and format employee names
Function<String, String> sanitizeName = name -> name
    .strip()
    .toLowerCase()
    .transform(s -> s.substring(0, 1).toUpperCase() + s.substring(1));

String cleanName = employeeInput.transform(sanitizeName);
```

**Real scenario in report generation:**

```java
public class ReportFormatter {
    
    private static final Function<String, String> formatDepartment = dept -> dept
        .strip()
        .toUpperCase()
        .replace("_", " ");
    
    private static final Function<String, String> maskEmail = email -> {
        int atIndex = email.indexOf("@");
        return email.substring(0, 2) + "***" + email.substring(atIndex);
    };
    
    public String generateEmployeeReport(Employee emp) {
        return """
            Employee Report
            ---------------
            Name: %s
            Department: %s
            Email: %s
            Status: %s
            """.formatted(
                emp.getName().transform(String::toUpperCase),
                emp.getDepartment().transform(formatDepartment),
                emp.getEmail().transform(maskEmail),
                emp.getStatus()
            );
    }
}
```

**Why transform() is useful:**
- Makes code more functional and composable
- Can be chained easily
- Better than multiple intermediate variables
- Can pass around string transformation pipelines

---

## HTTP CLIENT API (JAVA 11)

### Q100: What is the new HTTP Client API introduced in Java 11?
Java 11 introduced a modern, easy-to-use HTTP client to replace the old `HttpURLConnection`. It supports HTTP/2, WebSocket, and asynchronous requests.

**Basic synchronous request:**

```java
HttpClient client = HttpClient.newHttpClient();

HttpRequest request = HttpRequest.newBuilder()
    .uri(URI.create("https://api.example.com/employees"))
    .GET()
    .build();

HttpResponse<String> response = client.send(request, 
    HttpResponse.BodyHandlers.ofString());

System.out.println(response.body());
```

**POST request with JSON:**

```java
String json = """
    {
        "employeeId": 123,
        "score": 4,
        "comments": "Great performance"
    }
    """;

HttpRequest request = HttpRequest.newBuilder()
    .uri(URI.create("https://api.example.com/reviews"))
    .header("Content-Type", "application/json")
    .POST(HttpRequest.BodyPublishers.ofString(json))
    .build();

HttpResponse<String> response = client.send(request, 
    HttpResponse.BodyHandlers.ofString());
```

**Real PerformanceTrack usage - integrating with external HR system:**

```java
public class HRSystemIntegration {
    
    private final HttpClient httpClient = HttpClient.newBuilder()
        .version(HttpClient.Version.HTTP_2)
        .connectTimeout(Duration.ofSeconds(10))
        .build();
    
    public Employee fetchEmployeeFromHR(Long employeeId) throws Exception {
        HttpRequest request = HttpRequest.newBuilder()
            .uri(URI.create("https://hr-api.company.com/employees/" + employeeId))
            .header("Authorization", "Bearer " + getToken())
            .GET()
            .build();
        
        HttpResponse<String> response = httpClient.send(request,
            HttpResponse.BodyHandlers.ofString());
        
        if (response.statusCode() == 200) {
            return parseEmployee(response.body());
        } else {
            throw new RuntimeException("Failed to fetch employee: " + response.statusCode());
        }
    }
    
    public void syncReviewToHR(Review review) throws Exception {
        String json = convertReviewToJson(review);
        
        HttpRequest request = HttpRequest.newBuilder()
            .uri(URI.create("https://hr-api.company.com/reviews"))
            .header("Content-Type", "application/json")
            .header("Authorization", "Bearer " + getToken())
            .POST(HttpRequest.BodyPublishers.ofString(json))
            .build();
        
        HttpResponse<String> response = httpClient.send(request,
            HttpResponse.BodyHandlers.ofString());
        
        if (response.statusCode() != 201) {
            logger.error("Failed to sync review: " + response.body());
        }
    }
}
```

---

### Q101: How do you make asynchronous requests with HTTP Client?
Use `sendAsync()` which returns a `CompletableFuture`.

**Async GET request:**

```java
HttpClient client = HttpClient.newHttpClient();

HttpRequest request = HttpRequest.newBuilder()
    .uri(URI.create("https://api.example.com/employees"))
    .build();

CompletableFuture<HttpResponse<String>> futureResponse = 
    client.sendAsync(request, HttpResponse.BodyHandlers.ofString());

// Non-blocking - continues execution
futureResponse.thenAccept(response -> {
    System.out.println("Status: " + response.statusCode());
    System.out.println("Body: " + response.body());
});

// Do other work while request is in progress
System.out.println("Request sent, doing other work...");
```

**Multiple parallel requests:**

```java
public class AnalyticsService {
    
    private final HttpClient httpClient = HttpClient.newHttpClient();
    
    public CompletableFuture<DepartmentReport> generateReport(String department) {
        
        // Fetch data from multiple sources in parallel
        CompletableFuture<String> employeeData = fetchEmployeesAsync(department);
        CompletableFuture<String> reviewData = fetchReviewsAsync(department);
        CompletableFuture<String> goalData = fetchGoalsAsync(department);
        
        // Combine all results
        return CompletableFuture.allOf(employeeData, reviewData, goalData)
            .thenApply(v -> {
                String employees = employeeData.join();
                String reviews = reviewData.join();
                String goals = goalData.join();
                return buildReport(employees, reviews, goals);
            });
    }
    
    private CompletableFuture<String> fetchEmployeesAsync(String dept) {
        HttpRequest request = HttpRequest.newBuilder()
            .uri(URI.create("https://api.company.com/employees?dept=" + dept))
            .build();
        
        return httpClient.sendAsync(request, HttpResponse.BodyHandlers.ofString())
            .thenApply(HttpResponse::body);
    }
    
    private CompletableFuture<String> fetchReviewsAsync(String dept) {
        HttpRequest request = HttpRequest.newBuilder()
            .uri(URI.create("https://api.company.com/reviews?dept=" + dept))
            .build();
        
        return httpClient.sendAsync(request, HttpResponse.BodyHandlers.ofString())
            .thenApply(HttpResponse::body);
    }
}
```

**With timeout and error handling:**

```java
public CompletableFuture<Employee> fetchEmployeeSafely(Long id) {
    HttpRequest request = HttpRequest.newBuilder()
        .uri(URI.create("https://api.company.com/employees/" + id))
        .timeout(Duration.ofSeconds(5))  // Request timeout
        .build();
    
    return httpClient.sendAsync(request, HttpResponse.BodyHandlers.ofString())
        .thenApply(response -> {
            if (response.statusCode() == 200) {
                return parseEmployee(response.body());
            } else {
                throw new RuntimeException("Failed: " + response.statusCode());
            }
        })
        .exceptionally(ex -> {
            logger.error("Error fetching employee: " + ex.getMessage());
            return getDefaultEmployee();
        })
        .orTimeout(10, TimeUnit.SECONDS)  // Overall timeout
        .completeOnTimeout(getDefaultEmployee(), 10, TimeUnit.SECONDS);
}
```

**Benefits of async:**
- Don't block waiting for network response
- Can make multiple requests in parallel
- Better resource utilization
- Improved application responsiveness

---

## PRIVATE METHODS IN INTERFACES (JAVA 9)

### Q102: What are private methods in interfaces and why use them?
Java 9 allows private methods in interfaces to share code between default methods without exposing helper methods.

**Before Java 9 - code duplication:**

```java
public interface ReviewProcessor {
    
    default void processManagerReview(Review review) {
        validateReview(review);  // Can't call - no private methods!
        // Duplicate validation logic here
        if (review.getScore() < 1 || review.getScore() > 5) {
            throw new IllegalArgumentException("Invalid score");
        }
        // process manager review
    }
    
    default void processPeerReview(Review review) {
        // Duplicate validation logic again!
        if (review.getScore() < 1 || review.getScore() > 5) {
            throw new IllegalArgumentException("Invalid score");
        }
        // process peer review
    }
}
```

**Java 9+ - with private methods:**

```java
public interface ReviewProcessor {
    
    default void processManagerReview(Review review) {
        validateReview(review);  // Call private method
        applyManagerWeightage(review);
        saveReview(review);
    }
    
    default void processPeerReview(Review review) {
        validateReview(review);  // Reuse same validation
        applyPeerWeightage(review);
        saveReview(review);
    }
    
    default void processSelfReview(Review review) {
        validateReview(review);  // Reuse again
        applySelfWeightage(review);
        saveReview(review);
    }
    
    // Private method - shared logic
    private void validateReview(Review review) {
        if (review == null) {
            throw new IllegalArgumentException("Review cannot be null");
        }
        if (review.getScore() < 1 || review.getScore() > 5) {
            throw new IllegalArgumentException("Score must be between 1 and 5");
        }
        if (review.getComments().isBlank()) {
            throw new IllegalArgumentException("Comments are required");
        }
    }
    
    // Private method for saving
    private void saveReview(Review review) {
        review.setProcessedDate(LocalDateTime.now());
        // save logic
    }
}
```

**Private static methods in interfaces:**

```java
public interface ScoreCalculator {
    
    default double calculateWeightedScore(List<Review> reviews) {
        double managerScore = getAverageScore(reviews, "MANAGER");
        double peerScore = getAverageScore(reviews, "PEER");
        double selfScore = getAverageScore(reviews, "SELF");
        
        return calculateWeighted(managerScore, peerScore, selfScore);
    }
    
    // Private static helper
    private static double getAverageScore(List<Review> reviews, String type) {
        return reviews.stream()
            .filter(r -> r.getType().equals(type))
            .mapToDouble(Review::getScore)
            .average()
            .orElse(0.0);
    }
    
    // Private static calculator
    private static double calculateWeighted(double manager, double peer, double self) {
        return (manager * 0.5) + (peer * 0.3) + (self * 0.2);
    }
}
```

**Real PerformanceTrack usage:**

```java
public interface NotificationService {
    
    default void notifyReviewSubmission(Review review) {
        String message = buildMessage("submitted", review);
        logNotification("SUBMISSION", review);
        send(review.getEmployee().getEmail(), message);
    }
    
    default void notifyReviewApproval(Review review) {
        String message = buildMessage("approved", review);
        logNotification("APPROVAL", review);
        send(review.getEmployee().getEmail(), message);
    }
    
    default void notifyReviewRejection(Review review) {
        String message = buildMessage("rejected", review);
        logNotification("REJECTION", review);
        send(review.getEmployee().getEmail(), message);
    }
    
    // Private method - message builder
    private String buildMessage(String action, Review review) {
        return """
            Dear %s,
            Your review has been %s.
            Score: %d/5
            Period: %s
            """.formatted(
                review.getEmployee().getName(),
                action,
                review.getScore(),
                review.getPeriod()
            );
    }
    
    // Private method - logging
    private void logNotification(String type, Review review) {
        System.out.println("[" + LocalDateTime.now() + "] " + 
            type + " notification sent for review: " + review.getId());
    }
    
    // Abstract method - must be implemented
    void send(String email, String message);
}
```

**Benefits:**
- Reduce code duplication in default methods
- Keep interface clean (helper methods not exposed)
- Better encapsulation
- Makes interfaces more maintainable

---

## SWITCH EXPRESSIONS (JAVA 12-14)

### Q103: What are Switch Expressions and how are they different from Switch Statements?
Switch expressions (finalized in Java 14) return a value and use arrow syntax, making code cleaner and less error-prone.

**Old switch statement:**

```java
String rating;
switch (score) {
    case 5:
        rating = "Exceptional";
        break;
    case 4:
        rating = "Exceeds Expectations";
        break;
    case 3:
        rating = "Meets Expectations";
        break;
    case 2:
        rating = "Needs Improvement";
        break;
    case 1:
        rating = "Unsatisfactory";
        break;
    default:
        rating = "Invalid";
}
```

**New switch expression:**

```java
String rating = switch (score) {
    case 5 -> "Exceptional";
    case 4 -> "Exceeds Expectations";
    case 3 -> "Meets Expectations";
    case 2 -> "Needs Improvement";
    case 1 -> "Unsatisfactory";
    default -> "Invalid";
};
```

**Key differences:**

1. **Returns a value** - can assign directly
2. **No fall-through** - no need for break
3. **Arrow syntax** - cleaner `->` instead of `:`
4. **Exhaustiveness** - compiler ensures all cases covered

**Multiple values per case:**

```java
String category = switch (score) {
    case 5, 4 -> "High Performer";
    case 3 -> "Average Performer";
    case 2, 1 -> "Needs Development";
    default -> "Invalid";
};
```

**With code blocks using yield:**

```java
String feedback = switch (score) {
    case 5 -> {
        auditLog.log("Exceptional performance recorded");
        yield "Outstanding work! Keep it up!";
    }
    case 4 -> {
        auditLog.log("Good performance recorded");
        yield "Great job! Minor improvements possible.";
    }
    case 3 -> "Satisfactory performance.";
    case 2, 1 -> {
        auditLog.log("Poor performance recorded");
        yield "Significant improvement required.";
    }
    default -> throw new IllegalArgumentException("Invalid score");
};
```

**Real PerformanceTrack usage:**

```java
public class ReviewService {
    
    public double calculateBonus(Review review) {
        return switch (review.getScore()) {
            case 5 -> review.getEmployee().getSalary() * 0.15;
            case 4 -> review.getEmployee().getSalary() * 0.10;
            case 3 -> review.getEmployee().getSalary() * 0.05;
            default -> 0.0;
        };
    }
    
    public String getReviewStatus(Review review) {
        return switch (review.getStatus()) {
            case "DRAFT" -> "Still in progress";
            case "SUBMITTED" -> {
                if (LocalDate.now().isAfter(review.getDeadline())) {
                    yield "Overdue - pending review";
                }
                yield "Under review";
            }
            case "APPROVED" -> "Completed";
            case "REJECTED" -> "Rejected - needs revision";
            default -> "Unknown status";
        };
    }
    
    public int getNotificationPriority(String eventType) {
        return switch (eventType) {
            case "REVIEW_OVERDUE", "REVIEW_REJECTED" -> 1;  // High priority
            case "REVIEW_SUBMITTED", "REVIEW_REMINDER" -> 2;  // Medium
            case "REVIEW_APPROVED", "GOAL_COMPLETED" -> 3;  // Low
            default -> 4;  // Lowest
        };
    }
}
```

**Traditional switch vs Switch expression:**

```java
// Traditional - statement (no return value)
switch (status) {
    case "APPROVED":
        processApproval();
        break;
    case "REJECTED":
        processRejection();
        break;
}

// Expression - returns value
String result = switch (status) {
    case "APPROVED" -> processApproval();
    case "REJECTED" -> processRejection();
    default -> processDefault();
};
```

---

### Q104: Can you use switch expressions with Strings and Enums?
Yes! Switch expressions work with all types that traditional switch supports: int, String, enum, and wrapper classes.

**With Strings:**

```java
public class ReviewValidator {
    
    public boolean isValidStatus(String status) {
        return switch (status.toUpperCase()) {
            case "DRAFT", "SUBMITTED", "IN_REVIEW", "APPROVED", "REJECTED" -> true;
            default -> false;
        };
    }
    
    public String getStatusMessage(String status) {
        return switch (status) {
            case "DRAFT" -> "Review is being drafted";
            case "SUBMITTED" -> "Review submitted for approval";
            case "IN_REVIEW" -> "Review is under evaluation";
            case "APPROVED" -> "Review has been approved";
            case "REJECTED" -> "Review needs revision";
            default -> "Unknown status: " + status;
        };
    }
}
```

**With Enums (best practice):**

```java
public enum ReviewStatus {
    DRAFT, SUBMITTED, IN_REVIEW, APPROVED, REJECTED
}

public enum PerformanceLevel {
    EXCEPTIONAL, GOOD, AVERAGE, POOR
}

public class ReviewService {
    
    public String getStatusColor(ReviewStatus status) {
        return switch (status) {
            case DRAFT -> "gray";
            case SUBMITTED -> "blue";
            case IN_REVIEW -> "yellow";
            case APPROVED -> "green";
            case REJECTED -> "red";
        };  // No default needed - all enum values covered!
    }
    
    public double getBonusMultiplier(PerformanceLevel level) {
        return switch (level) {
            case EXCEPTIONAL -> 0.15;
            case GOOD -> 0.10;
            case AVERAGE -> 0.05;
            case POOR -> 0.0;
        };
    }
    
    public boolean requiresManagerAction(ReviewStatus status) {
        return switch (status) {
            case SUBMITTED, IN_REVIEW -> true;
            case DRAFT, APPROVED, REJECTED -> false;
        };
    }
}
```

**Combining enums with pattern matching (Java 21+):**

```java
sealed interface Review permits SelfReview, ManagerReview, PeerReview {}
final class SelfReview implements Review { int score; }
final class ManagerReview implements Review { int score; }
final class PeerReview implements Review { int score; }

public double calculateWeight(Review review) {
    return switch (review) {
        case SelfReview self -> 0.2;
        case ManagerReview manager -> 0.5;
        case PeerReview peer -> 0.3;
    };
}
```

**Real PerformanceTrack scenario with complex logic:**

```java
public enum Department {
    ENGINEERING, SALES, MARKETING, HR, FINANCE
}

public class BonusCalculator {
    
    public double calculateDepartmentBonus(Department dept, double baseBonus) {
        return switch (dept) {
            case ENGINEERING -> {
                // Complex calculation for engineering
                double techBonus = baseBonus * 1.2;
                double innovationBonus = calculateInnovationBonus();
                yield techBonus + innovationBonus;
            }
            case SALES -> {
                // Sales gets commission-based bonus
                double commission = calculateCommission();
                yield baseBonus + commission;
            }
            case MARKETING, HR -> baseBonus * 1.1;  // 10% boost
            case FINANCE -> baseBonus;  // Standard bonus
        };
    }
    
    public List<String> getRequiredDocuments(ReviewStatus status) {
        return switch (status) {
            case DRAFT -> List.of();
            case SUBMITTED -> List.of("self-assessment", "goals");
            case IN_REVIEW -> List.of("self-assessment", "goals", "manager-feedback");
            case APPROVED -> List.of("self-assessment", "goals", "manager-feedback", "approval-letter");
            case REJECTED -> List.of("rejection-reason", "improvement-plan");
        };
    }
}
```

**Benefits with enums:**
- Compiler checks all cases (exhaustiveness)
- No default needed if all enum values covered
- Refactoring safe - adding new enum value shows errors
- Type-safe compared to Strings

---

## HELPFUL NULLPOINTEREXCEPTIONS (JAVA 14)

### Q105: What are Helpful NullPointerExceptions?
Before Java 14, NullPointerExceptions just told you the line number but not which variable was null. Java 14 added detailed messages showing exactly what was null.

**Before Java 14:**

```java
String managerName = employee.getManager().getName();
// Exception: NullPointerException at line 42
// You don't know if employee is null or getManager() returned null
```

**Java 14+:**

```java
String managerName = employee.getManager().getName();
// Exception: Cannot invoke "Employee.getName()" because the return value of "Employee.getManager()" is null
// Now you know exactly: getManager() returned null!
```

**Real PerformanceTrack examples:**

```java
// Example 1: Nested calls
double avgScore = review.getEmployee().getDepartment().getAverageScore();

// Old error:
// NullPointerException at line 45

// New error:
// Cannot invoke "Department.getAverageScore()" because the return value 
// of "Employee.getDepartment()" is null
// Now you know getDepartment() returned null!
```

```java
// Example 2: Array access
String firstComment = reviews[0].getComments().split(",")[0];

// Old error:
// NullPointerException at line 67

// New error:
// Cannot read the array length because "reviews" is null
// OR
// Cannot invoke "String.split(String)" because the return value 
// of "Review.getComments()" is null
```

```java
// Example 3: Method chain
List<String> departments = employees.stream()
    .map(Employee::getDepartment)
    .map(Department::getName)
    .collect(Collectors.toList());

// New detailed error shows which employee had null department
```

**How to enable (Java 14+):**

By default, it's enabled. If not:
```bash
java -XX:+ShowCodeDetailsInExceptionMessages MyApp
```

**Real debugging scenario:**

```java
public class AnalyticsService {
    
    public double calculateTeamAverage(Long teamId) {
        // Complex chain
        return teamRepository.findById(teamId)
            .getManager()
            .getReviews()
            .stream()
            .mapToDouble(Review::getScore)
            .average()
            .orElse(0.0);
    }
}

// Before Java 14 error:
// NullPointerException at line 123
// Could be: teamRepository null? findById returned null? getManager returned null?

// Java 14+ error:
// Cannot invoke "Manager.getReviews()" because the return value 
// of "Team.getManager()" is null
// Clearly: The team exists but has no manager assigned!
```

**Benefits:**
- Faster debugging - know exactly what's null
- No more print statements to find null values
- Better error messages in logs
- Helps junior developers understand errors quickly

**Best practice:** Even with helpful NPEs, use Optional and null checks:

```java
// Better code - prevents NPE
public double calculateTeamAverage(Long teamId) {
    return teamRepository.findById(teamId)
        .flatMap(Team::getManager)
        .map(Manager::getReviews)
        .orElse(Collections.emptyList())
        .stream()
        .mapToDouble(Review::getScore)
        .average()
        .orElse(0.0);
}
```

---

## INSTANCEOF PATTERN MATCHING ENHANCEMENTS

### Q106: How has instanceof evolved from Java 16 to Java 21?
**Java 16: Basic pattern matching**

```java
if (obj instanceof Employee emp) {
    System.out.println(emp.getName());  // No cast needed
}
```

**Java 17: Pattern matching in switch**

```java
String result = switch (obj) {
    case Employee emp -> "Employee: " + emp.getName();
    case Manager mgr -> "Manager: " + mgr.getTeamSize();
    case null -> "Null object";
    default -> "Unknown";
};
```

**Java 21: Record patterns (deconstruction)**

```java
record EmployeeRecord(String name, String dept, int score) {}

// Deconstruct in instanceof
if (obj instanceof EmployeeRecord(String name, String dept, int score)) {
    System.out.println("Name: " + name + ", Dept: " + dept);
}

// Deconstruct in switch
String info = switch (obj) {
    case EmployeeRecord(var name, var dept, var score) when score > 4 
        -> name + " is a high performer in " + dept;
    case EmployeeRecord(var name, var dept, var score) 
        -> name + " from " + dept;
    case null -> "No data";
    default -> "Unknown";
};
```

**Real PerformanceTrack evolution:**

```java
// Java 16 way
public void processReview(Object review) {
    if (review instanceof ManagerReview mr) {
        System.out.println("Manager review score: " + mr.getScore());
    } else if (review instanceof PeerReview pr) {
        System.out.println("Peer review score: " + pr.getScore());
    }
}

// Java 17 way (cleaner with switch)
public String processReview(Object review) {
    return switch (review) {
        case ManagerReview mr -> "Manager review: " + mr.getScore();
        case PeerReview pr -> "Peer review: " + pr.getScore();
        case SelfReview sr -> "Self review: " + sr.getScore();
        case null -> "No review";
        default -> "Unknown review type";
    };
}

// Java 21 way (with record patterns)
record ReviewData(String type, int score, String comments) {}

public void analyzeReview(Object obj) {
    switch (obj) {
        case ReviewData(var type, int score, var comments) when score >= 4 -> {
            System.out.println("Excellent " + type + " review");
            System.out.println("Comments: " + comments);
        }
        case ReviewData(var type, int score, _) when score < 3 -> {
            System.out.println("Poor " + type + " review");
            // Ignoring comments with _
        }
        case ReviewData(var type, int score, var comments) -> {
            System.out.println("Average " + type + " review");
        }
        default -> System.out.println("Not a review");
    }
}
```

**Nested pattern matching (Java 21):**

```java
record Employee(String name, Address address) {}
record Address(String city, String country) {}

public String getLocation(Object obj) {
    return switch (obj) {
        case Employee(var name, Address(var city, "USA")) 
            -> name + " is in " + city + ", USA";
        case Employee(var name, Address(var city, var country)) 
            -> name + " is in " + city + ", " + country;
        case null -> "No employee";
        default -> "Unknown";
    };
}
```

**Guard conditions with patterns:**

```java
public double calculateBonus(Object review) {
    return switch (review) {
        case ManagerReview(int score, var comments) when score >= 5 
            -> 5000.0;
        case ManagerReview(int score, var comments) when score >= 4 
            -> 3000.0;
        case PeerReview(int score) when score >= 4 
            -> 2000.0;
        case SelfReview sr when sr.isVerified() 
            -> 1000.0;
        default 
            -> 0.0;
    };
}
```

---

## VIRTUAL THREADS (JAVA 21)

### Q107: What are Virtual Threads and why were they introduced?
Virtual Threads (Project Loom) are lightweight threads managed by the JVM, not the OS. You can create millions of them without the overhead of traditional platform threads.

**Problem with platform threads:**

```java
// Platform thread - expensive (1 MB stack, OS managed)
Thread thread = new Thread(() -> {
    // Long running task
});
thread.start();

// Can only create ~few thousand before running out of memory
```

**Solution - Virtual Threads:**

```java
// Virtual thread - lightweight (few KB, JVM managed)
Thread vThread = Thread.startVirtualThread(() -> {
    // Long running task
});

// Can create MILLIONS without issues!
```

**Real PerformanceTrack usage - handling many concurrent users:**

```java
public class ReviewService {
    
    // Old way - thread pool with limited threads
    private final ExecutorService executor = Executors.newFixedThreadPool(100);
    
    public void processReviewsOld(List<Review> reviews) {
        reviews.forEach(review -> {
            executor.submit(() -> {
                // Process review (blocking I/O)
                fetchEmployeeData(review);
                calculateMetrics(review);
                saveToDatabase(review);
            });
        });
    }
    
    // New way - virtual threads (can handle thousands concurrently)
    public void processReviews(List<Review> reviews) {
        reviews.forEach(review -> {
            Thread.startVirtualThread(() -> {
                // Process review (blocking I/O is fine now!)
                fetchEmployeeData(review);
                calculateMetrics(review);
                saveToDatabase(review);
            });
        });
    }
}
```

**Using ExecutorService with virtual threads:**

```java
// Create virtual thread executor
ExecutorService executor = Executors.newVirtualThreadPerTaskExecutor();

// Submit thousands of tasks - no problem!
for (int i = 0; i < 100000; i++) {
    executor.submit(() -> {
        // Each task gets its own virtual thread
        processReview();
    });
}

executor.shutdown();
```

**Structured Concurrency (preview in Java 21):**

```java
public EmployeeReport generateReport(Long employeeId) throws Exception {
    try (var scope = new StructuredTaskScope.ShutdownOnFailure()) {
        
        // Launch multiple virtual threads
        Future<Employee> employeeFuture = scope.fork(
            () -> fetchEmployee(employeeId)
        );
        
        Future<List<Review>> reviewsFuture = scope.fork(
            () -> fetchReviews(employeeId)
        );
        
        Future<List<Goal>> goalsFuture = scope.fork(
            () -> fetchGoals(employeeId)
        );
        
        // Wait for all to complete
        scope.join();
        scope.throwIfFailed();
        
        // Get results
        Employee employee = employeeFuture.resultNow();
        List<Review> reviews = reviewsFuture.resultNow();
        List<Goal> goals = goalsFuture.resultNow();
        
        return buildReport(employee, reviews, goals);
    }
}
```

**Real API endpoint example:**

```java
@RestController
public class AnalyticsController {
    
    // Each request runs on a virtual thread
    public void configureVirtualThreads() {
        // Spring Boot 3.2+ configuration
        TomcatProtocolHandlerCustomizer<?> customizer = protocolHandler -> {
            protocolHandler.setExecutor(Executors.newVirtualThreadPerTaskExecutor());
        };
    }
    
    @GetMapping("/analytics/department/{dept}")
    public DepartmentReport getReport(@PathVariable String dept) {
        // Runs on virtual thread - can block without issues!
        List<Employee> employees = employeeRepository.findByDepartment(dept);
        List<Review> reviews = reviewRepository.findByDepartment(dept);
        
        // Blocking calls are fine
        return generateReport(employees, reviews);
    }
}
```

**Benefits:**
- Can handle millions of concurrent requests
- Blocking operations don't waste resources
- Simpler code (no async/await complexity)
- Better throughput for I/O-heavy applications

**When to use:**
- High-concurrency applications
- I/O-bound tasks (database, network, file operations)
- Microservices handling many requests

**When NOT to use:**
- CPU-bound tasks (use platform threads or parallel streams)
- Legacy libraries with ThreadLocal heavy use (may need adjustment)

---

## STREAM GATHER (JAVA 22-23)

### Q108: What is the Stream Gatherer API?
Gatherers (preview in Java 22) are custom intermediate stream operations. They're more powerful than map/filter and let you create complex transformations.

**Built-in gatherers:**

```java
// windowFixed - group elements into fixed-size windows
List<List<Review>> batches = reviews.stream()
    .gather(Gatherers.windowFixed(10))  // Batches of 10
    .toList();

// windowSliding - sliding windows
List<List<Integer>> windows = scores.stream()
    .gather(Gatherers.windowSliding(3))  // Moving average window
    .toList();

// fold - accumulate with intermediate results
List<Integer> runningTotal = scores.stream()
    .gather(Gatherers.fold(() -> 0, (sum, score) -> sum + score))
    .toList();
```

**Real PerformanceTrack usage:**

```java
public class AnalyticsService {
    
    // Process reviews in batches
    public void processBatchedReviews(Stream<Review> reviews) {
        reviews
            .gather(Gatherers.windowFixed(50))  // Process 50 at a time
            .forEach(batch -> {
                // Batch processing to database
                saveBatch(batch);
            });
    }
    
    // Calculate moving average of scores
    public List<Double> calculateMovingAverage(List<Review> reviews) {
        return reviews.stream()
            .map(Review::getScore)
            .gather(Gatherers.windowSliding(3))  // 3-review window
            .map(window -> window.stream()
                .mapToDouble(Integer::doubleValue)
                .average()
                .orElse(0.0))
            .toList();
    }
    
    // Running totals for dashboard
    public List<Integer> getRunningTotals(List<Review> reviews) {
        return reviews.stream()
            .map(Review::getScore)
            .gather(Gatherers.fold(() -> 0, Integer::sum))
            .toList();
    }
}
```

**Custom gatherer (advanced):**

```java
// Custom gatherer to group consecutive elements
public class ConsecutiveGrouper {
    
    public static <T> Gatherer<T, ?, List<List<T>>> groupConsecutive(
            Predicate<T> condition) {
        
        return Gatherer.ofSequential(
            () -> new ArrayList<List<T>>(),  // Initial state
            (state, element, downstream) -> {
                // Grouping logic
                if (condition.test(element)) {
                    // Start new group or add to current
                } else {
                    // Emit group
                }
                return true;
            }
        );
    }
}

// Usage: Group consecutive high scores
List<List<Review>> consecutiveHighScores = reviews.stream()
    .gather(groupConsecutive(r -> r.getScore() >= 4))
    .toList();
```

---

## SEQUENCED COLLECTIONS (JAVA 21)

### Q109: What are SequencedCollection, SequencedSet, and SequencedMap?
Java 21 introduced these new interfaces to provide a uniform way to access elements in a defined order from both ends.

**Problem before Java 21:**

```java
// Getting first element - different for each collection
List<Review> reviews = new ArrayList<>();
Review first = reviews.get(0);  // List way

Deque<Review> deque = new LinkedList<>();
Review first2 = deque.getFirst();  // Deque way

// No uniform way!
```

**SequencedCollection methods (Java 21+):**

```java
interface SequencedCollection<E> {
    void addFirst(E);
    void addLast(E);
    E getFirst();
    E getLast();
    E removeFirst();
    E removeLast();
    SequencedCollection<E> reversed();
}
```

**Real PerformanceTrack usage:**

```java
public class ReviewQueue {
    
    private SequencedCollection<Review> pendingReviews = new LinkedList<>();
    
    public void addHighPriority(Review review) {
        pendingReviews.addFirst(review);  // Add to front
    }
    
    public void addNormalPriority(Review review) {
        pendingReviews.addLast(review);  // Add to end
    }
    
    public Review getNextReview() {
        return pendingReviews.removeFirst();  // Process from front
    }
    
    public Review getLatestReview() {
        return pendingReviews.getLast();  // Get most recent
    }
    
    public void processInReverseOrder() {
        for (Review review : pendingReviews.reversed()) {
            process(review);
        }
    }
}
```

**SequencedSet (for LinkedHashSet):**

```java
SequencedSet<String> departments = new LinkedHashSet<>();
departments.add("Engineering");
departments.add("Sales");
departments.add("HR");

String firstDept = departments.getFirst();     // "Engineering"
String lastDept = departments.getLast();       // "HR"

// Reverse iteration
for (String dept : departments.reversed()) {
    System.out.println(dept);  // HR, Sales, Engineering
}
```

**SequencedMap (for LinkedHashMap):**

```java
SequencedMap<String, Double> deptScores = new LinkedHashMap<>();
deptScores.put("Engineering", 4.5);
deptScores.put("Sales", 4.2);
deptScores.put("HR", 4.8);

// Get first/last entry
Map.Entry<String, Double> firstEntry = deptScores.firstEntry();
Map.Entry<String, Double> lastEntry = deptScores.lastEntry();

System.out.println("First: " + firstEntry.getKey());  // "Engineering"
System.out.println("Last: " + lastEntry.getKey());    // "HR"

// Reverse the map
SequencedMap<String, Double> reversed = deptScores.reversed();
reversed.forEach((dept, score) -> 
    System.out.println(dept + ": " + score)
);
// Output: HR: 4.8, Sales: 4.2, Engineering: 4.5
```

---

## TRY-WITH-RESOURCES ENHANCEMENTS

### Q110: What enhancements were made to try-with-resources in Java 9?
Java 9 allows using effectively final variables in try-with-resources without redeclaration.

**Before Java 9:**

```java
// Had to redeclare inside try
Connection conn = getConnection();
try (Connection c = conn) {  // Unnecessary redeclaration
    c.executeQuery("...");
}
```

**Java 9+:**

```java
// Can use directly if effectively final
Connection conn = getConnection();
try (conn) {  // No redeclaration needed!
    conn.executeQuery("...");
}
```

**Real PerformanceTrack example:**

```java
public void generateReport(Long employeeId) throws Exception {
    Connection connection = dataSource.getConnection();
    PreparedStatement statement = connection.prepareStatement(
        "SELECT * FROM reviews WHERE employee_id = ?"
    );
    
    try (connection; statement) {  // Cleaner!
        statement.setLong(1, employeeId);
        ResultSet rs = statement.executeQuery();
        // process results
    }
}
```

---

## PROCESS API IMPROVEMENTS (JAVA 9)

### Q111: What improvements were made to the Process API in Java 9?
Java 9 added ProcessHandle for better process management.

**Get current process info:**

```java
ProcessHandle current = ProcessHandle.current();
System.out.println("PID: " + current.pid());

ProcessHandle.Info info = current.info();
System.out.println("Command: " + info.command().orElse("Unknown"));
System.out.println("User: " + info.user().orElse("Unknown"));
```

**Real usage - running external PDF generator:**

```java
public void generatePDFReport(Review review) throws Exception {
    ProcessBuilder builder = new ProcessBuilder(
        "python3", "generate_report.py",
        "--review-id", review.getId().toString()
    );
    
    Process process = builder.start();
    ProcessHandle handle = process.toHandle();
    
    // Wait asynchronously
    handle.onExit().thenAccept(ph -> {
        System.out.println("Report generation completed");
        notifyUser(review.getEmployee());
    });
}
```

---

## FINAL QUESTIONS

### Q112: What is the difference between var and traditional variable declaration in Java 10+?
Already covered in Q89.

### Q113: How has Pattern Matching evolved from Java 16 to Java 21?
Already covered in Q106.

### Q114: What are Virtual Threads and why were they introduced in Java 21?
Already covered in Q107.

### Q115: What is the Stream Gatherer API introduced in Java 22-23?
Already covered in Q108.

### Q116: What are Sequenced Collections introduced in Java 21?
Already covered in Q109.

---

**Topics covered (Q1-Q116):**
All major Java 8-25 features with real PerformanceTrack examples!

This comprehensive guide covers Lambda Expressions, Stream API, Optional, Collectors, CompletableFuture, Date-Time API, Functional Interfaces, and all major Java 8-25 features with practical examples from the PerformanceTrack system.
