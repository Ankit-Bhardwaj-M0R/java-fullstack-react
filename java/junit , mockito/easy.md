# JUnit and Mockito Interview Questions - Complete Guide

I'll cover every possible question comprehensively. This will be a long list, so I'll organize it well for you.

---

## **PART 1: JUnit Basics**

### **1. What is JUnit?**

JUnit is a testing framework for Java that helps us write and run automated tests. Think of it like a quality checker in a factory - instead of manually testing your code every time, JUnit automatically runs tests to make sure everything works correctly.

**Example:** In your PerformanceTrack system, instead of manually checking if the login works every time you change code, you write a JUnit test once. It automatically verifies the login feature whenever you run tests.

---

### **2. Why do we need unit testing?**

Unit testing helps us catch bugs early, saves time, and makes our code more reliable. It's like checking each ingredient before cooking instead of tasting the final dish and finding something wrong.

**Example:** If someone changes the salary calculation logic in your Analytics module, unit tests will immediately tell you if it breaks the performance score calculation - before users see the problem.

---

### **3. What is a unit test?**

A unit test is a test for the smallest piece of code - usually one method. We test it in isolation to make sure it does exactly what it should.

**Example:** Testing just the `calculateAverageScore()` method in your ReportService, without worrying about database or other services.

---

### **4. What are the JUnit annotations you know?**

**@Test** - Marks a method as a test case
**@Before** - Runs before each test (setup)
**@After** - Runs after each test (cleanup)
**@BeforeClass** - Runs once before all tests in the class
**@AfterClass** - Runs once after all tests in the class
**@Ignore** - Skips a test temporarily

**Example:** In testing your ReportController:
```java
@Before
public void setup() {
    // Create test data before each test
}

@Test
public void testGetReport() {
    // Test getting report
}

@After
public void cleanup() {
    // Clean up test data
}
```

---

### **5. What is the difference between @Before and @BeforeClass?**

**@Before** runs before every single test method - use it when each test needs fresh setup.
**@BeforeClass** runs only once before all tests - use it for expensive operations like database connections.

**Example:** 
- Use @Before to create a new test employee before each test
- Use @BeforeClass to establish database connection once for all tests

---

### **6. What is an assertion in JUnit?**

Assertions are statements that check if your code produces the expected result. If the assertion fails, the test fails.

**Example:** Testing performance score calculation:
```java
@Test
public void testCalculateScore() {
    double score = reportService.calculateScore(80, 90);
    assertEquals(85.0, score); // Checks if score equals 85.0
}
```

---

### **7. What are the common assertion methods in JUnit?**

**assertEquals(expected, actual)** - Checks if two values are equal
**assertTrue(condition)** - Checks if condition is true
**assertFalse(condition)** - Checks if condition is false
**assertNull(object)** - Checks if object is null
**assertNotNull(object)** - Checks if object is not null
**assertSame(expected, actual)** - Checks if two references point to same object
**assertNotSame(expected, actual)** - Checks if references are different

**Example:** Testing employee validation:
```java
assertTrue(employee.isActive());
assertNotNull(employee.getEmail());
assertEquals("John", employee.getName());
```

---

### **8. What is the difference between assertEquals and assertSame?**

**assertEquals** checks if values are equal (uses `.equals()` method)
**assertSame** checks if both variables point to the exact same object in memory (uses `==`)

**Example:**
```java
String name1 = new String("John");
String name2 = new String("John");
assertEquals(name1, name2); // PASS - same value
assertSame(name1, name2); // FAIL - different objects
```

---

### **9. How do you test for exceptions in JUnit?**

Use `@Test(expected = ExceptionClass.class)` to verify a method throws the expected exception.

**Example:** Testing invalid employee ID:
```java
@Test(expected = EmployeeNotFoundException.class)
public void testInvalidEmployee() {
    employeeService.getEmployee(-1); // Should throw exception
}
```

---

### **10. What is a test suite?**

A test suite is a collection of multiple test classes that you want to run together. It's like creating a playlist of tests.

**Example:** Running all tests for your Analytics module together:
```java
@RunWith(Suite.class)
@Suite.SuiteClasses({
    ReportServiceTest.class,
    ReportControllerTest.class
})
public class AnalyticsTestSuite {
}
```

---

### **11. What is @Ignore annotation?**

@Ignore temporarily skips a test without deleting it. Useful when a feature is under development or a test is temporarily broken.

**Example:**
```java
@Ignore("Feature not yet implemented")
@Test
public void testAdvancedAnalytics() {
    // Test code here
}
```

---

### **12. What is the difference between @Before and @After?**

**@Before** runs setup code before each test - like preparing ingredients before cooking.
**@After** runs cleanup code after each test - like washing dishes after cooking.

**Example:**
```java
@Before
public void createTestData() {
    testEmployee = new Employee("John", "IT");
}

@After
public void deleteTestData() {
    testEmployee = null; // Clean up
}
```

---

### **13. Can you have multiple @Test methods in one class?**

Yes, absolutely! Each test method should test one specific behavior. This is the normal way to organize tests.

**Example:** Testing ReportService:
```java
public class ReportServiceTest {
    @Test
    public void testCalculateScore() { }
    
    @Test
    public void testGenerateReport() { }
    
    @Test
    public void testValidateData() { }
}
```

---

### **14. What happens if you don't use any assertions in a test?**

The test will always pass (unless there's an exception). This is bad because you're not actually verifying anything.

**Example - Bad test:**
```java
@Test
public void testSomething() {
    reportService.calculateScore(80, 90);
    // No assertion - test always passes!
}
```

Always use assertions to verify results.

---

### **15. What is test-driven development (TDD)?**

TDD means writing tests before writing actual code. The cycle is: Write test → Run test (it fails) → Write code to pass test → Refactor.

**Example:** For a new feature to calculate team average performance:
1. Write test for `calculateTeamAverage()` first
2. Run it - it fails (method doesn't exist yet)
3. Write the actual method
4. Run test again - it passes
5. Improve the code

---

### **16. What is the typical structure of a unit test?**

Tests follow the AAA pattern:
**Arrange** - Set up test data
**Act** - Execute the method being tested
**Assert** - Verify the results

**Example:**
```java
@Test
public void testEmployeeSalaryIncrease() {
    // Arrange
    Employee emp = new Employee("John", 50000);
    
    // Act
    emp.increaseSalary(10);
    
    // Assert
    assertEquals(55000, emp.getSalary());
}
```

---

### **17. How do you run JUnit tests?**

You can run tests in multiple ways:
- From IDE (Eclipse/IntelliJ) - right-click and "Run as JUnit Test"
- From command line using Maven: `mvn test`
- From build tools during deployment

**Example:** In your PerformanceTrack project, running `mvn test` will execute all JUnit tests across all modules before building the application.

---

### **18. What is the purpose of @RunWith annotation?**

@RunWith tells JUnit to use a specific test runner instead of the default one. Different runners provide different capabilities.

**Example:**
```java
@RunWith(SpringRunner.class) // For Spring Boot tests
public class ReportControllerTest {
}
```

---

### **19. What is a test fixture?**

A test fixture is the fixed state of objects used as baseline for running tests. It's your test data setup.

**Example:** For testing reports, your fixture might be:
```java
Employee testEmployee;
Review testReview;
Goal testGoal;

@Before
public void setupFixture() {
    testEmployee = new Employee("John", "IT");
    testReview = new Review(testEmployee, 2024);
    testGoal = new Goal("Complete project", testEmployee);
}
```

---

### **20. What does "green bar" mean in testing?**

When all tests pass, JUnit shows a green progress bar in IDEs. "Getting to green" means making all tests pass. Red bar means failures.

**Example:** After fixing a bug in your Analytics module, you run tests. Green bar means everything works correctly now.

---

## **PART 2: JUnit Advanced Concepts**

### **21. What is parameterized testing?**

Parameterized tests run the same test with different input values. Instead of writing 10 similar tests, you write one test that runs 10 times with different data.

**Example:** Testing score calculation with multiple inputs:
```java
@RunWith(Parameterized.class)
public class ScoreTest {
    private int input;
    private int expected;
    
    @Parameters
    public static Collection<Object[]> data() {
        return Arrays.asList(new Object[][] {
            {80, 90},
            {70, 85},
            {90, 95}
        });
    }
    
    @Test
    public void testScore() {
        assertEquals(expected, calculator.calculate(input));
    }
}
```

---

### **22. What is the timeout parameter in @Test?**

The timeout parameter fails a test if it takes longer than specified milliseconds. Useful for performance testing.

**Example:**
```java
@Test(timeout = 1000) // Must complete within 1 second
public void testReportGeneration() {
    reportService.generateReport(employeeId);
}
```

If report generation takes more than 1 second, test fails.

---

### **23. What are test categories in JUnit?**

Categories let you group tests and run specific groups. Like tagging tests as "fast" or "slow", "database" or "service".

**Example:**
```java
public interface FastTests { }
public interface SlowTests { }

@Category(FastTests.class)
@Test
public void testCalculation() { }

@Category(SlowTests.class)
@Test
public void testDatabaseQuery() { }
```

---

### **24. What is the difference between unit tests and integration tests?**

**Unit tests** - Test one small piece in isolation (like one method)
**Integration tests** - Test how multiple pieces work together (like controller + service + database)

**Example:**
- Unit test: Testing just `calculateScore()` method
- Integration test: Testing entire flow from HTTP request → Controller → Service → Database → Response

---

### **25. Should unit tests access the database?**

No. Real unit tests should not access database. Use mock objects instead. Database access makes tests slow and dependent on external systems.

**Example:** Instead of querying real database, mock the repository:
```java
@Test
public void testGetEmployee() {
    // Mock returns fake data, no database needed
    when(employeeRepository.findById(1)).thenReturn(testEmployee);
}
```

---

### **26. What is code coverage?**

Code coverage measures what percentage of your code is tested. Tools show which lines are executed during tests.

**Example:** If your ReportService has 100 lines and tests execute 80 lines, you have 80% code coverage. Higher is better, but 100% isn't always necessary.

---

### **27. What is a good code coverage percentage?**

Generally, 70-80% is good, 80-90% is very good. 100% is often unrealistic and not always necessary. Focus on testing critical business logic.

**Example:** In your Analytics module, focus on getting 90%+ coverage for score calculations (critical), but 60-70% for simple getters/setters is okay.

---

### **28. How do you test private methods?**

You shouldn't test private methods directly. Test them through public methods that use them. If you really need to test a private method, it might need to be public or in a separate class.

**Example:** Don't test private `validateInput()` directly. Test the public `calculateScore()` method which calls `validateInput()` internally.

---

### **29. What is assertEquals with delta for floating-point numbers?**

When comparing decimals, use delta (tolerance) because of floating-point precision issues.

**Example:**
```java
@Test
public void testAverageScore() {
    double avg = reportService.calculateAverage();
    assertEquals(85.5, avg, 0.01); // 0.01 is delta/tolerance
}
```

This passes if avg is between 85.49 and 85.51.

---

### **30. Can you run a specific test method only?**

Yes! In IDE, right-click the specific test method and run it. Or use Maven: `mvn test -Dtest=TestClassName#methodName`

**Example:** `mvn test -Dtest=ReportServiceTest#testCalculateScore`

---

## **PART 3: Mockito Basics**

### **31. What is Mockito?**

Mockito is a framework for creating mock objects in tests. Mock objects are fake versions of real objects that you control in tests.

**Example:** Instead of using real database repository in tests, create a mock repository that returns whatever test data you want.

---

### **32. Why do we need mocking?**

Mocking helps us:
- Test code in isolation without dependencies
- Speed up tests (no database/network calls)
- Test scenarios that are hard to reproduce (like errors)

**Example:** Testing your ReportService without needing a real database, real email service, or real employees.

---

### **33. What is the difference between mock and stub?**

**Mock** - A fake object where you verify interactions (was method called?)
**Stub** - A fake object that returns predefined data (no verification)

In Mockito, mocks can do both, but conceptually:
- Stub: "When X is called, return Y"
- Mock: "Verify that X was called"

**Example:**
```java
// Stub behavior
when(repository.findById(1)).thenReturn(employee);

// Mock verification
verify(repository).findById(1);
```

---

### **34. How do you create a mock object in Mockito?**

Use `@Mock` annotation or `Mockito.mock()` method.

**Example:**
```java
@Mock
private EmployeeRepository employeeRepository;

// Or

EmployeeRepository employeeRepository = Mockito.mock(EmployeeRepository.class);
```

---

### **35. What is @InjectMocks annotation?**

@InjectMocks creates an instance of the class and injects all the @Mock objects into it automatically.

**Example:**
```java
@Mock
private ReportRepository reportRepository;

@InjectMocks
private ReportService reportService; // Mockito injects reportRepository into this

@Test
public void test() {
    // reportService already has the mock repository injected
}
```

---

### **36. What does when().thenReturn() do?**

It defines what a mock should return when a specific method is called. This is called stubbing.

**Example:**
```java
Employee mockEmployee = new Employee("John", "IT");
when(employeeRepository.findById(1)).thenReturn(mockEmployee);

// Now whenever findById(1) is called, it returns mockEmployee
Employee result = employeeRepository.findById(1);
// result is mockEmployee
```

---

### **37. How do you verify that a method was called?**

Use `verify()` to check if a mock's method was called.

**Example:**
```java
@Test
public void testDeleteReport() {
    reportService.deleteReport(1);
    
    // Verify that repository's delete was called
    verify(reportRepository).deleteById(1);
}
```

---

### **38. What is verify with times()?**

`verify()` with `times()` checks how many times a method was called.

**Example:**
```java
reportService.sendNotifications(employeeList);

// Verify email service was called 5 times (for 5 employees)
verify(emailService, times(5)).sendEmail(anyString());
```

---

### **39. What are the different verification modes?**

**times(n)** - Called exactly n times
**atLeast(n)** - Called at least n times
**atMost(n)** - Called at most n times
**never()** - Never called
**atLeastOnce()** - Called one or more times

**Example:**
```java
verify(repository, never()).delete(any());
verify(emailService, atLeastOnce()).send(anyString());
```

---

### **40. What is the difference between mock() and spy()?**

**Mock** - Completely fake object, all methods return default values unless stubbed
**Spy** - Real object, real methods are called unless stubbed

**Example:**
```java
// Mock - calculateScore() returns 0.0 by default
Calculator mockCalc = mock(Calculator.class);

// Spy - calculateScore() actually executes real code
Calculator spyCalc = spy(new Calculator());
```

---

### **41. When would you use spy instead of mock?**

Use spy when you want to test the real object but override just a few specific methods.

**Example:** Testing ReportService where you want real calculation logic but fake database calls:
```java
@Spy
private ReportService reportService;

@Test
public void test() {
    // Real calculation runs
    // But you can stub database methods
    when(reportService.getFromDatabase()).thenReturn(testData);
}
```

---

### **42. What is argument matching in Mockito?**

Argument matchers let you stub or verify methods without specifying exact arguments.

**Example:**
```java
// Matches any integer
when(repository.findById(anyInt())).thenReturn(employee);

// Matches any string
verify(emailService).send(anyString());

// Matches specific pattern
verify(logger).log(contains("Error"));
```

---

### **43. What are the common argument matchers?**

**any()** - Any object
**anyInt(), anyString(), anyBoolean()** - Any primitive type
**eq(value)** - Specific value
**isNull(), isNotNull()** - Null checks
**contains(substring)** - String contains
**matches(regex)** - Regex pattern

**Example:**
```java
when(service.calculate(anyInt(), anyInt())).thenReturn(100);
verify(logger).log(contains("SUCCESS"));
```

---

### **44. What is doThrow() used for?**

`doThrow()` makes a mock method throw an exception. Useful for testing error handling.

**Example:**
```java
doThrow(new DatabaseException("Connection failed"))
    .when(repository).save(any());

@Test(expected = DatabaseException.class)
public void testDatabaseError() {
    reportService.saveReport(report); // Will throw exception
}
```

---

### **45. What is the difference between thenReturn() and doReturn()?**

Both do the same thing, but syntax differs:
**thenReturn()** - Used with when()
**doReturn()** - Used with spy objects or void methods

**Example:**
```java
// Normal mock
when(repository.findById(1)).thenReturn(employee);

// Spy object (avoids calling real method)
doReturn(employee).when(spyRepository).findById(1);
```

---

### **46. How do you mock void methods?**

Use `doNothing()`, `doThrow()`, or just don't stub them (they do nothing by default).

**Example:**
```java
// Make void method do nothing (default anyway)
doNothing().when(emailService).sendEmail(anyString());

// Make void method throw exception
doThrow(new RuntimeException()).when(service).deleteData();
```

---

### **47. What is MockitoAnnotations.initMocks()?**

It initializes all @Mock and @InjectMocks annotations. Usually called in @Before method.

**Example:**
```java
@Before
public void setup() {
    MockitoAnnotations.initMocks(this);
    // Now all @Mock objects are created
}
```

**Note:** In newer Mockito, use `@ExtendWith(MockitoExtension.class)` instead.

---

### **48. What is @RunWith(MockitoJUnitRunner.class)?**

It's a JUnit runner that automatically initializes mocks. Alternative to calling `MockitoAnnotations.initMocks()`.

**Example:**
```java
@RunWith(MockitoJUnitRunner.class)
public class ReportServiceTest {
    @Mock
    private ReportRepository repository;
    
    @InjectMocks
    private ReportService service;
    
    // Mocks automatically initialized, no @Before needed
}
```

---

### **49. Can you mock static methods in Mockito?**

Not in older Mockito versions. Mockito 3.4+ supports it with `mockStatic()`, but it's generally avoided. Static methods are hard to test.

**Example:** Better to refactor static methods into instance methods for easier testing.

---

### **50. What is ArgumentCaptor?**

ArgumentCaptor captures arguments passed to mock methods so you can verify them later.

**Example:**
```java
ArgumentCaptor<Employee> captor = ArgumentCaptor.forClass(Employee.class);

reportService.saveEmployee(employee);

verify(repository).save(captor.capture());
Employee savedEmployee = captor.getValue();
assertEquals("John", savedEmployee.getName());
```

---

I've covered 50 questions so far. There are many more questions to cover. 

**Should I continue with:**
- More Mockito advanced concepts
- Mockito best practices
- Common mistakes and troubleshooting
- JUnit 4 vs JUnit 5 differences
- Integration with Spring Boot
- Real interview scenarios

Type **"continue"** and I'll provide the next set of questions!

